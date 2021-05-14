# **Java** 

---

## 一、总述

Java是一种强类型的语言，所以不容易出错的

`Program Errors` 

- `trapped errors`导致程序终止执行，如除0，Java中数组越界访问
- `untrapped errors` 出错后继续执行，但可能出现任意行为。如C里的缓冲区溢出、Jump到错误地址

`Forbidden Behaviours `

   语言设计时，可以定义一组*forbidden behaviors*. 它必须包括所有`untrapped errors`, 但可能包含`trapped errors`.

`Well behaved、ill behaved` 

- `well behaved`: 如果程序执行不可能出现forbidden behaviors, 则为*well behaved*。
- `ill behaved`: 否则为ill behaved...

**强类型strongly typed**:  如果一种语言的所有程序都是well behaved——即不可能出现forbidden behaviors，则该语言为strongly typed。

**弱类型weakly typed**:  否则为weakly typed。比如C语言的缓冲区溢出，属于trapped errors，即属于forbidden behaviors.

**静态类型 statically**: 如果在编译时拒绝ill behaved程序，则是statically typed;

**动态类型dynamiclly**: 如果在运行时拒绝ill behaviors, 则是dynamiclly typed。

![img](https://pic4.zhimg.com/80/b0aeb7ffd1667b9162e5329154d43777_720w.jpg?source=1940ef5c)

- 语言特性：

  1. 简单性

     主要指Java设计时时偏向于C++的，

     小：运行环境所占内存小

  2. 面向对象

     Java和C++不同的主要是在于多继承，在Java中被接口取而代之

  3. 分布式

     主要指Java可以通过例程库，通过URL来访问像HTTP和FTP之类的协议（也就是指可以连接到网络）

  4. 健壮性

     考虑到了很多运行时bug

  5. 安全性

     Sun和Oracle之前都是尝试过做一个安全模型，但是由于越来越多的Bug需要修复，导致浏览器开发商不再信任远程代码，除非有[数字签名](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html)
     
  6. 可移植性
  

----

## 二、开发环境

### 2.1 基本术语

|  术语   |                    解释                    |
| :-----: | :----------------------------------------: |
|   JDK   | java开发工具包（编写程序的时候使用的软件） |
|   JRE   |           Java运行时候使用的软件           |
|   SE    |                   标准版                   |
|   EE    |                   企业版                   |
|   ME    |                   微型版                   |
| java FX |               图形化设计用的               |
| OpenJDK |          JavaSE的一个免费开源实现          |
|   SDK   |    （过时）其实就是JDK，软件开发工具包     |

## 三、语法

### 3.1 基本数据类型

1. 命名相关

- Java区分大小写

- Java命名只能由字母、数字、下划线、$符号组成，不能以数字开头，名称不能使用JAVA中的关键字

  （驼峰命名法：**第一个单词首字母为小写，其他单词首字母为大写**）

2. 注释

- 行内注释

  ```java 
  //
  /*   */
  ```

- 块状注释

  ```java 
  /**
   *
   */
  ```

3. **数据类型** 

   

#### byte

​	1字节

​	在数字前加上不同的前缀含义不同

| 前缀  |                             意义                             |
| :---: | :----------------------------------------------------------: |
|   0   |                            8进制                             |
|  0X   |                            16进制                            |
| 0B/0b | 二进制(是从Java7开始的，为了方便阅读而已)例如，0b1111_0100_0100 |

#### short

​	2字节

#### int

​	4字节

#### long

​	8字节，后接L

#### float

​	4字节，后接f

#### double

> float和double都有NAN表示0/0或者无穷大,NAN是不允许用 **==** 的

```java
public static final float POSITIVE_INFINITY = 1.0f / 0.0f;
public static final float NEGATIVE_INFINITY = -1.0f / 0.0f;
public static final float NaN = 0.0f / 0.0f;
```

---

#### char

> java中的char是占用两个字节，只不过有些字符需要两个char来表示，可以用转义字符（转义字符的代替是发生在读取字符之前）表示

```java
char a='\\';
char b='\'';
char c='\u1090';
```

- Character的内部结构

  ```java 
  public final class Character implements java.io.Serializable, Comparable<Character> {
  		...//一些属性
          public static class Subset  {
          	...
          }
      public static final class UnicodeBlock extends Subset {
      		...
      	}
  
      public static enum UnicodeScript {
      		...
      	}   
      
      //用于缓存ASCII码表示的常用字符
      private static class CharacterCache {
          private CharacterCache(){}
  		//用于暂存Character对象的数组，包括ASCII码支持的所有字符，1字节，7位，一共127个，0是空字符
          static final Character cache[] = new Character[127 + 1];
  
          //方便在类初始化的适合就存储数组中的数组，因为这些是常用的字符，所以要初始化相关的字符
          static {
              for (int i = 0; i < cache.length; i++)
                  cache[i] = new Character((char)i);
          }
       }
      
       //真正用来存储char的值     
       private final char value;        
         		
      //构造方法
      public Character(char value) {
          this.value = value;
      }
      
      
      ...//一些方法
  
  }
  ```

- Character的API

  ```java 
  //用于返回封装有指定的char的Character对象
  public static Character valueOf(char c) {
      //因为127之内是ASCII码所支持的字符，就直接返回在缓存的字符类中缓存的
      if (c <= 127) { // must cache
          return CharacterCache.cache[(int)c];
      }
      //再new 一个字符类
      return new Character(c);
  }
  ```

  ```Java
  //如果指定了相应的char，就直接char强行转成int，获取相应的hashcode
  public static int hashCode(char value) {
      return (int)value;
  }
  
  //这个就是返回相应的Character的hash值
  @Override
  public int hashCode() {
      return Character.hashCode(value);
  }
  ```

  ```Java
  //直接返回Character的char值
  public char charValue() {
      return value;
  }
  ```

  ```java
  //equals方法
  public boolean equals(Object obj) {
      //如果不是适应于Character
      if (obj instanceof Character) {
          //这个是直接判断是否是同一个
          return value == ((Character)obj).charValue();
      }
      return false;
  }
  ```

  ```java
  //将char对象转型成String
  public String toString() {
      //先转型成char数组
      char buf[] = {value};
      //调用构造函数，然后返回String对象
      return String.valueOf(buf);
  }
  
  //之后介绍
  public static String valueOf(char data[]) {
          return new String(data);
      }
  //这个是转型相应的Char成数组
      public static String toString(char c) {
          return String.valueOf(c);
      }
  ```

  ```java
  //用于判断相应的码点是否是有效的码点
  public static boolean isValidCodePoint(int codePoint) {
      //Optimized form of:
      //codePoint >= MIN_CODE_POINT && codePoint <= MAX_CODE_POINT，这个是直接判断是否介于最大和	
      //最小的码点之间
      int plane = codePoint >>> 16;
      return plane < ((MAX_CODE_POINT + 1) >>> 16);//这是规定plane<17，其实也就是码面只能在17之间
  }
  
  //这个是最大的码点，位于第17个码面的最后一个字符
  public static final int MAX_CODE_POINT = 0X10FFFF;
  ```

  ```Java
  //判断是否是BMP码，让码点右移16位，如果是0码面就是 Ox00
  public static boolean isBmpCodePoint(int codePoint) {
      return codePoint >>> 16 == 0;
      // Optimized form of:
      //     codePoint >= MIN_VALUE && codePoint <= MAX_VALUE？？？？优化在哪里
      // We consistently use logical shift (>>>) to facilitate
      // additional runtime optimizations.
  }
  ```

  ```Java
  //判断是否是补充码面的码点，这里就直接进行比较了（？？？不懂为什么这里不优化，）
  public static boolean isSupplementaryCodePoint(int codePoint) {
      return codePoint >= MIN_SUPPLEMENTARY_CODE_POINT
          && codePoint <  MAX_CODE_POINT + 1;
  }
  ```

  ```Java
  //判断是否是高层代理的码点
  public static boolean isHighSurrogate(char ch) {
      // Help VM constant-fold; MAX_HIGH_SURROGATE + 1 == MIN_LOW_SURROGATE
      return ch >= MIN_HIGH_SURROGATE && ch < (MAX_HIGH_SURROGATE + 1);
  }
  
  //判断是否是低层代理的码点
  public static boolean isLowSurrogate(char ch) {
  	return ch >= MIN_LOW_SURROGATE && ch < (MAX_LOW_SURROGATE + 1);
  }
  
  //判断是否是代码对，这里利用上面2个方法判断
  public static boolean isSurrogatePair(char high, char low) {
      return isHighSurrogate(high) && isLowSurrogate(low);
  }
  

  //这里需要和以下几种方法进行区分
  //通过码点获取高层代理的字符，
  public static char highSurrogate(int codePoint) {
     return (char) ((codePoint >>> 10)
          + (MIN_HIGH_SURROGATE - (MIN_SUPPLEMENTARY_CODE_POINT >>> 10)));
  }
    public static char lowSurrogate(int codePoint) {
          return (char) ((codePoint & 0x3ff) + MIN_LOW_SURROGATE);
      } 
  ```
  
  ```Java
  //如果是补充码，则返回2（先猜测是为了要取后续的2个字节，如果是BMP的码面的字符则就返回1）
  public static int charCount(int codePoint) {
      return codePoint >= MIN_SUPPLEMENTARY_CODE_POINT ? 2 : 1;
  }
  ```
  
  ```java
  //如果是代理的，则需要转换，将相应的char字符直接隐式转换成int，再将相应的拼接起来
public static int toCodePoint(char high, char low) {
      // Optimized form of:
      // return ((high - MIN_HIGH_SURROGATE) << 10)
      //         + (low - MIN_LOW_SURROGATE)
      //         + MIN_SUPPLEMENTARY_CODE_POINT;
      //左移10位是因为2^10=1024，给低层的码点让位
      return ((high << 10) + low) + (MIN_SUPPLEMENTARY_CODE_POINT
                                     - (MIN_HIGH_SURROGATE << 10)
                                     - MIN_LOW_SURROGATE);
      //利用相应的代码点，最小补充码的码点，就是第1个平面开始第一个-（最小高代理+最小低代理）
  }
  ```
  
  ```Java
  //》》》》》CharSequence类
  public static int codePointAt(CharSequence seq, int index) {
      char c1 = seq.charAt(index);
      if (isHighSurrogate(c1) && ++index < seq.length()) {
          char c2 = seq.charAt(index);
          if (isLowSurrogate(c2)) {
              return toCodePoint(c1, c2);
          }
      }
      return c1;
  }
  
  //获取char数组在相应的index的int值
  public static int codePointAt(char[] a, int index) {
      return codePointAtImpl(a, index, a.length);
  }
  //真正的实现处，给与要搜查的index，以及长度
      static int codePointAtImpl(char[] a, int index, int limit) {
          //获取到相应位置的char
          char c1 = a[index];
          //如果是高层代理的话，并且不是最后一个字符（因为高层代理就需要后2个字节来表示底层代理）
          if (isHighSurrogate(c1) && ++index < limit) {
              //获取到后2个字符
              char c2 = a[index];
              //如果char是低层的代理区的字符
              if (isLowSurrogate(c2)) {
                  //则返回相应的码点
                  return toCodePoint(c1, c2);
              }
          }
          return c1;
      }
  
  	//判断了下条件，其实就相当于是codePointAt的提交的条件多了些，无大的差别
      public static int codePointAt(char[] a, int index, int limit) {
          if (index >= limit || limit < 0 || limit > a.length) {
              throw new IndexOutOfBoundsException();
          }
          return codePointAtImpl(a, index, limit);
      }
  ```
  
  ```Java
  //获取到相应后一的字符，判断出是否属于代理那部分的字符，然后返回相应的码点
  public static int codePointBefore(CharSequence seq, int index) {
          //获得seq指定位置的字符
          char c2 = seq.charAt(--index);
          //如果是低层代理，并且Index是大于0的
          if (isLowSurrogate(c2) && index > 0) {
              //获取到高层代理的位置的字符
              char c1 = seq.charAt(--index);
              //如果c1是高层代理
              if (isHighSurrogate(c1)) {
                  //直接返回4字节的字符
                  return toCodePoint(c1, c2);
              }
          }
      	//就直接返回
          return c2;
      }
  //类似方法
      public static int codePointBefore(char[] a, int index) {
          return codePointBeforeImpl(a, index, 0);
      }
      public static int codePointBefore(char[] a, int index, int start) {
          if (index <= start || start < 0 || start >= a.length) {
              throw new IndexOutOfBoundsException();
          }
          return codePointBeforeImpl(a, index, start);
      }
  
  	//和用seq类似
      static int codePointBeforeImpl(char[] a, int index, int start) {
          char c2 = a[--index];
          if (isLowSurrogate(c2) && index > start) {
              char c1 = a[--index];
              if (isHighSurrogate(c1)) {
                  return toCodePoint(c1, c2);
              }
          }
          return c2;
      }
  ```
  
  ```java
  public static int codePointCount(CharSequence seq, int beginIndex, int endIndex) {
      //统计字符串的长度
      int length = seq.length();
      //判断相关数据是否符合条件
      if (beginIndex < 0 || endIndex > length || beginIndex > endIndex) {
          throw new IndexOutOfBoundsException();
      }
      //字符串的长度
      int n = endIndex - beginIndex;
      for (int i = beginIndex; i < endIndex; ) {
          //如果是四字节的字符，就跳过2个。并对n减1.因为2个表示一个字符
          if (isHighSurrogate(seq.charAt(i++)) && i < endIndex &&
              isLowSurrogate(seq.charAt(i))) {
              n--;
              i++;
          }
      }
      return n;
  }
  
  //与上同理
  public static int codePointCount(char[] a, int offset, int count) {
      if (count > a.length - offset || offset < 0 || count < 0) {
          throw new IndexOutOfBoundsException();
      }
      return codePointCountImpl(a, offset, count);
  }
  
  static int codePointCountImpl(char[] a, int offset, int count) {
      int endIndex = offset + count;
      int n = count;
      for (int i = offset; i < endIndex; ) {
          if (isHighSurrogate(a[i++]) && i < endIndex &&
              isLowSurrogate(a[i])) {
              n--;
              i++;
          }
      }
      return n;
  }
  ```
  
  ```Java
  //返回给定的char序列中与 index （ codePointOffset代码点偏移量）  的索引。其实就是拿到标识的偏移量之前或之后的东西
  public static int offsetByCodePoints(CharSequence seq, int index,
                                           int codePointOffset) {
          //算出seq的长度，并要index符合相关的实际范围
      	int length = seq.length();
          if (index < 0 || index > length) {
              throw new IndexOutOfBoundsException();
          }
  		//tmp值
          int x = index;
          if (codePointOffset >= 0) {
              int i;
              //先跳过偏移量的长度的单元，这些判断是为了跳过4字节的字符
              for (i = 0; x < length && i < codePointOffset; i++) {
                  if (isHighSurrogate(seq.charAt(x++)) && x < length &&
                      isLowSurrogate(seq.charAt(x))) {
                      x++;
                  }
              }
              //如果I仍然小于偏移量，证明偏移量太大了
              if (i < codePointOffset) {
                  throw new IndexOutOfBoundsException();
              }
              //如果偏移量要向前移动
          } else {
              int i;
              //同理要将相关的偏移量跳掉
              for (i = codePointOffset; x > 0 && i < 0; i++) {
                  if (isLowSurrogate(seq.charAt(--x)) && x > 0 &&
                      isHighSurrogate(seq.charAt(x-1))) {
                      x--;
                  }
              }
              if (i < 0) {
                  throw new IndexOutOfBoundsException();
              }
          }
          return x;
      }
  
  //与上同理
  public static int offsetByCodePoints(char[] a, int start, int count,
                                           int index, int codePointOffset) {
          if (count > a.length-start || start < 0 || count < 0
              || index < start || index > start+count) {
              throw new IndexOutOfBoundsException();
          }
          return offsetByCodePointsImpl(a, start, count, index, codePointOffset);
      }
  
      static int offsetByCodePointsImpl(char[]a, int start, int count,
                                        int index, int codePointOffset) {
          int x = index;
          if (codePointOffset >= 0) {
              int limit = start + count;//计算出最后限制的值，在String类中是数组最大值
              int i;
              for (i = 0; x < limit && i < codePointOffset; i++) {
                  if (isHighSurrogate(a[x++]) && x < limit &&
                      isLowSurrogate(a[x])) {
                      x++;
                  }
              }
              if (i < codePointOffset) {
                  throw new IndexOutOfBoundsException();
              }
          } else {
              int i;
              for (i = codePointOffset; x > start && i < 0; i++) {
                  if (isLowSurrogate(a[--x]) && x > start &&
                      isHighSurrogate(a[x-1])) {
                      x--;
                  }
              }
              if (i < 0) {
                  throw new IndexOutOfBoundsException();
              }
          }
          return x;
      }
  ```
  
  ----------------------
  
  ```Java
  //判断是大写字符
  public static boolean isUpperCase(int codePoint) {
          return getType(codePoint) == Character.UPPERCASE_LETTER ||
                 CharacterData.of(codePoint).isOtherUppercase(codePoint);
      }
  
  
  
  //判断是否是小写
  public static boolean isLowerCase(char ch) {
      return isLowerCase((int)ch);
  }
  //传入他的码点，
      public static boolean isLowerCase(int codePoint) {
          //该方法也是用CharacterData子类的方法获取到相应的10进制数
          return getType(codePoint) == Character.LOWERCASE_LETTER ||
                 CharacterData.of(codePoint).isOtherLowercase(codePoint);
      }
  
  
  
  //介于上面涉及到了CharacterData类，就提一下
      //它是一个抽象类，然后用的是
      abstract class CharacterData {
      abstract int getProperties(int ch);
      abstract int getType(int ch);
      abstract boolean isWhitespace(int ch);
      abstract boolean isMirrored(int ch);
      abstract boolean isJavaIdentifierStart(int ch);
      abstract boolean isJavaIdentifierPart(int ch);
      abstract boolean isUnicodeIdentifierStart(int ch);
      abstract boolean isUnicodeIdentifierPart(int ch);
      abstract boolean isIdentifierIgnorable(int ch);
      abstract int toLowerCase(int ch);
      abstract int toUpperCase(int ch);
      abstract int toTitleCase(int ch);
      abstract int digit(int ch, int radix);
      abstract int getNumericValue(int ch);
      abstract byte getDirectionality(int ch);
  
      //need to implement for JSR204
      int toUpperCaseEx(int ch) {
          return toUpperCase(ch);
      }
  
      char[] toUpperCaseCharArray(int ch) {
          return null;
      }
  
      boolean isOtherLowercase(int ch) {
          return false;
      }
  
      boolean isOtherUppercase(int ch) {
          return false;
      }
  
      boolean isOtherAlphabetic(int ch) {
          return false;
      }
  
      boolean isIdeographic(int ch) {
          return false;
      }
  
      // Character <= 0xff (basic latin) is handled by internal fast-path
      // to avoid initializing large tables.
      // Note: performance of this "fast-path" code may be sub-optimal
      // in negative cases for some accessors due to complicated ranges.
      // Should revisit after optimization of table initialization.
  
      static final CharacterData of(int ch) {
          if (ch >>> 8 == 0) {     // fast-path
              return CharacterDataLatin1.instance;//这是BPM码面基本的字符，其余都是补充字符
          } else {
              switch(ch >>> 16) {  //plane 00-16,
              case(0):
                  return CharacterData00.instance;
              case(1):
                  return CharacterData01.instance;
              case(2):
                  return CharacterData02.instance;
              case(14):
                  return CharacterData0E.instance;
              case(15):   // Private Use
              case(16):   // Private Use
                  return CharacterDataPrivateUse.instance;
              default:
                  return CharacterDataUndefined.instance;
              }
          }
      }
  }
  ```
  
  这是Character相关的UML图，可以看到CharacterData类是一个抽象类，这个抽象类中定义了许多判断字符属性的抽象方法，这些方法的具体实现都在Character0X类中。这也是运用LSP原则，而该父类唯一的实现方法，便是`of` 它是取决于你所传入的这个码点该找哪个具体的类进行相应的处理。还记得我们之前讲的到17个码面吗，其实这里就有所体现，不同的具体的实现类，其实就是不同的字符的划分，比如基本码面，Latin字符，私有的，未定义的区域等等。这些类的具体实现过于底层，大部分都是用switch-case语句进行对应10进行字符，再进行操作，之后补充############################################################################################
  
  ![image-20201027195742249](D:\App\Typora\resources\md-image\Java\image-20201027195742249.png)
  
  ----------
  
  ```Java
  public static boolean isTitleCase(int codePoint) {
      return getType(codePoint) == Character.TITLECASE_LETTER;//这个是3，判断是否是标题字符？？？
  }
  ```
  
  ```Java
  //判断是否是数字，还有许多类似方法不做阐述，
  public static boolean isDigit(char ch) {
      return isDigit((int)ch);
  }
  public static boolean isDigit(int codePoint) {
          return getType(codePoint) == Character.DECIMAL_DIGIT_NUMBER;
      }
  ```
  
  ```Java
  public int compareTo(Character anotherCharacter) {
      return compare(this.value, anotherCharacter.value);
  }
  //直接通过减码点来比较大小
      public static int compare(char x, char y) {
          return x - y;
      }
  ```
  
  -----------------
  
  ```Java
  //？？？找不到Character类的相关描述
  public static String getName(int codePoint) {
      if (!isValidCodePoint(codePoint)) {
          throw new IllegalArgumentException();
      }
      String name = CharacterName.get(codePoint);
      if (name != null)
          return name;
      if (getType(codePoint) == UNASSIGNED)
          return null;
      UnicodeBlock block = UnicodeBlock.of(codePoint);
      if (block != null)
          return block.toString().replace('_', ' ') + " "
                 + Integer.toHexString(codePoint).toUpperCase(Locale.ENGLISH);
      // should never come here
      return Integer.toHexString(codePoint).toUpperCase(Locale.ENGLISH);
  }
  ```

-----

tips:

​	unicode是一种编码机制，一个字符便对应一种编码，这个编码又叫做**代码点**，而**代码单元**是代码点的长度单位

​	Unicode码点可以简单分为两类，

　　	1.BMP码 Basic Multilingual Plane ，码点值为0-0xffff，即最大值65535，

　　	2.补充码 ，\*(BMP) 和 \*supplementary character\*s. 码点值>65535

​	当字符超出65535个的时候，便最直接想到的是，用4个字节来表示，比如UTF-32，但是由于这样十分浪费空间，原本属于BMP的字符也需要4个字节来表示，所以又有变长字符编码的机制，UTF-8，可以采用1，2，3，4四种字节的组合，利用高位来决定是哪种的编码方式

```java
0XXXXXXX                              有效编码位：7 bits
110XXXXX 10XXXXXX                     有效编码位：11 bits
1110XXXX 10XXXXXX 10XXXXXX            有效编码位：16 bits
11110XXX 10XXXXXX 10XXXXXX 10XXXXXX   有效编码位：21 bits
```

​	UTF-8对于码点小的字符的确很合适，也适合空间，而UTF-16是为了适用于那些码点大的字符，不过也是变长字符编码，有2，4两种编码方式，详见下面代码中的分析

​	Java中用两个字节表示char（其实也就是使用的是UTF-16的编码方式），在char中存储的是字符的Unicode码点，所以char只能表示 BMP字符，但是我们又需要补充码中的内容时又怎么办呢？就产生了如下办法：

```Java
/**前面已经提到了，java单个char只能表示BMP码，如果要处理补充码时，则使用字符串或字符数字表示。也就是调用如下函数*/
public static char[] toChars(int codePoint) {
    //isBmpCodePoint是用于判断是否是BMP码，底层是将其右移了16位判断是否==0
    if (isBmpCodePoint(codePoint)) {
        return new char[] { (char) codePoint };
    } else if (isValidCodePoint(codePoint)) {
        //如果是合法的二进制为32位长度的数字？？这里还有疑问，isValidCodePoint（）函数做了一次优化，到底优化在哪里
        //设置两个数组进行存放char，分别是高位和低位的
        char[] result = new char[2];
        //见下
        toSurrogates(codePoint, result, 0);
        return result;
    } else {
        throw new IllegalArgumentException();
    }
}

//用于计算高位和低位的char分别是多少
static void toSurrogates(int codePoint, char[] dst, int index) {
        // We write elements "backwards" to guarantee all-or-nothing
        dst[index+1] = lowSurrogate(codePoint);
        dst[index] = highSurrogate(codePoint);
    }

//这里就是解决补充码表示的关键，Unicode制定组从原本65535个字符码点中拿出来了2048个代码点，两两组合进行，这样就产生了1024^1024个新的代码点，就可以拿来表示补充码了。并且1024^1024=16*65535刚好能表示剩下的16个平面
//这1024个代码点分别是「High Surrogates」  U+D800 至 U+DBFF  和 「Low Surrogates」 U+DC00 至 U+DFFF ,所以可以看到 MIN_LOW_SURROGATE  = '\uDC00'，MAX_LOW_SURROGATE  = '\uDFFF'；MIN_HIGH_SURROGATE = '\uD800'，MAX_HIGH_SURROGATE = '\uDBFF'

//而codePoint和0x3ff做 与 操作， 那为什么是0x3ff呢？因为03ff=1023=DFFF-DC00，这样就可以取出后10位
public static char lowSurrogate(int codePoint) {
        return (char) ((codePoint & 0x3ff) + MIN_LOW_SURROGATE);
    }

//要取出前6位,MIN_SUPPLEMENTARY_CODE_POINT=65535，它右移10位=1024，然后高位移除
public static char highSurrogate(int codePoint) {
        return (char) ((codePoint >>> 10)
            + (MIN_HIGH_SURROGATE - (MIN_SUPPLEMENTARY_CODE_POINT >>> 10)));
    }
```

附载一篇**位移**相关操作：https://www.jianshu.com/p/927009730809

----

另外，在Java中还有专门用来判断是否属于Unicode字符的函数

```Java
/**
这里之所以调用isJavaIdentifierStart（int ch） 是因为这个方法无法判断补增的字符，所以将其转换成代码点，再  来进行判断
*/
public static boolean isJavaIdentifierStart(char ch) {
    return isJavaIdentifierStart((int)ch);
}
/**
其实这里是调用了CharacterData类里的唯一实现方法（关于Character的相关类，在下面会做详细介绍），
*/    public static boolean isJavaIdentifierStart(int codePoint) {
        return CharacterData.of(codePoint).isJavaIdentifierStart(codePoint);
    }

```

----



#### boolean

int和boolean之间不能强转

### 3.2 变量与常量

- 常量

  经常在常量前加上`final`表示这个变量只能被赋值一次，之后不能再更改（一般命名全大写）

tpis: 

​	1> 对于**<u>局部变量</u>**，如果可以从初始值推断变量类型，就不需要再声明变量类型，只需要用`var`,类似于`Scala`的用法（在Java10之后）

​	2> 关于泛型所用值得相关规定：https://juejin.cn/post/6844903917835419661

### 3.3 字符串

- String是不可变变量

底层是用char的数组存储的，因为这里用到了final所以是不可变的

```Java
private final char[] value;
```

```Java
private int hash; // Default to 0，缓存hash值
```

- 构造函数

```Java
/**
 * 默认是创建一个""字符串的对象，并不是null
 */
public String() {
    this.value = "".value;
}

/**
 * 这里直接输入一个String实例，并使用它的value数组和hash值
 */
public String(String original) {
    this.value = original.value;
    this.hash = original.hash;
}

/**
 * 这里是直接输入了value数组，并复制到当前对象
 */
public String(char value[]) {
    this.value = Arrays.copyOf(value, value.length);
}

/**
 * 和上面一样，只是复制指定范围内的value[]的值
 */
public String(char value[], int offset, int count) {
    if (offset < 0) {
        throw new StringIndexOutOfBoundsException(offset);
    }
    if (count <= 0) {
        if (count < 0) {
            throw new StringIndexOutOfBoundsException(count);
        }
        //如果他的偏移量是小于value数组的长度的话，就默认为空串
        if (offset <= value.length) {
            this.value = "".value;
            return;
        }
    }
    // Note: offset or count might be near -1>>>1.
    if (offset > value.length - count) {
        throw new StringIndexOutOfBoundsException(offset + count);
    }
    this.value = Arrays.copyOfRange(value, offset, offset+count);
}

public String(int[] codePoints, int offset, int count) {
    if (offset < 0) {
        throw new StringIndexOutOfBoundsException(offset);
    }
    if (count <= 0) {
        if (count < 0) {
            throw new StringIndexOutOfBoundsException(count);
        }
        if (offset <= codePoints.length) {
            this.value = "".value;
            return;
        }
    }
    // Note: offset or count might be near -1>>>1.
    if (offset > codePoints.length - count) {
        throw new StringIndexOutOfBoundsException(offset + count);
    }

    final int end = offset + count;

    // Pass 1: Compute precise size of char[]
    //这个n是用来计数一共需要用多少的位数，因为有辅助字符是需要4个字节的，而一般的字符只需要2个字节（UTF-16）
    int n = count;
    for (int i = offset; i < end; i++) {
        int c = codePoints[i];
        if (Character.isBmpCodePoint(c))
            continue;
        else if (Character.isValidCodePoint(c))
            n++;
        else throw new IllegalArgumentException(Integer.toString(c));
    }

    // Pass 2: Allocate and fill in char[]
    //创建真正的存char的数组，
    final char[] v = new char[n];
	//对每个码点，如果是bmp的话，可以直接转化，否则通过toSurrogates方法（在上面介绍过，是为了解决补充码点的）
    for (int i = offset, j = 0; i < end; i++, j++) {
        int c = codePoints[i];
        if (Character.isBmpCodePoint(c))
            v[j] = (char)c;
        else
            Character.toSurrogates(c, v, j++);
    }

    this.value = v;
}

@Deprecated
public String(byte ascii[], int hibyte, int offset, int count) {
    ...
}

@Deprecated
public String(byte ascii[], int hibyte) {
	...
}

//利用字节数组，并截取
    public String(byte bytes[], int offset, int length, String charsetName)
            throws UnsupportedEncodingException {
        if (charsetName == null)
            throw new NullPointerException("charsetName");
        checkBounds(bytes, offset, length);
        this.value = StringCoding.decode(charsetName, bytes, offset, length);
    }
//用于检查是否越界
    private static void checkBounds(byte[] bytes, int offset, int length) {
        if (length < 0)
            throw new StringIndexOutOfBoundsException(length);
        if (offset < 0)
            throw new StringIndexOutOfBoundsException(offset);
        if (offset > bytes.length - length)
            throw new StringIndexOutOfBoundsException(offset + length);
    }

```

- 常用字符串API

```Java
//指定索引处返回字符（Unicode代码点）
public int codePointAt(int index) {
    if ((index < 0) || (index >= value.length)) {
        throw new StringIndexOutOfBoundsException(index);
    }
    return Character.codePointAtImpl(value, index, value.length);
}

	//调用的是Character以下的codePointAtImpl方法
    static int codePointAtImpl(char[] a, int index, int limit) {
        char c1 = a[index];
        //判断是否是补充码点，并且index没有越界
        if (isHighSurrogate(c1) && ++index < limit) {
            //
            char c2 = a[index];
            //判断是否是低部分的补充码的码点
            if (isLowSurrogate(c2)) {
                return toCodePoint(c1, c2);//此方法是用来返回补充码的码点
            }
        }
        //如果是普通char就直接返回自动转型即可char-->int
        return c1;
    }
```

```Java
//返回相应index的char数组中的char
public char charAt(int index) {
    if ((index < 0) || (index >= value.length)) {
        throw new StringIndexOutOfBoundsException(index);
    }
    return value[index];
}
```

```Java
//返回在index之后，codePointOffset个的码点后的码点索引
public int offsetByCodePoints(int index, int codePointOffset) {
    if (index < 0 || index > value.length) {
        throw new IndexOutOfBoundsException();
    }
    //调用Character类里具体的类
    return Character.offsetByCodePointsImpl(value, 0, value.length,
            index, codePointOffset);
}

    static int offsetByCodePointsImpl(char[]a, int start, int count,
                                      int index, int codePointOffset) {
        int x = index;
        if (codePointOffset >= 0) {
            int limit = start + count;
            int i;
            //用于跳过用2个字节表示的补充码
            for (i = 0; x < limit && i < codePointOffset; i++) {
                if (isHighSurrogate(a[x++]) && x < limit &&
                    isLowSurrogate(a[x])) {
                    x++;
                }
            }
            if (i < codePointOffset) {
                throw new IndexOutOfBoundsException();
            }
            //和上面同理
        } else {
            int i;
            for (i = codePointOffset; x > start && i < 0; i++) {
                if (isLowSurrogate(a[--x]) && x > start &&
                    isHighSurrogate(a[x-1])) {
                    x--;
                }
            }
            if (i < 0) {
                throw new IndexOutOfBoundsException();
            }
        }
        return x;
    }
```

```Java
//将dst中的0-tstBegin的char字符，复制进该对象中，arrarycopy是本地方法了
void getChars(char dst[], int dstBegin) {
    System.arraycopy(value, 0, dst, dstBegin, value.length);
}
```

```Java
//
public byte[] getBytes(String charsetName)throws UnsupportedEncodingException {
    if (charsetName == null) throw new NullPointerException();
    return StringCoding.encode(charsetName, value, 0, value.length);
}
```

```Java
//用于比较两个String之间的字典顺序，如果长度不同，则返回该String对象.lenth-anotherString.lenth，否则返回第一个不同的k位码点之间的差值
public int compareTo(String anotherString) {
    int len1 = value.length;
    int len2 = anotherString.value.length;
    int lim = Math.min(len1, len2);
    char v1[] = value;
    char v2[] = anotherString.value;

    int k = 0;
    while (k < lim) {
        char c1 = v1[k];
        char c2 = v2[k];
        if (c1 != c2) {
            return c1 - c2;
        }
        k++;
    }
    return len1 - len2;
}
```

```Java
//Comparator类主要是一些比较相关的方法，
public static final Comparator<String> CASE_INSENSITIVE_ORDER
                                     = new CaseInsensitiveComparator();
private static class CaseInsensitiveComparator
        implements Comparator<String>, java.io.Serializable {
    
    private static final long serialVersionUID = 8575799808933029326L;

    public int compare(String s1, String s2) {
        int n1 = s1.length();
        int n2 = s2.length();
        int min = Math.min(n1, n2);
        for (int i = 0; i < min; i++) {
            char c1 = s1.charAt(i);
            char c2 = s2.charAt(i);
            if (c1 != c2) {
                c1 = Character.toUpperCase(c1);
                c2 = Character.toUpperCase(c2);
                if (c1 != c2) {
                    //这里做了一步用于不用区分大小写
                    c1 = Character.toLowerCase(c1);
                    c2 = Character.toLowerCase(c2);
                    if (c1 != c2) {
                        // No overflow because of numeric promotion
                        return c1 - c2;
                    }
                }
            }
        }
        return n1 - n2;
    }

    //用于获取相应的CaseInsensitiveComparator类的对象，（其实这里不太明白为什么不直接在String类中直接用这个方法，而是要创建个内部类，通过内部类来调用
    private Object readResolve() { return CASE_INSENSITIVE_ORDER; }
}
```

```Java
//用于判断2个字符串之间是否相同，这里需要和==区分开，==是比较2个字符串是否是出自同一个，而equals是比较的字符串内容(基本数据类型==比较的是值，引用数据类型==比较的是内存地址)
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    //所传入的object需要能转型成String才能进行比较
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        //先取出长度进行比较，如果相同，则进行逐个比较
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}

//和该函数相关的，有如下，只不过是忽略大小写的
public boolean equalsIgnoreCase(String anotherString) {
    //先比较是否是同一个字符串
        return (this == anotherString) ? true
                : (anotherString != null)//首先前提必须是判断的String是非空字符串
                && (anotherString.value.length == value.length)//anotherString和该String长度必须一样
                && regionMatches(true, 0, anotherString, 0, value.length);
    }

//比较这串的子串是否有相等的
    public boolean regionMatches(boolean ignoreCase, int toffset,
            String other, int ooffset, int len) {
        
        char ta[] = value;//该String的字符数组
        int to = toffset;
        char pa[] = other.value;//需要判断的字符数组
        int po = ooffset;
		//判断ooffset和toffset的范围是符合合理范围的
        if ((ooffset < 0) || (toffset < 0)
                || (toffset > (long)value.length - len)
                || (ooffset > (long)other.value.length - len)) {
            return false;
        }
        while (len-- > 0) {
            //将指定位置的char数组的值，赋值给C1和c2
            char c1 = ta[to++];
            char c2 = pa[po++];
            //如果直接相同就跳过
            if (c1 == c2) {
                continue;
            }
            //如果需要忽略大小写
            if (ignoreCase) {
                //toUpperCase是用来统一的
                char u1 = Character.toUpperCase(c1);
                char u2 = Character.toUpperCase(c2);
                //如果大写后的字符是同一个就跳过该字符
                if (u1 == u2) {
                    continue;
                }
                //如果小写后的字符是同一个就跳过该字符
                if (Character.toLowerCase(u1) == Character.toLowerCase(u2)) {
                    continue;
                }
            }
            return false;
        }
        return true;
    }
```

```Java
public boolean startsWith(String prefix, int toffset) {
    char ta[] = value;//该对象的String
    int to = toffset;//toffset的值是用于指定字符串的开始位置
    char pa[] = prefix.value;//前缀子串的值
    int po = 0;
    int pc = prefix.value.length;//前缀的长度
    // Note: toffset might be near -1>>>1.
    if ((toffset < 0) || (toffset > value.length - pc)) {
        return false;
    }
    while (--pc >= 0) {
        if (ta[to++] != pa[po++]) {
            return false;
        }
    }
    return true;
}
```

```Java
//indexOf是用于获取从第index位置向前数的找到相应字符的位置的
public int indexOf(int ch, int fromIndex) {
    final int max = value.length;//用于表示该对象的字符
    if (fromIndex < 0) {
        fromIndex = 0;//默认为首个字符的码点
    } else if (fromIndex >= max) {
        // Note: fromIndex might be near -1>>>1.
        return -1;//越过是不可能的，所有返回-1
    }
	//如果这个码点小于补充码点
    if (ch < Character.MIN_SUPPLEMENTARY_CODE_POINT) {
        final char[] value = this.value;
        for (int i = fromIndex; i < max; i++) {
            if (value[i] == ch) {
                return i;
            }
        }
        return -1;
    } else {
        //将做补充码处理，和上面提到的处理类似，遇到补充码则跳过4字节
        return indexOfSupplementary(ch, fromIndex);
    }
}
```

```Java
//从指定位置寻找目标子串的位置
static int lastIndexOf(char[] source, int sourceOffset, int sourceCount,
        char[] target, int targetOffset, int targetCount,
        int fromIndex) {
    //这是最右边的可能找到的起始位置
    int rightIndex = sourceCount - targetCount;
    //这是开始搜寻的位置
    if (fromIndex < 0) {
        return -1;
    }
    //如果要开始搜寻的位置>最右位置，则是不可能的，起始这里就可以直接返回了，因为容易被误解，但它默认是从最右开始
    if (fromIndex > rightIndex) {
        fromIndex = rightIndex;
    }
    //如果目标统计 为0，则直接返回要开始搜查的点即可
    if (targetCount == 0) {
        return fromIndex;
    }
	//##########################################################？？？
    int strLastIndex = targetOffset + targetCount - 1;
    char strLastChar = target[strLastIndex];//目标子串的某个字符
    int min = sourceOffset + targetCount - 1;//原字符偏移量+目标字符统计量
    int i = min + fromIndex;

    //这里是goto语句（没想到源码里还在用
startSearchForLastChar:
    while (true) {
        while (i >= min && source[i] != strLastChar) {
            i--;
        }
        if (i < min) {
            return -1;
        }
        int j = i - 1;
        int start = j - (targetCount - 1);
        int k = strLastIndex - 1;

        while (j > start) {
            if (source[j--] != target[k--]) {
                i--;
                continue startSearchForLastChar;
            }
        }
        return start - sourceOffset + 1;
    }
}
```

```Java
//获取子串，从开始的位置
public String substring(int beginIndex) {
    if (beginIndex < 0) {
        throw new StringIndexOutOfBoundsException(beginIndex);
    }
    //字符长度
    int subLen = value.length - beginIndex;
    if (subLen < 0) {
        throw new StringIndexOutOfBoundsException(subLen);
    }
    //这里返回的是新的字符串
    return (beginIndex == 0) ? this : new String(value, beginIndex, subLen);
}
```

```Java
//拼接字符串
public String concat(String str) {
    //要拼接的字符长度
    int otherLen = str.length();
    //如果是空串，就直接返回该对象
    if (otherLen == 0) {
        return this;
    }
    //该对象的长度
    int len = value.length;
    //可以看到它是返回一个新的字符串，先将前面的复制进去
    char buf[] = Arrays.copyOf(value, len + otherLen);
    //将后续的赋值进去
    str.getChars(buf, len);
    //再new一个新的String对象
    return new String(buf, true);
}
```

```Java
//将相应的字符串按大小隔断
public String[] split(String regex, int limit) {
   
    char ch = 0;
    //如果目标的子串长度为1 
    if (((regex.value.length == 1 &&
          //这些间隔符的是否包含有以下这些
         ".$|()[{^?*+\\".indexOf(ch = regex.charAt(0)) == -1) ||
         //或者它的长度为2，并且首个字符为\\
         (regex.length() == 2 &&
          regex.charAt(0) == '\\' &&
          //第二个字符，是否属于0-9，a-z，A-Z之间，由于只要是属于这之间的数，首个二进制数在做异或操作之后，一定是1所以比0小
          (((ch = regex.charAt(1))-'0')|('9'-ch)) < 0 &&
          ((ch-'a')|('z'-ch)) < 0 &&
          ((ch-'A')|('Z'-ch)) < 0)) &&
        //ch要满足高的代理区间的数
        (ch < Character.MIN_HIGH_SURROGATE ||
         ch > Character.MAX_LOW_SURROGATE))
    {
        int off = 0;
        int next = 0;
        boolean limited = limit > 0;
        //用于存储结果的
        ArrayList<String> list = new ArrayList<>();
        //当该对象一直有，该符号的时候
        while ((next = indexOf(ch, off)) != -1) {
 		    //如果要分割的间隔符合实际情况，并且list的长度是小于要分割的长度
            if (!limited || list.size() < limit - 1) {
                list.add(substring(off, next));
                off = next + 1;
            } else {    
                //否则是需要直接改成长度，然后跳出
                // last one
                //assert (list.size() == limit - 1);
                list.add(substring(off, value.length));
                off = value.length;
                break;
            }
        }
        
        if (off == 0)
            return new String[]{this};

        // Add remaining segment
        if (!limited || list.size() < limit)
            list.add(substring(off, value.length));

        // Construct result
        int resultSize = list.size();
        if (limit == 0) {
            while (resultSize > 0 && list.get(resultSize - 1).length() == 0) {
                resultSize--;
            }
        }
        String[] result = new String[resultSize];
        return list.subList(0, resultSize).toArray(result);
    }
    return Pattern.compile(regex).split(this, limit);
}
```

```Java
//用于将每个CharSequence加入到
public static String join(CharSequence delimiter, CharSequence... elements) {
    Objects.requireNonNull(delimiter);
    Objects.requireNonNull(elements);
    // Number of elements not likely worth Arrays.stream overhead.
    StringJoiner joiner = new StringJoiner(delimiter);
    for (CharSequence cs: elements) {
        joiner.add(cs);
    }
    return joiner.toString();
}

//StringJoiner是底层用StringBuilder实现的
public final class StringJoiner {
    private final String prefix;
    private final String delimiter;
    private final String suffix;
    
    private StringBuilder value;
```

```Java
//划分字符串
public String trim() {
        int len = value.length;//当前字符的长度
        int st = 0;
        char[] val = value;//获取当前对象的val
	    //直到遇到了st的长度
        while ((st < len) && (val[st] <= ' ')) {
            st++;
        }
    	//从后往前进行划分
        while ((st < len) && (val[len - 1] <= ' ')) {
            len--;
        }
    	//如果还没有划分完的话，就返回其中的，否则就直接返回this
        return ((st > 0) || (len < value.length)) ? substring(st, len) : this;
    }

```

```Java
//将String转换成相应的char数组
public char[] toCharArray() {
    char result[] = new char[value.length];
    System.arraycopy(value, 0, result, 0, value.length);
    return result;
}
```

```Java
//格式化并连接多个字符串对象
public static String format(String format, Object... args) {
    return new Formatter().format(format, args).toString();
}

public Formatter format(String format, Object ... args) {
    return format(l, format, args);
}

```

```Java
//这只是为了Obj为null的值
public static String valueOf(Object obj) {
    return (obj == null) ? "null" : obj.toString();
}
```

-----

这里需要提到一下关于**CharacterData类**，它是一个抽象类，里面定义了许多关于操作Charater的方法，

`isJavaIdentifierPart（）toLowerCase、toUpperCase、getNumericValue`等等抽象方法，里面唯一的具体方法**<u>of</u>**如下：

```Java
//根据码点，选择不同的具体的实现类，去实现上面的抽象方法，所以可以简单理解为，CharacterData类是操作Char的类，通过of方法获得实例化的对象，并进行相应的操作
static final CharacterData of(int ch) {
    if (ch >>> 8 == 0) {     // fast-path
        return CharacterDataLatin1.instance;
    } else {
        switch(ch >>> 16) {  //plane 00-16
        case(0):
            return CharacterData00.instance;
        case(1):
            return CharacterData01.instance;
        case(2):
            return CharacterData02.instance;
        case(14):
            return CharacterData0E.instance;
        case(15):   // Private Use
        case(16):   // Private Use
            return CharacterDataPrivateUse.instance;
        default:
            return CharacterDataUndefined.instance;
        }
    }
}
```

这里简单提出一个使用的例子，先调用CharacterData的静态方法，获得相应的对象（这里有点里氏替换法则/模板方法的意味），然后在调用相应对象的方法

![未命名文件](D:\App\Typora\resources\md-image\Java\未命名文件.png)

```Java
public static int toUpperCase(int codePoint) {
    return CharacterData.of(codePoint).toUpperCase(codePoint);
}
```

-----

```Java
//判定从指定偏移量开始，是否有由前缀构成的子串
public boolean startsWith(String prefix, int toffset) {
    char ta[] = value;//获取当前数组
    int to = toffset;
    char pa[] = prefix.value;//获取指定字符串数组
    int po = 0;
    int pc = prefix.value.length;//获取目标字符串长度
    //判定偏移量大于0,且偏移量+目标长度<=字符串总长度
    if ((toffset < 0) || (toffset > value.length - pc)) {
        return false;
    }
    //要判断完 ，所有的字符
    while (--pc >= 0) {
        //如果一旦有字符不匹配就返回false
        if (ta[to++] != pa[po++]) {
            return false;
        }
    }
    return true;
}
```

```Java
//判断是否由这个suffix结尾的
public boolean endsWith(String suffix) {
    return startsWith(suffix, value.length - suffix.value.length);
}
```

```Java
//返回这个字符串的指定字符的位置
public int indexOf(int ch) {
    return indexOf(ch, 0);
}

    public int indexOf(int ch, int fromIndex) {
        final int max = value.length;//获取到字符串数组长度
        //小于0表示0
        if (fromIndex < 0) {
            fromIndex = 0;
        } else if (fromIndex >= max) {
            // 返回-1表示输入的参数有误
            return -1;
        }
		//如果ch是普通普通字符
        if (ch < Character.MIN_SUPPLEMENTARY_CODE_POINT) {
            final char[] value = this.value;
            //依次找过去，直到value[i] == ch
            for (int i = fromIndex; i < max; i++) {
                if (value[i] == ch) {
                    return i;
                }
            }
            //-1表示找不到
            return -1;
        } else {
            return indexOfSupplementary(ch, fromIndex);
        }
    }

    private int indexOfSupplementary(int ch, int fromIndex) {
        //首先要判断是否是合法的字符码点的大小
        if (Character.isValidCodePoint(ch)) {
            //
            final char[] value = this.value;
            final char hi = Character.highSurrogate(ch);//返回高代理的字符（在上面提到提到过
            final char lo = Character.lowSurrogate(ch);//返回低代理的字符
            final int max = value.length - 1;
            for (int i = fromIndex; i < max; i++) {
                //要同时判断2个字符（其实是一共加起来算一个字符）
                if (value[i] == hi && value[i + 1] == lo) {
                    return i;
                }
            }
        }
        return -1;
    }
```

```Java
public int lastIndexOf(int ch) {
    return lastIndexOf(ch, value.length - 1);
}
```

```Java
//返回数组的相应长度
public int length() {
    return value.length;
}
```

```Java
//返回beginIndex到endIndex之间的Unicode码点个数，这个是不同于length的
public int codePointCount(int beginIndex, int endIndex) {
    //要beginIndex和endIndex符合正常范围
    if (beginIndex < 0 || endIndex > value.length || beginIndex > endIndex) {
        throw new IndexOutOfBoundsException();
    }
    return Character.codePointCountImpl(value, beginIndex, endIndex - beginIndex);
}

//通过在Character里面的codePointCountImpl方法
    static int codePointCountImpl(char[] a, int offset, int count) {
        int endIndex = offset + count;
        int n = count;//这个是起始点到终点之间的字符数
        for (int i = offset; i < endIndex; ) {
            //每过一个补充字符就n--，也就是减去补充码的个数
            if (isHighSurrogate(a[i++]) && i < endIndex &&
                isLowSurrogate(a[i])) {
                n--;
                i++;
            }
        }
        return n;
    }
```

```Java
//将字符数组中的值为oldChar替换为newChar
public String replace(char oldChar, char newChar) {
    if (oldChar != newChar) {
        int len = value.length;
        int i = -1;
        char[] val = value; 

        while (++i < len) {
            //当遇到第一个字符==oldChar，就跳出该循环
            if (val[i] == oldChar) {
                break;
            }
        }
        //要判断确定在该字符数组中确定有，如果没有的话，就i==len，其实前部分都是为了这个目的
        if (i < len) {
            char buf[] = new char[len];
            //将i之前的字符复制到buf[]中去
            for (int j = 0; j < i; j++) {
                buf[j] = val[j];
            }
            //将i之后的字符只要和oldChar相等的，就直接替换为newchar
            while (i < len) {
                char c = val[i];
                buf[i] = (c == oldChar) ? newChar : c;
                i++;
            }
            //？？？？这个构造方法的，boolean参数没被用到
            return new String(buf, true);
        }
    }
    return this;
}
```

```Java
//返回index为beginIndex到endIndex之间的子串
public String substring(int beginIndex, int endIndex) {
    if (beginIndex < 0) {
        throw new StringIndexOutOfBoundsException(beginIndex);
    }
    if (endIndex > value.length) {
        throw new StringIndexOutOfBoundsException(endIndex);
    }
    int subLen = endIndex - beginIndex;
    if (subLen < 0) {
        throw new StringIndexOutOfBoundsException(subLen);
    }
    return ((beginIndex == 0) && (endIndex == value.length)) ? this
            : new String(value, beginIndex, subLen);
}

    public String(char[] value, int offset, int count) {
        if (offset < 0) {
            throw new StringIndexOutOfBoundsException(offset);
        }
        //如果count<=0就返回空串
        if (count <= 0) {
            if (count < 0) {
                throw new StringIndexOutOfBoundsException(count);
            }
            if (offset <= value.length) {
                this.value = "".value;
                return;
            }
        }
        //如果起始值大于总长度-总数值数
        if (offset > value.length - count) {
            throw new StringIndexOutOfBoundsException(offset + count);
        }
        //返回初始值到结束值之间复制到value
        this.value = Arrays.copyOfRange(value, offset, offset+count);
    }
```

![image-20200829122234353](D:\App\Typora\resources\md-image\Java\image-20200829122234353.png)

- StringBuilder

  上面说String是不可变量，但是又由于需要有经常变动String的需求，所以产生了StringBuilder，它存储在父类`AbstractStringBuilder`中，是可变型的数组

  具体的如下：

  ```Java
  public final class StringBuilder
      extends AbstractStringBuilder
      implements java.io.Serializable, CharSequence
  {
      //其初始化的是它的父类的value数组
      public StringBuilder() {
          super(16);
      }
      ...
  }
      
  abstract class AbstractStringBuilder implements Appendable, CharSequence {
      //因为它的存储的数组并没有用final修饰这也是它的不同之处
      char[] value;
  	...
  }
  
  ```

### 3.3 大数

> 我们往往对于基本的整数和浮点数的精度是不能满足需求的，所以JDK就提供了2个类`BigDecimal`和`BigInteger`来专门处理**任意**长度的数字序列的数值，但是对于这2个类是不能使用基本的算术加减的，要通过方法来实现加减的运算，比如`add()`和`multiply()`

​	首先提到下这2个类都继承自`Number`类，主要是用于转型的

```java
public abstract class Number implements java.io.Serializable {
    //返回指定号码作为值 int ，这可能涉及舍入或截断。 
    public abstract int intValue();
    //返回指定数字的值为 long ，可能涉及四舍五入或截断。
    public abstract long longValue();
   	//返回指定数字的值为 float ，可能涉及四舍五入。 
    public abstract float floatValue();
    //返回指定数字的值为 double ，可能涉及四舍五入。 
    public abstract double doubleValue();
    //返回指定号码作为值 byte ，这可能涉及舍入或截断。 
    public byte byteValue() {
        return (byte)intValue();
    }
    //返回指定号码作为值 short ，这可能涉及舍入或截断。 
    public short shortValue() {
        return (short)intValue();
    }
   private static final long serialVersionUID = -8742448824652078965L;
}
```

- BigInteger

  ```java 
  public class BigInteger extends Number implements Comparable<BigInteger> {
  //此BigInteger的正负号：-1阴性，0表示零或1为阳性。 注意，零的BigInteger必须具有0到确保了正负号这是必要的，
  final int signum;
  //这是值的大小，以big-endian的方式（高位字节存入低地址，低位字节存入高地址，依次排列的方式），不可变的
  final int[] mag;
  
  //一些常用属性
      ...
          
  //构造函数
  public BigInteger(byte[] val) {
          if (val.length == 0)
              throw new NumberFormatException("Zero length BigInteger");
  
          if (val[0] < 0) {
              mag = makePositive(val);
              signum = -1;
          } else {
              mag = stripLeadingZeroBytes(val);
              signum = (mag.length == 0 ? 0 : 1);
          }
          if (mag.length >= MAX_MAG_LENGTH) {
          //MAX_MAG_LENGTH = Integer.MAX_VALUE / Integer.SIZE + 1; // (1 << 26)
              //用于判断是否超出了表示的范围
              checkRange();
          }
      }        
  //还有很多的构造函数都类似
   private BigInteger(int[] val) {
       ...
   }
      
  }
  ```

  1. 常用API

  ```java 
  //使byte数组的值变为正数，并返回以int[]数组
  private static int[] makePositive(byte a[]) {
          int keep, k;
          int byteLength = a.length;//数组的长度
  
          // 寻找第一个表示数值的数，因为它传入的是字节数组，第一位必为表示负值（调用这个函数的时候决定），所以它的第一位是-1，而前面提到过，计算器用补码表示-1是11111111
          for (keep=0; keep < byteLength && a[keep] == -1; keep++)
              ;
  
          //这里是跳至第一个非0值，如果它表示无符号的字节，比如全0，那么还需要分配额外的空间给它
          for (k=keep; k < byteLength && a[k] == 0; k++)
              ;
      
  	    //额外的字节空间（应对0的情况），如果是0的话，那么就是1，否则不需要
          int extraByte = (k == byteLength) ? 1 : 0;
      	//最终分配的长度为，字节总长度-跳过的长度+额外的字节空间=剩余总的长度+3 再除以4（因为Int要占4字节，但为什么加3？？？？
          int intLength = ((byteLength - keep + extraByte) + 3) >>> 2;
      	//分配的数组
          int result[] = new int[intLength];
  
          //字节长度-1
          int b = byteLength - 1;
      	//进行赋值
          for (int i = intLength-1; i >= 0; i--) {
              //不懂这里为什么要用&，直接取出来不行吗？？？？
              result[i] = a[b--] & 0xff;
              //要传输的字节数，以3来比较是为了不超过int的4字节？？
              int numBytesToTransfer = Math.min(3, b-keep+1);
              if (numBytesToTransfer < 0)
                  numBytesToTransfer = 0;
             
              for (int j=8; j <= 8*numBytesToTransfer; j += 8)
                  result[i] |= ((a[b--] & 0xff) << j);//每次向前推进8位，最多推进3次，刚好填满了int的32位
  
              // Mask indicates which bits must be complemented
              int mask = -1 >>> (8*(3-numBytesToTransfer));
              result[i] = ~result[i] & mask;
          }
  
          // Add one to one's complement to generate two's complement
          for (int i=result.length-1; i >= 0; i--) {
              result[i] = (int)((result[i] & LONG_MASK) + 1);
              if (result[i] != 0)
                  break;
          }
  
          return result;
      }
  ```

  ```Java
  //加法
  public BigInteger add(BigInteger val) {
      //如果其中一方为0则直接返回0
      if (val.signum == 0)
          return this;
      if (signum == 0)
          return val;
      //如果两个值相等，则直接调用add函数，并直接新一个对象new
      if (val.signum == signum)
          return new BigInteger(add(mag, val.mag), signum);
  
      int cmp = compareMagnitude(val);//获取比较val的情况
      if (cmp == 0)
          return ZERO;//直接返回0
      int[] resultMag = (cmp > 0 ? subtract(mag, val.mag)
                         : subtract(val.mag, mag));
      resultMag = trustedStripLeadingZeroInts(resultMag);//要返回的结果信息集
  	//判断符号
      return new BigInteger(resultMag, cmp == signum ? 1 : -1);
  }
  
  //将2个int类型的数组相加
  private static int[] add(int[] x, int[] y) {
          // 将x设置为长的那一方
          if (x.length < y.length) {
              int[] tmp = x;
              x = y;
              y = tmp;
          }
  	    
          int xIndex = x.length;
          int yIndex = y.length;
          //要返回的数组
          int result[] = new int[xIndex];
          //和为0
          long sum = 0;
      	//如果短的一方长度为1，
          if (yIndex == 1) {
              sum = (x[--xIndex] & LONG_MASK) + (y[0] & LONG_MASK) ;//将2个int值相加，得结果
              result[xIndex] = (int)sum;
          } else {
              while (yIndex > 0) {
                  sum = (x[--xIndex] & LONG_MASK) +
                        (y[--yIndex] & LONG_MASK) + (sum >>> 32);//删去上次的值，然后将结果存入result
                  result[xIndex] = (int)sum;
              }
          }
          // 是否长的数组还有剩余
          boolean carry = (sum >>> 32 != 0);
          while (xIndex > 0 && carry)
              carry = ((result[--xIndex] = x[xIndex] + 1) == 0);
  
          //赋值剩余的数值
          while (xIndex > 0)
              result[--xIndex] = x[xIndex];
  
          if (carry) {
              int bigger[] = new int[result.length + 1];
              System.arraycopy(result, 0, bigger, 1, result.length);
              bigger[0] = 0x01;
              return bigger;
          }
          return result;
      }
  
  
  //对不同的数组大小进行比较，相等就返回0，大于就返回1，否则-1
  final int compareMagnitude(BigInteger val) {
      	
          int[] m1 = mag;
          int len1 = m1.length;
          int[] m2 = val.mag;
          int len2 = m2.length;
          if (len1 < len2)
              return -1;
          if (len1 > len2)
              return 1;
      //各个字符进行比较 
      for (int i = 0; i < len1; i++) {
              int a = m1[i];
              int b = m2[i];
              if (a != b)
                  return ((a & LONG_MASK) < (b & LONG_MASK)) ? -1 : 1;
          }
          return 0;//如果长度为0，则直接返回0
      }
  
  	// 进行相减
      private static int[] subtract(int[] big, int[] little) {
          int bigIndex = big.length;
          int result[] = new int[bigIndex];//设置返回的结果集
          int littleIndex = little.length;//小的长度
          long difference = 0;
  
          // 进行相减
          while (littleIndex > 0) {
              difference = (big[--bigIndex] & LONG_MASK) -
                           (little[--littleIndex] & LONG_MASK) +
                           (difference >> 32);//这个是进位
              result[bigIndex] = (int)difference;//将原有的进位数字加上去
          }
  
          // 是否还有相应的剩余进位
          boolean borrow = (difference >> 32 != 0);
          //进行最后一次的
          while (bigIndex > 0 && borrow)
              borrow = ((result[--bigIndex] = big[bigIndex] - 1) == -1);
  
          //将剩余的进位加上去
          while (bigIndex > 0)
              result[--bigIndex] = big[bigIndex];
  
          return result;
      }
  ```

  ```Java
  //将2数相乘.....未解决
  public BigInteger multiply(BigInteger val) {
      return multiply(val, false);
  }
  
  //isRecursion用于判断是否进行溢出判断
      private BigInteger multiply(BigInteger val, boolean isRecursion) {
          //其中一为0，则直接返回0
          if (val.signum == 0 || signum == 0)
              return ZERO;
  		
          int xlen = mag.length;
  		//如果直接相等2数，则进行平方，函数见下
          if (val == this && xlen > MULTIPLY_SQUARE_THRESHOLD) {
              return square();
          }
  
          int ylen = val.mag.length;
  
          if ((xlen < KARATSUBA_THRESHOLD) || (ylen < KARATSUBA_THRESHOLD)) {
              //
              int resultSign = signum == val.signum ? 1 : -1;
              if (val.mag.length == 1) {
                  return multiplyByInt(mag,val.mag[0], resultSign);
              }
              if (mag.length == 1) {
                  return multiplyByInt(val.mag,mag[0], resultSign);
              }
              int[] result = multiplyToLen(mag, xlen,
                                           val.mag, ylen, null);
              result = trustedStripLeadingZeroInts(result);
              return new BigInteger(result, resultSign);
          } else {
              if ((xlen < TOOM_COOK_THRESHOLD) && (ylen < TOOM_COOK_THRESHOLD)) {
                  return multiplyKaratsuba(this, val);
              } else {
                  //
                  // In "Hacker's Delight" section 2-13, p.33, it is explained
                  // that if x and y are unsigned 32-bit quantities and m and n
                  // are their respective numbers of leading zeros within 32 bits,
                  // then the number of leading zeros within their product as a
                  // 64-bit unsigned quantity is either m + n or m + n + 1. If
                  // their product is not to overflow, it cannot exceed 32 bits,
                  // and so the number of leading zeros of the product within 64
                  // bits must be at least 32, i.e., the leftmost set bit is at
                  // zero-relative position 31 or less.
                  //
                  // From the above there are three cases:
                  //
                  //     m + n    leftmost set bit    condition
                  //     -----    ----------------    ---------
                  //     >= 32    x <= 64 - 32 = 32   no overflow
                  //     == 31    x >= 64 - 32 = 32   possible overflow
                  //     <= 30    x >= 64 - 31 = 33   definite overflow
                  //
                  // The "possible overflow" condition cannot be detected by
                  // examning data lengths alone and requires further calculation.
                  //
                  // By analogy, if 'this' and 'val' have m and n as their
                  // respective numbers of leading zeros within 32*MAX_MAG_LENGTH
                  // bits, then:
                  //
                  //     m + n >= 32*MAX_MAG_LENGTH        no overflow
                  //     m + n == 32*MAX_MAG_LENGTH - 1    possible overflow
                  //     m + n <= 32*MAX_MAG_LENGTH - 2    definite overflow
                  //
                  // Note however that if the number of ints in the result
                  // were to be MAX_MAG_LENGTH and mag[0] < 0, then there would
                  // be overflow. As a result the leftmost bit (of mag[0]) cannot
                  // be used and the constraints must be adjusted by one bit to:
                  //
                  //     m + n >  32*MAX_MAG_LENGTH        no overflow
                  //     m + n == 32*MAX_MAG_LENGTH        possible overflow
                  //     m + n <  32*MAX_MAG_LENGTH        definite overflow
                  //
                  // The foregoing leading zero-based discussion is for clarity
                  // only. The actual calculations use the estimated bit length
                  // of the product as this is more natural to the internal
                  // array representation of the magnitude which has no leading
                  // zero elements.
                  //
                  if (!isRecursion) {
                      // The bitLength() instance method is not used here as we
                      // are only considering the magnitudes as non-negative. The
                      // Toom-Cook multiplication algorithm determines the sign
                      // at its end from the two signum values.
                      if (bitLength(mag, mag.length) +
                          bitLength(val.mag, val.mag.length) >
                          32L*MAX_MAG_LENGTH) {
                          reportOverflow();
                      }
                  }
  
                  return multiplyToomCook3(this, val);
              }
          }
      }
  
  //平方方法
  private BigInteger square(boolean isRecursion) {
      	//如果有个为0 则0
          if (signum == 0) {
              return ZERO;
          }
          int len = mag.length;
  		//....整晕了11.3####
          if (len < KARATSUBA_SQUARE_THRESHOLD) {
              int[] z = squareToLen(mag, len, null);
              //将z的前导0全部删除
              return new BigInteger(trustedStripLeadingZeroInts(z), 1);
          } else {
              if (len < TOOM_COOK_SQUARE_THRESHOLD) {
                  return squareKaratsuba();
              } else {
                  //
                  // For a discussion of overflow detection see multiply()
                  //
                  if (!isRecursion) {
                      if (bitLength(mag, mag.length) > 16L*MAX_MAG_LENGTH) {
                          reportOverflow();
                      }
                  }
  
                  return squareToomCook3();
              }
          }
      }
  ```

- BigDecimal

1. 枚举

   针对有些变量的取值，只会在一定的范围内，所以出现了枚举

   ```java
   public enum  temp {
       temp1,temp2
   }
   ```

2. 运算符

   与（&）、非（~）、或（|）、异或（^）

   需要注意0/0和0.0/0和-1.0/0和1.0/0的结果

   Java中的浮点数运算其实是不严格的，可以使用关键字**strictfp**来使被它修饰的方法是采取严格的浮点数运算，比如，一些中间运算即使是原本64位的，它对中间结果的处理也会使用80位的来暂时存储中间结果，然后再计算最终结果，按不同的硬件条件来决定。

3. Math类

   如果需要更加严谨的`Math`类库的方法，可以试着用`StrictMath`类的相关方法

4. 数值之间的类型转换

   以下这些是无精度损失的：

    ![image-20200815221109433](D:\App\Typora\resources\md-image\Java\image-20200815221109433.png)

5. 输入与输出

   - 输出流需要只需要调用

     ```
     System.out.println();
     ```

   - 输入流

     首先需要构造一个输入对象

     ```java 
     Scanner sr=new Scanner(System.in);
     ```

6. 结构体

   - 循环

     这个跳出循环的时候会直接带到tag处，可以简单的理解为goto

     ```Java
     public static void main(String[] args) {
         int i=1;
         tag:
         while(i++<100){
             for (int k = 0; k < 100; k++) {
                 if (i==2) break tag;
             }
         }
     }
     ```

7. 数组

8. 大数

### 3.4 集合类型

> 集合类型大多存放于`Utils`包下，
>
> 可主要分为如下五类：
>
> ​	List列表
>
> ​	Set集合
>
> ​	Map映射
>
> ​	迭代器（Iterator、Enumeration）
>
> ​	工具类（Arrays、Collections）

`Collection接口`是所有集合类的根接口

![img](https://img-blog.csdn.net/20171231221535412)



#### 1. 迭代器

- **Iterable** 

  ```java 
  public interface Iterable<T> {
      Iterator<T> iterator();//返回一个相应的迭代器
      default void forEach(Consumer<? super T> action) {...}
      default Spliterator<T> spliterator() {...}
  }
  ```

- **Iterator** 

  ```java
  public interface Iterator<E> {
      //判断是否还有下一个元素
      boolean hasNext();
  	//获取到下一个元素
      E next();
      //进行移除元素，具体的实现由子类进行实现
      default void remove() {
          throw new UnsupportedOperationException("remove");
      }
      //对剩下的元素进行相应的遍历
      default void forEachRemaining(Consumer<? super E> action) {
          Objects.requireNonNull(action);
          while (hasNext())
              action.accept(next());
      }
  }
  ```

- 具体实现（以`AbstractList`为例）

  ```java
  //外部类就调用相应的方法获取到Itr的实例就可以，
  //这是属于AbstractList中的私有类但是外部享有方法能够返回相应的实例
  private class Itr implements Iterator<E> {
  	//光标，表示当前遍历到哪一地方了
      int cursor = 0;
      //最近一次调用下一个或上一个返回的元素的索引。 如果通过调用remove删除了此元素，则重置为-1
      int lastRet = -1;
      //迭代器认为后备列表应该具有的modCount值。 如果违反了此期望，则迭代器已检测到并发修改，这里是关键
      int expectedModCount = modCount;
  
      //表示当前的光标是不是Size/最后了
      public boolean hasNext() {
          return cursor != size();
      }
  
      //获取下一个元素
      public E next() {
          checkForComodification();
          try {
              //当前的光标
              int i = cursor;
              //根据相应的位置获取元素，具体由子类进行实现
              E next = get(i);
              //刷新最后返回的标记
              lastRet = i;
              //游标加1
              cursor = i + 1;
              //返回元素
              return next;
          } catch (IndexOutOfBoundsException e) {
              checkForComodification();
              throw new NoSuchElementException();
          }
      }
  
      //移除上次标记的位置的元素
      public void remove() {
          //判断最后返回的游标是否＜0
          if (lastRet < 0)
              throw new IllegalStateException();
          checkForComodification();
          try {
              //调用外部AbstractList的remove方法，
              AbstractList.this.remove(lastRet);
              if (lastRet < cursor)
                  cursor--;
              //移除了之后自然为-1
              lastRet = -1;
              //并且同步相应的期待版本号和外部类的版本号一致
              expectedModCount = modCount;
          } catch (IndexOutOfBoundsException e) {
              throw new ConcurrentModificationException();
          }
      }
      
  	//检查并发修改
      final void checkForComodification() {
          if (modCount != expectedModCount)
              throw new ConcurrentModificationException();
      }
  }
  ```

-  tmp

#### 2. ArrayList

>  数据可重复的，ArrayList 底层是基于数组来实现容量大小动态变化的。

- 存储的数据

  ```java 
  transient Object[] elementData; // 实际存放的数据，所以elementData.length就可以取出最大的数据容量了
  private int size;//实际的元素个数
  private static final Object[] EMPTY_ELEMENTDATA = {};//用于空实例共享的数组
  private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};//用于默认大小的空数组实例，主要是我们将其与EMPTY_ELEMENTDATA区分开来，以了解添加第一个元素时扩容多少。具体的可以查看下面的构造函数
  
  protected transient int modCount = 0;//实际上被修改的次数（版本数），之所以不能被序列化也是为了方便序列化之后能够重新计数
  ```

- 构造函数

  ```java
  //构造函数主要是3种
  //1.空参数构造，直接赋值，默认的这里其实是赋值了一个共享的空数组，数组在第一次添加元素时会判断elementData是否等于DEFAULTCAPACITY_EMPTY_ELEMENTDATA，假如等于则会初始化容量为DEFAULT_CAPACITY 也就是10
  public ArrayList() {
  	this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
  }
  //2.指定大小的构造函数，主要是判断其大小是否大于0
  public ArrayList(int initialCapacity) {
  	if (initialCapacity > 0) {
          this.elementData = new Object[initialCapacity];
      } else if (initialCapacity == 0) {
          this.elementData = EMPTY_ELEMENTDATA;//赋予相应的默认空的0的大小的空数组实例
      } else {
          throw new IllegalArgumentException("Illegal Capacity: "+
                                             initialCapacity);
      }
  }
  //3.赋予相应的集合类
      public ArrayList(Collection<? extends E> c) {
          elementData = c.toArray();
          if ((size = elementData.length) != 0) {
              if (elementData.getClass() != Object[].class)
                  elementData = Arrays.copyOf(elementData, size, Object[].class);
          } else {//空集合则赋值给无默认大小的那个空数组实例
          this.elementData = EMPTY_ELEMENTDATA;
      }
  }
  ```

- 迭代器

- 确认并修改数组内部容量

  ```java
  private void ensureCapacityInternal(int minCapacity) {
      ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
  }
  
  //计算容量大小的，用于为了上面提到的默认初始化大小的空数组的初始化
  private static int calculateCapacity(Object[] elementData, int minCapacity) {
      //如果是空数组则说明是初始化，直接返回给默认的是初始化的大小，这里是10，但是如果是期待的最小容量>默认的容量大小
          if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
              return Math.max(DEFAULT_CAPACITY, minCapacity);
          }
      //直接返回最小容量
          return minCapacity;
  }
  
  //确保数组的容量，
  private void ensureExplicitCapacity(int minCapacity) {
          modCount++;//修改该对象的版本数
      	//如果最小期待的容量>数组当前的长度，则需要进行扩容
          if (minCapacity - elementData.length > 0)
              grow(minCapacity);
  }
  
  //扩容
  private void grow(int minCapacity) {
          int oldCapacity = elementData.length;//当前的数组最大长度
          int newCapacity = oldCapacity + (oldCapacity >> 1);//新的容量=旧容量+旧容量的1/2=1.5*旧容量
      	//如果新的容量<最小容量，则新容量=最小容量
          if (newCapacity - minCapacity < 0)
              newCapacity = minCapacity;
      	//如果新容量>最大的允许值，则新的容量=最大的
          if (newCapacity - MAX_ARRAY_SIZE > 0)
              newCapacity = hugeCapacity(minCapacity);
          //重新赋值到elementData中去
          elementData = Arrays.copyOf(elementData, newCapacity);
  }
  
  //也就是多了一个字节的容量，并且限制到最大的容量而已
  private static int hugeCapacity(int minCapacity) {
          if (minCapacity < 0) // overflow
              throw new OutOfMemoryError();
      //如果最小期待容量>最大值，则返回Integer.MAX_VALUE，否则是默认的最大值MAX_ARRAY_SIZE
          return (minCapacity > MAX_ARRAY_SIZE) ?
              Integer.MAX_VALUE :
              MAX_ARRAY_SIZE;
  }
  ```

- 添加元素

  ```java
  //方法一
  public boolean add(E e) {
      //确认容量，扩容，增加版本号
          ensureCapacityInternal(size + 1);  
      //赋值
          elementData[size++] = e;
          return true;
  }
  
  //方法二
  public void add(int index, E element) {
          rangeCheckForAdd(index);//检查范围是否合理
  
          ensureCapacityInternal(size + 1);  // Increments modCount!!
      	//使用本地方法进行数据的定位复制
          System.arraycopy(elementData, index, elementData, index + 1,
                           size - index);
      	//然后再将指定的位置进行元素的赋值
          elementData[index] = element;
      	//记得修改相应的数组大小
          size++;
  }
  ```

- 获取元素

  ```java
  public E get(int index) {
              rangeCheck(index);
              checkForComodification();
              return ArrayList.this.elementData(offset + index);
  }
  
  //进行版本的比较防止并发产生问题
  private void checkForComodification() {
       if (ArrayList.this.modCount != this.modCount)
             throw new ConcurrentModificationException();
  }
  
  //用于进行返回元素的强转
      @SuppressWarnings("unchecked")
      E elementData(int index) {
          return (E) elementData[index];
      }
  ```

- 赋值元素

  ```java
  public E set(int index, E element) {
          //index的大小检查
          rangeCheck(index);
  	    //获取到相应的旧值，用于返回
          E oldValue = elementData(index);
      	//覆盖赋值
          elementData[index] = element;
          return oldValue;
  }
  ```

- 判断是否含有相应的元素

  ```java
  //是否包含
  public boolean contains(Object o) {
          return indexOf(o) >= 0;
  }
  
  //依次判断是否含有
  public int indexOf(Object o) {
          if (o == null) {
              for (int i = 0; i < size; i++)
                  if (elementData[i]==null)
                      return i;
          } else {
              //依次遍历
              for (int i = 0; i < size; i++)
                  if (o.equals(elementData[i]))
                      return i;
          }
          return -1;
  }
  ```

- 取得子List

  ```java
   //先介绍相应的SubList，是ArrayList的一个私有内部类，
   private class SubList extends AbstractList<E> implements RandomAccess {
       	//保存它的所从属的List的引用
          private final AbstractList<E> parent;
       	//这里所做的标记是专门用于表明其实SubList所用的List是同一个
          private final int parentOffset;
          private final int offset;
          int size;
  ```


#### 3. Vecotr

> `Vector` 可实现自动增长的对象数组，类似于C++中的动态数组.
>
> `Vector`和`ArrayList`的最大区别就是：
>
> 1. `Vector`的大部分方法是线程同步的，而`ArrayList`是线程不同步的所以性能会远高于`Vector` ；
> 2. 扩容时的大小不一样，`Vector`的默认的增长是成倍增长

- 存储数据

  ```java
      protected Object[] elementData;//用于存放的数据
      protected int elementCount;//实际数据的数量，类似于ArrayList的Size
      protected int capacityIncrement;//扩容的大小
  ```

- 构造方法

  ```java
  //Vector的初始化是初始化了1个容量为 10 的数组，同时初始化变量 capacityIncrement 为 0
  public Vector(int initialCapacity, int capacityIncrement) {
          super();
          if (initialCapacity < i0)
              throw new IllegalArgumentException("Illegal Capacity: "+
                                                 initialCapacity);
          this.elementData = new Object[initialCapacity];
          this.capacityIncrement = capacityIncrement;
  }
  public Vector(int initialCapacity) {
          this(initialCapacity, 0);
  }
  public Vector() {
          this(10);//默认长度为10
  }
  public Vector(Collection<? extends E> c) {
          elementData = c.toArray();
          elementCount = elementData.length;
          if (elementData.getClass() != Object[].class)
              elementData = Arrays.copyOf(elementData, elementCount, Object[].class);
  }
  ```

- 添加元素

  ```java
  //方法1
  public synchronized boolean add(E e) {
          modCount++;//修改版本
          ensureCapacityHelper(elementCount + 1);
          elementData[elementCount++] = e;
          return true;
  }
  
  //确认相应的容量
  private void ensureCapacityHelper(int minCapacity) {
          // 容量不够
          if (minCapacity - elementData.length > 0)
              
              grow(minCapacity);
  }
  
  //扩容方法，但是扩容大小和Arraylist不同
  private void grow(int minCapacity) {
          
          int oldCapacity = elementData.length;
  		//扩容是根据capacityIncrement来设置，如果capacityIncrement没有设置，则扩容整整一倍，否则扩容相应的capacityIncrement大小
          int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                           capacityIncrement : oldCapacity);
          if (newCapacity - minCapacity < 0)
              newCapacity = minCapacity;
          if (newCapacity - MAX_ARRAY_SIZE > 0)
              newCapacity = hugeCapacity(minCapacity);//这里和ArrayList一致
          elementData = Arrays.copyOf(elementData, newCapacity);
  }
  
  //方法2，在指定位置加载
  public void add(int index, E element) {
          insertElementAt(element, index);
  }
  
  //
  public synchronized void insertElementAt(E obj, int index) {
          modCount++;
      	//规定指定位置不符合相应的
          if (index > elementCount) {
              throw new ArrayIndexOutOfBoundsException(index
                                                       + " > " + elementCount);
          }
      	//确认是否扩容
          ensureCapacityHelper(elementCount + 1);
      	//
          System.arraycopy(elementData, index, elementData, index + 1, elementCount - index);
          elementData[index] = obj;
          elementCount++;
  }
  ```

- 删除元素

  ```java
  public synchronized E remove(int index) {
          modCount++;//版本的迭代
      	//范围约束
          if (index >= elementCount)
              throw new ArrayIndexOutOfBoundsException(index);
          //获取到相应的元素用于返回
      	E oldValue = elementData(index);
  		//需要移动的元素数量
          int numMoved = elementCount - index - 1;
          if (numMoved > 0)
              System.arraycopy(elementData, index+1, elementData, index,
                               numMoved);
          elementData[--elementCount] = null; // Let gc do its work
          return oldValue;
  }Vecotr
  ```

- tmp

#### 4. Stack

> 它是继承自`Vector`的，所以也是实际上把数组元素维护成一个栈而已

- `push`元素

  ```java
  public E push(E item) {
          addElement(item);//默认是放在最后一个的
          return item;
  }
  ```

- pop元素

  ```java
  public synchronized E pop() {
      E       obj;
      int     len = size();
  
      obj = peek();//获取到最后一个元素
      removeElementAt(len - 1);//移除最后一个
  
      return obj;
  }
  
  //用于窥视元素
  public synchronized E peek() {
          int     len = size();
  
          if (len == 0)
              throw new EmptyStackException();
          return elementAt(len - 1);
  }
  ```

- 查找元素

  ```java
  public synchronized int search(Object o) {
      	//返回所在的位置
          int i = lastIndexOf(o);
          if (i >= 0) {
              return size() - i;
          }
          return -1;
  }
  ```

- tmp

#### 5. LinkedList

> 这是完全不同于`ArrayList`的集合类，它底层是维护了一种链表，关键在于节点类

- `Node`结点

  ```java
  private static class Node<E> {
          E item;//当前存储元素
          Node<E> next;//后指针
          Node<E> prev;//前指针
  
          Node(Node<E> prev, E element, Node<E> next) {
              this.item = element;
              this.next = next;
              this.prev = prev;
          }
  }
  ```

- 存储数据

  ```java
      transient int size = 0;//链表长度
      transient Node<E> first;//头节点
      transient Node<E> last;//尾节点
  ```

- 构造方法

  ```java
  public LinkedList() {
  }
  
  public LinkedList(Collection<? extends E> c) {
          this();
          addAll(c);
  }
  
  public boolean addAll(Collection<? extends E> c) {
          return addAll(size, c);
  }
  
  //这是最终调用的有传入的数据的构造方法，也是体现了链表的特性
  public boolean addAll(int index, Collection<? extends E> c) {
          checkPositionIndex(index);
      	//先取得相应的数据数组
          Object[] a = c.toArray();
      	//获取到相应的数据数量
          int numNew = a.length;
      	//数量不能为0，否则就失去了这个方法的意义
          if (numNew == 0)
              return false;
  		//创建2个节点
          Node<E> pred, succ;
      	//接下来就是双向链表的初始化
          if (index == size) {
              succ = null;
              pred = last;
          } else {
              succ = node(index);
              pred = succ.prev;
          }
  
          for (Object o : a) {
              @SuppressWarnings("unchecked") E e = (E) o;
              Node<E> newNode = new Node<>(pred, e, null);
              if (pred == null)
                  first = newNode;
              else
                  pred.next = newNode;
              pred = newNode;
          }
  
          if (succ == null) {
              last = pred;
          } else {
              pred.next = succ;
              succ.prev = pred;
          }
  
          size += numNew;
          modCount++;
          return true;
  }
  ```

- 查找元素

  ```java
  //根据index在哪一边来决定相应的，从尾还是头进行遍历，以此减少相应的遍历浪费
  Node<E> node(int index) {
     
          if (index < (size >> 1)) {
              Node<E> x = first;
              for (int i = 0; i < index; i++)
                  x = x.next;
              return x;
          } else {
              Node<E> x = last;
              for (int i = size - 1; i > index; i--)
                  x = x.prev;
              return x;
          }
  }
  ```

- 清除元素

  ```java
  public void clear() {
          for (Node<E> x = first; x != null; ) {
              Node<E> next = x.next;//可以看到是保存头节点
              x.item = null;//清空所有的元素
              x.next = null;
              x.prev = null;
              x = next;//指向下一个元素
          }
          first = last = null;//清除头节点
          size = 0;
          modCount++;
  }
  ```

- 添加头元素

  ```java
  public void addFirst(E e) {
      linkFirst(e);
  }
  private void linkFirst(E e) {
          final Node<E> f = first;
          final Node<E> newNode = new Node<>(null, e, f);
          first = newNode;
          if (f == null)
              last = newNode;
          else
              f.prev = newNode;
          size++;
          modCount++;
  }
  ```

- tmp

#### 6. Map

> 这是所有的`Map`集合类下的接口，主要是一些操作`Map`集合类的方法，以及`Entry`类用于存储相应的`K-V`值.

```java
interface Entry<K,V> {
    K getKey();
    V getValue();
    V setValue(V value);
    boolean equals(Object o);
    int hashCode();//给每个Entry实体类（键值对）不同的Hash值，
    //一些不常用的比较方法
    public static <K extends Comparable<? super K>, V> Comparator<Map.Entry<K,V>> comparingByKey() {
        return (Comparator<Map.Entry<K, V>> & Serializable)
            (c1, c2) -> c1.getKey().compareTo(c2.getKey());
    }
    public static <K, V extends Comparable<? super V>> Comparator<Map.Entry<K,V>> comparingByValue() {
        return (Comparator<Map.Entry<K, V>> & Serializable)
            (c1, c2) -> c1.getValue().compareTo(c2.getValue());
    }
    public static <K, V> Comparator<Map.Entry<K, V>> comparingByKey(Comparator<? super K> cmp) {
        Objects.requireNonNull(cmp);
        return (Comparator<Map.Entry<K, V>> & Serializable)
            (c1, c2) -> cmp.compare(c1.getKey(), c2.getKey());
    }
public static <K, V> Comparator<Map.Entry<K, V>> comparingByValue(Comparator<? super V> cmp) {
        Objects.requireNonNull(cmp);
        return (Comparator<Map.Entry<K, V>> & Serializable)
            (c1, c2) -> cmp.compare(c1.getValue(), c2.getValue());
    }
}
```

#### 7. HashMap

> 散列函数又名Hash，实现Map接口的双列集合，数据结构是“链表散列”，也就是数组+链表 ，key唯一，value可以重复，允许存储null 键null 值，元素无序。

`HashMap`里面有想要的`Entry`其实就是一个键值对的接口

下面为大家介绍**6种哈希表常用方法**：

1. 直接寻址法：取关键字或关键字的某个线性函数值为散列地址。即H(key)=key或H(key) = a·key + b，其中a和b为常数（这种散列函数叫做自身函数）。若其中H(key）中已经有值了，就往下一个找，直到H(key）中没有值了，就放进去。

2. 数字分析法：分析一组数据，比如一组员工的出生年月日，这时我们发现出生年月日的前几位数字大体相同，这样的话，出现冲突的几率就会很大，但是我们发现年月日的后几位表示月份和具体日期的数字差别很大，如果用后面的数字来构成散列地址，则冲突的几率会明显降低。因此数字分析法就是找出数字的规律，尽可能利用这些数据来构造冲突几率较低的散列地址。

3. 平方取中法：当无法确定关键字中哪几位分布较均匀时，可以先求出关键字的平方值，然后按需要取平方值的中间几位作为哈希地址。这是因为：平方后中间几位和关键字中每一位都相关，故不同关键字会以较高的概率产生不同的哈希地址。 [1]

   例：我们把英文字母在字母表中的位置序号作为该英文字母的内部编码。例如K的内部编码为11，E的内部编码为05，Y的内部编码为25，A的内部编码为01, B的内部编码为02。由此组成关键字“KEYA”的内部代码为11052501，同理我们可以得到关键字“KYAB”、“AKEY”、“BKEY”的内部编码。之后对关键字进行平方运算后，取出第7到第9位作为该关键字哈希地址。 

4. 折叠法：将关键字分割成位数相同的几部分，最后一部分位数可以不同，然后取这几部分的叠加和（去除进位）作为散列地址。数位叠加可以有移位叠加和间界叠加两种方法。移位叠加是将分割后的每一部分的最低位对齐，然后相加；间界叠加是从一端向另一端沿分割界来回折叠，然后对齐相加。

5. 随机数法：选择一随机函数，取关键字的随机值作为散列地址，即H(key)=random(key)其中random为随机函数,通常用于关键字长度不等的场合。

6. 除留余数法：取关键字被某个不大于散列表表长m的数p除后所得的余数为散列地址。即 H(key) = key MOD p,p<=m。不仅可以对关键字直接取模，也可在折叠、平方取中等运算之后取模。对p的选择很重要，一般取素数或m，若p选的不好，容易产生同义词。

处理`Hash`冲突的几种方法：

1. 开放定址法
2. 再哈希法
3. 链地址法

- `Hash`表的组成

  是**数组**+**链表**，可以说，我们先来了解数组：

  查找：通过下标，复杂度为O(1)；通过给定值查找，复杂度为O(n)； 二分查找插值查找，复杂度为O(logn)

  线性列表：增、删 仅处理结点，时间复杂度O(1)，查找需要遍历也就是O(n)；

  二叉树：对一颗相对平衡的有序二叉树，对其进行插入，查找，删除，平均复杂度O(logn)

  哈希表：哈希表中进行添加，删除，查找等操作，性能十分之高，不考虑哈希冲突的情况下，仅需一次定位即可完成，时间复杂度为O(1)哈希表的主干就是数组

- hash值

  ```Java
  static final int hash(Object key) {//传入的对象
      int h;
      return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);//如果是非Null的对象的话，就让它的后16位进行与操作
  }
  ```

- 存储的数据

  ```java
  transient Node<K,V>[] table;//用于存储数据的
  transient Set<Map.Entry<K,V>> entrySet;//用来存放缓存
  transient int size;//当前数据大小
  transient int modCount;//版本记录
  int threshold;//临界值，也就是下一个要调整的容量的值的计算方式：loadFactor*capacity
  final float loadFactor;//加载因子，是用来衡量 HashMap 满的程度，计算HashMap的实时加载因子的方法为：size/capacity，而不是占用桶的数量去除以capacity。capacity 是桶的数量，也就是 table 的长度length
  
  //----------默认的值------------//
  static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // 默认初始化数组长度
  static final int MAXIMUM_CAPACITY = 1 << 30;//2^30次方最大长度
  static final float DEFAULT_LOAD_FACTOR = 0.75f;//默认加载因子
  static final int TREEIFY_THRESHOLD = 8;//链表长度的阈值，当链表的值超过8则会转红黑树
  static final int UNTREEIFY_THRESHOLD = 6;//下阈值，当链表的值小于6则会从红黑树转回链表
  static final int MIN_TREEIFY_CAPACITY = 64;//当Map里面的数量超过这个值时，表中的桶才能进行树形化 ，否则桶内元素太多时会扩容，而不是树形化 为了避免进行扩容、树形化选择的冲突，这个值不能小于 4 * TREEIFY_THRESHOLD
  ```

  - 不同的节点类型

  ```Java
  //1.正常的含有一个指向下一个元素指针的节点
  static class Node<K,V> implements Map.Entry<K,V>{
      final int hash;//真正存储的hash值
      final K key;//一旦复制不能修改
      V value;//value值
      Node<K,V> next;//下一个节点，所以Hashmap其实维护了一个单向链表
  
      Node(int hash, K key, V value, Node<K,V> next) {
          this.hash = hash;
          this.key = key;
          this.value = value;
          this.next = next;
      }
  
      public final K getKey()        { return key; }
      public final V getValue()      { return value; }
      public final String toString() { return key + "=" + value; }
  
      public final int hashCode() {
          return Objects.hashCode(key) ^ Objects.hashCode(value);//用于生成相应的Hash值
      }
  
      public final V setValue(V newValue) {
          V oldValue = value;
          value = newValue;
          return oldValue;
      }
  
      public final boolean equals(Object o) {
          if (o == this)
              return true;
          if (o instanceof Map.Entry) {
              Map.Entry<?,?> e = (Map.Entry<?,?>)o;
              if (Objects.equals(key, e.getKey()) &&
                  Objects.equals(value, e.getValue()))
                  return true;
          }
          return false;
      }
  }
  
  //2.TreeNode
  static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
          TreeNode<K,V> parent;  // 红黑树中的父节点
          TreeNode<K,V> left;//左孩子节点
          TreeNode<K,V> right;//右孩子节点
          TreeNode<K,V> prev;    // needed to unlink next upon deletion？？？？？
          boolean red;//颜色标记
  ...其他方法
  }
  
  //3.LinkedHashMap的Entry，
  static class Entry<K,V> extends HashMap.Node<K,V> {
          Entry<K,V> before, after;
          Entry(int hash, K key, V value, Node<K,V> next) {
              super(hash, key, value, next);
          }
  }
  ```

- 构造方法

  ```java
  //构造方法里，并没有进行table数组的初始化
  public HashMap(int initialCapacity) {
          this(initialCapacity, DEFAULT_LOAD_FACTOR);
  }
  public HashMap() {
          this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
  }   
  
  //判断进行一些容量大小的判断
  public HashMap(int initialCapacity, float loadFactor) {
          if (initialCapacity < 0)
              throw new IllegalArgumentException("Illegal initial capacity: " +
                                                 initialCapacity);
          if (initialCapacity > MAXIMUM_CAPACITY)
              initialCapacity = MAXIMUM_CAPACITY;
          if (loadFactor <= 0 || Float.isNaN(loadFactor))
              throw new IllegalArgumentException("Illegal load factor: " +
                                                 loadFactor);
          this.loadFactor = loadFactor;
          this.threshold = tableSizeFor(initialCapacity);//进行相应的2^N的转换，因为这个容量值
  }
  
  //对于给定的目标容量，返回两倍大小的幂
      static final int tableSizeFor(int cap) {
          int n = cap - 1;
          n |= n >>> 1;
          n |= n >>> 2;
          n |= n >>> 4;
          n |= n >>> 8;
          n |= n >>> 16;
          return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
      }
  ```

- 扩容

  ```java
  final Node<K,V>[] resize() {
      Node<K,V>[] oldTab = table;//table进行赋值
      int oldCap = (oldTab == null) ? 0 : oldTab.length;//旧的容量大小
      int oldThr = threshold;//要调整的下一个容量的阈值
      int newCap, newThr = 0;//新的容量和新的阈值
      //------------------赋新容量的值----------------------------------------------
      if (oldCap > 0) {
          //如果旧的容量>=最大容量，则让阈值=Integer的最大值，并直接返回旧的Table表，因为不能再扩容了 
          if (oldCap >= MAXIMUM_CAPACITY) {
              threshold = Integer.MAX_VALUE;
              return oldTab;
          }
          //否则成倍扩容
          else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                   oldCap >= DEFAULT_INITIAL_CAPACITY)
              //阈值也随之成倍增长
              newThr = oldThr << 1; 
      }
      //如果旧的容量==0,并且希望扩容，则需要将相应的新容量变成旧的阈值
      else if (oldThr > 0)
          newCap = oldThr;
      //如果阈值都没有，说明还没初始化完全，则使用初始化阈值和默认的加载因子，赋值给相应的新阈值以及相应的新容量
      else {              
          newCap = DEFAULT_INITIAL_CAPACITY;
          newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
      }
      //如果新的阈值==0，
      if (newThr == 0) {
          float ft = (float)newCap * loadFactor;//Ft=负载因子*新的容量==》这个容量是大于最大容量的==>最终ft的0.75
          //新的阈值=必须小于最大的容量
          newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                    (int)ft : Integer.MAX_VALUE);
      }
     
      threshold = newThr;//将确认后的阈值赋值给threshold
       //--------------------------赋值完毕---------------------
      @SuppressWarnings({"rawtypes","unchecked"})
      //这是扩容后的数组
      Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
      table = newTab;//这就是新的数组
      if (oldTab != null) {
          //遍历旧的集合中的每个值
          for (int j = 0; j < oldCap; ++j) {
              //遍历到的节点
              Node<K,V> e;
              if ((e = oldTab[j]) != null) {
                  oldTab[j] = null;//将原有的元素先置空
                  if (e.next == null)//如果在那个桶中只有一个元素则rehash一下,拿到新的数组的位置
                      newTab[e.hash & (newCap - 1)] = e;//将元素赋值到相应的Hash的地方
                  else if (e instanceof TreeNode)//如果是属于相应的树节点，这里也就是为什么TreeNode需要继承自LinkedHashMap.Entry（继承自HashMap.Node
                      //
                      ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                  else { // 链表的再维护
                      Node<K,V> loHead = null, loTail = null;
                      Node<K,V> hiHead = null, hiTail = null;
                      Node<K,V> next;
                      do {
                          next = e.next;
                          //hash值和旧容量做了&操作，为什么详见下
                          //位置还在原来桶所在位置的元素
                          if ((e.hash & oldCap) == 0) {
                              if (loTail == null)
                                  loHead = e;
                              else
                                  loTail.next = e;
                              loTail = e;
                          }
                          //位置在旧容量+原位置的元素
                          else {
                              if (hiTail == null)
                                  hiHead = e;
                              else
                                  hiTail.next = e;
                              hiTail = e;
                          }
                      } while ((e = next) != null);
                      //将相应的位置赋值给相应新数组的桶
                      if (loTail != null) {
                          loTail.next = null;
                          newTab[j] = loHead;
                      }
                      if (hiTail != null) {
                          hiTail.next = null;
                          newTab[j + oldCap] = hiHead;//详见下
                      }
                  }
              }
          }
      }
      return newTab;
  }
  ```

  *(e.hash & oldCap) == 0*原因：

  ​	扩容之前:hash值=(oldCap-1)&hash

  ​	扩容之后:hash值=(2*newCap-1)&hash

  ​	由于cap都是2的幂次，所以它的二进制一般都是10000...000（通用的），假设为1000，oldCap-1=0111；这样直接和hash做&操作之后；接着newCap=10000，newCap-1=01111，做&操作之后，就是否为0就只和hash值的**第一位**是否为1有关系，所以只需要oldCap&hash==0，就可以证明新的桶的位置=旧的；

  *j + oldCap*的原因：

  ​	这部分的值是位置发生了改变，即hash值为1XXX，原位置的桶的值=XXX,新位置的桶位置=1XXX，也就是加上了旧的容量大小，所以这里加上了oldCap

  ![进行&操作原理](https://img-blog.csdnimg.cn/20190817111248656.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2ODU2MDI0,size_16,color_FFFFFF,t_70)

- 存入值

  ```java
  final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                     boolean evict) {
          Node<K,V>[] tab; //后面赋值为table
      	Node<K,V> p; 
      	int n, i;
      	//如果相应的数组为null/数组长度为0，则进行resize扩容
          if ((tab = table) == null || (n = tab.length) == 0)
              n = (tab = resize()).length;
      	//如果所在Hash值的地方为空的，则直接new一个新的节点
          if ((p = tab[i = (n - 1) & hash]) == null)
              tab[i] = newNode(hash, key, value, null);
      	//如果不是空的
          else {
              Node<K,V> e; K k;
              //如果新的哈希值=旧哈希值&&K都相等，则e指向旧的头结点
              if (p.hash == hash &&
                  ((k = p.key) == key || (key != null && key.equals(k))))
                  e = p;
              //否则判断是否是树节点，如果是树结点，则进行红黑树的插入，这里默认只是插入了一个新的节点/直接替换了那个应该存入的节点，然后再进行相应的红黑树调整
              else if (p instanceof TreeNode)
                  e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
              //否则是链表的话，进行链表的插入
              else {
                  for (int binCount = 0; ; ++binCount) {
                      if ((e = p.next) == null) {
                          //这里使用了尾插法进行数据的遍历
                          p.next = newNode(hash, key, value, null);
                          //判断插入是否需要进行树化
                          if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                              treeifyBin(tab, hash);//进行树化
                          break;
                      }
                      //如果途中有元素的hash值和k值一样，则只需要进行替换即可
                      if (e.hash == hash &&
                          ((k = e.key) == key || (key != null && key.equals(k))))
                          break;
                      p = e;
                  }
              }
              //由参数决定是否进行替换
              if (e != null) { 
                  V oldValue = e.value;
                  if (!onlyIfAbsent || oldValue == null)
                      e.value = value;
                  afterNodeAccess(e);
                  return oldValue;
              }
          }
      	//叠加版本号，
          ++modCount;
      	//再进行扩容
          if (++size > threshold)
              resize();
          afterNodeInsertion(evict);
          return null;
      }
  
  
  
  
  //----------------------------------------树化---------------------------------------------------
  final void treeifyBin(Node<K,V>[] tab, int hash) {
          int n, index; Node<K,V> e;
      	//判断数组是否为空，或者n==表的长度<最小进行树化容量
          if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
              resize();//不进行树化，而是进行扩容
      	//如果需要进行树化了，还需要那个位置不为空
          else if ((e = tab[index = (n - 1) & hash]) != null) {
              TreeNode<K,V> hd = null, tl = null;
              //这个循环只是将节点进行转换成双向链表而已，
              do {
                  //p节点是新的树的结点，只是将原来的数组中的那个数据进行树结点的初始化复制数据
                  TreeNode<K,V> p = replacementTreeNode(e, null);
                  if (tl == null)
                      hd = p;
                  else {
                      p.prev = tl;
                      tl.next = p;
                  }
                  tl = p;
              } while ((e = e.next) != null);
              if ((tab[index] = hd) != null)
                  hd.treeify(tab);//真正进行树化
          }
  }
  
  final void treeify(Node<K,V>[] tab) {
              TreeNode<K,V> root = null;
      		//开始遍历链表
              for (TreeNode<K,V> x = this, next; x != null; x = next) {
                  next = (TreeNode<K,V>)x.next;//next指向下一个节点处
                  x.left = x.right = null;//因不是HashMap，的left和right==null
                  //如果新的树的根节点为Null
                  if (root == null) {
                      x.parent = null;//头节点无父节点
                      x.red = false;//根节点为黑色
                      root = x;//让root记录下根节点
                  }
                  //如果不是根节点
                  else {
                      //K的K值
                      K k = x.key;
                      int h = x.hash;//当前节点的哈希值
                      //K的Class类型
                      Class<?> kc = null;
                      //从根节点开始遍历，p是当前遍历到的节点指针
                      for (TreeNode<K,V> p = root;;) {
                          int dir, ph;
                          K pk = p.key;
                          //先进行Hash值的比较，进行dir的赋值
                          if ((ph = p.hash) > h)
                              dir = -1;
                          else if (ph < h)
                              dir = 1;
                          //这里是由于hash值相同，则直接在比较相应的k值
                          else if ((kc == null &&
                                    (kc = comparableClassFor(k)) == null) ||
                                   (dir = compareComparables(kc, k, pk)) == 0)
                              dir = tieBreakOrder(k, pk);//比较了：类的名字=>类的identityHashCode的大小
                          TreeNode<K,V> xp = p;
                          //再接着遍历下一个，直到叶节点
                          if ((p = (dir <= 0) ? p.left : p.right) == null) {
                              x.parent = xp;//找到了父节点
                              if (dir <= 0)
                                  xp.left = x;
                              else
                                  xp.right = x;
                              root = balanceInsertion(root, x);
                              break;
                          }
                      }
                  }
              }
              moveRootToFront(tab, root);//这是所有节点遍历之后，把root节点设为所在数据槽的第一个节点
  }
  ```
  
- 维护红黑树的平衡性的插入

  ```Java
  /**
   * 插入规则总结如下：
   * 父节点是黑色的不用调整
   * 父节点是红色的：
   * 1.设插入的节点必为红色
   * 2.如果叔叔节点为黑色/空，则进行变色+旋转
   * 3.如果叔叔节点为红色，则叔叔节点+父节点变黑，祖父节点变红色
   */
  static <K,V> TreeNode<K,V> balanceInsertion(TreeNode<K,V> root,
                                              TreeNode<K,V> x) {//root节点是相应的根结点，x是想插入的节点
      x.red = true;//给新的节点设置为红色
      //本质上进行递归调用
      //xp:新节点的父节点；xpp：新节点的祖父节点；xppl:祖父节点的左孩子；xppr：祖父节点的右孩子
      for (TreeNode<K,V> xp, xpp, xppl, xppr;;) {
          //如果父节点为空==证明它是树的根节点，
          if ((xp = x.parent) == null) {
              x.red = false;//根据规则，直接设置未黑色，并且直接返回即可因为它就是父节点
              return x;
          }
          //如果父节点是黑色的/新节点的父节点是根节点，则也不需要调整
          else if (!xp.red || (xpp = xp.parent) == null)
              return root;
          //这里应父节点是左节点
          if (xp == (xppl = xpp.left)) {
              //叔叔节点非空，并且是红色的
              if ((xppr = xpp.right) != null && xppr.red) {
                  xppr.red = false;//叔叔节点变黑
                  xp.red = false;//父节点变黑
                  xpp.red = true;//祖父节点变红
                  x = xpp;//进行下一次的递归
              }
              else {//对应叔叔节点为空/黑色
                  //如果是父节点的右节点
                  if (x == xp.right) {
                      //先左旋转
                      root = rotateLeft(root, x = xp);
                      xpp = (xp = x.parent) == null ? null : xp.parent;
                  }
                  //再右旋，并且进行变色
                  if (xp != null) {
                      xp.red = false;
                      if (xpp != null) {
                          xpp.red = true;
                          root = rotateRight(root, xpp);
                      }
                  }
              }
          }
          //以下和上述情况相反类似进行操作即可
          else {
              if (xppl != null && xppl.red) {
                  xppl.red = false;
                  xp.red = false;
                  xpp.red = true;
                  x = xpp;
              }
              else {
                  if (x == xp.left) {
                      root = rotateRight(root, x = xp);
                      xpp = (xp = x.parent) == null ? null : xp.parent;
                  }
                  if (xp != null) {
                      xp.red = false;
                      if (xpp != null) {
                          xpp.red = true;
                          root = rotateLeft(root, xpp);
                      }
                  }
              }
          }
      }
  }
  ```

- 获取值

  ```java
  //根据key获取值
  public V get(Object key) {
          Node<K,V> e;//要返回的值
      	//传入要寻找的key的hash值，和相应的值key
          return (e = getNode(hash(key), key)) == null ? null : e.value;
  }
  
  //
  final Node<K,V> getNode(int hash, Object key) {
      	//first是所在hash值的桶的头结点，n是数组的长度
          Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
          if ((tab = table) != null && (n = tab.length) > 0 &&//如果数组初始化了，并且头节点不为Null
              (first = tab[(n - 1) & hash]) != null) {
              //确定相应的头结点的hash值和key值是相同的，则直接返回该节点
              if (first.hash == hash && // always check first node
                  ((k = first.key) == key || (key != null && key.equals(k))))
                  return first;
              //如果下一个元素不为Null
              if ((e = first.next) != null) {
                  //并且头节点是树结点，则按树结点的规则进行查找
                  if (first instanceof TreeNode)
                      return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                  //否则按链表的规则进行查找即可
                  do {
                      if (e.hash == hash &&
                          ((k = e.key) == key || (key != null && key.equals(k))))
                          return e;
                  } while ((e = e.next) != null);
              }
          }
          return null;
  }
  
  //-----------------------------------------寻找树结点---------------------------------------------
  //先寻找到相应的根节点
  final TreeNode<K,V> getTreeNode(int h, Object k) {
              return ((parent != null) ? root() : this).find(h, k, null);//调用根节点的find函数，这里做了双层的保障
  }
  
  //然后从根节点进行相应的查找
  final TreeNode<K,V> find(int h, Object k, Class<?> kc) {
              TreeNode<K,V> p = this;
              do {
                  int ph, dir; K pk;
                  TreeNode<K,V> pl = p.left, pr = p.right, q;
                  //先比较hash值，进行左右分支的选择
                  if ((ph = p.hash) > h)
                      p = pl;
                  else if (ph < h)
                      p = pr;
                  //如果hash值相同，并且K值也相同则直接返回该节点即可
                  else if ((pk = p.key) == k || (k != null && k.equals(pk)))
                      return p;
                  //如果hash值相同，并且K值不同，挑选单独分支的情况就只能强行选择剩下的一条分支
                  else if (pl == null)
                      p = pr;
                  else if (pr == null)
                      p = pl;
                  //如果2条分支都存在，则进行相应的K值比较，如果dir==0,则跳过这个if
                  else if ((kc != null ||
                            (kc = comparableClassFor(k)) != null) &&
                           (dir = compareComparables(kc, k, pk)) != 0)
                      p = (dir < 0) ? pl : pr;
                  //如果不能使用ComparaTo方法进行判断，则先对右分支递归查找，如果右分支不存在
                  else if ((q = pr.find(h, k, kc)) != null)
                      return q;
                  //则默认选择左边的
                  else
                      p = pl;
              } while (p != null);
              return null;
  }
  ```

- 删除值

  ```java
  public V remove(Object key) {
          Node<K,V> e;
          return (e = removeNode(hash(key), key, null, false, true)) == null ?
              null : e.value;
  }
  
  final Node<K,V> removeNode(int hash, Object key, Object value,
                                 boolean matchValue, boolean movable) {
          Node<K,V>[] tab; Node<K,V> p; int n, index;
      	//如果数组不为空，并且所查询的地方的值不为空，（这样才有值
          if ((tab = table) != null && (n = tab.length) > 0 &&
              (p = tab[index = (n - 1) & hash]) != null) {
              Node<K,V> node = null, e; K k; V v;
              //如果p指向的节点的k和hash值都相等，则直接返回
              if (p.hash == hash &&
                  ((k = p.key) == key || (key != null && key.equals(k))))
                  node = p;
              //进行下一个节点的遍历
              else if ((e = p.next) != null) {
                  //如果是树结点，根据hash和key查找到相应的节点
                  if (p instanceof TreeNode)
                      node = ((TreeNode<K,V>)p).getTreeNode(hash, key);
                  else {
                      //否则进行链表的遍历
                      do {
                          if (e.hash == hash &&
                              ((k = e.key) == key ||
                               (key != null && key.equals(k)))) {
                              node = e;
                              break;
                          }
                          p = e;
                      } while ((e = e.next) != null);
                  }
              }
              //node取得要删除的值
              //node不为空并且或者值相等
              if (node != null && (!matchValue || (v = node.value) == value ||
                                   (value != null && value.equals(v)))) {
                  //进行树的删除
                  if (node instanceof TreeNode)
                      ((TreeNode<K,V>)node).removeTreeNode(this, tab, movable);
                  //链表情况下删除节点
                  else if (node == p)
                      tab[index] = node.next;
                  else
                      p.next = node.next;
                  ++modCount;
                  --size;
                  afterNodeRemoval(node);
                  return node;
              }
          }
          return null;
  }
  
  
  
  //删除树节点
  final void removeTreeNode(HashMap<K,V> map, Node<K,V>[] tab,
                                    boolean movable) {
              int n;
              if (tab == null || (n = tab.length) == 0)
                  return;
      		//求出索引值
              int index = (n - 1) & hash;
              /**
               * first-头节点，数组存放数据索引位置存在存放的节点值
               * root-根节点,红黑树的根节点，正常情况下二者是相等的
               * rl-root节点的左孩子节点,succ-后节点,pred-前节点
               */
              TreeNode<K,V> first = (TreeNode<K,V>)tab[index], root = first, rl;
              TreeNode<K,V> succ = (TreeNode<K,V>)next, pred = prev;
           	/**
               * 维护双向链表（map在红黑树数据存储的过程中，除了维护红黑树之外还对双向链表进行了维护）
               * 从链表中将该节点删除
               * 如果前驱节点为空，说明删除节点是头节点，删除之后，头节点直接指向了删除节点的后继节点
               */
              if (pred == null)
                  tab[index] = first = succ;
              else
                  pred.next = succ;
      		//修改后驱节点的前驱指针
              if (succ != null)
                  succ.prev = pred;
      		//说明在删除了该节点后，该树为空，只需要直接返回即可
              if (first == null)
                  return;
      		//父节点不为空，则重新选举父节点
              if (root.parent != null)
                  root = root.root();
      		//
              if (root == null
                  || (movable
                      && (root.right == null
                          || (rl = root.left) == null
                          || rl.left == null))) {
                  tab[index] = first.untreeify(map);  // too small
                  return;
              }
              TreeNode<K,V> p = this, pl = left, pr = right, replacement;
              if (pl != null && pr != null) {
                  TreeNode<K,V> s = pr, sl;
                  while ((sl = s.left) != null) // find successor
                      s = sl;
                  boolean c = s.red; s.red = p.red; p.red = c; // swap colors
                  TreeNode<K,V> sr = s.right;
                  TreeNode<K,V> pp = p.parent;
                  if (s == pr) { // p was s's direct parent
                      p.parent = s;
                      s.right = p;
                  }
                  else {
                      TreeNode<K,V> sp = s.parent;
                      if ((p.parent = sp) != null) {
                          if (s == sp.left)
                              sp.left = p;
                          else
                              sp.right = p;
                      }
                      if ((s.right = pr) != null)
                          pr.parent = s;
                  }
                  p.left = null;
                  if ((p.right = sr) != null)
                      sr.parent = p;
                  if ((s.left = pl) != null)
                      pl.parent = s;
                  if ((s.parent = pp) == null)
                      root = s;
                  else if (p == pp.left)
                      pp.left = s;
                  else
                      pp.right = s;
                  if (sr != null)
                      replacement = sr;
                  else
                      replacement = p;
              }
              else if (pl != null)
                  replacement = pl;
              else if (pr != null)
                  replacement = pr;
              else
                  replacement = p;
              if (replacement != p) {
                  TreeNode<K,V> pp = replacement.parent = p.parent;
                  if (pp == null)
                      root = replacement;
                  else if (p == pp.left)
                      pp.left = replacement;
                  else
                      pp.right = replacement;
                  p.left = p.right = p.parent = null;
              }
  
              TreeNode<K,V> r = p.red ? root : balanceDeletion(root, replacement);
  
              if (replacement == p) {  // detach
                  TreeNode<K,V> pp = p.parent;
                  p.parent = null;
                  if (pp != null) {
                      if (p == pp.left)
                          pp.left = null;
                      else if (p == pp.right)
                          pp.right = null;
                  }
              }
              if (movable)
                  moveRootToFront(tab, r);
          }
  ```

  

- Hash值的获取

  ```java
      static final int hash(Object key) {
          int h;
          return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);//这里是为了减少hash冲突，让前16位也参加相应的运算，hash值本质上只是Int
      }
  ```

  

- tmp

#### 8. TreeMap

> 其键的自然顺序进行排序，或者根据 创建映射时提供的 Comparator 进行排序，具体取决于使用的构造方法。`SortedMap`保证遍历时以Key的顺序来进行排序。例如，放入的Key是`"apple"`、`"pear"`、`"orange"`，遍历的顺序一定是`"apple"`、`"orange"`、`"pear"`，因为`String`默认按字母排序
>
> 底层使用红黑树进行相应的存储
>
> 使用`TreeMap`时，放入的Key必须实现`Comparable`接口。`String`、`Integer`这些类已经实现了`Comparable`接口，因此可以直接作为Key使用。作为Value的对象则没有任何要求。
>
> 如果作为Key的class没有实现`Comparable`接口，那么，必须在创建`TreeMap`时同时指定一个自定义排序算法：

- 关于`Comparable`与`Comparator` 

  这2个接口只是需要比较2个对象的大小而定义的方法而已，

  -- Comparator位于包java.util下，而Comparable位于包 java.lang下
  -- 使用Comparable接口实现排序，排序的规则（即重写compareTo()方法）是定义在Bean类里面
  -- 使用Comparator接口实现排序，排序的规则（即重写compare()方法）是定义在外部（即不是Bean类里面）
  -- 用 Comparator 是策略模式（strategy design pattern），就是不改变对象自身，而用一个策略对象（strategy object）来改变它的行为。

- SortMap

  ```java
  public interface SortedMap<K,V> extends Map<K,V> {
      //主要是通过该方法进行key的大小比较
      Comparator<? super K> comparator();
  ```

- 存储的数据

  ```java
  private final Comparator<? super K> comparator;//如果key是有自然排序的话，就不需要相应的比较器，否则需要付给相应的比较器
  private transient Entry<K,V> root;//根节点
  private transient int size = 0;//实际数据个数
  private transient int modCount = 0;//版本迭代
  
  //各种键值对集合
  private transient EntrySet entrySet;
  private transient KeySet<K> navigableKeySet;
  private transient NavigableMap<K,V> descendingMap;
  
  //红黑节点
  private static final boolean RED   = false;
  private static final boolean BLACK = true;
  ```

- 节点类型

  ```Java
  static final class Entry<K,V> implements Map.Entry<K,V> {
      K key;
      V value;
      Entry<K,V> left;
      Entry<K,V> right;
      Entry<K,V> parent;
      boolean color = BLACK;
  ```

- tmp

#### 9. LinkedHashMap

> LinkedHashMap=HashMap+双向链表，在底层根据插入的顺序维护了一个双向链表

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200430000245372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwMDUwNTg2,size_16,color_FFFFFF,t_70)

- 节点类型

  ```java
      static class Entry<K,V> extends HashMap.Node<K,V> {
          Entry<K,V> before, after;//维护的双向链表的指向
          Entry(int hash, K key, V value, Node<K,V> next) {
              super(hash, key, value, next);
          }
      }
  ```

- 存储的数据

  ```java
      transient LinkedHashMap.Entry<K,V> head;//头节点
      transient LinkedHashMap.Entry<K,V> tail;//尾节点
      final boolean accessOrder;//一个条件变量，它控制了是否在get操作后需要将新的get的节点重新放到链表的尾部.LinkedHashMap可以维持了插入的顺序，但是这个顺序不是不变的，可以被get操作打乱。
  ```

- 构造函数

  ```java
  //其实就是调用父类HashMap的构造函数  
  public LinkedHashMap(int initialCapacity, float loadFactor) {
          super(initialCapacity, loadFactor);
          accessOrder = false;
  }
  public LinkedHashMap(int initialCapacity) {
          super(initialCapacity);
          accessOrder = false;
  }
  public LinkedHashMap() {
          super();
          accessOrder = false;
  }
  ```

- 维护链表的操作

  ```java
  /**
   * 其实在LinkedHashMap就是在HashMap的基础上，额外调用了函数维护双向链表
   * 
   *
   */
  //这个Node是在Hashmap实现了remove函数
  void afterNodeRemoval(Node<K,V> e) {
          LinkedHashMap.Entry<K,V> p =
              (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
          p.before = p.after = null;
      	//删除其中的节点
          if (b == null)
              head = a;
          else
              b.after = a;
          if (a == null)
              tail = b;
          else
              a.before = b;
  }
  
  //在节点被访问后根据accessOrder判断是否需要调整链表顺序，这里也是为了实现LRU算法的
  void afterNodeAccess(Node<K,V> e) { // move node to last
      //如果accessOrder为false，什么都不做
          LinkedHashMap.Entry<K,V> last;
          if (accessOrder && (last = tail) != e) {
              //p指向待删除元素，b执行前驱，a执行后驱
              LinkedHashMap.Entry<K,V> p =
                  (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
              //这里执行双向链表删除操作
              p.after = null;
              if (b == null)
                  head = a;
              else
                  b.after = a;
              if (a != null)
                  a.before = b;
              else
                  last = b;
              //这里执行将p放到尾部
              if (last == null)
                  head = p;
              else {
                  p.before = last;
                  last.after = p;
              }
              tail = p;
              ++modCount;
          }
  }
  ```

- tmp

#### 10. HashTable

> 和HashMap的最大差别就是线程安全，
>
> 底层节点
>
> HashMap可以使用null作为key，而Hashtable则不允许null作为key

- 存储数据

  ```java
  private transient Entry<?,?>[] table;//存储的数据
  private transient int count;
  private int threshold;
  private float loadFactor;
  private transient int modCount = 0;
  ```

- 构造函数

  ```java
      public Hashtable(int initialCapacity, float loadFactor) {
          if (initialCapacity < 0)
              throw new IllegalArgumentException("Illegal Capacity: "+
                                                 initialCapacity);
          if (loadFactor <= 0 || Float.isNaN(loadFactor))
              throw new IllegalArgumentException("Illegal Load: "+loadFactor);
          if (initialCapacity==0)//如果为0，则容量为1
              initialCapacity = 1;
          this.loadFactor = loadFactor;
          table = new Entry<?,?>[initialCapacity];
          threshold = (int)Math.min(initialCapacity * loadFactor, MAX_ARRAY_SIZE + 1);
      }
  
      public Hashtable(int initialCapacity) {
          this(initialCapacity, 0.75f);
      }
  
      public Hashtable() {
          this(11, 0.75f);//11为初始容量
      }
  ```

  

- 节点类型

  ```java
  private static class Entry<K,V> implements Map.Entry<K,V> {
          final int hash;//节点的hash值
          final K key;//key
          V value;//value
          Entry<K,V> next;//next
  ```

- 存值

  ```java
      public synchronized V put(K key, V value) {
          if (value == null) {
              throw new NullPointerException();
          }
          Entry<?,?> tab[] = table;
          int hash = key.hashCode();
          int index = (hash & 0x7FFFFFFF) % tab.length;//这里并没有做相应的优化，而是直接
          @SuppressWarnings("unchecked")
          Entry<K,V> entry = (Entry<K,V>)tab[index];
          for(; entry != null ; entry = entry.next) {
              if ((entry.hash == hash) && entry.key.equals(key)) {
                  V old = entry.value;
                  entry.value = value;
                  return old;
              }
          }
  
          addEntry(hash, key, value, index);
          return null;
      }
  ```

  

- tmp

#### 11. ConcurrentHashMap

> 主要分为1.7和1.8的区别，
>
> 1.7: Segment + HashEntry + Unsafe
> 1.8: 移除 Segment，使锁的粒度更小，Synchronized + CAS + Node + Unsafe
>
> 详见：https://www.jianshu.com/p/78989cd553b4

- 存储数据

  ```java
  private static final int MAXIMUM_CAPACITY = 1 << 30;//最大容量
  private static final int DEFAULT_CAPACITY = 16;//默认容量
  static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;//最大数据长度
  private static final int DEFAULT_CONCURRENCY_LEVEL = 16;//
  private static final float LOAD_FACTOR = 0.75f;
  static final int TREEIFY_THRESHOLD = 8;
  static final int UNTREEIFY_THRESHOLD = 6;
  static final int MIN_TREEIFY_CAPACITY = 64;
  private static final int MIN_TRANSFER_STRIDE = 16;
  private static int RESIZE_STAMP_BITS = 16;
  private static final int MAX_RESIZERS = (1 << (32 - RESIZE_STAMP_BITS)) - 1;
  private static final int RESIZE_STAMP_SHIFT = 32 - RESIZE_STAMP_BITS;
  
  transient volatile Node<K,V>[] table;
  private transient volatile Node<K,V>[] nextTable;
  private transient volatile long baseCount;
  /**
   * SizeCtl=0:代表数组为初始化，且数组初始容量=16
   * sizeCtl为正数，如果数组未初始化，那么记录的是数据的初始容量；如果已经初始化了，则记录的是数组的扩容阈值
   * sizeCtl=-1 表示数组正在进行初始化；
   * sizeCtl<0，并不等于-1,表示数组正在扩容，-（1+n），表示此时有n个线程正在共同完成数组的扩容；
   */
  private transient volatile int sizeCtl;
  
  private transient volatile int transferIndex;
  private transient volatile int cellsBusy;
  private transient volatile CounterCell[] counterCells;
  ```

- 节点类型

  ```java
  //链表节点
  static class Node<K,V> implements Map.Entry<K,V> {
          final int hash;
          final K key;
          volatile V val;
      volatile Node<K,V> next;
  //红黑树节点
  static final class TreeNode<K,V> extends Node<K,V> {
          TreeNode<K,V> parent;  // red-black tree links
          TreeNode<K,V> left;
          TreeNode<K,V> right;
          TreeNode<K,V> prev;    // needed to unlink next upon deletion
  boolean red;
     
  
  
  
  ```

- 存入数据

  ````java
  public V put(K key, V value) {
          return putVal(key, value, false);
      }
  
  final V putVal(K key, V value, boolean onlyIfAbsent) {
          if (key == null || value == null) throw new NullPointerException();
      	//扰动函数，更哈希，一定是个正数
          int hash = spread(key.hashCode());
      	//
          int binCount = 0;
          for (Node<K,V>[] tab = table;;) {
              Node<K,V> f; int n, i, fh;
              if (tab == null || (n = tab.length) == 0)
                  tab = initTable();
              else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
                  if (casTabAt(tab, i, null,
                               new Node<K,V>(hash, key, value, null)))
                      break;                   // no lock when adding to empty bin
              }
              else if ((fh = f.hash) == MOVED)
                  tab = helpTransfer(tab, f);
              else {
                  V oldVal = null;
                  synchronized (f) {
                      if (tabAt(tab, i) == f) {
                          if (fh >= 0) {
                              binCount = 1;
                              for (Node<K,V> e = f;; ++binCount) {
                                  K ek;
                                  if (e.hash == hash &&
                                      ((ek = e.key) == key ||
                                       (ek != null && key.equals(ek)))) {
                                      oldVal = e.val;
                                      if (!onlyIfAbsent)
                                          e.val = value;
                                      break;
                                  }
                                  Node<K,V> pred = e;
                                  if ((e = e.next) == null) {
                                      pred.next = new Node<K,V>(hash, key,
                                                                value, null);
                                      break;
                                  }
                              }
                          }
                          else if (f instanceof TreeBin) {
                              Node<K,V> p;
                              binCount = 2;
                              if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                             value)) != null) {
                                  oldVal = p.val;
                                  if (!onlyIfAbsent)
                                      p.val = value;
                              }
                          }
                      }
                  }
                  if (binCount != 0) {
                      if (binCount >= TREEIFY_THRESHOLD)
                          treeifyBin(tab, i);
                      if (oldVal != null)
                          return oldVal;
                      break;
                  }
              }
          }
          addCount(1L, binCount);
          return null;
      }
  ````

  

- tmp

#### 5.  工具类

- 关于复制

  ```java
  //System类的，进行的是浅拷贝
  public static native void arraycopy(Object src,  int  srcPos,
                                      Object dest, int destPos,
                                      int length);
  ```

  ```java
  //底层还是使用arraycopy进行数据的复制使用
  public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
      @SuppressWarnings("unchecked")
      T[] copy = ((Object)newType == (Object)Object[].class)
          ? (T[]) new Object[newLength]
          : (T[]) Array.newInstance(newType.getComponentType(), newLength);
      System.arraycopy(original, 0, copy, 0,
                       Math.min(original.length, newLength));
      return copy;
  }
  ```

- tmp



### 3.5 深拷贝和浅拷贝

> Java 中的数据类型分为基本数据类型和引用数据类型。对于这两种数据类型，在进行赋值操作、用作方法参数或返回值时，会有值传递和引用（地址）传递的差别。

根据对对象属性的拷贝程度（基本数据类和引用类型），会分为两种：

- 浅拷贝 (`Shallow Copy`)

  只复制对象空间而不复制资源。

  1. 对于基本数据类型的成员对象，因为基础数据类型是值传递的，所以是直接将属性值赋值给新的对象。基础类型的拷贝，其中一个对象修改该值，不会影响另外一个。
  2. 对于引用类型，比如数组或者类对象，因为引用类型是引用传递，所以浅拷贝只是把内存地址赋值给了成员变量，它们指向了同一内存空间。改变其中一个，会对另外一个也产生影响。

  实现方法：

  实现对象拷贝的类，需要实现 `Cloneable` 接口，并覆写 `clone()` 方法，因为`Object`的`Clone`方法默认是浅拷贝。

  ```Java
  public interface Cloneable {
  }
  ```

  

- 深拷贝 (`Deep Copy`)

  即主要针对相应的引用对象采用相应的

  1.  实现Cloneable接口

     对于类中的引用对象也要实现该接口

  2. 实现Serializable接口

     通过字节流序列化实现深拷贝，需要深拷贝的对象必须实现Serializable接口

     

     ```Java
     import java.io.*;
     
     public class Demo2 implements Serializable {
     
         private String name;
     
         private String value;
     
         private DemoInternal2 demoInternal2;
     
         /*省略getter和setter方法*/
     
         // 深度复制
         public Demo2 myclone() {
             Demo2 demo2 = null;
             try {
             	//输出字节流
                 ByteArrayOutputStream baos = new ByteArrayOutputStream();
                 //flag_1
                 ObjectOutputStream oos = new ObjectOutputStream(baos);
                 //将对对象写入输出流
                 oos.writeObject(this);
     
                 //没有用到的输出流baos转换成二进制，写入输入流
                 ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());
                 //flag_1.1的输入流
                 ObjectInputStream ois = new ObjectInputStream(bais);
                 //返回值=flag_1.1的输入流读取出来
                 demo2 = (Demo2) ois.readObject();
             } catch (IOException | ClassNotFoundException e) {
                 e.printStackTrace();
             }
             return demo2;
         }
     }
     
     import java.io.Serializable;
     public class DemoInternal2 implements Serializable {
     private String internalName;
     
     private String internalValue;
     
     /*省略getter和setter方法*/
     }
     ```

### 3.6 输入输出流

- **分类** 

按流向分:

​	输入流: 程序可以从中读取数据的流
​	输出流: 程序能向其中写入数据的流

按数据传输单位分:

​	字节流: 以字节为单位传输数据的流
​	字符流: 以字符为单位传输数据的流

按功能分:

​	节点流: 用于直接操作目标设备的流
​	过滤流: 是对一个已存在的流的链接和封装，通过对数据进行处理为程序提供功能强大、灵活的读写功能

JDK所提供的所有流类位于java.io包中，都分别继承自以下四种抽象流类：

​	InputStream：继承自InputStream的流都是用于向程序中输入数据的，且数据单位都是字节（8位）。
​	OutputSteam：继承自OutputStream的流都是程序用于向外输出数据的，且数据单位都是字节（8位）。
​	Reader：继承自Reader的流都是用于向程序中输入数据的，且数据单位都是字符（16位）。
​	Writer：继承自Writer的流都是程序用于向外输出数据的，且数据单位都是字符（16位）。

![img](https://img-blog.csdn.net/20180127210410630)

- BIO(同步阻塞)

  > 进程等待被调用者将数据准备好，并且主动读写需要的数据

  `NIO`重点是把`Channel`（通道），`Buffer`（缓冲区），`Selector`（选择器）三个类之间的关系弄清楚。

  **1.缓冲区Buffer**

  `Buffer`是一个对象。它包含一些要写入或者读出的数据。在面向流的I/O中，可以将数据写入或者将数据直接读到`Stream`对象中。在`NIO`中，所有的数据都是用缓冲区处理。这也就本文上面谈到的IO是面向流的，NIO是面向缓冲区的。

  缓冲区实质是一个数组，通常它是一个字节数组（`ByteBuffer`），也可以使用其他类的数组。但是一个缓冲区不仅仅是一个数组，缓冲区提供了对数据的结构化访问以及维护读写位置（`limit`）等信息。

  最常用的缓冲区是`ByteBuffer`，一个`ByteBuffer`提供了一组功能于操作byte数组。除了`ByteBuffer`，还有其他的一些缓冲区，事实上，每一种Java基本类型（除了Boolean）都对应一种缓冲区，具体如下：

  - `ByteBuffer`：字节缓冲区

    `ByteBuffer`分为direct直接读写buffer和non-direct非直接读写的buffer.这里边的`HeapByteBuffer`是在`jvm`堆上面的一个buffer，底层的本质是一个数组，由于内容维护在`jvm`里，所以把内容写进buffer里速度会快些；并且，可以更容易回收，外设读取`jvm`堆里的数据时，不是直接读取的，而是把`jvm`里的数据读到一个内存块里，再在这个块里读取的；

    ```java 
    // Used only by direct buffers
    // NOTE: hoisted here for speed in JNI GetDirectBufferAddress
    long address;
    //这是只有在DirectByteBuffer才会用到的一个引用address指向了数据的堆外内存，因为它是存放在操作系统的内存中，由于DirectByteBuffer分配与native memory中，不在heap区，所以不会受到heap区的gc影响，但分配和释放需要更多的成本；
    ```

    ```Java
    DirectByteBuffer(int cap) {                   //这是direct的构造方法，
    
        super(-1, 0, cap, cap);
        boolean pa = VM.isDirectMemoryPageAligned();
        int ps = Bits.pageSize();
        long size = Math.max(1L, (long)cap + (pa ? ps : 0));
        Bits.reserveMemory(size, cap);
    
        long base = 0;
        try {
            base = unsafe.allocateMemory(size);//可以看到这里是直接调用native方法，在操作系统中分配内存
        } catch (OutOfMemoryError x) {
            Bits.unreserveMemory(size, cap);
            throw x;
        }
        unsafe.setMemory(base, size, (byte) 0);
        if (pa && (base % ps != 0)) {
            address = base + ps - (base & (ps - 1));
        } else {
            address = base;
        }
        cleaner = Cleaner.create(this, new Deallocator(base, size, cap));//这个是用于管理相应的数据的生存
        att = null;
    }
    ```

    而`ByteBuffer`也可以选择支持通过JNI从本机代码创建直接字节缓冲区，`DirectByteBuffer`的底层的数据其实是维护在操作系统的内存中，而不是`jvm`里，`DirectByteBuffer`里可以通过将文件的区域直接映射到内存中来创建，使用`directByteBuffer`跟外设（IO设备）打交道时会快很多，可以省去读取内存块这一步，实现零拷贝。
    

    ![image-20210307192940858](D:\App\Typora\resources\md-image\计算机网络\image-20210307192940858.png)

    ```java 
    private int mark = -1;//为某一读过的位置做标记，便于某些时候回退到该位置。可以用于mark()/reset()
    private int position = 0;//position类似于读写指针，表示当前读(写)到什么位置
    private int limit;//当写数据到buffer中时，limit一般和capacity相等，在读模式下表示最多能读多少数据，此时和缓存中的实际数据大小相同。
    private int capacity;//初始化时候的容量。Capacity在读写模式下都是固定的，就是我们分配的缓冲大小。
    final int offset;//当前数据的偏移
    final byte[] hb; //hb即内部用于缓存的数组。
    
    ```

  - `CharBuffer`:字符缓冲区

  - `ShortBuffer`：短整型缓冲区

  - `IntBuffer`：整型缓冲区

  - `LongBuffer`:长整型缓冲区

  - `FloatBuffer`：浮点型缓冲区

  - `DoubleBuffer`：双精度浮点型缓冲区

  

  **2.通道Channel**

  Channel是一个通道，可以通过它读取和写入数据，他就像自来水管一样，网络数据通过`Channel`读取和写入。

  通道和流不同之处在于通道是双向的，流只是在一个方向移动，而且通道可以用于读，写或者同时用于读写。

  因为`Channel`是全双工的，所以它比流更好地映射底层操作系统的`API`，特别是在`UNIX`网络编程中，底层操作系统的通道都是全双工的，同时支持读和写。

  `Channel`有四种实现：

  - `FileChannel`:是从文件中读取数据
  - `DatagramChannel`:从UDP网络中读取或者写入数据
  - `SocketChannel`:从TCP网络中读取或者写入数据
  - `ServerSocketChannel`:允许你监听来自TCP的连接，就像服务器一样。每一个连接都会有一个`SocketChannel`产生

  详细介绍：https://blog.csdn.net/zcw4237256/article/details/78662762

**3.多路复用器Selector ** 

`Selector`选择器可以监听多个`Channel`通道感兴趣的事情(`read、write、accept`(服务端接收)、`connect`，实现一个线程管理多个`Channel`，节省线程切换上下文的资源消耗。`Selector`只能管理非阻塞的通道，`FileChannel`是阻塞的，无法管理。

-- Selector的创建

通过调用`Selector.open()`方法创建一个Selector对象，如下：

```java
Selector selector = Selector.open();
```

这里需要说明一下

-- 注册Channel到Selector

```java
channel.configureBlocking(false);
SelectionKey key = channel.register(selector, Selectionkey.OP_READ);
```

**Channel必须是非阻塞的** 
所以`FileChannel`不适用Selector，因为`FileChannel`不能切换为非阻塞模式，更准确的来说是因为FileChannel没有继承`SelectableChannel`。Socket channel可以正常使用。

Selector的实现类维护了三个集合用于存放k-v值，关于相应的通道和关键字所对应的关系

 	`keys`:保存了所有已注册且没有cancel的选择键,Set类型.可以通过`selector.keys()`获取
	 `selectedKeys`:已选择键集,即前一次操作期间,已经准备就绪的通道所对应的选择键.此集合为keys的子集.通过`selector.selectedKeys`()获取.
	 `canceledKeys`:已取消键.已经被取消但尚未取消注册(deregister)的选择键.此集合不可被访问.为keys()的子集.

​	对于新的Selector实例,上述三个集合均为空.通过channel.register将导致keys()集合被添加；

​	如果某个`selectionKey.cancel()`被调用,那么此key 将会被添加到`canceldKey`集合中,在下一次selector选择期间,如果`canceldKeys`不为空,将会导致触发此key的deregister操作(释放资源,并从keys中移除);

​	无论通过`channel.close()`还是通过`selectionKey.cancel()`,都会导致key被加入到cannceldKey中。

​	每次选择操作期间(select),都可以将key添加到`selectedKeys`中或者将从`canceledKey`中移除.负责"选择"key的操作为`select(),selectNow(),select(long timeout);`"选择操作"执行的步骤:

​	首先对cancelledKey进行清除,遍历"已取消键集合",并对每个key进行deregister操作,然后从"已取消键"集合中删除.从	keys集合中删除,从"已选择键"中删除.
​	将由更底层的操作来查询操作系统中每个channel的 准备就绪信息.如果该通道的key尚未在selectedKeys中存在,则将其	添加到该集合中.如果该通道的key已经存在selectedKeys中,则修改器opts事件集."选择"通道的就绪信息,在底层是由"可	扩展尺寸"的线程池执行,但是在并发较高的IO操作中,这仍然会存在"select"延迟问题.
​	"选择"操作结束后,再次执行1),已防止在"选择"期间,有些keys被取消.

- NIO(同步非阻塞)

  > 进程并不直接等待数据准备好，而是不断轮询询问被调用者，并且直到数据被准备好为止，并由

- AIO(异步非阻塞)

  > 进程在发完数据后并不去等待，而是继续做其他事，直到被调用者将准备好的数据自动返还给进程

### 3.7 反射

> JAVA反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

- 反射能获取到的类的一些属性
  1. Class类：代表一个类
  2. Field类：代表类的成员变量（类的属性）
  3. Method类：代表类的方法
  4. Constructor类：代表类的构造方法
  5. Array类：提供了动态创建数组，以及访问数组的元素的静态方法

- 获取Class对象的三种方式:

  ```java
  public class test {
      public static void main(String[] args) {
          //第一种方式获取Class对象
          Student stu1 = new Student();//这一new 产生一个Student对象，一个Class对象。
          Class stuClass = stu1.getClass();//获取Class对象
          System.out.println(stuClass.getName());
          //第二种方式获取Class对象
          Class stuClass2 = Student.class;
          System.out.println(stuClass == stuClass2);
          //第三种方式获取Class对象
          try {
              Class stuClass3 = Class.forName("com.zxc.Student");
          //注意此字符串必须是真实路径，就是带包名的类路径，包名.类名
              System.out.println(stuClass3 == stuClass2);
          //判断三种方式是否获取的是同一个Class对象
          } catch (ClassNotFoundException e) {
              e.printStackTrace();
          }
      }
  }
  
  class Student {
      String name;
  
      public String getName() {
          return name;
      }
  }
  ```

- 静态代理和动态代理

  ```Java
  //----------------------------静态代理--------------------------------
  //接口
  public interface Operate {
      void doSomething();
  }
  
  //操作者
  class Operator implements Operate {
      @Override
      public void doSomething() {
          System.out.println("I'm doing something");
      }
  }
  
  //代理者
  class OperationProxy implements Operate {
      private Operator operator = null;
  
      @Override
      public void doSomething() {
          beforeDoSomething();
          if(operator == null){
              operator =  new Operator();
          }
          operator.doSomething();
          afterDoSomething();
      }
  
      private void beforeDoSomething() {
          System.out.println("before doing something");
      }
  
      private void afterDoSomething() {
          System.out.println("after doing something");
      }
  }
  
  //动态代理
  ```

- tmp