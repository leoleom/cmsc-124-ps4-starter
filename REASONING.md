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

The P01 is read as tom is a parent of X, and to answer the predictions, first and second, it is necessary to look at kb.pl as it is the one that shows the individuals who have tom as their parent. Facts are tried top to bottom, at the very top there is `parent(tom, liz).` matching the first prediction then `parent(tom, bob).` for the second prediction. For the count, there are two are only two kids of tom, after them no fact matched anymore and the search runs out of answers, thus the count is two. 

## P02

P02 does not hold a variable, so there is nothing to bind, and the answer is true for the first prediction as `parent(tom, bob).` shows up exactly once. The second will have none, as backtracking after that match finds no other fact that unifies with `parent(tom, bob).` in kb.pl. As for the count, it is one as `parent(tom, bob).` shows exactly once before the search runs out of answers. 

## P03

P03 has the goal of searching for the parent of tom however there is no such fact that gives the variable X a value thus it is false. As for the second prediction, it is none as there is no fact that unifies with `parent(X, tom)`. While, the count is 0 as there is no fact in kb.pl matching `parent(X, tom)`.

## P04

Goals run left to right. `X = f(Y, a)` unifies X with the term f(Y,a) and leaves Y unbound, then `Y = b` fills in Y, so X becomes f(b,a). Each `=` can succeed in only one way and leaves no choice point, so there is no second answer, which makes it none, and the count is 1.

## P05

There is only unification, without knowledge base lookup. The matching argument by argument, X unifies with `a` and `b` unifies with Y, thus both sides become f(a,b). There is only one way to unify them, which makes the second prediction, none, and the count is 1. 

## P06

`a` and `b` are different atoms, as no binding can make two different atoms identical, the unification fails which makes the query have no answer. For the prediction, the first one is false, the second is none, and the count is 0. 

## P07

As goals run left to right, and facts are tried top to bottom, `parent(tom,P)` binds P to liz first, however `parent(liz,X)` fails because liz has no children, thus Prolog backtracks then binds P to bob. `parent(bob,X)` gives X=ann and X=pat in order for the predictions, so the count is 2. 

## P08

The ancestor has a base clause first, thus the clause order makes the direct children of tom come out first, X=liz, and X=bob as the first and second predictions because of backtracking. After the base clause runs out, the recursive clause goes through the remaining descendants of bob (ann, pat, sue, jim) thus the count will result to 6. 

## P09

The ancestor_r has the recursive clause first, so the search goes deep before it reads the base fact. Facts are still read top to bottom, liz is a dead end, then tom, bob and ann lead to sue, so X=sue is first. Then pat leads to jim, so X=jim is second. The base clauses then give ann, pat, liz and bob, making the count 6. 

## P10

The base clause is tried first, so parent(X, jim) gives X=pat. The recursive clause then reads the parent facts top to bottom which results to second prediction to X=tom as the fact of tom and bob, leads to pat and jim, which gives X=bob. Following tom's fact for bob, the count is 3. 


## P11

The recursive clause is first, and its first goal is reach(a, Z), which calls the same clause again before any edge is read. Nothing ever succeeds or fails, so Prolog never backtracks to the base clause and never prints false. All three fields are unbounded.


## P12


The `reach_r` reads an edge before it recurses. Going a to b to c, edge(c, d) is a fact, so the first prediction is true. Backtracking into reach_r(c, d) takes edge(c, a) and asks reach_r(a, d) again, which is the starting query, so the second prediction is true too. The graph has a cycle, so the search never runs out of choice points and the count is unbounded.


## P13

Each list shape matches only one clause, [] matches the base clause and a non-empty list matches the recursive clause. The search goes down to len([], 0), then `is` adds 1 on the way back up for each of the three elements, so N=3 as the first prediction. No call has a second clause to try, so there is nothing to backtrack into which makes the prediction of second is none and the count is 1.


## P14

The `join` is run backward with the third argument fixed, so X and Y are the lists that are joined to make [1,2]. Clause order tries the base clause first, giving X=[] and Y=[1,2]. Backtracking into the recursive clause peels one element off at a time, giving X=[1],Y=[2] and then X=[1,2],Y=[]. After that [] has no head, so nothing else matches and the count is 3.


## P15

As goals run from left to right, the X in `X>2` has nothing bound to it, and `>` needs a number thus X, as an unbounded operand, will cause an error, which makes the three prediction fields, error. 

## P16

Now `elem` runs first and binds X to 1, 2, then 3 in list order, so `X > 2` has a number to check. 1 and 2 fail the test and make Prolog backtrack into elem, and only 3 passes. So the first answer is X=3, there is no second answer, none, and the count is 1.


