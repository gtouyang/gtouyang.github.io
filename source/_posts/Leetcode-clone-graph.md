---
title: Leetcode-clone-graph
date: 2019-03-30 13:52:51
categories: 算法
tags:
- Leetcode
- Java
- Graph
---
题目描述

Clone an undirected graph. Each node in the graph contains alabeland a list of itsneighbors.


OJ's undirected graph serialization:
Nodes are labeled uniquely.

We use#as a separator for each node, and,as a separator for node label and each neighbor of the node.

As an example, consider the serialized graph{0,1,2# 1,2# 2,2}.

The graph has a total of three nodes, and therefore contains three parts as separated by#.

First node is labeled as0. Connect node0to both nodes1and2.
Second node is labeled as1. Connect node1to node2.
Third node is labeled as2. Connect node2to node2(itself), thus forming a self-cycle.

Visually, the graph looks like the following:

		       1
		      / \
		     /   \
		    0 --- 2
		         / \
		         \_/
<!--more-->
---
图的克隆，我们可以用一个HashMap存我们已经克隆的结点，然后在队列在放我们要克隆的结点。步骤：我们克隆一个结点，然后将该结点放在map中，将结点的邻居放入队列。接着我们访问队列，我们从队列中移出一个结点，若结点已克隆(存在map中)，则不做操作，继续移出下一个结点，若没克隆，则将该结点的邻居放入队列中，并克隆该结点，放入map，直到队列中没有结点为止。


```java
/**
 * Definition for undirected graph.
 * class UndirectedGraphNode {
 *     int label;
 *     ArrayList<UndirectedGraphNode> neighbors;
 *     UndirectedGraphNode(int x) { label = x; neighbors = new ArrayList<UndirectedGraphNode>(); }
 * };
 */

import java.util.*;

public class Solution {
    public UndirectedGraphNode cloneGraph(UndirectedGraphNode node) {
        HashMap<Integer,UndirectedGraphNode> map = new HashMap<Integer,UndirectedGraphNode>();

        if(node == null){
            return null;
        }

        //复制第一个节点
        UndirectedGraphNode clone = new UndirectedGraphNode(node.label);

        //将其映射放入map中
        map.put(clone.label, clone);

        //建立一个队列，该队列中的结点已被部分克隆，即只克隆了label而没克隆neighbors
        LinkedList<UndirectedGraphNode> queue = new LinkedList<UndirectedGraphNode>();
        queue.add(node);

        while(!queue.isEmpty()){
            UndirectedGraphNode originalnode = queue.remove();
            UndirectedGraphNode cloneNode = map.get(originalnode.label);

            for (int i = 0; i < originalnode.neighbors.size(); i++) {
                UndirectedGraphNode neighbor = originalnode.neighbors.get(i);

                //如果邻居结点克隆过了
                if(map.get(neighbor.label) != null){
                    cloneNode.neighbors.add(map.get(neighbor.label));
                    continue;
                }

                //如果还没克隆，则把该邻居节点放入队列，并克隆一个新节点
                queue.add(neighbor);

                UndirectedGraphNode newNode = new UndirectedGraphNode(neighbor.label);

                map.put(newNode.label,newNode);

                cloneNode.neighbors.add(newNode);
            }
            
        }

        return clone;

    }
}
```
