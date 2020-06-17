# BIT操作

## 常见的Bit操作

1. 与&: 
   1. 有0为0；
   2.  n&(n-1) 去除n的最低位的1得到的数字；
   3. n&(-n) 得到n的位级表示中最低的那一位对应的整数；
2. 或|
   1. 有1为1
3. 非~
   1. 取反
4. 抑或^
   1. 不同为1， 相同为0
5. 算数位移
   1. n 为算术右移，相当于除以 2^n，例如 -7 >> 2 = -2。
6. 无符号左移
   1.  n为无符号右移，左边会补上 0。例如 -7 >>> 2 = 1073741822。

## JAVA中常见的位操作

```java
// 统计n中二进制表示1的数量
Integer.bitCount(n);
// n最高位的1表示的数字
Integer.highestOneBit(n);
// 转化为二进制的字符的数
Integer.toBinaryString(n);
```

## 常见题目

### 1.统计两个数的二进制表示有多少位不同

```java
public int hammingDistance(int x, int y) {
    int z = x ^ y; // 获取不同的1
    int cnt = 0; 
    while(z != 0) { // 计数
        if ((z & 1) == 1) cnt++;
        z = z >> 1;
    }
    return cnt;
}
```



### 2. 数组中唯一一个不重复的元素

```java
// 所有元素抑或 相同为0
public int singleNumber(int[] nums) {
    int ret = 0;
    for (int n : nums) ret = ret ^ n;
    return ret;
}
```



### 3. 找出数组中缺失的那个数

```java
// 所有元素加上下标抑或
public int missingNumber(int[] nums) {
    int ret = 0;
    for (int i = 0; i < nums.length; i++) {
        ret = ret ^ i ^ nums[i];
    }
    return ret ^ nums.length;
}
```

### 4. 数组中不重复的两个元素

```java
// dif &= -dif获得不同的最后一位进行区分
public int[] singleNumber(int[] nums) {
    int diff = 0;
    for (int num : nums) diff ^= num;
    diff &= -diff;  // 得到最右一位
    int[] ret = new int[2];
    for (int num : nums) {
        if ((num & diff) == 0) ret[0] ^= num;
        else ret[1] ^= num;
    }
    return ret;
}
```

### 5. 翻转一个数的比特位

```java
// 32位，每次取出最右边的为添加到ret的尾部
public int reverseBits(int n) {
    int ret = 0;
    for (int i = 0; i < 32; i++) {
        ret <<= 1; //左移
        ret |= (n & 1); //添加在尾部
        n >>>= 1; // 取出下一个
    }
    return ret;
}
```

### 6. 不用额外变量交换两个整数

```java
// 要求a b 不同，否则，a，b结果都为0
a= a^b;
b = a^b;
a = a^b;    
```

### 7. 判断一个数是不是 2 的 n 次方

```java
// 大于0且只有一个1
public boolean isPowerOfTwo(int n) {
    return n > 0 && Integer.bitCount(n) == 1;
}
```



### 8.判断一个数是不是 4 的 n 次方

```java
// 要求1的位置隔一个
public boolean isPowerOfFour(int num) {
    return num > 0 && (num & (num - 1)) == 0 && (num & 0b01010101010101010101010101010101) != 0;
}

```

### 9.实现整数的加法

```java
// a^b表示没有进位的加法
// a&b<<1 表示进位的数
public int getSum(int a, int b) {
    return b == 0 ? a : getSum((a ^ b), (a & b) << 1);
}

```

### 10. 统计从 0 ~ n 每个数的二进制表示中 1 的个数

```java
// DP
public int[] countBits(int num) {
    int[] ret = new int[num + 1];
    for(int i = 1; i <= num; i++){
        ret[i] = ret[i&(i-1)] + 1;
    }
    return ret;
}
```



