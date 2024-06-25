![Logo](https://66.media.tumblr.com/31491223671803eaf1459c95805c2225/tumblr_puc2j2wa1N1tgo74ho1_1280.gif)

# Huffman Coding Description

In computer science and information theory, a Huffman code is a particular type of optimal prefix code that is commonly used for lossless data compression. The process of finding or using such a code is Huffman coding, an algorithm developed by David A. Huffman while he was a Sc.D. student at MIT, and published in the 1952 paper "A Method for the Construction of Minimum-Redundancy Codes".[1]

The output from Huffman's algorithm can be viewed as a variable-length code table for encoding a source symbol (such as a character in a file). The algorithm derives this table from the estimated probability or frequency of occurrence (weight) for each possible value of the source symbol. As in other entropy encoding methods, more common symbols are generally represented using fewer bits than less common symbols. Huffman's method can be efficiently implemented, finding a code in time linear to the number of input weights if these weights are sorted.[2] However, although optimal among methods encoding symbols separately, Huffman coding is not always optimal among all compression methods - it is replaced with arithmetic coding[3] or asymmetric numeral systems[4] if a better compression ratio is required. 

# Huffman Coding Implementation

## Node Class
This class represents a node in the Huffman tree. Each node has a frequency (freq), a symbol (symbol), and optionally left and right child nodes (left and right). The __lt__ method is used to compare nodes based on their frequency.

```python
class Node:
    def __init__(self, freq, symbol, left=None, right=None):
        self.freq = freq
        self.symbol = symbol
        self.left = left
        self.right = right
        self.huff = ''

    def __lt__(self, other):
        return self.freq < other.freq
```

## PriorityQueue Class

This class is a simple implementation of a priority queue using a list and the heapq module. It provides methods to insert a node (insert), extract the node with the minimum frequency (extract_min), and check if the queue is empty (is_empty).

```python
import heapq

class PriorityQueue:
    def __init__(self):
        self.queue = []

    def insert(self, node):
        heapq.heappush(self.queue, node)

    def extract_min(self):
        return heapq.heappop(self.queue)

    def is_empty(self):
        return len(self.queue) == 0
```

## Creating the Huffman Tree

This function takes a sequence of symbols and creates a Huffman tree. It first counts the frequency of each symbol in the sequence using the Counter class from the collections module. Then it creates a priority queue and inserts a node for each symbol into the queue. The nodes are then extracted two at a time, merged into a new node, and inserted back into the queue until only one node remains. This node is the root of the Huffman tree.

## Generating Huffman Codes

This function generates the Huffman codes for each symbol in the Huffman tree. It performs a depth-first traversal of the tree, appending '0' to the code when moving to the left child and '1' when moving to the right child. When it reaches a leaf node (a node with no children), it adds the symbol and its code to the dictionary of codes.

```python
def generate_huffman_codes(node, binary_code='', code_dict={}):
    if node is None:
        return

    if node.left is None and node.right is None:
        code_dict[node.symbol] = binary_code
        return

    generate_huffman_codes(node.left, binary_code + '0', code_dict)
    generate_huffman_codes(node.right, binary_code + '1', code_dict)

    return code_dict
```

## Encoding and Decoding Sequences

The huffman_encoding function encodes a sequence using Huffman codes.

```python
def huffman_encoding(sequence, code_dict):
    return ''.join(code_dict[symbol] for symbol in sequence)
```

The huffman_decoding function decodes a Huffman-encoded sequence using the Huffman tree.

```python
def huffman_decoding(encoded_sequence, huffman_tree):
    decoded_sequence = []
    current_node = huffman_tree
    for bit in encoded_sequence:
        if bit == '0':
            current_node = current_node.left
        else:
            current_node = current_node.right

        if current_node.left is None and current_node.right is None:
            decoded_sequence.append(current_node.symbol)
            current_node = huffman_tree

    return ''.join(decoded_sequence)
```

## Plotting the Huffman Tree

This function visualizes the Huffman tree using the matplotlib and networkx libraries.

```python
import matplotlib.pyplot as plt
import networkx as nx

def plot_huffman_tree(node):
    def add_edges(graph, node, pos={}, x=0, y=0, layer=1):
        if node is None:
            return pos

        pos[node.symbol] = (x, y)
        if node.left:
            graph.add_edge(node.symbol, node.left.symbol)
            pos = add_edges(graph, node.left, pos, x - 1 / layer, y - 1, layer + 1)
        if node.right:
            graph.add_edge(node.symbol, node.right.symbol)
            pos = add_edges(graph, node.right, pos, x + 1 / layer, y - 1, layer + 1)

        return pos

    graph = nx.DiGraph()
    pos = add_edges(graph, node)
    labels = {node: node for node in graph.nodes()}

    plt.figure(figsize=(12, 8))
    nx.draw(graph, pos, labels=labels, with_labels=True, node_size=2000, node_color='skyblue', font_size=10,
            font_weight='bold', arrows=False)
    plt.show()
```

