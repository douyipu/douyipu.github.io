---
layout: post
title: SICP习题4.3
date: 2022-12-25 11:12:00-0400
description: Rewrite eval so that the dispatch is done in data-directed style.
tags: SICP
giscus_comments: true
---

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/az-quotes/steve-jobs.jpg" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br/>

## 1. 题目

<br/>

**Exercise 4.3:** Rewrite `eval` so that the dispatch is done in data-directed style. Compare this with the data-directed differentiation procedure of Exercise 2.73. (You may use the `car` of a compound expression as the type of the expression, as is appropriate for the syntax implemented in this section.) 

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
{% endhighlight %}

<br/>

## 2. 思路

<br/>

 注：下面的代码包含了执行所需的部分代码，如需执行，需要添加额外代码。

在SICP的2.4.3小节： Data-Directed Programming and Additivity 中， 书中介绍了什么是 data-directed programming。 

> Data-directed programming is the technique of designing programs to work with such a table directly. 

所以为了实现本次题目 data-directed style 的 `eval` 函数，我要构造一个 表来查询需要的函数。表格如下，第一行代表操作（Operations），第一列代表类型（Types）。 

|      | eval            |
| :----: | :-------------: |
| quote  | eval-quote      |
| set    | eval-assignment |
| define | eval-definition |
| if     | eval-if         |
| lambda | eval-lambda     |
| begin  | eval-begin      |
| cond   | eval-cond       |

<br/>

修改后的 `eval` 函数如下。因为 `quote` `set` `define` `if` `lambda` `begin` `cond` 这些表达式都有固定的前缀， 所以可以通过查表的方式获得对应的处理函数。 其中 `get` 函数是通过 `(get <op> <type>)` 来获得表格中的项，如果没有 找到，返回 `false` 。 

{% highlight scheme %}
(define (eval exp env)
  (cond ((self-evaluating? exp) exp)
        ((variable? exp) (lookup-variable-value exp env))
        ((get 'eval (expression-tag exp))
         ((get 'eval (expression-tag exp)) exp env))
        ((application? exp)
         (apply (eval (operator exp) env)
                (list-of-values (operands exp) env)))
        (else
         (error "Unknown expression type: EVAL" exp))))
(define (expression-tag exp) (car exp))
{% endhighlight %}

通过 `put` 函数来将新的项加入表格，使用是 `(put <op> <type> <item>)` 。 

{% highlight scheme %}
(put 'eval 'quote eval-quote)
(put 'eval 'set eval-assignment)
(put 'eval 'define eval-definition)
(put 'eval 'if eval-if)
(put 'eval 'lambda eval-lambda)
(put 'eval 'begin eval-begin)
(put 'eval 'cond eval-cond)
{% endhighlight %}

每个加入表格的函数的定义如下。

{% highlight scheme %}
(define (eval-quote exp env)
  (text-of-quotation exp))
(define (eval-assignment exp env)
  (set-variable-value! (assignment-variable exp)
                       (eval (assignment-value exp) env)
                       env)
  'ok)
(define (eval-definition exp env)
  (define-variable! (definition-variable exp)
    (eval (definition-value exp) env)
    env)
  'ok)
(define (eval-if exp env)
  (if (true? (actual-value (if-predicate exp) env))
      (eval (if-consequent exp) env)
      (eval (if-alternative exp) env)))
(define (eval-lambda exp env)
  (make-procedure (lambda-parameters exp)
                  (lambda-body exp)
                  env))
(define (eval-begin exp env)
  (eval-sequence (begin-acrions exp) env))
(define (eval-cond exp env)
  (eval (cond->if exp) env))
{% endhighlight %}

------------------

注：在SICP的3.3.3小节： Representing Tables 中，展示了如何实现表格的创建和 `get` 与 `put` 函数。

{% highlight scheme %}
(define (make-table)
  (let ((local-table (list '*table*)))
    (define (lookup key-1 key-2)
      (let ((subtable
	     (assoc key-1 (cdr local-table))))
	(if subtable
	    (let ((record
		   (assoc key-2 (cdr subtable))))
	      (if record (cdr record) false))
	    false)))
    (define (insert! key-1 key-2 value)
      (let ((subtable
	     (assoc key-1 (cdr local-table))))
	(if subtable
	    (let ((record
		   (assoc key-2 (cdr subtable))))
	      (if record
		  (set-cdr! record value)
		  (set-cdr! subtable
			    (cons (cons key-2 value)
				  (cdr subtable)))))
	    (set-cdr! local-table
		      (cons (list key-1 (cons key-2 value))
			    (cdr local-table)))))
      'ok)
    (define (dispatch m)
      (cond ((eq? m 'lookup-proc) lookup)
	    ((eq? m 'insert-proc!) insert!)
	    (else (error "Unknown operation: TABLE" m))))
    dispatch))
(define operation-table (make-table))
(define get (operation-table 'lookup-proc))
(define put (operation-table 'insert-proc!))
{% endhighlight %}
