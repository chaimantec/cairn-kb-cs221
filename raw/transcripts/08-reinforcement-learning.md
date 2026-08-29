---
title: Lecture 8: Reinforcement Learning
lecture: 8
video: https://www.youtube.com/watch?v=34Hk2v2kwg4
source: edited YouTube transcript — see original/ for verbatim captions
edited: true
---

# Lecture 8: Reinforcement Learning — transcript

Edited from auto-generated captions. Punctuation, capitalization, and technical terms have been corrected. Filler words and false starts have been removed. Student questions are marked. The verbatim original is in `original/08-reinforcement-learning.md`.

## Editing notes

Notable term restorations (cross-checked against the lecture slides, `raw/slides/08-reinforcement-learning.md`):

- "walk from I to I plus one" → walk from i to i + 1; "take a tram from I to 2I" → take a tram from i to 2i ([0:05]).
- "is end test" → is_end test ([0:05]); "is end is a test" → is_end ([0:53], [18:43]); "where is end is true" → where is_end is true ([28:53]); "is and function" / "Is and is generally pretty easy" → is_end ([38:09]).
- "a MDP" → an MDP throughout ([0:05] and later); "the estimated MP" → the estimated MDP ([43:37]); "estimating MDPK" → estimating the MDP ([51:18]).
- "V star" → V*(s) ([8:40]); "Q star of S SA" → Q*(s, a) ([8:40]); "pi of star of S" → π*(s) ([9:25]); "Q pi S of pi of S" → Q_π(s, π(s)) ([6:20]); "Q pi of SA" → Q_π(s, a) ([1:03:37], [1:15:05]); "Q S prime a prime" → Q(s′, a′) ([1:14:19]); "gamma times um the value of under pi of this state S prime" → γ times the value under π of this state s′ ([7:07]); "gamma" → γ ([1:08:12]).
- "Q opt" kept as spoken ([1:03:37]) — the slide figures use V_opt/Q_opt; the source text uses V*/Q*.
- "you're waiting um each successive step by .5 or .5 squared" → you're weighting each successive step by 0.5 or 0.5 squared ([3:16]).
- "take a tram if always possible" → take the tram if possible ([2:29], per the slides' `tram_if_possible_policy`); "walk tram policy" → `walk_tram_policy` ([26:34]).
- "roll out" / "roll outs" → rollout / rollouts as nouns ([17:58], [52:04], [1:05:53]); verb phrases ("roll out", [1:06:40]) kept.
- "epsilon greedy" → epsilon-greedy ([50:33], [56:44], [1:05:08]).
- Code names restored: `get_action` / `incorporate_feedback` (from [15:39] onward), `StaticAgent` ([15:39]), `simulate` ([17:58], [20:15]), `walk_tram_policy` ([26:34]), `sum_utilities` and `counts` ([52:04]), `self.pi` ([1:15:52]).
- "partial observed MDPs" → partially observable MDPs ([14:06]); the lecturer's echo at [42:05] likewise. "hidden Markov" kept as spoken ([42:05]) — the adjacent real term is hidden Markov model.
- "inverse of the cost" → negative of the cost ([21:47], student question), following the lecturer's echo ("the cost is the negative reward here").
- "sparse reward problems" kept ([22:34]).
- "model uh free" → model-free ([45:54]); "model free Monte Carlo" → model-free Monte Carlo ([51:18], [1:04:23], [1:05:08]); "model Monte Carlo" → model-free Monte Carlo ([1:05:53]); "model base" → model-based ([1:02:51]).
- Numbers: "expected utility of minus uh nine here" → minus 9 ([34:18]); "It's minus nine" → minus 9 ([42:51]); "we get minus nine" → minus 9 ([1:04:23]) — the slides' leaderboard records −9.0 (explore) and −9.45 (model_free_monte_carlo); the lecturer rounds to minus 9. "minus 6.6" kept ([41:13]); "minus 9.6" kept ([1:11:15]). "minus 10" kept ([19:30]) — a single rollout shown in the demo; not the seed(1) rollout recorded in the slide trace (utility −18), so it is kept as spoken. "0.6667 and 0.333" kept ([37:23], matching the slides' 2/3–1/3 split for state 1's tram). The six "1/6"s of the fair die are kept ([13:19]).
- "0.1" kept as spoken in the epsilon-greedy explanation ([50:33]) — the demo runs epsilon=0.4 per the slides, but the "let's say 0.1" is an illustrative value; no number is changed.
- "policy iteration" kept as spoken at [5:35] and [10:11], both flagged inline — the course's two recap algorithms are policy evaluation and value iteration; the pairing of the two names in each mention suggests the caption may be ASR-confused, but the words cannot be recovered with certainty.
- [32:47]: "as long as we don't work in the explore phase" → "as long as we don't have an exploitation policy — as long as we're in the exploration phase"; per the slides' `get_action` (follow the exploration policy until the exploitation policy exists).
- [39:41]: the caption's "That sometimes the policy actually ran another one earlier today" is garbled (false start); repaired to "Sometimes it doesn't work out — I actually ran another one earlier today with a different random seed, and the policy was wrong in that case as well."
- [49:47]: "If we forget a bunch of rollouts" → "if we generate a bunch of rollouts"; the meaning (estimate Q by averaging many rollouts) follows from the surrounding text.
- [44:22]: "up down up here" → "going back up here" (positional filler, silently cleaned); the student's "run into policies that we haven't seen before" is rendered "run into states that we haven't seen before", following the lecturer's reply ("it's possible that you'll encounter new states").
- [47:27]: "when we were doing policy" kept as spoken — the sentence's own continuation ("the first thing we did when we said we're going to evaluate policies...") makes the reference to policy evaluation clear.
- Student questions: marked throughout. Where the words are garbled, kept close to the captions with inline [Ed: unclear] markers. The [4:50] student question ("the partial in the in the policy") is unintelligible; the lecturer's paraphrase ("why do we have this partial? ... a partial application of this function") is the only recovery cue. The two-part exchange at [21:47]/[22:34] splits across the paragraph boundary as in the captions (second question's opening in [21:47], completion in [22:34]). At [1:16:45] the caption's ">>" speaker mark is stray; it is the lecturer's closing remarks.

Inline [Ed: unclear] markers:

- [4:50]: [Ed: unclear — "the partial in the in the policy"].
- [5:35] and [10:11]: [Ed: unclear — "policy iteration"].
- [33:33]: [Ed: unclear — "four times uh you end up in the same state and six times you're end up in you know, two times uh the current state"] — the numbers match no transition counts shown later in the demo (state 1 tram: 1/3 and 2/3; state 2 tram: 2/7 and 5/7), and the second half is internally contradictory ("same state" = "current state"), so the words are kept as captioned.
- [1:03:37]: [Ed: unclear — the student's "it appears that it can be average of the two things"] — the lecturer's reply ("we are computing Q_π ... later we're going to see how we can actually get to Q_opt") is the recovery cue.
- [1:13:33]: [Ed: as spoken — "SARSA is now a subclass of Q-learning"] — the source has Q-learning subclass SARSA; the lecturer's next sentence ("Q-learning inherits everything from SARSA") gives the intended meaning.

Parent adjudication (2026-08-29): the edit initially completed six sentences across marker boundaries; the parent restored them to the captions' original splits at [1:41]/[2:29], [28:53]/[29:41], [32:47]/[33:33], [44:22]/[45:08], [45:08]/[45:54], and [48:14]/[49:01], so citations follow the timestamps where the words were actually spoken. The heavy stutter in the equation dictation at [1:15:05]–[1:15:52] was removed without moving content.

---

**[0:05]** Okay, let's get started. So, last time we started talking about Markov decision processes. This time we're going to talk about reinforcement learning. But first, let's review what a Markov decision process is. So, when you define an MDP, or a Markov decision process, you specify a start state, successors — which specify the actions, probability, rewards, and next states — an is_end test, and a discount factor. So, we looked at this flaky tram MDP, where the goal is to go from state 1 to state 10. And at each point, you could either walk from i to i + 1, or you could take a tram from i to 2i, except for 40% of the time it

**[0:53]** fails and you end up in the same state. Okay? So, the start state is 1. So, the successors of a particular state are a set of successors, where in this case, if the state is 1, you can walk with probability 1, and you go to state 2 with a reward of minus 1. Remember, reward is just minus cost, so that's a cost of 1. And you can take the tram, and with probability 0.6, you go to state 2, and with probability 0.4, you go to state 1. And in both cases, it costs 2, or a reward of minus 2. And then is_end is a test that, given a state, determines whether you're at the end and the game is over.

**[1:41]** Okay, so MDPs can be best visualized by a graph, where nodes are states, and edges are either actions or transitions. So, there are two types of nodes. There are state nodes, such as this, and chance nodes. So, the agent makes decisions at the state nodes — for example, to walk or to tram. And then, from the chance nodes, nature decides what happens. So, if you took a tram, then with probability 0.4, you go back to state 1, and with probability 0.6, you go to state 2. And when you transition, you pick up your reward. Okay? And so, you can see that

**[2:29]** this is a graph, and our goal is to figure out how to go from 1 all the way to the end, 10. So, we also defined what a solution to an MDP is, which is a policy. A policy maps a state to an action. In search problems, we could just have a sequence of actions, and that was a solution. But in MDPs, there's randomness, so you have to know for each state what to do. We looked at one particular policy, which is take the tram if possible. And you can call that policy if you want. When you take a policy and an MDP, you can generate

**[3:16]** a rollout, which is a sequence of steps, where you're using the policy to determine what actions to take, and you're letting the MDP decide — you're choosing a successor from the MDP based on the probabilities. Okay? We also looked at the utility of a rollout, which is the discounted sum of rewards. In this case, the discount is 1, in which case the utility is just the sum of the rewards. If this is 0.5, then you're weighting each successive step by 0.5, or 0.5 squared, and so on. The value of the policy is the expected

**[4:04]** utility of the policy. Meaning that, if you do an infinite number of rollouts, what will be the average of your utilities? We looked at an algorithm called policy evaluation, which computes the value of a given policy. And policy evaluation defines a recurrence, kind of like when we do dynamic programming, where you compute the value for each state as a function of successor states. And then we also looked at value iteration, where, if you just insert a max in there, you can compute the value of the optimal policy. And also you can extract the optimal policy.

**[4:50]** which happens to be walk all the way to state 5 and take the tram. Okay. So, I'll pause there. Hopefully that should all be review. Yeah.

> **Student question:** [Ed: unclear — "the partial in the in the policy"]

So, the question is: why do we have this partial? This is just a syntactic trick. So, a tram-if-possible policy takes two arguments: an MDP and a state. So, this is a partial application of this function, where I've called it with only one argument. So then, this object here, `policy`, is still a function that takes a second argument, state.

**[5:35]** So, it allows you to do partial application. Yeah. Okay. Any questions about MDPs? Okay. So, I'm going to give you another way to think about this — if you like code, I guess this is good; if you like math, then hopefully this part will help as well. So, the way to think about where value iteration and policy iteration [Ed: unclear — "policy iteration"] come from is the recurrences that they're computing. So, in particular, the policy evaluation recurrence can be visualized as so.

**[6:20]** So, the idea is that you're at a state. What is the value of that state under the policy π? So, remember, the value at a state is: what is the expected utility — or the value — that you get if you start at s, and you follow policy π? Okay? And so, this can be expressed as — well, you follow policy π. You call π(s), that gives you an action, and that takes us to this chance node, which is Q_π(s, π(s)). Okay? So, this node is (s, π(s)) — a state and a particular action.

**[7:07]** And from there, what is the Q-value? The Q-value, Q_π(s, a) in general, is the value of taking an action a in state s, and then continuing to follow the policy π. Okay? So, this can be expressed as: let's consider all the possible successor states s′. I look at the probability of going to that successor state s′, and I use that to weight the reward that I get from making that transition — times, oh sorry — plus the discount factor γ times the value under π of this state s′.

**[7:53]** Okay, so hopefully this visual helps you understand the recurrences. You know, if you want to calculate the value here, you take an action, and then, to understand the value here, you consider all possible transitions. Okay. Maybe let me pause there, to see if there's any questions about the recurrence for policy evaluation. So then, value iteration is very similar. So, I'm using "opt" and "star" kind of interchangeably here.

**[8:40]** So, value iteration — remember — computes the optimal value V*, which is the value of following the optimal policy from state s. So, if I ask, what is the optimal value at state s? Well, I want to choose the best action. So, I take a max over a of Q*(s, a). So, instead of following a particular policy π, I'm going to take the max over these possible actions. And then, this part is the same as before, except I have Q* instead of Q_π. So, what happens when you're at

**[9:25]** a particular chance node? You consider all possible successors, weighted by the transition probability, of the reward plus the discounted future value. Okay? All right. So then, once you have Q*, you're basically done with the optimal policy. You take the action with the highest Q-value. So, π*(s) is just the argmax over a of Q*(s, a). So, you're basically looking at what is the optimal policy from here. You're looking at which of the successors gives you the most value,

**[10:11]** and then that is the action you choose. Okay. Questions about these recurrences? So, one way to think about these recurrences is that, once you have a very crisp understanding of what a quantity is, then you just write it in terms of other quantities. And then, when you go to policy evaluation and policy iteration [Ed: unclear — "policy iteration"], it's essentially writing these recurrences down in code, and iterating, as we did last time.

**[10:59]** Okay. So, now we're going to do something new. So, can we find the optimal policy if we don't know the MDP? Okay. So, if we know the MDP, we can run value iteration, we can compute the optimal policy, but what if we don't know it? And that is going to be reinforcement learning. So, reinforcement learning, in some sense, is MDPs where you don't know the MDP. Hopefully that'll make more sense in a second. So, here's the general reinforcement learning setting: you have an agent, and you have an environment. The agent takes actions,

**[11:47]** and the environment gives the agent rewards and observations. And you iterate, and you iterate, and stuff happens. Okay? So, agent produces an action, environment produces reward and observation, agent produces another action, environment produces reward and observation. Okay? So, it's very simple, which is one of the beautiful things about reinforcement learning. And intuitively, what's happening is: you might think about — okay, how do I design an agent that does well? A good agent should try various actions, essentially, to find the good ones, and then it should learn to keep on doing those

**[12:32]** actions. In other words, those actions are reinforced. So, the word "reinforcement learning" kind of shows up in the way I described what an agent should be trying to do. Okay? So, an important aspect of reinforcement learning is that, in the beginning, the agent doesn't know anything. So, the agent really has to try different actions, and along the way, hopefully the agent will learn what's good and what's bad, as well as where actions take me, for example. So, one thing is that I like to think of reinforcement learning as really a metaphor for life. So, in MDPs

**[13:19]** it's already kind of tricky, because you don't know what the outcomes are going to be. But at least you know their probabilities. So, for example, you don't know, when you roll a dice, if it's going to be three or four or five, but you know that, if it's a fair dice, it's going to be 1/6, 1/6, 1/6, 1/6, 1/6, 1/6. In reinforcement learning, we don't even know what the probabilities will be. But that's kind of real life, right? You take some action, but you just don't even know what the chance of success of a particular action is. So, you sometimes just have to try it out, and then learn from your mistakes.

**[14:06]** Okay, so in this lecture we're going to specialize a little bit, and assume that the environment is defined by a Markov decision process, or MDP, and that the observation is the state that you get when you take an action in the MDP. So, in real life, you don't get the full state of the world. You only observe part of the state, and that falls into the category of partially observable MDPs, or POMDPs, which we're not going to talk about. I would say that reinforcement learning is actually fairly general. All these algorithms apply to

**[14:52]** — well, maybe not these algorithms, but the algorithms I'll talk about next time can be generalized to POMDPs — but we're going to stick with MDPs, because it's simpler to think about. Okay, so let's define an example here, and see how RL works. So, again, we have this flaky tram MDP. So, that's going to be our environment. And we're going to define an agent, which I'm going to use interchangeably with an RL algorithm. So, "agent" is not to be confused with language model agents, which you hear all the time now. It's an RL agent.

**[15:39]** And so, let me define the simplest agent possible. The simplest agent is: you have some policy, such as take the tram if possible, and I'm going to define this `StaticAgent`, which just takes a policy. So, the agent just remembers the policy here. And then, remember, there are two things that it does. You can call `get_action` on it, and you call `incorporate_feedback` on it. Okay? That corresponds to these two arrows here. So, `get_action` takes — gets

**[16:25]** an action, and `incorporate_feedback` lets the agent consume the reward and observations. Okay. So, what does `get_action` do? In this static case, all `get_action` does is just call its internal policy. Okay? And `incorporate_feedback` — what does it do here? So, this is an example of feedback that the agent might get, which is: I was in state 1, I walked, I got reward minus 1, I ended up in state 2, and I'm not at the end. Okay? So, that's like a little fragment of data that

**[17:11]** is an observation that the environment is going to send back to the agent. And the agent can do whatever it wants with it. So, in this case, it's a simple agent, a static agent, so it doesn't do anything. Okay. So, given an agent — this static agent — and the environment, I can simulate by generating rollouts. So, we already did this a few times in class. Last lecture, we used rollouts to determine the value of a policy. So, we're going to do that here. So, this function `simulate` is

**[17:58]** going to take a number of trials, and I'm going to just try multiple times and take the average. So, okay, the environment state is the state of the MDP. And then, while I'm not at the end — while I haven't ended — I'm going to ask the agent for an action. The agent says tram. Okay, great. And then, I'm going to ask the environment to sample a particular successor state, or step in general, given the state and the action. And then, now I have the

**[18:43]** — so, maybe the environment gives me this. Now, this is a piece of feedback that I'm going to give back to the RL agent. So, I have a particular state. I took the action tram. I got this reward. I ended up in this new state, and is_end, which is false here. Okay? So, I'm also going to just keep track of all these steps here. So, this kind of goes round and round and round. We saw this last time, so I'm going to just jump over to here. And — oops. I didn't mean to jump there.

**[19:30]** Let's see what happened here. Okay. Okay, so I'm simulating. I'm going to jump here. So, one rollout gives me tram, tram, tram, tram, walk, and etc., etc. And that gives me a utility of minus 10. And if I do this over multiple trials, then each trial gives me a different utility. Okay? And this should be very familiar. This is exactly what we did in policy

**[20:15]** evaluation. So, in some sense, this piece of code, `simulate`, defines the rules of the game. Okay. So, now, the simulation is going to yield some estimated value — the estimated value of the utility. And notice again that the agent doesn't do anything with the feedback. So, the main difference between a policy, which we talked about last time, and an agent, which we're talking about today, is that the policy is something that's static. It just maps a state to

**[21:00]** an action, and doesn't change over time. Whereas an agent — in other words, an algorithm — also takes states and maps them to actions via the `get_action` function, but it can change over time; it's dynamic. In this case, this particular agent didn't change over time, but we call `incorporate_feedback`, and the agent can do something else. So, what we really want is for the RL algorithm to use that feedback to improve its own internal policy. That's the whole point of reinforcement learning. So, how should we do this update? That is the question. Okay, I'll maybe stop there.

**[21:47]** Any questions about the reinforcement learning setup? What an agent is? Yeah.

> **Student question:** Um, so for this current setup, you're using only the negative of the cost as the reward, but don't we have to set up the reward for reaching the end goal?

So, the question is: the cost is the negative reward here. So, you can either think in terms of costs, or think in terms of rewards. All the algorithms, all the math will be the same — you just have to negate it. Yeah.

> **Student question:** In your current, you just define the reward for each action, but

**[22:34]** > **Student question (continued):** don't we have to define the reward for reaching the end goal?

So, do you have to define the reward for reaching the end goal? You do, because reaching the end goal via an action is just another — so, for example, here, right? Reaching the end goal has some reward. It's not really special. Every transition in the MDP has some reward. You could say there are no rewards on everything except at the very end. So, some problems are like that, and those are called sparse reward problems. But, in general, you can have reward at any point in time.

**[23:26]** Yeah.

> **Student question:** So, can I just understand the agent as changing the policy dynamically?

Yeah, so, can you understand the agent as changing the policy dynamically? I think that's a great way to think about it. So, maybe I'll draw a picture here. So, here is the agent, and here is the environment. And you can think about: inside the agent, there is some policy. And we'll talk about what's inside the agent later. So far, we've seen this picture where there's some policy, and the logic of the agent in general is going to be

**[24:13]** So, when it says `get_action`, it's going to go and ask the policy for the action. And then, when it gets feedback, it's going to do some stuff to update the policy. Yeah, hopefully this will be clear once I actually describe some RL algorithms that are not just "do nothing." Okay, so I'm going to talk about four different RL algorithms: model-based value iteration, model-free Monte Carlo, SARSA, and Q-learning. And each one has a slightly different — well, all of them are very similar in some sense, but there are some important differences, which I'll try to motivate along the way.

**[24:59]** Okay, so the first thing: if you have already studied MDPs, like we have, then this model-based approach is the very natural one. So, what makes RL hard is that we don't know the MDP. Otherwise, if we did know the MDP, we could just use value iteration to compute the optimal policy. So, here's an idea. Why don't we just learn the MDP — or estimate the MDP — from the feedback that we have? Okay? So, because the feedback tells us: I'm in a state, I took an action, I got some reward, and I end up in a different state. Right? That information should allow us to get an idea of what the MDP is.

**[25:46]** Okay, so this approach is called model-based value iteration. And what we're going to do — there are three stages here. One is an exploration stage, where we're going to use some exploration policy, which might be as simple as just choosing random actions, and use that to estimate the MDP. And then, I'm going to compute the optimal policy of the estimated MDP using value iteration. So, this is: once I have the MDP, I just go back to last lecture, and that's what I do. And then, finally, I'm going to just follow this policy that I just learned.

**[26:34]** Okay, so there's a sort of exploration phase, and then there's an exploitation phase. Okay. So, let's define this MDP again. So, what should the exploration policy do? I'm going to call this `walk_tram_policy`. And it's going to be a policy that just essentially samples a valid action. Okay? So, for example, from state 1, if I can take the tram, then I'm going to choose either walk or tram with equal probability. So, sometimes I get tram, sometimes I get walk.

**[27:20]** From state 6, I can't take the tram, because 6 * 2 would go over 10, so I can just walk. Okay? So, the reason why I'm using this as the exploration policy is that I want to explore. So, this is the exploration phase. So, I want to try out all the actions. Okay. So, now I'm going to define the agent. This is an instance of the RL algorithm. And I'm going to keep track of the exploration policy, and also an exploitation policy, which is going to be estimated soon. And also

**[28:06]** an MDP. Okay? And so, now I can maybe try to flush this out. So, in the case of model-based value iteration: if I'm model-based, then what I have is the exploration policy, and then I have this MDP that I'm trying to estimate, and then I also have this exploitation policy. Okay.

**[28:53]** So, what is this MDP? So, this is this class. So, normally, when I define an MDP, it was essentially hard-coded, right? I just said, "Here are the states, here are the actions," and so on. But now, I want to learn this MDP, so I'm going to make it a little bit more flexible. So, in particular, there's some start state, which I don't know. There are some rewards, which I don't know, and this is just going to be a mapping from (state, action, next state) triples to a reward. And then transitions, which I also don't know — that's going to be a mapping from state, to action, to state′,

**[29:41]** to some count of how many times I witnessed this transition. And end states are going to be just a set of states that I saw, where is_end is true. Okay, so initially, I don't know anything. So, all these are just going to be empty. And discount — I'm just going to — it's just a number; I'm just going to remember that. Okay, so the model-based RL algorithm starts off with the exploration policy, an empty MDP, and an exploitation policy, which is also empty right now. Okay, so let me give some examples of

**[30:27]** calling `get_action` and `incorporate_feedback` on this RL agent. So, if I call `get_action` on state 1, what does it do? Well, this is what the agent is going to do. So, if I have an exploitation policy, then I would use it. If I don't, I call the exploration policy. Okay? So, the exploration policy is just going to choose a random valid action. Okay? And then, `incorporate_feedback` — before, I didn't do anything, so let's see how I incorporate feedback here. I'm going to call the underlying MDP's `incorporate_feedback`, just pass

**[31:14]** all the feedback to the MDP. And here, I'm going to update the MDP now. So, if I haven't seen a start state, then I set the start state. I'm going to set the reward. So, I saw this piece of feedback: I was in a state, I took an action, I got the next state, and I got this reward. So, I'm just going to remember this reward. And then, I'm going to note that I saw this transition from state, action, to next state, and I'm going to add one to that. Okay, and if I'm at the end, then I would add it to an end state, but I'm not at the end here. Okay. So,

**[32:00]** after incorporating this particular feedback, then this is my representation of the MDP. I know that I started in 1. I know that the reward of this particular transition is minus 1, and I saw that, if I am in state 1 and I walked, then one time I got to 2. Okay, any questions about what is happening here? So, maybe, just to flush this out: every agent has two functions, `get_action` and `incorporate_feedback`.

**[32:47]** Okay, so I went through what that means. So, I'm going to define a bunch of agents. So, for each agent, I have to tell you what its `get_action` does. And in this case, it just follows the exploration policy as long as we don't have an exploitation policy — as long as we're in the exploration phase. And `incorporate_feedback` just updates the MDP. Yeah.

> **Student question:** So, we don't really need to worry about probabilities when we're doing this.

So, the question is: do we have to worry about probabilities? We will have to worry about probabilities, and hopefully I'll show that. But this is going to come in through transition counts.

**[33:33]** So, in walk, it's deterministic, so it's always going to be — there's only one possible choice here. But if you take the tram, you'll see that, you know, [Ed: unclear — "four times uh you end up in the same state and six times you're end up in you know, two times uh the current state"]. And then we'll use that to actually estimate the probability. Okay. So, let's see what happens when I simulate the MDP using RL. So, I'm not going to step through this function, since I already showed it. This is basically 10 times rolling out this RL algorithm on this

**[34:18]** MDP, and I get an expected utility of minus 9. And so, what I can do now is compare the true MDP, which was used to simulate, and the internal MDP. So, you can think about this as: there's the environment, which has an MDP that is not telling the agent. And the agent is trying to make its internal MDP match the true MDP from the environment. So, in this case, I think

**[35:04]** let's see. So, let's look at the internals of this MDP. So, this is after doing 10 rollouts. The start state is 1, and here are all the rewards that I saw. So, I think it probably saw everything, but maybe not everything. And then here are the transition counts. So, to answer your question earlier: if I'm in state 2 and I took the tram, then two times I ended up in state 2, and five times I ended up in state 4. So, then, later we'll see that you can estimate the probability of this transition to be five out of seven and two out of seven.

**[35:52]** So, you notice that the counts are not fully accurate, because it should be 60% and 40%, but because these are small numbers, sometimes it's bigger and sometimes it's smaller. Okay. So, that's the internal representation of the MDP. That's this guy over here. Now, let's see when we call the true MDP. Okay. So, the true start state is 1, and the estimated — so, let me say, I have this MDP. If I want to get its estimated start state,

**[36:37]** I just return the start state. So, that's going to be the easy case. And now, if the true MDP has the successors from state 1, let's see what the estimated ones have. So, how do I compute the estimated successors? This is where I'm going to compute the probabilities. Right? Because the MDP class currently only stores the counts. So, what I'm going to do is: for every action — I'm at a particular state, and for every action, I'm going to look at where

**[37:23]** I kind of end up. In the case of walking, I always end up in 2. So, then the probability is 1, and I add walk with probability 1. So, now, for the action tram, I have these counts. So, one time I end up in 1, two times I end up in 2. So, when you compute those successors, then the estimated fraction will be 0.6667 and 0.333. So, this is the way I'm using the counts to define

**[38:09]** these successors. So, you'll see that the estimated successors — this is the true MDP, and this is the estimated MDP — and it's somewhat close, but not exactly correct, because it's just an estimate. Okay, and then you can check that the is_end function works as well. is_end is generally pretty easy. Okay. So, the more the agent explores, the closer the estimated MDP will be to

**[38:54]** the true MDP, assuming that the exploration policy actually tries all valid actions. So, obviously, if the exploration policy only tries one thing, then you'll never learn about the other actions that you didn't try. Okay, so that's stage one: what we've done is learned — that is, estimated — the MDP. Now we need to compute the optimal policy of this estimated MDP. And we're just going to run value iteration, and all value iteration does is — well, you know, remember what value iteration does from last time — it computes

**[39:41]** the optimal value, as well as the optimal policy. Okay? In this case, it actually turns out to be the optimal policy, even though the MDP was slightly wrong. Sometimes it doesn't work out — I actually ran another one earlier today with a different random seed, and the policy was wrong in that case as well. So, there's definitely some stochasticity. Okay? So, notice that, when you rerun value iteration, this policy is optimal for this particular MDP. But if the MDP is wrong, then the policy is also going to be wrong. But if the MDP is close to the true MDP,

**[40:27]** then hopefully the policy will be close to the true one as well. Okay, so let's just compare the policies. I think in this case the two policies are the same, which is great. Okay, that comment was about a different random seed. Oh, well. So, using this estimated policy, now we can go and exploit. Okay, so what I do here is just

**[41:13]** run this — it's the same agent, but now, inside the agent, it has the updated estimated MDP and the exploitation policy, and now I can get a much higher reward of minus 6.6. Okay, so this minus 9 was me just randomly exploring, and of course that's not going to be optimal. Once I estimated an MDP and computed the optimal policy on it, then now I'm getting a much better value. Okay, any questions about this?

**[42:05]** Yeah.

> **Student question:** Is this called hidden Markov or partial hidden Markov, or something?

So, your question is: is this called hidden Markov or partially observable MDPs? This is fully observed. So, fully observed means that everything we'll do here in RL is fully observed, because the RL agent — if you remember, when getting the feedback, it actually gets the state. So, if you only got part of the state, then it would be partially observed. Okay, so just some notes.

**[42:51]** So, notice that the utility, or the value, that you get in the exploration phase is suboptimal. It's minus 9. But this is critical, because — you know — we're learning. And so, you invest in learning, and then you can exploit later. In practice, you don't have to have these two phases, where you're exploring purely and then exploiting; I'm just doing this for simplicity right now. What you can do is: you can always continue to refine your MDP, and then, once in a while, you call value iteration and update the policy, and then you can even — as we'll see later — use the exploitation policy to explore as well, as long as

**[43:37]** you're careful. Okay. So, to summarize: model-based RL is what we're doing here. And model-based means estimating the MDP, or the model of the world, from feedback. And then, once you estimate the MDP, you use value iteration to compute the optimal policy of the estimated MDP. It's not the optimal policy overall, but you get what you can get. And then, once you have the estimated policy, you just exploit it. Okay. Yeah.

**[44:22]** Yeah.

> **Student question:** So, all of the states that are possible, right? Like, at the very beginning, we just don't know what to start off with in any state.

So, the question is: do you know all the states up ahead of time? You don't. If you remember — let's see, going back up here — in this MDP, when you initialize it, there are no transitions, there are no rewards — there's nothing. You only start updating this incrementally when you get `incorporate_feedback` from the environment.

> **Student question:** So, is it possible that we run into states that we haven't seen before?

Right. Yeah, so that's a good question. So, when you run your policy, after you've estimated your MDP, it's possible

**[45:08]** that you'll encounter new states. And in that case, yeah, you just generate a random action. Or, in this case, the code probably just crashes. Okay. Any other questions? Okay. So, now let's think about the next thing, which is: can we estimate the optimal policy more directly? So, at the end of the day, we just care about the policy. And so, the MDP that we estimated was just a means to an end. We did that because we knew

**[45:54]** how to get a policy from an MDP. Right? So, now, can we do this more directly? Okay, so the next three algorithms are going to be doing that, and, in general, these methods are called model-free. Model-based means that you're estimating the MDP. Model-free means that you're not estimating the MDP. Okay? So, before, we estimated the MDP first, and then used value iteration to compute the optimal policy of the estimated MDP. Okay, so let's just review — this is the recurrence that we just saw 30 minutes ago.

**[46:40]** The optimal policy is the argmax over the Q-values. And the Q-values are this other recurrence, in terms of transitions, rewards, and future values. So, the thing to note, based on these recurrences, is that all you need to know is Q, right? Like, if you know Q, then you can just get the optimal policy. When we did the model-based approach, we estimated T and R, and then we used those to compute Q, but maybe we can just get that Q directly.

**[47:27]** Okay, so let's try to do that. And the idea is actually very simple. We actually saw it last time, when we were doing policy. The first thing we did, when we said we're going to evaluate policies, we just rolled out a bunch of things and averaged. So, that's exactly what we're going to do here. Okay? So, there's going to be a slight nuance here, but we'll get to that. So, let's suppose that you did a rollout, and you have — you know — you walked, you took the tram, you took the tram, and the first time the tram got stuck, so you ended up in the same state; the second time, you ended up in state 4. So,

**[48:14]** I'm going to, for every step, compute a utility. Okay. So, normally, when we compute the utility of a rollout, we just compute a single number, which is the utility at the beginning. But, for reasons that will become clear, I also want the utility at each step, from that point on. Okay, so, at step 0, I have minus 1, which is this reward, plus the discount times minus 2, plus discount squared times this minus 2. Okay, so that's my utility here. And then, at step 1, I'm just going

**[49:01]** to pretend this first step, step 0, doesn't exist, and the reward is just going to be minus 2, plus the discount times minus 2. And then, for the last step, I'm going to assume that the first two steps don't exist, and it's just going to be minus 2 plus 0. Okay. So, there happens to be a nice recurrence between the utilities. So, the utility at step 0 is the reward at step 0, plus the discount times the utility at step 1. And similarly, for each

**[49:47]** step, it's going to be the reward at that step, plus the discount times the utility at the next step. Okay, so this recurrence is going to be helpful when we generalize the algorithm. All right. So, now let's go to the question of which policy should we use to roll out? If we generate a bunch of rollouts, we can estimate Q. So, for model-based value iteration, we had a purely random exploration policy.

**[50:33]** Here, we're going to do something a little bit more sophisticated. It's called epsilon-greedy. And the way epsilon-greedy works is: with probability epsilon — let's say 0.1 — you're going to choose a random action according to the exploration policy. And with probability 1 − epsilon, I'm going to choose the best action according to the current estimate of the Q-values. Okay? So, epsilon is generally small, and it controls how much exploration you do. The larger epsilon is, the more exploration you do. The smaller it is, the less exploration you do. So, what's going to happen —

**[51:18]** let's see here. I'll draw maybe another one of these diagrams. So, now we have an agent that's, I guess, model-free Monte Carlo. And what I'm going to do is: inside it, I'm still going to have the exploration policy here, but instead of estimating the MDP, I'm going to have an estimate of Q_π. Okay. So, let's see how I do that.

**[52:04]** Okay. So, the same MDP as before. The exploration policy is the same. So, in model-free Monte Carlo, I'm going to keep track of my estimate of Q. And the way I'm going to do that is: whenever I see a state-action pair, I'm just going to remember all the utilities that I've gotten at that point. I'm going to sum them. I'm also going to remember how many I have. So, essentially, that gives me a way to keep a running average. Okay. So, `sum_utilities` means, for every action and state,

**[52:50]** the sum of all the utilities I've gotten, and `counts` is the number of times I'm seeing that state and action. Okay. So, I'm also going to keep track of the current rollout. Because the feedback I'm getting is sort of in chunks: I only get one step of feedback at a time, and in order to compute the utilities, I need the whole sequence. So, this is sort of an annoyance, but, you know, I think it's fine. All right. So, let's see what happens when I call `get_action` on this particular agent, with state 1. There is a case where I haven't seen the

**[53:37]** state before, or I haven't taken any actions from the state, then I'm just going to call the exploration policy. And what happens when I do `incorporate_feedback` — this part is the same. I have to keep track of what the start state is, and I'm going to add to my rollout this particular step. Okay, so I remember the action. I'm just putting probability 1, because I don't need the probability here — just the reward and the next state. So, if I'm not at the end, I'm going to actually update my Q-values, but I'm not at the end yet. So, I incorporate the second piece of feedback. So, I'm not

**[54:25]** at the end. And now, the third piece of feedback. Now my rollout is walk, tram, tram, and, assuming that the end state is 4, just to keep things simple. Now, I'm at the end of this episode, and I'm going to update the statistics needed for computing the Q-values. So, I look at the utilities, and I want to compute a utility for each element of the rollout, where the last element — just like at the very end — is always going to be 0. So, I'm going to walk backwards and compute the utilities for every step. And remember — that

**[55:12]** recurrence I had — the utility at step i is the reward at step i, plus the discount times the utility at step i + 1. Okay. So, at this step, I have the discount is 1, so I have essentially minus 2 plus 0. And then, I'm going to update `sum_utilities` with the utilities I've seen. And then, remember, I update this counter, to say I've visited this state-action pair once more. And then, so here,

**[55:57]** the reward is minus 2, so this is basically minus 2 plus minus 2, which is minus 4. Update the running sums. And then, finally, this reward is minus 1, plus minus 4. So, each utility at each position is essentially the discounted sum of all the rewards from that point on. Okay. So, this gives you an unbiased estimate of the utility at a particular state-action pair, if you were just rolling out the policy. Okay. So,

**[56:44]** when the episode ends, I'm going to reset the history. Because, when I reach the end state, then I just wipe out the history, and I can start over. Okay. So, now, once I have these Q-values, let's see what `get_action` does. So, before, I didn't see any counts, so I just followed the exploration policy. And now I'm doing epsilon-greedy, which means that, with probability epsilon, I'm going to choose the exploration policy, and otherwise, I'm going to choose the best action according to the Q-values. Okay. So, let's dive in to see what π does. So — remember, I have

**[57:30]** for each action, I'm going to compute the Q-value of that state and action, and I'm going to take the best one. So, how do I compute the Q-value? It's just looking at the sum over the utilities that I've seen at that state-action pair, and dividing by the number of times I've seen that state-action pair. Okay? So, I do that for all the actions, and I can just take the action that gives me the maximum Q-value.

**[58:15]** Okay. So, rewinding back, that's what `get_action` does. So, inside, I have Q-values, and the Q-values allow me to quickly go to a policy. So, let me try to flush out the rest of this. So, here's the environment. `get_action` — so, this π is going to be used as `get_action`, as well as this exploration policy. And then, this `incorporate_feedback` is

**[59:02]** going to be: whatever that feedback is, it's going to be updating these `counts`. So, actually, let me do it this way. So, I have these `counts` that I'm remembering, and the counts, if you normalize them, you get the Q-value. From the Q-value, you get the policy, which you can use to drive `get_action`. Okay? And notice that the MDP still exists. It's the same MDP, but I never have to explicitly try to estimate this MDP. And, just to maybe make this more symmetric, you can think about: when I have this MDP, I also can get a policy.

**[59:48]** So, I guess that's what I've been calling the exploitation policy. And either, in the first phase, I'm using exploration to get action, or I use this exploitation policy. And that `incorporate_feedback` goes to this MDP. Okay. All right. So, let me skip these other calls for now. Okay. Maybe I'll pause for questions.

**[1:00:33]** Yeah.

> **Student question:** Is the Q just another style of the MDP?

The question is: is the Q another style of MDP? It has information that's kind of like what you have in an MDP, but it's not — it's not an MDP. An MDP has transitions and rewards and all that stuff. You can think about it as maybe a compiled version of the MDP, that tells you what the value of certain actions are.

> **Student question:** So, how can it be better than the model-based one? The model-free one would be more efficient than the model-based one.

The question is: why is the model-free one better than the model-based one? This is actually a really interesting

**[1:01:20]** question. So, one — okay, so I'll give you one piece of intuition, which is that, if you think about it, often you might know what the right thing to do is, but you can't necessarily predict what's going to happen. So, if you think about the real world, right? There, the state is super complicated. And so, in general, it might be easier to just learn a policy that takes the current observations and just tells you what to do. Like, if you're playing tennis or something, you might have a policy to go and hit the ball, but you can't really predict everything that's going

**[1:02:06]** to happen in the scene. Now, one thing that's kind of — so, the Q-values, basically, and also π, you should think about these as essentially synonymous with each other, right? You can just quickly get π from Q. This just tells you what to do, essentially — or at least the value of each action. Whereas MDPs are more general and powerful: they tell you not only what to do — well, they don't tell you what to do, but they tell you, if you did something, what would happen. So, these days, a lot of people talk about world models. And the idea of a world model in the context of RL is that you actually want to go back and build

**[1:02:51]** the MDP, or some sort of model of what's happening in the world, because — even though it's harder — now that we have foundation models and better models of the world, that can actually, in the long term, give you potentially better results. But the model-free versus model-based is kind of an ongoing debate. So, there are cases where you want to do one versus the other. Yeah.

> **Student question:** I thought we were trying to estimate Q*, but it appears that it can be an average of the two things [Ed: unclear — "average of the two things"; the lecturer's reply confirms the question was about Q_π vs. Q*]. Was that first policy for Q*?

Yeah, that's a good point. So, you caught me on this one. So, we're not even solving the same problem here. I

**[1:03:37]** was trying to pull a fast one here. So, we are computing Q_π. And Q_π is the value of — so, what is Q_π? Let me just be very clear about it. Q_π(s, a) is the value if you take state s, and you take action a, and then follow π. Okay? So, this is not what you actually want. It helps you estimate how well you're doing. But, you know, later we're going to see how we can actually get to Q_opt. It's going to take two more steps to do

**[1:04:23]** that. Okay. So, let's simulate this MDP using this model-free Monte Carlo agent, and we get minus 9. And I think this relates to your point that — you know — you see that the value here actually isn't very good. It's basically in the exploration phase, and that's because we're not actually computing the optimal policy. So, the way you would actually use this is: you estimate the current policy. So, Q_π gives you some information,

**[1:05:08]** right? Because you can take the argmax over this, and it sort of gives you one-step improvement. So, this is called policy improvement, which we didn't talk about. And that gives you a slightly better policy. And then, you can try to estimate the value of that, and you can improve that policy, and so on. So, there is a path to improving the policy, if you can estimate Q_π. Okay. So, to summarize: model-free Monte Carlo estimates Q-values of the current policy. It directly uses rollouts, bypassing the need to estimate the MDP. And we're also using epsilon-greedy to balance exploration and exploitation.

**[1:05:53]** Okay? So, there's one problem here: in life, you only get one rollout. Okay? You go to the end, and you die, and then that's it. So, you can't really do model-free Monte Carlo in real life. So, now the question is: can you update the Q-values before the rollout is over? Right? The problem with model-free Monte Carlo is that you're going to be, you know, 100 years old, and you're going to look back across your life, and you're going to estimate the Q-values, and it'll be sort of too late. You kind of want to estimate the Q-values as you go. So, that's going to be this new algorithm called SARSA, and we're going to try to estimate the Q

**[1:06:40]** values as you roll out. So, this seems a little bit tricky, because — you know — if you don't roll out completely, how do you even get the utility? Because the utility is defined as going all the way to the end. So, the trick here is this idea called bootstrapping, which is common in RL: you combine your immediate reward, that you got, with a model estimate of the future. Okay? So, remember — let's see. Okay, I'm going to go over here.

**[1:07:26]** I'll talk about SARSA. So, remember, `incorporate_feedback` gives you state, action, reward, next state pairs, right? And so, you don't see everything. So, let's see if we can do something with that knowledge. So, in model-free Monte Carlo, we can compute a utility, and that's just going to be the discounted sum of the rewards. And the idea in bootstrapping is that we're going to take the immediate reward, and then we're going to substitute the rest with our current estimate of

**[1:08:12]** the rewards — γ times Q_π. Right? Remember, Q_π is essentially our current estimate of the value. Okay. So, algorithmically, what we're going to do is we're going to perform essentially a gradient update, to move our current estimate of Q_π towards this new piece of information we got. Okay. So, let's go through our favorite example again. So, you know, SARSA — we're going to keep track of these Q-values

**[1:08:57]** directly. And when you call `get_action` on SARSA, it looks kind of the same as before. If you haven't seen anything, you try the exploration policy. And now, `incorporate_feedback` — this is the core SARSA algorithm. So, the idea is that you have your state, action, reward, next state, and next action. That's kind of what a part of a rollout is. And what we're going to do is: take this next state, and we're going to simulate getting a next action.

**[1:09:43]** So, at this point, we have also an action that we sort of simulated. And now, hopefully, you know why this is called the SARSA algorithm. So, then, you can compute your utility as the reward, plus the discount times Q of the next state and the next action. Okay? So, remember, in Monte Carlo, all `incorporate_feedback` did was add to the rollout, and then, when I'm at the end, I can compute the utilities. But now, I'm going to just compute the utilities as best I can, immediately. And now, this update

**[1:10:30]** is going to be: take my current estimate of Q, and nudge it with some learning rate in the direction of utility minus Q. So, if the utility is equal to that, then I don't move it at all. And if the utility is greater than that, then I'm moving it towards the utility. Okay. So, once I incorporate the feedback, then I can — okay, in this case, I got unlucky and I'm just exploring as well. But, you know, let's see if I can see π. So, π is determined, in terms of Q, as

**[1:11:15]** simply the argmax over Q of state and action. So, it chooses the action that gives me the best Q. Okay, so let's try it out. Again, minus 9.6, which is around this kind of the same. And again — you know — the observation is that what I'm doing is estimating the Q-values of the current policy, just as you roll out. So, it's not going to do better; it just allows you to update sooner. We're able to do that by using bootstrapping — substituting part of the utility calculation with the actual model itself. And then

**[1:12:01]** to estimate the Q-values, we do these gradient updates. So, now, in the last minute, I'm going to try to get you to the optimal policy. So, this sounds familiar from last week. So, this is called an on-policy algorithm, because the Q-values that you're computing are of the current policy. But, what we really want to do is estimate the optimal policy. So, in order to do that, we need to have an off-policy algorithm, because the policy that we're following has to have some exploration, and we don't know what the optimal policy is. So, we want to estimate the optimal policy,

**[1:12:47]** but not follow it. Okay. So, that's going to be Q-learning. It's basically a few characters' change from SARSA. So, SARSA estimates the Q-values of the current policy. Q-learning estimates the Q-values of the optimal policy. So, everything's going to be the same here. So — actually, what I've done is — okay. So, actually, Q-learning and SARSA are basically the same algorithm. So, actually, in this implementation, I've subclassed

**[1:13:33]** — SARSA is now a subclass of Q-learning [Ed: as spoken — in the source, Q-learning subclasses SARSA]. So, let's see. `incorporate_feedback` — this is the part that's different. So, just to show you how similar they are — Q-learning inherits everything from SARSA, except I'm overriding `incorporate_feedback`. Okay? So, what am I doing here? The only difference here is that, now, I'm using `pi` instead of `get_action`. Okay? And `pi` represents, if you remember, the optimal policy given these Q-values. And

**[1:14:19]** the rest of the update is identical. Okay? So, what I'm doing — I should have had — so, let's see. So, let me do Q-learning. Here, in Q-learning, I'm updating Q*, and I'm doing an update: plus the learning rate, and then I have the utility, which is some reward plus γ times

**[1:15:05]** the value of — sorry, Q* — which is going to be the max over a′ of Q(s′, a′). And minus this Q*(s, a). And then, in SARSA, I have Q_π(s, a). I'm estimating Q_π(s, a), and this is going to be the reward plus the Q value of

**[1:15:52]** (s′, a′), minus Q_π(s, a). So, in the code, what you're seeing is that this is coming from, you know, `get_action`, which is basically the action that π — your policy — is giving you. And this is coming from `self.pi`. Okay? So, this is on-policy, and this is off-policy.

**[1:16:45]** Okay. I realize I was a little bit rushed, so maybe next time I'll try to review this a little bit more. Okay, so — maybe I'll just summarize what we learned today. So, reinforcement learning: we're learning the optimal policy by interacting with the environment, instead of being handed the MDP. It's kind of the contract here, that we have an RL algorithm that takes actions in the world, and incorporates feedback. We looked at four algorithms. First, you can just estimate the MDP, and then you can compute the optimal policy. You can estimate the Q-values directly from rollouts, using model-free Monte Carlo. You can use SARSA to do the same thing, but with bootstrapping, so

**[1:17:31]** you don't have to wait until the end. And then, finally, you can switch everything over to estimating the Q-values of the optimal policy. Okay, so next time, what we're going to do is consider the case of huge state spaces. So, here, we've assumed the number of states is small, because we're just indexing — computing the value for every state. But, in a much more realistic setting, you can't see all the states, and then what do you do in that case? Okay, that's it for today. I'll see you next week.
