# morpheus-neo4j-example
This repository contains a usecase of [morpheus](https://github.com/opencypher/morpheus), the project from [opencypher](https://www.opencypher.org/) previously known as [Cypher](https://neo4j.com/developer/cypher-query-language/) for [Apache Spark](https://spark.apache.org).

### Motivations
The motivation behind this tutorial is the fact that [Neo4j](https://neo4j.com/) is a great playground for cypher queries, but it allows for quering only one graph at a time.
However, cypher allows for quering on multiple graphs. 
With morpheus you can run cypher queries on multiple graphs.

### Usecase

The Sirius Cybernetics Corporation offers real-time geo-marketing services to shop owners to increase their sales by distributing instantaneous discount coupons to potential shoppers nearby. Alice and Bob, who respectively own shops A and B, decided to try that service. We can represent those facts in the following graph <i>g<sub>shops</sub></i>.

```
CREATE (:Person {name:'Alice'})-[:OWNS]->(:Shop {name:'Shop A'})
CREATE (:Person {name:'Bob'})-[: OWNS]->(:Shop {name:'Shop B'})
```

The Sirius Cybernetics Corporation offers for free-download a mobile App that delivers instantaneous discount coupons to shoppers while they are near by shops like A and B. The shoppers Carl, Diana and Eve have such an App on their mobiles. When they are within 200 meters from A or B, the App records it in the graph <i>g<sub>nearby</sub></i>.

```
CREATE (a:Shop {name:'Shop A'})
CREATE (b:Shop {name:'Shop B'})
CREATE (:Person {name:'Diana'})-[:IS_NEAR_BY]->(a)
CREATE (:Person {name:'Eve'})-[:IS_NEAR_BY]->(b)
CREATE (:Person {name:'Carl'})-[:IS_NEAR_BY]->(a)
```

The instantaneous discount coupon service offered by the Sirius Cybernetics Corporation allows shop owners to propose discounts to shoppers nearby, who use the App associated to the service, e.g., when their shops are empty. Those instantaneous stored in the graph <i>g<sub>coupons</i></sub>.


```
CREATE (:Person {name:'Alice'})-[:OFFERS]->(:Discount {desc:'10%'})
CREATE (:Person {name:'Bob')-[: OFFERS]->(:Discount {desc:'Free coffee!'})
```

Sirius Cybernetics Corporation monitors the graph <i>g<sub>nearby</sub></i>, the graph <i>g<sub>coupons</i></sub>., and the graph <i>g<sub>shops</sub></i> to send coupons proposed by the shop owners to shoppers nearby their shops. This query involves multiple graphs and is represented by the following cyoher query.

```
FROM GRAPH gshops.graph
MATCH (o1:Person)-[:OWNS]->(s1:Shop)
FROM GRAPH gcoupons.graph
MATCH (o2:Person)-[:OFFERS]->(c:Coupon)
WHERE o1.name = o2.name
FROM GRAPH gnearby.graph
MATCH (t:Person)-[:IS_NEAR_BY]->(s2:Shop)
WHERE s1.name = s2.name
RETURN t, c
```

### Usage
```bash
docker-compose up -d
```

#### Apache Zeppelin
The web interface is available at [localhost:8080](localhost:8080)

#### Neo4j
The example is based on four instances of Neo4j databases. You can access the web interfaces at [localhost:7474](localhost:7474), [localhost:7475](localhost:7475), [localhost:7476](localhost:7476) and  [localhost:7477](localhost:7477). For more details look at ```docker-compose.yml``` file.

### Acknowledgements
This usecase is inspired by the example reported in [RSP-QL Semantics: A Unifying Query Model to Explain Heterogeneity of RDF Stream Processing Systems](http://dx.doi.org/10.4018/ijswis.2014100102).
