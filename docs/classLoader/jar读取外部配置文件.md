## jar包读取外部classpath的文件

- ### 情景

  自定义的一个jar，web工程引用这个jar，web的classpath下有文件demo.properties

- 实现

  ```java
  /**
   * 位于jar包内
   */
  class JarInner {
      //方法1
      public void getOuterConfFile() {
          InputStream inputStream = Thread
              .currentThread()
              .getContextClassLoader()
              .getResourceAsStream("demo.properties");
      }
  	//方法2
      public void getOutCOnfFile2() {
          JarInner.class.getClassLoader().getResourceAsStream("demo.properties");
      }
  }
  ```


