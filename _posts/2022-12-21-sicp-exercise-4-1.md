---
layout: post
title: SICP习题4.1
date: 2022-12-21 11:12:00-0400
description: 使用迭代或了let关键字的方式，让list-of-values函数的执行顺序不依赖内部函数cons的执行顺序。
tags: SICP
giscus_comments: true
---

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/az-quotes/john-von-neumann.jpg" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br/>

## 1. 题目

<br/>

**Exercise 4.1:** Notice that we cannot tell whether the metacircular evaluator evaluates operands from left to right or from right to left. Its evaluation order is inherited from the underlying Lisp: If the arguments to `cons` in `list-of-values` are evaluated from left to right, then `list-of-values` will evaluate operands from left to right; and if the arguments to `cons` are evaluated from right to left, then `list-of-values` will evaluate operands from right to left.

{% highlight scheme %}
(define (list-of-values exps env)
  (if (no-operands? exps)
      '()
      (cons (eval (first-operand exps) env)
            (list-of-values (rest-operands exps) env))))
{% endhighlight %}

Write a version of `list-of-values` that evaluates operands from left to right regardless of the order of evaluation in the underlying Lisp. Also write a version of `list-of-values` that evaluates operands from right to left.

<br/>

## 2. 思路

<br/>

注：下面的代码包含了执行所需的全部代码，可以直接执行。

为了实现 `list-of-values` 的执行顺序不依赖于 `cons` 的执行顺序。 我想到使用迭代的方式来实现执行顺序的控制。这样，当 `iter` 函数递归调用时，不论 `iter` 与 `cons` 函数的执行顺序是从左向右还是从右向左，都会按照表达式的次序依次求值， 并将结果添加在 `values` 这个列表中保存起来，最后在 `iter` 函数结束时返回 `(reverse values)` 。 （注：The MIT implementation of Scheme includes `eval` , as well as a symbol `user-initial-environment` that is bound to the initial environment in which the user's input expressions are evaluated.） 

{% highlight scheme %}
(define (no-operands? exps) (null? exps))
(define (first-operand exps) (car exps))
(define (rest-operands exps) (cdr exps))
(define (list-of-values-left-to-right exps env)
  (define (iter exps values)
    (if (no-operands? exps)
        (reverse values)
        (iter (rest-operands exps)
              (cons (eval (first-operand exps) env) values))))
  (iter exps '()))
{% endhighlight %}

也可以使用 `let` 关键字来实现。

{% highlight scheme %}
(define (list-of-values-left-to-right exps env) 
  (if (no-operands? exps)
      '()
      (let ((left (eval (first-operand exps) env)))
    	(cons left (list-of-values (rest-operands exps) env)))))
{% endhighlight %}

用下面的2行代码测试一下 `list-of-values-left-to-right` 函数。 第一句代码说明函数正常返回求值后的列表，第二句代码说明函数从左向右求值。 

{% highlight scheme %}
1 ]=> (list-of-values-left-to-right '('x 'y) user-initial-environment)

;Value: (x y)
	      
1 ]=> (list-of-values-left-to-right '((display "X") (display "Y")) user-initial-environment)
XY
;Value: (#!unspecific #!unspecific)
{% endhighlight %}

`list-of-values-right-to-left` 函数可以通过 `list-of-values-left-to-right` 函数来实现。 

{% highlight scheme %}
(define (list-of-values-right-to-left exps env)
  (reverse (list-of-values-left-to-right (reverse exps) env)))
{% endhighlight %}

`list-of-values-right-to-left` 函数也可以通过迭代的方式独立实现。 

{% highlight scheme %}
(define (last-operand exps) (last exps))
(define (remove-last-operand exps)
  (reverse (cdr (reverse exps))))
(define (list-of-values-right-to-left exps env)
  (define (iter exps values)
    (if (no-operands? exps)
        values
        (iter (remove-last-operand exps)
              (cons (eval (last-operand exps) env) values))))
  (iter exps '()))
{% endhighlight %}

也可以使用 `let` 关键字来实现。

{% highlight scheme %}
(define (list-of-values-right-to-left exps env) 
  (if (no-operands? exps)
      '()
      (let ((right (list-of-values (rest-operands exps) env)))
    	(cons (eval (first-operand exps) env) right))))
{% endhighlight %}

用下面的2行代码测试一下 `list-of-values-right-to-left` 函数。 第一句代码说明函数正常返回求值后的列表，第二句代码说明函数从右向左求值。 

{% highlight scheme %}
1 ]=> (list-of-values-right-to-left '('x 'y) user-initial-environment)

;Value: (x y)

1 ]=> (list-of-values-right-to-left '((display "X") (display "Y")) user-initial-environment)
YX
;Value: (#!unspecific #!unspecific)
{% endhighlight %}

