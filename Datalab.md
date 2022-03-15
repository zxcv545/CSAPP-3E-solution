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
