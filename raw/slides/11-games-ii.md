---
title: Lecture 11 — Games II (Slides)
lecture: 11
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_commit: cbedcb10 (2025-10-27, "lecture 11")
source_files:
  - https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/cbedcb10/td_learning.py
  - https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/cbedcb10/simultaneous_games.py
rendered:
  - https://stanford-cs221.github.io/autumn2025-lectures/?trace=td_learning
  - https://stanford-cs221.github.io/autumn2025-lectures/?trace=simultaneous_games
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. Lecture 11 was delivered as
  two programs, `td_learning.py` (first half) and `simultaneous_games.py` (second
  half). There are no traditional slide numbers; sections correspond to function
  calls in the source. Both files are transcribed here AS OF COMMIT cbedcb10, which
  the course repo labels "lecture 11" and which landed at 1:22pm PDT on Mon Oct 27
  2025, eight minutes before the lecture. Both files were substantially rewritten in
  2026 for a later offering (commits a270be95, 1c0a8bd0, 4d53157a), so the repo's
  `main` branch and the live rendered UI no longer show what was taught; the pinned
  URLs above do. All demo outputs were read on 2026-10-22 from the execution traces
  committed at the same commit (`var/traces/td_learning.json`,
  `var/traces/simultaneous_games.json`); both traces embed source text identical to
  the pinned files. The 26 figures shown are described in the Figures section at the
  end of this file: 24 from a vision pass over the images at the same commit, and 2
  (`minimax.png`, `eval-functions-chess.png`) reused from lecture 10, where they are
  already described.
---

# Lecture 11 Slides — Games II

Lecture 11 closes the games module in two halves. The first half (`td_learning.py`) asks whether the hand-written evaluation functions of lecture 10 can be **learned**, and answers with **TD learning**: estimate the value function $V_\pi(s)$ from experience by bootstrapping, then play by choosing the move whose successor state has the best value, with agent and opponent sharing one value function (**self-play**). Backgammon is the running example, and the history runs from Samuel's checkers (1959) through TD-Gammon (1992) to AlphaGo Zero (2017). The second half (`simultaneous_games.py`) drops the turn-based assumption: in a **simultaneous game** both players move at once, so the game tree breaks down. For zero-sum games, **von Neumann's minimax theorem** shows that once mixed strategies are allowed, the order of play no longer matters — revealing an optimal mixed strategy does not hurt. For non-zero-sum games the analogue is the **Nash equilibrium**, which always exists (Nash, 1950) but need not be unique.

| Part | Source | Sections |
| ---- | ------ | -------- |
| 1. TD learning | `td_learning.py` | Recap · Motivation · TD learning · TD learning for games · Historical examples |
| 2. Simultaneous games | `simultaneous_games.py` | Introduction · Zero-sum: definitions · Zero-sum: minimax theorem · Non-zero-sum: definitions · Nash equilibria · Summary |

---

# Part 1 — `td_learning.py`

## Section: Recap and This Time (`main`, `review_games`)

**Last time: two-player zero-sum games.**

- Principle: minimax (also expectimax, expectiminimax). Figure: `minimax.png` (reused from lecture 10 — the minimax game tree for game 1, root value 1).
- Speeding up minimax: alpha-beta pruning (exact); evaluation functions (approximate). Figure: `eval-functions-chess.png` (reused from lecture 10 — the hand-written chess evaluation function, material + mobility + king-safety + center-control).
- "This was manual heuristics...can we learn the evaluation function?"

**Today: use reinforcement learning (TD learning) to learn the evaluation function!**

Order of the half: `td_learning_motivation` → `td_learning` → `td_learning_for_games` → `historical_examples`.

---

## Section: Motivation (`td_learning_motivation`)

Definitions, as printed:

- $V_\pi(s)$: value (expected utility) of following policy $\pi$ from state $s$.
- $Q_\pi(s, a)$: value (expected utility) of taking action $a$ in state $s$, and then following policy $\pi$.

**Recall SARSA from reinforcement learning:**

- On-policy: estimating Q-values of the current policy, $Q_\pi(s, a)$.
- Bootstrapping: target is immediate reward + estimated future reward.
- Figure: `sarsa-algorithm.png` (the SARSA update; see Figures).

Demo code (executed, but the calls go into `reinforcement_learning.py`, which the trace does not step into, so **no values are shown**): `SARSA(exploration_policy=partial(walk_tram_policy, 6), epsilon=0.4, discount=1, learning_rate=0.1)`, then three hand-fed pieces of feedback — `(1, walk, −1, 2)`, `(2, walk, −1, 3)`, `(3, tram, −2, 6, is_end=True)`.

**Policy improvement:** $\pi_{\text{new}}(s) = \arg\max_a Q_\pi(s, a)$. In other words, from Q-values you can read out a (myopically optimal) policy.

**Recall policy evaluation from MDPs:**

$$V_\pi(s) = Q_\pi(s, \pi(s))$$

$$Q_\pi(s, a) = \sum_{s'} T(s, a, s') \left( R(s, a, s') + \gamma V_\pi(s') \right)$$

**Question:** if you only knew $V_\pi(s)$, could you compute a policy from $V_\pi(s)$?

**Answer:**

$$\pi_{\text{new}}(s) = \arg\max_a \sum_{s'} T(s, a, s') \left( R(s, a, s') + \gamma V_\pi(s') \right)$$

Note this requires knowledge of the MDP (transitions $T$, rewards $R$)!

**However, for games, we do know $T$ and $R$!** Because it's deterministic, it simplifies to:

$$\pi_{\text{new}}(s) = \arg\max_a V_\pi(\text{Succ}(s, a))$$

**Wait, if we already know the MDP, why don't we just solve the MDP via value iteration?** Because it's too expensive (number of states is exponential)!

So we'll use reinforcement learning... not due to unknown MDP (original motivation), but because the number of states is exponential!

---

## Section: TD learning (`td_learning`, class `TDLearning`)

Link: [Autumn 2023 lecture](https://stanford-cs221.github.io/autumn2023/modules/module.html#include=games%2Ftd-learning.js&mode=print6pp).

**TD learning : $V_\pi$ :: SARSA : $Q_\pi$** (TD learning is to $V_\pi$ what SARSA is to $Q_\pi$).

- SARSA: $Q_\pi(s, a)$ tells us how good each action is.
- TD learning: $V_\pi(s)$ tells us how good each state is (since action → next state is known).

Assume **function approximation**: $V_\pi(s) = V(s; \mathbf{w})$ for some weights $\mathbf{w}$. In deep reinforcement learning, $V_\pi(s)$ is called a **value network**.

**Basic idea:**

- Get piece of experience $(s, a, r, s')$.
- Model predicts $V(s; \mathbf{w})$.
- Target (bootstrapping): $r + \gamma V(s'; \mathbf{w})$.
- Define squared loss: $L(\mathbf{w}) = \left( V(s; \mathbf{w}) - (r + \gamma V(s'; \mathbf{w})) \right)^2$.
- Take a gradient step: $\mathbf{w} = \mathbf{w} - \alpha \nabla_{\mathbf{w}} L(\mathbf{w})$.

Figure: `td-algorithm.png` — the boxed algorithm (see Figures):

$$\mathbf{w} \leftarrow \mathbf{w} - \eta \left[ V(s; \mathbf{w}) - (r + \gamma V(s'; \mathbf{w})) \right] \nabla_{\mathbf{w}} V(s; \mathbf{w})$$

with $V(s; \mathbf{w})$ labeled **prediction** and $r + \gamma V(s'; \mathbf{w})$ labeled **target**. Note on notation: the printed text calls the step size $\alpha$; the figure calls it $\eta$, the course's usual learning-rate symbol. The figure's update also drops the factor of 2 from differentiating the square and treats the target as a constant (no gradient through $V(s'; \mathbf{w})$); both are as printed.

### The code

```python
class TDLearning(RLAlgorithm):
    def __init__(self, mdp, exploration_policy, epsilon, discount, learning_rate):
        self.mdp = mdp
        ...
        self.V = defaultdict(float)

    def get_action(self, state):
        """Use MDP to compute Q_π(s, a) from V_π(s)."""
        if np.random.random() < self.epsilon:
            return self.exploration_policy(state)
        else:
            return self.pi(state)

    def pi(self, state):
        """Return the policy corresponding to the current V_π(s)."""
        # Note this is the only place where we use knowledge of the MDP
        q_values = {}  # action -> Q-value
        for action, successors in get_action_successors(self.mdp, state).items():
            q_values[action] = sum(succ.prob * (succ.reward + self.discount * self.V[succ.state]) for succ in successors)
        action = max(q_values.keys(), key=lambda action: q_values[action])
        return action

    def incorporate_feedback(self, state, action, reward, next_state, is_end):
        """Update V_π(s) based on the feedback (s, a, r, s')."""
        predicted = self.V[state]
        target = reward + self.discount * self.V[next_state]
        self.V[state] += self.learning_rate * (target - predicted)
```

The demo implementation keeps $V$ as a **table** (a `defaultdict` from state to value) rather than as weights $\mathbf{w}$, and its update moves $V(s)$ a fraction `learning_rate` of the way from the prediction toward the target. The comment in the source says the MDP is needed only in `pi`, to turn state values into Q-values for policy improvement.

### Demo: TD learning on the flaky tram MDP

`set_random_seed(1)`; `FlakyTramMDP(num_locs=6, failure_prob=0.1)`; exploration policy `walk_tram_policy` for 6 locations; `TDLearning(epsilon=0.2, discount=1, learning_rate=0.1)`. (The flaky tram: from location $s$, `walk` moves to $s+1$ at reward −1; `tram` moves to $2s$ at reward −2, but fails with probability `failure_prob` and stays put — see lecture 7's slide file.)

Traced values (the first `get_action` and first `incorporate_feedback` are stepped into; the remaining four calls are stepped over, so their values are not shown):

1. `get_action(state=1)`: the random draw is not below $\epsilon = 0.2$, so it calls `pi(1)`. With every $V$ still 0, the Q-values are **walk: −1.0** and **tram: −2.0** (tram: $0.9 \cdot (-2 + 0) + 0.1 \cdot (-2 + 0) = -2$). Chosen action: **walk**.
2. `incorporate_feedback(state=1, action="walk", reward=-1, next_state=2)`: predicted $V(1) = 0.0$; target $= -1 + 1 \cdot V(2) = -1.0$; update $V(1) \leftarrow 0 + 0.1 \cdot (-1 - 0) = $ **−0.1**. Trace shows `self.V = {2: 0.0, 1: -0.1}`.
3. Stepped over: `get_action(state=2)`, `incorporate_feedback(2, walk, −1, 3)`, `get_action(state=3)`, `incorporate_feedback(3, tram, −2, 6, is_end=True)`.

**Summary:**

- TD learning estimates $V_\pi(s)$ from experience $(s, a, r, s')$.
- On policy (we're not considering other actions).
- Uses bootstrapping.

---

## Section: TD learning for games (`td_learning_for_games`)

TD learning works for arbitrary MDPs. Now we adapt it to games:

- $\text{Succ}(s, a)$ captures the transition (deterministically).
- No utility until the end of the game.
- Two players: agent and opponent.

Both agent and opponent use the **same value function** $V_\pi(s)$ (**self-play**) ...but the agent maximizes and the opponent minimizes:

$$\pi_{\text{agent}}(s) = \arg\max_a V_\pi(\text{Succ}(s, a))$$

$$\pi_{\text{opp}}(s) = \arg\min_a V_\pi(\text{Succ}(s, a))$$

### Backgammon

- Each player tries to move their pieces off the board (figure: `backgammon1.jpg`).
- Dice determines how many places the player can move (figure: `backgammon2.jpg`).
- Other rules: if you land on a point with 1 opponent piece, move it to the bar; you cannot land on a point with >1 opponent pieces.

Note that this game has randomness (dice). Roll out the policies:

$$\pi_{\text{dice}} \to \pi_{\text{agent}} \to \pi_{\text{dice}} \to \pi_{\text{opp}} \to \pi_{\text{dice}} \to \pi_{\text{agent}} \to \dots$$

We are learning $\pi_{\text{agent}}$ and $\pi_{\text{opp}}$, but $\pi_{\text{dice}}$ is fixed.

**The parameterized value function.** First define a feature vector for each state (figure: `backgammon-features.png` — a small board and its features $\phi(s)$). Then define

- a linear value function: $V(s; \mathbf{w}) = \phi(s) \cdot \mathbf{w}$ (printed as `φ(s) * w`),
- ...or an MLP value function: $V(s; \mathbf{w}) = \text{MLP}_{\mathbf{w}}(\phi(s))$.

Then just apply TD learning!

---

## Section: Historical examples (`historical_examples`)

### Checkers (figure: `checkers.jpg`)

Arthur Samuel's checkers program [1959]:

- Learned by playing itself repeatedly (self-play).
- Smart features, linear evaluation function, use intermediate rewards.
- Used alpha-beta pruning + search heuristics.
- Reach human amateur level of play.
- IBM 701: 9K of memory!

### Backgammon (figure: `backgammon1.jpg`)

Gerald Tesauro's TD-Gammon [1992]:

- Learned weights by playing itself repeatedly (1 million times).
- Dumb features, neural network, no intermediate rewards.
- Reached human expert level of play, provided new insights into opening.

### Go (figure: `go.jpg`)

AlphaGo Zero [2017]:

- Learned by self-play (4.9 million games).
- Dumb features (stone positions), neural network, no intermediate rewards, Monte Carlo Tree Search.
- Beat AlphaGo, which beat Le Sedol in 2016. (Spelled "Le Sedol" in the source.)
- Provided new insights into the game.

**Next:** turn-based → simultaneous games; zero-sum → non-zero-sum.

---

# Part 2 — `simultaneous_games.py`

## Section: Introduction (`main`)

So far: turn-based games. Now: **simultaneous games**. Link: [Autumn 2023 lecture](https://stanford-cs221.github.io/autumn2023/modules/module.html#include=games/simultaneous-games.js&mode=print6pp).

Example: rock-paper-scissors (figure: `rock-paper-scissors.jpg`).

**Poll: can you still play optimally if you reveal your strategy?**

In turn-based games, we could compute minimax policies using a game tree (figure: `minimax.png`, reused from lecture 10). But in simultaneous games, two players have to move at the same time, ...so the game tree breaks down!

Order of the half: `zero_sum_games` (definitions, then the minimax theorem) → `non_zero_sum_games` (definitions, then Nash equilibria) → summary.

---

## Section: Zero-sum games — definitions (`zero_sum_games_definitions`, `evaluate_game`)

We will focus on simultaneous zero-sum games. Let's start with an example game: **two-finger Morra** (figure: `two-finger-morra.png`). Players A and B each show 1 or 2 fingers. If both show 1, B gives A 2 dollars. If both show 2, B gives A 4 dollars. Otherwise, A gives B 3 dollars.

Intuition: A wants the same number of fingers as B, leaning towards 4.

**Definition: single-move simultaneous game.**

- Two players: A and B.
- Payoff matrix: $V(a, b)$ for each action pair $(a, b)$.
- Zero-sum: A's utility is $V(a, b)$, B's utility is $-V(a, b)$.
- Note that there is no state, only one action.

Payoff matrix (figure: `morra-payoff.png`; in code `V = {1: {1: 2, 2: -3}, 2: {1: -3, 2: 4}}`):

| A \ B | 1 finger | 2 fingers |
| ----- | -------- | --------- |
| 1 finger | 2 | −3 |
| 2 fingers | −3 | 4 |

**Definition: strategy (policy).**

- A **pure strategy** (deterministic policy) is just a single action $a$.
- A **mixed strategy** (stochastic policy) is a probability distribution over actions $\pi(a)$.

Example policies in two-finger Morra: `always_one` $= [1, 0]$, `always_two` $= [0, 1]$, `uniform` $= [0.5, 0.5]$.

**Game evaluation:**

$$V(\pi_A, \pi_B) = \sum_a \sum_b \pi_A(a) \pi_B(b) V(a, b)$$

```python
def evaluate_game(V, policy_a, policy_b):
    """Computed expected utility of the game."""
    value = 0
    for a, prob_a in policy_a.items():
        for b, prob_b in policy_b.items():
            value += prob_a * prob_b * V[a][b]
    return value
```

Traced values:

| $\pi_A$ | $\pi_B$ | $V(\pi_A, \pi_B)$ |
| ------- | ------- | ----------------- |
| uniform | uniform | 0.0 |
| always_one | uniform | −0.5 |
| always_two | uniform | 0.5 |
| uniform | always_one | −0.5 |
| uniform | always_two | 0.5 |

---

## Section: Zero-sum games — the minimax theorem (`minimax_theorem`)

How do you come up with the optimal strategy? Think of a strategy and try it with a partner!

Game value: $V(\pi_A, \pi_B)$. Player A wants to maximize... Player B wants to minimize... **simultaneously**! (figure: `deadlock-traffic.png` — four cars jammed in an intersection.)

Let's break the jam and just let someone go first.

### Pure strategies

First, consider pure strategies (actions). Figure `pure-a-b.png` shows two game trees:

- **Player A goes first:** each of A's actions leads to B choosing the minimum. A = 1: $\min(2, -3) = -3$; A = 2: $\min(-3, 4) = -3$. Value **−3**.
- **Player B goes first:** each of B's actions leads to A choosing the maximum. B = 1: $\max(2, -3) = 2$; B = 2: $\max(-3, 4) = 4$. B picks 1. Value **2**.

Figure `second-is-no-worse.png` — **Proposition: going second is no worse:**

$$\max_a \min_b V(a, b) \le \min_b \max_a V(a, b)$$

### Mixed strategies

Now, let's consider mixed strategies. Suppose player A plays the mixed strategy $\pi_A = [0.5, 0.5]$. Let's compute the optimal strategy for player B:

$$V(\pi_A, \pi_B) = \pi_A(1) \pi_B(1) V(1, 1) + \pi_A(1) \pi_B(2) V(1, 2) + \pi_A(2) \pi_B(1) V(2, 1) + \pi_A(2) \pi_B(2) V(2, 2)$$

$$V(\pi_A, \pi_B) = 0.5 \pi_B(1) (2 - 3) + 0.5 \pi_B(2) (4 - 3)$$

$$V(\pi_A, \pi_B) = -0.5 \pi_B(1) + 0.5 \pi_B(2)$$

Optimal $\pi_B$: $\pi_B(1) = 1$, $\pi_B(2) = 0$.

In general, the second player can always play a pure strategy (figure: `second-pure.png` — **Proposition: second player can play pure strategy.** For any fixed mixed strategy $\pi_A$, $\min_{\pi_B} V(\pi_A, \pi_B)$ can be attained by a pure strategy.)

**In general, player A plays $\pi_A = [p, 1 - p]$** (figure: `mixed-a-b.png`). B's two pure responses give

- B shows 1: $p \cdot (2) + (1 - p) \cdot (-3) = 5p - 3$,
- B shows 2: $p \cdot (-3) + (1 - p) \cdot (4) = -7p + 4$.

Minimax value of the game (figure: `mixed-a-b-value.png`):

$$\max_{0 \le p \le 1} \min \lbrace 5p - 3, -7p + 4 \rbrace = -\frac{1}{12} \quad \left(\text{with } p = \frac{7}{12}\right)$$

**Now let player B go first with a mixed strategy $\pi_B = [p, 1 - p]$** (figure: `mixed-b-a.png`). A's two pure responses give the same two lines, $5p - 3$ (A shows 1) and $-7p + 4$ (A shows 2), because the payoff matrix is symmetric. Minimax value of the game (figure: `mixed-b-a-value.png`):

$$\min_{p \in [0, 1]} \max \lbrace 5p - 3, -7p + 4 \rbrace = -\frac{1}{12} \quad \left(\text{with } p = \frac{7}{12}\right)$$

**It's not a coincidence...** Figure `minimax-theorem.png` — **Theorem: minimax theorem [von Neumann, 1928].** For every simultaneous two-player zero-sum game with a finite number of actions:

$$\max_{\pi_A} \min_{\pi_B} V(\pi_A, \pi_B) = \min_{\pi_B} \max_{\pi_A} V(\pi_A, \pi_B),$$

where $\pi_A, \pi_B$ range over **mixed strategies**.

- Proof: linear programming duality.
- Algorithm: compute optimal mixed strategies via linear programming.

**Upshot: revealing your optimal mixed strategy doesn't hurt you!**

For two-finger Morra, the optimal mixed strategy for player A is $[7/12, 5/12]$, and the optimal mixed strategy for player B is $[7/12, 5/12]$. Traced: `evaluate_game(V, pi_opt, pi_opt)` = **−0.08333333333333326** (that is, $-1/12$ up to floating-point rounding).

From minimax principles:

- If your opponent changes their strategy, you can only improve!
- If you change your strategy, you can only get worse!

---

## Section: Non-zero-sum games — definitions (`non_zero_sum_games_definitions`)

So far: zero-sum games (utility of player A + utility of player B = 0). Now: **non-zero-sum games** (utility of players are arbitrary). Link: [Autumn 2023 lecture](https://stanford-cs221.github.io/autumn2023/modules/module.html#include=games%2Fnon-zero-sum-games.js&mode=print6pp).

Relationship between utilities:

- Competitive games: minimax, via linear programming or search.
- Cooperative games (utility of all players is same): pure maximization, via search.
- Real life: somewhere in between.

**Example: Prisoner's dilemma** (figure: `prisoners-dilemma.png`). Prosecutor asks A and B individually if each will testify against the other. If both testify, then both are sentenced to 5 years in jail. If both refuse, then both are sentenced to 1 year in jail. If only one testifies, then he/she gets out for free; the other gets a 10-year sentence.

**Definition: payoff matrix.** $V_p(\pi_A, \pi_B)$: utility for player $p \in \lbrace A, B \rbrace$ when $\pi_A$ and $\pi_B$ are the strategies.

Utility: −(number of years in jail). Figure `payoff-prisoners.png`:

| A \ B | testify | refuse |
| ----- | ------- | ------ |
| testify | A: −5, B: −5 | A: 0, B: −10 |
| refuse | A: −10, B: 0 | A: −1, B: −1 |

---

## Section: Nash equilibria (`nash_equilibria`)

Can't apply von Neumann's minimax theorem (not zero-sum). But we can get something weaker: **Nash equilibrium**.

Figure `nash-equilibrium.png` — **Definition: Nash equilibrium.** A Nash equilibrium is $(\pi_A^\ast, \pi_B^\ast)$ such that no player has an incentive to change his/her strategy:

$$V_A(\pi_A^\ast, \pi_B^\ast) \ge V_A(\pi_A, \pi_B^\ast) \quad \text{for all } \pi_A$$

$$V_B(\pi_A^\ast, \pi_B^\ast) \ge V_B(\pi_A^\ast, \pi_B) \quad \text{for all } \pi_B$$

**Theorem (Nash's existence theorem [1950]):** consider any finite-player game with finite number of actions. There exists **at least one** Nash equilibrium.

**Examples of Nash equilibria:**

1. Two-finger Morra (figure: `payoff-morra.png`, the zero-sum matrix written with both players' utilities: (1,1) A: 2, B: −2; (1,2) A: −3, B: 3; (2,1) A: −3, B: 3; (2,2) A: 4, B: −4). Nash equilibrium (also the minimax strategy): $[7/12, 5/12]$.
2. Collaborative two-finger Morra (figure: `payoff-collaborative-morra.png`, both players receive the same payoff: (1,1) 2, 2; (1,2) −3, −3; (2,1) −3, −3; (2,2) 4, 4). Nash equilibrium: both players play 1 or both players play 2.
3. Prisoner's dilemma (figure: `payoff-prisoners.png`). Nash equilibrium: both players testify.

**Simultaneous zero-sum games:** von Neumann's minimax theorem; multiple minimax strategies, single game value.
**Simultaneous non-zero-sum games:** Nash's existence theorem; multiple Nash equilibria, multiple game values.

**Summary:** Nash equilibria are stable (no notion of optimality). Nash equilibria exist for non-zero-sum games.

---

## Section: Summary (`main`)

- Simultaneous games: two players move at the same time.
- In general, second player has an advantage (and can play pure strategy).
- Minimax theorem: if we allow mixed strategies, then both players can play optimally.
- Nash equilibrium: exists for non-zero-sum games (but not unique).
- Huge literature in game theory / economics.

---

## Figures (vision pass)

Vision pass on 2026-10-22 over the images at commit cbedcb10, `images/`, done by the parent model directly (each image is small, about 1k tokens). Ground-truth cross-checks: the Morra payoff matrix in `morra-payoff.png`, `payoff-morra.png` and `pure-a-b.png` matches the code's `V = {1: {1: 2, 2: -3}, 2: {1: -3, 2: 4}}`; the five traced `evaluate_game` values and the traced optimum −0.0833 reconcile with it; the two lines $5p - 3$ and $-7p + 4$ in `mixed-a-b.png` follow from the matrix and intersect at $p = 7/12$ with value $35/12 - 36/12 = -1/12$, as `mixed-a-b-value.png` prints; the pure-strategy tree values (−3 when A goes first, 2 when B goes first) follow from the matrix. Four further images were added in the same commit but are never displayed by the lecture source (`td-pi-agent-opp.png`, `nash-morra.png`, `nash-prisoners.png`, `nash-collaborative-morra.png`) and are not described here.

### `minimax.png` (reused)

The minimax game tree for game 1 from lecture 10, root value $V_{\text{minmax}}(s_{\text{start}}) = 1$. Described in the Figures section of raw/slides/10-games-i.md.

### `eval-functions-chess.png` (reused)

The chess board in its initial position and the hand-written evaluation function $\text{Eval}(s) = \text{material} + \text{mobility} + \text{king-safety} + \text{center-control}$ from lecture 10. Described in the Figures section of raw/slides/10-games-i.md.

### `sarsa-algorithm.png`

A bordered box headed "Algorithm: SARSA" in blue, with a laptop icon. Text: "On each $(s, a, r, s', a')$:", then the update

$$\hat{Q}_\pi(s, a) \leftarrow (1 - \eta) \hat{Q}_\pi(s, a) + \eta \left[ r + \gamma \hat{Q}_\pi(s', a') \right]$$

The bracketed target is green. A brace under $r$ labels it **data**; a brace under $\gamma \hat{Q}_\pi(s', a')$ labels it **estimate**. Symbols: $\hat{Q}_\pi$ is the estimated Q-value of the current policy, $\eta$ the learning rate, $\gamma$ the discount, $a'$ the next action actually taken.

### `td-algorithm.png`

A bordered box headed "Algorithm: TD learning" in blue, with a laptop icon. Text: "On each $(s, a, r, s')$:", then

$$\mathbf{w} \leftarrow \mathbf{w} - \eta \left[ V(s; \mathbf{w}) - (r + \gamma V(s'; \mathbf{w})) \right] \nabla_{\mathbf{w}} V(s; \mathbf{w})$$

Colour coding: $V(s; \mathbf{w})$ inside the bracket is red with a red brace labelled **prediction**; $(r + \gamma V(s'; \mathbf{w}))$ is green with a green brace labelled **target**; the trailing gradient $\nabla_{\mathbf{w}} V(s; \mathbf{w})$ is blue and unlabelled. $\mathbf{w}$ is bold throughout. Unlike the SARSA box, the experience tuple has no $a'$.

### `backgammon1.jpg`

A small cartoon of a backgammon board in its starting position, tan background with alternating dark- and light-green triangular points, divided down the middle by a strip labelled "Bar" (with an arrow pointing at it). The quadrants are labelled "Outer Board" (top left and bottom left), "Red's Home Board" (top right) and "White's Home Board" (bottom right). Checkers are red and white circles: top-left quadrant, 5 white on the leftmost point and 3 red on the fifth point; top-right quadrant, 5 red on the leftmost point and 2 white on the rightmost; bottom-left quadrant, 5 red on the leftmost point and 3 white on the fifth point; bottom-right quadrant, 5 white on the leftmost point and 2 red on the rightmost. The image is about 287 × 241 px.

### `backgammon2.jpg`

The same board with points numbered 13–24 along the top (13–18 left of the bar, 19–24 right) and 12–1 along the bottom (12–7 left, 6–1 right). Yellow arrows trace example moves, each passing a small white die: along the top, a white checker's path runs leftward from the 24 point toward 21 and on toward 17–18, hooking down; along the bottom, two arrows run rightward from the white checkers at 8 and 6 toward the low points (around 3–5), hooking down. The pip counts on the dice are not legible at this resolution (about 287 × 216 px), so which moves the dice license cannot be read off the image; the figure's point is that the dice determine how far a checker moves.

### `backgammon-features.png`

Left, labelled "state $s$": a simplified backgammon board with four columns (points) numbered 2 and 3 along the top and 1 and 0 along the bottom, a vertical brown strip down the middle (the bar) and another along the right edge (where removed pieces go). Contents: column 0 (bottom right) holds one **o**; column 1 (bottom left) holds one **x**; column 2 (top left) is empty; column 3 (top right) holds three **x**; the bar holds one **o**; the right-hand strip holds two **o** (removed).

Right, headed "Features $\phi(s)$:" in blue, six indicator-style features with their values:

| Feature | Value |
| ------- | ----- |
| `[(# o in column 0) = 1]` | 1 |
| `[(# o on bar)]` | 1 |
| `[(fraction o removed)]` | 1/2 |
| `[(# x in column 1) = 1]` | 1 |
| `[(# x in column 3) = 3]` | 1 |
| `[(is it o's turn)]` | 1 |

The values are consistent with the board drawn: one o in column 0, one o on the bar, two of the four o pieces removed (1/2), one x in column 1, three x in column 3.

### `checkers.jpg`

A small (224 × 225 px) top-down image of a checkers board in its starting position: black and grey squares, 12 red pieces on the dark squares of the top three rows and 12 dark grey pieces on the bottom three rows.

### `go.jpg`

A photograph of a traditional wooden Go board (a thick, legged floor board in light yellow wood) standing on tatami matting, with a cushion behind it and a dark wooden stone bowl in the lower-left corner. The grid is densely covered with black and white stones, apparently in the late stage of a game. No text.

### `rock-paper-scissors.jpg`

A small photograph of three hands against a white background: one making "scissors" (two fingers extended) at lower left, one open flat hand ("paper") at upper right, one closed fist ("rock") at lower right.

### `two-finger-morra.png`

A bordered box headed "Example: two-finger Morra" in orange, with a polyhedron icon. Text, with A in red and B in blue: "Players A and B each show 1 or 2 fingers. If both show 1, B gives A 2 dollars. If both show 2, B gives A 4 dollars. Otherwise, A gives B 3 dollars."

### `morra-payoff.png`

A bordered box headed "Example: two-finger Morra payoff matrix" in orange. A 2 × 2 table with row label "A \ B": columns "1 finger", "2 fingers"; row "1 finger": 2, −3; row "2 fingers": −3, 4. Entries are A's utility (B's is the negative).

### `deadlock-traffic.png`

A black-and-white line drawing of a four-way intersection seen from above, four grey city blocks in the corners and dashed lane lines. Four cars sit in the middle, each pointing into the intersection from a different direction and blocking the next, so none can move: gridlock.

### `pure-a-b.png`

Two game trees side by side, upward triangles for the maximizer A and downward triangles for the minimizer B.

- **"Player A goes first:"** (A in red). Root: upward triangle, value **−3** in red; both edges to its children are red. Each child is a downward triangle with value **−3** in blue. Left child (A shows 1): leaves 2 (black edge) and −3 (blue edge). Right child (A shows 2): leaves −3 (blue edge) and 4 (black edge).
- **"Player B goes first:"** (B in blue). Root: downward triangle, value **2** in blue; its left edge is blue, its right edge black. Left child (B shows 1): upward triangle, value **2** in red, leaves 2 (red edge) and −3 (black edge). Right child (B shows 2): upward triangle, value **4** in red, leaves −3 (black edge) and 4 (red edge).

Coloured edges mark each node's chosen move; both of A's moves are red because they tie at −3.

### `second-is-no-worse.png`

A bordered box headed "Proposition: going second is no worse" in blue, with a purple sphere icon:

$$\max_a \min_b V(a, b) \le \min_b \max_a V(a, b)$$

$\max$ and its subscript $a$ are red; $\min$ and its subscript $b$ are blue.

### `second-pure.png`

A bordered box headed "Proposition: second player can play pure strategy" in blue, with a purple sphere icon: "For any fixed mixed strategy $\pi_A$: $\min_{\pi_B} V(\pi_A, \pi_B)$ can be attained by a pure strategy."

### `mixed-a-b.png`

A two-level tree: an upward triangle at the top, a dashed line through the red label $\pi = [p, 1 - p]$ (A's mixed strategy) down to a downward triangle (B), which branches into two edges labelled **1** and **2** in blue. Under edge 1: $p \cdot (2) + (1 - p) \cdot (-3) = 5p - 3$. Under edge 2: $p \cdot (-3) + (1 - p) \cdot (4) = -7p + 4$.

### `mixed-a-b-value.png`

One line:

$$\max_{0 \le p \le 1} \min \lbrace 5p - 3, -7p + 4 \rbrace = \boxed{-\frac{1}{12}} \quad \left(\text{with } p = \frac{7}{12}\right)$$

$\max$ and its range are red, $\min$ is blue, and the value $-1/12$ is boxed.

### `mixed-b-a.png`

The mirror of `mixed-a-b.png`: a downward triangle at the top, a dashed line through the blue label $\pi = [p, 1 - p]$ (B's mixed strategy) down to an upward triangle (A), branching into edges **1** and **2** in red. The expressions underneath are the same: $p \cdot (2) + (1 - p) \cdot (-3) = 5p - 3$ and $p \cdot (-3) + (1 - p) \cdot (4) = -7p + 4$.

### `mixed-b-a-value.png`

One line:

$$\min_{p \in [0, 1]} \max \lbrace 5p - 3, -7p + 4 \rbrace = \boxed{-\frac{1}{12}} \quad \left(\text{with } p = \frac{7}{12}\right)$$

$\min$ and its range are blue, $\max$ is red, the value boxed.

### `minimax-theorem.png`

A bordered box headed "Theorem: minimax theorem [von Neumann, 1928]" in blue, with a purple sphere icon. Text: "For every simultaneous two-player zero-sum game with a finite number of actions:"

$$\max_{\pi_A} \min_{\pi_B} V(\pi_A, \pi_B) = \min_{\pi_B} \max_{\pi_A} V(\pi_A, \pi_B),$$

"where $\pi_A, \pi_B$ range over **mixed strategies**." (bold as printed). $\max$ and $\pi_A$ subscripts red, $\min$ and $\pi_B$ subscripts blue.

### `prisoners-dilemma.png`

A bordered box headed "Example: Prisoner's dilemma" in orange, with a polyhedron icon. Text: "Prosecutor asks A and B individually if each will testify against the other. If both testify, then both are sentenced to 5 years in jail. If both refuse, then both are sentenced to 1 year in jail. If only one testifies, then he/she gets out for free; the other gets a 10-year sentence."

### `payoff-prisoners.png`

Titled "Payoff matrix for prisoner's dilemma", a grey-ruled 3 × 3 table: header row "A \ B", "testify", "refuse"; row "testify": "A: -5, B: -5", "A: 0, B: -10"; row "refuse": "A: -10, B: 0", "A: -1, B: -1".

### `nash-equilibrium.png`

A bordered box headed "Definition: Nash equilibrium" in green, with a green book icon. Text: "A **Nash equilibrium** is $(\pi_A^\ast, \pi_B^\ast)$ such that no player has an incentive to change his/her strategy:"

$$V_A(\pi_A^\ast, \pi_B^\ast) \ge V_A(\pi_A, \pi_B^\ast) \text{ for all } \pi_A$$

$$V_B(\pi_A^\ast, \pi_B^\ast) \ge V_B(\pi_A^\ast, \pi_B) \text{ for all } \pi_B$$

The deviating strategy on each right-hand side ($\pi_A$ in the first line, $\pi_B$ in the second) and the "for all" variable are coloured, red for A and blue for B.

### `payoff-morra.png`

Titled "Payoff matrix for two-finger Morra", a grey-ruled 3 × 3 table: header "A \ B", "1", "2"; row "1": "A: 2, B: -2", "A: -3, B: 3"; row "2": "A: -3, B: 3", "A: 4, B: -4". The same game as `morra-payoff.png`, written with both players' utilities.

### `payoff-collaborative-morra.png`

Titled "Payoff matrix for collaborative two-finger Morra", the same layout: row "1": "A: 2, B: 2", "A: -3, B: -3"; row "2": "A: -3, B: -3", "A: 4, B: 4". Both players receive A's Morra payoff.
