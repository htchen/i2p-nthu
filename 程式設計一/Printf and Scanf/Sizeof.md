## 使用 sizeof 查詢型別的佔用的 byte 數 ##
```C
範例 9
#include <stdio.h>
int main(void)
{
 printf("int: %u bytes, char: %u bytes,\n", sizeof(int), sizeof(char));
 printf("long: %u bytes, double: %u bytes.\n", sizeof(long), sizeof(double));
 return 0;
}
```
這個範例使用 `sizeof` 這個內建 `operator` 來顯示 `int`, `char`, `long`, `double` 這些型別所佔用的 byte 數目。
當我們把數值設定給變數時，要注意變數的型別是否符合我們的需求，譬如
```C
int a = 9.99;
float pi = 3.1415926536;
```
這兩個情況都無法把我們想要的數值存到變數裡。對變數 a 來說，小數點後面的數目會被無條件捨去，所以 a 的值會是9。至於 float 的精確度只有保證六個有效位數，因此超過的位數就不一定正確。如果用 double 的話可以保證到十個有效位數。
