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

Prolog tries the parent facts in source order, so the first answer is X=liz and the second is X=bob. No other fact has tom as its first argument, so the count is 2.

## P02

The fact parent(tom,bob) matches the query directly, giving true because there are no query variables to bind. Only one fact matches, so the second answer is none and the count is 1.

## P03

No parent fact has tom as its second argument, so the query fails and the first field is false. There is no second answer, giving none and a count of 0.

## P04

The goals run left to right, first binding X to f(Y,a) and then binding Y to b, which also makes X equal to f(b,a). The answer is X=f(b,a),Y=b, with no alternative solutions, so the second field is none and the count is 1.

## P05

Unification matches corresponding arguments, binding X to a and Y to b. This gives X=a,Y=b as the only answer, so the second field is none and the count is 1.

## P06

The distinct atoms a and b cannot unify, so the first field is false. There is no second answer, giving none and a count of 0.

## P07

The first goal initially selects liz, but she has no children, so Prolog backtracks and selects bob. Bob's children appear as ann followed by pat, giving X=ann and X=pat, with a total count of 2.

## P08

The base clause runs first and returns Tom's direct children in fact order, giving X=liz and X=bob. The recursive clause later returns ann, pat, sue, and jim, so the search ends with a count of 6.

## P09

The recursive clause runs first, so Prolog explores deeper descendants before returning direct children. The first answers are X=sue and X=jim, followed by ann, pat, liz, and bob, giving a count of 6.

## P10

The base clause first matches parent(pat,jim), giving X=pat. The recursive clause then finds Tom's path to Jim before Bob's path, giving X=tom as the second answer and X=bob as the last, so the count is 3.

## P11

The first clause calls reach again before checking any edge, and each nested call selects that same recursive clause. The search produces no answer and never finishes, so first, second, and count are all unbounded.

## P12

The base clause is tried first at each call, so the path a to b to c to d produces true. Backtracking follows the cycle through c to a and eventually reaches d again, producing a second true. Repeated trips around the cycle produce more proofs without ending, so the count is unbounded.

## P13

The recursive clause removes one list element per call until the empty-list base case returns 0, then each returning call adds 1. This gives N=3 as the only answer, so the second field is none and the count is 1.

## P14

The base clause first gives X=[],Y=[1,2], and backtracking through the recursive clause next gives X=[1],Y=[2]. The final split is X=[1,2],Y=[], so the count is 3.

## P15

The comparison X > 2 runs before elem binds X to a number, causing an instantiation error. The grader records error in all three fields.

## P16

The elem goal tries X=1, X=2, and X=3 in order, but only 3 passes the comparison X > 2. The only answer is X=3, so the second field is none and the count is 1.