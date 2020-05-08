# design-pattern
学习设计模式的笔记
# 大话设计模式

## 一、简单工厂模式

1.封装 按照功能模块进行封装 运算+显示结果

2.继承 不用说了吧

3.多态：父类的方法被若干子类继承，有了不同的实现。Operation有OperationAdd，OperationSqrt等实现

***工厂模式（Factory Pattern）是 Java 中最常用的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。***

书中的例子就是提供运算符，根据运算符去调用不同的Operation接口！



#### 了解下<u>UML表示法</u>

![](C:\Users\Nakarami\Desktop\大话设计模式\image\Uml.PNG)

之前没听过的几种关系：

1.关联：添加一个属性

```java
public Penguin{
    private Climate climate;
}
```

![](C:\Users\Nakarami\Desktop\大话设计模式\image\4.PNG)

2.聚合：弱的拥有关系 A包含B

```java
class WildGooseAggregate{
    public WildGoose[] wildGooses;
}
```

![](C:\Users\Nakarami\Desktop\大话设计模式\image\3.PNG)

3.合成:强拥有关系

```java
class bird{
	private Wing wing;
	public bird(){
	this.wing = new Wing;
    }
}
```

![](C:\Users\Nakarami\Desktop\大话设计模式\image\1.PNG)

4.依赖关系

```java
abstract class animal{
    public metabolism(Oxygen oxygen, Water water){
        
    }
}
```

![](C:\Users\Nakarami\Desktop\大话设计模式\image\2.PNG)

#### 简单工厂模式的例子

书中例子是一个收费程序 场景有正常消费 打折 满减三种

思路：抽取出公共部分--收费 抽象类或接口-->实现三个子类对应上述三种场景

![](C:\Users\Nakarami\Desktop\大话设计模式\image\factory.PNG)

```java
abstract class CashSuper{
	public abstract double acceptCash(double money);
}

//正常收费
class CashNormal extends CashSuper{
    public override double acceptCash(double money){
        return money;
    }
}

//打折收费
class CashRebate extends CashSuper{
    private double moneyRebate;
    //构造
    public CashRebate(String moneyRebate){
        this.moneyRebate = double.Parse(moneyRebate);
    }
    public override double acceptCash(double money){
        return money*moneyRebate;
    }
}

//满减收费
class CashReturn extends CashSuper{
    private double moneyCondition;
    private double moneyReturn;
    //构造
    public CashReturn(String moneyCondition, String moneyReturn){
        this.moneyConditon = double.Parse(moneyCondition);
        this.moneyReturn = double.Parse(moneyReturn);
    }
    public override double acceptCash(double money){
        double result = money;
        if(money > moneyCondition){
            result = money - Math.floor(money/moneyCondition)*moneyReturn;
        }
        return result;
    }
}
```

还需要一个现金收费工厂类: 

```java
class CashFactory{
    public static CashSuper createCashAccept(String type){
        CashSuper cs = null;
        switch(type){
                case "正常收费"：
                    cs = new CashNormal();
                	break;
                case "满300返100"：
                   CashReturn cr1 = new CashReturn("300","100");
                	cs = cr1;
                	break;
                case "打8折"：
                    CashRebate cr2 = new CashRebate("0.8");
                	cs = cr2;
                	break;
        }
        return cs;
    }
}
```

简单工厂模式就是添加处理场景 但不灵活  ”这个模式只是解决对象的创建问题“ 不容易维护 因为一有变更需求 就需要改动这个工厂，对于算法时常变动的场景不是很合适。



## 二、策略模式

在上面的简单工厂模式的基础上进行修改--->策略模式+工厂模式

策略模式可以理解为加了一层CashContext对象，解耦的思想：客户端只需要知道CashContext就好了。客户端仅需传入金额和收费策略给context，context进行实例化策略并计算结果给客户端。![](C:\Users\Nakarami\Desktop\大话设计模式\image\strategy.PNG)

```java
class CashContext{
	private CashSuper cs;
    
    public CashContext(String type){
        case:"正常收费"：
            cs = new CashNormal();
        	break;
        case:"满300减100"：
            cs = new CashReturn("300","100");
        	break;
        case:"打8折"：
            cs = new CashReturn("8");
        	break;
    }
    public double getResult(double money){
        return cs.accpectCash(money);
    }
}

//简单工厂模式的用法
CashSuper csuper = CashFactory.createCashAccept(type);
...=csuper.GetResult(...)
//策略+工厂
CashSuper csuper = new CashContext(type);
...=csuper.GetResult(...)
```

“策略模式封装了变化”

"策略模式减少了各种算法和使用算法类之间的耦合"

"策略模式的Strategy类为Context提供了一系列可重用的算法和行为"

## 三、开发原则

#### 单一职责原则

就一个类而言，应该仅有一个引起它变化的原因。

软件设计要尽量做到职责相互分离！

#### 开放-封闭原则

就是说软件实体（类、模块、函数等等）应该可以扩展 但是不可修改。

绝对的对修改关闭是不可能的，设计人员必须对他设计的模块应该对哪种变换封闭做出封闭做出选择，先猜测出最有可能发生变换的种类，然后构造抽象来隔离变化。

面对需求，对程序的改动是通过增加新代码进行的，而不是更改现有的代码。

#### 里氏代换原则

子类型必须能够替换掉她们的父类型

#### 依赖倒转原则

针对抽象编程而不是依赖细节  抽象不应该依赖细节 细节应该依赖于抽象

针对接口编程 不要对实现编程

接口保持稳定 这样去更改实现代码就不会导致全盘大改

由于子类型的可替换性才使得使用父亲类型的模块在无需修改的情况下就可以扩展

## 四、装饰模式

动态地给一个对象添加一些额外的职责，就增加功能来说，装饰模式比生成子类更为灵活。![](C:\Users\Nakarami\Desktop\大话设计模式\image\装饰模式.PNG)

这个有点像组件注入。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\5.PNG)

```java
class Person{
    public Person(){}
    private String name;
    
    public Person(String name){
        this.name = name;
    }
    
    public virtual void Show(){
        System.out.println("装饰的"+name);
    }
}


class Finery extends Person{
    protected Person component;
    
    public void Decorate(Person component){
        this.component = component;
    }
    
    public override void Show(){
        if(component != null){
            component.Show();
        }
    }
}

class Tshirts extends Finery}{
    
    public override void Show(){
        super.show();
        System.out.println("大T恤");
    }
}


```

装饰模式：当徐通需要新功能的时候，向旧的类中添加新的代码。这些新代码装饰了原有类的核心职责或主要行为。新加入的东西仅仅是为了满足一些只在某种特定情况下才会执行的特殊行为的需要。

**优点**：有效地把类的核心职责和装饰功能区分开了。而且可以去除相关类中重复的装饰逻辑。

## 五、代理模式

![](C:\Users\Nakarami\Desktop\大话设计模式\image\proxy.PNG)

在Spring中也有用到代理模式 AOP

```java
abstract class Subject{
    public abstract void Request();
}

//真实实体
class RealSubject extends Subject{
    @override
    public void Request(){
        System.out.println("真实的请求");
    }
}

//Proxy类
class Proxy extends Subject{
    private RealSubject realSubject;
    @override
    public void Request(){
        if (realSubject == null){
            realSubject = new RealSubject();
        }
        realSubject.Request();
    }
}
```

## 六、工厂方法模式

同一功能用简单工厂和工厂方法实现：

1. 简单工厂模式

![](C:\Users\Nakarami\Desktop\大话设计模式\image\6.PNG)

2.工厂方法模式

![](C:\Users\Nakarami\Desktop\大话设计模式\image\工厂模式.PNG)

```java
//先构建一个工厂接口
interface IFactory{
    Operation CreateOperation();
}

//然后加减乘除各建一个具体工厂去实现这个接口
class AddFactory extends IFactory{
    public Operation CreateOperation(){
        return new OperationAdd();
    }
}
...
    
//客户端实现
IFactory operFactory = new AddFactory();
Operation oper = operFactory.CreataOperation();
oper.NumberA = 1;
oper,NumberB = 2;
double result = oper.GetResult;
```

工厂方法模式实现时，客户端需要决定实例化哪一个工厂来实现运算类，选择判断问题依然存在，也就是说，工厂方法把简单工厂的内部逻辑判断转移到了客户端代码来进行。

书中的例子：

```java
//factory method
IFactory factory = new UndergraduateFactory();
LeiFeng student = factory.CreateLeiFeng();

//simple factory
LeiFeng student = SimpleFactory.CreateLeiFeng("大学生");
LeiFeng community = SimpleFactory.CreateLeiFeng("社区志愿者");
```

如果需要新增一种志愿者，普通工厂方法需要更改SimpleFactory的代码，违背开放-封闭原则。工厂方法模式则克服了这个缺点，但增加了额外的开发量，需要写一个新的工厂类。

## 七、原型模式

![](C:\Users\Nakarami\Desktop\大话设计模式\image\7.PNG)



```java
public class Resume implements Cloneable {
    private String name;
    private String gender;
    private int age;
    //存在引用...
    private List<WorkExperience> experienceList;
    
    //增加新的工作经验
        public void addWorkExperience(String var1, String var2) {
        if (this.experienceList == null) {
            this.experienceList = new ArrayList();
        }

        WorkExperience var3 = new WorkExperience(var1, var2);
        this.experienceList.add(var3);
    }
    //克隆:对引用的属性进行特殊操作
        public Object clone() {
        Resume var1 = null;

        try {
            var1 = (Resume)super.clone();
            if (this.experienceList != null) {
                ArrayList var2 = new ArrayList();
                Iterator var3 = this.experienceList.iterator();

                while(var3.hasNext()) {
                    WorkExperience var4 = (WorkExperience)var3.next();
                    //引用对象的克隆 复制出一个新的对象
                    WorkExperience var5 = (WorkExperience)var4.clone();
                    var2.add(var5);
                }

                var1.setExperienceList((ArrayList)var2.clone());
            }
        } catch (CloneNotSupportedException var6) {
            var6.printStackTrace();
        }

        return var1;
    }
}
```

```java
public class WorkExperience implements Cloneable {
    private String dateDuring;
    private String company;
    //不存在引用时...
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

```java
//控制台
Resume var1 = new Resume();
var1.setInfo("Tony", 28, "male");
var1.addWorkExperience("2010-2011", "Company AAA");

Resume var2 = (Resume)var1.clone();
var2.setWorkExperience("2010-2012", "Company AAA");

Resume var3 = (Resume)var2.clone();
var3.addWorkExperience("2012-2015", "Company BBB");
```

需要特别注意的是**浅复制**和**深复制**的区别：

1. 浅复制：被复制对象的所有变量都含有与原来的对象相同的值，而所有的对其他对象的引用都引用都仍然指向原来的对象
2. 深复制：把要复制的对象所引用的对象都复制一遍，把引用对象的变量指向复制过的新对象，而不是原有的被引用的对象。

## 八、模板方法模式

当我们要完成在某一细节层次一致的一个过程或一系列步骤，但其个别步骤在更详细的层次上的实现可能不同时，我们通常考虑用模板方法模式来处理。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\8.PNG)

```java
abstract class AbstractClass{
    //定义骨架
	public abstract void operation1();
    public abstract void operation2();
    
    public void TemplateMethod(){
        //定义逻辑
        operation1();
        operation2();
    }
}

class ConcreteClassA extends AbstractClass{
    @override
    public void operation1(){
        //具体实现
    }
    @override
    public void operation2(){
        //具体实现
    }
}
```

模板方法模式是通过把不变行为搬移到超类，去除子类中的重复代码来体现优势

## 九、迪米特法则

如果两个类不必彼此通信，那么这两个类就不应当发生直接的相互作用。如果其中一个类需要调用另一个类需要调用另一个类的某一个类方法的话，可以通过第三者转发这个调用；

在类的结构设计上，每一个类都应该尽量降低成员的访问权限；

迪米特法则其根本思想，是强调了类质检的松耦合，类之间的耦合越弱，越有利于复用，一个处在弱耦合的类被修改，不会对有关系的类造成波及。

## 十、外观模式

在设计初期，要有意识地将不同地两个层分离，经典地三层架构，需要考虑在数据访问层和业务逻辑层，业务逻辑层和表示层地层与层之间建立外观Façade，在开发阶段，子系统往往因为不断地重构演化而变得越来越复杂。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\facade.PNG)

```java
class Facade {
    private SubSystemOne one = new SubSystemOne();
    private SubSystemTwo two = new SubSystemTwo();
    private SubSystemThree three = new SubSystemThree();

    public Facade() {
    }

    public void methodA() {
        System.out.println("Methods A from Facade\n");
        this.one.methodOne();
        this.two.methodTwo();
        this.three.methodThree();
    }

    public void methodB() {
        System.out.print("\nMethods B from Facade\n");
        this.one.methodOne();
        this.three.methodThree();
    }
}

//模块
class SubSystemOne {
    SubSystemOne() {
    }

    public void methodOne() {
        System.out.println("Method one from SubSystemOne");
    }
}

class SubSystemThree {
    SubSystemThree() {
    }

    public void methodThree() {
        System.out.println("Method three from SubSystemThree");
    }
}

```

## 十一、建造者模式

将一个复杂对象的构建和它的表示分离，使得同样的构建过程可以创建不同的表示。使用建造者模式，用户只需要指定建造的类型就可以得到它们，而具体建造的过程和细节就不需要知道了。

思路：用一个抽象类提取整个过程，定义抽象方法和公共属性，让具体的实现去继承这个抽象类，重写抽象方法。还需要一个指挥者（Director）来控制建造。加入抽象类属性，以具体实现类为参数的构造函数来实例化该抽象属性

![](C:\Users\Nakarami\Desktop\大话设计模式\image\9.PNG)

```java
//Director
public class PersonDirector {
    private PersonBuilder pb;

    public PersonDirector(PersonBuilder p) {
        pb = p;
    }

    public void createPerson() {
        pb.BuilderHead();
        pb.BuilderBody();
        pb.BuilderLeftArm();
        pb.BuilderRightArm();
        pb.BuilderLeftLeg();
        pb.BuilderRightLeg();
    }
}

//抽象类
abstract public class PersonBuilder {
    protected Graphics g;
    protected int bX;
    protected int bY;

    public PersonBuilder(Graphics gr, int x, int y) {
        g = gr;
        bX = x;
        bY = y;
    }

    public abstract void BuilderHead();

    public abstract void BuilderBody();

    public abstract void BuilderLeftArm();

    public abstract void BuilderRightArm();

    public abstract void BuilderLeftLeg();

    public abstract void BuilderRightLeg();
}

//具体实现
public class ThinPersonBuilder extends PersonBuilder {
    ...
}
```

## 十二、观察者模式

改善双向耦合的问题

书中的例子：前台秘书 看股票的同事 看NBA的同事  初始程序是双向耦合，秘书和划水同事类需要互相认识。

1.解耦：增加Observer抽象类 所有划水者都去继承，秘书只要认识Observer即可

2.解耦：增加一个通知者接口，让秘书 老板这种可以通知的人去实现该接口

![](C:\Users\Nakarami\Desktop\大话设计模式\image\observer.PNG)

观察者模式的动机：将一个系统分割成一系列相互协作的类会带来维护、扩展、重用的不方便。一个抽象模型有两个方面，其中一方面依赖于另一方面，此时用观察者模式可以将这两者封装在独立的对象中使它们各自的独立地改变和复用。

观察者模式所做的工作其实就是在接触耦合。让耦合的双方都依赖于抽象而非具体，从而使得各自的变化都不会影响另一边的变化。

```java
interface Subject {
    void attach(Observer var1);

    void detach(Observer var1);

    void changeState(String var1);

    void notifyNow();

    String getState();
}


abstract class Observer {
    protected String name;
    protected Subject sub;

    public Observer(String var1, Subject var2) {
        this.name = var1;
        this.sub = var2;
    }

    abstract void notified(String var1);
}

class Boss implements Subject {
    private List<Observer> observers = new ArrayList();
    private String state;

    Boss() {
    }

    public void attach(Observer var1) {
        this.observers.add(var1);
    }

    public void detach(Observer var1) {
        this.observers.remove(var1);
    }

    public void changeState(String var1) {
        this.state = var1;
    }

    public String getState() {
        return this.state;
    }

    public void notifyNow() {
        Iterator var1 = this.observers.iterator();

        while(var1.hasNext()) {
            Observer var2 = (Observer)var1.next();
            var2.notified("fuck you,back to work!");
        }

    }
}

public class NBAObserver extends Observer {
    public NBAObserver(String var1, Subject var2) {
        super(var1, var2);
    }

    public void notified(String var1) {
        System.out.println(this.name + " was notified! message was:" + var1 + " state is:" + this.sub.getState());
    }
}

```

## 十三、单例模式

单例模式保证一个类仅有一个实例，并提供一个访问它的全局访问点。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\singleton.PNG)

让类自身负责保存它的唯一实例。这个类可以保证没有其他实例可以被创建，并提供一个访问该实例的方法。

```java
public final class Singleton1 {
    private static Singleton1 instance = new Singleton1();

    private Singleton1() {
    }

    public static Singleton1 getInstance() {
        return instance;
    }
}

public final class Singleton2 {
    private static Singleton2 instance = null;

    private Singleton2(int var1) {
    }

    public static Singleton2 getInstance(int var0) {
        if (instance == null) {
            Class var1 = Singleton2.class;
            synchronized(Singleton2.class) {
                if (null == instance) {
                    instance = new Singleton2(var0);
                }
            }
        }

        return instance;
    }
}
```

**多线程时的单例**

程序中有两次判断instance是否为null，这种做法时Double-Check-Locking(双重锁定)

目的：对于Instance存在的情况就直接返回，

当Instance为null时，并且有两个线程调用GetInstance方法时，两个进程都可以进入第一重判断，其中一个拿到了锁，另一个排队等待，必须要进入的一个实例化完成以后，第二个才进入，但此时第二个进程无法进入第二重判断，因此不会有两个Instance的实例被创建。

**懒汉式和饿汉式单例**

前者式在第一次被**引用**的时候自己实例化，后者是自己被**加载**时就实例化。



## 十四、抽象工厂模式

书中例子：数据库实现不同，要修改的话需要重写代码--->用工厂方法写数据库工厂，然后把数据库的操作写成接口，然后每一种数据库实现去重写接口。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\abstractFactory.PNG)

本质上还是工厂模式，但涉及到了多个产品要生产的时候，就称之为**抽象工厂模式**：

提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\11.PNG)

通过抽象的接口来操纵实例

缺点是 如果要增加一张表Project，那么除了增加Iproject接口和SqlSeverProject和AccessProject 还需要改动工厂类：增加一个CreateProject，并在两个实现工厂类中添加对应的实现，即**增加一个需求，更改工厂的全部**。

这就有了生硬套模板的嫌疑，用简单工厂方法来简化上述的抽象工厂

![](C:\Users\Nakarami\Desktop\大话设计模式\image\简化抽象工厂.PNG)

继续改进：用反射的方式去实例化，彻底规避switch判断然后new实例。

```java
class DataAccess {
    private static final String dbName = "Access";

    DataAccess() {
    }

    public static IUser createUser() {
        try {
            Class var1 = Class.forName("AccessUser");
            return (IUser)var1.newInstance();
        } catch (Exception var2) {
            var2.printStackTrace();
            return null;
        }
    }

    public static IDepartment createDepartment() {
        try {
            Class var1 = Class.forName("AccessDepartment");
            return (IDepartment)var1.newInstance();
        } catch (Exception var2) {
            var2.printStackTrace();
            return null;
        }
    }
}
```

如果用配置文件去读取dbName，就彻底不需要去更改程序了，在spring中ioc就是这么做的。

## 十五、状态模式

书中的例子：一个方法中有很多的判断，逻辑简单，但是方法太长了。【方法过长是坏味道】

当一个对象的内在状态改变时允许改变其行为，这个对象看起来像是改变了这个类。

**状态模式**：状态模式主要解决的是当控制一个对象状态转换的条件表达式过于复杂时的情况。把状态的判断逻辑转移到表示不同状态的一系列类当中，可以把复杂的判断逻辑简化。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\state.PNG)

```java
abstract class State {
    State() {
    }

    abstract void writeProgram(Work var1);
}
//子状态
public class NoonState extends State {
    public NoonState() {
    }

    public void writeProgram(Work var1) {
        if (var1.getCurrentTime() < 13.0D) {
            System.out.println("current time:" + var1.getCurrentTime() + " ,feels hungry,need a nap");
        } else {
            var1.setState(new AfternoonState());
            var1.writeProgram();
        }

    }
}

//子状态2
public class ForenoonState extends State {
    public ForenoonState() {
    }

    public void writeProgram(Work var1) {
        if (var1.getCurrentTime() < 12.0D) {
            System.out.println("current time:" + var1.getCurrentTime() + " ,feels energetic");
        } else {
            var1.setState(new NoonState());
            var1.writeProgram();
        }

    }
}
//状态3

public class NoonState extends State {
    public NoonState() {
    }

    public void writeProgram(Work var1) {
        if (var1.getCurrentTime() < 13.0D) {
            System.out.println("current time:" + var1.getCurrentTime() + " ,feels hungry,need a nap");
        } else {
            //状态转换
            var1.setState(new AfternoonState());
            var1.writeProgram();
        }

    }
}



//Context
public class Work {
    private State current = new ForenoonState();
    private double hour;
    private boolean isFinished;

    public Work() {
    }

    public void setFinished(boolean var1) {
        this.isFinished = var1;
    }

    public void setHour(double var1) {
        this.hour = var1;
    }

    public double getCurrentTime() {
        return this.hour;
    }

    public void setState(State var1) {
        this.current = var1;
    }

    public void writeProgram() {
        this.current.writeProgram(this);
    }

    public boolean finishedTask() {
        return this.isFinished;
    }
}


```

将与特定状态相关的行为局部化，并且将不同状态的行为分割开。

## 十六、适配器模式

将一个类的接口转换成客户希望的另一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\adapter.PNG)

在已经写好的代码不好修改的时候可以用适配器模式适配，或者一开始考虑使用第三方开发组件的时候，需要适配接口。

```java
abstract class Player {
    protected String name;

    public Player(String var1) {
        this.name = var1;
    }

    abstract void attack();

    abstract void defense();
}

//姚明不懂英语，不能直接继承Player
public class ForeignCenter {
    private String name;

    public ForeignCenter() {
    }

    public void setName(String var1) {
        this.name = var1;
    }

    public String getName() {
        return this.name;
    }

    public void attack() {
        System.out.println(" CENTER " + this.name + " attacking!!");
    }

    public void defense() {
        System.out.println(" CENTER " + this.name + " defense!!");
    }
}
//适配器
public class TranslatorAdapter extends Player {
    private ForeignCenter fc = new ForeignCenter();

    public TranslatorAdapter(String var1) {
        super(var1);
        this.fc.setName(var1);
    }

    public void attack() {
        this.fc.attack();
    }

    public void defense() {
        this.fc.defense();
    }
}

//Main
   TranslatorAdapter var3 = new TranslatorAdapter("YaoM");
   var3.attack();
   var3.defense();
```

## 十七、备忘录模式

<img src="C:\Users\Nakarami\Desktop\大话设计模式\image\Memo.PNG" style="zoom:67%;" />

比如游戏机的存档。

Memento模式比较适用于功能比较复杂的，但需要维护或者记录属性历史的类，或者需要保存众多属性的一小部分。Originator可以根据保存的Memento信息还原到前一状态。

```java
public class GameRole {
    private int vit;
    private int atk;
    private int def;

    public GameRole() {
    }

    public void setVitality(int var1) {
        this.vit = var1;
    }

    public int getVitality() {
        return this.vit;
    }

    public void setAttack(int var1) {
        this.atk = var1;
    }

    public int getAttack() {
        return this.atk;
    }

    public void setDefense(int var1) {
        this.def = var1;
    }

    public int getDefense() {
        return this.def;
    }

    public void displayState() {
        System.out.println("******************************");
        System.out.println("current role state:");
        System.out.println("Vitality: " + this.vit);
        System.out.println("Attack: " + this.atk);
        System.out.println("Defense: " + this.def);
        System.out.println("******************************");
    }

    public RoleStateMemonto saveState2Memonto() {
        return new RoleStateMemonto(this.vit, this.atk, this.def);
    }

    public void recoveryState(RoleStateMemonto var1) {
        this.vit = var1.getVitality();
        this.atk = var1.getAttack();
        this.def = var1.getDefense();
    }

    public void initRole() {
        this.vit = 100;
        this.atk = 100;
        this.def = 100;
    }

    public void fight() {
        this.vit = 0;
        this.atk = 0;
        this.def = 0;
    }
}


public class RoleStateMemonto {
    private int vit;
    private int atk;
    private int def;

    public RoleStateMemonto(int var1, int var2, int var3) {
        this.def = var3;
        this.vit = var1;
        this.atk = var2;
    }

    public void setVitality(int var1) {
        this.vit = var1;
    }

    public int getVitality() {
        return this.vit;
    }

    public void setAttack(int var1) {
        this.atk = var1;
    }

    public int getAttack() {
        return this.atk;
    }

    public void setDefense(int var1) {
        this.def = var1;
    }

    public int getDefense() {
        return this.def;
    }
}

public class RoleStateCaretaker {
    private RoleStateMemonto memento;

    public RoleStateCaretaker() {
    }

    public void saveState(RoleStateMemonto var1) {
        this.memento = var1;
    }

    public RoleStateMemonto getState(int var1) {
        return this.memento;
    }
}

```

## 十八、组合模式

![](C:\Users\Nakarami\Desktop\大话设计模式\image\Composite.PNG)

什么时候用组合模式：需要中体现部分和整体的结构时，以及希望用户可以忽略组合对象和单个对象的不同，统一使用组合结构中的所有对象时，就应该考虑用组合模式了

![](C:\Users\Nakarami\Desktop\大话设计模式\image\layer.PNG)

```java
abstract class Company {
    protected String name;

    Company(String var1) {
        this.name = var1;
    }

    public abstract void add(Company var1);

    public abstract void remove(Company var1);

    public abstract void display(int var1);

    public abstract void lineOfDuty();
}


public class ConcreteCompany extends Company {
    private List<Company> children = new ArrayList();

    public ConcreteCompany(String var1) {
        super(var1);
    }

    public void add(Company var1) {
        this.children.add(var1);
    }

    public void remove(Company var1) {
        this.children.remove(var1);
    }

    public void display(int var1) {
        String var2;
        for(var2 = ""; var2.length() < var1; var2 = var2 + "-") {
        }
		//先打印根节点 然后向叶节点打印
        System.out.println(var2 + this.name);
        Iterator var3 = this.children.iterator();

        while(var3.hasNext()) {
            Company var4 = (Company)var3.next();
            var4.display(var1 + 2);
        }

    }

    public void lineOfDuty() {
        Iterator var1 = this.children.iterator();

        while(var1.hasNext()) {
            Company var2 = (Company)var1.next();
            var2.lineOfDuty();
        }

    }
}
```

## 十九、迭代器模式

![](C:\Users\Nakarami\Desktop\大话设计模式\image\Iterator.PNG)

这个其实就时iterator接口，如果需要不同的迭代方式 可以自己实现。

## 二十、桥接模式

对象的继承关系是在编译时就定义好了，在运行时就无法改变父类的实现，子类的实现与父类的实现有着非常紧密的关系，以至于父类中的任何变化都会导致子类的变换。这种继承关系限制了灵活性并最终限制的复用性。

**合成/聚合复用原则**

尽量不要使用类继承。继承是一种强耦合的结构。

聚合为弱的拥有关系，A对象可以包含B对象，但B对象不是A对象的一部分；

合成是一种强的拥有关系，体现严格的整体和部分的关系。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\合成聚合.PNG)

好处：有助于每个类都被封装，并被集中在单个任务上，这样类和类继承层次会保持较小的规模。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\old.PNG)

![](C:\Users\Nakarami\Desktop\大话设计模式\image\new.PNG)

```java
abstract class HandsetSoft{
	//软件
    public abstract void Run();
	
}
class HandsetGame extends HandsetSoft(){
    public void run(){
        sout("运行手机游戏")
    }
}

//手机
abstract class HandsetBrand{
    protected HandsetSoft soft;//聚合
    public void SetHandsetSoft(HandsetSoft soft){
        this.soft =soft;
    }
}

```

**正式进入桥接模式**

将抽象部分与他的实现部分分离，是的他们都可以独立的变化。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\bridge.PNG)

## 二十一、命令模式

行为请求者和行为实现者应该尽可能地松耦合。

![](C:\Users\Nakarami\Desktop\大话设计模式\image\命令 紧耦合.PNG)

![命令 松耦合](C:\Users\Nakarami\Desktop\大话设计模式\image\命令 松耦合.PNG)

```java
public class Barbecuer {
    //烤肉串者
    public Barbecuer() {
    }

    public void bakeMutton() {
        System.out.println("bake mutton~~~");
    }

    public void bakeChicken() {
        System.out.println("bake chicken~~~");
    }
}

//命令
abstract class Command {
    protected Barbecuer receiver;

    public Command(Barbecuer var1) {
        this.receiver = var1;
    }

    abstract void excuteCommand();
}

//具体命令1
public class BakeMuttonCommand extends Command {
    public BakeMuttonCommand(Barbecuer var1) {
        super(var1);
    }

    public void excuteCommand() {
        this.receiver.bakeMutton();
    }
}
//具体命令2
public class BakeChickenCommand extends Command {
    public BakeChickenCommand(Barbecuer var1) {
        super(var1);
    }

    public void excuteCommand() {
        this.receiver.bakeChicken();
    }
}
//服务员
public class Waiter {
    private List<Command> orders = new ArrayList();
    public boolean hasChicken = true;
    public boolean hasMutton = true;

    public Waiter() {
    }

    public void setOrder(Command var1) {
        if (var1 instanceof BakeMuttonCommand && !this.hasMutton) {
            System.out.println("there is no mutton anymore!");
        } else if (var1 instanceof BakeChickenCommand && !this.hasChicken) {
            System.out.println("chicken are fly away!");
        } else {
            this.orders.add(var1);
            System.out.println("add a new " + var1.getClass().getSimpleName() + " at:" + System.currentTimeMillis());
        }
    }

    public void cancelOrder(Command var1) {
        this.orders.remove(var1);
        System.out.println("Cancel a " + var1.getClass().getSimpleName() + " at:" + System.currentTimeMillis());
    }

    public void notifyOrder() {
        Iterator var1 = this.orders.iterator();

        while(var1.hasNext()) {
            Command var2 = (Command)var1.next();
            var2.excuteCommand();
        }

    }
}
//客户端实现
    public static void main(String[] var0) {
        Barbecuer var1 = new Barbecuer();
        Waiter var2 = new Waiter();
        BakeMuttonCommand var3 = new BakeMuttonCommand(var1);
        BakeMuttonCommand var4 = new BakeMuttonCommand(var1);
        BakeChickenCommand var5 = new BakeChickenCommand(var1);
        var2.setOrder(var3);
        var2.hasMutton = false;
        var2.setOrder(var4);
        var2.setOrder(var5);
        var2.notifyOrder();
    }
```

