
### Double check
```java
public class Singleton {

  //leave constructor empty
  private Singleton() {}

  //volatile
  //disable opcode reorder
  //let all thread see this var
  private static volatile Singleton instance;

  public static Singleton getInstance() {
        if (instance == null) {
          synchronized(Singleton.class) {
            if (instance == null) {
              instance = new Singleton();
            }
          }
        }
      }
      return instance;
  }
}
```
