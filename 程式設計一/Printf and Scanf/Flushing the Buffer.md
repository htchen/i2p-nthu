## Flushing the Buffer ##
當你使用 `printf()` 把資料顯示到螢幕上時，其實並不會立即顯示在螢幕上，而是先送到所謂的 buffer 裡。要等到下列幾種情況才會做 flushing the buffer 的動作，把 buffer 裡的資料沖到螢幕上：i) 當 buffer 滿的時候；ii) 當 '\n' 字元出現的時候；iii) 當接下來是做輸入的動作的時候 (譬如遇到 scanf())。
有時候為了讓資料能立刻顯示到螢幕上，可以用 fflush() 強迫把 buffer 裡的東西送出。當你發現有時候輸入或輸出的顯示順序會亂掉，可以試著在 printf() 之後用 `fflush()` 來確保資料不會被卡在 buffer 裡。
