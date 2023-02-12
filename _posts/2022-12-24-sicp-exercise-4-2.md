---
layout: post
title: SICP习题4.2
date: 2022-12-24 11:12:00-0400
description: 对于没有固定关键字的用户定义的函数，在求值器中的判断顺序应该在最后。
tags: SICP
giscus_comments: true
---

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/az-quotes/jonathan-ive.jpg" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br/>

## 1. 题目

<br/>

**Exercise 4.2:** Louis Reasoner plans to reorder the cond clauses in `eval` so that the clause for procedure applications appears before the clause for assignments. He argues that this will make the interpreter more effcient: Since programs usually contain more applications than assignments, definitions, and so on, his modiﬁed `eval` will usually check fewer clauses than the original `eval` before identifying the type of an expression. 

{% highlight scheme %}
(define (eval exp env)
  (cond ((self-evaluating? exp) exp)
        ((variable? exp) (lookup-variable-value exp env))
        ((quoted? exp) (text-of-quotation exp))
        ((assignment? exp) (eval-assignment exp env))
        ((definition? exp) (eval-definition exp env))
        ((if? exp) (eval-if exp env))
        ((lambda? exp) (make-procedure (lambda-parameters exp)
                                       (lambda-body exp)
                                       env))
        ((begin? exp)
         (eval-sequence (begin-actions exp) env))
        ((cond? exp) (eval (cond->if exp) env))
        ((application? exp)
         (apply (eval (operator exp) env)
                (list-of-values (operands exp) env)))
        (else
         (error "Unknown expression type: EVAL" exp))))

(define (application? exp) (pair? exp))
{% endhighlight %}

a. What is wrong with Louis’s plan? (Hint: What will Louis’s evaluator do with the expression `(define x 3)`?)

b. Louis is upset that his plan didn’t work. He is willing to go to any lengths to make his evaluator recognize procedure applications before it checks for most other kinds of expressions. Help him by changing the syntax of the evaluated language so that procedure applications start with `call`. For example, instead of `(factorial 3)` we will now have to write `(call factorial 3)` and instead of `(+ 1 2)` we will have to write `(call + 1 2)`. 

<br/>

## 2. 思路

<br/>

注：下面的代码包含了执行所需的部分代码，如需执行，需要添加额外代码。

a. 如果直接将 `eval` 函数中的 `(application? exp)` 的情况放到 `(assignment? exp)` 的情况前面，就会出错。比如，在求值表达式 `(define x 10)` 时， `eval` 函数会匹配到 `application` ，因为 `(pair? '(define x 10))` 为真。 这是因为 `application?` 的判断是通过表达式的结构来判断的，而其他的表达式是通过第一个元素来判断的， `application` 的结构与其他表达式的结构有重合，所以必须最后判断。 如果想要提前 `application?` 判断，就要修改它的语法，为它的表达式添加一个固定的前缀，就像第二问一样。

b. 修改 `application` 的语法，所有的表达式以 `call` 开头。为此，增加2个函数，并且修改 `eval` 函数。 

{% highlight scheme %}
(define (call-application? exp) (tagged-list? exp 'call))
(define (application-body exp) (cdr exp))

(define (eval exp env)
  (cond ((self-evaluating? exp) exp)
        ((variable? exp) (lookup-variable-value exp env))
        ((quoted? exp) (text-of-quotation exp))
	((call-application? exp)
	 (let ((application-exp (application-body exp)))
	   (apply (eval (operator application-exp) env)
		  (list-of-values (operands application-exp) env))))
        ((assignment? exp) (eval-assignment exp env))
        ((definition? exp) (eval-definition exp env))
        ((if? exp) (eval-if exp env))
        ((lambda? exp) (make-procedure (lambda-parameters exp)
                                       (lambda-body exp)
                                       env))
        ((begin? exp)
         (eval-sequence (begin-actions exp) env))
        ((cond? exp) (eval (cond->if exp) env))
        (else
         (error "Unknown expression type: EVAL" exp))))
{% endhighlight %}
