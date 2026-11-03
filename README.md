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
    D - Dependency Inversion Principle (DIP)