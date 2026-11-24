<!--no-pdf-->
# Why Each Prediction Reads the Way It Does

Write one entry per case, before you run anything. Name the rule behind each
predicted field. One to three sentences is enough. The grader checks the
structure of this file only. It does not judge how good the reasoning is, and
it sets no word minimum.

For every case you predicted wrongly, add a short correction after you run
it: what you predicted, what the language printed, and which rule you
misread. Corrections belong in the correction commits, not the prediction
commit.

Commit this file together with `predictions.tsv`, before any case runs:

```bash
git add predictions.tsv REASONING.md
```

The example below uses a query that is not one of the 16 cases, so it gives
no answer away. `parent(pat, X)` reads `parent(pat, jim)` because the `parent`
facts are tried top to bottom and only that one has `pat` as its first
argument:

```text
## parent(pat, X)

The goal scans the parent facts in source order. Only parent(pat, jim) has
pat first, so X binds to jim. No second fact matches, so the first ; finds
nothing and the search ends after one answer.
```

## P01

The parent facts are tried from top to bottom, and the query has tom as the first argument with X still unbound. Prolog finds parent(tom, liz) first and binds X to liz, then backtracks when you press ; and finds parent(tom, bob), which binds X to bob. No other fact has tom first, so the search ends with a count of 2.

## P02

There are no variables in this case, so nothing gets bound. Prolog tries parent(tom, liz) first, but liz and bob are two different atoms, so that fails. Then parent(tom, bob) matches, which gives true once, and no other fact matches, so the count is 1.

## P03

The query asks for a fact with tom as the second argument, but no parent fact has tom in that position. And since no clause head has X, Prolog fails it. The first field is false and the count is 0.

## P04

The = in this case is unification, so it computes nothing. X = f(Y, a) binds X to the term f(Y, a) and leaves Y unbound inside it, then Y = b fills that in, so X reads f(b, a) and Y is b. This gives one answer so the count is 1.

## P05

Both sides are compound terms with the same functor f and the same number of arguments so Prolog unifies them argument by argument. X binds to a and Y to b. This gives one answer, X = a, Y = b, so the second field is none and the count is 1.

## P06

a and b are two different atoms, and as stated in the manual no binding can change that. Unification fails right away, so the answer is false and the count is 0.

## P07

grandparent(tom, X) is already rule, so its two goals run from left to right. parent(tom, P) gives liz first, but liz has no children so Prolog backtracks and gives bob. bob's children are ann and then pat, so X is ann first and pat second, with a count of 2.

## P08

ancestor has its base clause first, so the direct children come out first. That gives liz and bob, then the recursive clause tries liz (no children) and goes through bob, which gives ann and pat, then sue through ann and jim through pat. The order is liz, bob, ann, pat, sue, jim, which is a count of 6.

## P09

ancestor_r is the same two clauses but with the recursive one first, so Prolog goes deep before it reads any of the base fact. Going through bob, it reaches sue via ann and jim via pat first, then bob's own base clause gives ann and pat, and the base clause for tom gives liz and bob last. The order is sue, jim, ann, pat, liz, bob, so it is the same six answers as P08 in a different order, with a count of 6.

## P10

The base clause is tested first and parent(X, jim) gives pat. The recursive clause next wherein it goes through the parent facts in order and since parent(tom, bob) comes before parent(bob, pat) and ancestor(bob, jim) holds through pat, so tom is the second answer. Then parent(bob, pat) works because ancestor(pat, jim) holds by the base clause, so bob is the third, and every other parent fact fails, so the count is 3.

## P11

The first clause of reach calls reach(a, Z) as its first goal, so it loops forever and never reads an edge. No answer returns, so all three fields are unbounded.

## P12

reach_r reads one edge before it recurses so the first answer is true through a, b, c and d. The edge from c comes back to a and sends it into reach_r(a, d) again, and that gives a second true. It becomes a cycle from there so it keeps making more answers (true) and the count is unbounded.

## P13

len([], 0) is the base case so an empty list has length 0. The recursive clause asks for the length of the tail first then N is M + 1 which simply adds 1 for the item it took off, so the length builds up 1, 2, 3 on the way back. This gives N=3 once so the second field is none and the count is 1.

## P14

The third argument here is a fixed [1, 2], so join has to split that list at every possible point. The first clause gives X=[] and Y=[1,2]. The second clause takes one element off the front and calls join again on the rest, and the first clause then answers that call, which gives X=[1] with Y=[2], then X=[1,2] with Y=[]. Once the list is [], the second clause can't match because there is no element left to take so the search stops with a count of 3.

## P15

X > 2 runs first, while X is still unbound. > needs its operands bound to numbers already so Prolog throws an error here which is the "you asked before you told me" case from the course site. The error comes before any answer, so elem never runs, and the first field, the second field and the count are all returning error.

## P16

The goal is swapped here from the last case since this one runs elem first and binds X to 1, then 2, then 3 in list order. X > 2 fails for 1 and 2, so Prolog backtracks each time, and it only passes for 3 wherein X is bound before > is called, so there is no error. The answer is X=3, the second field is none, and the count is 1.
