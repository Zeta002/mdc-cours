
# Prolog

* Faits : `human(albert)`
* Règles `animal(X) := humain(X)`
* Questions `? - animal(albert)

Résolution

	not P v Q    P v R
	------------------
	           Q v R

`human(X) --> animal(X)` <=> `not human(X) v animal(X)`

S a pour conséquence A congrue S u {not A} est in satisfaisable

S = {`human(albert), not human(X) v animal(X)`}
A = `animal(albert)`

S u {not A} = {`human(ablert), not human v animal(X), not animal(albert)`} 