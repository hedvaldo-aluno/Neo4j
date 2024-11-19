```cypher
CALL db.schema.visualization
```
![image](https://github.com/user-attachments/assets/89cbdbef-e8b6-4d55-9c2b-9245b939fbb7)

```cypher
MATCH (n: Actor) return n limit 100
```
![image](https://github.com/user-attachments/assets/b68e737b-e051-46e6-a694-b880b19528fb)

```cypher
MATCH (n:Actor {name: "Emil Jannings"})-[:ACTED_IN]->(m:Movie) RETURN n.name, m.title
```
![image](https://github.com/user-attachments/assets/fafd23ba-91e8-4c94-bbf9-5baced28a637)

```cypher

```
```cypher

```
```cypher

```
```cypher

```
```cypher

```
```cypher

```
