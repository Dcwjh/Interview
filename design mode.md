# 前言
总结常见的设计模式



# 1. 单例模式

核心代码：构造函数要私有
何时使用：控制实例数目，节省系统资源

Windows 是多进程多线程的，在操作一个文件的时候

- 优点：
1、在内存里只有一个实例，减少了内存的开销，尤其是频繁的创建和销毁实例（比如管理学院首页页面缓存）。
2、避免对资源的多重占用（比如写文件操作）。
- 缺点：
没有接口，不能继承，与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心外面怎么样来实例化。

### 懒汉式: 用到才创建


```java
public class Singleton {  
    private static Singleton instance;  
    private Singleton (){}  
  
    public static Singleton getInstance() {  
    if (instance == null) {  
        instance = new Singleton();  
    }  
    return instance;  
    }  
}
```

线程不安全（多线程会创建多个实例）， ，若线程安全则：`public static synchronized Singleton getInstance()` 但是粒度太大


```java
public class Singleton {  
    private volatile static Singleton singleton;  //一定要加volatile 防止指令重排。 static JVM保证只执行此一  
    private Singleton (){}  
    public static Singleton getSingleton() {  
    if (singleton == null) {  
        synchronized (Singleton.class) {  
        if (singleton == null) {  
            singleton = new Singleton();  
        }  
        }  
    }  
    return singleton;  
    }  
}
```


### 饿汉式：类加载的时候就创建实例  

```java
public class Singleton {  
    private static Singleton instance = new Singleton();  
    private Singleton (){}  
    public static Singleton getInstance() {  
    return instance;  
    }  
}
```

描述：这种方式比较常用，但容易产生垃圾对象。
优点：没有加锁，执行效率会提高。
缺点：类加载时就初始化，浪费内存。
```java
public class Singleton {  
    private static final Singleton instance;  
    private Singleton (){}  
  
    public static Singleton getInstance() {  
    if (instance == null) {  
        instance = new Singleton();  
    }  
    return instance;  
    }  
}
```





# 2. 工厂模式
描述：创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

主要解决：接口选择的问题。

何时使用：明确地计划不同条件下创建不同实例时

关键代码：创建过程在其子类执行。

- 优点：
 1、一个调用者想创建一个对象，只要知道其名称就可以了。
  2、扩展性高，如果想增加一个产品，只要扩展一个工厂类就可以。
   3、屏蔽产品的具体实现，调用者只关心产品的接口。

- 缺点：每次增加一个产品时，都需要增加一个具体类和对象实现工厂，使得系统中类的个数成倍增加，在一定程度上**增加了系统的复杂度**，同时也增加了系统具体类的依赖。这并不是什么好事。

- 应用场景 1、日志记录器：记录可能记录到本地硬盘、系统事件、远程服务器等，用户可以选择记录日志到什么地方。 2、数据库访问，当用户不知道最后系统采用哪一类数据库，以及数据库可能有变化时。 3、设计一个连接服务器的框架，需要三个协议，"POP3"、"IMAP"、"HTTP"，可以把这三个作为产品类，共同实现一个接口。

在任何需要生成复杂对象的地方，都可以使用工厂方法模式。
### 实现
1. shap.java
    ```java
    public interface Shape {
        void draw();
    }
    ```
2. 创建实现接口的实体类。

- Rectangle.java
    ```java    
    public class Rectangle implements Shape {
    
        @Override
        public void draw() {
        System.out.println("Inside Rectangle::draw() method.");
        }
    }
    ```
- Square.java
    ``` java 
    public class Square implements Shape {
    
        @Override
        public void draw() {
            System.out.println("Inside Square::draw() method.");
        }
    }
    ```
- Circle.java
    ```java
    public class Circle implements Shape {
    
        @Override
        public void draw() {
            System.out.println("Inside Circle::draw() method.");
        }
    }
    ```
3. 创建一个工厂，生成基于给定信息的实体类的对象。

- ShapeFactory.java
    ```java
    public class ShapeFactory {
        
        //使用 getShape 方法获取形状类型的对象
        public Shape getShape(String shapeType){
            if(shapeType == null){
                return null;
            }        
            if(shapeType.equalsIgnoreCase("CIRCLE")){
                return new Circle();
            } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
                return new Rectangle();
            } else if(shapeType.equalsIgnoreCase("SQUARE")){
                return new Square();
            }
            return null;
        }
    }
    ```

    调用
    ```java
    public class FactoryPatternDemo {
 
        public static void main(String[] args) {
            ShapeFactory shapeFactory = new ShapeFactory();
        
            //获取 Circle 的对象，并调用它的 draw 方法
            Shape shape1 = shapeFactory.getShape("CIRCLE");
        
            //调用 Circle 的 draw 方法
            shape1.draw();
        
            //获取 Rectangle 的对象，并调用它的 draw 方法
            Shape shape2 = shapeFactory.getShape("RECTANGLE");
        
            //调用 Rectangle 的 draw 方法
            shape2.draw();
        
            //获取 Square 的对象，并调用它的 draw 方法
            Shape shape3 = shapeFactory.getShape("SQUARE");
        
            //调用 Square 的 draw 方法
            shape3.draw();
        }
    }
    ```

# 3. 建造者模式

# 4. 适配器模式

# 5. 组合模式

# 6. 代理模式

# 7. 策略模式

# 8. 模板模式

# 9. 享元模式（可选）

# 10. 状态模式（可选）


# 设计模式在Spring中的应用

- 工厂模式：spring中的BeanFactory就是简单工厂模式的体现，根据传入唯一的标识来获得bean对象；

- 单例模式：提供了全局的访问点BeanFactory；

- 代理模式：AOP功能的原理就使用代理模式（1、JDK动态代理。2、CGLib字节码生成技术代理。）

- 装饰器模式：依赖注入就需要使用BeanWrapper；

- 观察者模式：spring中Observer模式常用的地方是listener的实现。如ApplicationListener。

- 策略模式：Bean的实例化的时候决定采用何种方式初始化bean实例（反射或者CGLIB动态字节码生成）

