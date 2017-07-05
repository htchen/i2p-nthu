# 第一章：C 程式設計簡介 課程簡介
## 程式編寫流程
![Image](page1.JPG)
- text editor  <br>是用來寫程式的純文字編輯器，通常還會有一些額外的輔助功能，譬如縮排或改變關鍵字顏色，讓你的程式碼  看起來更一目了然。</br>

- compiler 
    <br>是程式語言的核心工具，用來將你寫的 C source code 變成一堆要讓電腦執行的指令。由於我們要使用的程式
    開發工具 Visual C++、Dev C++ 或 Code::Blocks 所包含的 compiler 其實可以編譯 C 或 C++ source code，不過這兩種
    語言還是有一定程度的差異，所以必須強迫 VC++ 或 Dev C++ compiler 使用 C 語言的語法來編譯你的 source code。只
    要你的 source code 檔案存檔時選擇用的副檔名是 ".c"， VC++ 或 Dev C++ 就會用 C compiler 來編譯你的程式。VC++
    使用的是 Microsoft 自行開發的 compiler，而 Dev C++ 和 Code:Blocks 則通常是用公開原始碼的 MinGW GCC。 另外，
    在 Dev C++ 裡可以從主選單裡選 [Tools]→[Compiler Options]→[Settings] 然後在 C compiler 的項目裡把 "Support all
    ANSI standard C programs" 項目選成 "Yes"。</br>

- linker 
    <br>是用來將編譯好的 object code 連結成最後的執行檔。因為除了我們自己寫的程式碼之外，無可避免一定會用到許
    多現成的 library (函式庫)。在現實的情況下，不可能每一東西都要靠自己創造，所以絕大多數的時候我們都會使用內建的
    或別人寫好的函式庫，來達到自己想要的功能。 linker 就是用來把我們的程式碼和需要的函式庫裡的程式碼連結在一起，
    形成單一的可執行檔。在 Windows 裡，執行的的副檔名為 ".exe"。</br>
