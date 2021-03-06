---
title: 关于 let 和 const
date: '2019-12-22'
spoiler: 那么我应该用哪个呢?
---

我在 [早先的文章](/what-is-javascript-made-of/) 中有这样一段内容:

> **`let` vs `const` vs `var`**：通常你使用的都是 `let`。如果你希望禁止再给这个变量赋值，你可以使用 `const` 。（一些仓库或者开发者对这个问题很保守，他们强迫你在只有一次赋值的情况下必须使用 `const` 来声明变量）。

这段话引发了巨大的争议，在 Twitter 和 Reddit 中出现了大量关于此话题的讨论。目前主流的观点（或者是提到最多的观点）是开发者应该 *尽可能的使用 `const`*，只有在必要的情况下才使用 `let`，并且可以通过 ESLint 的 [`prefer-const`](https://eslint.org/docs/rules/prefer-const) 的规则来做强制约束。
 
在本文中，我将简单的总结一下我所了解到的关于此话题的一些正反方观点，以及我本人对此的观点。 

## 为什么 `const 优先`

* **单种方式**: 每次纠结于使用 `let` 还是 `const` 都是烦人的事情。如果采用 "`const 优先`" 的规则就可以让你不再纠结这个问题，并且还可以通过一个 linter 来自动处理这个事情。
* **重新赋值可能会导致 BUG**: 在长函数中，变量重新赋值很容易被忽略。 这就可能引发 bug。 特别是在闭包中，`const` 能让你确信它的值不会发生改变。
* **学习突变**：JavaScript 新手通常都会误以为 `const` 等同于不可变（immutability）。然而，还是有人会说学习变量突变和赋值的区别是很重要的，强制优先使用 `const` 可以迫使你更早的去面对这种区别。
* **无意义的赋值**：有时候，有些赋值是毫无意义的。例如，在使用 React Hooks 时，你从 `userState` 之类的 Hook 得到的值更像一个参数。它们是单向传递的。在它们赋值时就看到错误可以帮助你更早的了解 React 的数据流。
* **性能提升**: 偶尔还有人声称 JavaScript 引擎能够更快处理 `const` 声明的变量，因为它知道这个变量不会被重新赋值。

## 为什么不 `const 优先`

* **缺少语义**：如果我们强制使用 `const` 的话，我们就没法标识那些*真正*不能被重新赋值的变量。
* **与不可变混淆**：在每个关于为何要优先使用 `const` 的讨论中，总是有人会把它与不可变搞混。这并不奇怪，因为赋值和突变使用的是相同的操作符 `=`。对此，这些人总是被告知他们应该“好好学习下这门语言”。然而，反方的观点是如果一个防止新手犯错的特性却让新手很困惑， 那它的作用就不大。不幸的是，它并不能防止横跨多个模块并且会影响到所有人的突变错误。
* **避免重新声明的压力**：采用 `const` 优先的仓库会迫使你在条件赋值时不使用 `let`。例如，你会写 `const a = cond ? b : c` 而不是 `if` 的形式，即使 `b` 和 `c` 的内部逻辑很复杂，并且也不好太给它们命名。
* **重新赋值可能并不会导致 BUG**：重新赋值会引发 bug 通常是在这三种情况下：当作用域非常大时（如模块级别的作用域或是大型函数）；当值是一个参数时（这样它和会传入时的值不一致）；或者是变量被用在嵌套函数时。然而，在大多数代码仓库中变量都不符合上述情况，并且参数也无法声明成常量。
* **没有性能提升**：据我了解，引擎早就知道哪些变量只被赋值一次，即便你是使用 `var` 或 `let` 声明的。如果我们继续推测，我们还能推测出额外的检查会“产生”性能消耗，而不是降低它。但实际上，引擎是很智能的。

## 我的结论

我并不关心这个问题。

我更倾向于直接遵循各个仓库中的已有的惯例。

如果你在意这个问题，你可以借助 lint 工具来自动的检测和修复这个问题，这样就不必等到代码审查的阶段再来做 `let` 到 `const` 的修改。

最后，要记住 lint 工具存在的意义就是服务 *你*。如果一个 lint 规则困扰了你和你的团队，那么直接删掉它吧。它可能不值得你花时间去纠结。从自己的错误中去学习与成长。