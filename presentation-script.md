# Speaker Script: When Graphs Learn to Speak in Tokens

Target length: approximately 10–15 minutes

Use this as a speaking guide. Do not read it mechanically. Add your own examples and explanations so the presentation sounds like you.

## Slide 1 — Title

Hello everyone. My topic is **Quantized Graph Representation Learning**, or QGR.

Graphs appear in many data-mining problems. They can represent social networks, recommendation systems, knowledge graphs, traffic networks, or molecular structures. The main idea in this presentation is simple: instead of representing graph data only with long continuous vectors, can we represent it with compact discrete codes, similar to tokens in language?

I will explain why this idea is useful, how the architecture works, what quantization strategies exist, how the codes can connect to large language models, and how we should evaluate these systems.

## Slide 2 — Why quantize graph representations?

Traditional graph-learning systems usually create a dense vector for every node, edge, or complete graph. These vectors are useful because they are expressive and work well with downstream machine-learning models.

However, there are three recurring problems.

First, memory usage can grow quickly when the graph contains millions of entities. Second, individual dimensions in a vector are difficult to interpret. We can use the vector for prediction, but we usually cannot explain what each dimension means.

Third, continuous graph embeddings do not naturally fit into a language model. An LLM expects a sequence of discrete tokens. Connecting an arbitrary graph vector to an LLM requires an additional alignment step.

That leads to the main design question: can graph structure be compressed into a smaller vocabulary of useful codes without losing the information needed for prediction?

## Slide 3 — The QGR architecture

This slide shows the general QGR pipeline.

The input is a graph, together with node features when they are available. A graph encoder, often a GNN, looks at node neighborhoods and graph topology and creates continuous latent vectors.

The quantizer then maps those vectors to discrete codes. In many methods, the quantizer uses a codebook containing prototype vectors. The model chooses the codeword that best represents each latent vector.

After quantization, the codes can be sent in two directions. A decoder can try to reconstruct features or graph structure. Alternatively, a downstream task can use the codes for node classification, link prediction, graph classification, or graph generation.

The important trade-off is compression versus fidelity. A small codebook is efficient, but it may discard details. A large codebook preserves more information, but it can be expensive and harder to train.

## Slide 4 — Four quantization strategies

The survey groups the main approaches into four families.

The first is **vector quantization**. The model learns a codebook of prototype vectors, and each latent vector is assigned to its nearest prototype. A common issue is codebook collapse, where some codewords are used frequently and others are almost never used.

The second is **residual vector quantization**. It uses several codebooks in sequence. The first code gives a rough approximation, and later codes represent the remaining error. This can preserve more information without creating one extremely large codebook.

The third is **finite scalar quantization**. The representation is projected into a small number of dimensions, and each dimension is rounded to one of a fixed set of values. This reduces the need to manage a learned codebook.

The fourth is **anchor selection and assignment**. This is especially useful for knowledge graphs. An entity can be represented using nearby or structurally important real entities called anchors.

Each strategy makes a different trade-off between efficiency, stability, interpretability, and information loss.

## Slide 5 — What should the codes preserve?

Quantization itself is not the whole learning objective. The training objective decides what the model is rewarded for preserving.

At the node level, feature reconstruction asks the decoder to recover node attributes from the discrete codes. This is important when the attributes contain semantic information, such as text features or molecular properties.

At the edge level, link prediction checks whether the codes preserve graph connectivity. If two nodes are connected, the codes should help the model recover that relationship. Some methods also preserve distances or semantic similarity between nodes.

At the graph level, contrastive learning creates different views of the same graph. For example, the model might mask attributes, remove nodes, or perturb edges. It then encourages the representations of those views to remain compatible.

A strong system may combine these objectives. Feature reconstruction protects attributes, link prediction protects topology, and graph-level objectives encourage stability across different views.

## Slide 6 — Why connect graph codes to LLMs?

Large language models already process discrete sequences. That makes discrete graph codes a possible interface between graph learning and language modeling.

There are two broad options. One option is to reuse the LLM’s existing vocabulary as the graph codebook. The other option is to add new learned graph tokens to the vocabulary and fine-tune the model.

This could support tasks such as node classification, knowledge-graph link prediction, molecule–text retrieval, molecule captioning, and molecule generation.

The graph encoder contributes relational information. The LLM contributes sequence modeling and instruction-following abilities.

But there is an important limitation. A code sequence may look like language tokens without actually being meaningful language. The model may still learn shortcuts. Therefore, researchers need tests that show whether the LLM is really responding to graph structure.

## Slide 7 — How should QGR systems be evaluated?

Accuracy by itself is not enough.

The first category is task quality. Depending on the task, this may include accuracy, F1 score, mean reciprocal rank, Hits at K, or regression error.

The second category is compression. Researchers should report code length, vocabulary size, memory use, and inference cost. A method should not be called efficient only because it achieves good accuracy.

The third category is robustness. We should test missing features, noisy edges, graph perturbations, distribution shifts, and larger graphs.

The fourth category is code usage. If a codebook has many codewords but the model uses only a few of them, the effective representation is smaller than expected.

For graph–LLM systems, I would also add counterfactual tests. For example, if an important edge is removed, does the model’s prediction change in a meaningful way? That type of test is more informative than measuring accuracy alone.

## Slide 8 — Ablations that reveal what matters

Ablation studies help us understand where an improvement actually comes from.

A useful paper should compare continuous embeddings with quantized codes. It should compare different quantization strategies and different codebook sizes. It should also compare reconstruction-only training with combined node, edge, and graph objectives.

For graph–LLM systems, we should compare the model with and without the LLM while keeping the downstream task fixed. We should also compare random, learned, and domain-informed codebooks.

Finally, robustness should be tested as the graph becomes larger or noisier.

These experiments answer the central causal question: did quantization create the improvement, or did the improvement come from a larger encoder, extra pretraining, or a stronger downstream model?

## Slide 9 — My takeaway

My main takeaway is that discrete graph codes are most valuable as an interface.

They can make graph representations more compact and provide a common format for retrieval, generation, and language-model pipelines.

For data mining, this could support similarity search, clustering, recommendation, link prediction, knowledge-graph retrieval, and anomaly detection.

At the same time, discrete does not automatically mean interpretable. A code such as `code_204` is meaningful only if experiments show what information it captures, whether it is stable across runs, and how it changes when the graph changes.

So I see QGR as a promising direction, but one that still needs careful evaluation and stronger interpretability studies.

## Slide 10 — References and closing

The main source for this presentation is the survey by Lin and colleagues on quantized graph representation learning and its connection to large language models.

I also used related work on node2vec, NodePiece, and Mole-BERT to connect the topic to graph representation learning, knowledge graphs, and molecular data.

To conclude, quantized graph representation learning treats graph representation as a vocabulary-learning problem. The model encodes the graph, compresses it into discrete codes, and trains those codes to preserve the information needed for downstream tasks.

Graphs may not become ordinary text, but graph models and language models may be able to communicate through a shared token-like representation.

Thank you. I am happy to take questions.
