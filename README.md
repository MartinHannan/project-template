# Irish Constituencies Neo4j Database
###### Martin Hannan, G00318811

## Introduction
As part of my Graph Theory module I was asked to construct a Graph Database using Neo4j. The database consists of the information based on the 2016 Irish General Election. I used many different websites to source the different types of information about the election. I used Microsoft Excel to format the data into proper cypher queries. 

## Database
The database consists of 3 nodes for Candidate, Constituency and Party. It also has 3 relationships "Won_Seat_In","Ran_In" and "Member_Of". A Candidate "Ran_In" a constituency and possibly "Won_Seat_In" that constituency. A Candidate is also a "Member_Of" a political party or is an independant which is also listed in the Party node.

## Queries
Summarise your three queries here.
Then explain them one by one in the following sections.

#### Query one title
This query retreives the Bacon number of an actor...
```cypher
MATCH
	(Bacon)
RETURN
	Bacon;
```

#### Query two title
This query retreives the Bacon number of an actor...
```cypher
MATCH
	(Bacon)
RETURN
	Bacon;
```

#### Query three title
This query retreives the Bacon number of an actor...
```cypher
MATCH
	(Bacon)
RETURN
	Bacon;
```
##### Notes
Through some research into some warnings I was getting while building queries I found some interesting answers.
One such warning was the following
"This query builds a cartesian product between disconnected patterns. If a part of a query contains multiple disconnected patterns, this will build a cartesian product between all those parts. This may produce a large amount of data and slow down query processing. While occasionally intended, it may often be possible to reformulate the query that avoids the use of this cross product, perhaps by adding a relationship between the different parts or by using OPTIONAL MATCH (identifier is: (c))."

While looking for answers online to the meaning behind the warning  i stumbled upon this answer which cleared it up for me.
http://stackoverflow.com/questions/33352673/why-does-neo4j-warn-this-query-builds-a-cartesian-product-between-disconnected

The answer being...
" 
The browser is telling you that:

    It is handling your query by doing a comparison between every Gene instance and every Chromosome instance. If your DB has G genes and C chromosomes, then the complexity of the query is O(GC). For instance, if we are working with the human genome, there are 46 chromosomes and maybe 25000 genes, so the DB would have to do 1150000 comparisons.

    You might be able to improve the complexity (and performance) by altering your query. For example, if we created an index on :Gene(chromosomeID), and altered the query so that we initially matched just on the node with the smallest cardinality (the 46 chromosomes), we would only do O(G) (or 25000) "comparisons" -- and those comparisons would actually be quick index lookups! This is approach should be much faster.

    Once we have created the index, we can use this query:

    MATCH (c:Chromosome)
    WITH c
    MATCH (g:Gene) 
    WHERE g.chromosomeID = c.chromosomeID
    CREATE (g)-[:PART_OF]->(c);

    It uses a WITH clause to force the first MATCH clause to execute first, avoiding the cartesian product. The second MATCH (and WHERE) clause uses the results of the first MATCH clause and the index to quickly get the exact genes that belong each chromosome.
"
## References
1. [Neo4J website](http://neo4j.com/), the website of the Neo4j database.
2. http://www.thejournal.ie/election-2016/constituency/16/
2. http://elections.independent.ie/election-2016/kildare-south
2. http://stackoverflow.com/questions/33352673/why-does-neo4j-warn-this-query-builds-a-cartesian-product-between-disconnected
