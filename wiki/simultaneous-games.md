---
title: Simultaneous Games — Payoff Matrices, Mixed Strategies, and the Minimax Theorem
---

# Simultaneous Games

In a **simultaneous game** both players choose their moves at the same time, as in rock-paper-scissors ([lecture 11](11-games-ii.md), [≈38:04](../raw/transcripts/11-games-ii.md)). The [game trees](games.md) of lecture 10 cannot represent this, because a tree assumes exactly one player moves at each node; with both moving at once "are you maxing? are you mining?" ([≈38:53](../raw/transcripts/11-games-ii.md), [≈39:40](../raw/transcripts/11-games-ii.md)). The question the topic answers is the lecture's opening poll: **can you still play optimally if you reveal your strategy?** For zero-sum games the answer is yes, provided you randomize. Non-zero-sum simultaneous games are on the [Nash equilibrium](nash-equilibrium.md) page.

## Payoff Matrices and Strategies

A **single-move simultaneous game** has two players A and B, no state, and a single action each ([≈41:14](../raw/transcripts/11-games-ii.md), [≈42:01](../raw/transcripts/11-games-ii.md)). It is described by a **payoff matrix** $V(a, b)$ giving A's utility for each action pair $(a, b)$; in a zero-sum game B's utility is $-V(a, b)$.

The running example is **two-finger Morra** ([≈39:40](../raw/transcripts/11-games-ii.md), [≈40:27](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)): each player shows 1 or 2 fingers; if both show 1, B pays A \$2; if both show 2, B pays A \$4; otherwise A pays B \$3.

| A \ B | 1 finger | 2 fingers |
| --- | --- | --- |
| 1 finger | 2 | −3 |
| 2 fingers | −3 | 4 |

A **strategy** is game theory's word for a policy ([≈42:48](../raw/transcripts/11-games-ii.md), [≈43:37](../raw/transcripts/11-games-ii.md)). A **pure strategy** (deterministic policy) is a single action. A **mixed strategy** (stochastic policy) is a probability distribution $\pi(a)$ over actions, written as a vector: always-one is $[1, 0]$ and uniform is $[0.5, 0.5]$. The value of the game when A plays $\pi_A$ and B plays $\pi_B$ is the payoff averaged over both players' independent choices ([≈44:23](../raw/transcripts/11-games-ii.md)):

$$V(\pi_A, \pi_B) = \sum_a \sum_b \pi_A(a) \pi_B(b) V(a, b)$$

For Morra: both uniform gives 0; A always-one against uniform B gives −0.5; A always-two against uniform B gives 0.5; uniform A against B always-one gives −0.5, and against B always-two 0.5 ([≈45:57](../raw/transcripts/11-games-ii.md), [≈46:45](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)).

## Breaking the Jam: Let Someone Go First

A maximizes $V$ and B minimizes it, at the same time, a deadlock like four cars jammed at an intersection. The lecture's way out is to let one player go first and see what that costs ([≈49:05](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)).

**With pure strategies** going first is plain [minimax](minimax.md) on a two-level tree ([≈49:51](../raw/transcripts/11-games-ii.md), [≈50:39](../raw/transcripts/11-games-ii.md)). In Morra, if A goes first, B mismatches and A gets −3 either way; if B goes first, A matches and gets 2 or 4, so B shows 1 and the value is 2. In general, **going second is no worse** ([≈51:26](../raw/transcripts/11-games-ii.md)):

$$\max_a \min_b V(a, b) \le \min_b \max_a V(a, b)$$

The second mover sees the first move and can react, and with no state the first mover cannot remove any options, so moving second only adds information.

**The second player never needs to randomize.** Fix any mixed strategy for the first player; each of the second player's actions then has a fixed expected payoff, and the best response is to put "all my eggs in that one basket" on the best one ([≈54:34](../raw/transcripts/11-games-ii.md), [≈55:21](../raw/transcripts/11-games-ii.md)). Formally, for any fixed $\pi_A$, $\min_{\pi_B} V(\pi_A, \pi_B)$ is attained by a pure strategy. Against a uniform A, for example, B's two options are worth $-1/2$ (show 1) and $1/2$ (show 2), and B, who is minimizing, shows 1 ([≈53:45](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)). In the lecture this came out as "always play two" before a student's correction; see [lecture 11](11-games-ii.md#mixed-strategies).

## Mixed Minimax: Solving Morra

Now let A go first with a **mixed** strategy $\pi_A = [p, 1 - p]$, an uncountably infinite set of options, and let B answer with one of its two pure strategies ([≈56:10](../raw/transcripts/11-games-ii.md)–[≈57:44](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)):

- B shows 1: $p \cdot (2) + (1 - p) \cdot (-3) = 5p - 3$
- B shows 2: $p \cdot (-3) + (1 - p) \cdot (4) = -7p + 4$

B takes the smaller, and A picks $p$ to make that as large as possible:

$$\max_{0 \le p \le 1} \min \lbrace 5p - 3, -7p + 4 \rbrace = -\frac{1}{12} \quad \text{at } p = \frac{7}{12}$$

The minimum of a rising line and a falling line is largest where they cross, so set $5p - 3 = -7p + 4$ to get $p = 7/12$ and value $-1/12$ ([≈59:22](../raw/transcripts/11-games-ii.md)–[≈1:00:54](../raw/transcripts/11-games-ii.md)). Redo it with B going first, $\pi_B = [p, 1 - p]$: the min and max swap order and the answer is again $-1/12$ at $p = 7/12$ ([≈1:01:39](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)).

## Von Neumann's Minimax Theorem

The equality is not a coincidence ([≈1:02:24](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)). **Minimax theorem** (von Neumann, 1928): for every simultaneous two-player zero-sum game with a finite number of actions,

$$\max_{\pi_A} \min_{\pi_B} V(\pi_A, \pi_B) = \min_{\pi_B} \max_{\pi_A} V(\pi_A, \pi_B),$$

where $\pi_A$ and $\pi_B$ range over **mixed strategies**. With pure strategies it is false, as Morra's −3 against 2 shows ([≈1:03:11](../raw/transcripts/11-games-ii.md)). The proof is **linear programming duality**: the problem is a linear program, and optimal mixed strategies can be computed efficiently by linear programming for any payoff matrix and any number of actions ([≈1:03:11](../raw/transcripts/11-games-ii.md)).

Consequences:

- **Revealing your optimal mixed strategy doesn't hurt you.** You can announce that you will play Morra with $[7/12, 5/12]$ and lose nothing by it ([≈1:04:00](../raw/transcripts/11-games-ii.md)). In Morra both players' optimal strategy is $[7/12, 5/12]$, and the game's value is $-1/12$, slightly in B's favor.
- **The minimax principles hold.** At minimax strategies, an opponent who deviates can only help you, and deviating yourself can only hurt ([≈1:04:46](../raw/transcripts/11-games-ii.md)).
- **Minimax is still a guarantee, not a best response.** If you knew your opponent always shows two fingers you would do better by showing two; as in lecture 10, expectimax is best against a known opponent, while minimax guards against the worst case ([≈1:05:33](../raw/transcripts/11-games-ii.md)).
- **Several minimax strategies, one value.** A simultaneous zero-sum game may have multiple minimax strategies but has a single game value, which is what fails in non-zero-sum games ([≈1:11:46](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)).

The lecture notes one boundary: these are **single-move** games. Played repeatedly, the history becomes state and the game is a different one ([≈48:20](../raw/transcripts/11-games-ii.md), [≈49:05](../raw/transcripts/11-games-ii.md)).

## Related Pages

- [Lecture 11](11-games-ii.md) — the lecture page, including the in-class Morra rounds
- [Nash equilibrium](nash-equilibrium.md) — what replaces the minimax theorem when the game is not zero-sum
- [Minimax](minimax.md) — the turn-based principle this generalizes
- [Games](games.md) — the turn-based formalism, and the face-off showing that "optimal" depends on what you assume about the opponent
