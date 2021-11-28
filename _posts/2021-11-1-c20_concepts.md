---
layout: post
title: "C++20 Concepts"
description: ""
comments: true
keywords: ""
---

https://www.youtube.com/watch?v=qawSiMIXtE4
https://godbolt.org/z/1c7hEWv8v

```C++

#include <concepts>
#include <iostream>
#include <tuple>
#include <type_traits>
#include <boost/hana.hpp>
using namespace boost::hana::literals;
namespace hana = boost::hana;
using namespace std;

template<typename T>
concept SizeConstant = std::convertible_to<T, std::size_t> && requires (T t) {
    { T::value } -> std::convertible_to<std::size_t>;
    std::integral_constant<std::size_t, (std::size_t)T{}>{};
};

template<typename T, template<typename ... > typename Primary>
struct is_specialization_of: std::false_type {};

template<template<typename ...> typename Primary, typename ... Args>
struct is_specialization_of<Primary<Args...>, Primary>: std::true_type {};

template<typename T, template<typename...> typename Primary>
inline constexpr bool is_specialization_of_v = is_specialization_of<T, Primary>::value;

template<typename T>
concept Node = std::is_object_v<T>;

template<typename T>
// concept Tree = true;
concept Tree = requires (T t) {
    { t.root } -> Node;
    { T::childCount } -> std::convertible_to<std::size_t>;
    requires T::childCount == 0ull || requires {
        t.template child<0>();
    };
};

template<typename T>
concept TreeRef = std::is_reference_v<T> && Tree<std::remove_reference_t<T>>;


template<typename T>
concept TreeLocation = requires (T t, const T ct) {
    { t.isRoot } -> std::convertible_to<bool>;
    t.indices;
    ct.tail();
    requires !T::isRoot || requires {
        { ct.head() } -> std::convertible_to<std::size_t>;
        ct.ofParent();
    };
};

template<std::size_t... indices_>
struct tree_location {
    static constexpr const bool
        isRoot = sizeof...(indices_) == 0;

    std::tuple<std::integral_constant<std::size_t, indices_>...>
        indices;

    constexpr tree_location() { }

    constexpr tree_location(hana::tuple<hana::size_t<indices_>...>) { }

    auto ofParent() const {
        return ::tree_location{hana::drop_back(hana::tuple<hana::size_t<indices_>...>{}, 1_c)};
    }

    auto tail() const {
        return ::tree_location{hana::drop_front(hana::tuple<hana::size_t<indices_>...>{}, 1_c)};
    }

    constexpr std::size_t head() const {
        return std::get<0>(indices);
    }

    auto ofChild(SizeConstant auto index) const {
        return tree_location<indices_..., index>{};
    }
};

template<Node Root_, Tree... Children_>
struct tree{
    Root_ root;
    std::tuple<Children_...> children;
    static constexpr  SizeConstant  auto childCount = hana::size_c<sizeof...(Children_)>;
    tree() = default;

    //tree(Root_ root): root{root}{}; // two extra copies

    // template<typename RootArg_>
    // tree(RootArg_&& root) : root{std::forward<RootArg_>(root)}{}

    // tree(auto&& root): root{std::forward<decltype(root)>(root)} {}

    tree(std::convertible_to<Root_> auto&& root): root{std::forward<decltype(root)>(root)} {}

    Tree auto& subtree(TreeLocation auto location) {
        if constexpr (location.isRoot) {
            return *this;
        } else {
            return child<location.head()>().subtree(location.tail());
        }
    }


    template<std::size_t index_>
        requires (index_ < sizeof...(Children_))
    Tree auto &child(){
        return std::get<index_>(children);
    }
};

static_assert(Tree<tree<int>>);
static_assert(Tree<tree<int, tree<int>>>);

template<typename T>
concept Message = std::is_object_v<T>;

template<typename T>
concept Context = requires (T t) {
    { t.tree } -> TreeRef;
    { t.location } -> TreeLocation;
};

template<Tree Tree_, TreeLocation TreeLocation_ = tree_location<>>
struct context{
    Tree_& tree;
    static constexpr const TreeLocation_ location;

    // context(Tree_& tree) : tree{ tree }{
    //     static_assert(Context<context>);
    // }

    context(Tree_ &tree, TreeLocation_ location = TreeLocation_{}): tree{ tree } {
        static_assert(Context<context>);
    }

    void sendDown(Message auto message){
        Tree auto &subtree = tree.subtree(location);
        subtree.childCount.times.with_index(
            [&] (SizeConstant auto index) {
                Node auto &child = subtree.template child<index>().root;
                Context auto childContext = ::context(tree, location.ofChild(index));
                if constexpr (requires { child.handle(message, childContext); }) {
                    child.handle(message, childContext);
                } else if constexpr (requires { child.handle(message); }) {
                    child.handle(message);
                    childContext.sendDown(message);
                } else {
                    childContext.sendDown(message);
                }
            }
        );
    }

    void sendUp(Message auto message){
        if constexpr (!location.isRoot){
            Node auto& parent = tree.subtree(location.ofParent()).root;
            Context auto parentContext = ::context{tree, location.ofParent()};
            if constexpr (requires {parent.handle(message, parentContext);} ){
                parent.handle(message, parentContext);
            }
            else if constexpr (requires {parent.handle(message); }){
                parent.handle(message);
                parentContext.sendUp(message);
            } else{
                parentContext.sendUp(message);
            }
        }
    }

};


struct start { };

struct tick { };

struct tock { int data; };

struct idle {
    void handle(Message auto &message) {
        puts(".");
    }

    void handle(Message auto &message)
        requires (sizeof(message) > 1) {
        puts("!");
    }
};

struct tick_tock {
    void handle(const start& message, Context auto context) {
        puts("tick: ");
        context.sendDown(tick{});
    }

    void handle(const tock& message) {
        puts("tock!");
    }
};

struct responder {
    void handle(const tick& message, Context auto context) {
        context.sendUp(tock{});
    }
};

int main() {
    tree<idle,
         tree<tick_tock,
              tree<idle,
                   tree<responder>,
                   tree<idle,
                        tree<responder>>>>>
        tr;


    context(tr).sendDown(start{});
}

```$$$$