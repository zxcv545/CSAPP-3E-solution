# Talk is cheap show me your code
## 1.1
        /* 
         * bitXor - x^y using only ~ and & 
         *   Example: bitXor(4, 5) = 1
         *   Legal ops: ~ &
         *   Max ops: 14
         *   Rating: 1
         */
        int bitXor(int x, int y) {

          return ~(x&y)&(~((~x)&(~y)));
        }
其实第一题非常简单，看到这个应该立马想起**De Morgan's laws**，即使你没有想到**De Morgan's laws**，那么用画真值表的方式也可以推理出结果。
## 1.2
        /* 
         * tmin - return minimum two's complement integer 
         *   Legal ops: ! ~ & ^ | + << >>
         *   Max ops: 4
         *   Rating: 1
         */
        int tmin(void) {
          int x=0x8;
          return x<<28;
        }
这个也非常的简单，正好可以利用这个题目回想一下**two`s complement**的定义。
## 1.3
        /*
         * isTmax - returns 1 if x is the maximum, two's complement number,
         *     and 0 otherwise 
         *   Legal ops: ! ~ & ^ | +
         *   Max ops: 10
         *   Rating: 1
         */
        int isTmax(int x) {
          return !(x+x+2);
        }
这一题也比较简单，当X为最大的数时，`x+1=min`,那么这时再`min+x=-1`,`-1+1=0`,然后再逻辑取反即可完成此题。
## 1.4
        /* 
         * allOddBits - return 1 if all odd-numbered bits in word set to 1
         *   where bits are numbered from 0 (least significant) to 31 (most significant)
         *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
         *   Legal ops: ! ~ & ^ | + << >>
         *   Max ops: 12
         *   Rating: 2
         */
        int allOddBits(int x) {
            int y=0xAA<<24+0xAA<<16+0xAA<<8+0xAA;
             return !(~(((y&x)<<1)+y+1));
        }
这题我的思路是先构造出来一个奇数位都为1的数，也就是`y=0xAA<<24+0xAA<<16+0xAA<<8+0xAA`，然后`y&x`，如果y符合题目的要求，由于补码的唯一性，后续的操作都是为了y符合要求设计的，那么此题也就解决了。
## 1.5
        /* 
        * negate - return -x 
        *   Example: negate(1) = -1.
        *   Legal ops: ! ~ & ^ | + << >>
        *   Max ops: 5
        *   Rating: 2
        */
        int negate(int x) {
        return ~x+1;
        }
还记得书上的内容吗？ `A+~A=-1`
