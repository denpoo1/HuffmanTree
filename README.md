![Logo](https://66.media.tumblr.com/31491223671803eaf1459c95805c2225/tumblr_puc2j2wa1N1tgo74ho1_1280.gif)

# Huffman Coding Description

Huffman coding is a lossless data compression algorithm. The idea is to assign variable-length codes to input characters, lengths of the assigned codes are based on the frequencies of corresponding characters. 
The variable-length codes assigned to input characters are Prefix Codes, means the codes (bit sequences) are assigned in such a way that the code assigned to one character is not the prefix of code assigned to any other character. This is how Huffman Coding makes sure that there is no ambiguity when decoding the generated bitstream. 
Let us understand prefix codes with a counter example. Let there be four characters a, b, c and d, and their corresponding variable length codes be 00, 01, 0 and 1. This coding leads to ambiguity because code assigned to c is the prefix of codes assigned to a and b. If the compressed bit stream is 0001, the de-compressed output may be “cccd” or “ccb” or “acd” or “ab”.

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

