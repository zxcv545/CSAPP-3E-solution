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
          return !(x^(~(0x1<<31)));
        }
这一题也比较简单，当X为最大的数时，利用异或的性质，构造出来一个max，并与x进行异或操作，只有当x也为max时，`x^(~(0x1<<31))`才能取0，再`!`操作即可。
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
            int a=0xAA<<8;
            int c=a|0xAA;
            int d=c<<16|c;
          return !((x&d)^(d));
        }

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
还记得书上的内容吗？ `A+~A=-1`,那么这题就能很快解决了。
## 1.6
        /* 
         * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
         *   Example: isAsciiDigit(0x35) = 1.
         *            isAsciiDigit(0x3a) = 0.
         *            isAsciiDigit(0x05) = 0.
         *   Legal ops: ! ~ & ^ | + << >>
         *   Max ops: 15
         *   Rating: 3
         */
        int isAsciiDigit(int x) {   
            int a=!(x >> 4 ^0x3);
            int b=x&0xF;
            int c=~0xA+1;
            int e=0x80<<24;
            int d=!!((b+c)&(e));
          return  a&d ;
        }
第一个Rating:3的，让我想了一段时间，我一开始想的是利用加减操作来解决这个问题，可是很快就发现漏洞了`因为会有溢出`当然，如果想办法解决溢出所带来的不一样的情况也可以

就换了一种思路，首先发现`0x30` `0x39`的第4-7位均为`0011`所以可以先判断是否符合这个条件，然后判断0-3位是否在范围内，利用的是将第0-3位与`-0xA`相加得到的结果再与`0x80<<24`做`&`操作判断是否在范围内，判断原理是：判断`b+c`是否溢出，如果溢出，则与e后就会变为0x0，将这个结果赋给d，然后`a&b`来判断是否在范围内。
## 1.7
        /* 
         * conditional - same as x ? y : z 
         *   Example: conditional(2,4,5) = 4
         *   Legal ops: ! ~ & ^ | + << >>
         *   Max ops: 16
         *   Rating: 3
         */
        int conditional(int x, int y, int z) {
           int a=~(!x)+1;
          return ((~a)&y)+(a&z);
        }
## 1.8
        /* 
         * isLessOrEqual - if x <= y  then return 1, else return 0 
         *   Example: isLessOrEqual(4,5) = 1.
         *   Legal ops: ! ~ & ^ | + << >>
         *   Max ops: 24
         *   Rating: 3
         */
        int isLessOrEqual(int x, int y) {

          int a=x>>31&0x1;
          int b=y>>31&0x1;
          int c1=(a&~b); 
          int c2=(~a&b);
          int e=y+(~x+1); 
          int flag=e>>31;
          return c1 |(!c2&!flag);
          }
## 1.9
        /* 
         * logicalNeg - implement the ! operator, using all of 
         *              the legal operators except !
         *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
         *   Legal ops: ~ & ^ | + << >>
         *   Max ops: 12
         *   Rating: 4 
         */
        int logicalNeg(int x) {

          return ((x|(~x+1))>>31)+1;
        }
## 1.10
        /* howManyBits - return the minimum number of bits required to represent x in
         *             two's complement
         *  Examples: howManyBits(12) = 5
         *            howManyBits(298) = 10
         *            howManyBits(-5) = 4
         *            howManyBits(0)  = 1
         *            howManyBits(-1) = 1
         *            howManyBits(0x80000000) = 32
         *  Legal ops: ! ~ & ^ | + << >>
         *  Max ops: 90
         *  Rating: 4
         */
        int howManyBits(int x) {
            int a=0,b=0,c=0,e=0,f=0,h=0,j=0,d=0;
            a=x>>31&0x1;
            b=(~a+1)^x;      
            d=(0xff<<24)+(0xff<<16);
            c=!!(b&d);
            c<<=4;
            b>>=c;
            e=!!(b&0xff00);
            e<<=3;
            b>>=e;
            f=!!(b&0xf0);
            f<<=2;
            b>>=f;
            h=!!(b&0xc);
            h<<=1;
            b>>=h;
            j=!!(b&0x3);
            j<<=0;
            b>>=j;
          return c+e+f+h+j+b+1;
        }
## 1.11
        /* 
         * floatScale2 - Return bit-level equivalent of expression 2*f for
         *   floating point argument f.
         *   Both the argument and result are passed as unsigned int's, but
         *   they are to be interpreted as the bit-level representation of
         *   single-precision floating point values.
         *   When argument is NaN, return argument
         *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
         *   Max ops: 30
         *   Rating: 4
         */
        unsigned floatScale2(unsigned uf) {
        unsigned a;
        a=(uf>>23)&0xff; 
           if(a){
           if(~(a|(~0x0<<8)))
           {uf+=0x1<<23;}
           }
           else
           {uf+=uf&(~(0x1<<31));}
           return uf;
        }
## 1.12
        /* 
         * floatFloat2Int - Return bit-level equivalent of expression (int) f
         *   for floating point argument f.
         *   Argument is passed as unsigned int, but
         *   it is to be interpreted as the bit-level representation of a
         *   single-precision floating point value.
         *   Anything out of range (including NaN and infinity) should return
         *   0x80000000u.                            
         *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
         *   Max ops: 30
         *   Rating: 4
         */
        int floatFloat2Int(unsigned uf) {
           int b,sign,e,E;
           sign=uf>>31&0x1;
           b=(uf&0x007fffff)|(0x1<<23);
           e=(uf>>23)&0xff;
           E=e-127;
           if(e==0)
           uf=0;
           else{
           if(e==0xff)
           uf=0x80000000;
           else
           {
           if(E>30)
           uf=0x80000000;
           if(E<23&&E>=0)
           {b=b>>(150-e);
             if(sign)
             uf=-b;
             else
             uf=b;}
           if(E<0)
           uf=0x0;
           if(E>=23&&E<=30)
           {b=b<<(e-150);
           if(sign)
           uf=-b;
           else
           uf=b;
           }
        }
           }
          return uf;
        }
## 1.13
        /* 
         * floatPower2 - Return bit-level equivalent of the expression 2.0^x
         *   (2.0 raised to the power x) for any 32-bit integer x.
         *
         *   The unsigned value that is returned should have the identical bit
         *   representation as the single-precision floating-point number 2.0^x.
         *   If the result is too small to be represented as a denorm, return
         *   0. If too large, return +INF.
         * 
         *   Legal ops: Any integer/unsigned operations incl. ||, &&. Also if, while 
         *   Max ops: 30 
         *   Rating: 4
         */
        unsigned floatPower2(int x) {
            unsigned a=0;
            if(x<-149)
            a=0;
            if(x>127)
            a=0xff<<23;
            if(x>=-149&&x<=127)
            {
            if(x<-126)
            a=a|1<<(149+x);
            else
            a=(x+127)<<23;
            }
            return a;
        }
