
桥接设计模式（Bridge Pattern）是一种结构型设计模式，它通过将抽象部分与实现部分分离，使它们可以独立地变化。这种模式特别适合于需要在多个维度上扩展的场景，避免了类爆炸（类的数量随着组合需求呈指数级增长）的情况。


### 核心思想


* **抽象部分**：定义对象的主要功能或者高层操作接口。
* **实现部分**：实现抽象部分定义的具体功能。
* **桥接**：通过组合（而非继承）将抽象部分与实现部分连接在一起。


桥接模式关注的是**将抽象层和实现层解耦**，使得它们可以独立变化，以便应对复杂的变化场景。通过这种分离，抽象和实现可以独立扩展，不会互相影响，通常是为了`处理多维度扩展问题`。


### 组成部分


1. **Abstraction（抽象类）**


* 定义抽象的接口或基类。
* 持有一个对实现类（Implementor）的引用。


2. **RefinedAbstraction（细化抽象类）**


* 扩展或实现抽象类的功能。


3. **Implementor（实现类接口）**


* 定义实现类的通用接口。


4. **ConcreteImplementor（具体实现类）**


* 实现 `Implementor` 接口的具体逻辑。


## 案例\-\-支付系统中的桥接模式


在支付系统中，我们需要支持不同的`支付方式`（如微信支付、支付宝支付、信用卡支付等），而且支付方式可能会随着时间和需求的变化而不断增加。同时，每种支付方式又可能需要处理不同的`支付类型`（例如，消费、退款等操作）。**桥接模式**可以帮助我们将支付方式和支付类型分离，让它们独立扩展，减少类的数量。


### 0\. 类图


![image](https://img2024.cnblogs.com/blog/1209017/202501/1209017-20250106213724968-514370671.png)


### 1\. 抽象部分：支付操作


首先，我们定义一个抽象的 `Payment` 类，代表支付操作。该类包含一个指向支付方式的引用，支付方式可以是微信支付、支付宝支付等。



```
// Abstraction: 抽象的支付操作类
abstract class Payment {
    protected PaymentMethod paymentMethod; // 支付方式

    public Payment(PaymentMethod paymentMethod) {
        this.paymentMethod = paymentMethod;
    }

    // 执行支付操作
    abstract void executePayment(double amount);
}

```

### 2\. 具体支付操作：消费和退款


然后，我们定义两种支付类型：消费和退款。每种支付类型都可以独立扩展。



```
// RefinedAbstraction: 消费支付
class ConsumerPayment extends Payment {
    public ConsumerPayment(PaymentMethod paymentMethod) {
        super(paymentMethod);
    }

    @Override
    void executePayment(double amount) {
        System.out.println("Initiating consumer payment of " + amount + " using " + paymentMethod.getMethodName());
        paymentMethod.processPayment(amount);
    }
}

// RefinedAbstraction: 退款支付
class RefundPayment extends Payment {
    public RefundPayment(PaymentMethod paymentMethod) {
        super(paymentMethod);
    }

    @Override
    void executePayment(double amount) {
        System.out.println("Initiating refund payment of " + amount + " using " + paymentMethod.getMethodName());
        paymentMethod.processPayment(amount);
    }
}

```

### 3\. 实现部分：支付方式


接下来，我们定义支付方式接口 `PaymentMethod`，以及它的具体实现类：微信支付、支付宝支付、信用卡支付等。



```
// Implementor: 支付方式接口
interface PaymentMethod {
    String getMethodName(); // 获取支付方式名称
    void processPayment(double amount); // 处理支付操作
}

// ConcreteImplementor: 微信支付
class WeChatPayment implements PaymentMethod {
    @Override
    public String getMethodName() {
        return "WeChat Payment";
    }

    @Override
    public void processPayment(double amount) {
        System.out.println("Processing payment of " + amount + " through WeChat.");
    }
}

// ConcreteImplementor: 支付宝支付
class AlipayPayment implements PaymentMethod {
    @Override
    public String getMethodName() {
        return "Alipay Payment";
    }

    @Override
    public void processPayment(double amount) {
        System.out.println("Processing payment of " + amount + " through Alipay.");
    }
}

// ConcreteImplementor: 信用卡支付
class CreditCardPayment implements PaymentMethod {
    @Override
    public String getMethodName() {
        return "Credit Card Payment";
    }

    @Override
    public void processPayment(double amount) {
        System.out.println("Processing payment of " + amount + " through Credit Card.");
    }
}

```

### 4\. 使用桥接模式的客户端


我们可以通过客户端代码来进行测试。客户端可以动态地组合不同的支付方式和支付操作类型（如消费或退款）。



```
public class BridgePatternExample {
    public static void main(String[] args) {
        // 使用微信支付进行消费
        Payment payment1 = new ConsumerPayment(new WeChatPayment());
        payment1.executePayment(100.0);

        // 使用支付宝支付进行退款
        Payment payment2 = new RefundPayment(new AlipayPayment());
        payment2.executePayment(50.0);

        // 使用信用卡支付进行消费
        Payment payment3 = new ConsumerPayment(new CreditCardPayment());
        payment3.executePayment(200.0);
    }
}

```

### 5\. 运行结果


我们通过桥接模式将`支付操作`（抽象）和`支付方式`（具体）分离开来，**使得支付操作和支付方式可以独立扩展**。之所以，称`支付操作`为**抽象部分**、`支付方式`为**具体部分**，那是因为支付操作并没有实实在在调用支付接口完成支付操作，而是由`支付方式`去调用支付接口完成的支付操作。 如果将来我们需要添加新的支付操作（例如银行转账等）或支付方式（例如代付、分期付款等），我们可以独立地扩展它们，而不需要修改现有的代码。


## 桥接模式与策略模式的对比


* **策略模式**：如果你希望在运行时动态地切换支付方式，而不需要知道具体的支付操作（如消费、退款等），**策略模式**更合适。在这种模式下，我们将支付方式作为一种策略，并在运行时选择适当的策略来处理支付。
* **桥接模式**：如果你有多个维度需要扩展（比如支付操作和支付方式），并且希望将抽象和实现分离，使得它们可以独立变化，**桥接模式**则更为合适。桥接模式适合需要同时支持多个变化维度（例如支付方式和支付类型）的场景。


### 桥接模式的优点


1. **分离抽象和实现**：可以分别独立开发，不互相影响；
2. **提高系统的扩展性**：增加新的抽象类或实现类都非常方便；
3. **多维度遵循开闭原则**：在多维度场景问题中，修改或扩展某一维度不会影响其他部分。


### 桥接模式的适用场景


1. **一个类有两个（或多个）独立变化的维度**，且需要独立扩展时。
2. 不希望通过继承增加类的数量。
3. 需要在运行时切换实现。


## 总结


桥接模式通过将抽象部分和实现部分分离，极大地增强了代码的灵活性和可维护性。它是一种非常实用的设计模式，尤其在需要扩展多个维度的场景中，可以显著减少代码复杂度。


桥接模式**强调抽象和实现分离及多维度扩展**，在这些情况下可以考虑使用桥接模式。


![image](https://img2024.cnblogs.com/blog/1209017/202501/1209017-20250106213705570-898570662.gif)


需要查看往期设计模式文章的，可以在个人主页中或者文章开头的集合中查看，可关注我，持续更新中。。。




---


[超实用的SpringAOP实战之日志记录](https://github.com)


[2023年下半年软考考试重磅消息](https://github.com)


[通过软考后却领取不到实体证书？](https://github.com)


[计算机算法设计与分析（第5版）](https://github.com):[悠兔机场官网订阅](https://5tutu.com)


[Java全栈学习路线、学习资源和面试题一条龙](https://github.com)


[软考证书\=职称证书？](https://github.com)


[软考中级\-\-软件设计师毫无保留的备考分享](https://github.com)


