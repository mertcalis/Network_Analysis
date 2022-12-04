# Network Analysis
> Network Analysis with [NetworkX](https://networkx.org/documentation/stable/index.html).


This projects shows how to import a network and how to create a random network with Erdös-Renyi model. Also how to visiualize and how to analyse networks

*Dataset is driven from [Stanford Large Network Dataset Collection](http://snap.stanford.edu/data/index.html)* ✔️

## Features

- Create network via reading edges (networkX)
- Degree Distribution and Average Degree (Visualization)
- Clustering Coefficient Distribution and Average Clustering Coefficient (Visualization)
- Shortest Paths Distribution and Average Shortest Path (Visualization)
- Creating a random graph with Erdös-Renyi Model and also its analysis

## Terminology
- **Network** consist of two or more 'nodes' that are linked to each other with 'edges' to share information. 
- **Node (Vertex)** is the component of the system. N is the size of the network. 
- **Link (Edge)** is the connections between nodes. L is the number of interactions(links) between the nodes. 
- **Degree** of the i-th node is the number of links attached (in or out) to the node. 
- **Path** is a route that runs along the links of the network. 
- **Clustering Coefficient** is the number of the triangles that includes root node(i-th node).

> {network, node, link} terms often refers to real systems
> {graph, vertex, edge} terms refers to mathematical representation of networks

## Imports
```sh
import networkx as nx
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.cm import ScalarMappable
import random as rd
```
## Algorithms

### Importing an existing network by networkX

```sh
fh = open("facebook_combined.txt", "rb")
G = nx.read_edgelist(fh)
fh.close()
nx.draw(G)
```
 Only 3 lines of code is enough thanks to NetworkX. Check out for the other importing options from their website: [NetworkX](https://networkx.org/documentation/stable/reference/readwrite/generated/networkx.readwrite.edgelist.read_edgelist.html).
 
 ![indir](https://user-images.githubusercontent.com/44343742/205502891-2e08ed36-a610-4d0c-ac1f-97f26ce8930e.png)

 
 ## Degree and Average Degree
 ```sh
 def degree_rank(Graph):
    degree_sequence = sorted((d for n, d in Graph.degree()), reverse=True)
    dmax = max(degree_sequence)

    fig = plt.figure("Degree of a random graph", figsize=(10, 10))
    # Create a gridspec for adding subplots of different sizes
    axgrid = fig.add_gridspec(5, 4)

    ax0 = fig.add_subplot(axgrid[0:3, :])
    Gcc = Graph.subgraph(sorted(nx.connected_components(Graph), key=len, reverse=True)[0])
    pos = nx.spring_layout(Gcc, seed=10396953)
    nx.draw_networkx_nodes(Gcc, pos, ax=ax0, node_size=20)
    nx.draw_networkx_edges(Gcc, pos, ax=ax0, alpha=0.4)
    ax0.set_title("Connected components of G")
    ax0.set_axis_off()

    ax1 = fig.add_subplot(axgrid[3:, :2])
    ax1.plot(degree_sequence, "b-", marker="o")
    ax1.set_title("Degree Rank Plot")
    ax1.set_ylabel("Degree")
    ax1.set_xlabel("Rank")

    ax2 = fig.add_subplot(axgrid[3:, 2:])
    ax2.bar(*np.unique(degree_sequence, return_counts=True))
    ax2.set_title("Degree histogram")
    ax2.set_xlabel("Degree")
    ax2.set_ylabel("Number of Nodes")

    fig.tight_layout()
    plt.show()
```
![indir (1)](https://user-images.githubusercontent.com/44343742/205502915-25026a5f-ea7f-4266-962b-5e7c7ae3d6bf.png)


## Clustering Coefficient Distribution and Avg Clusterin Coefficient
```sh
def avg_Clus_Coeff(Graph):
    gc = Graph.subgraph(max(nx.connected_components(Graph)))
    lcc = nx.clustering(gc)

    cmap = plt.get_cmap('autumn')
    norm = plt.Normalize(0, max(lcc.values()))
    node_colors = [cmap(norm(lcc[node])) for node in gc.nodes]

    fig, (ax1, ax2) = plt.subplots(ncols=2, figsize=(12, 4))
    nx.draw_spring(gc, node_color=node_colors, with_labels=True, ax=ax1)
    fig.colorbar(ScalarMappable(cmap=cmap, norm=norm), label='Clustering', shrink=0.95, ax=ax1)

    ax2.hist(lcc.values(), bins=50)
    ax2.set_xlabel('Clustering')
    ax2.set_ylabel('Frequency')

    plt.tight_layout()
    plt.show()
    
avg_cluster_coeff = nx.average_clustering(G)
```

![download](https://user-images.githubusercontent.com/44343742/205502924-8445b20f-7754-4eda-ad78-20fdbe6bb7c9.png)


## Distribution of Shortest Paths and Average Path Length
```sh
def dist_shortestPath(Graph):
    shortest_path_dist = dict(nx.all_pairs_shortest_path_length(Graph))
    dist = []
    for i in range(Graph.number_of_nodes()):
        for j in range(Graph.number_of_nodes()):
            if(i>j):
                dist.append(shortest_path_dist[str(i)][str(j)])

    plt.hist(dist,bins = 8)
    # number of bins are depends on the diameter of the graph and diameter can be found easiliy with
    #nx.diameter(G), for this system diameter is calculated as 8.
    plt.show()
    
avg_path_length = nx.average_shortest_path_length(G)
```
![indir (2)](https://user-images.githubusercontent.com/44343742/205502946-1fd5c7e4-2780-467f-8bfc-2ee1a3015878.png)


## Erdös-Renyi Model

According to this random graph model there are **N** labeled nodes are connected with **L** randomly placed links. Here is the implementation

```sh
def Erdos_Renyi_model(N,L):
    listOfEdges = []
    while(len(listOfEdges) < L ):
        x = rd.randint(0,(N-1))
        y= rd.randint(0,(N-1))
        if(x!=y):
            if(((x,y) in listOfEdges) or ((y,x) in listOfEdges)):
                pass
            else:
                listOfEdges.append((x,y))
    
    G2 = nx.empty_graph()
    
    for i in range(N):
        G2.add_node(i)
    
    G2.add_edges_from(listOfEdges)
        
    return G2
    
graphh = Erdos_Renyi_model(1000,4000)
nx.draw(graphh)
```
![indir (3)](https://user-images.githubusercontent.com/44343742/205502960-ff9e29ea-64cf-46a9-92ee-79c5fb191f83.png)


### Degree and Rank for Randomed Graphs
![indir (4)](https://user-images.githubusercontent.com/44343742/205502969-4348c61f-92d3-4ffd-8cae-e50ac7dba40e.png)

### Average Clustering for Randomed Graphs
![indir (5)](https://user-images.githubusercontent.com/44343742/205502976-4da77761-0b30-43b8-8ac9-ec17683dd068.png)


## License

MIT

