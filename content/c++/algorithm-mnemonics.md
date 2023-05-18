---
title: "Algorithm Mnemonics"
date: 2023-05-18T19:01:52+02:00
draft: false
---

Today I want to introduce a visual studio plugin I recently found:
* [C++ Algorithm Mnemonics](https://github.com/tommybennett/algorithm-mnemonics)

It has shortcuts to use many STL algorithms. A list with current shortcuts
is [available](https://github.com/tommybennett/algorithm-mnemonics/blob/master/STLInstructionSet.md).

Here, I will guide you with some examples:



```c++

#include <iostream>
#include <vector>
#include <algorithm>
#include <iterator>

void sortVector(std::vector<int> &unsorted_list)
{
    // use: srt
    std::sort(unsorted_list.begin(), unsorted_list.end());
}

int main()
{
    std::string greet{"Hello world!"};
    std::cout << greet << std::endl;

    // use: stv
    std::vector<int> my_list{1, 5, 6, 3};
    sortVector(my_list);
    for (int item : my_list)
    {
        // use: sto
        std::cout << item << ", ";
    }
    std::cout << std::endl;

    // use: oit
    std::copy(std::begin(my_list), std::end(my_list), std::ostream_iterator<int>{std::cout, " "});

    greet.erase(0, greet.find_first_not_of("Hello \t\n\r"));

    // use: lwr
    std::transform(std::begin(greet), std::end(greet), std::begin(greet), [](char c)
                   { return std::tolower(c); });
    std::cout << std::endl;

    // use: lwr -> another example with int
    std::transform(std::begin(my_list), std::end(my_list), std::begin(my_list), [](int number)
                   { return number * 2; });

    for (int item : my_list)
    {
        // use: sto
        std::cout << item << ", ";
    }
    std::cout << std::endl;

    // use: erm
    my_list.erase(std::remove(std::begin(my_list), std::end(my_list), 10), std::end(my_list));

    std::cout << "After remove:" << std::endl;
    for (int item : my_list)
    {
        std::cout << item << ", ";
    }
    std::cout << std::endl;

    return 0;
}
```

This results into following output
```txt
Hello world!
1, 3, 5, 6, 
1 3 5 6 
2, 6, 10, 12, 
After remove:
2, 6, 12,
```