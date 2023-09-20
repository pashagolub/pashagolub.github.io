---
layout: post
title:  Announcing pgxmock v3 - Elevating Mocking for PostgreSQL in Go!
description: >
 I am thrilled to introduce the much-anticipated major release of pgxmock v3! This release represents a  significant leap forward in enhancing your PostgreSQL database mocking experience in Go.
date:   2023-09-20 12:10:19
tags: 
- golang
- pgxmock
- what's new
- postgresql
- personal
---
I am thrilled to introduce the much-anticipated major release of **[pgxmock v3](https://github.com/pashagolub/pgxmock/releases/tag/v3.0.0)**! This release represents a significant leap forward in enhancing your PostgreSQL database mocking experience in Go.

Here's a quick overview of the exciting changes and additions in this release:

## High-Priority New Features

### Rewrite with `findExpectationFunc()`
I've completely overhauled the internals of `pgxmock` by rewriting all methods using `findExpectationFunc()`. This improvement allows us to add even more custom expectations without writing a duplicate code. The only thing we will need is a custom callback function to compare a new kind of expectation.

### Enhanced Expectations
I've expanded the capabilities of **all** expectations with new methods like `WillDelayFor()`, `WillReturnError()`, and `WillPanic()`. Now you have even more control over how your mock database behaves in various scenarios.

### Introducing `Times()` and `Maybe()`
I'm introducing the `Times()` method, allowing you to specify how many times an expectation should be met. This feature enhances the flexibility of your mock database interactions. Meanwhile `Maybe()` method allows the expectation to be optional. Not calling an optional method will not cause an error while asserting expectations.

## New Features

### Improved Test Coverage [![Coverage Status](https://coveralls.io/repos/github/pashagolub/pgxmock/badge.svg?branch=master)](https://coveralls.io/github/pashagolub/pgxmock?branch=master)
I've extended test coverage, ensuring that `pgxmock` remains a reliable choice for your testing needs. To make the testing even more comprehensive, I've added several new test cases, e.g., `TestRowsConn()` and `TestPanic()`. These scenarios cover a wider range of potential use cases. They will help us to eliminate possible bugs in the future.

### Keeping Up with the Times
I'm staying up-to-date with the latest developments. In this release, I've bumped up the Go version to v1.21, ensuring compatibility and leveraging the newest features of the language.

### Introducing CallModifier Interface
I've introduced the `CallModifier` interface to expectations, providing greater control and customization when working with mock database calls. All expectations currently support common behavior modifiers, like delay, error, and panic. We can extend the list later with ease.

## Ready to Upgrade?

I encourage all users to upgrade to **pgxmock v3** to take advantage of these exciting enhancements and improvements. Whether you're a long-time user or just getting started with pgxmock, these changes will elevate your database mocking experience to new heights.

To get started with pgxmock v3, simply update your import statement:

```go
import "github.com/pashagolub/pgxmock/v3"
```

and/or run 

```sh
go get -u github.com/pashagolub/pgxmock/v3
```

Thank you for your continued support and feedback. I look forward to seeing how **pgxmock v3** empowers your PostgreSQL database testing. If you have any questions or need assistance with the upgrade, feel free to reach out to me on [GitHub](https://github.com/pashagolub/pgxmock).

Happy mocking!

Truly yours,
Pavlo Golub  
Author of **pgxmock** 💙💛