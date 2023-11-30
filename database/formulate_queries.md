# [linear approach](https://www.essentialsql.com/write-sql/)
know your database’s structure (tables, relations)
## pose the question
be very explicit: question any possible ambiguity

## map key aspects of our question into DB language
collect all the pieces we’ll need to put the puzzle together
break down a seemingly complicated problem into it simple parts

**Select**
- Do you need actual columns or aggregate data?
- What columns do we want to include in our query?
- Do we want duplicates?

**From**
- What tables are needed to display those columns?
- Are other tables, such as bridge tables required to "navigate" to those tables?

|Column|Alias|Table|
|-|-|-|
collegare le colonne richieste alle tabelle necessarie per dimostrare la correttezza

**Join** Conditions
- how these tables are related to one another
- decide with type of join is the right one

|start table|start table columns|end table columns|end table|
|-|-|-|-|
||PK|FK||
tutte le tabelle devono essere collegate (struttura a grafo/albero)

**Where** Clause
- Are there any filter conditions we want to consider?
- Are the columns we want to filter included in the list of tables?
- If not, we’ll have to include them in the join conditions.

|table|column|comparison|value|
|-|-|-|-|
|employee|marital_status|=|'M'|

**Order by**
- How should the results be sorted?

**group by**
se la target list include sia dati aggregati che campi normali, significa che bisogna raggruppare per i campi normali

**having**
filter groups (aggregate functions aliased)
## translate our mapped information into SQL
write complex queries in stages: write a piece, test it and then build on that success
add a new table (to join) per step

# [top-down approch](https://junglescouteng.medium.com/a-practical-guide-to-complex-sql-queries-1880a91def20)
When we receive a problem, sometimes we can’t answer it directly. We need to break down the problem into multiple parts, solve each of them, and then sum them up.

## Framing the problem
When we receive a problem, we should look for the simplest solutions first. If none exist, then we should proceed to more complex solutions.
(describe the hypothetical simplest solution)

To do so, we need to keep rephrasing (in NL) the problem into a progressively more complex and/or lower-level version until the entities in the revised problem resemble the available data
ex: `Write a query that returns the profit of January 2020.` →
`Find the total expenses for January 2020, and subtract that from the total revenue for January 2020.`

## going inside-out
solve the sub-queries first, and then progressively work towards a higher level until we solve the overall problem
differentiate between sub-queries and main-query to reveal constraints early in the process

## making subquery results accessible
- Planning the context: what sub-queries return?
- Incorporating with the `JOIN` clause the sub-queries into the same context

## Computing the results returned by FROM
