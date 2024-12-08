---
layout: post
title: Creating directed and weighted scale-free network using NetworkX
subtitle: 
tags: [network,code]
comments: true
---
I work on network dynamics just for two years. My research is based on time series analysis primarily, and I use the python library NetworkX to create networks and do some fundamental analysis. 

At the beginning of my research, I was using undirected scale-free networks and obtaining an adjacency or Laplacian matrix for a desired-size network was enough. Using NetworkX was easy, and the documentation was user friendly for this kind of basics. Then I needed to study weighted and directed scale-free networks, and things have changed. If I wrote the code to generate the Laplacian matrix of a scale-free weighted and directed network, it would take less time. But, I wanted to make it using Networkx to feel more comfortable with it and decided to write my first blog post.

```python
def network_generate(n,eta):
    G = nx.scale_free_graph(n) #obtain a directed graph
    #remove self loops
    G.remove_edges_from(nx.selfloop_edges(G))
    #remove parallel links
    removed_list = []
    for edge in G.edges:
        if edge[2] != 0:
            removed_list.append(edge)
    G.remove_edges_from(removed_list)
    #add weights to links
    for edge in range(G.number_of_edges()):
        list(G.edges(data=True))[edge][2]["weight"] = np.random.uniform(1.-eta,1.+eta)
    A = nx.adj_matrix(G).todense().T
    k_in = np.zeros(G.number_of_nodes())
    for node in range(G.number_of_nodes()):
        if G.in_degree(node) != 0:
            k_in[node] = sum(list(G.in_edges(node, data=True))[i][2]["weight"] for i in range(G.in_degree(node)))
    #weighted laplacian matrix
    L = np.diag(k_in) - A
    return G, A, L

```

This code returns the network object, adjacency matrix and the Laplacian matrix of the weighted and directed network. The size of the network and measurement for weights are the only inputs. Since the distribution of the weights is essential, the parallel links and self-loops are removed. Adjacency matrix was computed from in-degree distribution due to the application. Also, the Laplacian matrix should be zero row-sum, so this function finds the number of connection on node _i_ concerning the weighted links. 

Finally, if you want to tune the weights and need a zero row-sum Laplacian and persist in using NetworkX, you can use this function.  
