## 使用浮點數型別 float, double ##
```C
範例 8
#include <stdio.h>
int main(void)
{
float x = 12345.0;
double y = 2.34e12;
printf("%f or %e\n", x, x);
printf("%f or %e\n", y, y);
return 0;
}
```
可以用 %f 格式或是 %e 科學記號格式來表示浮點數。浮點數運算也有 overflow 的問題，譬如
```C
float s = 3.4e38 * 10.0;printf("%e\n", s);
```
由於 `3.4e38` 已經接近 `float` 可容許的範圍的邊緣，再乘上 `10.0` 之後就爆掉，所以會得到輸出結果是`1.#INF00e+000`，表示無限大。使用浮點數另外有一個要注意的是 `round-off error` 的問題，譬如
```C
float a, b;
b = 1.0e12 + 1.0;
a = b - 1.0;
printf("%f\n", a);
```
照理答案應該是 1 後面接 12 個 0，但是實際上會得到奇怪的數字。這是因為要對兩個大小差很多的數目做運算時，只用float 並不足夠同時精確表達兩個數目。
