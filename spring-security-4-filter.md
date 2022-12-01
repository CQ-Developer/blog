# Customized Filter

本章基于 **spring boot 2.7.5** 讨论如何自定义安全过滤器。

一般情况，基于安全的目的在 spring security 中自定义 `Filter` 是完全没有必要的，因为 spring security 提供的众多 `Filter` 已经非常全面了。

但当 spring security 提供的 `Filter` 不能满足需求，或者说不适用于一些场景的情况下，自定义 `Filter` 可以帮助我们更方便的解决问题。

`HttpSecurity` 提供了在 `SecurityFilterChain` 中添加自定义 `Filter` 的能力，其中包含4个方法。

- addFilter(Filter)

- addFilterAt(Filter, Class<? extends Filter>)

- addFilterBefore(Filter, Class<? extends Filter>)

- addFilterAfter(Filter, Class<? extends Filter>)

## addFilter(Filter)

`addFilter` 是一个比较特殊的方法，它接收的参数必须是 spring security 现有 `Filter` 的一个子类，可以通过该方法的 *Javadoc* 描述查看具体有哪些 `Filter` 允许继承。

当添加了指定 `Filter` 的子类实现时，会将原有的 `Filter` 替换成新添加的 `Filter`，但是其在 `SecurityFilterChain` 中的顺序不会发生变化。

这个方法的主要职责是提供一个扩展原有过滤器功能的入口，如果确实有需要扩展原有 `Filter` 的功能，可以考虑使用这个方法。

不推荐使用这个方法，因为该方法要求必须继承现有 `Filter`，且必须对现有的 `Filter` 的执行流程和实现逻辑非常清晰。如果需要实现的功能不是必须修改默认的 `Filter`，那么其他3个方法永远是更优的选择。

## addFilterAt(Filter, Class<? extends Filter>)

`addFilterAt` 方法可以在指定的 `Filter` 的位置上添加一个 Filter，但有两点需要注意：

1. 添加的 `Filter` 不会覆盖指定的 `Filter`。

2. 如果在一个位置上添加很多 `Filter`，不能保证它们的顺序。

## addFilterBefore(Filter, Class<? extends Filter>)

addFilterBefore 方法可以在指定的 Filter 之前添加一个 Filter。

## addFilterAfter(Filter, Class<? extends Filter>)

`addFilterAfter` 方法可以在指定的 `Filter` 之后添加一个 `Filter`。

# CsrfFilter

举一个具体的例子。

spring security 默认提供了 `CsrfFilter` 为应用程序提供了 *CSRF* 保护。
