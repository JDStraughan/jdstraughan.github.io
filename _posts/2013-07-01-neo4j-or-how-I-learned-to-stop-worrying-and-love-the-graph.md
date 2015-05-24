---
layout: post
published: true
title: Introduction to Graph Databases using Neo4j
subtitle: or how I learned to stop worrying and love the graph
tags: ["neo4j", "database", "graph"]
description: "For those that are unfamiliar with graph databases, they do not store graphs in the chart sense, but rather graph structures."
header-img: "img/header/Internet_map_1024.jpg"
image-credit: The Opte Project [<a href="http://creativecommons.org/licenses/by/2.5/deed.en">CC Attribution 2.5 Generic</a>], <a href="http://en.wikipedia.org/wiki/File:Internet_map_1024.jpg">from Wikimedia</a>
---
At just that moment when I was convinced the social networking revolution was no more enlightening than reality television, I began to realize that there was one great and powerful idea that needed this exact environment to gestate: the graph database. From Facebook's Graph Search, to LinkedIn's connection recommendations, graph databases are shaping the world around us.  Although graph theory has been around for centuries, its penchant for highly-related datasets makes it an attractive alternative to traditional RDBMS solutions in some situations.

###What is a graph database?

For those that are unfamiliar with graph databases, they do not store graphs in the chart sense, but rather <a href="http://en.wikipedia.org/wiki/Graph_(abstract_data_type)">graph structures</a>. The three moving parts are nodes, properties, and relationships (edges).  These can be visualized nicely in this image from wikimedia:

![graph example](http://upload.wikimedia.org/wikipedia/commons/3/3a/GraphDatabase_PropertyGraph.png)

Just by looking at this first example, we can see that even representing a graph looks much different than the tables with relationship arrows that are generally associated with visualizing relational databases.

The circles in this diagram are referred to as **nodes**. Each node has an identifier, and then a set of **properties** represented as key/value pairs. Further inspection of these nodes reveal they are schemaless, with 2 of the nodes containing name and age properties, and one of the nodes containing type and name properties.  This is common in other NoSQL databases, so we've not seen anything unique so far.

Next we can see the arrow pointing from one node to another.  These arrows are **relationships** between the nodes.  Relating database records is common in both the NoSQL and SQL worlds. In our graph example, the astute observer would notice these relationships also have properties that are represented as key/value pairs.  Like their node counterparts, they are also schemaless, allowing arbitrary keys on different relationships.  It is worth noting, in the graph world, these relationships are referred to as **edges**.

###Why use a graph database?

The most often use case given as an example for graphs is social networking.  This is also the arena that has (re)introduced graph structures to developers, however, graphs also offer a solution for modeling other data, and can be a powerful tool for recommendations, ratings, and highly interconnected data.

Once we begin to understand how the graph works, we can begin to see uses for it everywhere.  Imagine dungeon games, where map locations, guild relationships, and other related data lives in a graph structure.  Family trees also come to mind, where you can traverse a <a href="http://en.wikipedia.org/wiki/Tree_(data_structure)">tree shaped graph</a> to learn more about one's heritage.

Traversal is the way we *walk* graph structures, and in a tree shaped result, it is referred to as <a href="http://en.wikipedia.org/wiki/Tree_(data_structure)#Traversal_methods">walking the tree</a>.

###Walking trees (and other fun shapes)

Traversal is simply the process of working from one node to the next, using relationships, based on whatever parameters have been established. [Tree traversal](http://en.wikipedia.org/wiki/Tree_traversal) is more complex than *walking* a linear path, like steps 1 - 5, that can easily be visited in order.  With a tree shaped data structure, you can choose paths to visit every node, or find paths based on other criteria to return subsets of the total structure.

![example of tree graph](http://upload.wikimedia.org/wikipedia/commons/thumb/7/77/Sorted_binary_tree_inorder.svg/336px-Sorted_binary_tree_inorder.svg.png)

This is an example of a binary tree, being traversed in order.  Following the dotted line, we can see how the tree is traversed, or walked, to get all the nodes along the edges.

Shapes refer to named structures found in graphs.  Most are named after the way they look when visualized.  Popular *shapes* include trees, stars, wheels, and [many others](http://en.wikipedia.org/wiki/Gallery_of_named_graphs).

![example of star graph](http://upload.wikimedia.org/wikipedia/commons/7/7d/Star_graphs.svg)

A <a href="http://en.wikipedia.org/wiki/Star_(graph_theory)">star graph</a> shows one node, with all the nodes directly connected to it via an edge (relationship).

Now that we've reviewed the basics of graphs and their structures, lets take a look at using them in an actual database.

###Introducing neo4j

There are several graph databases available to choose from, but the one I've worked with, and seems to have the most popularity right now, is neo4j. Neo4j's API can be accessed via Java, JPython, JRuby, PHP, .NET, Clojure, and REST.  There are several choices for querying neo4j, with cypher, neo4j's query language, being the most prominent. Neo4j ships with a data browser, uses ACID transactions, and scales via replication.  For a more in depth view, I'd encourage [visiting their website](http://www.neo4j.org/).

####Learning neo4j

The neo4j team and community have done a great job of supplying educational material and [sample datasets](http://www.neo4j.org/develop/example_data) for exploration. At the time of this blog post, they are even giving away a [free copy of O'Reilly's new book: Graph Databases](http://www.neo4j.org/learn).  The [video section](http://www.neo4j.org/learn/videos) of their site is very informative, with the [webinars](http://www.neo4j.org/learn/videos_webinar) particularly useful.

####Querying with Cypher

The cypher query language is unique to neo4j, and while it is very different than other database query languages, I have found it to be quite expressive and easy to use.  The basics are simple: each query has a start, a match, and a return.  Traversing a graph requires knowing where you want to begin (the start node), the rules that will allow traversal (the match segment) and what data you are expecting back (the return).

A simple cypher query looks something like this:

    START n
    MATCH n-[r]-m
    RETURN r;

Before we can show an actual query that retrieves data, we need to load some nodes and relationships into our graph.

Creating new nodes and relationships are also fairly straightforward. To create the graph example we've used above in neo4j (version 1.9) using cypher, we would need the following queries to create the nodes:

    CREATE (n { name : 'Alice' , age : 18 });
    CREATE (n { name : 'Bob' , age : 22 });
    CREATE (n { type : 'Group' , name : 'Chess' });

These 3 queries each create a new node with properties.  The first 2 create people, the last one creates the "Chess" group.  Each query would also assign an internal ID to each node.

Given the knowledge that Bob's node has an ID of 1, and Alice has an ID of 2, we can use these queries to create the relationships:

    START alice = node(1), bob = node(2)
    CREATE alice-[r:KNOWS { since:"2001/10/03"}]->bob
    RETURN r;

    START alice = node(1), bob = node(2)
    CREATE bob-[r:KNOWS { since:"2001/10/04"}]->alice
    RETURN r;

Here we see 2 relationships defined: one from Alice to Bob, and another that relates Bob to Alice.  In neo4j relationships are unidirectional, so to represent the graph as described in our example image above, we needed to create 2 relationships.

Given that the group Chess has a node ID of 3, we can relate Bob and Alice to the Chess group as follows:

    START alice = node(1), chess_group = node(3)
    CREATE alice-[r:IS_MEMBER { since:"2005/7/1"}]->chess_group
    RETURN r;

    START alice = node(1), chess_group = node(3)
    CREATE chess_group-[r:MEMBERS]->alice
    RETURN r;

    START bob = node(2), chess_group = node(3)
    CREATE bob-[r:IS_MEMBER { since:"2011/2/14"}]->chess_group
    RETURN r;

    START bob = node(2), chess_group = node(3)
    CREATE chess_group-[r:MEMBERS]->bob
    RETURN r;

Again, we created on directional relationship from Bob and Alice to the group, and another back from the group for each member.

It is possible to create nodes and relationships in a single query.  For example, if we wanted to add a node for Joe, aged 19, and relate him as a friend of Alice starting on 2013/7/1, we could do this:

    START alice=node(1)
    CREATE p = (n {name:'Joe', age: 19})-[:KNOWS {since:"2013/7/1"}]->alice
    RETURN p;

Now that we've loaded some data into our graph, let's see what it takes to get some results back from some queries.

Using our example graph from above, if we wanted to know who Bob knows, we could ask:

    START bob=node(2) MATCH bob-[r:KNOWS]->m RETURN m.name;

Neo4j will reply:


    +---------+
    | m.name  |
    +---------+
    | "Alice" |
    +---------+
    1 row

If we wanted to know the members of our Chess group, we could query:

    START chess_group=node(3) MATCH chess_group-[r:MEMBERS]->m RETURN m.name;

Which returns:

    +---------+
    | m.name  |
    +---------+
    | "Bob"   |
    | "Alice" |
    +---------+
    2 rows

While these examples are fairly simple, the larger the dataset becomes, the more complex queries you can make.  There are also methods to help you find things like the shortest path between nodes, etc.

You can [try cypher live on real data](http://www.neo4j.org/learn/try) via the neo4j website.  The web interface is very nice for playing with existing datasets, or creating your own.

###Beyond Bob and Alice

Looking at social graphs with Bob and Alice is fun and all, but the real world is a bit more interesting that 2 folks and a chess club.  To really our hands dirty using graphs, lets explore something a bit more real.



###Conclusion

While graph databases are not the silver bullet for all our data needs, they do offer a unique way of modeling data that is highly interconnected.  For web developers, the possibilities for graphs in web applications is almost endless.

Neo4j is as good as a place as any to start learning about these data stores. With a robust API, active community, and the expressiveness of the cypher query language, I look forward to continuing developing web applications backed by graph databases for years to come.
