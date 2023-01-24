## Chaos
```python
procedure CHAOS(S)
input: set of clauses S
output: interpretation I such that I |= S
		or "don't know"
parameters: positive integer MAX-TRIES
begin
	repeat MAX-TRIES times

	I := random interpretation

	if I |= S then return I
	else return "don't know"
end
```
Simply tries random interpretations repeatedly until it either hits the limit on tries or gets lucky and finds a model. If it doesn't find an interpretation, then it can only confirm that it isn't sure; at it might have just randomly missed the model.
#### Properties
- Cannot establish unsatisfiability
- Doesn't always give an answer

## GSAT
```python
procedure GSAT(S)
input: set of clauses S
output: interpretation I such that I |= S
		or "don't know"
parameters: positive integer MAX-TRIES,
							 MAX-FLIPS
begin
	repeat MAX-TRIES times
		I := random interpretation
		
		if I |= S then return I
		
		repeat MAX-FLIPS times
			p := a variables such that flip(I,p) satisfies
						the maximal number of clauses in S
			I = flip(I, p)
			if I |= S then return I
	return "don't know"
end
```
Generates a random interpretation like Chaos, but instead of rerolling if its wrong, it repeatedly flips variables such that the number of clauses satisfied is maximised. The variable to flip is decided using heuristics, random chance, or both.
Pro: Can often find a model for large problems very quickly
Con: Can get stuck in a "plateau" state, where flipping no longer improves the number of satisfied clauses.
#### Example
![](Pasted%20image%2020230124143327.png)

## WSAT (Walk SAT)
```python
procedure WSAT(S)
input: set of clauses S
output: interpretation I such that I |= S
		or "don't know"
parameters: positive integer MAX-TRIES,
							 MAX-FLIPS
begin
	repeat MAX-TRIES times
		I := random interpretation
		if I |= S then return I
		
		repeat MAX-FLIPS times
			randomly select a clause C in S such that I |/= C
			randomly select a variables p in C

			I = flip(I, p)
			if I |= S then return I
	return "don't know"
end
```
Similar to GSAT, but instead of moving towards the interpretation with the highest amount of satisfied clauses, it picks a variable from a random unsatisfied clause in an attempt to make it satisfied.
#### Example
![](Pasted%20image%2020230124143351.png)
