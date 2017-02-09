---
title: Apache Tinkerpop
date: 2017-02-08 14:13:03
tags:
---

图是顶点（即节点、点）和边（即关系、线）的集合，其中顶点是表示某个域对象的实体（例如一个人、一个地方等），边代表两个顶点之间的关系。

TinkerPop是一个开源的图数据库程序的开发框架。Mallette回复了一个[帖子](http://stackoverflow.com/questions/20522547/what-is-tinkerpop)说明了了TinkerPop不是一种编写图应用程序的规范或标准。TinkerPop仅仅提供一系列的接口，图数据库和数据库供应商可以实现（Blueprints），获得TinkerPop的其余特性，支持基于图的应用程序开发。


## TinkerPop 资料

http://umlg.org/sqlg.html

## TinkerPop 入门

### Gremlin Console

下载[Gremlin Console](https://www.apache.org/dyn/closer.lua/tinkerpop/3.2.3/apache-tinkerpop-gremlin-console-3.2.3-bin.zip)

```bash
$ unzip apache-tinkerpop-gremlin-console-3.2.3-bin.zip
$ cd apache-tinkerpop-gremlin-console-3.2.3/
$ apache-tinkerpop-gremlin-console-3.2.3 bin/gremlin.sh

         \,,,/
         (o o)
-----oOOo-(3)-oOOo-----
plugin activated: tinkerpop.server
plugin activated: tinkerpop.utilities
plugin activated: tinkerpop.tinkergraph
gremlin>
```

### 简单遍历

使用`Gremlin Console`创建一个下面这样的图

![](/images/tinkerpop-modern.png)

使用下面的命令在`Gremlin Console`中实例化

创建了一个`graph`的图实例，仅仅提供了图的一些基本操作和数据。

```gremlin
gremlin> graph = TinkerFactory.createModern()
==>tinkergraph[vertices:6 edges:6]
```

还需要一个`TraversalSource`提供了额外的操作，例如遍历策略。

```gremlin
gremlin> g = graph.traversal()
==>graphtraversalsource[tinkergraph[vertices:6 edges:6], standard]
gremlin>
```

获取所有顶点

```gremlin
gremlin> g.V()
==>v[1]
==>v[2]
==>v[3]
==>v[4]
==>v[5]
==>v[6]
```

获取id=1的顶点

```gremlin
gremlin> g.V(1)
==>v[1]
```

获取id=1的顶点 name 属性的值

```gremlin
gremlin> g.V(1).values('name')
==>marko
```

获取id=1的订单的所有指向节点，且包含标签knows的边

```gremlin
gremlin> g.V(1).outE('knows')
==>e[7][1-knows->2]
==>e[8][1-knows->4]
```

获取id=1的订单的所有指向节点，且包含标签knows的边指向顶点的 name 属性的值

```gremlin
gremlin> g.V(1).outE('knows').inV().values('name')
==>vadas
==>josh
```

`.out()` == `.outE().inV()`

```gremlin
gremlin> g.V(1).out('knows').values('name')
==>vadas
==>josh
```

获取id=1的订单的所有指向节点，且包含标签knows的边指向的顶点，且age > 30的顶点的 name 属性的值

```gremlin
gremlin> g.V(1).out('knows').has('age', gt(30)).values('name')
==>josh
```

### 复杂遍历

```gremlin
gremlin> graph = TinkerFactory.createModern()
==>tinkergraph[vertices:6 edges:6]
gremlin> g = graph.traversal()
==>graphtraversalsource[tinkergraph[vertices:6 edges:6], standard]
gremlin> g.V().has('name',within('vadas','marko')).values('age')
==>29
==>27
gremlin> g.V().has('name',within('vadas','marko')).values('age').mean()
==>28.0
gremlin> g.V().has('name','marko').out('created')
==>v[3]
gremlin> g.V().has('name','marko').out('created').in('created').values('name')
==>marko
==>josh
==>peter
gremlin> g.V().has('name','marko').as('exclude').out('created').in('created').where(neq('exclude')).values('name')
==>josh
==>peter
gremlin> g.V().as('a').out().as('b').out().as('c').select('a','b','c')
==>[a:v[1],b:v[4],c:v[5]]
==>[a:v[1],b:v[4],c:v[3]]
gremlin> g.V().group().by(label)
==>[software:[v[3],v[5]],person:[v[1],v[2],v[4],v[6]]]
gremlin> g.V().group().by(label).by('name')
==>[software:[lop,ripple],person:[marko,vadas,josh,peter]]
```


### 创建一个图

![](/images/modern-edge-1-to-3-1.png)

上图展示了一个包含两个顶点的图，分别是`id=1`，`id=3`。边的`id=9`，并且边有个方向`1->3`。

只有顶点和边的图，并没有什么意义，所以接下来我们给这些顶点和边赋予一些业务上的含义。

![](/images/modern-edge-1-to-3-2.png)

还可以给这些实体增加一些标签

![](/images/modern-edge-1-to-3-3.png)

这种模式可以直观的表示数据，使用下面的命令来创建这个图：

```gremlin
gremlin> graph = TinkerGraph.open()
==>tinkergraph[vertices:0 edges:0]
gremlin> v1 = graph.addVertex(T.id, 1, T.label, "person", "name", "marko", "age", 29)
==>v[1]
gremlin> v2 = graph.addVertex(T.id, 3, T.label, "software", "name", "lop", "lang", "java")
==>v[3]
gremlin> v1.addEdge("created", v2, T.id, 9, "weight", 0.4)
==>e[9][1-created->3]
```

`T.id` 和 `T.label` 是保留关键字，可以将`T`看做一个静态变量。

### 遍历自己的图

```gremlin
gremlin> g = graph.traversal();
==>graphtraversalsource[tinkergraph[vertices:2 edges:1], standard]
gremlin> g.V().has("name", "marko")
==>v[1]
gremlin> g.V().has('name','marko').outE('created')
==>e[9][1-created->3]
gremlin> g.V().has('name','marko').outE('created').inV()
==>v[3]
gremlin> g.V().has('name','marko').out('created')
==>v[3]
gremlin> g.V().has('name','marko').out('created').values('name')
==>lop
```

### 加载数据

```bash
$ curl -L -O http://snap.stanford.edu/data/wiki-Vote.txt.gz
$ gunzip wiki-Vote.txt.gz
```

```groovy
graph = TinkerGraph.open()
graph.createIndex('userId', Vertex.class) (1)

g = graph.traversal()

getOrCreate = { id ->
  g.V().has('userId', id).tryNext().orElseGet{ g.addV('userId', id).next() }
}

new File('wiki-Vote.txt').eachLine {
  if (!it.startsWith("#")){
    (fromVertex, toVertex) = it.split('\t').collect(getOrCreate) (2)
    fromVertex.addEdge('votesFor', toVertex)
  }
} } }
```

在`Gremlin Console`里的运行结果如下：

```gremlin
➜  apache-tinkerpop-gremlin-console-3.2.3 bin/gremlin.sh

         \,,,/
         (o o)
-----oOOo-(3)-oOOo-----
plugin activated: tinkerpop.server
plugin activated: tinkerpop.utilities
plugin activated: tinkerpop.tinkergraph
gremlin> graph = TinkerGraph.open()
==>tinkergraph[vertices:0 edges:0]
gremlin> graph.createIndex('userId', Vertex.class)
==>null
gremlin> g = graph.traversal()
==>graphtraversalsource[tinkergraph[vertices:0 edges:0], standard]
gremlin>
gremlin> getOrCreate = { id ->
......1>     g.V().has('userId', id).tryNext().orElseGet{ g.addV('userId', id).next() }
......2> }
==>groovysh_evaluate$_run_closure1@323e8306
gremlin> new File('../wiki-Vote.txt').eachLine {
......1>     if (!it.startsWith("#")){
......2>         (fromVertex, toVertex) = it.split('\t').collect(getOrCreate)
......3>         fromVertex.addEdge('votesFor', toVertex)
......4>     }
......5> }
==>e[117918][117916-votesFor->44907] } }
```

1. 首先为了快速查询节点，建立索引

2. 在`wiki-Vote.txt`的每一行使用`\t`区分`from`、`to`顶点

3. 加载更大的数据集，建议阅读[BulkLoaderVertexProgram(BLVP)](http://tinkerpop.apache.org/docs/3.2.3/reference/#bulkloadervertexprogram)，提供了一种可以加载任意大小数据的方法。

### Gremlin Server

下载[Gremlin Server](https://www.apache.org/dyn/closer.lua/tinkerpop/3.2.3/apache-tinkerpop-gremlin-server-3.2.3-bin.zip)

```bash
$ unzip apache-tinkerpop-gremlin-server-3.2.3-bin.zip
$ cd apache-tinkerpop-gremlin-server-3.2.3
$ bin/gremlin-server.sh
[INFO] GremlinServer -
         \,,,/
         (o o)
-----oOOo-(3)-oOOo-----

[INFO] GremlinServer - Configuring Gremlin Server from conf/gremlin-server.yaml
[INFO] MetricManager - Configured Metrics ConsoleReporter configured with report interval=180000ms
[INFO] MetricManager - Configured Metrics CsvReporter configured with report interval=180000ms to fileName=/tmp/gremlin-server-metrics.csv
[INFO] MetricManager - Configured Metrics JmxReporter configured with domain= and agentId=
[INFO] MetricManager - Configured Metrics Slf4jReporter configured with interval=180000ms and loggerName=org.apache.tinkerpop.gremlin.server.Settings$Slf4jReporterMetrics
[INFO] GraphManager - Graph [graph] was successfully configured via [conf/tinkergraph-empty.properties].
[INFO] ServerGremlinExecutor - Initialized Gremlin thread pool.  Threads in pool named with pattern gremlin-*
[INFO] ScriptEngines - Loaded gremlin-groovy ScriptEngine
[INFO] GremlinExecutor - Initialized gremlin-groovy ScriptEngine with scripts/empty-sample.groovy
[INFO] ServerGremlinExecutor - Initialized GremlinExecutor and configured ScriptEngines.
[INFO] ServerGremlinExecutor - A GraphTraversalSource is now bound to [g] with graphtraversalsource[tinkergraph[vertices:0 edges:0], standard]
[INFO] OpLoader - Adding the standard OpProcessor.
[INFO] OpLoader - Adding the control OpProcessor.
[INFO] OpLoader - Adding the session OpProcessor.
[INFO] OpLoader - Adding the traversal OpProcessor.
[INFO] TraversalOpProcessor - Initialized cache for TraversalOpProcessor with size 1000 and expiration time of 600000 ms
[INFO] GremlinServer - Executing start up LifeCycleHook
[INFO] Logger$info - Executed once at startup of Gremlin Server.
[INFO] AbstractChannelizer - Configured application/vnd.gremlin-v1.0+gryo with org.apache.tinkerpop.gremlin.driver.ser.GryoMessageSerializerV1d0
[INFO] AbstractChannelizer - Configured application/vnd.gremlin-v1.0+gryo-lite with org.apache.tinkerpop.gremlin.driver.ser.GryoLiteMessageSerializerV1d0
[INFO] AbstractChannelizer - Configured application/vnd.gremlin-v1.0+gryo-stringd with org.apache.tinkerpop.gremlin.driver.ser.GryoMessageSerializerV1d0
[INFO] AbstractChannelizer - Configured application/vnd.gremlin-v1.0+json with org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerGremlinV1d0
[INFO] AbstractChannelizer - Configured application/vnd.gremlin-v2.0+json with org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerGremlinV2d0
[INFO] AbstractChannelizer - Configured application/json with org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0
[INFO] GremlinServer$1 - Gremlin Server configured with worker thread pool of 1, gremlin pool of 4 and boss thread pool of 1.
[INFO] GremlinServer$1 - Channel started at port 8182.
```

## TinkerPop

TinkerPop 支持的features

```gremlin
gremlin> graph = TinkerGraph.open()
==>tinkergraph[vertices:0 edges:0]
gremlin> graph.features()
==>FEATURES
> GraphFeatures
>-- Persistence: true
>-- ConcurrentAccess: false
>-- ThreadedTransactions: false
>-- Transactions: false
>-- Computer: true
> VariableFeatures
>-- Variables: true
>-- FloatValues: true
>-- IntegerValues: true
>-- LongValues: true
>-- MapValues: true
>-- MixedListValues: true
>-- SerializableValues: true
>-- StringValues: true
>-- UniformListValues: true
>-- BooleanArrayValues: true
>-- ByteArrayValues: true
>-- DoubleArrayValues: true
>-- FloatArrayValues: true
>-- IntegerArrayValues: true
>-- LongArrayValues: true
>-- StringArrayValues: true
>-- BooleanValues: true
>-- ByteValues: true
>-- DoubleValues: true
> VertexFeatures
>-- RemoveVertices: true
>-- MultiProperties: true
>-- AddVertices: true
>-- MetaProperties: true
>-- AddProperty: true
>-- RemoveProperty: true
>-- NumericIds: true
>-- StringIds: true
>-- UuidIds: true
>-- CustomIds: false
>-- AnyIds: true
>-- UserSuppliedIds: true
> VertexPropertyFeatures
>-- AddProperty: true
>-- RemoveProperty: true
>-- NumericIds: true
>-- StringIds: true
>-- UuidIds: true
>-- CustomIds: false
>-- AnyIds: true
>-- UserSuppliedIds: true
>-- Properties: true
>-- FloatValues: true
>-- IntegerValues: true
>-- LongValues: true
>-- MapValues: true
>-- MixedListValues: true
>-- SerializableValues: true
>-- StringValues: true
>-- UniformListValues: true
>-- BooleanArrayValues: true
>-- ByteArrayValues: true
>-- DoubleArrayValues: true
>-- FloatArrayValues: true
>-- IntegerArrayValues: true
>-- LongArrayValues: true
>-- StringArrayValues: true
>-- BooleanValues: true
>-- ByteValues: true
>-- DoubleValues: true
> EdgeFeatures
>-- AddEdges: true
>-- RemoveEdges: true
>-- AddProperty: true
>-- RemoveProperty: true
>-- NumericIds: true
>-- StringIds: true
>-- UuidIds: true
>-- CustomIds: false
>-- AnyIds: true
>-- UserSuppliedIds: true
> EdgePropertyFeatures
>-- Properties: true
>-- FloatValues: true
>-- IntegerValues: true
>-- LongValues: true
>-- MapValues: true
>-- MixedListValues: true
>-- SerializableValues: true
>-- StringValues: true
>-- UniformListValues: true
>-- BooleanArrayValues: true
>-- ByteArrayValues: true
>-- DoubleArrayValues: true
>-- FloatArrayValues: true
>-- IntegerArrayValues: true
>-- LongArrayValues: true
>-- StringArrayValues: true
>-- BooleanValues: true
>-- ByteValues: true
>-- DoubleValues: true
```

Blueprints支持的存储

TinkerGraph (TinkerGraph) 
Neo4j Implementation (Neo4jGraph) 
Neo4jHA Implementation (Neo4jHaGraph) 
Neo4jBatch Implementation (Neo4jBatchGraph) 
Sail Implementation (SailGraph) 
Sparksee Implementation (SparkseeGraph) 
Rexster Implementation (RexsterGraph) 
Accumulo Implementation (AccumuloGraph – 3rd party – implements Blueprints 2.4.0) 
ArangoDB Implementation (ArangoDBGraph – 3rd party – implements Blueprints 2.3.0) 
Bitsy (BitsyGraph – 3rd party – implements Blueprints 2.4.0) 
Bigdata (BigdataGraph – 3rd party – implements Blueprints 2.5.0) 
FluxGraph - Datomic Implementation (FluxGraph – 3rd party – implements Blueprints 2.1.0) 
FoundationDB Implementation (FoundationDBGraph – 3rd party – implements Blueprints 2.4.0) 
InfiniteGraph Implementation (IGGraph – 3rd party – implements Blueprints 2.1.0) 
JPA Implementation (JpaGraph – 3rd party – implements Blueprints 2.5.0) 
MongoDB Implementation (MongoDBGraph – 3rd party – implements Blueprints 2.3.0) 
Oracle NoSQL Implementation (KVGraph – 3rd party – implements Blueprints 2.1.0) 
OrientDB Implementation (OrientGraph – 3rd party – implements Blueprints 2.4.0) 
SQL/JDBC Implementation – 3rd party – implements Blueprints 2.4.0) 
Titan Implementation (TitanGraph – 3rd party – implements Blueprints 2.3.0)


