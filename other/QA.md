1. 有关包装类的拆箱问题。代码如下，当e、f的值为[-128, 128)时，均为true，否则为false。这是为什么？
   ``` java
    Integer e = 127;
    Integer f = 127;
    System.out.println(e == f);
   ```
