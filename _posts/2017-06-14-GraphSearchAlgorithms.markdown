---
layout: post
title: Search algorithms with Graph models
date: 2017-06-14 19:20:23 +0900
category: graph
---

In mathematics, graph theory is the study of graphs, which are mathematical structures used to model pairwise relations between objects. A graph consists of nodes which are connected by edges. A distinction is made between undirected graphs, where edges link two vertices symmetrically, and directed graphs, where edges link two nodes asymmetrically.

Graph models are useful for certain business problems where objects and its relations matter. The following diagram below shows some common usecases it this regard

![Graph Usecases](/assets/graph/graphalgos.jpg){:class="img-responsive"}

On my experince, I have used it for following needs

- Finding the shortest path between two equipments connected by electrical harnessess
- Simplifying electric harness wires which are connected to multiple pin sockets of an equipment by single connection point.

Following are the (simplified) code snippets used:

### Data Structure for storing graph model with some utility functions

``` csharp
public class Edge 
{
    public string Name;
    public string Extremity1;
    public string Extremity2;
    public string Length; // Some Property of edge
}
public class Node 
{
    public string Name;
}

public class GraphModel
{
    private List<Edge> _edges = new List<Edge>();
    private List<Node> _nodes = new List<Node>();
    private Dictionary <string, List<string>> _dicNetwork = new Dictionary <string, List<string>>();
    public Dictionary<System.Guid, List<string>> dicPaths = new Dictionary<System.Guid, List<string>>();

    public GraphModel(edges, links)
    {
        foreach(var item in edges)
        {
            _dicNetwork.Add(item.Name, new List<string>());
        }
        foreach(var item in links)
        {
            if (_dicNetwork.ContainsKey(item.Extremity1))
            {
                _dicNetwork[item.Extremity1].Add(item.Extremity2)
            }
            if (_dicNetwork.ContainsKey(item.Extremity2))
            {
                _dicNetwork[item.Extremity2].Add(item.Extremity1)
            }
        }
    }
    public List<string> GetChildren(string name)
    {
        if (_dicNetwork.ContainsKey(name))
        {
            return _dicNetwork[name];
        }
        else
        {
            return new List<string>();
        }
    }
}

```

Finding the shortest path between two nodes can be found based on different conditions like minimum number of hops between nodes, shortest length of edges or any other property of the edge. 
Below we show two algorithms which can be made use for computng the paths. 

Essentially it the only the way algorithms computes the path that differs as shown in the image below
![BFS vs DFS](/assets/graph/dfs-vs-bfs.gif){:class="img-responsive"}

### Finding the shortest path betweeen nodes using Breadth First Search (BFS)

``` csharp

public void BreadthFirstSearch(string start, string end, int hops)
{
    Queue<List<string>> queue = new Queue<List<string>>();

    queue.Enqueue(new List<string>) {start});
    while (queue.Count() != 0)
    {
        List<string> temp = queue.Dequeue();
        string last = temp.Last();
        // Exit if the number of levels is more than value set by hops
        if (temp.Count() == hops)
        {
            break;
        }
        if (last == end)
        {
            // dicPath should contain the all the paths from start to end
            dicPaths.Add(System.Guid.NewGuid, new List<string>(temp));
        }
        foreach(string item in GetChildren(last))
        {
            if (!temp.Contains(item))
            {
                List<string> newpath = new List<string>(temp);
                newpath.Add(item);
                queue.Enqueue(newpath);
            }
        }
    }
}

```

### Finding the shortest path betweeen nodes using Depth First Search (DFS)

``` csharp
List<string> lstExplored = new List<string>();
List<string> lstPaths = new List<string>(); 
int nPathIndex = 0;
public void DepthFirstSearch(string start, string end)
{
    lstExplored.Add(start);
    lstPaths.Add(start);
    nPathIndex++;

    if (start == end)
    {
        dicPaths.Add(System.Guid.NewGuid, new List<string>(lstPaths));
    }
    else
    {
        foreach(string item in GetChildren(start))
        {
            if (!lstExplored.Contains(item))
            {
                DepthFirstSearch(item, end)
            }
        }
    }
    lstPaths.RemoveAt(--nPathIndex);
    lstExplored.Remove(start);
}

```

### Finding node clusters based on some property

Further using the same data stucture we can also build a partitioning of the graph model based on total length of edges.

``` csharp
List<string> lstExplored = new List<string>();
List<string> lstPaths = new List<string>(); 
int nPathIndex = 0;
public void SearchClusterForMaxLength(string start, double maxLength, ref double lenghtAccumulator)
{
    lstExplored.Add(start);
    lstPaths.Add(start);
    nPathIndex++;

    if (lenghtAccumulator >= maxLength && lstPaths.Count >= 3)
    {
        int flag = 0;
        foreach(var key in dicPaths.Keys)
        {
            List<string> intersection = lstPaths.Intersect(dicPaths[key]).ToList();

            int index1 = dicPaths[key].IndexOf(intersection.Count >= 1 ? intersection.First() : "");
            int index2 = dicPaths[key].IndexOf(intersection.Count >= 2 ? intersection.Skip(1).First() : "");

            if (index2 > index1)
            {
                for (int i = index1, j = lstPaths.IndexOf(intersection.First()); j < lstPaths.Count; i++, j++)
                {
                    if (dicPaths[key].Count - 1 >= i)
                    {
                        dicPaths[key][i] = lstPaths[j];
                        flag = 1;
                    }
                    else
                    {
                        dicPaths[key].Add(lstPaths[j];
                        flag = 1;
                    }
                }
                if (lstPaths.IndexOf(intersection.First()) != 0)
                {
                    List<string> temp = lstPaths.Take(lstPaths.IndexOf(intersection.First())).ToList();
                    if (int i = 0; i < temp.Count; i++)
                    {
                        dicPaths[key].Insert(i, temp[i]);
                    }
                    flag = 1;
                }
            }
            else if (index2 < index1)
            {
                dicPaths[key].Reverse();

                int index1 = dicPaths[key].IndexOf(intersection.Count >= 1 ? intersection.First() : "");
                int index2 = dicPaths[key].IndexOf(intersection.Count >= 2 ? intersection.Skip(1).First() : "");

            
                for (int i = index1, j = lstPaths.IndexOf(intersection.First()); j < lstPaths.Count; i++, j++)
                {
                    if (dicPaths[key].Count - 1 >= i)
                    {
                        dicPaths[key][i] = lstPaths[j];
                        flag = 1;
                    }
                    else
                    {
                        dicPaths[key].Add(lstPaths[j];
                        flag = 1;
                    }
                }
                if (lstPaths.IndexOf(intersection.First()) != 0)
                {
                    List<string> temp = lstPaths.Take(lstPaths.IndexOf(intersection.First())).ToList();
                    if (int i = 0; i < temp.Count; i++)
                    {
                        dicPaths[key].Insert(i, temp[i]);
                    }
                    flag = 1;
                }
            }
        }
        if (flag == 0)
        {
            dicPaths.Add(System.Guid.NewGuid(), new List<string>(lstPaths));
        }
        else
        {
            foreach(string item in GetChildren(start))
            {
                if (!lstExplored.Contains(item))
                {
                    lenghtAccumulator += _edges.Find( x => (x.Extremity1 == start && x.Extremity2 == item) || 
                    (x.Extremity2 == start && x.Extremity1 == item)).Length;
                    SearchClusterForMaxLength(item, maxLength, ref lenghtAccumulator);
                }
            }
        }
        lstPaths.RemoveAt(--nPathIndex);
        lstExplored.Remove(start);

    }
}

```
