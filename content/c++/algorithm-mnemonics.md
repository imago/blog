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
#include <numeric>
#include <optional>
#include <any>
#include <tuple>

void sortVector(std::vector<int> &unsorted_list)
{
    // use: srt
    std::sort(unsorted_list.begin(), unsorted_list.end());
}
void printList(const std::vector<int> &input, const char *delimiter = " ")
{
    // use: oit
    std::copy(std::begin(input), std::end(input), std::ostream_iterator<int>{std::cout, delimiter});
    // use: sto
    std::cout << std::endl;
}
void printList(const std::string &input, const char *delimiter = " ")
{
    // use: oit
    std::copy(std::begin(input), std::end(input), std::ostream_iterator<char>{std::cout, delimiter});
    // use: sto
    std::cout << std::endl;
}

int main()
{
    std::string greet{"Hello world!"};
    // use: sto
    std::cout << greet << std::endl;

    // use: stv
    std::vector<int> my_list{1, 5, 6, 3};
    sortVector(my_list);
    for (int item : my_list)
    {
        // use: sto
        std::cout << item << ", ";
    }
    // use: sto
    std::cout << std::endl;

    // use: oit
    std::copy(std::begin(my_list), std::end(my_list), std::ostream_iterator<int>{std::cout, " "});

    greet.erase(0, greet.find_first_not_of("Hello \t\n\r"));

    // use: lwr
    std::transform(std::begin(greet), std::end(greet), std::begin(greet), [](char c)
                   { return std::tolower(c); });
    std::cout << std::endl;

    // use: lwr -> another example
    std::transform(std::begin(my_list), std::end(my_list), std::begin(my_list), [](int number)
                   { return number * 2; });

    for (int item : my_list)
    {
        // use: sto
        std::cout << item << ", ";
    }
    // use: sto
    std::cout << std::endl;

    // use: erm
    my_list.erase(std::remove(std::begin(my_list), std::end(my_list), 10), std::end(my_list));

    std::cout << "After remove:" << std::endl;
    for (int item : my_list)
    {
        // use: sto
        std::cout << item << ", ";
    }
    // use: sto
    std::cout << std::endl;

    printList(my_list, " <-> ");

    // use: acm
    auto sum = std::accumulate(std::begin(my_list), std::end(my_list), 0);
    // use: acl
    auto product = std::accumulate(std::begin(my_list), std::end(my_list), 1, std::multiplies<int>());

    // use: sto
    std::cout << "sum: " << sum << ", product: " << product << std::endl;

    // use: stv
    std::vector<int> new_list = std::move(my_list);
    // move content of my_list to new_list without copy of elements
    // my_list not needed anymore
    my_list.clear();

    printList(new_list, " -> ");

    // example for optional usage
    std::optional<int> maybe{std::nullopt};

    std::cout << maybe.has_value() << std::endl;
    try
    {
        std::cout << maybe.value() << std::endl;
    }
    catch (const std::exception &e)
    {
        std::cout << "Exception caught: " << e.what() << std::endl;
    }
    std::cout << maybe.value_or(-1) << std::endl;

    maybe = 5;

    std::cout << maybe.has_value() << std::endl;
    std::cout << maybe.value() << std::endl;

    std::any myany = 7;
    std::cout << myany.type().name() << ": " << myany.has_value() << " -> " << std::any_cast<int>(myany) << std::endl;
    try
    {
        std::any_cast<double>(myany);
    }
    catch (const std::bad_any_cast &e)
    {
        std::cerr << e.what() << '\n';
    }
    myany = true;
    std::cout << myany.type().name() << ": " << myany.has_value() << " -> " << std::any_cast<bool>(myany) << std::endl;
    if (myany.type().name())
    {
        std::cout << "myany is boolean.\n";
    }
    myany.reset();
    if (!myany.has_value())
    {
        std::cout << "myany has no value!\n";
    }

    // will be deprecated; using std::random -> random generator available as third parameter
    // but "harmfull" if used
    // use: shf
    std::random_shuffle(std::begin(new_list), std::end(new_list));
    printList(new_list, " <shuffled> ");

    // use: rvr
    std::reverse(std::begin(new_list), std::end(new_list));
    printList(new_list, " <reverse> ");

    // use: stv
    std::vector<int> copyList(new_list.size());
    // use: rvc
    std::reverse_copy(std::begin(new_list), std::end(new_list), std::begin(copyList));
    printList(copyList, " <copyList> ");
    copyList.insert(std::begin(copyList), {1, 2, 3, 4});
    printList(copyList, " <rotate> ");

    // use: rte
    std::rotate(std::begin(copyList), std::begin(copyList) + 2, std::end(copyList));
    printList(copyList, " <rotate> ");

    // use: stv
    std::vector<int> rotate_list(copyList.size());
    const auto pivot = std::find(std::begin(copyList), std::end(copyList), 4);
    // use: rtc
    std::ignore = std::rotate_copy(std::begin(copyList), pivot, std::end(copyList),
                                   std::begin(rotate_list));
    printList(rotate_list, " <rotate_copy> ");

    // use: rtc
    std::ignore = std::rotate_copy(std::begin(rotate_list), std::begin(rotate_list) + 2, std::end(rotate_list),
                                   std::ostream_iterator<int>(std::cout, " "));
    // use: sto
    std::cout << std::endl;
    printList(rotate_list);

    // use: sto
    std::cout << "Hello Flush!\n"
              << std::flush;

    // lambda function
    auto compare = [rotate_list](const char &a, const char &b)
    {
        std::ignore = rotate_list.size(); // reading in as const by value
        return a < b;
    };

    std::string s = "abc";
    // use: srt
    std::sort(s.begin(), s.end(), compare);
    // coult also use for compare: std::greater<char>()

    do
        std::cout << s << ' ';
    // use: nxp
    while (std::next_permutation(s.begin(), s.end()));
    // use: sto
    std::cout << '\n';

    // use: sto
    std::cout << "current rotate_list\n";
    printList(rotate_list);
    // use: srt
    std::sort(std::begin(rotate_list), std::end(rotate_list));
    // use: sto
    std::cout << "current rotate_list after sorting\n";
    printList(rotate_list);

    // use: uqe
    const auto last_elem = std::unique(std::begin(rotate_list), std::end(rotate_list));
    // use: sto
    std::cout << "size: " << rotate_list.size() << std::endl;
    rotate_list.erase(last_elem, rotate_list.end());
    // use: sto
    std::cout << "size: " << rotate_list.size() << std::endl;
    // use: sto
    std::cout << "current rotate_list after unique\n";
    printList(rotate_list);

    if (!rotate_list.empty())
    {
        std::cout << "value at beginning: " << *std::begin(rotate_list) << " <> " << rotate_list.at(0) << std::endl;
    }

    const int remove_value = 6;
    // use: rmv
    auto pos = std::remove(std::begin(rotate_list), std::end(rotate_list), remove_value);
    rotate_list.erase(pos, rotate_list.end());
    if (pos == std::end(rotate_list))
    {
        // use: sto
        std::cout << remove_value << " was removed!\n";
    }
    printList(rotate_list, " <remove> ");

    // use: rmi
    pos = std::remove_if(std::begin(rotate_list), std::end(rotate_list), [remove_value](const int &value)
                         { return value > remove_value; });
    rotate_list.erase(pos, rotate_list.end());
    if (pos == std::end(rotate_list))
    {
        // use: sto
        std::cout << "Everything > " << remove_value << " was removed!\n";
    }
    printList(rotate_list, " <remove_if> ");

    std::string remove_copy_var = "Hello Merhaba World!";
    // std::string::npos <- used for "no position" or "not found"
    if (remove_copy_var.find_first_not_of("Merhaba") != std::string::npos)
    {
        std::cout << "Merhaba found in string!\n";
    }

    // use: rmf
    const auto pos_copy_if = std::remove_copy_if(std::begin(remove_copy_var), std::end(remove_copy_var),
                                                 std::begin(remove_copy_var), [](const char &input)
                                                 { return input == 'a'; });
    remove_copy_var.erase(pos_copy_if, remove_copy_var.end());
    printList(remove_copy_var, "-");

    std::string new_hello;
    // use: rmc
    std::remove_copy(std::begin(remove_copy_var), std::end(remove_copy_var),
                     std::back_inserter(new_hello), 'H');

    // use: sto
    std::cout << remove_copy_var.size() << std::endl;
    // use: sto
    std::cout << new_hello.size() << std::endl;
    printList(new_hello, "-");

    // use: tfm
    std::transform(std::begin(new_hello), std::end(new_hello),
                   std::begin(new_hello), [](const char &input)
                   { return std::toupper(input); });
    printList(new_hello, " <transform> ");

    // containers must have equal ranges
    std::string lowercase_hello = "hello world!";
    std::string uppercase_hello{};
    // use: tfm
    std::transform(std::begin(lowercase_hello), std::end(lowercase_hello), std::back_inserter(uppercase_hello), [](const char &input)
                   { return std::toupper(input); });
    printList(uppercase_hello, "");
    printList(lowercase_hello, "");

    // use: sto
    std::cout << lowercase_hello.size() << "\n";
    // use: sto
    std::cout << uppercase_hello.size() << "\n";
    // use: swr
    std::swap_ranges(std::begin(lowercase_hello), std::begin(lowercase_hello) + 5, std::begin(uppercase_hello));
    printList(lowercase_hello, "");
    printList(uppercase_hello, "");

    // use: swp
    std::swap(lowercase_hello, uppercase_hello);
    printList(uppercase_hello, " <now uppercase> ");

    // use: rpl
    std::replace(std::begin(lowercase_hello), std::end(lowercase_hello), 'l', 'X');
    printList(lowercase_hello, " <replace> ");

    // use: tfm
    std::transform(
        std::begin(lowercase_hello), std::end(lowercase_hello), std::begin(lowercase_hello), [](const char &input)
        { return (std::islower(input) ? std::toupper(input) : input); });
    printList(lowercase_hello, " <all upper> ");

    // use: rpi
    std::replace_if(
        std::begin(lowercase_hello), std::end(lowercase_hello), [](const char &input)
        { return std::islower(input); },
        'X');
    printList(lowercase_hello);

    std::string my_copy(lowercase_hello.size(), ' ');
    // use: rpc
    std::replace_copy(std::begin(lowercase_hello), std::end(lowercase_hello), std::begin(my_copy), 'X', 'y');
    printList(my_copy);

    // use: rci
    std::replace_copy_if(
        std::begin(lowercase_hello), std::end(lowercase_hello),
        std::begin(my_copy), [](const char &input)
        { return input != 'X'; },
        'Z');
    printList(my_copy);

    std::string my_copy_backwards(my_copy.size(), ' ');
    // use: mvb
    std::move_backward(std::begin(my_copy) + 2, std::end(my_copy), std::end(my_copy_backwards));
    printList(my_copy_backwards);

    // use: mov
    std::move(std::begin(lowercase_hello), std::end(lowercase_hello), std::begin(my_copy));
    printList(my_copy);

    // use: ita
    std::iota(std::begin(my_copy), std::end(my_copy), 'A');
    printList(my_copy);

    return 0;
}
```

Output
```txt
Hello world!
1, 3, 5, 6, 
1 3 5 6 
2, 6, 10, 12, 
After remove:
2, 6, 12, 
2 <-> 6 <-> 12 <-> 
sum: 20, product: 144
2 -> 6 -> 12 -> 
0
Exception caught: bad optional access
-1
1
5
i: 1 -> 7
bad any_cast
b: 1 -> 1
myany is boolean.
myany has no value!
2 <shuffled> 12 <shuffled> 6 <shuffled> 
6 <reverse> 12 <reverse> 2 <reverse> 
2 <copyList> 12 <copyList> 6 <copyList> 
1 <rotate> 2 <rotate> 3 <rotate> 4 <rotate> 2 <rotate> 12 <rotate> 6 <rotate> 
3 <rotate> 4 <rotate> 2 <rotate> 12 <rotate> 6 <rotate> 1 <rotate> 2 <rotate> 
4 <rotate_copy> 2 <rotate_copy> 12 <rotate_copy> 6 <rotate_copy> 1 <rotate_copy> 2 <rotate_copy> 3 <rotate_copy> 
12 6 1 2 3 4 2 
4 2 12 6 1 2 3 
Hello Flush!
abc acb bac bca cab cba 
current rotate_list
4 2 12 6 1 2 3 
current rotate_list after sorting
1 2 2 3 4 6 12 
size: 7
size: 6
current rotate_list after unique
1 2 3 4 6 12 
value at beginning: 1 <> 1
6 was removed!
1 <remove> 2 <remove> 3 <remove> 4 <remove> 12 <remove> 
Everything > 6 was removed!
1 <remove_if> 2 <remove_if> 3 <remove_if> 4 <remove_if> 
Merhaba found in string!
H-e-l-l-o- -M-e-r-h-b- -W-o-r-l-d-!-
18
17
e-l-l-o- -M-e-r-h-b- -W-o-r-l-d-!-
E <transform> L <transform> L <transform> O <transform>   <transform> M <transform> E <transform> R <transform> H <transform> B <transform>   <transform> W <transform> O <transform> R <transform> L <transform> D <transform> ! <transform> 
HELLO WORLD!
hello world!
12
12
HELLO world!
hello WORLD!
H <now uppercase> E <now uppercase> L <now uppercase> L <now uppercase> O <now uppercase>   <now uppercase> w <now uppercase> o <now uppercase> r <now uppercase> l <now uppercase> d <now uppercase> ! <now uppercase> 
h <replace> e <replace> X <replace> X <replace> o <replace>   <replace> W <replace> O <replace> R <replace> L <replace> D <replace> ! <replace> 
H <all upper> E <all upper> X <all upper> X <all upper> O <all upper>   <all upper> W <all upper> O <all upper> R <all upper> L <all upper> D <all upper> ! <all upper> 
H E X X O   W O R L D ! 
H E y y O   W O R L D ! 
Z Z X X Z Z Z Z Z Z Z Z 
    X X Z Z Z Z Z Z Z Z 
H E X X O   W O R L D ! 
A B C D E F G H I J K L 
```