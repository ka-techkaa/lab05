[![Coverage Status](https://coveralls.io/repos/github/Bu5y5leeper/lab05/badge.svg?branch=master)](https://coveralls.io/github/Bu5y5leeper/lab05?branch=master)

## Laboratory work V

Данная лабораторная работа посвещена изучению фреймворков для тестирования на примере **GTest**

## Tutorial

```sh
$ mkdir third-party
$ git submodule add https://github.com/google/googletest third-party/gtest
$ cd third-party/gtest && git checkout release-1.12.1 && cd ../..
$ git add third-party/gtest
$ git commit -m"added gtest framework"
```

```sh
mkdir tests
touch test_banking.cpp
```
---
Содержимое файла ./tests/test_banking.cpp
```sh
#include <Account.h>
#include <Transaction.h>
#include <gtest/gtest.h>

TEST(Banking, Account) {
  Account some_bal(0,0);
  
  EXPECT_EQ(some_bal.GetBalance(), 0);
  
  EXPECT_THROW(test.ChangeBalance(123), std::runtime_error);
  
  some_bal.Lock();
  
  some_bal.ChangeBalance(123);
  EXPECT_EQ(some_bal.GetBalance(), 123);
  
  EXPECT_THROW(some_bal.Lock(), std::runtime_error);
}

TEST(Banking, Transaction) {
  int some_money = 5000, some_more_money = 0, fee = 100;
  
  Account rich(0,some_money), poor(1,some_more_money);
  Transaction transaction;

  EXPECT_EQ(transaction.fee(), 1);

  transaction.set_fee(fee);

  EXPECT_EQ(transaction.fee(), fee);

  EXPECT_THROW(transaction.Make(rich, rich, 1), std::logic_error);
  EXPECT_THROW(transaction.Make(rich, poor, -1), std::invalid_argument);
  EXPECT_THROW(transaction.Make(rich, poor, 99), std::logic_error);

  EXPECT_EQ(transaction.Make(rich, poor, 150), false);

  EXPECT_EQ(transaction.Make(rich, poor, 1000), true);
  EXPECT_EQ(poor.GetBalance(), 1000); 
  EXPECT_EQ(rich.GetBalance(), 3900);

  EXPECT_EQ(transaction.Make(rich, poor, 3900), false);
  
  EXPECT_EQ(poor.GetBalance(), 1000); 
  EXPECT_EQ(rich.GetBalance(), 3900);
}
```
---
Содержимое файла CMakeLists.txt
```sh
option(BUILD_TESTS "Build tests" OFF)

if(BUILD_TESTS)                   
  add_compile_options(--coverage) 
endif()

project (banking)

add_library(banking STATIC ${CMAKE_CURRENT_SOURCE_DIR}/banking/Transaction.cpp ${CMAKE_CURRENT_SOURCE_DIR}/banking/Account.cpp)
target_include_directories(banking PUBLIC
${CMAKE_CURRENT_SOURCE_DIR}/banking )

target_link_libraries(banking gcov)

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB BANKING_TEST_SOURCES tests/*.cpp)
  add_executable(check ${BANKING_TEST_SOURCES})
  target_link_libraries(check banking gtest_main)
  add_test(NAME check COMMAND check)
endif()
```

```
Copyright (c) 2015-2021 The ISC Authors
```
