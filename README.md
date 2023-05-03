Download Link: https://assignmentchef.com/product/solved-cs305-homework-2-extra-scheme-interpreter
<br>
In this homework you will implement a Scheme interpreter, similar to the ones we saw in the lectures. However, the subset of Scheme that is handled by the interpreter will be larger.

1             The Scheme subset s7

The syntax of the Scheme subset that will be covered by the interpreter that you will implement for this homework is given below.

We have already implemented a sequence of interpreters for Scheme during the lectures. You can find these interpreters on SUCourse (see Resources page). If you like, you can simply take the most advanced interpret we have on SUCourse and modify it to implement the additional fetaures required by this homework. On the other hand, if you like you can also start implementing a brand–new interpreter of yourself.

The last subset of Scheme we handled in the class was named as s6. Let us call the subset we will handle in this homework as s7. The subset s7 has if, cond, let, and let* as the new constructs to be added on top of s6. Therefore, it would be sufficient for you if you take the interpreter for s6 from SUCorse, and simply add the implementation of if, cond, let, and let*.

As a matter of fact, we have explained in the class, after you implement let, implementing let* can easily be handled by converting let* expressions into equivalent let expressions. See the lecture notes for the explanation of how one can convert a let* expression into an equivalent let expression. Similarly, the implementation of cond expression can be performed by converting a given cond expression into an equivalent if expression (i.e. into a sequence of nested if expressions), and interpreting this if expression.

<table width="602">

 <tbody>

  <tr>

   <td width="602">Grammar for the subset s7</td>

  </tr>

  <tr>

   <td width="602">&lt;s7&gt; -&gt; &lt;define&gt;| &lt;expr&gt;&lt;define&gt; -&gt; ( define IDENT &lt;expr&gt; )&lt;expr&gt; -&gt; NUMBER| IDENT| &lt;if&gt;| &lt;cond&gt;| &lt;let&gt;| &lt;letstar&gt;| ( &lt;procedure&gt; &lt;actuals&gt; )&lt;if&gt; -&gt; ( if &lt;expr&gt; &lt;expr&gt; &lt;expr&gt; )&lt;cond&gt; -&gt; ( cond &lt;conditional_list&gt; &lt;else_condition&gt; )&lt;conditional_list&gt; -&gt; &lt;conditional&gt;| &lt;conditional&gt; &lt;conditional_list&gt;&lt;conditional&gt; -&gt; ( &lt;expr&gt; &lt;expr&gt; )&lt;else_condition&gt; -&gt; (else &lt;expr&gt;)&lt;let&gt; -&gt; ( let ( &lt;var_binding_list&gt; ) &lt;expr&gt; )&lt;letstar&gt; -&gt; ( let* ( &lt;var_binding_list&gt; ) &lt;expr&gt; )&lt;var_binding_list&gt; -&gt; ( IDENT &lt;expr&gt; ) &lt;var_binding_list&gt; |&lt;procedure&gt; -&gt; +| –| *| /&lt;actuals&gt; -&gt; &lt;expr&gt; &lt;actuals&gt;|</td>

  </tr>

 </tbody>

</table>

Note that anything accepted as a number by number? predicate is a number for our interpeter as well. This is how we have been implementing the numbers in the interpreters we developed in the class.

Since we now have if and cond expressions, we actually need boolean values as well. However, to keep things simple for the homework, we will represent boolean values by numeric values. We will adopt the following approach:

<ul>

 <li>Number 0 is considered as the boolean false</li>

 <li>Any number value other than 0 is considered as the boolean true.</li>

</ul>

We would like to emphasize the following points about the syntax and semantics of the new constructs that you will include into the subset in this homework.

<ul>

 <li>We defined above an if expression to have 3 expressions. This is the normal syntax. The first expression is the “test expression”. The second expression is the “then expression” (the value of “then expression” is used when the value of the “test expression” is true – i.e. a non–zero numeric value). The third expression is the “else expression” (the value of “else expression” is used when the value of the “test expression” is false – i.e. 0).</li>

 <li>Note that, normally an if expression can be used with 2 expressions only. Such an if expression would not have an “else expression”. However, to keep things simple in this homework, we restrict ourselves to if expressions with all 3 expressions existing. This is in fact how we presented the grammar above.</li>

 <li>A cond expression will have a non–empty list of alternative expressions (as represented by &lt;conditional list&gt; above). In addition, there will always be an “default alternative” at the end (as represented by tt ¡else condition¿ above). Note again that normally, a cond expression may have an empty list of “alternative expressions” and it may not have any “default alterntive”. However, again to keep things simple in this homework, we restrict the syntax of cond expressions as described above.</li>

 <li>The semantics of a cond expression is exactly the same as we considered in the class. We start by considering the first alternative (i.e. &lt;conditional&gt;), and traverse the alternatives one by one. Each alternative has two expressions. The first expression is the “test expression”, the second expression is the “value expression”. When we consider an alternative, if the “test expression” evaluates to true (i.e to a non–zero numeric value), the value of the “value expression” is returned as the value of the entire cons If the “test expression” evaluates to false (i.e to 0), then we proceed and consider the next alternative. If this process reaches to the default alternative at the end, the value of the expression that we have in the default alternative is returned as the value of the entire cond expression.</li>

 <li>let and let* expressions can have empty list of bindings. The semantics of let and let* expression are the same as we explained in the class.</li>

</ul>

Below, we provide some sample interactions in “Scheme Interaction” part, which we hope would explain the semantics of the contructs a little bit more.

2             The procedure cs305

You should declare a procedure named cs305 which will start the show when called. It should not take any arguments.

In every iteration of your REPL, you should print out the prompt given below in “Scheme Interaction” sample, then accept an input from the user, then evaluate the value of the input expression, and finally print the value evaluated by using a value prompt. The following is a sample on how the interaction with your interpreter must look like.




<table width="602">

 <tbody>

  <tr>

   <td width="602">Scheme Interaction</td>

  </tr>

  <tr>

   <td width="602">1 ]=&gt; (cs305) cs305&gt; 3 cs305: 3cs305&gt; (define x 5) cs305: xcs305&gt; x cs305: 5cs305&gt; (define y 7) cs305: ycs305&gt; y cs305: 7cs305&gt; (+ x y) cs305: 12cs305&gt; (+ x y (- x y 1) (* 2 x y ) (/ y 7))cs305: 80cs305&gt; (if x (+ x 1) (* x 2)) cs305: 6cs305&gt; (if (- 5 x) (+ x 1) (* x 2)) cs305: 10cs305&gt; (cond(0                                 (+ x 1))((* y 0 )                    (/ y 0))((- y (+ x 5)) (* x y))(else                                     -1)) cs305: 35</td>

  </tr>

 </tbody>

</table>




<table width="602">

 <tbody>

  <tr>

   <td width="602">Scheme Interaction (cont’d)</td>

  </tr>

  <tr>

   <td width="602">cs305&gt; (cond(0                                 (+ x 1))((* y 0 )                    (/ y 0))((- y (+ x 2)) (* x y))(else                                     -1)) cs305: -1cs305&gt; (define z (let ((x 1) (y x)) (+ x y))) cs305: zcs305&gt; z cs305: 6cs305&gt; (define z (let* ((x z) (y x))(if (- (/ y 2) 3) (+ x z) (- z (/ x 3)))))cs305: zcs305&gt; z cs305: 4cs305&gt; (let ((x 1)) (+ x y z)) cs305: 12cs305&gt; (let () (+ x y z)) cs305: 16cs305&gt; (define 1 y) cs305: ERRORcs305&gt; (def x 1) cs305: ERRORcs305&gt; t cs305: ERRORcs305&gt; (if 1 2) cs305: ERRORcs305&gt; (if 1) cs305: ERRORcs305&gt; (if) cs305: ERROR</td>

  </tr>

  <tr>

   <td width="602">Scheme Interaction (cont’d)</td>

  </tr>

  <tr>

   <td width="602">cs305&gt; (cond) cs305: ERRORcs305&gt; (cond 1) cs305: ERRORcs305&gt; (cond 1 2) cs305: ERRORcs305&gt; (cond 1 2 3) cs305: ERRORcs305&gt; (cond (1 2) (1 3) (2 4)) cs305: ERRORcs305&gt; (cond (1 2) (else 3) (else 4)) cs305: ERRORcs305&gt; (cond (else 3) (3 4)) cs305: ERRORcs305&gt; (let (x 3) (y 4) (+ x y)) cs305: ERRORcs305&gt; (let ((x)) (+ x y)) cs305: ERRORcs305&gt; (let (()) (+ x y)) cs305: ERROR</td>

  </tr>

 </tbody>

</table>

The ERROR message can be anything you want to write. However make sure that it appears on the same line as the value prompt cs305:

Use the built–in display procedure for displaying the error messages.