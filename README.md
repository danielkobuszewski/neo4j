# Atividade Prática – Neo4j
# Exercise 1 - Retrieving Nodes
1.1 Retrieve all nodes from the database.
```
$match(n) return n
```
1.2 Examine the data model for the graph.
```
$call db.schema()
```
1.3 Retrieve all Person nodes.
```
$match(p:Person) return p
```
1.4 Retrieve all Movie nodes.
```
$match(m:Movie) return m
```
# Exercise 2 - Filtering queries using property values
2.1 Retrieve all movies that were released in a specific year.
```
$match(m:Movie{released:2003}) return m
```
2.2 View the retrieved results as a table.
```
{
  "title": "The Matrix Reloaded",
  "tagline": "Free your mind",
  "released": 2003
}
{
  "title": "The Matrix Revolutions",
  "tagline": "Everything that has a beginning has an end",
  "released": 2003
}
{
  "title": "Something's Gotta Give",
  "released": 2003
}
```

2.3 Query the database for all property keys.
```
$call db.propertyKeys()
```
2.4 Retrieve all Movies released in a specific year, returning their titles.
```
$match(m:Movie {released:2006}) return m.title
```
2.5 Display title, released, and tagline values for every Movie node in the graph.
```
$match(m:Movie) return m.title, m.released, m.tagline
```
2.6 Display more user friendly headers in the table
```
$match(m:Movie) return m.title as `Título`, m.released as `Lançamento`, m.tagline as `Tag Line`
```
# Exercise 3 - Filtering queries using relationships
3.1 Display the schema of the database.
```
$call db.schema()
```
3.2 Retrieve all people who wrote the movie Speed Racer.
```
$match (p:Person)-[rel:WROTE]->(m:Movie { title: 'Speed Racer'}) return p.name
```
3.3 Retrieve all movies that are connected to the person Tom Hanks.
```
$match(m:Movie)--(p:Person {name:'Tom Hanks'}) return m.title
```
3.4 Retrieve information about the relationships Tom Hanks had with the set of movies retrieved earlier.
```
$match(m:Movie)-[rel]-(:Person {name: 'Tom Hanks'}) return m.title, type(rel)
```
3.5 Retrieve information about the roles that Tom Hanks acted in.
```
$match(m:Movie)<-[rel:ACTED_IN]-(p:Person {name:'Tom Hanks'}) return m.title, rel.roles
```
# Exercise 4 - Filtering queries using the WHERE clause
4.1: Retrieve all movies that Tom Cruise acted in.
```
$match(m:Movie)<-[:ACTED_IN]-(p:Person) where p.name = 'Tom Cruise' return m.title
```
4.2: Retrieve all people that were born in the 70’s.
```
$match(p:Person) where p.born >= 1970 AND p.born < 1980 return p.name, p.born
```
4.3: Retrieve the actors who acted in the movie The Matrix who were born after 1960.
```
$match(p:Person)-[:ACTED_IN]->(m:Movie) where m.title = 'The Matrix' AND p.born > 1960 return p.name, p.born
```
4.4: Retrieve all movies by testing the node label and a property.
```
$match(m) where m:Movie and m.released = 1995 return m.title
```
4.5: Retrieve all people that wrote movies by testing the relationship between two nodes.
```
$match(p)-[rel]->(m) where p:Person AND type(rel) = 'WROTE' AND m:Movie return p.name, m.title
```
4.6: Retrieve all people in the graph that do not have a property.
```
$match(p:Person) where exists(p.born) = false return p.name
```
4.7 Retrieve all people related to movies where the relationship has a property.
```
$match(p:Person)-[rel]-(m:Movie) where exists(rel.rating) return p.name, m.title, m.rating
```
4.8: Retrieve all actors whose name begins with James.
```
$match(p:Person) where p.name STARTS WITH 'James' return p.name
```
4.9: Retrieve all all REVIEW relationships from the graph with filtered results.
```
$match(p:Person)-[rel:REVIEWED]->(m:Movie) where tolower(rel.summary) contains 'but' return m.title, rel.summary, rel.rating
```
4.10: Retrieve all people who have produced a movie, but have not directed a movie.
```
$match(p:Person)-[:PRODUCED]->(m:Movie) where not ((p)-[:DIRECTED]->(:Movie)) return p.name
```
4.11: Retrieve the movies and their actors where one of the actors also directed the movie.
```
$match (a:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(d:Person) where exists((d)-[:DIRECTED]->(m)) return a.name as Ator, d.name as `Ator/Diretor`, m.title
```
4.12: Retrieve all movies that were released in a set of years.
```
$match(m:Movie) where m.released in [1995, 1998, 2000] return m.title, m.released
```
4.13: Retrieve the movies that have an actor’s role that is the name of the movie. 
```
$match(p:Person)-[rel:ACTED_IN]->(m:Movie) where m.title in rel.roles return m.title, rel.roles, p.name
```
# Exercise 5 - Controlling query processing
5.1: Retrieve data using multiple MATCH patterns.
```
$match(a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person), (c:Person)-[:ACTED_IN]->(m) where a.name = 'Tom Hanks' return m.title as Ator, d.name as Diretor, c.name as `Co-Ator`
```
5.2: Retrieve particular nodes that have a relationship.
```
$match(p1:Person)-[:FOLLOWS]-(p2:Person) where p1.name = 'James Thompson' return p1, p2
```
5.3: Modify the query to retrieve nodes that are exactly three hops away.
```
$match(p1:Person)-[:FOLLOWS*3]-(p2:Person) where p1.name = 'James Thompson' return p1, p2
```
5.4: Modify the query to retrieve nodes that are one and two hops away.
```
$match(p1:Person)-[:FOLLOWS*1..2]-(p2:Person) where p1.name = 'James Thompson' return p1, p2
```
5.5: Modify the query to retrieve particular nodes that are connected no matter how many hops are required.
```
$match(p1:Person)-[:FOLLOWS*]-(p2:Person) where p1.name = 'James Thompson' return p1, p2
```
5.6: Specify optional data to be retrieved during the query.
```
$match(p:Person) where p.name STARTS WITH 'Tom' optional match(p)-[:DIRECTED]->(m:Movie) return p.name, m.title
```
5.7: Retrieve nodes by collecting a list.
```
$match(p:Person)-[:ACTED_IN]->(m:Movie) return p.name, collect(m.title) as Filmes
```
5.9: Retrieve nodes as lists and return data associated with the corresponding lists.
```
$match(p:Person)-[:REVIEWED]-(m:Movie) return m.title, count(p), collect(p.name)
```
5.10: Retrieve nodes and their relationships as lists.
```
$match(d:Person)-[:DIRECTED]->(m:Movie)<-[:ACTED_IN]-(a:Person) return d.name, count(distinct a) as QtdAtores, collect(distinct a.name) as ListaAtores
```
5.11: Retrieve the actors who have acted in exactly five movies.
```
$match(p:Person)-[:ACTED_IN]->(m:Movie) with p, count(p) as qtd, collect(m.title) as filmes where qtd = 5 return p.name, filmes
```
5.12: Retrieve the movies that have at least 2 directors with other optional data.
```
$match(m:Movie) with m, size((:Person)-[:DIRECTED]->(m)) as diretores where diretores >= 2 optional match(p:Person)-[:REVIEWED]-(m) return m.title, p.name
```
# Exercise 6 - Controlling results returned
6.1: Execute a query that returns duplicate records.
```
$match(p:Person)-[:ACTED_IN]-(m:Movie) where m.released >= 1990 and m.released < 2000 return distinct m.released, m.title, collect(p.name)
```
6.2: Modify the query to eliminate duplication.
```
$match(p:Person)-[:ACTED_IN]-(m:Movie) where m.released >= 1990 and m.released < 2000 return distinct m.released, collect(m.title), collect(p.name)
```
6.3: Modify the query to eliminate more duplication.
```
$match(p:Person)-[:ACTED_IN]-(m:Movie) where m.released >= 1990 and m.released < 2000 return distinct m.released, collect(distinct m.title), collect(p.name)
```
6.4: Sort results returned.
```
$match(p:Person)-[:ACTED_IN]-(m:Movie) where m.released >= 1990 and m.released < 2000 return distinct m.released, collect(distinct m.title), collect(p.name) order by m.released desc
```
6.5 Retrieve the top 5 ratings and their associated movies.
```
$match(m:Movie)<-[rel:REVIEWED]-(p:Person)return m.title, rel.rating order by rel.rating desc limit 5
```
6.6: Retrieve all actors that have not appeared in more than 3 movies.
```
$match(p:Person)-[:ACTED_IN]->(m:Movie) with p, count(m) as qtdFilmes, collect(m.title) as filmes where qtdFilmes <= 3 return p.name, filmes
```
# Exercise 7 - Working with Cypher data
7.1: Collect and use lists.
```
$match(a:Person)-[:ACTED_IN]->(m:Movie), (m)<-[:PRODUCED]-(p:Person) with m, collect(distinct a.name) as atores, collect(distinct p.name) as produtores return m.title, atores, produtores order by size(atores)
```
7.2: Collect a list.
```
$match(p:Person)-[:ACTED_IN]->(m:Movie) with p, collect(m) as filmes where size(filmes) > 5 return p.name, filmes
```
7.3: Unwind a list.
```
$match(p:Person)-[:ACTED_IN]->(m:Movie) with p, collect(m) as filmes where size(filmes) > 5 with p, filmes unwind filmes as filme return p.name, filme.title
```
7.4: Perform a calculation with the date type.
```
$match(p:Person)-[:ACTED_IN]->(m:Movie) where p.name = 'Tom Hanks' return m.title, m.released, date().year - m.released as anos, m.released - p.born as idade order by anos 
```
# Exercise 8 - Creating Nodes
8.1: Create a Movie node.
```
$create(:Movie {title: 'Forrest Gump'})
```
8.2: Retrieve the newly created node.
```
$match(m:Movie {title:'Forrest Gump'}) return m
```
8.3: Create a Person node.
```
$create(:Person {name:'Robin Wright'})
```
8.4: Retrieve the newly created node.
```
$match(p:Person {name:'Robin Wright'}) return p
```
8.5: Add a label to a node.
```
$match(m:Movie) where m.released < 2010 set m:OlderMovie return distinct labels(m)
```
8.6: Retrieve the node using the new label.
```
$match(m:OlderMovie) return m.title, m.released
```
8.7: Add the Female label to selected nodes.
```
$match(p:Person) where p.name starts with 'Robin' set p:Female
```
8.8: Retrieve all Female nodes.
```
$match(p:Female) return p.name
```
8.9: Remove the Female label from the nodes that have this label.
```
$match(p:Female) remove p:Female
```
8.10: View the current schema of the graph.
```
$call db.schema
```
8.11: Add properties to a movie.
```
$match(m:Movie) where m.title = 'Forrest Gump' set m:OlderMovie, m.released = 1994, m.tagline = 'Life is like a box of chocolates.. you never know what you`re gonna get.', m.lengthInMinutes = 142
```
8.12: Retrieve an OlderMovie node to confirm the label and properties.
```
$match(m:OlderMovie) where m.title = 'Forrest Gump' return m
```
8.13: Add properties to the person, Robin Wright.
```
$match(p:Person) where p.name = 'Robin Wright' set p.born = 1996, p.birthPlace = 'Dallas'
```
8.14: Retrieve an updated Person node.
```
$match(p:Person) where p.name = 'Robin Wright' return p
```
8.15: Remove a property from a Movie node.
```
$match(m:Movie) where m.title = 'Forrest Gump' set m.lengthInMinutes = null
```
8.16: Retrieve the node to confirm that the property has been removed.
```
$match(m:Movie) where m.title = 'Forrest Gump' return m
```
8.17: Remove a property from a Person node.
```
$match(p:Person) where p.name = 'Robin Wright' set p.birthPlace = null
```
8.18: Retrieve the node to confirm that the property has been removed.
```
$match(p:Person) where p.name = "Robin Wright" return p
```
# Exercise 9 - Creating Relationships
9.1: Create ACTED_IN relationships.
```
$match(m:Movie) where m.title = 'Forrest Gump' match(p:Person) where p.name = 'Tom Hanks' or p.name = 'Robin Wright' or p.name = 'Gary Sinise' create (p)-[:ACTED_IN]->(m)
```
9.2: Create DIRECTED relationships.
```
$match(m:Movie) where m.title = 'Forrest Gump' match(p:Person) where p.name = 'Robert Zemeckis' create (p)-[:DIRECTED]->(m)
```
9.3: Create a HELPED relationship.
```
$match(a:Person) where a.name = 'Tom Hanks' match(p:Person) where p.name = 'Gary Sinise' create(a)-[:HELPED]->(p)
```
9.4: Query nodes and new relationships.
```
$match(p:Person)-[rel]-(m:Movie) where m.title = 'Forrest Gump' return p, rel, m
```
9.5: Add properties to relationships.
```
$match(p:Person)-[rel:ACTED_IN]->(m:Movie) where m.title = 'Forrest Gump' set rel.roles = case p.name when 'Tom Hanks' then ['Forrest Gump'] when 'Robin Wright' then ['Jenny Curran'] when 'Gary Sinise' then ['Lieutenant Dan Taylor'] end
```
9.6: Add a property to the HELPED relationship.
```
$match(a:Person)-[rel:HELPED]->(p:Person) where a.name = 'Tom Hanks' and p.name = 'Gary Sinise' set rel.research = 'war history'
```
9.7: View the current list of property keys in the graph.
```
$call db.propertyKeys()
```
9.8: View the current schema of the graph.
```
$call db.schema()
```
9.9: Retrieve the names and roles for actors.
```
$match(p:Person)-[rel:ACTED_IN]->(m:Movie) where m.title = 'Forrest Gump' return rel.roles, p.name
```
9.10: Retrieve information about any specific relationships.
```
$match(a:Person)-[rel:HELPED]-(p:Person) return a.name, rel, p.name
```
9.11: Modify a property of a relationship.
```
$match(p:Person)-[rel:ACTED_IN]->(m:Movie) where m.title = 'Forrest Gump'and p.name = 'Gary Sinise' set rel.roles = 'Lt. Dan Taylor'
```
9.12: Remove a property from a relationship.
```
$match(a:Person)-[rel:HELPED]->(p:Person) where a.name = 'Tom Hanks' and p.name = 'Gary Sinise' remove rel.research
```
9.13: Confirm that your modifications were made to the graph.
```
$match(a:Person)-[rel:HELPED]->(p:Person) where a.name = 'Tom Hanks' and p.name = 'Gary Sinise' return a, rel, p
```
# Exercise 10 - Deleting Nodes and Relationships
10.1: Delete a relationship.
```
$match(a:Person)-[rel:HELPED]->(p:Person) delete rel
```
10.2: Confirm that the relationship has been deleted.
```
$match(a:Person)-[rel:HELPED]->(p:Person) return a, rel, p
```
10.3: Retrieve a movie and all of its relationships.
```
$match(p:Person)-[rel]->(m:Movie) where m.title = 'Forrest Gump' return p, rel, m
```
10.4: Try deleting a node without detaching its relationships.
```
$match(m:Movie) where m.title = 'Forrest Gump' delete m
```
10.5: Delete a Movie node, along with its relationships.
```
$match(m:Movie) where m.title = 'Forrest Gump' detach delete m
```
10.6: Confirm that the Movie node has been deleted.
```
$match(m:Movie) where m.title = 'Forrest Gump' return m
```
# Exercise 11 - Merging Data in the Graph
11.1: Use MERGE to create a Movie node
```
$merge(m:Movie {title: 'Forrest Gump'}) on create set m.released = 1994 return m
```
11.2: Use MERGE to update a node
```
$merge (m:Movie {title: 'Forrest Gump'}) on create set m.released = 1994 on match set m.tagline = 'Life is like a box of chocolates... you never know what you`re gonna get.' return m
```
11.3: Use MERGE to create a Production node
```
$merge (p:Production {title: 'Forrest Gump'}) on create set p.year = 1994 return p
```
11.4: Find all labels for nodes with a property value
```
$match (m) where m.title = 'Forrest Gump' return labels(m)
```
11.5: Use MERGE to update a Production node
```
$merge (p:Production {title: 'Forrest Gump'}) on match set p.company = 'Paramount Pictures' return p
```
11.6: Use MERGE to add a label to a node
```
$merge (m:Movie {title: 'Forrest Gump'}) on match set m:OlderMovie return labels(m)
```
11.7: Use MERGE to create two nodes and a single relationship 
```
$merge (p:Person {name: 'Robert Zemeckis'})-[:DIRECTED]->(m {title: 'Forrest Gump'})
```
11.8: Use the same MERGE statement to attempt to create two nodes and a single relationship
```
$11.8: Use the same MERGE statement to attempt to create two nodes and a single relationship
```
11.9: Find the correct Person node to delete
```
$match (p:Person {name: 'Robert Zemeckis'})-[rel]-(x) where not exists(p.born) return p, rel, x
```
11.10: Delete this Person node, along with its relationships
```
$match (p:Person {name:'Robert Zemeckis'})--() where not exists(p.born) detach delete p
```
11.11: Find the correct Forrest Gump node to delete
```
$match(m) where m.title = 'Forrest Gump' and labels(m) = [] return m, labels(m)
```
11.12: Delete the Forrest Gump node
```
$match(m) where m.title = 'Forrest Gump' and labels(m) = [] detach delete m
```
11.13: Use MERGE to create the DIRECTED relationship
```
$match(p:Person), (m:Movie) where p.name = 'Robert Zemeckis' and m.title = 'Forrest Gump' merge (p)-[:DIRECTED]->(m)
```
11.14: Use MERGE to create the ACTED_IN relationship
```
$match (p:Person), (m:Movie) where p.name in ['Tom Hanks', 'Gary Sinise', 'Robin Wright'] and m.title = 'Forrest Gump' merge (p)-[:ACTED_IN]->(m)
```
11.15: Modify the role relationship property
```
$match(p:Person)-[rel:ACTED_IN]->(m:Movie) where m.title = 'Forrest Gump' set rel.roles = case p.name when 'Tom Hanks' then 'Forrest Gump' when 'Robin Wright' then 'Jenny Curran' when 'Gary Sinise' then 'Lt. Dan Taylor' end
```
