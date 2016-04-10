# Irish Constituencies Neo4j Database
###### Martin Hannan, G00318811

## Introduction
As part of my Graph Theory module I was asked to construct a Graph Database using Neo4j. The database consists of the information based on the 2016 Irish General Election. I used many different websites to source the different types of information about the election. I used Microsoft Excel to format the data into proper cypher queries. 

## Database
The database consists of 3 nodes for Candidate, Constituency and Party. It also has 3 relationships "Won_Seat_In","Ran_In" and "Member_Of". A Candidate "Ran_In" a constituency and possibly "Won_Seat_In" that constituency. A Candidate is also a "Member_Of" a political party or is an independant which is also listed in the Party node.

###Candidates
Name         | Name of the Candidate     

 Party        | Candidates Party          
 Constituency | Area Candidate is Running  
 Gender       | Sex of the Candidate      
 Occupation   | Candidate Occupation      
 Age          | Age of Candidate          
 RanIN        | Tag for the Constituency  
 Won          | If they won a seat        

###Constituency
 Name         | Name of the Constituency  	
 Tag          | Tag for the Constituency  	
 Electorate   | Number of Voter	   	
 Seats        | Seats Available           	
 TotalPoll    | Number of Voters who Voted	
 Turnout      | Percentage of TotalPoll   	
 ValidPoll    | Valid Votes		   	
 SpoiledVotes | Bad Vote	           	
 Quot	       | Required amount to get a seat  

###Party
 Name         | Name of the Party  	
 Tag          | Tag for the Party  	
 Leader       | Current Party Leader	
 Translation  | English Translation    
 Founded      | Year Founded		
 Founder      | Name of Founder  	
 Ideology     | Political Ideal  	
 Position     | Left-Right Spectrum	




## Queries
Here are my 3 queries that I found to be interesting.

#### Youngest Candidate per Constituency
This query retreives the age of youngest candidate running in each Constituency
```cypher
MATCH 
    (c:Candidate)
RETURN 
   min(c.Age) AS Youngest_Candidate, c.Constituency
```

#### Shortest Path
This query finds the shorts path between Joan Burton and Mick Wallace
```cypher
MATCH (joan:Candidate { Name:"Joan Burton" }),(mick:Candidate { Name:"Mick Wallace" }),
  p = shortestPath((joan)-[*..15]-(mick))
RETURN p
```

#### Social Democrat Wins
Lists all the Social Democrat who won seats and the constituency that they won it in.
```cypher
match (a:Candidate),(b:Constituency)
where a.Tag = "sd" AND a.Won = b.Tag
return a,b;
```
### Notes
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
2. http://www.thejournal.ie/election-2016
2. http://elections.independent.ie/election-2016/kildare-south
2. http://stackoverflow.com/questions/33352673/why-does-neo4j-warn-this-query-builds-a-cartesian-product-between-disconnected
