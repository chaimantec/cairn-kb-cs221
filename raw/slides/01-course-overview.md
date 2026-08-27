---
title: Lecture 1 — Course Overview and AI Foundations (Slides)
lecture: 1
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. This file transcribes the
  content of the three source files that comprise Lecture 1, organized by section.
  There are no traditional slide numbers; sections correspond to function calls
  in the source. Figure descriptions were written by a vision pass: a
  vision-capable model read each rendered figure and described what it shows.
---

# Lecture 1 Slides — Course Overview and AI Foundations

Lecture 1 comprises three executable Python files: `welcome.py`, `history.py`, and `tensors.py`.

---

## Part 1: Welcome (`welcome.py`)
**Source:** <https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/main/welcome.py>
**Rendered:** <https://stanford-cs221.github.io/autumn2025-lectures/?trace=welcome>

(Staff roster — `course-staff.png`: circular portrait headshots with two-line labels (name in dark red, role in gray). Top row: "Percy Liang / Instructor" and "Ken Liu / Head CA"; below a divider, seven CAs: Adi Badlani, Aryaman Arora, Erick Ramirez, Iddah Mlauze, Linda Liu, Minsik Oh, and Zhengxuan (Zen) Wu.)

### Section: What is AI?

Examples of AI all around us:
- AI assistants: ChatGPT, Claude, Gemini, Grok
- Autonomous vehicles: Waymo, Wayve
- Game playing: Deep Blue, AlphaGo, AlphaStar
- Competition math and programming: IMO, IOI, ICPC
- 3D protein structure prediction: AlphaFold

**But what exactly *is* AI?** — Artificial: runs on a computer (or robot). Intelligence: ??? — "We seek a definition from general principles."

### Section: Ingredients of Intelligence

What kinds of things should an intelligent agent be able to do?

(Diagram: `perceive-reason-act-learn.png` — a grayscale diagram: an "Agent" box in the center, a straight arrow labeled "perceive" entering from the left, a straight arrow labeled "act" exiting to the right, a looped arrow curving over the top of the box labeled "reason", and a looped arrow curving under the box labeled "learn".)

**Motivating example: driving** — (Image: `self-driving-image.png` — a stylized first-person view of a city intersection from a vehicle, in flat purple/blue/olive tones: the vehicle's hood in the foreground labeled "self-driving car", a dark blue street with a zebra crosswalk labeled "road" and "crosswalk", and white-labeled scene elements: "tree", "building", several "car"s, "traffic light"s on green poles, "sidewalk", and a pink "pedestrian" on the right.)

1. **Perceive** — process raw inputs from the world
   - Visual scene understanding
   - Speech recognition
   - Natural language understanding

2. **Reason** — use knowledge + percepts to draw inferences about the world
   - Uniform cost search (deterministic world)
   - Value iteration (decision-making under uncertainty, MDPs)
   - Minimax (adversarial games)
   - Probabilistic inference (Bayesian networks)

3. **Act** — output actions that affect the world
   - Text/image generation
   - Speech synthesis
   - Robot manipulation

4. **Learn** — update agent based on experience
   - Gradient descent
   - Q-learning (reinforcement learning)
   - Expectation maximization (Bayesian networks)

**...all under resource constraints:**
- **Computation**: running time, memory, communication
- **Information**: data/experience, available inputs in a given situation

### Section: Goals

**What does the developer want the agent to achieve?**
- An agent explicitly or implicitly encodes values/goals/objectives/utility functions
- **Alignment**: how to make these values correspond to what the developer wants
- Example: ChatGPT aims to be informative, avoid hallucinations, refuse harmful queries

**What impact do we want the agent to have on society?**
- Issues: privacy, copyright, jobs, inequality, geopolitics
- This is a deep sociotechnical problem (who's "we"?)
- Fundamental tradeoffs between people's values
- Unintended consequences (social media, impact on education)

### Section: About This Course

**Course philosophy:**
- Timeless foundations (e.g., gradient descent)
- Modern examples (e.g., GPT-5 solving 12/12 problems at the ICPC)
- Learn by doing: grounded in building practical applications

**Changes this year:**
- Tensor-native: from deep learning to value iteration to Bayesian network inference
- Cut constraint satisfaction problems
- Deep dive into societal impact (e.g., copyright, supply chains, policy)

Course website: <https://stanford-cs221.github.io/autumn2025/>

### Section: What is This Program?

This is an executable lecture — a program whose execution delivers the content of a lecture.

- Lectures inherit the hierarchical structure of code
- Code is more precise than English and also than math (well-defined semantics)
- You need to write code to build AI at the end of the day

---

## Part 2: History of AI (`history.py`)
**Source:** <https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/main/history.py>
**Rendered:** <https://stanford-cs221.github.io/autumn2025-lectures/?trace=history>

The history of AI is the story of three intellectual traditions: symbolic AI, neural AI, and statistical AI.

(Image: `symbolic-neural-statistical.png` — the three traditions arranged in a triangle: top center a marble bust of a Greek philosopher labeled "symbolic AI" in orange; lower left a biological neuron with branching dendrites and glowing synapses labeled "neural AI" in green; lower right a 3D plot of a wavy surface made of blue data points labeled "statistical AI" in blue.)

### Section: The Turing Test (1950)

Alan Turing published his 1950 paper asking: "Can machines think?"

(Photo: `alan-turing.jpg` — a black-and-white head-and-shoulders portrait of Turing, mid-20th-century print, dark jacket and tie, looking slightly left of camera. Image: `turing-1950-paper.jpg` — the first page of the *Mind* journal article "I.—COMPUTING MACHINERY AND INTELLIGENCE" by A. M. TURING (Vol. LIX, October 1950), opening with section "1. The Imitation Game." and the paragraph beginning "I propose to consider the question, 'Can machines think?' ...")

- More basically: How could you tell?
- His answer: The Imitation Game (the Turing Test)

  (Diagram: `turing-test.jpg` — the classic setup: above a horizontal line, candidate A (a computer monitor on a keyboard base) and candidate B (a stick figure); below the line, interrogator C with a thought bubble cycling through both candidates and ending in a question mark.)

- Significance: grounds a philosophical question in objective measurement
- Left open the solution: machine learning? logic?

### Section: Symbolic AI

**Dartmouth Workshop (1956):**
- John McCarthy organized workshop at Dartmouth College

  (Photo: `dartmouth.jpg` — a red-brick college building with a tall white central clock tower, photographed across a wide green lawn on a sunny day.)

- Convened leading thinkers (Shannon, Minsky, etc.)
- Goal: make a "significant advance" in 2 months
- Coined the term "artificial intelligence"

**Early Programs:**
- 1952 — Arthur Samuel's checkers: weights learned; played at strong amateur level
- 1955 — Newell & Simon's Logic Theorist: used search + heuristics; produced new proofs for Principia Mathematica

**Overwhelming Optimism:**
- Herbert Simon, Marvin Minsky, and Claude Shannon all predicted machines surpassing or equaling humans within 10–20 years

**Underwhelming Results:**
- Machine translation folklore: "The spirit is willing but the flesh is weak" → Russian → back: "The vodka is good but the meat is rotten"
- 1966: ALPAC report cut government funding for machine translation → **first AI winter**

**What went wrong:**
- Limited computation: exponential search space growth
- Limited information: complexity of real-world AI problems

**Silver lining (John McCarthy):**
- Lisp (advanced programming language)
- Garbage collection (automatic memory management)
- Time-sharing (multi-user computing)

**Knowledge-Based Systems (1970s–80s):**
- Expert systems: elicit domain knowledge as rules

  (Image: `knowledge-key.jpg` — an antique skeleton key lying diagonally across a cream paper strip printed "KNOWLEDGE" — knowledge as the key. Screenshot: `mycin-rule.png` — a MYCIN-style production rule:

  "IF: 1) The stain of the organism is gramneg, and
      2) The morphology of the organism is rod, and
      3) The aerobicity of the organism is aerobic
  THEN: There is strongly suggestive evidence (.8) that the class of the organism is enterobacteriaceae")

- Systems: DENDRAL (mass spectrometry), MYCIN (blood infections), XCON (order-to-parts)
- Wins: knowledge bridged information/computation gaps; first real industrial AI impact
- Shortcomings: deterministic rules couldn't handle real-world uncertainty; rules became too complex
- 1987: Collapse of Lisp machines → **second AI winter**

### Section: Neural AI

**Origins:**
- 1943: McCulloch/Pitts — artificial neural networks linking neural circuitry to mathematical logic
- 1949: Hebbian learning — "cells that fire together wire together"
- 1958: Rosenblatt's Perceptron for linear classifiers
- 1959: Widrow/Hoff's ADALINE for linear regression
- 1969: Minsky/Papert's *Perceptrons* book showed linear models can't solve XOR → killed neural net research

**Revival of Connectionism:**
- 1980: Fukushima's Neocognitron (convolutional neural networks for images)
- 1986: Rumelhart, Hinton, Williams popularized backpropagation for multi-layer networks
- 1989: LeCun applied CNNs to USPS handwritten digit recognition
- Neural nets were hard to train and unpopular in the 2000s

**Deep Learning Era:**
- 2006: Hinton et al. — unsupervised layerwise pre-training of deep networks
- 2009: NNs beat Hidden Markov Models in speech recognition — transformed speech community
- 2012: AlexNet achieves huge gains in object recognition — transformed computer vision
- 2014: Sequence-to-sequence modeling for machine translation
- 2014: Adam optimizer
- 2015: Attention mechanism for machine translation
- 2016: AlphaGo defeats Lee Sedol using deep reinforcement learning
- 2017: Transformer architecture for machine translation

### Section: Statistical AI

**Early Ideas Outside AI:**
- 1801: Linear regression (Gauss, Legendre)
- 1936: Linear classification (Fisher)
- 1951: Stochastic gradient descent (Robbins/Monro)
- 1956: Uniform cost search / Dijkstra's algorithm
- 1957: Markov decision processes (Bellman)

**Statistical Machine Learning:**
- 1985: Bayesian networks — reasoning under uncertainty (Pearl)
- 1995: SVMs (Cortes/Vapnik) — easier to train, grounded in statistical learning theory
- 1999: Variational inference popularized (Jordan/Jaakkola)
- 2001: Conditional random fields for structured prediction (Lafferty/McCallum/Pereira)
- 2003: Topic modeling with hierarchies and parameter uncertainty (Blei/Ng/Jordan)

### Section: Foundation Models

**Pretrained Language Models:**
- ELMo: LSTMs + pretraining, fine-tunes for downstream tasks
- BERT: Transformer pretraining + fine-tuning
- Google's T5 (11B): everything cast as text-to-text

  (Diagram: `t5.png` — the T5 text-to-text framework: a central "T5" box with four color-paired input boxes on the left and four output boxes on the right — green: "translate English to German: That is good." → "Das ist gut."; pink: "cola sentence: The course is jumping well." → "not acceptable"; yellow: "stsb sentence1: The rhino grazed on the grass. sentence2: A rhino is grazing in a field." → "3.8"; blue: "summarize: state authorities dispatched emergency crews tuesday..." → "six people hospitalized after a storm in attala county.")

**Scaling Up:**
- GPT-2: fluent text, first zero-shot capability signs
- Scaling laws: predictability for scaling
- GPT-3: in-context learning (closed)
- Chinchilla: compute-optimal scaling laws
- Llama 3, DeepSeek v3

**Reasoning:**
- Hard questions require thinking
- LMs produce "thoughts" before responding
- Models: OpenAI's o1–o4, DeepSeek's r1

**Industrialization of AI:**

(Photo: `industrialisation.jpg` — a wide photograph of a vast industrial complex — an oil refinery or petrochemical plant — at dusk: a maze of steel pipe racks, tanks and towers dotted with hundreds of glowing lights, striped smoke stacks rising into a pink-purple-blue sunset sky.)

- GPT-4: ~1.8T parameters; ~$100M training cost
- xAI's Colossus cluster: 200,000 H100s for Grok
- Stargate project (OpenAI, NVIDIA, Oracle): $500B over 4 years
- No public details on frontier model construction

  (Screenshot: `gpt4-no-details.png` — from the GPT-4 technical report, section "2  Scope and Limitations of this Technical Report", with one passage highlighted in blue: "Given both the competitive landscape and the safety implications of large-scale models like GPT-4, this report contains no further details about the architecture (including model size), hardware, training compute, dataset construction, training method, or similar.")
- AI has emerged from research and now is shaping businesses and public policy
- The research is still far from done

### Section: Parting Thoughts

**Fierce Battles Between Traditions:**
- Minsky/Papert promoted symbolic AI, killed neural net research
- 2000s statistical ML considered neural nets dead

**Deeper Connections:**
- McCulloch/Pitts' neural paper was really about implementing logical operations
- Go is defined symbolically, but deep NNs are essential to play it
- Deep learning started with perception but now tackles reasoning (symbolic AI's goals)

**AI as a Melting Pot:**
- Symbolic AI: vision and ambition
- Neural AI: model architectures
- Statistical AI: rigor (optimization, generalization)

This class will see elements of all three traditions.

---

## Part 3: Tensors (`tensors.py`)
**Source:** <https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/main/tensors.py>
**Rendered:** <https://stanford-cs221.github.io/autumn2025-lectures/?trace=tensors>

(Image: `tensor.jpg` — a drawing of a 3×3×3 cube of small cuboids in isometric view: the front face (bluish-gray) shows the numbers 1–9 in a 3×3 grid, the top face (orange) shows letters A, B, C in its back row and a, b, c in its front row, and the right face (salmon-pink) shows c, g, j and C, G, J in its columns — each face a 3×3 matrix, the cube built from three matrices. Shown with the note: 'This is probably what you think of when you hear the word "tensor".')

Tensors are the atoms of modern machine learning. They represent data, model parameters, gradients, intermediate computations (activations), etc. Tensors show up elsewhere in science and engineering.

### Section: Creating Tensors

Tensors are multi-dimensional arrays (generalizing vectors and matrices).

- **Scalar (rank 0):** `np.array(42)` — shape: `()`
- **Vector (rank 1):** `np.array([1, 2, 3])` — shape: `(3,)`
- **Matrix (rank 2):** `np.array([[1, 2, 3], [4, 5, 6]])` — shape: `(2, 3)`
- **Rank 3 tensor:** shape: `(2, 2, 3)`

Structured creation:
- `np.zeros((2, 3))` — zeros
- `np.ones((2, 3))` — ones
- `np.random.randn(2, 3)` — normal random
- `np.eye(3)` — identity
- `np.diag([1, 2, 3])` — diagonal matrix

Reading/writing: `np.save("var/x.npy", x)` and `np.load("var/x.npy")`

### Section: Tensor Examples in ML

- **Single data point:** vector of dimension D
- **Batch of points:** N × D matrix (rows = data points)
- **Sequence data (language):** N × L × D tensor (examples × length × dim)
- **Images:** N × H × W × C tensor (examples × height × width × channels)
- **Weight matrix:** D_in × D_out matrix

The parameters of a neural network model are a collection of tensors (e.g., DeepSeek V3 has 670B parameters as tensors).

### Section: Viewing Tensors

- `x[0]` — first row
- `x[:, 1]` — first column
- `x.transpose(1, 0)` — transpose

Note: these operations do not copy — they produce views. Be careful with mutation.

### Section: Element-wise Operations

- `np.power(x, 2)`, `np.sqrt(x)` — applied per-element
- `x + x`, `x * 3`, `x / 2` — broadcasting
- `np.triu(x)`, `np.tril(x)` — upper/lower triangular (useful for masking in Transformers)
- `np.zeros_like(x)`, `np.ones_like(x)` — same-shape creation

### Section: Matrix Multiplication

The bread and butter of deep learning:

```python
x = np.ones((4, 6))
w = np.ones((6, 3))
y = x @ w    # shape (4, 3)
```

**Batched:** `x = np.ones((2, 4, 6)) @ w = np.ones((6, 3))` → shape `(2, 4, 3)`
— `w` is broadcasted to each slice of `x`.

### Section: Efficiency

Whenever possible, express computation as tensor operations. Python triple-loop matmul vs `x @ w` — NumPy is dramatically faster (written in C, optimized). Matrix multiplication is even faster on GPUs for large matrices.

### Section: Einops

Einops is a library for manipulating tensors where dimensions are named. Inspired by Einstein summation notation.

**Motivating problem:**
```python
x = torch.ones(2, 2, 3)          # batch seq hidden
y = torch.ones(2, 2, 3)          # batch seq hidden
z = x @ y.transpose(-2, -1)      # batch seq seq — what is -2, -1?
```

**Einsum — generalized matrix multiplication with good bookkeeping:**
```python
# Simple matmul
z = einsum(x, y, "seq1 hidden, hidden seq2 -> seq1 seq2")

# Batched attention pattern
z = einsum(x, y, "batch seq1 hidden, batch seq2 hidden -> batch seq1 seq2")

# With ellipsis
z = einsum(x, y, "... seq1 hidden, ... seq2 hidden -> ... seq1 seq2")
```

Dimensions not named in the output are summed over.

**Reduce:**
```python
reduce(x, "... hidden -> ...", "sum")  # instead of x.sum(dim=-1)
```

**Rearrange (for multi-head attention):**
```python
rearrange(x, "... (heads hidden1) -> ... heads hidden1", heads=2)
# ... operate on heads dimension ...
rearrange(x, "... heads hidden2 -> ... (heads hidden2)")
```

### Section: Summary

- Use tensors to represent everything
- Express computations using few tensor operations for efficiency (a puzzle!)
- Use einops to make computations more legible
- Practice — it's like learning a new language
