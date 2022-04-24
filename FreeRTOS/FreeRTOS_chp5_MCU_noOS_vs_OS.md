<!-- Copyright 2022 KaiWei TSOU. All Rights Reserved.
    This personal note refers to <<FreeRTOS 內核實現與應用開發實戰指南>> by "野火".
-->


# 5.1 Bare Machine

裸機系統指的是順序性地執行程式設計師的指令，沒有作業系統在其中。其中裸機系統分為兩大類：**輪詢系統**和**前後台系統**。

## 5.1.1 輪詢系統

輪詢系統會在一開始會做好初始化硬體配置，然後讓主程式在一個 infinite for loop 持續循環。這種系統是單純的軟體架構，適合在簡單功能中實現，如：紅綠燈循環。這種系統的缺點是，對於外部信號的響應沒這麼即時，對於需要及時回應外部事件的系統，輪詢系統不是很好的選擇。我們可以得出一個總結：輪詢系統只適合在順序性執行的功能中使用。

假設我們有一個如下的輪詢系統，假設 ```DoSomething2``` 是按鍵檢測，若偵測到按鍵觸發會執行某些工作。但是若剛好 ```DoSomethin1``` 以及 ```DoSomething3``` 是需要花費一定時間處理的功能，並且按鍵觸發的時機點剛好在這兩個功能的執行期間，那這個按鍵觸發事件將會遺漏！

```c
int main() {
    HardWareInit();

    for (;;) {
        DoSomething1();
        DoSomething2();
        DoSomething3();
    }
}
```

### 5.1.2 前後台系統

相比於輪詢系統，前後台系統因為加入了中斷機制，因此可以避免外部事件遺漏的問題，但是缺點是此系統的即時性沒這麼高。中斷事件的處理我們稱為**前台**，而 ```main``` 函式我們稱為**後台**。這個系統大致的架構如下：

```c
int flag1 = 0;
int flag2 = 0;
int flag3 = 0;

int main() {
    HardWareInit();
    for (;;) {
        if (flag1) {
            DoSomething1();
        }

        if (flag2) {
            // DoSomething2();
        }

        if (flag3) {
            DoSomething3();
        }
    }
}

void ISR1() { // interrupt service routine
    flag1 = 1;
}

void ISR2() { // interrupt service routine
    flag2 = 1;
    DoSomething2();
}

void ISR3() { // interrupt service routine
    flag3 = 1;
}
```

在執行後台時，發生外部事件就會觸發中斷，轉而執行中斷服務，若該中斷服務很以在短時間做完，就直接在中斷裡面處理，如 ```ISR2()```，反之我們就留到後台處理。雖然引入中斷服務，可以確保所有外部事件被妥善處理，卻還是無法達成即時響應。

# 5.2 多任務系統

多任務系統同樣具有中斷服務，不一樣的地方在於，多任務系統將每個任務獨立開來，並且賦予優先級，當高優先級的事件被觸發，該任務會被優先執行。在這種設計下，系統的即時性將會被提高。

```c
int flag1 = 0;
int flag2 = 0;
int flag3 = 0;

int main() {
    HardWareInit();

    // OS initialization
    RTOSInit();

    // OS start, and will never come back
    RTOSStart();
}

void ISR1() {
    flag1 = 1;
}

void ISR2() {
    flag2 = 1;
}

void ISR3() {
    flag3 = 1;
}

void DoSomething1() {
    for (;;) {
        if (flag1) {
            //...
        }
    }
}

void DoSomething2() {
    for (;;) {
        if (flag2) {
            //...
        }
    }
}

void DoSomething3() {
    for (;;) {
        if (flag3) {
            //...
        }
    }
}
```

在多任務系統中，我們將各種功能切割成各種獨立的、無限循環的小執行緒，我們稱之為**任務**。每個任務具備優先級，作業系統會依照優先級調度管理。程式設計師不需要處理各執行緒以何種順序執行、不用擔心每個任務是否互相干擾，這些事情作業系統會為我們處理，我們寫出來的程式碼會變得簡潔很多。




# 5.3 總結

**系統**   | **事件響應** | **事件處理** | **特點**
:---------|:------------|:--------------|:---------
輪詢系統   | 主程式       | 主程式       | 輪詢外部事件，輪詢處理事件
前後台系統 | 中斷         | 主程式       | 即時處理事件，輪詢處理事件
多任務系統 | 中斷         | 任務         | 即時處理事件，依優先及處理事件


