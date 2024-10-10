---
title: Z3 for feature selection solving problems
date: 2024-10-10 09:00:00 +0000
tags: [learning,junior,development]
published: true
---

### Optimizing Bike Builds with Z3: A Solver for Part Selection

When you’re running a bike shop that offers customizable builds, you quickly run into a common problem: how do you ensure that customers can mix and match parts, without accidentally combining incompatible options? This is where things get tricky. Just like finding the right chord progression to harmonize with a melody, selecting compatible bike parts requires an understanding of both the rules and the exceptions. You need to account for which parts work together, apply price rules, and avoid combinations that just won’t fit. Luckily, the [Z3 solver from Microsoft Research](https://github.com/Z3Prover/z3) can help make this problem much easier to manage.

In this post, we'll break down how we use the **Z3 solver** in Python to create a part selection service for custom bike builds. It’s a bit like creating a playlist for a jam session, except instead of making sure the keys and tempos work together, we’re figuring out which handlebars fit the frame and which brake options are compatible with certain wheels.

Let’s dive in!

---

### What is Z3?

Z3 is an SMT (Satisfiability Modulo Theories) solver. In simpler terms, it’s a tool that helps solve complex logical constraints. It’s commonly used in formal verification, optimization problems, and decision making, where different rules or conditions must be considered. For our use case—building bikes—we can leverage Z3 to check whether the customer’s selected parts are compatible with each other.

---

### The Problem: Part Compatibility in Bike Builds

Consider a scenario where we sell custom bikes, and each bike consists of several parts, such as frames, wheels, brakes, and gear options. Each part can come with several options, but not all of them play nicely together. For example:

- A specific frame may only support disc brakes and not rim brakes.
- Certain gear options might be incompatible with particular wheels.
- Some components might be temporarily out of stock.

To make this manageable, we need a way to check if a customer’s selection of parts is valid before allowing the order. We also need to handle pricing rules based on the selected options.

---

### Enter the Part Selection Solver

Let’s break down how we use the Z3 solver in our bike shop with the `PartSelectionService` class.

#### Step 1: Setting Up Options and Parts

We begin by creating Z3 variables for each part option. Each part (like a handlebar or a wheel) can have several options. For instance, if we have three different wheels, we create three variables representing those options:

```python
self.option_vars = {
    option.id: Int(f"part{option.part_id}") for option in options
}
```

This ensures that each option is represented as a unique variable in the solver.

#### Step 2: Defining Compatibility Rules

Now, let’s handle compatibility rules. Using `Z3`, we define logical constraints to ensure that incompatible parts are not selected together. If a customer selects a specific frame, we want the solver to ensure only compatible wheel options are available. Here’s how we add those rules:

```python
for option1_id, rules in grouped_compatibilities.items():
    option1_var = self.option_vars[option1_id]

    self.solver.add(
        Implies(
            option1_var == option1_id,
            Or(
                [self.option_vars[opt_id] == opt_id for opt_id in rules["compatible"]]
                + [self.option_vars[opt_id] != opt_id for opt_id in rules["incompatible"]]
            )
        )
    )
```

This chunk of code means: *If option A is selected, then option B must also be selected, and option C must not be selected*. For example, if a customer chooses disc brakes, the solver ensures that incompatible wheels are not part of the final build.

#### Step 3: Selecting Available Options

Before presenting the final configuration to the customer, we want to ensure they’re only seeing valid options. We can do this by checking each part’s options using Z3’s `check()` method, which tells us if the current constraints are "satisfiable" (i.e., if they work together):

```python
def get_available_options(self, parts):
    return {
        part.id: list(filter(
            lambda option: self.is_option_available(option),
            part.options
        ))
        for part in parts
    }

def is_option_available(self, option):
    self.solver.push()
    self.solver.add(self.option_vars[option.id] == option.id)

    is_available = self.solver.check() == sat

    self.solver.pop()

    return is_available
```

Here, the solver is tested with each option in isolation to determine which ones are valid in the current configuration. The `push()` and `pop()` methods allow us to temporarily add constraints to the solver without permanently modifying its state—sort of like testing different combinations in a jam session to see what works.

#### Step 4: Finalizing the Selection

Once the customer has made their final selection, we use Z3 to lock in the chosen options and make sure the entire configuration is valid. We add all the customer’s choices to the solver and check for any conflicts:

```python
def select_part_options(self, options):
    for option in options:
        self.solver.add(self.option_vars[option.id] == option.id)
```

If the solver still reports that the configuration is valid (`sat`), we can proceed with the order.

---

### Wrapping Up: Why Z3 Rocks for Bike Shops

Z3 shines in this scenario because it takes a complex problem—part compatibility—and makes it manageable. By modeling each part and its options as variables with constraints, we can guarantee that customers only see configurations that work. This reduces the chances of errors, like incompatible parts being sold together, and it helps ensure that our builds are consistent and functional.

Just like how a skilled musician knows which notes can blend to create a harmonious chord, Z3 helps us mix and match parts that create the perfect bike build for every customer.

So next time you’re selecting parts for your next custom build, think of Z3 as the tuner ensuring everything comes together in perfect harmony!

---

Happy riding and happy solving!

