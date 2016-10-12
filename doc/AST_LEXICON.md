# AST Lexicon

- [Literals](#literals)
	- [String](#string)
	- [Symbol](#symbol)
	- [Execute-string](#execute-string)
	- [Regexp](#regexp)
	- [Array](#array)
	- [Hash](#hash)
		- [Pair](#pair)
	- [Range](#range)
- [Access](#access)
	- [Global Variable](#global-variable)
	- [Constant](#constant)
- [Assignment](#assignment)
	- [To Constant](#to-constant)
	- [Multiple Assignment](#multiple-assignment)
	- [Binary Operator-Assignment](#binary-operator-assignment)
	- [Logical Operator-Assignment](#logical-operator-assignment)
- [Class and Module Definition](#class-and-module-definition)
- [Method (Un)Definition](#method-undefinition)
- [Aliasing](#aliasing)
- [Formal Arguments](#formal-arguments)
	- [Objective-C Arguments](#objective-c-arguments)
- [Send](#send)
- [Control Flow](#control-flow)
	- [Logical Operators](#logical-operators)
	- [Branching](#branching)
	- [Case Matching](#case-matching)
		- [`case`-Expression Clause](#case-expression-clause)
	- [Looping](#looping)
	- [Exception Handling](#exception-handling)
		- [`rescue` Statement](#rescue-statement)
- [Miscellanea](#miscellanea)

## Literals

<h5 align="center">Singletons</h5> ||
---------------- | ---------------------
`true`           | (true)
`false`          | (false)
`nil`            | (nil)
<h5 align="center">Integer</h5>    |
`123`            | (int 123)
`-123`           | (int -123)
`__LINE__`       | (int 1)
<h5 align="center">Float</h5>      |
`1.0`            | (float 1.0)
`-1.0`           | (float -1.0)
<h5 align="center">Complex</h5>    |
`1i`             | (complex (0+1i))
`1ri`            | (complex (0+(1/1)*i))
<h5 align="center">Rational</h5>   |
`2.0r`           | (rational (2/1))

### String

<h5 align="center">Plain</h5>              ||
------------------------ | -----------------------------------------
`'foo'`                  | (str "foo")
`__FILE__`               | (string "foo.rb")
<h5 align="center">With Interpolation</h5> |
`"foo#{bar}baz"`         | (dstr (str "foo") (lvar bar) (str "baz"))
<h5 align="center">Here Document</h5>      |
`<<HERE␊foo␊bar␊HERE`    | (str "foo\nbar\n")

### Symbol

<h5 align="center">Plain</h5>              ||
------------------------ | -----------------------------------------
`:foo`                   | (sym :foo)
`:'foo'`                 | (sym :foo)
<h5 align="center">With Interpolation</h5> |
`:"foo#{bar}baz"`        | (dsym (str "foo") (lvar bar) (str "baz"))

### Execute-string

<h5 align="center">Plain</h5>             ||
----------------------- | -----------------------------
`foo#{bar}`             | (xstr (str "foo") (lvar bar))
<h5 align="center">Here Document</h5>     |
`<<`HERE`␊foo␊bar␊HERE` | (xstr (str "foo\nbar\n"))

### Regexp

<h5 align="center">Options</h5>  ||
-------------- | --------------------------------------------
`im`           | (regopt :i :m)
<h5 align="center">Plain</h5>    |
`/foo#{bar}/i` | (regexp (str "foo") (lvar :bar) (regopt :i))

### Array

<h5 align="center">Plain</h5>              ||
------------------------ | -------------------------------------------
`[1, 2]`                 | (array (int 1) (int 2))
<h5 align="center">Splat</h5>              |
`*foo`                   | (splat (lvar :foo))
<h5 align="center">With Interpolation</h5> |
`[1, *foo, 2]`           | (array (int 1) (splat (lvar :foo)) (int 2))

> _Splat_: Can also be used in argument lists, e.g. `foo(bar, *baz)`

### Hash

#### Pair

<h5 align="center">With Hashrocket</h5>          ||
------------------------------ | ------------------------------------------------------
`1 => 2`                       | (pair (int 1) (int 2))
<h5 align="center">With Label (1.9)</h5>         |
`answer: 42`                   | (pair (sym :answer) (int 42))
<h5 align="center">Plain</h5>                    |
`{1 => 2, 3 => 4}`             | (hash (pair (int 1) (int 2)) (pair (int 3) (int 4)))
<h5 align="center">Keyword Splat (2.0)</h5>      |
`**foo`                        | (kwsplat (lvar :foo))
<h5 align="center">With Interpolation (2.0)</h5> |
`{ foo: 2, **bar }`            | (hash (pair (sym :foo) (int 2)) (kwsplat (lvar :bar)))

> _Keyword Splat_: Can also be used in argument lists, e.g. `foo(bar, **baz)`

### Range

<h5 align="center">Inclusive</h5> ||
--------------- | ------------------------
`1..2`          | (irange (int 1) (int 2))
<h5 align="center">Exclusive</h5> |
`1...2`         | (erange (int 1) (int 2))

## Access

<h5 align="center">Self</h5>              ||
----------------------- | -------------
`self`                  | (self)
<h5 align="center">Local Variable</h5>    |
`foo`                   | (lvar :foo)
<h5 align="center">Instance Variable</h5> |
`@foo`                  | (ivar :@foo)
<h5 align="center">Class Variable</h5>    |
`@@foo`                 | (cvar :@@foo)

### Global Variable

<h5 align="center">Regular global Variable</h5>            ||
---------------------------------------- | --------------
`$foo`                                   | (gvar :$foo)
<h5 align="center">Regular expression capture groups</h5>  |
`$1`                                     | (nth-ref 1)
<h5 align="center">Regular expression back-references</h5> |
`$&`                                     | (back-ref :$&)
`` $` ``                                 | (back-ref :$`)
`$'`                                     | (back-ref :$')
`$+`                                     | (back-ref :$+)

### Constant

<h5 align="center">Top-level Constant</h5> ||
------------------------ | ----------------------
`::Foo`                  | (const (cbase) :Foo)
<h5 align="center">Scoped Constant</h5>    |
`a::Foo`                 | (const (lvar :a) :Foo)
<h5 align="center">Unscoped Constant</h5>  |
`Foo`                    | (const nil :Foo)
<h5 align="center">`defined?`</h5>         |
`defined? a`             | (defined? (lvar :a))
`defined?(a)`            | _same_

## Assignment

<h5 align="center">To Local Variable</h5>    ||
-------------------------- | ---------------------------
`foo = bar`                | (lvasgn :foo (lvar :bar))
<h5 align="center">To instance Variable</h5> |
`@foo = bar`               | (ivasgn :@foo (lvar :bar))
<h5 align="center">To Class Variable</h5>    |
`@@foo = bar`              | (cvasgn :@@foo (lvar :bar))
<h5 align="center">To Global Variable</h5>   |
`$foo = bar`               | (gvasgn :$foo (lvar :bar))

### To Constant

<h5 align="center">Top-level Constant</h5>                           ||
-------------------------------------------------- | ------------------------------
`::Foo = 1`                                        | (casgn (cbase) :Foo (int 1))
<h5 align="center">Scoped Constant</h5>                              |
`a::Foo = 1`                                       | (casgn (lvar :a) :Foo (int 1))
<h5 align="center">Unscoped Constant</h5>                            |
`Foo = 1`                                          | (casgn nil :Foo (int 1))
<h5 align="center">To Attribute</h5>                                 |
`self.foo = 1`                                     | (send (self) :foo= (int 1))
<h5 align="center">To Attribute (with Safe Navigation Operator)</h5> |
`self&.foo = 1`                                    | (csend (self) :foo= (int 1))

### Multiple Assignment

<h5 align="center">Multiple Left Hand Side</h5> ||
----------------------------- | ---------------------------------------------------------------------
`a, b`                        | (mlhs (lvasgn :a) (lvasgn :b))
`(a, b)`                      | (mlhs (lvasgn :a) (lvasgn :b))
<h5 align="center">Assignment</h5>              |
`foo, bar = 1, 2`             | (masgn (mlhs (lvasgn :foo) (lvasgn :bar)) (array (int 1) (int 2)))
`@a, @@b = *c`                | (masgn (mlhs (ivasgn :@a) (cvasgn :@@b)) (array (splat (lvar :c))))
`a, (b, c) = d`               | (masgn (mlhs (mlhs (lvasgn :a) (lvasgn :b)) (lvasgn :c)) (lvar :d))
`self.a, self[1] = a`         | (masgn (mlhs (send (self) :a=) (send (self) :[]= (int 1))) (lvar :a))

> - Every node inside `(mlhs)` is _incomplete_
> - To _complete_ a node, a corresponding node from the `(mrhs)` can be
_appended_ to the node in question
> - This applies both to side-effect free
Assignments (`lvasgn`, etc) and side-effectful assignments (`send`).

### Binary Operator-Assignment

Features the same "incomplete assignments"
and "incomplete calls" as [Multiple Assignment](#multiple-assignment).

<h5 align="center">Variable Binary Operator-Assignment</h5> ||
----------------------------------------- | -----------------------------------------------------------
`a += 1`                                  | (op-asgn (lvasgn :a) :+ (int 1))
`@a += 1`                                 | (op-asgn (ivasgn :a) :+ (int 1))
<h5 align="center">Method Binary Operator-Assignment</h5>   |
`@a.b += 1`                               | (op-asgn (send (ivar :@a) :b) :+ (int 1))
`@a[0, 1] += 1`                           | (op-asgn (send (ivar :@a) :[] (int 0) (int 1))) :+ (int 1))

### Logical Operator-Assignment

Features the same "incomplete assignments"
and "incomplete calls" as [Multiple Assignment](#multiple-assignment).

<h5 align="center">Variable Logical Operator-Assignment</h5>||
------------------------------------ | ------------------------------
<code>@a &#124;&#124;= 1</code>      | (or-asgn (ivasgn :@a) (int 1))
`a &&= 1`                            | (and-asgn (lvasgn :a) (int 1))

<h5 align="center">Method Logical Operator-Assignment</h5> ||
---------------------------------------- | ---------------------------------------------------------
<code>@foo.bar &#124;&#124;= 1</code>    | (or-asgn (send (ivar :@foo) :bar) (int 1))
`foo.bar &&= 1`                          | (and-asgn (send (lvar :@foo) :bar) (int 1))
<code>@foo[1, 2] &#124;&#124; = 1</code> | (or-asgn (send (ivar :@foo) :[] (int 1) (int 2)) (int 1))

## Class and Module Definition

<h5 align="center">Module</h5>           ||
---------------------- | -----------------------------------------------
`module Foo; end`      | (module (const nil :Foo) (nil))
<h5 align="center">Class</h5>            |
`class Foo < Bar; end` | (class (const nil :Foo) (const nil :Bar) (nil))
`class Foo; end`       | (class (const nil :Foo) nil (nil))
<h5 align="center">Singleton Class</h5>  |
`class << a; end`      | (sclass (lvar :a) (nil))

## Method (Un)Definition

<h5 align="center">Instance Methods</h5>      ||
--------------------------- | --------------------------------------------------------
`def foo; end`              | (def :foo (args) nil)
<h5 align="center">Singleton Methods</h5>     |
`def self.foo; end`         | (defs (self) :foo (args) nil)
<h5 align="center">Undefinition</h5>          |
`undef foo :bar :"foo#{1}"` | (undef (sym :foo) (sym :bar) (dsym (str "foo") (int 1)))

## Aliasing

<h5 align="center">Method Aliasing</h5>          ||
------------------------------ | ---------------------------------------------
`alias foo :"foo#{1}"`         | (alias (sym :foo) (dsym (str "foo") (int 1)))
<h5 align="center">Global Variable Aliasing</h5> |
`alias $foo $bar`              | (alias (gvar :$foo) (gvar :$bar))
`alias $foo $&`                | (alias (gvar :$foo) (back-ref :$&))

## Formal Arguments

<h5 align="center">Plain</h5>                              ||
---------------------------------------- | ---------------------------------------------------------
`(foo)`                                  | (args (arg :foo))
<h5 align="center">Required Argument</h5>                  |
`foo`                                    | (arg :foo)
<h5 align="center">Optional Argument</h5>                  |
`foo = 1`                                | (optarg :foo (int 1))
<h5 align="center">Named splat Argument</h5>               |
`*foo`                                   | (restarg :foo)
<h5 align="center">Unnamed splat Argument</h5>             |
`*`                                      | (restarg)
<h5 align="center">Block Argument</h5>                     |
`&foo`                                   | (blockarg :foo)
<h5 align="center">Auto-Expanding Proc Argument (1.9)</h5> |
<code>&#124;foo&#124;</code>               | (procarg0 :foo)
<h5 align="center">Expression Arguments</h5>               |
<code>&#124;@bar&#124;</code>                     | (args (arg_expr (ivasgn :@bar)))
<code>&#124;foo.a&#124;</code>                     | (args (arg_expr (send (send nil :foo) :a=)))
<code>&#124;*@bar&#124;</code> | (args (restarg_expr (ivasgn :@bar)))
<code>&#124;@bar&#124; </code> | (args (blockarg_expr (ivasgn :@bar)))
<h5 align="center">Block Shadow Arguments</h5>             |
<code>&#124;;foo, bar&#124;</code>  | (args (shadowarg :foo) (shadowarg :bar))
<h5 align="center">Decomposition</h5>                      |
`def f(a, (foo, *bar)); end`             | (def :f (args (arg :a) (mlhs (arg :foo) (restarg :bar))))
<h5 align="center">Required Keyword Argument</h5>          |
`foo:`                                   | (kwarg :foo)
<h5 align="center">Optional Keyword Argument</h5>          |
`foo: 1`                                 | (kwoptarg :foo (int 1))
<h5 align="center">Named Keyword splat Argument</h5>       |
`**foo`                                  | (kwrestarg :foo)
<h5 align="center">Unnamed Keyword Splat Argument</h5>     |
`**`                                     | (kwrestarg)

> _Block Argument_: Begin of the `expression` points to `&`

> _Auto-Expanding Proc Argument_: In Ruby 1.9 and later, when a proc-like closure (i.e. a closure
created by capturing a block or with the `proc` method, but not with the `->{}` syntax or the
`lambda` method) has exactly one argument, and it is called with more than one argument, the
behavior is as if the array of all arguments was instead passed as the sole argument. This behavior
can be prevented by adding a comma after the sole argument (e.g. `|foo,|`). (procarg0 :foo), `|foo|`

> _Expression Arguments_: Ruby 1.8 allows to use arbitrary expressions
as block arguments, such as `@var` or `foo.bar`. Such expressions
should be treated as if they were on the lhs of a multiple Assignment.

### Objective-C Arguments

MacRuby includes a few more syntactic "arguments" whose name becomes the part of the Objective-C
method name, despite looking like Ruby 2.0 Keyword arguments, and are thus treated differently.

<h5 align="center">Objective-C label-like Keyword Argument</h5> ||
--------------------------------------------- | --------------------------------
`a: b`                                        | (objc-kwarg :a :b)
<h5 align="center">Objective-C pair-like Keyword Argument</h5>  |
`a => b`                                      | (objc-kwarg :a :b)
<h5 align="center">Objective-C Keyword Splat Argument</h5>      |
`(*a: b)`                                     | (objc-restarg (objc-kwarg :foo))

> Splat Arguments will only be parsed inside parentheses, e.g. 
`def f((*a: b)); end`. However, this code results in a parse error `def f(*a: b); end`

## Send

<h5 align="center">To Self</h5>                                      ||
-------------------------------------------------- | -------------------------------------------------------------------------------------
`foo(bar)`                                         | (send nil :foo (lvar :bar))
<h5 align="center">To Receiver</h5>                                  |
`foo.bar(1)`                                       | (send (lvar :foo) :bar (int 1))
`foo + 1`                                          | (send (lvar :foo) :+ (int 1))
`-foo`                                             | (send (lvar :foo) :-@)
`foo.a = 1`                                        | (send (lvar :foo) :a= (int 1))
`foo[i]`                                           | (send (lvar :foo) :[] (int 1))
`bar[1, 2] = baz`                                  | (send (lvar :bar) :[]= (int 1) (int 2) (lvar :baz))
<h5 align="center">To Superclass (with Arguments)</h5>               |
`super a`                                          | (super (lvar :a))
`super()`                                          | (super)
<h5 align="center">To Superclass (No Arguments, **Z**ero-Arity)</h5> |
`super`                                            | (zsuper)
<h5 align="center">To Block Argument</h5>                            |
`yield(foo)`                                       | (yield (lvar :foo))
<h5 align="center">Passing a Literal Block</h5>                      |
<code>foo do &#124;bar&#124;; end</code>                             | (block (send nil :foo) (args (arg :bar)) (begin ...))
<h5 align="center">Passing Expression as Block</h5>                  |
`foo(1, &foo)`                                     | (send nil :foo (int 1) (block-pass (lvar :foo)))
<h5 align="center">Stabby lambda</h5>                                |
`-> {}`                                            | (block (lambda) (args) nil)
<h5 align="center">Safe Navigation Operator</h5>                     |
`foo&.bar`                                         | (csend (send nil :foo) :bar)
<h5 align="center">Objective-C Variadic `send`</h5>                  |
`foo(1, bar: 2, 3, nil)`                           | (send nil :foo (int 1) (hash (pair (sym :bar) (objc-varargs (int 1) (int 2) (nil)))))

> MacRuby allows to pass a variadic amount of arguments via the last keyword "argument".
Semantically, these, together with the pair value of the last pair in the hash implicitly passed
as the last argument, form an array, which replaces the pair value. Despite that, the node is
called `objc-varargs` to distinguish it from a literal array passed as a value.

## Control Flow

### Logical Operators

<h5 align="center">Binary (`and`, `or`, `&&` <code>&#124;&#124;</code>)</h5>||
------------------------------------- | -----------------------------
`foo and bar`                         | (and (lvar :foo) (lvar :bar))
`foo or bar`                          | (or (lvar :foo) (lvar :bar))
<h5 align="center">Unary (`!`, `not`) (1.8)</h5>        |
`!foo`                                | (not (lvar :foo))
`not foo`                             | (not (lvar :foo))

### Branching

<h5 align="center">Without `else`</h5>                          ||
--------------------------------------------- | -------------------------------------------------------------
`if cond then iftrue; end`                    | (if (lvar :cond) (lvar :iftrue) nil)
`if cond; iftrue; end`                        | _same_
`iftrue if cond`                              | _same_
`unless cond then iftrue; end`                | (if (lvar :cond) nil (lvar :iftrue))
`unless cond; iftrue; end`                    | _same_
`iftrue unless cond`                          | _same_
<h5 align="center">With `else`</h5>                             |
`if cond then iftrue; else; iffalse; end`     | (if (lvar :cond) (lvar :iftrue) (lvar :iffalse))
`if cond; iftrue; else; iffalse; end`         | _same_
`unless cond then iftrue; else; iffalse; end` | (if (lvar :cond) (lvar :iffalse) (lvar :iftrue))
`unless cond; iftrue; else; iffalse; end`     | _same_
<h5 align="center">With `elsif`</h5>                            |
`if cond1; 1; elsif cond2; 2; else 3; end`    | (if (lvar :cond1) (int 1) (if (lvar :cond2 (int 2) (int 3))))
<h5 align="center">Ternary</h5>                                 |
`cond ? iftrue : iffalse`                     | (if (lvar :cond) (lvar :iftrue) (lvar :iffalse))

### Case Matching

<h5 align="center">`when` Clause</h5>   ||
--------------------- | ---------------------------------------------------
`when /foo/ then bar` | (when (regexp "foo" (regopt)) (begin (lvar :bar)))
`when 1, 2; meth`     | (when (int 1) (int 2) (send nil :meth))
`when 1, *foo; meth`  | (when (int 1) (splat (lvar :foo)) (send nil :meth))
`when *foo; meth`     | (when (splat (lvar :foo)) (send nil :meth))

#### `case`-Expression Clause

<h5 align="center">Without `else`</h5>                       ||
------------------------------------------ | -------------------------------------------------------------
`case foo; when "bar"; bar; end`           | (case (lvar :foo) (when (str "bar") (lvar :bar)) nil)
<h5 align="center">With `else`</h5>                          |
`case foo; when "bar"; bar; else baz; end` | (case (lvar :foo) (when (str "bar") (lvar :bar)) (lvar :baz))
<h5 align="center">`case`-Conditions Clause</h5>             |
`case; else baz; end`                      | (case nil (when (lvar :bar) (lvar :bar)) nil)
`case; when bar; bar; end`                 | (case nil (when (lvar :bar) (lvar :bar)) (lvar :baz))
`case; when bar; bar; else baz; end`       | (case nil (lvar :baz))

### Looping

<h5 align="center">With Precondition</h5>           ||
--------------------------------- | --------------------------------------------------------------------------------------------
`while condition do foo; end`     | (while (lvar :condition) (send nil :foo))
`while condition; foo; end`       | _same_
`foo while condition`             | _same_
`until condition do foo; end`     | (until (lvar :condition) (send nil :foo))
`until condition; foo; end`       | _same_
`foo until condition`             | _same_
<h5 align="center">With Postcondition</h5>          |
`begin; foo; end while condition` | (while-post (lvar :condition) (kwbegin (send nil :foo)))
`begin; foo; end until condition` | (until-post (lvar :condition) (kwbegin (send nil :foo)))
<h5 align="center">`for`-`in`</h5>                  |
`for a in array do p a; end`      | (for (lvasgn :a) (lvar :array) (send nil :p (lvar :a)))
`for a in array; p a; end`        | _same_
`for a, b in array; p a, b; end`  | (for<br/>&nbsp;(mlhs (lvasgn :a) (lvasgn :b)) (lvar :array)<br/>&nbsp;&nbsp;(send nil :p (lvar :a) (lvar :b)))
<h5 align="center">`break`</h5>                     |
`break 1`                         | (break (int 1))
<h5 align="center">`next`</h5>                      |
`next 1`                          | (next (int 1))
<h5 align="center">`redo`</h5>                      |
`redo`                            | (redo)
<h5 align="center">`return`</h5>                    |
`return(foo)`                     | (return (lvar :foo))

### Exception Handling

<h5 align="center">`rescue` Body</h5>                 ||
----------------------------------- | -----------------------------------------------------------------------------
`rescue Exception, A => bar; 1`     | (resbody (array (const nil :Exception) (const nil :A)) (lvasgn :bar) (int 1))
`rescue Exception, A => bar then 1` | _same_
`rescue Exception => @bar; 1`       | (resbody (array (const nil :Exception)) (ivasgn :bar) (int 1))
`rescue => bar; 1`                  | (resbody nil (lvasgn :bar) (int 1))
`rescue; 1`                         | (resbody nil nil (int 1))

#### `rescue` Statement

<h5 align="center">Without `else`</h5>                                  ||
----------------------------------------------------- | ------------------------------------------------------------------------------------------
`begin; foo; rescue Exception; rescue; end`           | (begin<br/>&nbsp;(rescue (send nil :foo) (resbody ...) (resbody ...) nil))
<h5 align="center">With `else`</h5>                                     |
`begin; foo; rescue Exception; rescue; else true end` | (begin<br/>&nbsp;(rescue (send nil :foo) (resbody ...) (resbody ...) (true)))
<h5 align="center">`ensure` Statement</h5>                              |
`begin; foo; ensure; bar; end`                        | (begin<br/>&nbsp;(ensure (send nil :foo) (send nil :bar))
<h5 align="center">`rescue` with `ensure`</h5>                          |
`begin; foo; rescue; nil; else; 1; ensure; bar; end`  | (begin<br/>&nbsp;(ensure<br/>&nbsp;&nbsp;(rescue (send nil :foo) (resbody ...) (int 1))<br/>&nbsp;&nbsp;&nbsp;(send nil :bar))
<h5 align="center">`retry`</h5>                                         |
`retry`                                               | (retry)
<h5 align="center">BEGIN and END</h5>                                   |
`BEGIN { puts "foo" }`                                | (preexe (send nil :puts (str "foo")))
`END { puts "bar" }`                                  | (postexe (send nil :puts (str "bar")))

## Miscellanea

<h5 align="center">Flip-flops</h5>                       ||
-------------------------------------- | ---------------------------------------------------------------------------------
`if a..b; end`                         | (iflipflop (lvar :a) (lvar :b))
`if a...b; end`                        | (eflipflop (lvar :a) (lvar :b))
<h5 align="center">Implicit Matches</h5>                 |
`if /a/; end`                          | (match-current-line (regexp (str "a") (regopt)))
<h5 align="center">Local Variable Injecting Matches</h5> |
`/(?<match>bar)/ =~ baz`               | (match-with-lvasgn (regexp (str "(?<match>bar)") (regopt)) (lvar :baz))</match>
