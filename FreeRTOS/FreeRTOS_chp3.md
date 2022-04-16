<!-- Copyright 2022 KaiWei TSOU. All Rights Reserved.
    This personal note refers to <<FreeRTOS 內核實現與應用開發實戰指南>> by "野火".
-->


# 3.4 FreeRTOS 的 Coding Style

## 3.4.1 數據類型

Cortex-M 內核中，short 為 16 bit，long 為 32 bit，FreeRTOS 內沒有使用 int 型別。為了避免受到編譯器的影響，我們必須指定 char 為 signed 或是 unsigned 的，因為編譯器可能默認 char 為 signed 或是 unsigned。在 keil 中，編譯器默認 char 是 unsigned，這裡可以從設置中自行調整。

Defined data type | Real data type     | Supp
:-----------------|:-------------------|:----------------
portCHAR          | char               | 
portSHORT         | short              | 
portLONG          | long               | 
portTickType      | unsigned short int | 用於定義系統時間計數器和阻塞時間的值。FreeRTOSConfig.h 中的 preprocessor USE_16_BIT_TICKS 為 1 時為 16 bit。
portTickType      | unsigned short int | 用於定義系統時間計數器和阻塞時間的值。FreeRTOSConfig.h 中的 preprocessor USE_16_BIT_TICKS 為 0 時為 32 bit。
portBASE_TYPE     | long               | 根據處理器架構決定此型別為多少位元

## 3.4.2 變量名稱

FreeRTOS 會將變數名稱加入前綴，char 前綴為 c，short 前綴為 s，long 為 l， portBASE_TYPE 為 x，其他資料結構的前綴也為 x。這樣的好處是，透過每個變量的名稱就可以知道該變量的型別。

## 3.4.3 函數名稱

函數名稱包含了：函數返回類型、函數所在文件名、函數功能。舉例來說：

- ```vTaskPrioritySet()``` 返回 void，並且定義在 task.c 文件
- ```xQueueReceive()``` 返回 portBASE_TYPE，在 queue.c 文件定義

## 3.4.4 Preprocessor

Preprocessor 的命名會將 header file 的名稱以小寫的方式放在前綴，後面則由大寫字母表示。另外，由 preprocessor 定義的函數，有些會遵循函數命名規則，而非 preprocessor 命名。

Prefix | Header file
:-----------------------|:-----------
port (eg. portMAX_DELY) | portable.h
task                    | task.h
pd                      | projdefs.h
config                  | FreeRTOSConfig.h
err                     | projdefs.h

另位，bool 類型也有定義 preprocessor。

Defined preprocessor | Real value
:--------------------|:-----------
pdTRUE               | 1
pdFALSE              | 0
pdPASS               | 1
pdFAIL               | 0

## 3.4.5 Tab

一個 tab 等於 4 個空白鍵！利用 tab 鍵在程式碼移植的時候，可能編排會變亂，使用空格鍵就不會有這種問題。