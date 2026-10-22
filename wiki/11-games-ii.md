---
title: Lecture 11 — Games II
---

# Lecture 11 — Games II

Lecture 11 closes the games unit in two halves. The first half asks whether the hand-written [evaluation functions](evaluation-functions.md) of lecture 10 can be **learned**, and answers with [TD learning](td-learning.md): estimate the value of each state from experience by bootstrapping, then let agent and opponent share that one value function, the agent maximizing and the opponent minimizing (**self-play**). The second half drops two assumptions every earlier game made. In [simultaneous games](simultaneous-games.md) both players move at once, so the game tree breaks down; von Neumann's **minimax theorem** shows that once players may randomize, it no longer matters who goes first, and revealing an optimal mixed strategy costs nothing. In **non-zero-sum** games the analogue is the [Nash equilibrium](nash-equilibrium.md), a stable pair of strategies that always exists but need not be unique or optimal ([≈0:05](../raw/transcripts/11-games-ii.md), [≈1:12:32](../raw/transcripts/11-games-ii.md)).

## Recap: Minimax and Its Two Speedups

The lecture opens on lecture 10's game tree: at the root the agent maximizes, at the next level the opponent minimizes, and the [minimax](minimax.md) value of game 1 is 1 ([≈0:05](../raw/transcripts/11-games-ii.md), [≈0:52](../raw/transcripts/11-games-ii.md)). Minimax can be sped up exactly with [alpha-beta pruning](alpha-beta-pruning.md), which tracks lower and upper bounds on each state and skips a subtree once the bounds cannot overlap: in the recap tree the root is known to be at least 1, the third subtree at most −5, so its 15 is never looked at ([≈1:38](../raw/transcripts/11-games-ii.md)). Or it can be sped up approximately with an [evaluation function](evaluation-functions.md) that brings in domain knowledge, such as the chess function that combines material, mobility and control of the center ([≈2:25](../raw/transcripts/11-games-ii.md)).

That chess function was "very much about manual heuristics." The question for today is whether the evaluation function can be learned instead, since a learned one might do better than a hand-coded system; the tool is reinforcement learning, specifically TD learning ([≈2:25](../raw/transcripts/11-games-ii.md)).

## From Q-values to State Values

Value-based reinforcement learning works with two quantities, both from [policy evaluation](policy-evaluation.md) and [Q-learning](q-learning.md) ([≈3:11](../raw/transcripts/11-games-ii.md), [≈3:59](../raw/transcripts/11-games-ii.md)). $V_\pi(s)$ is the value (expected utility) of following policy $\pi$ from state $s$; $Q_\pi(s, a)$ is the value of first taking action $a$ in state $s$ and then following $\pi$.

**SARSA** estimates $Q_\pi$ for the policy currently being followed (on-policy), and its key idea is **bootstrapping**: the target is not the empirical utility of a full rollout but an immediate reward plus an estimated future reward ([≈3:59](../raw/transcripts/11-games-ii.md), [≈4:47](../raw/transcripts/11-games-ii.md)). On each experience $(s, a, r, s', a')$ it nudges the estimate toward that target at learning rate $\eta$ ([≈5:33](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)):

$$\hat{Q}_ \pi(s, a) \leftarrow (1 - \eta) \hat{Q}_ \pi(s, a) + \eta \left[ r + \gamma \hat{Q}_ \pi(s', a') \right]$$

where $r$ is the reward just received ("data") and $\gamma \hat{Q}_ \pi(s', a')$, the discounted estimate for the next state and action, is the "estimate."

The lecturer stresses the link between Q-values and policies ([≈6:20](../raw/transcripts/11-games-ii.md), [≈7:05](../raw/transcripts/11-games-ii.md)). Given $Q_\pi$ you can read out a new policy by taking the best action,

$$\pi_{new}(s) = \arg\max_a Q_\pi(s, a),$$

which is one step of **policy improvement**: optimize myopically over the next action, then follow $\pi$ afterwards.

Could you get a policy from $V_\pi$ alone? Policy evaluation in MDPs says $V_\pi(s) = Q_\pi(s, \pi(s))$ and

$$Q_\pi(s, a) = \sum_{s'} T(s, a, s') \left( R(s, a, s') + \gamma V_\pi(s') \right),$$

where $T(s, a, s')$ is the transition probability, $R(s, a, s')$ the reward and $\gamma$ the discount ([≈7:53](../raw/transcripts/11-games-ii.md), [≈8:39](../raw/transcripts/11-games-ii.md)). So yes: compute Q from V with this formula and take the arg max ([≈9:25](../raw/transcripts/11-games-ii.md)). The catch is that this needs the MDP. In general reinforcement learning you do not know the transitions, and being told "this state is great, this state is bad" does not tell you which action gets you there; a Q-value "binds the action into the value." That is why the RL lectures used only SARSA and Q-learning ([≈10:14](../raw/transcripts/11-games-ii.md), [≈11:01](../raw/transcripts/11-games-ii.md)).

**In games we do know $T$ and $R$**, unless it is "a cruel game where you don't know the rules," and in chess or Go everything is deterministic and fully observed ([≈11:01](../raw/transcripts/11-games-ii.md)). With a deterministic successor function the policy reduces to trying every action and scoring where it lands ([≈11:47](../raw/transcripts/11-games-ii.md)):

$$\pi_{new}(s) = \arg\max_a V_\pi(\text{Succ}(s, a))$$

A student asked how the general formula simplifies to this. The answer: in a game the transition probability is 1 for the single successor $\text{Succ}(s, a)$, so the sum disappears; all the reward comes at the end, so $R$ is zero on essentially every transition; and the discount is 1 ([≈13:18](../raw/transcripts/11-games-ii.md), [≈14:04](../raw/transcripts/11-games-ii.md)).

**So why use reinforcement learning at all, if the MDP is known?** In principle you could run [value iteration](value-iteration.md), since there is nothing to learn about the rules. But outside toy games like tic-tac-toe the number of states is exponential, and value iteration's cost grows with the number of states. RL is used here "not because we don't know the MDP" but because the state space is too big, and RL lets you use function approximation to effectively reduce the number of states ([≈11:47](../raw/transcripts/11-games-ii.md), [≈12:33](../raw/transcripts/11-games-ii.md), [≈13:18](../raw/transcripts/11-games-ii.md)).

## TD Learning

The one line to remember: **TD learning is to $V_\pi$ as SARSA is to $Q_\pi$** ([≈14:04](../raw/transcripts/11-games-ii.md)). SARSA's Q-values say how good each action is from a state; TD learning's $V_\pi$ says only how good each state is, and since the action-to-next-state mapping is known, that is all you need. A function of the state alone instead of state and action is "a minor point," but it does make some things easier ([≈14:50](../raw/transcripts/11-games-ii.md)).

TD learning assumes **function approximation**: instead of a table with an independent value for every state, $V_\pi(s) = V(s; \mathbf{w})$ is a parameterized function of the state with weights $\mathbf{w}$. In deep reinforcement learning this function is called a **value network** ([≈14:50](../raw/transcripts/11-games-ii.md), [≈15:36](../raw/transcripts/11-games-ii.md)). The algorithm in a nutshell ([≈15:36](../raw/transcripts/11-games-ii.md)–[≈17:54](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)):

1. Get a piece of experience $(s, a, r, s')$: in state $s$ you took action $a$, got reward $r$ and ended up in $s'$.
2. The model predicts $V(s; \mathbf{w})$.
3. The **target** bootstraps: $r + \gamma V(s'; \mathbf{w})$, the immediate reward plus the discounted value of the successor under the same weights. There is no $a'$, because this is a value function, not a Q-function ([≈17:07](../raw/transcripts/11-games-ii.md)).
4. Define the squared loss $L(\mathbf{w}) = \left( V(s; \mathbf{w}) - (r + \gamma V(s'; \mathbf{w})) \right)^2$ and take a gradient step against it.

Differentiating gives the natural form "difference times the gradient of what's inside" ([≈17:54](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)):

$$\mathbf{w} \leftarrow \mathbf{w} - \eta \left[ \underbrace{V(s; \mathbf{w})}_ {\text{prediction}} - \underbrace{(r + \gamma V(s'; \mathbf{w}))}_ {\text{target}} \right] \nabla_{\mathbf{w}} V(s; \mathbf{w})$$

One important point: the target also depends on $\mathbf{w}$, but it is **treated as a constant**. You only backpropagate through the prediction, which is where you would call *detach* in the computation-graph framework from lecture 2: "your model prediction, move it towards the target. The target is fixed" ([≈17:54](../raw/transcripts/11-games-ii.md), [≈18:40](../raw/transcripts/11-games-ii.md), [≈19:25](../raw/transcripts/11-games-ii.md)). (The printed bullet list writes the step size as $\alpha$; the boxed algorithm writes $\eta$.)

### Demo: TD learning on the flaky tram

The code runs TD learning on the flaky tram MDP from [lecture 7](07-markov-decision-processes.md), with 6 locations, tram failure probability 0.1, an exploration policy that either walks or takes the tram, $\epsilon = 0.2$, discount 1 and learning rate 0.1 ([≈19:25](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)). The MDP is passed in; evaluating a policy would not need it, but turning values into a policy does ([≈19:25](../raw/transcripts/11-games-ii.md), [≈20:11](../raw/transcripts/11-games-ii.md)). The agent is epsilon-greedy: with probability $\epsilon$ it explores, otherwise it plays `pi`, which computes a Q-value for each action by summing over successors, weighting by transition probability, and adding immediate reward to the discounted $V$ of where it lands. "If I take V, I combine it with my knowledge of how the world works, the transitions, and then I'm going to get a Q value" ([≈20:56](../raw/transcripts/11-games-ii.md)). With all values still 0, state 1 gives walk −1 and tram −2, so the policy chooses **walk** ([≈21:42](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)).

On feedback, the update compares the predicted value of the state with the target (reward plus discounted value of the next state). For simplicity the demo keeps a table rather than a function approximator, and in the tabular case the update has a simple reading: move the value toward the target, away from the prediction, at the learning rate. Predicted 0, target −1, so $V(1)$ moves to **−0.1** ([≈22:30](../raw/transcripts/11-games-ii.md)). "So then I do this over and over again and that is essentially TD learning."

To summarize ([≈23:16](../raw/transcripts/11-games-ii.md), [≈24:01](../raw/transcripts/11-games-ii.md)): TD learning estimates $V_\pi$ from experience; it is **on-policy**, since rollouts follow $\pi$ and no other actions are considered; and it **bootstraps**, since the target contains the model's own estimate. The lecturer's rule of thumb: "whenever you see a max over actions, that usually means something like off-policy or Q-learning. If you don't see that, it's probably on-policy."

## TD Learning for Games: Self-Play

The derivation so far works for any MDP ([≈24:01](../raw/transcripts/11-games-ii.md)). Games are special in three ways: transitions are deterministic (in the games seen so far), there is no utility until the end of the game, and there are two players. The first two are special cases of an MDP; the two players are what is genuinely new ([≈24:47](../raw/transcripts/11-games-ii.md)).

The fix is "simple and elegant": let agent and opponent use **the same value function**. This is **self-play** ([≈24:47](../raw/transcripts/11-games-ii.md), [≈25:33](../raw/transcripts/11-games-ii.md)). They do not do the same thing with it, though: the agent picks the move leading to the best state and the opponent replaces the arg max with an arg min ([≈25:33](../raw/transcripts/11-games-ii.md), [≈26:22](../raw/transcripts/11-games-ii.md)):

$$\pi_{agent}(s) = \arg\max_a V_\pi(\text{Succ}(s, a)) \qquad \pi_{opp}(s) = \arg\min_a V_\pi(\text{Succ}(s, a))$$

"If you know what's good and what's bad, you can choose to do the good thing or you can do the bad thing" ([≈26:22](../raw/transcripts/11-games-ii.md)).

### Backgammon

The worked example is backgammon ([≈26:22](../raw/transcripts/11-games-ii.md)–[≈28:47](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)). The board is made of triangles called points; the red player moves pieces clockwise to their home board and off, the white player counterclockwise. Two dice set how far you move: rolling a 3 and a 5, you can move one piece 3 and then 5, or one piece 5 and another 3. Landing on a point with a single opponent piece sends that piece to the **bar**, where it has to start over; you cannot land on a point with more than one opponent piece. So the strategy is to keep pieces in tall stacks, safe from the opponent, while still moving everyone across. The lecturer adds that he is not sure whether there is another rule, "but that's basically the gist" ([≈28:47](../raw/transcripts/11-games-ii.md)).

Backgammon has randomness, which the framework absorbs by treating the dice as a player with a fixed policy $\pi_{dice}$ ([≈29:33](../raw/transcripts/11-games-ii.md)). Turns run

$$\pi_{dice} \to \pi_{agent} \to \pi_{dice} \to \pi_{opp} \to \pi_{dice} \to \pi_{agent} \to \dots$$

with the dice before each move because the move depends on the roll ([≈30:19](../raw/transcripts/11-games-ii.md)). Self-play learns $\pi_{agent}$ and $\pi_{opp}$ together while $\pi_{dice}$ stays fixed. "If you were in a cruel world then you could also play against adversarial dice, but I imagine that would be a much harder game to win."

To apply TD learning you must say how $V$ is parameterized, which starts by turning the game state into a vector ([≈31:07](../raw/transcripts/11-games-ii.md)). On the lecture's simplified board, the features $\phi(s)$ include whether the number of o's in column 0 equals 1 (yes: 1), the number of o's on the bar (1), the fraction of o's removed (two of four: 1/2), whether the number of x's in column 1 equals 1 (1), whether the number of x's in column 3 equals 3 (1), and whether it is o's turn (1) ([≈31:07](../raw/transcripts/11-games-ii.md), [≈31:53](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)). Then $V$ can be linear, $V(s; \mathbf{w}) = \phi(s) \cdot \mathbf{w}$, which is "basically linear regression," or a multi-layer perceptron $V(s; \mathbf{w}) = \text{MLP}_ {\mathbf{w}}(\phi(s))$, and you just apply TD learning ([≈31:53](../raw/transcripts/11-games-ii.md), [≈32:39](../raw/transcripts/11-games-ii.md)).

### Three Historical Programs

The lecture traces learning in game playing through three programs ([≈32:39](../raw/transcripts/11-games-ii.md)–[≈37:18](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)); the trend is that as data and compute grow, you engineer features less.

- **Checkers — Arthur Samuel, 1959.** Learned by playing itself repeatedly, "perhaps one of the earliest examples of self-play in action." With limited compute it relied on smart features, a linear evaluation function and added **intermediate rewards**, because win/lose alone is a hard delayed-reward signal to learn from. It also used alpha-beta pruning with domain-specific heuristics, reached amateur level ("it could probably beat me"), and ran on an IBM 701 with 9K of memory ([≈33:27](../raw/transcripts/11-games-ii.md), [≈34:12](../raw/transcripts/11-games-ii.md)). In speech the lecturer calls it "a chess program" at [≈32:39](../raw/transcripts/11-games-ii.md); the slide and the rest of his description say checkers.
- **Backgammon — Gerald Tesauro's TD-Gammon, 1992.** Self-play a million times, fairly simple features, a neural network and no intermediate rewards. It reached human expert level and is one of the earliest examples of a program giving new insight into a game, in this case into how openings should be played ([≈34:58](../raw/transcripts/11-games-ii.md), [≈35:46](../raw/transcripts/11-games-ii.md)).
- **Go — AlphaGo Zero, 2017.** Highlighted over the 2016 AlphaGo because it is "much more bullish on reinforcement learning." Self-play over 4.9 million games (the lecturer says "about 5 million"), "dumb" features (just stone positions, where earlier Go programs encoded liveness and connectivity by hand), a deep network, no intermediate rewards, and Monte Carlo tree search, which the lecture does not cover beyond calling it a less greedy way to get a policy. It beat the 2016 AlphaGo, which had beaten Lee Sedol, and it too provided new insights into the game ([≈35:46](../raw/transcripts/11-games-ii.md)–[≈37:18](../raw/transcripts/11-games-ii.md)).

See [TD learning](td-learning.md) for the topic page.

## Simultaneous Games

The second half generalizes in two directions: from turn-based to **simultaneous** games, and beyond the zero-sum assumption ([≈37:18](../raw/transcripts/11-games-ii.md), [≈38:04](../raw/transcripts/11-games-ii.md)). In a simultaneous game, such as rock-paper-scissors, both players move at the same time. The opening poll asks: **can you still play optimally if you reveal your strategy?** Announcing "I'm going to play rock" is clearly not smart; the answer turns out to depend on what kinds of strategies you are allowed ([≈38:04](../raw/transcripts/11-games-ii.md), [≈38:53](../raw/transcripts/11-games-ii.md)).

A game tree handles turn-taking because exactly one player moves at each node. With two players moving at once, "are you maxing? are you mining?" and the tree no longer works ([≈38:53](../raw/transcripts/11-games-ii.md), [≈39:40](../raw/transcripts/11-games-ii.md)). The lecture takes zero-sum simultaneous games first, then non-zero-sum. Full treatment on the [simultaneous games](simultaneous-games.md) page.

### Two-Finger Morra

The example game ([≈39:40](../raw/transcripts/11-games-ii.md), [≈40:27](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)): players A and B each show 1 or 2 fingers. If both show 1, B gives A \$2; if both show 2, B gives A \$4; otherwise A gives B \$3. A wants the fingers to match, and leans toward 2 for the bigger prize, but if A always plays 2 then B plays 1 and A loses money: "there's sort of this balancing act."

Formally this is a **single-move simultaneous game** ([≈41:14](../raw/transcripts/11-games-ii.md), [≈42:01](../raw/transcripts/11-games-ii.md)): two players, no state, one action each, and a **payoff matrix** $V(a, b)$ giving A's utility for each action pair; since the game is zero-sum, B's utility is $-V(a, b)$. For Morra:

| A \ B | 1 finger | 2 fingers |
| --- | --- | --- |
| 1 finger | 2 | −3 |
| 2 fingers | −3 | 4 |

The game-theory word for a policy is a **strategy** ([≈42:48](../raw/transcripts/11-games-ii.md), [≈43:37](../raw/transcripts/11-games-ii.md)). A **pure strategy** is a deterministic policy, a single action; a **mixed strategy** is a stochastic policy, a probability distribution $\pi(a)$ over actions, written as a vector: always one is $\pi = [1, 0]$, always two is $[0, 1]$, uniform is $[0.5, 0.5]$. The value of the game under mixed strategies $\pi_A$, $\pi_B$ weights each payoff by the independent probabilities of the two actions ([≈44:23](../raw/transcripts/11-games-ii.md), [≈45:09](../raw/transcripts/11-games-ii.md)):

$$V(\pi_A, \pi_B) = \sum_a \sum_b \pi_A(a) \pi_B(b) V(a, b)$$

The code evaluates five pairings ([≈45:57](../raw/transcripts/11-games-ii.md), [≈46:45](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)): both uniform gives 0 (the average of all four entries); A always one against uniform B gives −0.5; A always two against uniform B gives 0.5 (the average of −3 and 4); uniform A against B always one gives −0.5; uniform A against B always two gives 0.5. Against a generic uniform opponent, A "can kind of get away with" playing two ([≈47:33](../raw/transcripts/11-games-ii.md)).

At [≈48:20](../raw/transcripts/11-games-ii.md) the class pairs up to play a few rounds. The lecturer notes that repeated play is really a different game: the history becomes state, which changes the dynamics, whereas Morra here is a single move ([≈48:20](../raw/transcripts/11-games-ii.md), [≈49:05](../raw/transcripts/11-games-ii.md)).

### Let Someone Go First

A wants to maximize $V(\pi_A, \pi_B)$ and B to minimize it, simultaneously, which is a jam; to break it, let one player go first ([≈49:05](../raw/transcripts/11-games-ii.md)). With **pure strategies** this is ordinary minimax ([≈49:51](../raw/transcripts/11-games-ii.md), [≈50:39](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)). If A goes first, B plays the opposite number and A gets **−3** whatever A does. If B goes first, A matches B's number, so B showing 1 gives 2 and B showing 2 gives 4; B picks 1, and the value is **2**.

That illustrates a general fact: **going second is no worse**, and often better ([≈50:39](../raw/transcripts/11-games-ii.md), [≈51:26](../raw/transcripts/11-games-ii.md)):

$$\max_a \min_b V(a, b) \le \min_b \max_a V(a, b)$$

Here $-3 \le 2$. Intuitively, the second player sees the first player's move and can react, and with no state the first mover cannot take any options away, so going second only adds information.

### Mixed Strategies

Now suppose A announces a mixed strategy: "I'm going to flip a coin; heads I play 1, tails I play 2," without yet flipping ([≈52:13](../raw/transcripts/11-games-ii.md)). B's best response follows from expanding $V(\pi_A, \pi_B)$ into its four terms ([≈52:58](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)):

$$V(\pi_A, \pi_B) = 0.5 \pi_B(1) (2 - 3) + 0.5 \pi_B(2) (4 - 3) = -0.5 \pi_B(1) + 0.5 \pi_B(2)$$

Showing 1 gets B an average of −1/2, showing 2 gets 1/2, and B is minimizing, so B should always show **1**: $\pi_B = [1, 0]$. In the lecture the lecturer first says B "should always play two ... because half is greater than minus half," and after a student's correction concludes that B, who wants to minimize, prefers 1 ([≈53:45](../raw/transcripts/11-games-ii.md), [≈54:34](../raw/transcripts/11-games-ii.md), [≈55:21](../raw/transcripts/11-games-ii.md)). The slide states $\pi_B(1) = 1$, $\pi_B(2) = 0$.

The reasoning generalizes into a second fact: **the second player can always play a pure strategy**. Whatever mixed strategy the first player fixes, the second player computes the weighted average for each of their actions and puts "all my eggs in that one basket" ([≈54:34](../raw/transcripts/11-games-ii.md), [≈55:21](../raw/transcripts/11-games-ii.md)). Formally, for any fixed $\pi_A$, $\min_{\pi_B} V(\pi_A, \pi_B)$ is attained by a pure strategy.

So run minimax where A's "actions" are mixed strategies $\pi_A = [p, 1 - p]$, an uncountably infinite set, and B, moving second, only needs its two pure responses ([≈56:10](../raw/transcripts/11-games-ii.md), [≈56:59](../raw/transcripts/11-games-ii.md), [≈57:44](../raw/transcripts/11-games-ii.md)):

- B shows 1: $p \cdot (2) + (1 - p) \cdot (-3) = 5p - 3$
- B shows 2: $p \cdot (-3) + (1 - p) \cdot (4) = -7p + 4$

B takes the smaller of these and A chooses $p$ to make it as large as possible (the lecturer first says "max over these two" and corrects himself to min at [≈58:35](../raw/transcripts/11-games-ii.md)):

$$\max_{0 \le p \le 1} \min \lbrace 5p - 3, -7p + 4 \rbrace = -\frac{1}{12} \quad \text{at } p = \frac{7}{12}$$

The minimum of two lines is maximized where they cross. On the board the lecturer sketches the rising line $5p - 3$ and the falling line $-7p + 4$, erases the upper pieces, and sets the two equal: $5p - 3 = -7p + 4$ gives $p = 7/12$, and plugging back in gives $-1/12$ ([≈59:22](../raw/transcripts/11-games-ii.md), [≈1:00:08](../raw/transcripts/11-games-ii.md), [≈1:00:54](../raw/transcripts/11-games-ii.md)). The value slightly favors B, who went second.

To be fair, let B go first with $\pi_B = [p, 1 - p]$ and do the same calculation. "Something remarkable happens": the minimax value is again exactly $-1/12$, with probability $7/12$ ([≈1:01:39](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)).

### Von Neumann's Minimax Theorem

"You might think I rigged all these numbers," but it is not a coincidence ([≈1:01:39](../raw/transcripts/11-games-ii.md), [≈1:02:24](../raw/transcripts/11-games-ii.md)). The **minimax theorem** (John von Neumann, 1928): for every simultaneous two-player zero-sum game with a finite number of actions,

$$\max_{\pi_A} \min_{\pi_B} V(\pi_A, \pi_B) = \min_{\pi_B} \max_{\pi_A} V(\pi_A, \pi_B),$$

where $\pi_A$ and $\pi_B$ range over **mixed** strategies. Going first and going second give the same value. The mixed part is essential: with pure strategies the equality is false, as the −3 versus 2 example showed ([≈1:03:11](../raw/transcripts/11-games-ii.md)). The proof is **linear programming duality**, and the optimal mixed strategies can be computed efficiently by linear programming for any payoff matrix and any number of actions; the lecture has just solved such a linear program by hand ([≈1:03:11](../raw/transcripts/11-games-ii.md)).

The upshot answers the poll: **revealing your optimal mixed strategy doesn't hurt you**. You can tell all your friends you will play Morra with $[7/12, 5/12]$ (7/12 on one finger, 5/12 on two) and you won't suffer for it ([≈1:04:00](../raw/transcripts/11-games-ii.md)). For Morra both players' optimal strategy is $[7/12, 5/12]$; the code's evaluation of that pairing gives $-1/12 \approx -0.0833$ ([slides](../raw/slides/11-games-ii.md)).

The minimax principles from turn-based games carry over ([≈1:04:46](../raw/transcripts/11-games-ii.md)): at minimax strategies, if your opponent deviates it can only help you, and if you deviate you can only make it worse. And as in lecture 10, minimax is the right play against a perfect opponent; if you knew your opponent always shows two fingers, you could do better by showing two as well. Expectimax is best when you know what your opponent will do, while minimax guards against any huge downside when you don't ([≈1:05:33](../raw/transcripts/11-games-ii.md)).

## Non-Zero-Sum Games and Nash Equilibria

The last part is "just a brief overview just to get you some exposure" ([≈1:05:33](../raw/transcripts/11-games-ii.md)). In a **non-zero-sum** game the players' utilities are arbitrary rather than summing to zero ([≈1:06:18](../raw/transcripts/11-games-ii.md)). Competitive games are zero-sum (solved by minimax, via linear programming or search). In cooperative games every player has the same utility, which reduces to pure maximization: "it's just search, because everything is a max." Real life is somewhere in between.

The example is the **prisoner's dilemma** ([≈1:07:05](../raw/transcripts/11-games-ii.md), [≈1:07:54](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)). A prosecutor asks A and B separately whether each will testify against the other. If both testify, both get 5 years; if both refuse, both get 1 year; if only one testifies, that one goes free and the other gets 10 years. With utility equal to minus the years in jail, and a payoff matrix $V_p(\pi_A, \pi_B)$ now subscripted by the player $p$:

| A \ B | testify | refuse |
| --- | --- | --- |
| testify | A: −5, B: −5 | A: 0, B: −10 |
| refuse | A: −10, B: 0 | A: −1, B: −1 |

Von Neumann's theorem does not apply, since the game is not zero-sum, but something weaker does: the **Nash equilibrium** ([≈1:07:54](../raw/transcripts/11-games-ii.md), [≈1:08:39](../raw/transcripts/11-games-ii.md)). This is a pair of strategies $(\pi_A^\ast, \pi_B^\ast)$ such that neither player has an incentive to change strategy:

$$V_A(\pi_A^\ast, \pi_B^\ast) \ge V_A(\pi_A, \pi_B^\ast) \text{ for all } \pi_A \qquad V_B(\pi_A^\ast, \pi_B^\ast) \ge V_B(\pi_A^\ast, \pi_B) \text{ for all } \pi_B$$

**Nash's existence theorem** (1950): any game with a finite number of players and a finite number of actions has **at least one** Nash equilibrium ([≈1:09:27](../raw/transcripts/11-games-ii.md)).

Three examples ([≈1:09:27](../raw/transcripts/11-games-ii.md)–[≈1:11:46](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)):

1. **Two-finger Morra:** the minimax strategy $[7/12, 5/12]$ is also a Nash equilibrium, since no player has an incentive to move off it.
2. **Collaborative Morra**, where A and B receive the same payoff: there are two equilibria, both play 1 or both play 2. Both-play-1 is not optimal, but it is stable, because if B moves off it things get worse for everyone. "So this is a local optimum."
3. **Prisoner's dilemma:** the only Nash equilibrium is that both testify. Both refusing is not stable, since each would rather testify and go free, and neither off-diagonal cell is stable. "If you have agents that are only after their own selfish interest you can get into situations which are suboptimal": both refusing is clearly better for both, but it is not an equilibrium.

The contrast ([≈1:11:46](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)): simultaneous zero-sum games have von Neumann's minimax theorem, possibly several minimax strategies but a **single game value**; simultaneous non-zero-sum games have Nash's existence theorem and possibly **multiple equilibria with multiple game values**. Nash equilibria are locally stable, in that no individual agent can improve by changing, but they are not about optimality in the sense the rest of the course pursues. With players holding different utilities, "what does optimal with multi-objective even mean?" ([≈1:12:32](../raw/transcripts/11-games-ii.md)). See [Nash equilibrium](nash-equilibrium.md).

## Summary and What Comes Next

In simultaneous games the two players move at the same time ([≈1:12:32](../raw/transcripts/11-games-ii.md)). In general the second player has an advantage and can play a pure strategy, but the minimax theorem says that with mixed strategies the second player has no advantage and both players can play optimally. Nash equilibria exist for non-zero-sum games but are not unique. There is a huge literature in game theory and economics, and a game-theory course is the place to learn more ([≈1:13:19](../raw/transcripts/11-games-ii.md)). This lecture concludes the games unit and the state-based models (search, MDPs, RL, games); the next unit is Bayesian networks ([≈1:13:19](../raw/transcripts/11-games-ii.md)).

---

- **[Slides](../raw/slides/11-games-ii.md)**: the full content of `td_learning.py` and `simultaneous_games.py` as taught (pinned to the course repo's "lecture 11" commit, since both files were later rewritten), every traced demo output, and vision-pass descriptions of all 26 figures. Rendered lectures: <https://stanford-cs221.github.io/autumn2025-lectures/?trace=td_learning> and <https://stanford-cs221.github.io/autumn2025-lectures/?trace=simultaneous_games> (these now show the 2026 rewrite, not the version taught).
- **[Transcript](../raw/transcripts/11-games-ii.md)**: edited and timestamped. Verbatim captions are in `original/`.
- Topic pages: [TD learning](td-learning.md), [simultaneous games](simultaneous-games.md), [Nash equilibrium](nash-equilibrium.md); also [games](games.md), [minimax](minimax.md), [evaluation functions](evaluation-functions.md).
