# NEO4J
---

## SOBRE:

O Neo4j é um **NoSQL** que utiliza grafos para fazer a relação entre entidades que é muito utilizado para relacionar os gostos do comprador (em um e-commercer), redes socias (recomendação de novas amizades) e determinação de rotas como o caso do Uber.

## QUANDO NÃO USAR?

Mesmo sendo um bando de dados robusto e prático, o Neo4j possui a problemática de que, a utilização de um grafo muito grande, para fazer as consultas diárias, faz com que a aplicação se torne lenta.

## ESTRUTURA:

* Nós (nodes): Representam entidades ou objetos. Exemplo: uma pessoa, um produto e entre outros.

* Relações (relationships): Conectam nós e indicam como eles se relacionam. Exemplo: pessoa A **"conhece"** pessoa B, pessoa A **"comprou"** produto A.

* Propriedades (Properties): Tanto nós quanto relacionamentos podem ter atributos (chave-valor) para armazenar mais detalhes. Exemplo: idade, sexo, profissão e entre outros.

## COMO USAR?

Ao entrar no Neo4j Desktop, criamos um novo projeto
![image](https://github.com/user-attachments/assets/56642bc6-34be-4729-bf6b-8180bf3fbee1)

iniciamos um banco de dados gráfico e criamos a senha
![image](https://github.com/user-attachments/assets/934274ed-86a7-4ef9-a156-ea950e5306f8)

Com o banco criado, damos start no projeto e abrimos o Neo4j browser para utilizar o cypher. (Cypher é a linguagem de consulta do Neo4j. É semelhante ao SQL, mas voltada para grafos.)
![image](https://github.com/user-attachments/assets/0c422ea5-573b-4a14-acb1-c02087dc815e)



### CRIANDO NÓS

```cypher
// a:Person -> Nó com rótulo Person
// {name: "Alice", age: 30} -> criação de duas entidades: name e age
CREATE (a:Person {name: "Alice", age: 30})
```

```cypher
// a: Pet -> Nó com rótulo Pet
// {name: "Hulk", age: 3, race: "bulldog"} criação de três entidades
CREATE (a: Pet {name: "Hulk", age: 3, race: "bulldog"})
```

### RETORNANDO NÓS

```cypher
// Irá retornar todos os nós com o rótulo Person que possuem o nome Alice
MATCH (p:Person {name: "Alice"})
RETURN p
```
![image](https://github.com/user-attachments/assets/96bc8101-ed0b-4c10-baf7-45dbd6ec07bb)


```cypher
// Irá retornar todos os nós com o rótulo Person
MATCH (p:Person) 
RETURN p
```
![image](https://github.com/user-attachments/assets/407939a1-e78b-4bae-a9e2-72525b11c41a)

### CRIANDO RELACIONAMENTOS

```cypher
// MATCH: Localiza os nós que já foram criados (nesse caso, Alice e Bob).
// -[:KNOWS]->: Cria um relacionamento chamado `KNOWS` entre `a` (Alice) e `b` (Bob)
MATCH (a:Person {name: "Alice"}), (b:Person {name: "Bob"})
CREATE (a)-[:KNOWS]->(b)
```
![image](https://github.com/user-attachments/assets/2807354f-926d-4178-aaad-4e0742f50e92)


```cypher
// Cria um relacionamento com a propriedade since
MATCH (a:Person {name: "Alice"}), (b:Person {name: "Bob"})
CREATE (a)-[r:KNOWS {since: 2015}]->(b)
RETURN a, b, r
```
### CONSULTANDO RELACIONAMENTOS

```cypher
// Aqui, estamos buscando todas as pessoas que têm um relacionamento `KNOWS` com outra pessoa. A consulta retorna os dois nós e o relacionamento entre eles.
MATCH (a:Person)-[r:KNOWS]->(b:Person) 
RETURN a, r, b
```

### ATUALIZANDO PROPRIEDADES

```cypher
// Aqui, estamos localizando o nó `Gabriel` e atualizando sua propriedade `age` para 26.
MATCH (p:Person {name: "Gabriel"}) 
SET p.age = 26
```

```cypher
// Agora, o nó `Alice` também terá a propriedade `city`, cujo qual ainda não existia.
MATCH (p:Person {name: "Alice"}) 
SET p.city = "New York"
```

### DELETANDO NÓS E RELACIONAMENTOS

```cypher
// Isso remove o relacionamento `KNOWS` entre `a` e `b`.
MATCH (a:Person)-[r:KNOWS]->(b:Person)
DELETE r
```

```cypher
// Aqui deletamos o nó Alice. Só funcionará se o nó não tiver relacionado
MATCH (p:Person {name: "Alice"})
DELETE p
```

### FILTRANDO RESULTADOS

```cypher
// Utilizamos WHERE para retornar todas as pessoas cuja idade é maior que 25.
MATCH (p:Person) 
WHERE p.age > 25 
RETURN p
```

```cypher
// Aqui, estamos buscando pessoas que se conhecem antes de 2020.
// Nesse caso, since é uma propriedade de r que representa o ano em que a relação foi estabelecida
MATCH (a:Person)-[r:KNOWS]->(b:Person) 
WHERE r.since < 2020 
RETURN a, b
```

### REMOVENDO RÓTULOS E PROPRIEDADES

```cypher
// Isso remove a propriedade `city` do nó `Alice`.
MATCH (p:Person {name: "Alice"}) 
REMOVE p.city
```

```cypher
// Isso remove o rótulo `Person` do nó `Alice`.
MATCH (p:Person {name: "Alice"}) 
REMOVE p:Person
```

### VISUALIZANDO O ESQUEMA DO BANCO DE DADOS

**Observação: Estou utilizando o conjunto de dados carregado do próprio Neo4j, o banco de recomendações de filmes**

```cypher
// Função para visualizar a estrutura dos tipos de nós e os relacionamentos existentes entre eles.
CALL db.schema.visualization
```

### CLÁUSULA WITH

```cypher
// Aqui, o WITH passa a pessoa (p) e a contagem de livros lidos (booksRead) para a próxima etapa.
MATCH (p:Person)-[:HAS_READ]->(b:Book)
WITH p, count(b) AS booksRead
WHERE booksRead > 3
RETURN p.name, booksRead
```

```cypher
// Agrupa os livros pelo título (b.title) e conta o número de pessoas que os leram (readers).
MATCH (p:Person)-[:HAS_READ]->(b:Book)
WITH b.title AS book, count(p) AS readers
RETURN book, readers
```

```cypher
// Aqui, a contagem de amigos é usada para filtrar pessoas antes de buscar os livros lidos.
MATCH (p:Person)-[:IS_FRIENDS_WITH]->(f:Person)
WITH p, count(f) AS friendsCount
WHERE friendsCount > 5
MATCH (p)-[:HAS_READ]->(b:Book)
RETURN p.name, b.title
```

```cypher
// Pode ser combinado com DISTINCT para remover duplicatas em uma etapa intermediária.
MATCH (p:Person)-[:IS_FRIENDS_WITH]->(f:Person)
WITH DISTINCT p
MATCH (p)-[:HAS_READ]->(b:Book)
RETURN p.name, b.title
```

```cypher
// Ordenar ou limitar os dados antes de passá-los adiante
MATCH (p:Person)-[:HAS_READ]->(b:Book)
WITH p, count(b) AS booksRead
ORDER BY booksRead DESC
LIMIT 10
RETURN p.name, booksRead
```

```cypher
// Encontra o nó do tipo Actor com o nome "Eric Campbell" e os filmes (m:Movie) nos quais ele atuou, por meio do relacionamento [:ACTED_IN]
MATCH (a:Actor {name: "Eric Campbell"})-[:ACTED_IN]->(m:Movie)
// Passa os filmes (m) encontrados na etapa anterior para a próxima parte da consulta e as variáveis que não são explicitamente listadas no WITH são descartadas
WITH m
// Para cada filme (m) encontrado na etapa anterior, busca todos os nós do tipo User (node2) que têm qualquer relacionamento com o filme, independentemente do tipo ou da profundidade ([*] indica um relacionamento de qualquer tipo e comprimento)
MATCH (m)<-[*]-(node2:User)
// Agrupa todos os nós User (node2) encontrados na etapa anterior em uma lista chamada nodes.
// A função COLLECT é usada para reunir múltiplos valores em uma lista, facilitando o retorno ou processamento conjunto.
WITH COLLECT(node2) AS nodes
RETURN nodes
```

### DETACH DELETE

```cypher
// Remove o nó e todos os relacionamentos conectados a ele.
MATCH (n:Label {property: value})
DETACH DELETE n
```
**Observação: Se usado sem critérios específicos, pode excluir uma grande parte do grafo. Após a exclusão, os dados não podem ser recuperados, a menos que exista um backup.**

```cypher
// Remove todos os nós e relacionamentos
MATCH (n)
DETACH DELETE n
```
