---
title: "C++ Core Guidelines Checker Setup"
date: 2023-05-19T12:38:05+02:00
draft: false
---

The CPP Core guidelines are commonly used in modern C++ development. Here, I will show you how you can setup your development environment to check you code against the guidelines using *clang-tidy*.

# Install dependencies

```bash
sudo apt-get update
sudo apt-get install clang clang-tidy

clang --version
clang-tidy --version
```

# Create a simple application

main.cpp
```c++
#include <iostream>
#include <iterator>
#include <vector>

void printList(const std::vector<int> &input, const char *delimiter = " ")
{
    std::copy(std::begin(input), std::end(input), std::ostream_iterator<int>{std::cout, delimiter});
}

int main()
{
    std::vector<int> my_list{1, 2, 3, 4};
    printList(my_list);
    return 0;
}

```


# Compile your executable and run check

Checking CPP core guidelines:
```bash
clang-tidy -checks='cppcoreguidelines-*' main.cpp -- -std=c++17
```

Using all checkers (see [list](https://clang.llvm.org/extra/clang-tidy/checks/list.html)):

```bash
clang-tidy -checks='*' main.cpp -- -std=c++17
```

You will get the warning for all checkers:

```c++
/workspaces/playground/main.cpp:5:47: warning: declaring a parameter with a default argument is disallowed [fuchsia-default-arguments]
void printList(const std::vector<int> &input, const char *delimiter = " ")
                                              ^                    ~~~~~~~
/workspaces/playground/main.cpp:12:22: warning: calling a function that uses a default argument is disallowed [fuchsia-default-arguments]
    std::vector<int> my_list{1, 2, 3, 4};
                     ^
/usr/lib/gcc/aarch64-linux-gnu/7.5.0/../../../../include/c++/7.5.0/bits/stl_vector.h:384:7: note: default parameter was declared here
             const allocator_type& __a = allocator_type())
             ^
/workspaces/playground/main.cpp:13:5: warning: calling a function that uses a default argument is disallowed [fuchsia-default-arguments]
    printList(my_list);
    ^
/workspaces/playground/main.cpp:5:47: note: default parameter was declared here
void printList(const std::vector<int> &input, const char *delimiter = " ")
```