---
title: Lecture 10 — Games I (Slides)
lecture: 10
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_file: https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/9a9fc146/games.py
rendered: https://stanford-cs221.github.io/autumn2025-lectures/?trace=games
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. This file transcribes the
  content of `games.py`. There are no traditional slide numbers; sections correspond
  to function calls in the source. All demo outputs (simulated rollouts, game values,
  the halving-game solution table) were read on 2026-09-07 from the published
  execution trace committed in the lecture repo at `var/traces/games.json` — the same
  trace the rendered lecture UI displays. The stochastic values depend on
  `set_random_seed(1)` at games.py:249 and games.py:381, so the trace is exactly
  reproducible. The 27 figures shown during the lecture are described by a vision
  pass over the copies in the lecture repo at `images/` (see the Figures section at
  the end of this file).
---

# Lecture 10 Slides — Games I

Lecture 10 opens the games module: the agent still maximizes utility, but the environment now contains an **opponent whose strategy is unknown**. The path: formalize a game as a tree with per-state players, define the **value** of a game under fixed policies (evaluation recurrence, Monte Carlo), then find optimal policies under three increasingly adversarial assumptions — **expectimax** (opponent policy known), **minimax** (opponent plays optimally), and **expectiminimax** (plus chance nodes). The face-off section shows how each "optimal" policy is only optimal against the assumption baked into it. The lecture closes with the two speedups: **alpha-beta pruning** (exact branch-and-bound) and **evaluation functions** (approximate, depth-limited search).

---

## Section: Recap and This Time (`main`)

**Last time: Markov decision processes and reinforcement learning. This time: games.**

Opening example — game 1 (figure: `game1.png`, see the Figures section): there are three bins A, B, C. The agent chooses a bin, then the opponent (the lecturer) chooses a number from that bin, and the agent's goal is to maximize the chosen number. **What should your strategy be?** Note that it depends on your opponent's strategy, which is unknown.

**MDPs vs games:**

- MDPs: agent tries to maximize utility, environment is random and known.
- Games: agent tries to maximize utility, opponent strategy is **unknown**.

**Roadmap:** modeling (what is a game?) → game evaluation (play and see who wins) → expectimax (opponent plays a fixed strategy) → minimax (opponent plays the best possible strategy) → face-off (relationships between values) → expectiminimax (plus randomness in the game) → alpha-beta pruning (speed up search, exact) → evaluation functions (speed up search, approximate).

**Summary:**

- Games: agent tries to maximize utility, opponent strategy is unknown
- Minimax principle: assume the opponent tries to minimize utility
- Recurrences define various (game, policy) outcomes (evaluation, expectimax, minimax, expectiminimax)
- Alpha-beta pruning: branch and bound to speed up minimax (exact)
- Evaluation functions: prior knowledge that speeds up (approximately)

---

## Section: Modeling: what is a game? (`modeling`, `game1`, `halving_game`)

**This lecture: two-player zero-sum games**

- Two players: *agent* and *opponent*
- Zero-sum: utility of agent = −utility of opponent

### The formal game interface (`Game` class)

A game is any object with five methods:

- `start_state()` — where the game starts
- `successors(state)` — what the possible successor states are (a dict action → next state)
- `player(state)` — which player should move in `state`
- `is_end(state)` — is the game over?
- `utility(state)` — what the utility of the game is (for the agent)

### Game 1: the bins game, as a tree (figure: `game-tree.png`)

Traced walk-through on the running example (`Game1`):

- `state = start_state()` → `root`; `is_end(root)` → False; `player(root)` → `"agent"`; `successors(root)` → `{A: A, B: B, C: C}`.
- `state = "A"` → `is_end(A)` → False; `player(A)` → `"opp"`; `successors(A)` → `{1: A1, 2: A2}`.
- `state = "A1"` → `is_end(A1)` → True; `utility(A1)` → **−50**.

Full `Game1`: root is the agent's turn with actions A/B/C; A/B/C are the opponent's turn with actions 1/2 leading to the end states; utilities are A1 = −50, A2 = 50, B1 = 1, B2 = 3, C1 = −5, C2 = 15.

**Key characteristics of a game:**

- All the utility is at the end state (sparse reward)
- Different players in control at different states

**Types of policies:**

- Deterministic: $\pi_p(s)$ is the action player p takes in state s
- Stochastic: $\pi_p(a \mid s)$ is the probability player p takes action a in state s

In general, we will consider stochastic policies.

### The halving game (figure: `halving-game.png`)

Two players take turns halving or decrementing a number; **the player that is left with 0 wins**.

`HalvingGame(n=11)` traced: start state `(11, agent)`; `player` → agent; `successors` → `decrement: (10, opp)`, `half: (5, opp)` (integer division `n // 2`); `is_end((11, agent))` → False. End is `n == 0`; utility at 0 is **+1 if it is the agent's turn** (agent wins), **−1 if it is the opponent's turn**.

---

## Section: Game Evaluation (`game_evaluation`, `V_eval`)

**Given a game and a policy for each player, the value (expected utility) of a game over all possible rollouts.**

`simulate(game, policies)` plays a rollout: from the start state, whichever player's turn it is chooses actions via `sample_dict(policies[player](state))` (sampling from the distribution the policy returns), advancing until an end state, then returns `Rollout(steps, utility)`.

**Let's play the game!** Policies: agent always chooses A (`{"A": 1}`), opponent chooses randomly between 1 and 2 (`{"1": 0.5, "2": 0.5}`). With `set_random_seed(1)`:

- One rollout (traced): agent plays A, opponent samples 2 → state A2, **utility 50**.
- Do it multiple times and average: 20 rollouts give 13 utilities of 50 and 7 of −50, **mean 15.0** (the true value is $0.5 \cdot 50 + 0.5 \cdot (-50) = 0$; 20 samples is not enough to see it).

**Can we compute the game value exactly without simulation? Yes, by defining a recurrence!** (figures: `game-evaluation-graphical.png`, `game-evaluation-recurrence.png`)

```python
def V_eval(game, policies, state):
    if game.is_end(state):
        return game.utility(state)
    player = game.player(state)
    policy = policies[player]
    value = 0
    for action, prob in policy(state).items():
        next_state = game.successors(state)[action]
        value += prob * V_eval(game, policies, next_state)
    return value
```

In words: at an end state the value is the utility; at the agent's turn, average the successor values weighted by the agent's policy; at the opponent's turn, the same with the opponent's policy (as printed on the slide):

$$V_{eval}(s) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \sum_{a} \pi_{agent}(s, a) \, V_{eval}(\text{Succ}(s, a)) & \text{Player}(s) = \text{agent} \\ \sum_{a} \pi_{opp}(s, a) \, V_{eval}(\text{Succ}(s, a)) & \text{Player}(s) = \text{opp} \end{cases}$$

Traced on game 1 with the policies above: **V_eval = 0.0** ($A: 0.5 \cdot (-50) + 0.5 \cdot 50 = 0$). This computation is exact, but could take exponential time! (figure: `game-evaluation.png` shows the filled-in tree.)

**Summary:**

- Value is the expected utility of the game
- Monte Carlo: we can simulate the game and average the utilities
- Recurrence to compute value exactly (but could take exponential time!)
- Analogous to policy evaluation in MDPs

---

## Section: Expectimax (`expectimax`, `V_exptmax`)

**Previously: we can evaluate a fixed agent policy. Now: find the optimal agent policy.**

Still assume the opponent's policy is **given** (e.g. the opponent plays randomly, 0.5/0.5). (figures: `expectimax-graphical.png`, `expectimax-recurrence.png`)

```python
def V_exptmax(game, opp_policy, state):
    if game.is_end(state):
        return game.utility(state)
    player = game.player(state)
    if player == "agent":
        # Choose the action (next state) that maximizes utility
        values = [V_exptmax(game, opp_policy, s2) for s2 in game.successors(state).values()]
        return np.max(values)
    else:
        # Follow the opponent's policy
        values = [prob * V_exptmax(game, opp_policy, game.successors(state)[a])
                  for a, prob in opp_policy(state).items()]
        return np.sum(values)
```

In words: at the agent's turn, take the **max** over successor values; at the opponent's turn, take the **expectation** under the fixed opponent policy:

$$V_{exptmax}(s) = \begin{cases} u(s) & \text{end state} \\ \max_a V_{exptmax}(s') & \text{agent's turn} \\ \sum_a \pi_{opp}(a \mid s) \, V_{exptmax}(s') & \text{opponent's turn} \end{cases}$$

Traced on game 1 against a random opponent: values of A, B, C are **0.0, 2.0, 5.0** → **V_exptmax(root) = 5.0**, and **now the optimal action is to choose C!** (figure: `expectimax.png`) — the same C the class mostly chose in the opening poll, against an opponent assumed random and therefore lenient.

**Summary:**

- Expectimax: find the optimal agent policy with respect to a fixed opponent policy
- Analogous to value iteration in MDPs

---

## Section: Minimax (`minimax`, `V_minmax`)

**Previously: assumed the opponent policy was known (expectimax). The whole point of games is that we don't know the opponent policy! What should we do?**

**Minimax**: assume the opponent plays their best possible strategy. Here is the minimax recurrence (figures: `minimax-graphical.png`, `minimax-recurrence.png`):

```python
def V_minmax(game, state):
    if game.is_end(state):
        return game.utility(state), None
    player = game.player(state)
    successors = game.successors(state)
    values = {action: V_minmax(game, next_state)[0]
              for action, next_state in successors.items()}
    if player == "agent":   # Agent maximizes utility
        action, value = max(values.items(), key=lambda x: x[1])
    else:                   # Opponent minimizes utility
        action, value = min(values.items(), key=lambda x: x[1])
    return value, action
```

In words: the agent maximizes, the opponent minimizes:

$$V_{minmax}(s) = \begin{cases} u(s) & \text{end state} \\ \max_a V_{minmax}(s') & \text{agent's turn} \\ \min_a V_{minmax}(s') & \text{opponent's turn} \end{cases}$$

**Here's what it looks like for game 1** (figure: `minimax.png`): opponent's values at A, B, C are min(−50, 50) = **−50**, min(1, 3) = **1**, min(−5, 15) = **−5**; the agent takes the max → **V_minmax(root) = (1, "B")** — choose B (the bin the lecturer noted few people picked).

### The halving game under minimax

`HalvingGame(n=11)`, agent plays the minimax policy, opponent plays random (0.5/0.5). With `set_random_seed(1)`:

- One traced rollout: agent `decrement` → (10, opp); opp `half` → (5, agent); agent `decrement` → (4, opp); opp `decrement` → (3, agent); agent `half` → (1, opp); opp `decrement` → (0, agent); **utility 1**.
- 10 rollouts: utilities all 1, **mean 1.0** — "The minimax policy crushes the random policy!"

**We can compute the minimax value and optimal action for each state** (values from the agent's perspective, traced for n = 1..11):

| n | value | optimal action |
| --- | --- | --- |
| 1 | −1 | decrement |
| 2 | +1 | decrement |
| 3 | +1 | half |
| 4 | −1 | decrement |
| 5 | +1 | decrement |
| 6 | −1 | decrement |
| 7 | +1 | decrement |
| 8 | +1 | half |
| 9 | +1 | half |
| 10 | −1 | decrement |
| 11 | +1 | decrement |

If the value is 1, the agent is guaranteed to win no matter what the opponent does. If the value is −1, the opponent is guaranteed to win **if** they play optimally.

**Perfect play, solved games:**

- When agent and opponent play optimally, this is **perfect play**.
- A game is **solved** if the outcome under perfect play is known.
- Strongly solved games: tic-tac-toe, nim, connect four
- Weakly solved games (from initial position): checkers, Othello
- Unsolved games: chess, Go (even if computers are superhuman!)

**Summary:**

- Agent maximizes utility, opponent minimizes utility
- Unlike expectimax, no fixed policies given
- No analogy in MDPs

---

## Section: Face-off (`face_off`)

**Computing recurrences produce different policies:**

- $V_{minmax}$ → $\pi_{max}$ (agent), $\pi_{min}$ (opponent)
- $V_{exptmax}$ → $\pi_{exptmax}(7)$ (agent), $\pi_7$ (opponent)

**Each "optimal" policy bakes in assumptions about how the other player plays:**

- Minimax: $\pi_{max}$ is optimal against $\pi_{min}$
- Minimax: $\pi_{min}$ is optimal against $\pi_{max}$
- Expectimax: $\pi_{exptmax}(7)$ is optimal against $\pi_7$

What happens when these policies play different policies? Let $V(\pi_{agent}, \pi_{opp})$ be the value of the game when $\pi_{agent}$ plays $\pi_{opp}$. We can play the policies against each other (figure: `face-off.png`).

**Property 1: $\pi_{max}$ is the best policy against $\pi_{min}$** (figure: `minimax-prop1.png`)

$$V(\pi_{exptmax}(7), \pi_{min}) \leq V(\pi_{max}, \pi_{min})$$

**Property 2: $\pi_{min}$ is the best policy against $\pi_{max}$** (figure: `minimax-prop2.png`)

$$V(\pi_{max}, \pi_{min}) \leq V(\pi_{max}, \pi_7)$$

If the minimax value is 1, that means the agent is guaranteed to win no matter what the opponent does!

**Property 3: $\pi_{exptmax}(7)$ is optimal against $\pi_7$** (figure: `minimax-prop3.png`)

$$V(\pi_{max}, \pi_7) \leq V(\pi_{exptmax}(7), \pi_7)$$

You can do better than minimax if you know your opponent! Putting everything together (figure: `face-off-rel.png`).

**Summary:**

- Always think about optimality with respect to what?
- Minimax provides both optimality and a lower bound against any (unknown) opponent

---

## Section: Expectiminimax (`expectiminimax`)

**Game 2** (figure: `game2.png`): the agent picks a bin, then a **coin is flipped** — if heads, the bin is shifted one position to the left with wraparound (A→C, B→A, C→B), if tails it stays — and the opponent picks a number from the resulting bin. Let's draw the game tree for this game (figure: `expectiminimax.png`): it has a third kind of node, a **chance node** for the coin, played by a fixed "policy" $\pi_{coin}$ (0.5/0.5).

Traced values on the expectiminimax tree: the chance nodes average the min-values of the two possible bins — A-coin: $0.5 \cdot (-50) + 0.5 \cdot (-5) =$ **−27.5**, B-coin: $0.5 \cdot 1 + 0.5 \cdot (-50) =$ **−24.5**, C-coin: $0.5 \cdot (-5) + 0.5 \cdot 1 =$ **−2**; the agent maximizes → **V_exptminmax(root) = −2, choose C**. (The coin makes game 2 much worse for the agent than game 1: even the best bin C averages only −2, because half the time the coin moves it to B.)

**Here's the recurrence** (figures: `expectiminimax-graphical.png`, `expectiminimax-recurrence.png`):

$$V_{exptminmax}(s) = \begin{cases} u(s) & \text{end state} \\ \max_a V(s') & \text{agent's turn} \\ \min_a V(s') & \text{opponent's turn} \\ \sum_a \pi_{coin}(s, a) \, V(s') & \text{coin (chance) node} \end{cases}$$

In general, one could imagine many possible extensions:

- More than two players (either agents or opponents)
- Players taking extra turns or choosing who goes next

Just define the recurrence $V_{\dots}()$ generally!

**Things that aren't covered by game trees:**

- Games with imperfect information (e.g., poker)
- Non-zero-sum games (e.g., prisoner's dilemma)
- Non-turn-based games (e.g., rock-paper-scissors)

---

## Section: Alpha-beta pruning (`alpha_beta_pruning`)

**Now let's go back to the basic minimax formulation** (figure: `minimax-recurrence.png`). Recall that it takes exponential time in general! How can we speed it up?

**Alpha-beta pruning**: don't visit states that we know aren't going to be optimal. But how do we know if we don't look?

**Simple example:**

- A has value in [3, 5]
- B has value in [5, 100]
- Which one would you choose?

No matter what A and B turn out to be, B will always be better. And we don't need to compute A and B exactly! This idea is called **branch and bound**, used heavily in combinatorial optimization.

**Let's consider the following game tree** (figure: `alpha-beta-example.png`): root computes $\max(3, \min(2, X)) = 3$ no matter what X is... so don't need to explore X!

**In general** (figure: `alpha-beta-optimal-path.png`):

- While we're exploring, each max (min) node has a lower (upper) bound on its value
- The minimax value came from some leaf
- **Optimal path** is the path taken by minimax policies to that leaf
- This optimal path should be within the lower/upper bounds
- Prune a node if its bounds don't overlap every ancestor's bounds

**Let's work this out on the board** (figure: `alpha-beta-example2.png`): the lecturer's worked example — a max root over three min nodes with leaves 9, 7 | 6, [max: 3, 4], [max: 7, 9] | 8, 3. The min nodes resolve to min(9,7) = **7**, min(6, max(3,4), max(7,9)) = **4**, min(8,3) = **3**, so the root is max(7, 4, 3) = **7** from the leftmost subtree. During the walk-through the α (lower bound for max) and β (upper bound for min) intervals are maintained at each node, and a branch is pruned as soon as its interval no longer overlaps every ancestor's; the walked value comes out 7 (see the transcript around 59:17–1:01:37 for the walk-through).

**Ordering:**

- The order in which the children are visited impacts what you can prune (figure: `move-ordering-example.png`)
- In practice, use a heuristic (evaluation function later) to order the children:
  - Decreasing for max nodes
  - Increasing for min nodes

**Summary:**

- Alpha-beta pruning: speed up minimax search, exact computation
- Want to order actions to shrink the bounds as fast as possible

---

## Section: Evaluation functions (`evaluation_functions`)

**Now let's go back to the basic minimax formulation** (figure: `minimax-recurrence.png`). Recall that it takes exponential time in general! How can we compute things approximately?

**Key idea: define an evaluation function** (figure: `eval-functions-chess.png`):

- In the game, only thing that matters is who wins at the end
- The evaluation function captures prior knowledge about what might be good

**Using an evaluation function, can define a recurrence that keeps track of a depth d** (figure: `eval-functions-recurrence.png`):

$$V_{minmax}(s, d) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \text{Eval}(s) & d = 0 \\ \max_a V_{minmax}(\text{Succ}(s, a), d) & \text{Player}(s) = \text{agent} \\ \min_a V_{minmax}(\text{Succ}(s, a), d - 1) & \text{Player}(s) = \text{opp} \end{cases}$$

(As printed on the slide: the agent branch keeps the depth d and the opponent branch decrements to d − 1, so d counts opponent moves remaining until the horizon.)

- No guarantees that we will find the optimal policy!

**Summary:**

- Evaluation function: prior knowledge that evaluates a state
- Do search to a certain depth, and then use the evaluation function
- Analogy: FutureCost in search problems

---

## Figures (vision pass)

Vision pass on 2026-09-07 over the copies in the lecture repo at `images/`. Ground-truth cross-checks: all game-tree values and the face-off matrix (1, 2, −5, 5) reconcile with the execution trace and the source; game 2's coin-flip rule and the chess weights are confirmed by the transcript ([46:52], [1:07:47]).

### `game1.png`

Slide titled "Example: game 1" (orange heading with a 3D cube icon), stating the rules inside a bordered box:

- "You choose one of the three bins."
- "I choose a number from that bin."
- "Your goal is to maximize the chosen number."

Below are three rounded white cards; "You" is the agent and "I" is the opponent. Each card is labeled with a green capital letter and holds two numbers in black:

| Bin | Numbers inside |
|-----|----------------|
| A | -50, 50 |
| B | 1, 3 |
| C | -5, 15 |

The game is played in two steps: the agent picks one of the three bins (A, B, or C), then the opponent picks one of the two numbers inside it; the picked number is the agent's utility. Approx. 1113 × 689 px; numbers are the only data, colors: green bin letters, orange title.

### `game-tree.png`

Slide titled "Key idea: game tree" (red heading, yellow lightbulb icon), with the boxed text:

- "Each node is a decision point for a player."
- "Each root-to-leaf path is a possible outcome of the game."

Below is the game tree for game 1, drawn as plain unlabeled circles with black arrows:

- Root node (top center) — branching 3 ways to three circle nodes in a row (the agent's choice of bin A, B, or C; the arrows are unlabeled).
- Each of the three middle nodes branches 2 ways to leaf values (the opponent's choice of a number from the chosen bin; arrows unlabeled).
- Leaf utilities, left to right: -50, 50 (under the first middle node), 1, 3 (second), -5, 15 (third).

Leaves are drawn as bare numbers, not circles — they are terminal utilities for the agent. Matches Game1's utilities: A→(-50, 50), B→(1, 3), C→(-5, 15). Approx. 1119 × 632 px. Colors: white background, red title, yellow icon; tree itself is black/white.

### `halving-game.png`

Slide titled "Problem: halving game" (orange heading with colorful puzzle-piece icons), containing one bordered box with three lines:

- "Start with a number $N$."
- "Players take turns either decrementing $N$ or replacing it with $\left\lfloor \frac{N}{2} \right\rfloor$."
- "The player that is left with 0 wins."

So the two legal moves each turn are: decrement ($N \to N-1$) or halve ($N \to \lfloor N/2 \rfloor$); the player who first brings the number to 0 wins the game (utility +1 for the agent if the agent reaches 0, -1 if the opponent does). This is a sequential, turn-based two-player game akin to a nim-like game. Approx. 2146 × 509 px (as rendered in the native image; the slide itself is a wide banner).

### `game-evaluation-graphical.png`

A minimal schematic of a rollout of the game under fixed policies: a horizontal chain of four empty circles (states) connected by arrows, the last arrow followed by an ellipsis "..." indicating continuation until a terminal state.

Arrows alternate labels: the first arrow is labeled $\pi_{\text{agent}}$, the second $\pi_{\text{opp}}$, the third $\pi_{\text{agent}}$, i.e., at each state the player whose turn it is (agent or opponent) takes an action sampled from that player's fixed policy $\pi_\text{player}$. Black and white, approx. 1184 × 296 px. Captures the "play the game" idea behind $V_{\text{eval}}$: follow the policies until a leaf, read the utility.

### `game-evaluation-recurrence.png`

The $V_{\text{eval}}$ recurrence, printed as a piecewise (cases) definition:

$$V_{\text{eval}}(s) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \sum_{a \in \text{Actions}(s)} \pi_{\text{agent}}(s, a)\, V_{\text{eval}}(\text{Succ}(s, a)) & \text{Player}(s) = \text{agent} \\ \sum_{a \in \text{Actions}(s)} \pi_{\text{opp}}(s, a)\, V_{\text{eval}}(\text{Succ}(s, a)) & \text{Player}(s) = \text{opp} \end{cases}$$

Symbols: $V_{\text{eval}}(s)$ is the value (expected utility) of the game starting from state $s$ under the fixed policies given to both players; $\text{Utility}(s)$ the terminal utility of the game for the agent; $\text{IsEnd}(s)$ true when $s$ is a leaf; $\text{Actions}(s)$ the actions available in $s$; $\text{Succ}(s,a)$ the successor state of taking $a$ in $s$; $\text{Player}(s)$ the player to move; $\pi_{\text{agent}}(s,a)$ and $\pi_{\text{opp}}(s,a)$ the probabilities that the agent's / opponent's policy assigns to action $a$ in state $s$. The two non-terminal branches average (expect over) the policy of whichever player is to move, corresponding to $p(s,a)$-weighted sums. Approx. 1884 × 383 px.

### `game-evaluation.png`

Slide titled "Example: game evaluation" (orange heading, cube icon). The two assigned policies are printed at top:

- $\pi_{\text{agent}}(s) = \mathbf{A}$ — the agent deterministically always chooses bin A.
- $\pi_{\text{opp}}(s,a) = \tfrac{1}{2}$ for all $a \in \text{Actions}(s)$ — the opponent picks uniformly at random.

The game tree (game 1) is drawn with edge labels in parentheses giving each action's probability under the corresponding policy:

- Root (agent): edges labeled (1) to the A-node, (0) to the B-node, (0) to the C-node. The (1) edge and its subtree are drawn green (positive-probability branch); the (0) edges are black.
- Each A/B/C node (opponent): edges labeled (0.5) and (0.5) to its two leaves, all drawn green.

$V_{\text{eval}}$ values are printed in green at each internal node: A-node = 0, B-node = 2, C-node = 5, root = 0. Leaf utilities: -50, 50; 1, 3; -5, 15. (Checks: $0.5(-50)+0.5(50)=0$; $0.5(1)+0.5(3)=2$; $0.5(-5)+0.5(15)=5$; $1\cdot0+0\cdot2+0\cdot5=0$.)

A boxed result under the tree reads $V_{\text{eval}}(s_{\text{start}}) = 0$ (black text). Approx. 1000 × 1024 px; green = values / positive-probability edges.

### `expectimax-graphical.png`

Like the evaluation schematic, but the nodes distinguish the players: triangles are agent (max) decision nodes and circles are opponent (chance-ish) nodes, since the agent has no fixed policy here.

A horizontal chain: triangle $\to^{\pi_{\text{agent}}}$ circle $\to^{\pi_{\text{opp}}}$ triangle $\to^{\pi_{\text{agent}}}$ circle $\to \cdots$ (ellipsis, continuing to a leaf).

The agent's arrows are marked $\pi_{\text{agent}}$ (the agent picks an action; in expectimax this becomes a max), and the opponent's arrows are marked $\pi_{\text{opp}}$ (the opponent follows the fixed stochastic policy). Black and white, approx. 1196 × 262 px; states unlabeled.

### `expectimax-recurrence.png`

The $V_{\text{exptmax}}$ recurrence, as a cases definition; the $\mathbf{max}_{\dots}$ operator on the agent branch is printed in red, the rest in black:

$$V_{\text{exptmax}}(s) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \mathbf{\max}_{a \in \text{Actions}(s)} V_{\text{exptmax}}(\text{Succ}(s, a)) & \text{Player}(s) = \text{agent} \\ \sum_{a \in \text{Actions}(s)} \pi_{\text{opp}}(s, a)\, V_{\text{exptmax}}(\text{Succ}(s, a)) & \text{Player}(s) = \text{opp} \end{cases}$$

Symbols (same conventions as $V_{\text{eval}}$): $V_{\text{exptmax}}(s)$ is the value of the game under the optimal agent policy given a *fixed* opponent policy $\pi_{\text{opp}}$; $\text{Utility}(s)$ terminal utility; $\text{IsEnd}(s)$ leaf test; $\text{Actions}(s)$ — subscript is "$a \in \text{Actions}(s)$" in both non-terminal cases; $\text{Succ}(s,a)$ successor state; $\text{Player}(s)$ whose turn; $\pi_{\text{opp}}(s,a)$ the opponent's fixed probability of action $a$ in $s$. At agent nodes the agent maximizes over its actions (no agent policy appears — it is optimized); at opponent nodes the value is the $\pi_{\text{opp}}$-weighted expectation. Approx. 1891 × 313 px.

### `expectimax.png`

Slide titled "Example: expectimax" (orange heading, cube icon). Fixed opponent policy at top: $\pi_{\text{opp}}(s,a) = \tfrac{1}{2}$ for $a \in \text{Actions}(s)$ (uniform random).

Game tree with the root drawn as a **triangle** (agent / max node), and A, B, C drawn as circles (opponent nodes):

- Agent values (red): root = 5. Opponent values (green): A-node = 0, B-node = 2, C-node = 5. Leaf utilities: -50, 50; 1, 3; -5, 15.
- Edges from A, B, C nodes to leaves are labeled (0.5) and (0.5), drawn green (positive probability under the uniform opponent policy).
- The root→C edge is drawn **red** and the other two root edges black: C is the optimal action for the agent (max over {0, 2, 5}).

Boxed result below: $V_{\text{exptmax}}(s_{\text{start}}) = 5$ — note the value 5 exactly equals the C-node's green expectimax value. Approx. 1000 × 1021 px; red = agent/max values and optimal action, green = opponent expectation values.

### `minimax-graphical.png`

The alternating-node chain for minimax, with distinct icons for the two players: upward triangles = agent (max) nodes, downward (inverted) triangles = opponent (min) nodes.

Chain: triangle $\to^{\pi_{\text{agent}}}$ inverted triangle $\to^{\pi_{\text{opp}}}$ triangle $\to^{\pi_{\text{agent}}}$ inverted triangle, with a "..." ellipsis next to the last inverted triangle indicating continuation. Labels: $\pi_{\text{agent}}$ on the arrows leaving agent (max) nodes, $\pi_{\text{opp}}$ on arrows leaving opponent (min) nodes. Black and white, approx. 1196 × 262 px; node states unlabeled.

### `minimax-recurrence.png`

The $V_{\text{minmax}}$ recurrence, as a cases definition; $\mathbf{max}$ (agent branch) is in red and $\mathbf{min}$ (opponent branch) in blue, the rest black:

$$V_{\text{minmax}}(s) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \mathbf{\max}_{a \in \text{Actions}(s)} V_{\text{minmax}}(\text{Succ}(s, a)) & \text{Player}(s) = \text{agent} \\ \mathbf{\min}_{a \in \text{Actions}(s)} V_{\text{minmax}}(\text{Succ}(s, a)) & \text{Player}(s) = \text{opp} \end{cases}$$

Symbols: $V_{\text{minmax}}(s)$ is the minimax value of state $s$ — the outcome under perfect play, where the agent maximizes and the opponent minimizes utility. $\text{Utility}(s)$ terminal utility; $\text{IsEnd}(s)$ leaf test; $\text{Actions}(s)$ actions available (the subscript "$a \in \text{Actions}(s)$" appears in both branches); $\text{Succ}(s,a)$ successor state; $\text{Player}(s)$ whose turn it is; the agents "agent" and "opp" (opponent). In contrast to $V_{\text{eval}}$ / $V_{\text{exptmax}}$, no policy is given for either player: the opponent is *assumed* to minimize. Approx. 1812 × 313 px.

### `minimax.png`

Slide titled "Example: minimax" (orange heading, cube icon).

Game tree for game 1 with node icons: root and A/B/C nodes are triangles (agent = upward triangle, opponent = downward inverted triangle):

- Agent (max) values in red: root = 1. Opponent (min) values in blue: A = -50, B = 1, C = -5 — each is the min of its two leaf children (A: min(-50, 50) = -50; B: min(1, 3) = 1; C: min(-5, 15) = -5).
- The minimizing child edge at each opponent node is drawn blue (A→-50, B→1, C→-5); the dominated child edge is black.
- The root→B edge is drawn red: B is the optimal agent action, since max(-50, 1, -5) = 1 at the root.
- Leaf utilities unchanged: -50, 50; 1, 3; -5, 15.

Boxed result: $V_{\text{minmax}}(s_{\text{start}}) = 1$ (black text). Approx. 1000 × 776 px; red = agent/max values and optimal action, blue = opponent/min values and minimizing edges.

### `face-off.png`

A 2×2 table of game values $V(\pi_{\text{agent}}, \pi_{\text{opp}})$ (value of the game when the agent plays row policy and the opponent plays column policy) — "facing off" the policies induced by minimax and expectimax against each other. The table is symbolic only; no numeric utilities are shown.

| | $\pi_{\text{min}}$ (blue) | $\pi_7$ (blue) |
|---|---|---|
| $\pi_{\text{max}}$ (red) | $V(\pi_{\text{max}}, \pi_{\text{min}})$ | $V(\pi_{\text{max}}, \pi_7)$ |
| $\pi_{\text{exptmax}(7)}$ (red) | $V(\pi_{\text{exptmax}(7)}, \pi_{\text{min}})$ | $V(\pi_{\text{exptmax}(7)}, \pi_7)$ |

- Red entries are agent policies: $\pi_{\text{max}}$ (minimax policy) and $\pi_{\text{exptmax}(7)}$ (expectimax policy against opponent policy $\pi_7$).
- Blue entries are opponent policies: $\pi_{\text{min}}$ (minimax opponent policy) and $\pi_7$ (a stochastic opponent policy, uniform-ish at the A/B/C nodes), attached to the header row.
- Each cell value is spelled out inside $V(\dots)$ with $\pi_{\text{exptmax}(7)}$ written as "π_exptmax(7)". The subscript "(7)" refers to the expectimax opponent policy $\pi_7$.

Approx. 2506 × 634 px; white background, thin black border.

### `minimax-prop1.png`

Slide titled "Proposition: best against minimax opponent" (blue heading, glossy purple sphere icon). Boxed proposition:

$$V(\pi_{\text{max}}, \pi_{\text{min}}) \geq V(\pi_{\text{agent}}, \pi_{\text{min}}) \quad \text{for all } \pi_{\text{agent}}$$

(π_max and π_agent printed in red, π_min in blue). Meaning: when the opponent plays the minimax policy π_min, no agent policy beats the minimax policy π_max — π_max is optimal against π_min.

Below is the same minimax game tree as in minimax.png: root triangle (red) with value 1; A/B/C inverted triangles (blue) with values -50, 1, -5; red root→B edge (optimal agent action); blue minimizing edges (A→-50, B→1, C→-5); leaves -50, 50; 1, 3; -5, 15. Approx. 2348 × 1253 px.

### `minimax-prop2.png`

Slide titled "Proposition: lower bound against any opponent" (blue heading, glossy purple sphere icon). Boxed proposition:

$$V(\pi_{\text{max}}, \pi_{\text{min}}) \leq V(\pi_{\text{max}}, \pi_{\text{opp}}) \quad \text{for all } \pi_{\text{opp}}$$

(π_max in red, π_min and π_opp in blue). Meaning: if the agent plays the minimax policy π_max, its expected utility is at least the minimax value no matter what (even the best) opponent policy plays — the minimax value is a guaranteed lower bound; playing minimax cannot do worse than $V(\pi_{\text{max}}, \pi_{\text{min}})$.

Below is the same minimax tree as in minimax.png / minimax-prop1.png: root triangle (red) valued 1; A/B/C inverted triangles (blue) valued -50, 1, -5; red optimal edge root→B; blue minimizing edges; leaves -50, 50; 1, 3; -5, 15. Approx. 2393 × 1257 px.

### `minimax-prop3.png`

Slide titled "Proposition: not optimal if opponent is known" (blue heading, glossy purple sphere icon). Boxed proposition:

$$V(\pi_{\text{max}}, \pi_7) \leq V(\pi_{\text{exptmax}(7)}, \pi_7) \quad \text{for opponent } \pi_7$$

(π_max and π_exptmax(7) in red, π_7 in blue). Meaning: against the *known* opponent policy π_7, the expectimax policy π_exptmax(7) does at least as well as the minimax policy π_max — minimax is optimal against π_min but not against a known, possibly suboptimal, opponent: if you know your opponent you can do better than minimax.

Below is the expectimax tree (cf. expectimax.png): root triangle (red) valued 5; A/B/C circles (green) valued 0, 2, 5; edges from A/B/C labeled (0.5),(0.5) in green; red root→C edge (optimal action); leaves -50, 50; 1, 3; -5, 15. Approx. 2375 × 1346 px.

### `face-off-rel.png`

The face-off table of the previous slide, now filled with numbers (for game 1) and relation symbols:

| | $\pi_{\text{min}}$ (blue) | $\pi_7$ (blue) |
|---|---|---|
| $\pi_{\text{max}}$ (red) | $V(\pi_{\text{max}}, \pi_{\text{min}}) = \mathbf{1}$ | $V(\pi_{\text{max}}, \pi_7) = \mathbf{2}$ |
| $\pi_{\text{exptmax}(7)}$ (red) | $V(\pi_{\text{exptmax}(7)}, \pi_{\text{min}}) = \mathbf{-5}$ | $V(\pi_{\text{exptmax}(7)}, \pi_7) = \mathbf{5}$ |

Additional printed relations:

- Horizontally between the two top cells: $V(\pi_{\text{max}}, \pi_{\text{min}}) \leq V(\pi_{\text{max}}, \pi_7)$ (property 2; numerically 1 ≤ 2).
- Vertically between the rows, the two cells are connected by rotated (vertical) inequality glyphs: left column shows a "|V"-like symbol (a rotated ≥), relating 1 (top) and -5 (bottom) — i.e. $V(\pi_{\text{exptmax}(7)}, \pi_{\text{min}}) = -5 \leq 1 = V(\pi_{\text{max}}, \pi_{\text{min}})$ (property 1); right column shows a "|∧"-like symbol (a rotated ≤), relating 2 (top) and 5 (bottom) — i.e. $V(\pi_{\text{max}}, \pi_7) = 2 \leq 5 = V(\pi_{\text{exptmax}(7)}, \pi_7)$ (property 3).

Together: $-5 \leq 1 \leq 2 \leq 5$ along the chain. Numbers printed in black below each $V$ expression. Approx. 2500 × 853 px.

### `game2.png`

Slide titled "Example: game 2" (orange heading, cube icon). Bordered box with the rules; the second line is printed in red:

- "You choose one of the three bins."
- **"Flip a coin; if heads, then move one bin to the left (with wrap around)."** (red — the new chance step)
- "I choose a number from that bin."
- "Your goal is to maximize the chosen number."

Below are three cards (black labels here, not green) with the same numbers as game 1: A → -50, 50; B → 1, 3; C → -5, 15.

Game 2 is game 1 with an added randomness step: after the agent chooses a bin, a fair coin is flipped; if heads, the bins are shifted one position to the left cyclically (with wraparound) before the opponent picks a number. The neighbor mappings are made explicit in the expectiminimax tree (cf. expectiminimax.png). Approx. 2242 × 960 px.

### `expectiminimax.png`

Slide titled "Example: expectiminimax" (orange heading, cube icon). The coin policy is stated at top:

- $\pi_{\text{coin}}(s,a) = \tfrac{1}{2}$ for $a \in \{0, 1\}$ — fair coin; action $a = 1$ (heads) shifts the chosen bin one step left with wraparound, $a = 0$ (tails) leaves it.

Game tree of game 2 (left to right; coin branches shown as (0.5) edges):

- Root: triangle (agent/max), value **-2** (red). Edges: to the A-coin node (black), B-coin node (black), C-coin node (red = optimal action C; $\max(-27.5, -24.5, -2) = -2$).
- Chance (coin) circles, green values: A-coin = **-27.5**, B-coin = **-24.5**, C-coin = **-2**. Each has two (0.5) edges (green) to opponent (min) nodes.
- Opponent inverted-triangle (min) nodes, blue values, and their leaves:
  - A-coin children: **-50** (bin A, leaves -50, 50) and **-5** (bin C, leaves -5, 15).
  - B-coin children: **1** (bin B, leaves 1, 3) and **-50** (bin A, leaves -50, 50).
  - C-coin children: **-5** (bin C, leaves -5, 15) and **1** (bin B, leaves 1, 3).
- Blue arrows on each min node point to the minimizing child.

Chain values check: $0.5(-50)+0.5(-5) = -27.5$; $0.5(1)+0.5(-50) = -24.5$; $0.5(-5)+0.5(1) = -2$. Boxed result: $V_{\text{exptminmax}}(s_{\text{start}}) = \mathbf{-2}$ (black). The heads shift thus maps A→C, B→A, C→B. Approx. 1600 × 800 px

### `expectiminimax-graphical.png`

The schematic chain for expectiminimax, with three node types: upward triangle = agent (max), circle = chance/coin (random), downward inverted triangle = opponent (min).

Chain: triangle $\to^{\pi_{\text{agent}}}$ circle $\to^{\pi_{\text{coin}}}$ inverted triangle $\to^{\pi_{\text{opp}}}$ circle $\to \cdots$ (ellipsis after the last circle, indicating the game continues/pattern repeats). Arrow labels: $\pi_{\text{agent}}$ leaving the agent (max) node, $\pi_{\text{coin}}$ leaving the chance node, $\pi_{\text{opp}}$ leaving the opponent (min) node. Black and white, approx. 1196 × 262 px; states unlabeled.

### `expectiminimax-recurrence.png`

The $V_{\text{exptminmax}}$ recurrence, as a cases definition with three colored non-terminal branches: $\mathbf{max}$ (agent) in red, $\mathbf{min}$ (opponent) in blue, $\sum$ and $\pi_{\text{coin}}$ (chance) in green:

$$V_{\text{exptminmax}}(s) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \mathbf{\max}_{a \in \text{Actions}(s)} V_{\text{exptminmax}}(\text{Succ}(s, a)) & \text{Player}(s) = \text{agent} \\ \mathbf{\min}_{a \in \text{Actions}(s)} V_{\text{exptminmax}}(\text{Succ}(s, a)) & \text{Player}(s) = \text{opp} \\ \sum_{a \in \text{Actions}(s)} \pi_{\text{coin}}(s, a)\, V_{\text{exptminmax}}(\text{Succ}(s, a)) & \text{Player}(s) = \text{coin} \end{cases}$$

Symbols: $V_{\text{exptminmax}}(s)$ is the game value under optimal agent and opponent (max/min) play, with pure randomness (the "coin") governed by the fixed distribution $\pi_{\text{coin}}(s,a)$; $\text{Utility}(s)$ terminal utility; $\text{IsEnd}(s)$ leaf test; $\text{Actions}(s)$ the actions available in $s$; $\text{Succ}(s,a)$ the successor of $a$ in $s$; $\text{Player}(s)$ whose turn — "agent", "opp" (opponent), or "coin" (chance). The coin branch is an expectation over $\pi_{\text{coin}}$, which is why the sum/π are colored green (chance), analogous to the (0.5) branches in the expectiminimax tree. Approx. 1898 × 321 px.

### `alpha-beta-example.png`

The motivating small tree for pruning (black edges default; colored as noted):

- Root: triangle (agent, max) with value **3** printed in red to its right.
- Left child: inverted triangle (min) with value **3** printed in blue to its right; two leaves: **3** (blue edge — the minimizing child) and **5** (black edge). So this node's value is $\min(3, 5) = 3$.
- Right child: inverted triangle (min) with **≤ 2** printed in blue to its right; it shows only one leaf, **2**, reached via a blue edge — its other child (the hypothetical $X$) is not drawn, indicating it need not be explored.
- The root→left-min edge is drawn red (the branch that determines the root value).

Logic: the left subtree evaluates to 3, so the root's value is $\max(3, \min(2, X)) = 3$ no matter what $X$ is: the right min node's value is already known to be ≤ 2 < 3, so $X$ (its unvisited subtree) can be pruned. Approx. 1176 × 868 px.

### `alpha-beta-optimal-path.png`

General alpha-beta diagram: a root-to-leaf "optimal path" descends diagonally down-right through a path of alternating nodes, while every off-path branch is collapsed to a "…" ellipsis (subtrees pruned/unvisited). Node bounds are printed next to each node on the path, colored by node type:

- Root: triangle (max) with bound **≥ 6** (red); left child is "…" (ellipsis far-left), right child continues the path.
- Next: inverted triangle (min) with bound **≤ 8** (blue); left child "…", right child continues.
- Next: triangle (max) with bound **≥ 3** (red); left child "…", right child continues.
- Next: inverted triangle (min) with bound **≤ 5** (blue); "…" below it, the path continues there (optimum is somewhere along this path).

Key idea: while exploring, each max node carries a lower bound (≥, red) and each min node an upper bound (≤, blue) on its value; the minimax value comes from some leaf on this path, whose subtree intervals must overlap all ancestors' bounds; a node can be pruned when its bounds don't overlap every ancestor's bounds. Approx. 1178 × 1008 px.

### `alpha-beta-example2.png`

The "work this out on the board" worked-example tree (black and white only, no values on internal nodes): root is a triangle (max) with three children, all inverted triangles (min):

- Left min node: two leaves — **9** and **7**. Value $\min(9,7) = 7$.
- Middle min node: three branches — a direct leaf **6**, then a triangle (max) with leaves **3** and **4**, then a triangle (max) with leaves **7** and **9**. (Middle-min value: $\min(6, \max(3,4), \max(7,9)) = \min(6, 4, 9) = 4$.)
- Right min node: two leaves — **8** and **3**. Value $\min(8,3) = 3$.

So the nine leaf numbers, left to right: 9, 7, 6, 3, 4, 7, 9, 8, 3. The root (max) value would be $\max(7, 4, 3) = 7$, achieved via the left-most min subtree; with alpha-beta, several subtrees (notably the middle min's third branch and the right min node) can be pruned. Approx. 2609 × 1232 px.

### `move-ordering-example.png`

The move-ordering example tree showing how the *order* in which children are visited affects how much can be pruned:

- Root: triangle (max), value **3** printed in red to its right. The root→right-min edge is red (the branch that determines the root's maximin value).
- Left child: inverted triangle (min), value **2** (blue, to its right); leaves **2** (blue edge — the minimizing child $\min(2,10)=2$) and **10** (black edge).
- Right child: inverted triangle (min), value **3** (blue); leaves **3** (blue edge, $\min(3,5)=3$) and **5** (black edge).

Root value: $\max(2, 3) = 3$ (red). The lesson: if the right subtree is visited first at the root, α becomes 3; then at the left min node the first leaf 2 already caps the node at ≤ 2 < α = 3, and the second leaf (10) can be pruned. The tighter the bounds, the more pruning — hence the practical heuristic: order children decreasingly at max nodes, increasingly at min nodes. Approx. 1163 × 868 px.

### `eval-functions-chess.png`

Slide titled "Example: chess" (orange heading, cube icon), with a chess diagram at top and a boxed evaluation function below.

Chess diagram: standard initial position on an 8×8 board with files a–h (bottom/top labels) and ranks 1–8 (side labels); tan and brown alternating squares; white pieces on ranks 1–2, black pieces on ranks 7–8 (both sides' pawns in front row; queens on the d-file, kings on the e-file). No notation text on the squares.

Boxed equations (Deep-Blue-style hand-written evaluation):

$$\text{Eval}(s) = \text{material} + \text{mobility} + \text{king-safety} + \text{center-control}$$

$$\text{material} = 10^{100}(K - K') + 9(Q - Q') + 5(R - R') + 3(B - B' + N - N') + 1(P - P')$$

$$\text{mobility} = 0.1(\text{num-legal-moves} - \text{num-legal-moves}')$$

with a trailing "…" line indicating further terms omitted. Symbols: unprimed letters (K king, Q queen, R rook, B bishop, N knight, P pawn) are counts/pieces for the agent (white), primed for the opponent (black); material weights: king essentially infinite (10^100), queen 9, rook 5, bishop/knight 3, pawn 1; mobility is weighted difference in number of legal moves; numerics: 0.1 mobility weight, 10^100 king weight, weights 9, 5, 3, 1. Approx. 2018 × 1152 px.

### `eval-functions-recurrence.png`

Heading (blue): "Limited depth tree search (stop at maximum depth $d_{\text{max}}$):"

Above the equation is a schematic tree "fan" (a wide flat triangle) whose top portion — near the apex, i.e. the first $d_{\text{max}}$ levels from the root — is shaded gray: the searched region; the unshaded lower wedge is the part of the tree left unexplored.

Boxed recurrence ($V_{\text{minmax}}$ with a depth parameter $d$; branch-specific symbols in red):

$$V_{\text{minmax}}(s, d) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \mathbf{\text{Eval}}(s) & d = 0 \\ \max_{a \in \text{Actions}(s)} V_{\text{minmax}}(\text{Succ}(s, a), d) & \text{Player}(s) = \text{agent} \\ \min_{a \in \text{Actions}(s)} V_{\text{minmax}}(\text{Succ}(s, a), d - 1) & \text{Player}(s) = \text{opp} \end{cases}$$

Symbols: same minimax conventions as before plus: $d$ = depth remaining until the horizon; $d = 0$ (red) triggers the base case; $\text{Eval}(s)$ (red) is the hand-crafted evaluation function applied at the depth cut-off; on the agent branch the depth stays $d$ (printed $d$ in red), on the opponent branch the depth decrements to $d-1$ (printed in red). Approx. 2018 × 687 px.

