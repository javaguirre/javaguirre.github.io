---
title: 'Refactoring with the strategy pattern'
date: 2024-08-13 10:42:52 +0000
tags: [strategy,patterns]
published: true
---

### Refactoring with the Strategy Pattern in Python

When working on a system that involves multiple variations of a process, like payment processing, it's easy to end up with complex `if-else` statements. This can make the code difficult to manage and extend. The **Strategy Pattern** provides a way to encapsulate each variation of the process into separate classes, making the code more flexible and maintainable.

#### The Problem

Suppose you're working on a payment processing system for a platform called **SkyPay**. The code currently supports several payment methods: credit card, PayPal, and bank transfers. Here’s what the code might look like initially:

```python
class PaymentProcessor:
    def process_payment(self, method: str, amount: float) -> None:
        # ... Legacy code

        if method == 'credit_card':
            self._process_credit_card_payment(amount)
        elif method == 'paypal':
            self._process_paypal_payment(amount)
        elif method == 'bank_transfer':
            self._process_bank_transfer_payment(amount)
        else:
            raise ValueError('Unknown payment method')

        # ...more legacy code

    def _process_credit_card_payment(self, amount: float) -> None:
        print(f'Processing credit card payment of ${amount}')

    def _process_paypal_payment(self, amount: float) -> None:
        print(f'Processing PayPal payment of ${amount}')

    def _process_bank_transfer_payment(self, amount: float) -> None:
        print(f'Processing bank transfer payment of ${amount}')
```

As more payment methods are added, this `if-else` structure becomes cumbersome and harder to maintain. Even more when we have code around adding more complexity to the payment processing logic as we could have in a legacy codebase.

#### Refactoring with the Strategy Pattern

Let's refactor the code using the **Strategy Pattern**. We’ll create a base `PaymentStrategy` interface and concrete strategies for each payment method.

```python
from abc import ABC, abstractmethod

# Payment strategy interface
class PaymentStrategy(ABC):
    @abstractmethod
    def process_payment(self, amount: float) -> None:
        pass

# Concrete strategies
class CreditCardPayment(PaymentStrategy):
    def process_payment(self, amount: float) -> None:
        print(f'Processing credit card payment of ${amount}')

class PayPalPayment(PaymentStrategy):
    def process_payment(self, amount: float) -> None:
        print(f'Processing PayPal payment of ${amount}')

class BankTransferPayment(PaymentStrategy):
    def process_payment(self, amount: float) -> None:
        print(f'Processing bank transfer payment of ${amount}')

# Payment processor using the strategy
class PaymentProcessor:
    def __init__(self, strategy: PaymentStrategy) -> None:
        self._strategy = strategy

    def set_strategy(self, strategy: PaymentStrategy) -> None:
        self._strategy = strategy

    def process_payment(self, amount: float) -> None:
        self._strategy.process_payment(amount)

# Usage example
if __name__ == "__main__":
    credit_card_payment = CreditCardPayment()
    paypal_payment = PayPalPayment()
    bank_transfer_payment = BankTransferPayment()

    processor = PaymentProcessor(credit_card_payment)
    processor.process_payment(100.0)

    processor.set_strategy(paypal_payment)
    processor.process_payment(200.0)

    processor.set_strategy(bank_transfer_payment)
    processor.process_payment(300.0)
```

With this refactor, the `PaymentProcessor` class no longer needs to know the details of each payment method. Instead, it relies on the strategy passed to it, making it easy to add new payment methods by simply creating a new strategy class.

---

#### The Importance of Dependency Injection

In this refactor, Dependency Injection plays a vital role by allowing the `PaymentProcessor` to remain agnostic of the specific payment strategies it uses. By injecting the appropriate `PaymentStrategy` into the `PaymentProcessor`, we decouple the processor from the implementation details of each payment method.

This not only makes the code more flexible—enabling easy swapping of strategies at runtime—but also significantly improves testability. With Dependency Injection, we can effortlessly mock or replace strategies during testing, allowing for isolated and focused unit tests.

Ultimately, this approach leads to a more modular, maintainable, and adaptable system, where each component adheres to the single responsibility principle.

---

#### Testing with Pytest

Testing this setup in Pytest is straightforward. Below is an example of how you can write tests for the payment processor using Pytest:

```python
import pytest

@pytest.fixture
def payment_processor():
    return PaymentProcessor(CreditCardPayment())

def test_credit_card_payment(payment_processor, capsys):
    payment_processor.process_payment(100.0)
    captured = capsys.readouterr()
    assert captured.out == "Processing credit card payment of $100.0\n"

def test_paypal_payment(payment_processor, capsys):
    payment_processor.set_strategy(PayPalPayment())
    payment_processor.process_payment(200.0)
    captured = capsys.readouterr()
    assert captured.out == "Processing PayPal payment of $200.0\n"

def test_bank_transfer_payment(payment_processor, capsys):
    payment_processor.set_strategy(BankTransferPayment())
    payment_processor.process_payment(300.0)
    captured = capsys.readouterr()
    assert captured.out == "Processing bank transfer payment of $300.0\n"
```

In these tests:

- The `payment_processor` fixture initializes a `PaymentProcessor` with a `CreditCardPayment` strategy.
- Each test method changes the payment strategy using `set_strategy` and verifies that the correct output is printed using `capsys` to capture printed output.

#### Why Use the Strategy Pattern?

- **Maintainability**: The Strategy Pattern allows you to keep the core logic of your application clean and focused. Each payment method is handled in its own class, making it easier to manage and update.
- **Flexibility**: You can easily switch between different payment methods at runtime without altering the core payment processing logic.
- **Extensibility**: Adding a new payment method requires just creating a new strategy class and passing it to the `PaymentProcessor`, leaving the existing code untouched.

---

### Conclusion

The **Strategy Pattern** is a useful design pattern that can help you manage complex algorithms or processes by encapsulating them into separate, interchangeable classes. By applying this pattern to a payment processing system, we’ve made the code more modular, easier to maintain, and more adaptable to change.

Design patterns like this one are tools in your software engineering toolkit that can help you write better, more maintainable code. Happy refactoring! :-)
