# C-notes


## GCC (GNU compiler collection)


### C-99 相關

__可變動陣列 VLA__

亦即，陣列的長度 arr[n]，n 是可以在 run-time 期間變動的，  
傳統上，陣列長度必須在編譯期間就要設定在變數中或 macro 中，  
但 C99 可以讓陣列變動，可以更精準設定陣列長度  

甚至可以使用運算，如：   

int arr[n*3+5];

二維陣列也可  

int arr[m][n]


### options

__-W__ : warning 
__-Wall__ : warning all ，加強版  
__-pedantic__ : 有點像 linter，會 warning 所有非 standard 用法  
__ansi__ : 會關掉大部分非標準 C 之功能，並且會開啟一些平常關閉的功能  
__-std=c89__ : -std=年份，表示要用某版本的 C 去確認 C 語言的語法  


## Formatted IO

### printf

printf 是用來列印出格式化過的 string,  
printf() 可以放無限多的參數，也可以同時放字串跟轉換規格內容 conversion specification，   
轉換規格的內容通常會以 % 作為前綴，也就是把原本的 binary 轉換成 %* 之格式  

__%d__ 就是把 int 的值，從 binary 轉換成 十進位 的字串  
__%f__ 就是把 float 的值，從 binary 轉換成 十進位浮點數 的字串  

__關於實際 printf format__

可查詢   
$ man 3 printf  

__minimum field width__：有關於 printf 的顯示位數，   
float 可以知道是 ％m.pX,   
例如："%10.3f"  


m 是前置，如果 m 不足十位數，則前面會有顯示多個空格，  
p 是小數後位數，不足會補零，  
X 是格式字母   

|   1234566.123|   

例如："%5.3d" , 12  
|  012|  

例如："%-5.3d" , 12   
|012  |  

5 : 代表全部的數字位數，意即最少顯示 5 位數，12.3 會顯示成 | 12.3|，小數點也算一位   
+- : 代表左右預留的位數，不顯示   
3 : 代表必定顯示數字的位數  


### 字元 escape 

大部分加 \   
% 就要用 %%

### scanf

scanf("%d %f" , &x , &y);  
scanf 其實是一個開發者避免使用的函式，  

scanf 的運作是藉由除取 string 內 "%d %f" 的部份，判斷輸入規格是什麼東西，  
接著以規格去篩選鍵盤輸入的內容，判斷正確格式的內容就載入 &x , &y 記憶體中

所以 string 判斷 pattern 很重要，scanf("%d/%f")  
就是告訴系統要先找 int，之後再找 / ，最後再找 float

如果 string pattern 中，有任何字元，都會影響判斷輸入，  
因為系統會去找輸入的內容，比對內容是否符合 string pattern

例如：

scanf("%d abc %f" , &x , &y);

使用者就必須輸入 10 abc 11.1   
如此符合 string pattern， scanf 才能正確讀取   
如果 10 後面接的不是 abc，則 scanf 就會直接中止，不會繼續讀取數值，所以 y 變數就會是 0  

字元方面

scanf("%c" , &ch);

這樣無法避免略過使用者輸入空白的情況，如果想要略過使用者的空白，   
就這樣做

scanf(" %c" , &ch);

如此，不管使用者在輸入 ch 字元的前面輸入多少空白，都不會影響讀取真正的字元  


__輸入格式__

可查詢 $ man scanf 

可得到相關 variable specification 相對應的 format

如：

unsigned int : 
octal == %o
decimal == %u
hex == %x


__陷阱區__

scanf("%d\n") || scanf("%d ");    

%d 後面的 \n 或 空格，對於 scanf 來說都會看成一個空格 (space)   
都會造成 scanf 在讀取完第一個 int 之後，不正常的持續運作，   
直到收到另外一個『非空值字元』，才會停止，但是那個非空值字元，實際上 scanf 也沒辦法使用    


### getchar() 和 putchar()

基本用法

```c
char ch;
while( (ch = getchar()) != 'q'){
    putchar(ch);
}
```
上面的例子，  
當程式運行到 getchar() 的部份時，因為 IO 暫存區中沒有字元，
會 blocking，等待使用者輸入資料，  
當我們輸入了一串字元時，按 enter 後，  

'abcdefg1234567ABCDEqWXYZ'

上面的字串會被放在 IO 的緩衝區記憶體中（包括 enter 的 \n 字元），  
然後 getchar 會先取出第一個字元 'a'，放到 ch 變數中，   
while 判斷式裡面，會判斷 ch 是否是 'q'   
不是的話，就進入 while 迴圈，   
putchar 取得 ch 變數中的值，也就是 'a'，把它 IO 到螢幕上，完成一個流程，    

接下來繼續，又重新進入 while 判斷式的 getchar() 部份，週而復始，   
直到 while 判斷式檢查到 ch 變數中的字元等於 'q'，   
這時候就會直接跳出 while 迴圈，中止程式   

getchar() 跟 scanf() 請不要混用，   
否則可能會造成無法判斷緩衝區順序的無法預期狀況  

__常見格式__：

d 十進位整數，  
e 十進位浮點數 exponential 形式  
f 十進位浮點數  
g 十進位之整數、浮點數形式  
 

## 小疑問

### return 

1. 當 main 使用 exit(0)，即與 return 0 相同，return int = 0 給 OS  

2. 當 main (){}，  
    1. 沒有寫明 return type 時  
        1. 當 C89 標準時，則 main 會 return undefined，  
        2. 在 C99 時，則會 return 0，  
    2. 有寫明時，  
        1. C99 會 return 隨機 int  


### if-else

這兩種 if-else 最大的差別是，   
一個只允許 > 0 的值，一個允許 truthy 值  

__> 0 的正整數__
if(i>0){}

__truthy 值 (1,2,-1,-2,true,非空值 ...)__
if(i){}


### type

__signed__

leftmost bit : sign bit

2^15 -1

16 bit 
1111_1111 1111_1111


2^31 -1

32 bit 
1111_1111 1111_1111 1111_1111 1111_1111

int 會預設為 有號變數 signed

__unsigned__

如果要用 無號變數 unsigned，就必須加上 unsigned 關鍵字  

另外可以配合 short int long 來改變需要的位元數  

排列組合是

short int  
unsigned short int  

int   
unsigned int

long int   
unsigned long int  


在 C 語言的標準中，  
規定並沒有很嚴格說 short 要幾 bit，  
規定並沒有很嚴格說 int 要幾 bit，只有說 int 不能比 short 小   
規定並沒有很嚴格說 long 要幾 bit，只有說 long 不能比 int 小   

但這也代表了  

short 可以跟 int 一樣大，  
或者  
long 可以跟 int 一樣大  

實際規格，主要還是看編譯器是如何實做，  
有一個方法可以查到 smallest 跟 largest int，  
就是使用 limits.h header  

__C99__

在 C99 的標準還增加了

long long int  
unsigned long long int  

給予極大數據使用的變數規格，會需求最少 64 bit 的空間

以上『有號規格』，包含 signed char，統稱為 標準有號正整數  
以上『無號規格』，包含 unsigned char， _Bool 統稱為 標準無號正整數  

C99 更提供編譯器可以實做有無號的 128 bit 變數的可能，  
稱為 extended integer types  


__整數常數__

整數有三種表示形式  
octal   base 8      0337  
decimal base 10     123  
hex     base 16     0xff  

這三種都只是一種整數常數的表示法，除了表示數字以外，並沒有額外的功能，  
實際上 整數 通常是用 binary 儲存在記憶體中  

在底層程式上，使用 octal 跟 hex 是最方便的  



如果一個整數常數原本是設定為 int，但當數值太大已超過 int 的空間時，  
編譯器就會嘗試轉成 long int，再來會嘗試 unsidned long int  
流程如下：

int  
long int  
unsigned long int  


但如果要判定的整數常數是 octal 或 hex 的形式時，  
流程如下：  

int  
unsigned int  
long int   
unsigned long int  


如果想要編譯器把整數常數都當作 long 整數看待的話，可以使用 L

15L  
0337L  
0x7fffL


如果想要編譯器把整數常數當作 unsigned，可以使用 U

15U  
0337U  
0x7fffU  

此外，UL 可以並用，順序不重要

0xffffffffUL

在 __C99 後綴__ ，整數的部份可以用 LL 或 ll 取得 long long int 的規格，  
然後加上 U，變成 LLU (llu) 或 ULL (ull) 來取得 unsigned long long int 的規格，  

同樣的，在 C99 的 __無後綴__ 的情況下，  
對於 decimal 跟 octal 跟 hex 的狀況並不一樣，  

在 decimal __無後綴__ 的情況下，順序是  

int   
long int  
long long int  

在 octal 及 hex __無後綴__ 的情況下，順序是  

int  
unsigned int  
long int  
unsigned long int   
long long int  
unsigned long long int   


但在 __有後綴__ 的情況，就會因為後綴而有不同的規格，  

例如 U(u) 就會是，  
int  
unsigned long int   
unsigned long long int  


而 L(l) 就會是，  
long int  
long long int  


### 如何讀取，如何輸出

用什麼格式讀取，用什麼格式輸出，都會影響實際的值


__int__

使用 unsigned int decimal 讀取  
scanf("%u" , &u);  
輸入 20   
printf("%u\n" , u);  
以 unsigned int decial 輸出會得到 20   

使用 unsigned int octal 讀取  
scanf("%o" , &u);  
輸入 20   
printf("%u\n" , u);  
以 unsigned int decimal 輸出會得到 16  
可以看到之間的差距  

使用 unsigned int hex 讀取  
scanf("%x" , &u);  
輸入 20   
printf("%u\n" , u);  
以 unsigned int decimal 輸出會得到 32
可以看到之間的差距  


__short int__

以 short int 來讀取跟輸出，就是在格式內使用 h

short int   
scanf("%hd")  
printf("%hd")  

short int octal  
scanf("%ho")  
printf("%ho")  

short int hex  
scanf("%hx")  
printf("%hx")  

unsigned short int  
scanf("%hu")  
printf("%hu")  

__long int__

以 long int 來讀取跟輸出，就是在格式內使用 l

long int 
scanf("%ld")

long int octal
scanf("%lo")

long int hex
scanf("%lx")

unsigned long int
scanf("%lu")

__long long int, C99 only__

以 long long int 來讀取跟輸出，就是在格式內使用 l

long long int 
scanf("%lld")

long long int octal
scanf("%llo")

long long int hex
scanf("%llx")

unsigned long long int
scanf("%llu")


### 浮點數

真實世界中，常常會使用到浮點數，C 語言提供  

float (32 bit)
double (64 bit)
long double ()

實際規格須參照 IEEE 754 (IEC 60559)

但實做上，並不一定會參照 IEEE，
實做可以參考 <float.h>

另外 C99 也有複數形式的浮點數

float _Complex
double _Complex
long double _Complex


__浮點常數__

浮點數基本一定要帶著浮點，並且可選擇性的帶著 E(e) 代表 10 的次方數  
以下全部代表同樣的數值  

57.0  
57.  
57.0e0  
57E0  
5.7e1  
5.7e+1  
.57e2  
570.e-1  

編譯器遇到浮點常數，基本上會預設以 double 格式的變數來裝  
之後如果需要也可以再把 double 轉換成 float  

如果想要強制僅用 float 的空間來儲存，就必須在常數後加上 f(F)

57.0f

long double 就用

57.0l

__浮點數 16 進位__ ，也是跟正整數一樣  
在前綴加上 0x，不過很少用到


### 浮點數讀寫

float   
可以使用  
scanf("%f")  浮點數  
scanf("%e")  exponent 浮點數  
scanf("%g")  整數或浮點數  
會使用單精度 float 來儲存


double   
scanf("%lf")  
使用 小寫 l 來做 double 的讀取  
會使用雙精度 float 來儲存

long double  
scanf("%Lf")
使用 大寫 L 來做 long double 的讀取   
會使用 加長 雙精度 float 來儲存

### 字元規格

講到字元，最常見的是 ASCII    
使用 7 個 bit 就可以代表 128 種狀態 (127 種有數狀態跟 0 狀態)    

如果使用 7 bit 的每一種狀態來表示一個字元，  
可以常見到 ASCII 的 A ~ Z 就是 0100_0001 ~ 0101_1010，  

也可以擴充到 8 bit 共 256 種狀態 (255 種有數狀態跟 0 狀態)    
就可以擴充到 Latin-1 的字元組  

但其實 C 語言中的 char 規格是儲存小正整數的 binary，  
也就是 unsigned 0 ~ 256 或 signed -128 ~ 127 相對應的 binary   
然後透過轉換 把每一組 binary 對應到特定的字元上  

對於字元格式 char，通常並不會在意是 signed 或 unsigned，  
只有在需要儲存小整數的時候，才會在意，  
因為 signed 跟 unsigned 的差別就是 數值 -128 ~ 127 或 0 ~ 256 的差別。


而 __字元__ 跟 __整數__ 之間的關係  

在 C89 的時候會用 integral types (積分型態)，來包含整數、字元、Enum 格式，  

但在 C99 的時候，不再使用 integral 這個字眼，  
而是用 integer type (整數型態) 去函概字元型態跟 Enum 型態，  
integer type 同時也包含了 _Bool 型態，_Bool 型態是 unsigned integer type

1. Integer types
    1. char
    2. signed integer
        1. char
        2. short
        3. int
        4. long int 
        5. long long int
        6. extended
    3. unsigned integer
        1. char
        2. short
        3. int 
        4. long
        5. long long
        6. _Bool
        7. extended
    4. Enumerated
2. Floating types
    1. real floating
        1. float
        2. double
        3. long double
    2. Complex
        1. float _Complex
        2. double _Complex
        3. long double _Complex

### 字元表示方法

char 的變數規格常用來代表一些字元，  
除了可以直接賦予字元，如： char c = 'a'; 這樣之外，  
還可以用整數、octal、hex 來表示字元

首先，要注意，char 一定要 unsigned

例如：我們想要列印出 # 字元   
可以這樣寫

unsigned char c = '#';      
unsigned char c = 35;       
unsigned char c = '\43';    
unsigned char c = '\x23';   

以上的方法 printf("%c", c) 出來都是 # 字元，  

用數字代表字元的好處，就是如果你想要用你鍵盤上找不到的字元，  
只要你在 ASCII 或 unicode 上找到它們的代表數字，就可以印出這個字元，  


[控制碼](https://en.wikipedia.org/wiki/ASCII#ASCII_control_code_chart)

__char \0__

char \0 與 int 0 與 null 在 c 語言中是一樣的，  
差別是 0 是明確的使用 number，  
而 \0 是一種字元，常用來當作字串的終止字元，  

```
Binary   Oct  Dec    Hex    Abbr    Unicode  Control char  C Escape code   Name
0000000  000  0      00     NUL     ␀       ^@            \0              Null character
```

__char \1__

char '\1' 在 printf 時，會被認為成 octal，等同 '\01'


__char 與 number 計算轉型__




### 型別轉型

轉型分為兩種

1. implicit
2. explicit

轉型會在幾個情況下發生  
1. 當要做『數字』、『邏輯』運算時，兩個運算元的格式不同
    1. 稱為 usual arithmetic conversions
2. 當變數的格式與賦予變數的值不同時
    1. int a = 10.23 // 格式不合
3. 在 function 內之變數，或 return 變數，格式不合
    1. int abc(int num);
    2. abc(12.34) // 格式不合


轉型的基本邏輯是，以小配合大，  
規格所佔的 bit 數小的，必須轉型配合 bit 數大的  
int 會配合 float

例如：

8 bit char 與 32 bit int，  
這時 char 就會轉型成 32 bit int 再與 int 做計算  

16 bit short 與 32 bit int，  
這時 short 就會轉型成 32 bit int 再與 int 做計算  

32 bit int 與 32 bit float ,  
這時候 32 bit int 會先轉型成 32 bit float

由小至大轉型的過程，稱為 promotion，也稱為 narrower to wider  

__signed & unsigned__

signed -> unsigned 

signed 與 unsigned 做運算  
會轉型成 unsigned

__signed & unsigned 型別比較陷阱__

int a = -10;  
unsigned int b = 1;  

照數字本身大小，b > a，  
但是當在 if( b > a ) 時，  
也就是編譯器會做 implicit 轉型，把 a 轉成 unsigned int，  

這時，因為 unsigned int 沒有『負號』的形式，  
所以會變成 a = 4294967286  

所以反而會變成 a > b

![convert](./images/covert.png)


__floating point type promotion__  

float -> double -> long double

要注意的特別例子：  
long int 跟 double 做運算，  
最後都會轉成 double

如果想要避免 implicit 轉型，可以使用 suffix，  
float f = 3.14159f  
後面加一個後綴 f，就可以保證在型別範圍內的值，規格是想要的  

__int point type promotion__  

int -> unsigned int -> long int -> unsigned long int

特別例子：  
long int 與 unsigned int 做運算  
兩個都會轉型成 unsigned long int  

取最大公倍數的概念

__賦予時期轉換__

char c;
int i;
float f;
double d;

如果把 c 賦予給 i，i = c，則 c 就會被轉型成 int  
如果把 i 賦予給 f，f = i，則 i 就會被轉型成 float  
如果把 f 賦予給 d，d = f，則 f 就會被轉型成 double

__反向轉型__

int i;

i = 12.345;     // i == 12
i = -12.345;    // i == -12

因為 i 本身為 int，而 12.345 為 float，  
所以 i 會把小數部份刪除掉

要注意反向轉型，很容易和其他轉型行為稿混

__C99__

C99 因為有新的變數規格，所以轉型規則有一些跟 C89 不同，  
新的規格：  

1. _Bool
2. long long 
3. extended integer type
4. complex type

C99 基本上有一個轉型權重排名

1. long long int / unsigned long long int
2. long int / unsigned long int
3. int / unsigned int
4. short int / unsigned short ing
5. char / signed char / unsigned char
6. _Bool
7. 先忽略 extended integer type / complex type / enum type



### 強制轉型 casting

語法
```
(type name) expression
```

轉型流程差別

```
例子 1
float fa, fb, f1, f2;
fa = 12.345f;
fb = 10.345f;
f1 = fa - (int)fb;
f2 = fa - fb;

printf("fa: %f , fb: %f\n", fa, fb);
printf("%f\n", f1);
printf("%f\n", f2);

可以看到全部都是 float，計算本來就沒有問題，
但是在 f1 計算的部份，把 fb 先轉型成 int 再計算
跟 f2 不轉型計算，計算結果會是天差地別，

f1 的部份是 fb 先轉型成 int，所以原本的 fb == 10.345 會變成 10
而 fa - fb 計算的關係，在 fb 被轉型成 int 後，又會迫使 fb 再次轉型成 float，10 變成 10.000
f1 就會等於 12.345 - 10.000

```


轉型精度差別
```
例子 2
float ans;
int a = 5;
int b = 3;
ans = (float)a/b;
printf("%f\n" , ans);

可以看到例子 2，  
a/b 就算不用轉型，當答案賦予給 ans 時，也會自然轉型成 float，
只是 a/b 的結果就是 int/int，也就是 5/3 會變成 1，小數點後被捨去
之後再轉型成 float 就會變成 1.0000


而 (float) a/b 的真實意義是 ((float) a) / b
先把 a 轉型成 float，之後 b 也必須轉型成 float 才能計算，
這時候 a/b 的小數部份就會被保留下來，
也就是 float/float 的結果跟 int/int 的結果大不相同，
一個是 1.6667，一個是 1.0000

```

轉型避免 overflow
```
long ans;
int i = 100000;
// 2147483647
// 10000000000
ans = (long)i * i;
printf("%ld\n" , ans);

當 i = 100000 時，int 可以處理的很好，
但是 i * i 時，i 的乘積在賦予 ans 之前還是 int 的規格，
所以這時候 i 的乘積會超過 int 的 2147483647 的上限，而造成 overflow

所以在計算之前，把其中一個 i 做轉型，成為 long int 後，
overflow 的問題就能暫時避免

```

__整數型態 overflow__

1. signed int 亦即擁有正數及負數的範圍，signed int 在 overflow 時
    1. 通常結果是未知行為 (ub)
    2. 有可能造成程式崩潰
2. unsigned int 亦即只擁有正數的範圍，unsigned int 在 overflow 時
    1. 有定義的行為
    2. 答案為 2^n 取 mod，n 是用來儲存結果的位數
    3. 65535 +1 可以確定結果為 0

__檢測型別的最大值例子__

以 i 的平方數，檢測 i 於多少時，會遇到該規格的 overflow

檢測 short int
```
short int short_int_i = 100;
int int_i = (int) short_int_i;
while( (short int)(short_int_i * short_int_i) == (int_i * int_i) ){
    printf("check OK! short_int_i : %hd , %hd , %d , %d\n" , short_int_i, short_int_i * short_int_i, int_i * int_i, short_int_i * short_int_i);
    short_int_i++;
    int_i = (int) short_int_i;
}

printf("check ERROR! short_int_i : %hd , %hd , %d , %d\n" , short_int_i, short_int_i * short_int_i, int_i * int_i, short_int_i * short_int_i);
printf("%hd , %hd\n" , short_int_i , short_int_i * short_int_i);
printf("%d , %d\n" , int_i , int_i * int_i);
printf("Break point i : %hd\n", short_int_i);
```


檢測 int 
```
int int_i = 100;
long int long_i = (int) int_i;
while( (int)(int_i * int_i) == (long_i * long_i) ){
    printf("check OK! int_i : %d , %d , %d , %ld\n" , int_i, int_i * int_i, int_i * int_i, long_i * long_i);
    int_i++;
    long_i = (int) int_i;
}

printf("check ERROR! int_i : %d , %d , %d , %ld\n" , int_i, int_i * int_i, int_i * int_i, long_i * long_i);
printf("%d , %d\n" , int_i , int_i * int_i);
printf("%ld , %ld\n" , long_i , long_i * long_i);
printf("Break point i : %d\n", int_i);
```

__cast 語法陷阱__

記住語法上的陷阱
```
long int ans;  
int i = 10000;

ans = (long) i * i
```
實際上等於 
```
ans = ((long) i) * i;
```
讓其中一個變數轉型，才能在計算之前讓其他運算元轉型，

__wrong__

```
ans = (long) (i * i)

這樣的寫法無法讓我們達到計算前就轉型的目的
```


## typedef 型別定義

型別定義有幾種方式

1. 使用 macro 的 #define

```
＃define BOOL int

如此使用 BOOL 就等於使用 int
BOOL abc === int abc
在預處理階段就會把 BOOL 代換成 int
```

2. 使用 typedef

```
typedef int Bool (typedef 的名稱會大寫開頭是 convention)

Bool 
```
typedef 與 #define 有本質上的不同，  
#define 只是名稱替換，在預處理階段就會整個替換掉，

而 typedef 是一種『內建變數規格名稱的增加』，  
所以 typedef int Bool 就是增加一種叫做 Bool 的變數規格  
只是說 Bool 變數的行為跟 int 一樣，  
把 Bool 當成 int 的 synonym (代名詞) 一樣，因為行為沒什麼不同  

typedef 可以確定的變數名稱，先定義規格，  
其後就不需要在擔心變數規格問題，

例如：  
typedef float Dollars  

就可以確定 Dollars 規格的變數一定是 float 類型的，  
```
Dollars cash_in, cash_out;

替代掉 
float cash_in, cash_out;
```
這樣在大型程式協作時，可以避免掉規格不一致的問題  

而且在後期，如果發現 Dollars 其實應該要用 double 才夠用時，   
也直接 typedef double Dollars 就可以全局轉換了，  
如此可以避免掉許多潛在問題


__typedef 與 #define 之間的優缺點__

typedef 是比 #define 的 macro 還要強大一些，  
主要是因為 typedef 會被編譯器認為是內建的變數型別的行為，  
而 #define 只是 macro，會在預編譯的時候，做語法替代，

所以 macro 通常不會用來做 array 或 pointer 的定義，因為容易會有語法上的 bug,  

macro 語法陷阱  

```
#define PTR_TO_INT int *

PTR_TO_INT a, b, c;

實際上在預編譯時，會變成  

int * a, b, c;

a 是 pointer of int, 
而 b , c 都只是 int

```

__移植性 ＆ 相容性__

typedef 可以解決另外一個問題，就是程式的可移植性問題，  
因為同樣的 int，在 A 機器可能是 32 bit，在 B 機器卻可能是 16 bit，  
所以同樣的   
int a = 100,000  
在 32 bit 機器上沒問題，但在 16 bit 的機器上就是個災難，  

因此 typedef 就像是個全局的轉換器，  

在 A 機器上，可以寫
```
typedef int Quantity;
```

在 B 機器上，可以改成
```
typeddef long Quantity;
```
雖然在 printf 的 %d 還是必須要改成 %ld   
但如此還是可以解決不少問題，  

__C 語言內建程式庫風格__

C 語言的內建程式庫，有許多使用 typedef 所創造出來的新變數規格，  
這些新的變數規格都有一些命名規格，讓你可以一看就知道這是 typedef 做的，  

像是變數後方加上一個 『_t』，這樣一看就知道是前人用 typedef 創建出來的，  

ptrdiff_t  : typedef long int ptrdiff_d

size_t  : typedef unsigned long int size_t

wchar_t  : typedef int wchar_t


在 C99 裡面的 <stdint.h> 裡面也有 int32_t 這樣的例子，  
代表使用 32 bit 的空間的 signed int 的規格類型  

這樣的規格定義，會讓 portability 這件事比較高  


__sizeof__  

sizeof 是一種一元計算子 unary operator，不是 function，  
因此可以寫成 sizeof i，  
而寫成 sizeof(i)，只是為了防止一些 compilor 解析上的問題，  
像是想寫 sizeof i+j，變成 sizeof(i) +j 的問題

sizeof(type) 所解析的數值為其規格在機器上所佔有的記憶體數量，  
其數值為 unsigned int，  

實際上每台機器的規格都不同，  
編譯器會依照該台機器的狀況，解析出變數所佔的記憶體空間


另外要注意到的是，
sizeof 這個 operator 其實是由 size_t 這個變數規格所實做出來的，  
而 size_t 他是一個 typedef，  
所以在每一台機器可能會有不同的規格，  

但是基本上，__size_t 通常是 unsigned int__，  
在 __別的機器上則可能是 unsigned long int__，  
為了避免錯誤，在 C89 的情況，會依照轉型的基本概念，取兩者的最大公倍數的概念，  
把 sizeof 解析的部份做轉型，轉型成 unsigned long，  
因此不管是 unsigned int 或 unsigned long int，  
都可以確定為 unsigned long int

```
(unsigned long) sizeof(int)
```

在 C99 的部份，size_t 則可能會有比 unsigned long 還大的格式出現，  
但是在 C99 也有可以不用在轉型就可以直接 printf 的技巧出現，  
就是使用
```
printf("%zu" , sizeof(int));
```

__sizeof 元素長度__

在 C89 可以正確的認出元素長度，  
而在 C99 通常會有一個例外，就是 sizeof 無法確認 array 的元素長度，  
因為 array 的長度會在程式的執行時期變化  



## 函數


### 函數定義

__函數的樣子__

```c
double avg(double a, double b){
    return (a+b)/2;
}
```

1. 第一個 double ： return type  
2. avg ： 函數名   
3. double a, double b ： 為 parameters，形式參數  


__函數調用__

```c
avg(num1, num2);  
```

1. avg() ：調用函數  
2. num1, num2 ： 為 argument，實際參數   

__函數定義宣告 (函數原型)__

```c
double avg(double, double);
```


### 函數之參數


函數對於被呼叫時，所傳入的參數是否符合定義的形式，所產生的反應，  
會因為一些情況，而有不同的反應。  

1. 函數被呼叫前，編譯器不知道函數原型

函數在被呼叫前，如果不知道函數原型，  
那函數對於實際傳入的參數就會做預設的提昇，  

例如：

```c
int a;  
float b;  
avg(a, b);  
```

因為編譯器不知道 avg 的原型，所以對於呼叫 avg 函數時，  
會把 avg 傳入的參數做預設的提昇，  
提昇的意思就是做最大的擴充，  
把 char, short 提昇成 int，  
把 flaot 提昇成 double,  


2. 函數被呼叫前，編譯器不知道原型，但無法提昇的情況

例如：

```c

int add(int, int);

double x, y;

add(x, y);

```

這邊可以遇見，當 add 被呼叫時，編譯器因為還不知道 add 原型，  
所以會做提昇，  

但是因為傳入的 x, y 都是 double 的形式，  
所以實際上 add 函數就算做提昇，也沒有意義  

除非在傳入的過程中，為了保險起見而做強制轉型，
```c
add((int) x, (int) y);
```

3. 函數被呼叫前，編譯器知道函數原型

如果函數被呼叫前，知道函數的原型，  
則對於傳入錯誤類型的參數，會做強制轉型，  

原型  
```c
double avg(double, double);

int a;
float b;
avg(a,b);
```
這邊就可以預見到，實際參數 a, b 都會被強制轉型成 double  


4. 函數參數(陣列)問題

```c
int getSize(int a[]){
    unsigned int size = sizeof(a)/sizeof(a[0]);
}

int main(void){
    int arr[10];
    getSize(arr);
}
```

從之前的經驗，我們會預設可以用  

```c
unsigned int size = sizeof(a)/sizeof(a[0]);
```

來得到 arr[] 的長度，  

但是實際上把 arr[] 傳到其他的函數之後，  
如果使用 sizeof(a) 的時候，不會得到 陣列 a 的總長度，  
而是得到 陣列 a 的「指標長度」，    
所以陣列在傳遞之後，就無法使用這個方法得到陣列長度了，  
所以實際上在 C 語言的函數中，都會用第二個參數來指明陣列的長度，  

```c
#define N 100

int arr[N];
sum_array(arr, N);

```

用額外的參數來表明陣列的實際長度，有額外的好處也有壞處，  

1. 如果陣列實際元素長度小於陣列表定長度
    1. 如果陣列表定 100，實際只有 50 個元素
    2. 就可以傳遞 50，讓函數計算，減少無謂的計算
2. 如果傳遞的長度大於實際陣列表定長度
    1. 如果陣列表定 100，傳遞的參數超過 100
    2. 如果傳遞長度超過表定長度，則會造成未知行為 (UB)


__C99 函數陣列參數原型__

```c
int test(int n, int m, int arr[n][m]);
int test(int n, int m, int arr[*][*]);
int test(int n, int m, int arr[][m]);
int test(int n, int m, int arr[][*]);
```

__函數陣列參數 static__

函數中使用陣列作為參數，陣列中的 index 使用 static 關鍵字   

```c
int avg(int arr[static 3]){
    ...
}
```

static 意義是告訴編譯器，陣列長度最少是 3，  
因為編譯器知道陣列有最小長度 3，所以編譯器可以預先生成更快的指令來訪問陣列，  
但注意，不管陣列有幾維，static 關鍵字只能用在「第一維」    

__匿名參數 inline 形式__

常見的情況

```c
int arr[] = {1,2,3};

test(arr, 3);
```

上面的形式是常見的狀況，但是 arr 變數如果沒有用在別的地方，  
那其實有點浪費效能，因此可改成

```c
test( (int []){1,2,3} , 3)

```