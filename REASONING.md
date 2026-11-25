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

The query parent(tom, X) is asking the program to find values of X such that parent(tom, X) would evaluate to true. Since the program evaluates facts top-to-bottom, parent(tom, liz) is the first one read, giving us X=liz. The line right after that gives us X=bob as the second (there are no other applicable facts after this, so the count is 2).

## P02

parent(tom, bob) is directly true in the list of facts, thus we get true as the first answer. Since this fact is only stated once, when “;” is pressed to get the second answer, it will return false as there are no other facts that state parent(tom, bob). Thus, the count is 1.

## P03

This asks to find an X such that parent(X, tom) is supported by the facts. However, there is no fact that has tom in the second argument, so the first field is false. Getting a second answer from here will then lead to none due to the false already being encountered.

## P04

X is bound to f(Y,a) on the first expression, and then Y is bound to b. Prolog searches for proofs to support the assertion that X=f(Y,a) so X=f(b,a) and Y=b is returned. However, this is the only match so the second field is none and the count is 1.

## P05

X=a and Y=b satisfies the conditions of unification, to make the structures of both sides match, so that is what is returned. This is the only answer so the next answer is none, and the count is one.

## P06

“a=b” attempts to unify two atoms (not variables) that are different from each other, which is impossible. Therefore it is false, and there is no alternative, giving us none for the second answer, and a count of 0.

## P07

The query attempts to find values of X to make grandparent(tom,X) true. Evaluating from the top-down, it first finds parent(tom,liz) but liz does not appear as the parent of anyone. Next, it encounters bob, which does have children, giving us ann then pat going in evaluation-order, and a count of 2 (since nothing else is found).

## P08

ancestor has the base case first, so it utilizes the values of X that are relatively shallow in the tree. It checks tom-liz first which satisfies the base case then checks tom-bob which also satisfies the base case. The total count of ancestors is 6, but the ones that are closer to tom are returned first.

## P09

ancestor_r has the recursive rule first, so ancestor_r(tom,X) gets to sue first (tom and liz first but that is a dead end, so the program goes to tom and bob, and then checks bob and ann, and then ann and sue; this last case being caught by the base case), and then jim. So essentially the ones that require the most expansions are taken first. There are four other possibilities after jim, so there is a total count of 6.

## P10

The base case is tried first which gives us X=pat. There aren’t any direct parents after pat, so we try the recursive case, which actually gets us X=tom first because when it expands into parent(A,P), ancestor(P,pat), it actually tests the pairs for A,P starting with tom,liz first. This leaves X=bob as the last proof to be found and a count of 3.

## P11

Since the recursive condition reach(X,Z) is written before edge(Z,Y), this just results in infinite recursion. Expanding on reach(a,d) makes the program evaluate reach(a,Z) next, and that immediately recurses as well. Therefore it is unbounded.

## P12

Since the base case is written first, we expand into edge(a,b), reach_r(b,d). This further expands into edge(b,c), reach_r(c,d) which is covered by the base case so true is returned. Since a->b->c->a is a cycle, when the program attempts to find another solution, it backtracks to c and finds the cycle, giving us an unbounded count.

## P13

The rule here essentially breaks down the list into head and tail until the list is empty, adding one to N until the list can no longer be broken down. Thus, N=3 is the only answer, and the count is 1.

## P14

Upon entry, the base case says that join([], L, L) is true, therefore X gets bound to [] and Y to [1,2] (same value as the last argument). Backtracking, the program will then try the recursive case next which gives us X=[1],Y[2], because the program gives X the head value of [1,2] before recursing and going back to the base case. For the final case, X=[1,2],Y=[] is reached because the program recurses twice, this time passing 2 as well onto X before unwinding everything.

## P15

There is an initialization error as X>2 is up front, so the entire expression evaluates as an error.

## P16

This case runs two conditions: elem(X, [1,2,3]) and X > 2. The first condition gives us X=1, X=2 and X=3 but only X=3 actually passes the second condition, so overall the sole answer is X=3.
