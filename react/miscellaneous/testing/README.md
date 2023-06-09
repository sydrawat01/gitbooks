---
description: Basics of Testing in React Apps.
---

# 🧪 Testing

## What & Why?

What is testing? Why do we need tests.

![Manual vs Automated Testing](<../../.gitbook/assets/Screenshot 2021-05-12 at 12.29.38.png>)

## Types of Automated Tests

There are 3 basic categories of automated tests:

1. Unit Tests
2. Integration Tests
3. End-to-end (e2e) Tests

![Types of Automated Tests](<../../.gitbook/assets/Screenshot 2021-05-12 at 12.49.14.png>)

### Unit Tests

These are the most common and important type of tests. Unit tests are basically the ones that test the **individual building blocks** (functions, components) of the app **in isolation**.

A typical app contains dozens and hundreds of unit tests.

### Integration Tests

Integration tests usually test the **combination** of multiple building blocks. Projects typically contain a couple of integration tests. These are also important, but the focus is more on unit tests.

### E2E Tests

These automated tests test the complete scenarios in our app as the user would experience them. Projects typically contain a few end-to-end tests. These types of test are important, but can also be done manually (_partially_).

## What & How to Test?

We'll be working with unit tests, so typically we will be testing the smallest building blocks of our code.

![What & How to test our app.](<../../.gitbook/assets/Screenshot 2021-05-12 at 12.58.38.png>)
