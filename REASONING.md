<!--no-pdf-->
# Why Each Prediction Reads the Way It Does

## P01

The P01 is read as tom is a parent of X, and to answer the predictions, first and second, it is necessary to look at kb.pl as it is the one that shows the individuals who have tom as their parent. Facts are tried top to bottom, at the very top there is `parent(tom, liz).` matching the first prediction then `parent(tom, bob).` for the second prediction. For the count, there are only two kids of tom, after them no fact matched anymore and the search runs out of answers, thus the count is two. 

## P02

There are no variables in this case, so nothing gets bound. Prolog tries parent(tom, liz) first, but liz and bob are two different atoms, so that fails. Then parent(tom, bob) matches, which gives true once. No other fact matches, so the first ; finds nothing, the second field is none, and the count is 1.

## P03

The query asks for a fact with tom as the second argument, but no parent fact has tom in that position. And since no clause head has X, Prolog fails it. The first field is false, the second field is none because there is no answer to backtrack into, and the count is 0.

## P04

Goals run left to right. `X = f(Y, a)` unifies X with the term f(Y,a) and leaves Y unbound, then `Y = b` fills in Y, so X becomes f(b,a) and the first answer is X=f(b,a),Y=b. Each `=` can succeed in only one way and leaves no choice point, so there is no second answer, which makes it none, and the count is 1.

## P05

There is only unification, without knowledge base lookup. The matching goes argument by argument, X unifies with `a` and Y binds to `b`, thus both sides become f(a,b) and the first answer is X=a,Y=b. There is only one way to unify them, which makes the second prediction, none, and the count is 1. 

## P06

a and b are two different atoms, and as stated in the manual no binding can change that. Unification fails right away, so the answer is false, the second field is none because nothing succeeded to backtrack into, and the count is 0.

## P07

As goals run left to right, and facts are tried top to bottom, `parent(tom,P)` binds P to liz first, however `parent(liz,X)` fails because liz has no children, thus Prolog backtracks then binds P to bob. `parent(bob,X)` gives X=ann and X=pat in order for the predictions, so the count is 2. 

## P08

The ancestor has a base clause first, thus the clause order makes the direct children of tom come out first, X=liz, and X=bob as the first and second predictions because of backtracking. After the base clause runs out, the recursive clause goes through the remaining descendants of bob (ann, pat, sue, jim) thus the count will result in 6. 

## P09

The ancestor_r has the recursive clause first, so the search goes deep before it reads the base fact. Facts are still read top to bottom, liz is a dead end, then tom, bob and ann lead to sue, so X=sue is first. Then pat leads to jim, so X=jim is second. The base clauses then give ann, pat, liz and bob, making the count 6. 

## P10

The base clause is tried first, so parent(X, jim) gives X=pat. The recursive clause then reads the parent facts in order. `parent(tom, bob)` comes first and bob reaches jim through pat, so X=tom is second. Then `parent(bob, pat)` works because `ancestor(pat, jim)` holds by the base clause, so X=bob is third, and every other parent fact fails, so the count is 3. 

## P11

The first clause of reach calls reach(a, Z) as its first goal, so it loops forever and never reads an edge. No answer returns, so all three fields are unbounded.

## P12

reach_r reads one edge before it recurses so the first answer is true through a, b, c and d. The edge from c comes back to a and sends it into reach_r(a, d) again, and that gives a second true. It becomes a cycle from there so it keeps making more answers (true) and the count is unbounded.

## P13
len([], 0) is the base case so an empty list has length 0. The recursive clause asks for the length of the tail first then N is M + 1 which simply adds 1 for the item it took off, so the length builds up 1, 2, 3 on the way back. This gives N=3 once so the second field is none and the count is 1.

## P14

The third argument here is a fixed [1, 2], so join has to split that list at every possible point. The first clause gives X=[] and Y=[1,2]. The second clause takes one element off the front and calls join again on the rest, and the first clause then answers that call, which gives X=[1] with Y=[2], then X=[1,2] with Y=[]. Once the list is [], the second clause can't match because there is no element left to take so the search stops with a count of 3.

## P15

As goals run from left to right, the X in `X>2` has nothing bound to it, and `>` needs a number thus X, as an unbound operand, will cause an error before elem ever runs, which makes the three prediction fields, error. 

## P16

Now `elem` runs first and binds X to 1, 2, then 3 in list order, so `X > 2` has a number to check. 1 and 2 fail the test and make Prolog backtrack into elem, and only 3 passes. So the first answer is X=3, there is no second answer, none, and the count is 1.
