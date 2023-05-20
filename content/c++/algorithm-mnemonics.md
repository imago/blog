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
#include <memory>

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
                                   std::ostream_iterator<int>(std::cout));
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
    pos = std::remove_if(std::begin(rotate_list), std::end(rotate_list), [](const int &value)
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

    std::string my_copy_backwards{};
    my_copy_backwards.resize(my_copy.size());
    // use: mvb
    std::move_backward(std::begin(my_copy) + 2, std::end(my_copy), std::end(my_copy_backwards));
    printList(my_copy_backwards);

    // use: mov
    std::move(std::begin(lowercase_hello), std::end(lowercase_hello), std::begin(my_copy));
    printList(my_copy);

    // use: ita
    std::iota(std::begin(my_copy), std::end(my_copy), 'A');
    printList(my_copy);

    printList(rotate_list);
    // use: gnr
    std::generate(std::begin(rotate_list), std::end(rotate_list), [n = 3]() mutable
                  { return n--; });
    // using mutable the captured variable n is not a const anymore and writeable
    // also possible:
    // int n = 3
    // std::generate(std::begin(rotate_list), std::end(rotate_list), [&n]()
    //              { return n--; });
    // we do not really need an n out of generate here
    printList(rotate_list);

    //  use: gnn
    const int list_size = 7;
    rotate_list.resize(list_size);
    std::generate_n(std::begin(rotate_list), list_size, [n = 5]() mutable
                    { return n--; });
    printList(rotate_list);

    // use: fln
    std::fill_n(std::begin(rotate_list), std::abs(list_size - 3), 0);
    printList(rotate_list);

    // use: fil
    std::fill(std::begin(rotate_list), std::end(rotate_list) - 5, 9);
    printList(rotate_list);

    printList(my_copy);
    // use: cpy
    std::copy(std::begin(uppercase_hello) + 2, std::end(uppercase_hello) - 2, std::begin(my_copy));
    printList(my_copy);

    // use: cpn
    std::copy_n(std::begin(uppercase_hello), uppercase_hello.size() - 2, std::begin(my_copy));
    printList(uppercase_hello);
    printList(my_copy);

    // use: cpi
    std::copy_if(std::begin(uppercase_hello), std::end(uppercase_hello), std::begin(my_copy),
                 [](const char &input)
                 {
                     return std::islower(input);
                 });
    printList(my_copy);

    // use: cpb
    std::copy_backward(std::begin(uppercase_hello), std::end(uppercase_hello), std::end(my_copy));
    printList(my_copy);

    // use: upb
    std::vector<int> upper_bound_list = {3, 5, 7, 9, 1};
    std::sort(std::begin(upper_bound_list), std::end(upper_bound_list));
    printList(upper_bound_list);
    auto upper = std::upper_bound(std::begin(upper_bound_list), std::end(upper_bound_list), 4);

    if (upper != std::end(upper_bound_list))
    {
        const auto distance = std::distance(std::begin(upper_bound_list), upper);
        std::cout << "Upper Bound: " << *upper << " at " << distance << std::endl;
    }
    else
    {
        std::cout << "Upper Bound not found" << std::endl;
    }

    // use: lwb
    auto pos_lower_bound = std::lower_bound(std::begin(upper_bound_list), std::end(upper_bound_list), 2);
    if (pos_lower_bound != std::end(upper_bound_list))
    {
        const auto distance = std::distance(std::begin(upper_bound_list), pos_lower_bound);
        std::cout << "Lower Bound: " << *pos_lower_bound << " at " << distance << std::endl;
    }
    else
    {
        std::cout << "Lower Bound not found" << std::endl;
    }

    printList(rotate_list);
    sortVector(rotate_list);

    // use: ucp
    std::unique_copy(std::begin(rotate_list), std::end(rotate_list),
                     std::ostream_iterator<int>(std::cout));
    // if not sorted, duplicate entries possible with std::unique_copy
    std::cout << "\n";

    // use: stv
    std::vector<int> rotate_list_union{};
    rotate_list_union.resize(rotate_list.size());
    // use: ucp
    std::unique_copy(std::begin(rotate_list), std::end(rotate_list),
                     std::back_inserter(rotate_list_union));
    printList(rotate_list_union);

    // use: stu
    std::ignore = std::set_union(std::begin(rotate_list), std::end(rotate_list),
                                 std::begin(rotate_list_union), std::end(rotate_list_union), std::ostream_iterator<int>(std::cout));
    // use: sto
    std::cout << "\n";

    printList(uppercase_hello);
    // use: tfm
    std::transform(std::begin(uppercase_hello), std::end(uppercase_hello),
                   std::begin(my_copy), [](const char &input)
                   { return std::tolower(input); });
    printList(my_copy);
    // use: stn
    std::ignore = std::set_intersection(std::begin(uppercase_hello), std::end(uppercase_hello),
                                        std::begin(my_copy), std::end(my_copy), std::ostream_iterator<char>(std::cout));
    std::cout << "\n";

    // use: std
    std::ignore = std::set_difference(std::begin(uppercase_hello), std::end(uppercase_hello),
                                      std::begin(my_copy), std::end(my_copy), std::ostream_iterator<char>(std::cout));
    std::cout << "\n";

    int m;
    // use: ssd
    std::ignore = std::set_symmetric_difference(std::begin(uppercase_hello),
                                                std::end(uppercase_hello), std::begin(my_copy), std::end(my_copy), std::ostream_iterator<char>(std::cout));
    std::cout << "\n";

    printList(uppercase_hello);
    printList(my_copy);
    // use: mrg
    std::merge(std::begin(uppercase_hello), std::end(uppercase_hello),
               std::begin(my_copy), std::end(my_copy), std::ostream_iterator<char>(std::cout));
    std::cout << "\n";

    // use: stv
    std::vector<int> first = {3, 1, 7, 9, 5};
    std::vector<int> second = {2, 6, 4, 10, 8};
    sortVector(first);
    sortVector(second);
    std::vector<int> result(first.size() + second.size());
    // copy first and second part to result
    auto it = std::copy(std::begin(first), std::end(first), std::begin(result));
    std::ignore = std::copy(std::begin(second), std::end(second), it);

    // use: ipm
    std::inplace_merge(std::begin(result), it, std::end(result));
    printList(result);

    first = {1, 2, 3, 4};
    second = {3, 2, 4};
    sortVector(second);

    // use: inc
    if (std::includes(std::begin(first), std::end(first),
                      std::begin(second), std::end(second)))
    {
        // use: sto
        std::cout << "first includes second:" << std::endl;
    }
    else
    {
        // use: sto
        std::cout << "first does NOT include second:" << std::endl;
    }
    printList(first);
    printList(second);

    sortVector(rotate_list_union);
    printList(rotate_list_union);

    // use: eqr
    auto bounds = std::equal_range(std::begin(rotate_list_union), std::end(rotate_list_union), 0);
    std::cout << "Range of elements equal to 0: ";
    for (auto it = bounds.first; it != bounds.second; ++it)
    {
        std::cout << *it << " ";
    }
    std::cout << std::endl;

    // use: bns
    auto found = std::binary_search(std::begin(rotate_list_union), std::end(rotate_list_union), 1);
    if (found)
    {
        std::cout << "found!" << std::endl;
    }

    // use: ajf adjacent_find
    auto pos_adjacent_find = std::adjacent_find(std::begin(rotate_list_union), std::end(rotate_list_union));
    if (pos_adjacent_find != std::end(rotate_list_union))
    {
        std::cout << "equal pair found: " << *pos_adjacent_find << " " << *pos_adjacent_find++ << std::endl;
    }

    std::vector<int> all_same{1, 1, 1, 1, 1, 1, 1};
    printList(all_same);
    // use: alo all_of
    if (std::all_of(std::begin(all_same), std::end(all_same), [](const int &input)
                    { return input == 1; }))
    {
        std::cout << "all number are same" << std::endl;
    }

    all_same.push_back(2);
    // use: ano any_of
    if (std::any_of(std::begin(all_same), std::end(all_same), [](const int &input)
                    { return input > 1; }))
    {
        std::cout << "at least one element is > 1" << std::endl;
    }

    // use: cni count_if
    all_same.push_back(3);
    auto n = std::count_if(std::begin(all_same), std::end(all_same), [](const int &input)
                           { return input > 1; });
    std::cout << n << " elements are greate than 1" << std::endl;

    // use: cnt count
    n = std::count(std::begin(all_same), std::end(all_same), 1);
    std::cout << n << " elements are equal to 1" << std::endl;

    // use: stv
    std::vector<int> all_same_equal(all_same.size(), 0);
    // use: cpy
    std::copy(std::begin(all_same), std::end(all_same), std::begin(all_same_equal));
    all_same_equal.emplace_back(4);
    printList(all_same);
    printList(all_same_equal);
    // use: eql equal
    if (std::equal(std::begin(all_same_equal), std::end(all_same_equal), std::begin(all_same)))
    {
        std::cout << "all_same and all_same_equal have both equal content." << std::endl;
    }
    // Caution here:
    if (std::equal(std::begin(all_same), std::end(all_same), std::begin(all_same_equal)))
    {
        std::cout << "all_same and all_same_equal have both equal content until size of all_same although last element of all_same_equal is different." << std::endl;
    }

    // use: ffo find_first_of
    std::vector<int> find_me{1, 2, 3};
    auto pos_find_first_of = std::find_first_of(std::begin(all_same), std::end(all_same),
                                                std::begin(find_me), std::end(find_me));
    if (pos_find_first_of != std::end(all_same))
    {
        std::cout << "I found ";
        printList(find_me);
        std::cout << " within ";
        printList(all_same);
    }

    // use: fin find_if_not
    auto pos_find_if_not = std::find_if_not(std::begin(all_same), std::end(all_same), [](const int &input)
                                            { return input == 1; });
    if (pos_find_if_not != std::end(all_same))
    {
        auto distance = std::distance(std::begin(all_same), pos_find_if_not);
        std::cout << "found not equl 1 at distance: " << distance << std::endl;
    }

    // use: fnd find
    auto pos_find = std::find(std::begin(all_same), std::end(all_same), 3);
    if (pos_find != std::end(all_same))
    {
        auto distance = std::distance(std::begin(all_same), pos_find);
        std::cout << "found 3 at " << distance << std::endl;
    }

    // use: fne find_end
    find_me = {1, 1, 1};
    std::copy(std::begin(find_me), std::end(find_me), std::ostream_iterator<char>{std::cout, " <find_me> "});
    auto pos_find_end = std::find_end(std::begin(all_same), std::end(all_same),
                                      std::begin(find_me), std::end(find_me));
    if (pos_find_end != std::end(all_same))
    {
        auto distance = std::distance(std::begin(all_same), pos_find_end);
        std::cout << "last occurence of ";
        printList(find_me);
        std::cout << " at position " << distance << std::endl;
    }

    // use: fni find_if
    auto pos_find_if = std::find_if(std::begin(all_same), std::end(all_same), [](const int &input)
                                    { return input > 1; });
    if (pos_find_if != std::end(all_same))
    {
        auto distance = std::distance(std::begin(all_same), pos_find_if);
        std::cout << "Greater 1 found at position " << distance << std::endl;
    }

    // use: fre for_each
    std::for_each(std::begin(all_same), std::end(all_same), [](const int &input)
                  { std::cout << (input * input) << " "; });
    std::cout << std::endl;

    // use: ihp is_heap
    if (std::is_heap(std::begin(all_same), std::end(all_same)))
    {
        std::cout << "alls_same is in heap structure" << std::endl;
    }

    std::vector<int> heap = {11, 9, 8, 7, 6, 5, 4, 3, 2, 1};
    bool isHeap = std::is_heap(heap.begin(), heap.end());

    if (isHeap)
    {
        std::cout << "The container represents a valid heap." << std::endl;
    }

    // use: ihu is_heap_until
    heap.at(1) = 12;
    auto pos_is_heap_until = std::is_heap_until(std::begin(heap), std::end(heap));
    if (pos_is_heap_until != std::end(heap))
    {
        auto distance = std::distance(std::begin(heap), pos_is_heap_until);
        std::cout << "The container represents a valid heap until: " << distance << std::endl;
    }

    // use: ipr is_permutation
    // use: ipt is_partitioned
    // use: iss is_sorted
    // use: isu is_sorted_until
    // use: lxc lexigraphical_compare
    // use: mme minmax_element
    // use: mne min_element
    // use: msm mismatch
    // use: mxe max_element
    // use: nno none_of
    // use: ppt partition_point
    // use: srh search
    // use: srn search_n

    // use: mkh make_heap
    // use: nth nth_element
    // use: phh push_heap
    // use: pph pop_heap
    // use: psc partial_sort_copy
    // use: pst partial_sort
    // use: ptc partition_copy
    // use: ptn partition
    // use: spt stable_partition
    // use: srt sort
    // use: sth sort_heap
    // use: sts stable_sort

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
12612342
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
1 2 3 4 
3 2 1 0 
5 4 3 2 1 0 -1 
0 0 0 0 1 0 -1 
9 9 0 0 1 0 -1 
A B C D E F G H I J K L 
L L O   w o r l I J K L 
H E L L O   w o r l d ! 
H E L L O   w o r l K L 
w o r l d   w o r l K L 
H E L L O   w o r l d ! 
1 3 5 7 9 
Upper Bound: 5 at 2
Lower Bound: 3 at 1
9 9 0 0 1 0 -1 
-1019
0 0 0 0 0 0 0 -1 0 1 9 
-10000000-10199
H E L L O   w o r l d ! 
h e l l o   w o r l d ! 
world!
HELLO 
HELLO hello 
H E L L O   w o r l d ! 
h e l l o   w o r l d ! 
HELLO hello world!world!
1 2 3 4 5 6 7 8 9 10 
first includes second:
1 2 3 4 
2 3 4 
-1 0 0 0 0 0 0 0 0 1 9 
Range of elements equal to 0: 0 0 0 0 0 0 0 0 
found!
equal pair found: 0 0
1 1 1 1 1 1 1 
all number are same
at least one element is > 1
2 elements are greate than 1
7 elements are equal to 1
1 1 1 1 1 1 1 2 3 
1 1 1 1 1 1 1 2 3 4 
all_same and all_same_equal have both equal content until size of all_same although last element of all_same_equal is different.
I found 1 2 3 
 within 1 1 1 1 1 1 1 2 3 
found not equl 1 at distance: 7
found 3 at 8
 <find_me>  <find_me>  <find_me> last occurence of 1 1 1 
 at position 4
Greater 1 found at position 7
1 1 1 1 1 1 1 4 9 
The container represents a valid heap.
The container represents a valid heap until: 1
```