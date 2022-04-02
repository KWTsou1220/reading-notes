## 務實的方法

### 優秀設計的精隨

```一個好的設計比爛設計更容易改動```

**降低耦合性 (decoupling)** 是為了減少相關性，我們做更動的時候會更容易，這是 ETC (easier to change)。**單一責任原則 (single responsibility principle)** 是因為依需求所做的修改，只要修改一個模組就好了，這也是 ETC。甚至為甚麼命名很重要，因為好的命名使程式碼更容易閱讀，進而加以改動，這也是 ETC。

ETC 是一個價值觀，但有時候我們也會毫無頭緒，不確定是否目前的設計是否符合 ETC，我們可以遵循以下原則：

- **在加入新程式碼之前，先想一想是否加入的東西可以輕易替換**
- **若遇到不確定的情形時，先在程式碼旁邊註記，並寫入工作日誌。未來此處需要改動時，可以得到及時的回饋。**

**Note:** 低耦合性的一個例子。假設我們有一個 vehicle 的 class，照理來說 interface 應該只有 ```brack()```、```accelarate()```、```switch_gear()```... 這些函數，供外部使用者操作，而將引擎、油門機構、換檔機制等操作隱藏起來，避免使用者使用。萬一我們需要更動這些內部機制的時候，可以降低外部使用者的程式碼更動。


### DRY - 重複的罪惡

```在一個系統中，每一條知識都必須有一個單一的、明確的、權威的表述```

維護並不只代表著修復 bug 和增強功能，因為知識隨時都在變動，原本可行的演算法，隨著環境變動後，可能變得不再適用。因此程式設計師應該是隨時處於維護的狀態，不論出於何種原因，維護都是開發過程中的一種常態活動。若我們在開發的過程中有過多的重複，就會招致維護惡夢，為了避免此狀況我們必須遵循 DRY (don't repeat yourself) 原則。

#### **DRY 範圍不僅止於程式碼**

需要注意的是，DRY 並不僅止於程式碼，還包括意圖、知識的重複。大原則是，當你必須修改某程式碼時，是否需要同時修改多處程式碼？若答案是肯定的話，代表可能沒有實踐好 DRY 原則。

#### **重複的程式碼**

```
def print_balance(account)
    printf "Debits: %10.2f\n", account.debits
    printf "Credits: %10.2f\n", account.credits
    if account.fees < 0
        printf "Fees:   %10.2f-\n", -accounts.fees
    else
        printf "Fees:   %10.2f\n", accounts.fees
    if account.balance < 0
        printf "Balances: %10.2f-\n", -account.balance
    else
        printf "Balance: %10.2f\n", account.balance
```

這個例子有幾處重複的程式碼，首先 ```printf``` 的內容可以寫做一個函式。第二，print 之前需要判斷 balance 和 fees 是否是負數，再依照正負值選擇要如何輸出，我們可以寫一個函式來做這件事情，避免架構重複。

#### **不是所有重複的程式碼都屬於知識複製**

設想一個情況，假設我們要驗證某個 class 裡面的年齡、身高是否符合規範，寫出來的程式碼會非常相似，都是利用 if-else 判斷是否為非負值。此時會出現兩處一模一樣的程式碼，但這種狀況下不應該被視為違反 DRY 原則，因為他們包含不同的知識，單純是因為巧合才寫出相同的程式碼。

#### **文件中的重複**

有時很容易理解的程式碼中，不需要再撰寫註解了，不然如果客戶需要修改其中的某個內容，註解處就必須跟著修改，違反了 DRY 原則。增加註解的時候問問自己，這些註解是否必要？是否違反 DRY？

#### **資料違反 DRY 原則**

```
class Line {
    Point start;
    Point end;
    double length;
}
```

這個物件違反了 DRY，因為當我們修改 ```start``` 或是 ```end```，必須同時修改 ```length```。因此比較好的方式是把宣告一個 ```length()``` 函式，自動回傳該直線物件的長度。但實際情況中，我們時常需要考慮到程式運行效率的問題，呼叫函式肯定會導致程式運行較慢，這種情況下，我們可以維持 ```double length``` 這個成員，但是在物件內部我們需要自行處理 length 的問題。

```
class Line {
public:
    Point start;
    Point end;
    double length;
    void setStart(Point p) {this.start = p; calculateLength();}
    void setEnd(Point p) {this.end = p; calculateLength();}
private:
    void calculateLength() {
        this.length = distance(this.start, this.end);
    }
}
```

#### **API 的重複**

(TODO) 目前這個部分沒有特別心得，如果未來融會貫通再補上。

#### **開發者的重複**

多人同時開發專案的狀況下，可以會寫出很多重複的程式碼，為了減輕這個問題，可以：

- 每日站立會議，分享開發的近況。
- 指派團隊成員擔任圖書館員，職責在於促進知識交流。
- 建立文件，擺放實用函式和腳本。
- 多閱讀他人的程式碼。





