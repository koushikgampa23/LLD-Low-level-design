# LLD-Low-level-design
LLD (Low level design)

## SOLID principles and small intro about them
    S - Single responsibility Priciple(SRP)
        A class should have single responsibility
        Example:
            A user object should have responsibility such as adding user, getting user info, user related data
            But the database insertions should not be happening in same class, if we wanted to change the db in future it effect current code
            Make it 2 objects User object responisible of user data, UserManager/UserRepo for managing db inserations

    O - Open/Closed Principle
        Our code should be open for adding new features and it should be closed for modifications since the exisiting code has been tested and approved, modifiying existing code can lead to retest
        Example:
            Bad example:
                class Payment:
                    def pay(self, pay_type="upi"):
                        if pay_type == "upi":
                            pass
                        if pay_type == "credit":
                            pass
            If i wanted to add new payment method i need to add to exisiting code instead 
            Good example:
                class Payment(ABC):
                    @abstractmethod
                    def pay(self):
                        pass
                class UPIPayment(Payment):
                    def pay(self):
                        print("payment done")
                class CreditCardPayment(Payment):
                    def pay(self):
                        print("payment done")
                class PaymentProcessor: # Manages which payment type
                    def process_payment(payment):
                        payment.pay()
                
                upi_payment = UPIPayment()
                payment_processer = PaymentProcesser()
                payment_processer.process_payment(upi_payment) #Passing the payment has an object

    L - Liskov Substitution Principle (LSP)
        Defination:
        Parent abstractions should be designed so that child classes can replace them without breaking expected behavior.
        Explanation:
        A child that inherits the parent must not forcefully implement its method. A parent class to be designed specifically, or designed with minimum so that every child can implement without force
        Example:
            Bad:
                class Account(ABC):
                    @abstractmethod()
                    def deposit(self):
                        pass
                    def withdraw(self):
                        pass
            SavingsAccount we need deposit and withdraw that is correct, but FixedDepositAccount doesnot need withdraw option
            Good:
                class Account(ABC):
                    def __init__(self, balance):
                        self.balance = balance
                    @abstractmethod
                    def deposit(self,amount):
                        pass
                class WithDrawableAccount(Account):
                    def __init__(self,balance):
                        super().__init__(balance)
                    @abstractmethod
                    def withdraw(self, amount):
                        pass
                #Lets implement those interfaces
                class FixedDepositAccount(Account):
                    def deposit(self, amount):
                        self.balance += amount
                class SavingsAccount(WithDrawableAccount):
                    def deposit(self,amount):
                        self.balance += amount
                    def withdraw(self, amount):
                        self.balance -= amount
                savings_account = SavingsAccount(1000)
                savings_account.deposit(500)
                savings_account.withdraw(200) 
                fixed_deposit_account = FixedDepositAccount(2000)
                fixed_deposit_account.deposit(100)

    I - Interface Segregation Principle (ISP)
        Defination:
        Build smaller, focused interfaces so that classes implement only the functionality they actually need.

        Give methods to class that necessary to it
        Example:
            if i create a interface with Employee class with abstact methods work, eat
            if i create robot concrete class based on employee, it should not implement eat
            so create Workable interface that contains work abstract method, Eatable interface contains eat abstract method
            if i create EmployeeConcreteClass inherit both Workable, Eatable class
            if i create RobotConcreteClass inherit only workable class
    
    D - Dependency Inversion Principle (DIP)
        High level modules should not directly depend on low level modules. both should depend on abstractions.

        Example:
            Desgin a notification system to send notification through email, sms and whatsapp
            Bad:
                class EmailService:
                    def send(self, message):
                        print("message from email: message")
                class SMSService:
                    def send(self, message):
                        print("message from sms: message)
                
                from .services import EmailService, SMSService
                class Notification:
                    def __init__(self):
                        self.email_service = EmailService()
                        self.sms_service = SMSService()
                    def notifyByEmail(self, message):
                        self.email_service.send(message)
                    def notifyBySMS(self,message):
                        self.sms_service.send(message)
                
                notify = Notification()
                notify.notifyByEmail("hello") #message from email: hello
            Good:
                Instead of Notification service depending on the low level module it should depend on abstract/contract

                class NotificationChannel(ABC):
                    @abstractmethod
                    def send(self, message):
                        pass
                class SMSService(NotificationChannel):
                    def send(self, message):
                        print("message from sms: message)
                class EmailService(NotificationChannel):
                    def send(self, message):
                        print("message from email: message)
                
                class NotificationService:
                    def __init__(self, notification_channel: NotificationChannel):
                        self.notification_channel = notification_channel

                    def notify(self, message):
                        self.notification_channel.send(message)
                
                email_service = EmailService()
                notification_service = NotificationService(email_service)
                notification_service.notify("hello") #message from email: hello

## Design Patterns
    Total patterns are 22:
    Important patterns also implement in python(9):
        Strategy, Factory Method, Singleton, Observer, Decorator, Adapter, State, Command, Builder, Iterator
    Medium important(5):
        Facade, Proxy, Composite, Template Method, Chain of Responsibility
    Low(7):
        Abstract Factory, Bridge, Flyweight, Memento, Mediator, Prototype, Visitor, Interpreter
### Behavioural Design pattern
    How objects talk to each other
    How Objects should communicate with one another
    how objects relate to each other and define communication rules that keeps code flexible and loosely connected
    Coordination interactions between objects
    Managing state transitions and communication efficiently

    We mainly focus on interactions, state transitions and communication
#### Memento pattern
    undo/redo problem statement
#### Observer pattern
    wheather station gives temparature update, that has to notify different set of devices like tvs, mobile and other devices
    Example:
        1 workstation notifies all the devices but workstation need not to know each device how they work
#### Strategy pattern
    Imagine builiding a flipkart app and want to offer discounts
    first time user: 100 ruppees off
    Diwali sale: 20% on all products
    Student offer: 15% on student ids

    if i implement using normally i need to add multiple if statements if holi sale comes i need to modify the code that violates open/closed solid priciple rule

    First i will create DiscountStrategy interface that contains abstract method apply
    Student Concrete class inherits it and define apply method
    Diwali Concrete class inherits it and define apply method

    i will pass student object if i want student discount to discount service
#### Command pattern
    Waiter taking order and asking chef to cook food
#### Template method Pattern
    Image building system that reads files of different types like CSV, XML, and JSON. Every file reader needs to follow basic process: open, parse and close

    but the open and close logic is same for all the file types, the only change in logic is parse.

    Create a DataParser abstract class that contains open and close methods and make the parse method as the abstract method
    The concrete class JSONParser must implement parse method
    It will implement using 
        def parse(): 
            self.open() 
            print("parsing json") 
            self.close()
    Note: we got open and close method since we have inherited the DataParser class
#### Iterator Pattern
    Imagine we are buliding a song playlist app, internally we might use set or list or trees, if we do our application as package then the client doesnot know how to iterate through playlist
    so we create custom iterator and client only needs to know next, has_next methods to iter over playlist
    Example: Collection framework follows this
    Interview:
        The Iterator Design Pattern provides a standard way to traverse elements of a collection without exposing its underlying representation.
        The collection is responsible for storing elements, while the iterator maintains the traversal state and provides methods to access elements sequentially.
        This allows clients to traverse different collection implementations through a common interface without depending on their internal data structures.
#### State Design Pattern
    Imagine iam building flipkart order system, where state of order placed or shipped or delivered, in this case i can use State Design

    Create a OrderState interface contains confirm, ship, deliver abstract method,
    Concrete class PlacedState, ConfirmState, ShipState, DeliveredState concrete classes implement them
#### Mediator Design Pattern
    Imagine buliding airport system, where multiple airplanes need to coordinate for landings and takeoffs. If each airplane communicate with another airplane it makes system complex
#### Singleton Design Pattern

#### Factory Design pattern
    Imagine running a restaurant, each food item is object, if we add new items to menu increases the adding code complexicity

    The factory pattern moves all object creation logic into one central place - the factory class. Instead of creating all the objects directly through code, you hand over this responsibility to the factory, which decides which specific class to create based on request. This approach follows the open-closed principle by allowing you to add new food types without chaning any exisiting code - you only update the factory

    Use cases:
        Buliding apps based on os, factory gives objects if it windows, if it linux or mac
        Database connections if postgres give postgres connector, mysql give that
        File export tools when user wants to export document in different formates, like word or pdf the factory determines which file generator to generate the file

#### Abstract Factory Design pattern
    Imagine i wanted to create cusine for a restaurent, if choose one cusine it should contain starter, meal and dessert
    if i choose that particular cusine i need to get everything of that particular cusine

#### Builder desing pattern
    When creating an object that requires many different settings - most of them are optional, the constructor becomes messy and confusing.
    Problems:
        how to remeber which are optional and which are required
        long params in constructor
    The builder designer pattern lets you construct complex objects step-by-step in a clear and readable way.

    question:
        Why are you returning self from setter methods?
        To support method chaining (fluent interface), allowing multiple configuration methods to be called in a single statement.

#### Adapter Design pattern
    The adapter pattern is a design pattern that lets two systems or components that weren't designed to work together communicate with each other by creating a bridge between them

    Imagine i have built a e-commerce application and send notifications to customers using EmailNotificationSystem that i have built
    When i wanted to switch to the popular email service SendGrid, 
    Your existing code expects one interface, but sendGrid expects a completely different interface. That dont match.

#### Decorator pattern
    Imagine you have a coffee class, what if i wanted to create a coffee + milk, coffee+milk+cream, i need to add new features to the existing object
    i need to create subclasses for varient, imagine i have 20 combination then i need to create 20 more classes
    This is called explosion of subclasses

#### Proxy pattern
    Sometimes you dont want to give direct access to an object because of security concerns, performance concerns, or need to control how its accessed

    The proxy pattern creates a standin object that sits between the client and real object. The proxy control and manages access to the real object.

    Imagine iam building a photo gallery application that contains high resolution images, without proxy pattern the application will try to load every single image when the applicaiton opens, even if the user didnt even visit the image that loads
    Time(slow startup), performance(loading unnecessary photos), processing power(wasting resource)

    with proxy, the image is only loaded when user actually tries to view it. The proxy acts as a placeholder until the real image is needed

                     
                

