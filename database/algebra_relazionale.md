# operazioni unarie
## select (SQL where)

## project (SQL select)
quando viene rimossa la chiave potrebbe rimuove alcune tuple se duplicate
→ DISTINCT
## rename (SQL as)
# operazioni insiemistiche
le relazioni devono essere compatibili (eccetto per X):
- gli insiemi devono avere lo stesso numero di attributi
- devono avere lo stesso dominio (o domini compatibili)
UNION e INTERSECTION sono operazioni 
- commutative:
	R ∪ S = S ∪ R
	R ∩ S = S ∩ R
- associative:
	(R ∩ S) ∩ T = R ∩ (S ∩ T)
## unione (U)
R U S : include tutte le tuple che sono almeno in un'insieme
elimina i duplicati
## intersezione (∩)
R ∩ S : include tutte le tuple che sono in entrambi gli insiemi
## differenza (-)
R - S : include tutte le tuple che sono in R ma non in S 
## prodotto cartesiano (X) (SQL theta-join)
R ⋅ S : è la relazione che ha come schema l’unione degli attribute di R e S e una tupla <r,s> (la concatenazione di r e s) per ogni coppia di tuple rεR e sεS
è commutativo ed associativo
# operazioni binarie
## join (|><|)
con differenti varianti
la cardinalità del join è compresa tra 0 e r1 X r2
se è eseguito su una super-chiave allora ogni tupla di r2 fa match con al massimo una tupla di r1 se c'è un vincolo di integrità referenziale allora fa match esattamente una volta
## divisione
non è un operatore primitivo e non tutti i DBMS la supportano
es: Trovare i marinai che hanno prenotato tutte le barche
# altre operazioni
## outer joins, outer unions
serve per mantenere alcune (o tutte) le tuple che non hanno un match nelle altre forme di JOIN
- left outer join (=|><|) : mantenere tutte le tuple del primo operando
- right (|><|=) : * del secondo operando
- full (=|><|=) : * di entrambi gli operandi
## funzioni aggregate
sum, count, avg, min, max
