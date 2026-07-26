---
title: Lecture 4: Learning III
lecture: 4
video: https://www.youtube.com/watch?v=89NND-Ca0yY
source: edited YouTube transcript — see original/ for verbatim captions
edited: true
---

# Lecture 4: Learning III — transcript

Edited from auto-generated captions. Punctuation, capitalization, and technical terms have been corrected. Filler words and false starts have been removed. Student questions are marked. The verbatim original is in `original/04-learning-iii.md`.

**[0:05]** This lecture is going to be about deep learning. Last time we started talking about linear classification, and we've also done linear regression in last week's lecture. The goal is to do nonlinear regression and classification, which is going to give us more powerful machine learning methods. Here's the agenda: I want to start transitioning us to PyTorch. We're going to talk about the motivation for nonlinear methods, then go through basic multi-layer neural networks, and then talk about all the tricks you have to be aware of when doing deep

**[0:51]** learning. Let's start with the basics.

So far, what we've done: we are hopefully fairly comfortable with NumPy at this point, which gives us a way to create tensors and do operations on tensors. And we showed you how to build our own computation graph library and implement backpropagation, which allows us to not only compute forward with tensors, but also compute backwards with the gradients. We did this not because you would actually do this in your life, but to really understand what's going on under the hood. If you remember, we constructed the computation graph, and it wasn't that bad. In

**[1:36]** practice, you would use a standard library such as **PyTorch** or **JAX**, which is going to be much more efficient than what we've rolled out by hand, more robust, and also implements many of the common operations you'll want to use. Implementing things from scratch is a good way to build understanding, but if you're trying to get work done, you should definitely use PyTorch or JAX.

So let's make this transition. Conceptually, you should already understand — some of you probably have already worked with PyTorch — but you should understand all the pieces. It's just a bit of syntactic and convention adjustments. Remember, when we rolled our

**[2:22]** own library, we were able to create nodes in the computation graph. Each node has a name, a value associated with it, and a gradient. In the forward direction — we have this graph depicted: we have x and y, and they're combined (via dot product) to produce z. Then in backprop, we go and update the gradients of x and y.

The way you would do this in PyTorch

**[3:08]** is as follows. You call `torch.tensor` instead of creating a node. `requires_grad=True` just means "this is a node that I want to compute the gradient of." By default, up here, because this is simple, we assume `requires_grad` is true for every node, but we can add that feature if you want. y is another tensor. You take the dot product. And when you do `z.backward()`, you get — stuffed inside x and y — the gradients of x and y with respect to z.

> **Student question:** Why is there a dot after these entries (e.g., `1.` vs `1`)?

**[3:56]** `1.` is a floating-point number; `1` is an integer. When I write `1.`, this is treated by PyTorch as a float vector, and you can only take gradients if it's going to be floating-point. If you try removing the dot, it'll throw an error.

So this is the same computation graph, in PyTorch. If we reflect on what's different and what's the same: **`torch.tensor` — you really should think of these as nodes in the computation graph.** By design,

**[4:43]** `torch.tensor` looks like a NumPy array and you can just do operations on it, but it's really conceptually important to think of these as nodes in a graph rather than just values. Then it will be less mysterious, and if things go wrong, you'll hopefully understand why. PyTorch has done a nice job making this transparent. You can add, multiply, take dot products — similar to what you'd do in NumPy. It makes it seem like you're operating on NumPy values, but again, think of these as nodes in a computation graph. There are some minor naming differences — it's like

**[5:28]** learning a slightly different language — but there's no underlying conceptual difference between a `torch.tensor` and an `np.array`, aside from the fact that they're nodes.

Notice that values are computed eagerly during construction. The moment you do the dot product, forward is essentially called — whereas in our library, we could have done that as well, but we decided to delay the forward computation until backpropagation, when we do forward and backward — just to make it explicit that you can construct a graph without actually evaluating anything. In some other frameworks,

**[6:15]** this is actually useful because you can construct a graph and have a compiler manipulate it and make it more efficient before you actually operate on it. But that's for another day.

We call `.backward()` here on the root node to backpropagate gradients throughout the entire computation graph — to the nodes that have `requires_grad=True`. Whereas before — again, this is just convention — `backward` operated on a single node, and `backpropagation` was the thing that actually calls backward on each node. As I mentioned before, you

**[7:00]** specify `requires_grad` on the nodes that you want to compute gradients for. In general, you have a computation graph. It's going to munch parameters and data together. As a general rule, you want `requires_grad` on the parameters, not the data — unless you're trying to find adversarial examples or optimize over your data somehow, then you want gradients on your data as well.

One thing I'll mention — and this is a subtlety to make sure you understand — is that whenever you have a node, you can use it in two ways. You can use a node directly — that's what we've seen: you take a node, build on top of it, build a larger graph. Or you can just pluck out the node's

**[7:46]** value and use it for some downstream computation. In the latter case, the values aren't going to backpropagate through the node — it's like you copy the value out.

To show what I mean, I'm going to construct a simple example. I have x as a node. y = x². z = y². Here I'm noticing: I take z = y (the node) squared. But I can also do something like this: I take y's value and create a separate node, which is detached; then I square that, add it to u, and

**[8:32]** get L2. In the first case, z builds directly on y. In the second case, I've essentially copied — taken y out — and it's just a new node whose value is y's value. I'm writing it like this to make it very transparent what's happening here.

What happens when I do backprop now? When I do backprop on L2, it's only looking at the descendants of this graph here. So

**[9:21]** x's gradient actually doesn't get affected — even though x has been involved in this computation, I've deliberately taken only y's value, not the identity of y. You can think about this as pass-by-value vs. pass-by-reference in programming languages. `u.grad` is computed when I do backprop, but `x.grad` is not.

In PyTorch, what this looks like is a little bit more subtle because, in this very verbose language, you

**[10:06]** could see what you're looking at — a node vs. its value. Whereas in PyTorch, the two notions are deliberately minimized. In PyTorch, you use the tensors directly as values — you don't do things like `x.value`. So how do you actually do this detaching? Well, let me show you this example. Suppose we built up `y = x²`, `z = y²`. Here's the way you would do it in PyTorch: you'd say `y.detach()`, which

**[10:55]** essentially detaches y from the previous computation, and then you can build up computations on top of that. That's identical to doing this. You take y, detach it from x, and use it. In this case, again, `u.grad` is populated but `x.grad` is not, because y has been cut.

Why is this useful? Because sometimes you just want to compute values forward and have control over whether you are computing gradients

**[11:41]** or not. For example, if you train a model and you're now serving it to customers at test time, you don't want to be updating your parameters. So there's no reason to compute any gradients. In that case, you would use a context called **`torch.no_grad()`**, which basically says: "In this context, I'm just going to compute forward. I'm telling PyTorch: I don't care about gradients — don't keep track of anything. Just treat them as values." This is good because it reduces the overhead, and it's also

**[12:29]** safe in the sense that if you try to backprop through it, it'll throw an error saying that you don't have the graph anymore. PyTorch uses a thing called a `grad_fn` — the equivalent of our dependencies — and it says, "I don't know how to backprop because I don't have the graph."

> **Student question:** Can you explain this in terms of the graph?

**[13:16]** When you have this — this is basically the equivalent of detach. I take y's value and I'm just using it instead of putting y. If I put y here, then I'd be using the actual node, and y would connect to this computation. Instead I've detached it — I create a separate node that only takes y's value, and I'm using that instead.

That's all I'm going to say about the basics of PyTorch. Let's start using it and get more intuition for how it works.

I'm going to do linear models, just as a review but using PyTorch.

**[14:02]** The nice thing about PyTorch: it has a bunch of built-in stuff like models, loss functions, and optimizers. That'll make it easier to do things.

I'm going to start with one example. I have input [1, 2, 3, 4], and I'm doing multiclass classification. So the target y is going to be the one-hot representation of the label — this label is 1 because position 1 is set. I'm going to define a linear model. PyTorch has this built-in module called `nn.Linear`,

**[14:50]** which takes the input dimension and output dimension. You can think of it as basically a weight matrix multiply plus a bias term. Using that module, you can call it like a function, and that gives you logits. I've taken a 4-dimensional vector, multiplied by this weight matrix, added this bias, and gotten the logits. We did the exact same thing last time, but now using PyTorch.

If you remember, that's what we did in linear classification: take the input, multiply — for every class you have a weight vector

**[15:36]** and a bias — multiply, add, and get logits. Then last time, remember, we talked about the loss function — in particular the cross entropy loss. Now, this cross entropy loss — the notation/terminology: the arguments are different than what we had before. Usually if you see cross entropy, it's target distribution, predicted distribution. In this case, this loss function takes the predicted logits and the target distribution — so it's flipped. Also, you're passing in logits directly instead of

**[16:21]** the softmax version of the logits. Nonetheless, it's a function — it returns a number — and you can call `.backward()` on this, and that produces gradients for all the model parameters. There are two tensors inside this model: `model.weight` and `model.bias`, and those have gradients that are computed. Then, using these gradients, you can define an optimizer — which we also did by hand. This is SGD, which stands for Stochastic Gradient Descent, but here

**[17:08]** we're actually using it like gradient descent, because all we're doing is computing a gradient and taking a step — and here the gradient is over the entire data set (well, just one example, but conceptually the entire data set). When you call `optimizer.step()`, basically this reaches into the model weights and updates them based on the gradient.

To review: `.backward()` sets the gradients on the model parameters, and then `optimizer.step()` takes these gradients and applies the update to the actual model weights.

> **Student question:** Can you pass the number of steps?

**[17:54]** `.step()` just takes one step. If you want to take multiple steps, you wrap it in a loop — which I'll show you.

All right, I think we have all the pieces. Let's complete the full training loop. I'm going to load my data set of three examples here. I'm going to call this function I've written, `train_model`. `train_model` takes a generic `nn.Module` — basically any function that takes some inputs and produces logits (not a loss, just logits) — and tries to learn

**[18:42]** this model. The first thing I do is stack these x's into a matrix and stack these y's into a matrix as well. This is the natural way to operate in PyTorch, because you always want to batch things into tensors for efficiency — even though I find it much more natural to think of x and the corresponding y grouped together.

I have the cross entropy function, which is also a module you can call. Then I instantiate the optimizer. By default,

**[19:29]** the optimizer is SGD, but I could also pass in Adam if I wanted — these are essentially drop-in replacements. Once you set this up, you can play around with different versions. The optimizer takes a learning rate (how fast you move) and also `model.parameters()`, which — as we'll see — can be a fairly complicated thing. It recursively goes through and gets all the tensors and puts them there.

Now I'm going to step through the code. Each step, I take a **forward pass**: compute logits,

**[20:15]** then loss — remember, cross entropy loss applied to the logits, which is essentially the prediction against the target. Then in the **backward pass**, I'm going to zero out the gradients and apply `loss.backward()`. Remember that each time you do this, you have to zero out the gradients — PyTorch doesn't do this for you. This is a feature, not a bug, because sometimes you iterate over a bunch of examples and call `.backward()`, and that is meant to accumulate gradients over multiple examples. But here, you don't want to accumulate gradients over multiple iterations of training, so you need to zero

**[21:01]** it out at the beginning or end of each step. `loss.backward()` populates the model parameters with `.grad`. Then I call `optimizer.step()`. And then if I look at the parameters — the parameters are updated accordingly.

This is basically what I went through, just one step. Typically, every machine learning pipeline essentially looks like this: you do inference to figure out the predictions (forward pass),

**[21:48]** backward to get the gradients, and then update the parameters. Now we can go through — step two, different parameters — and keep doing this. I'm going to skip forward because I don't want to count up to 80. But you can see as training proceeds, the loss goes down. The logits are also changing. You can see that if you imagine exponentiating and normalizing, the logits are meant to match the labels. If you take the softmax, this is putting a lot of probability mass on the first class — matching the label.

**[22:34]** The loss is going down. The logits are matching the labels. The weights are changing — you can't easily understand the weights just by looking at them, but we'll keep going.

Let me jump out — over a number of steps, the loss is going down. Whenever you're doing machine learning, you monitor the loss and make sure it's going down. If it's not going down, you probably have a bug.

> **Student question:** What is the difference between `optimizer.step()` and `.backward()`?

**[23:21]** `loss.backward()` populates the gradients inside the parameters. `optimizer.step()` takes these gradients and updates the actual parameter weights. For every step, you're always looking at multiple quantities: there are the parameter weights, and then there are the gradients. `.backward()` computes the gradients; `optimizer.step()` uses those gradients to update the weights.

> **Student follow-up:** Isn't it wasteful to keep all the gradients around?

**[24:09]** Yes, there are things you can do to optimize this. When you compute the forward pass, you compute all the values. On the way back, as you compute gradients, you can throw them out — once you've computed a gradient in the backward pass, you don't need it anymore. In general, if you're doing normal things, it's a bit hard to make sure you're doing this right, and it introduces complexity — which for this first lecture I'm not going to do. If you're training very big models and you're compute-constrained,

**[24:56]** you can do fancy tricks. There's also activation checkpointing, where you throw away activations (which also take a lot of memory) as you compute forward, and you can save memory that way.

So we've trained a model successfully. Some things to call out: PyTorch makes it fairly modular. You can define a model — in this case, a linear model. A model takes input tensors or vectors and produces logits. The logits correspond to the strength of prediction for every class

**[25:42]** (in the multiclass case). You can use a loss — in this case, cross entropy loss — and that takes logits and targets and maps them to a scalar loss. Then you can look at optimizers, which update the parameters using the gradients. You have this menu of options, which essentially corresponds to the three questions we start with in machine learning: What is the design space of models/predictors? What is the loss function? What is the optimizer? For each of these, you can choose your own adventure.

That is a bit of PyTorch and

**[26:29]** hopefully a review of the previous material in PyTorch land.

Now I'm going to move on to the core part of this lecture: let's tackle **nonlinear regression and classification** — actually, mostly classification. You can look at these slides from a previous quarter if you like. So far we've looked at linear classifiers: remember, these classifiers are predictors, and you can visualize a predictor in terms of a decision boundary. A predictor cuts up the space into two parts (in binary classification): a part

**[27:15]** of input space where the predictor returns positive, and a part where it returns negative. If you think about the set of all linear predictors, you're limited to classifiers that essentially have straight cuts through the space. Or in linear regression, your predictors all look like lines as well — mapping some input to some output.

But sometimes your data might look like this (e.g., concentric circles). In that case, it would be strange to fit a line. I mean, you could fit a line here, but that doesn't seem intuitively a wise thing to do — at least you're not going to get a very good fit. In this case, we need **nonlinear**

**[28:02]** models to create a larger set of predictors.

What should we use? There are actually a lot of things you can do. For classification: decision trees, nearest neighbors, neural networks. But it turns out that **even linear models can do the job**. You're probably thinking, "Wait — that seems like an apparent contradiction. How can linear models represent nonlinear decision boundaries?" There's a trick here, and it's a cool trick. Suppose you wanted to define some nonlinear classifier — we'll call it

**[28:48]** a quadratic classifier. It classifies everything outside a circle as positive and everything inside as negative. The way you define this is: suppose I have input [1, 1] — right in the center. What the classifier does is compute a logit: (x₀ − 1)² + (x₁ − 1)² − 2. If you put in [1, 1], you get −2 — negative logit → predict −1. If you put in [3, 0],

**[29:34]** that's outside the circle: (3−1)² + (0−1)² − 2 = 4 + 1 − 2 = 3 — positive prediction. Everyone understands what this quadratic classifier is doing.

Now clearly, this is not a linear function. But here's what I'm going to do: I'm going to define a **nonlinear feature map** first — a transformation that takes x into something: [x₀, x₁, x₀² + x₁²].

**[30:19]** And now we're going to define a **linear predictor** on top of this. The way it works: when I get [1, 1] in, I first compute this feature map — giving me [1, 1, 2]. And now with this φ(x), I define a linear function on top: −2φ₀ − 2φ₁ + φ₂.

**[31:05]** −2 − 2 + 2 = −2 — exactly what we had before. That predicts −1. If you take [3, 0], the feature map gives [3, 0, 9], and the logit is −6 − 0 + 9 = 3 — which is +1.

All I've done is rewrite this apparently very nonlinear classifier in terms of a feature map that is definitely nonlinear, plus a linear map

**[31:52]** on top. In other words: a **linear classifier in a higher-dimensional feature space** leads to a **nonlinear classifier in the original space**.

What this means: we now have a very, very simple procedure. If we're lazy, we say: "Take our data, preprocess it with a feature map. Define some feature map. Transform the x's into φ(x). Then hand it over to the part of the code that just knows how to deal with linear predictors — and we're done." This is a super lazy way to do nonlinear classifiers. You don't actually have to write

**[32:38]** any new code, really — except for your feature map.

The reason I'm saying this: I think a lot of people, when they think "nonlinear classifiers," they jump immediately to "I need to train a deep neural net." Well, it turns out you don't have to. Depending on your problem, something as simple as applying a nonlinear transform to your data might suffice, and that's worth remembering.

Now of course, we are going to talk about neural nets, because there is a limitation: the feature map is **fixed**. The question is: how do you design your feature maps so that in the feature space things are linear? That's sometimes clear

**[33:24]** but not always. So one might wonder: can we actually **learn** the feature map as well? Because the idea of machine learning is you're being lazy — "I don't know what the feature map is; let the learning algorithm figure it out" — just like "I don't know what the weights are; let the learning algorithm figure it out."

> **Student question:** What kind of transformations do you typically apply?

This feature map takes a 2-dimensional vector and returns a 3-dimensional vector. In general, you can return 2-dimensional, 1-dimensional, 100-dimensional. If you've heard of kernel methods — actually the feature map is infinite-dimensional, and there are

**[34:10]** mathematical tricks that allow you to not actually take infinite time or memory. It's a pretty interesting, powerful framework.

Let's move on. We're motivated — we know how to create some nonlinear classifiers, but we want to **learn the feature map**. What we can do is define a **two-layer network** where the first layer is the feature map and the second layer is the linear predictor. I'm going to talk about two-layer neural nets. The way you should conceptualize this: the first layer is a feature map, and the

**[34:56]** second layer is the linear predictor. I start with some training data — 4 dimensions going down to 3 classes. I'm going to define a module called `LinearMLP`.

The way modules work, if you haven't seen this before: you define a class and set member variables to be the sub-modules, which themselves have parameters (or you can have parameters directly). `w1` is the linear map from input to hidden. `w2` is the linear map from hidden to

**[35:42]** the output logits. You can visualize: I have `w1.weight`, `w1.bias` and `w2.weight`, `w2.bias`. The dot notation gets automatically constructed by PyTorch — that basically corresponds to the hierarchy of parameters.

Now, I have this model. I can call it on the first data point, and that calls the `forward` function. I apply some transformation to x to get a hidden dimension —

**[36:28]** I've set hidden dimension to 5 — and then map the hidden dimension to logits via another linear transformation, and return the logits.

Now I have this model. I'm going to train it. I'm not going to step through it because we already did that once for linear — it's the same. I get this nice plot.

> **Student question:** What is the role of the hidden dimension?

Hidden dimension — it's just a name. It's called hidden because it's not the input or output, which are the only things you observe. You're not given the hidden dimension. But you can think of it as the analog of φ

**[37:14]** from before: we have the feature map that takes x and produces a vector, and then another linear predictor on top of φ to produce logits. Here I've renamed it "hidden" because we don't have it ahead of time.

So I've trained this model — seems to work great. But does anyone see something weird about this? Something is missing, especially if you've seen neural nets before. My claim is: **this is actually the same as training a linear**

**[38:01]** **classifier.** It looks like I'm doing something fancier, but it's actually just a linear classifier. The reason is because **matrix multiplication is associative**. If you have input x, first layer weights w₁, second layer weights w₂ — remember, logits are (x·w₁)·w₂. But we know from matrix multiplication that you can group w₁ and w₂ together into a single weight vector: x·(w₁·w₂) — and you get the same thing.

**[38:48]** If you interpret it like this, all this is doing is taking a dot product with a single matrix — that's a linear predictor. (There are bias terms I'm not showing, but it's the same.) Now you're parameterizing the linear predictor in a somewhat funny way — instead of a single matrix, you have two matrices — but it's still essentially a linear predictor.

One caveat: this requires the hidden dimension to be at least as large as

**[39:33]** the input and output dimensions. Otherwise, it's not exactly the same — you'd be restricted to low-rank matrices. But in this case, it's just a linear predictor. So just a caveat: when you have a really complicated model, it might actually be something simpler.

So we actually didn't make much progress. No wonder this kind of looked like the same plot. How do we actually go beyond linear classifiers? Well, clearly we need to put something **nonlinear** in there — because if you just have a bunch of linear things, it's just going to be linear.

**[40:22]** Linear networks are not more expressive (by definition — that's a tautology). Although interestingly, people still study deep linear networks because the training dynamics are interesting — mostly for theoretical purposes. But what we need to do is make things more expressive by adding a **nonlinear activation function**. What is an activation function? It's basically a function that maps a number to another number — that's not linear. That's literally the only requirement.

**[41:07]** There are many choices: sigmoid, tanh, **ReLU**, GeLU, Swish, etc. We're going to focus on ReLU just for simplicity, although it's not necessarily the best one. ReLU stands for **Rectified Linear Unit**. ReLU takes a number — actually, you can pass in a vector and it applies element-wise — and takes the max of that number and zero:

$$\text{ReLU}(x) = \max(0, x)$$

What it looks like: if x is less than zero, output zero. If x is greater than zero,

**[41:53]** return the identity — the same number. This is clearly a nonlinear function.

As an aside: where does the name **multi-layer perceptron (MLP)** come from? Perceptrons come from Frank Rosenblatt's 1958 paper, which essentially introduced linear classifiers. In the 60s, 70s, and 80s, people stacked perceptrons into multiple layers — that's why they're called multi-layer perceptrons. Neural networks are another name for the same thing.

So let's stick this ReLU in and see what happens. I have my

**[42:39]** data, and I'm going to define a model called `MultiLayerPerceptron`. Notice it has the exact same parameters as the linear version — it still has w₁, w₂ — but the difference is: when I call the model, I first compute a transformed version of x by applying the first linear layer, and then I apply a **ReLU** to produce a set of hiddens. You'll notice that any of the negative

**[43:24]** pre-activation values get zeroed out, whereas the others are still there. Then I apply w₂ to get the logits. All I've done is stuck a ReLU in there. It seems like a small thing, but that makes all the difference — now you have something that's truly nonlinear and much more expressive.

A few notes on terminology: **activations** — generally in neural net speak — correspond to the values of the intermediate

**[44:11]** values that are computed in the forward pass. They're also called hidden units or sometimes neurons. It all refers to intermediate computations of the forward pass.

One thing I want everyone to be paranoid about: ReLU does have a zero gradient when x ≤ 0. In this region, if you're out here, the gradient is zero. And hopefully we've been burned once by this already — with the zero-one loss! Every time you see a zero gradient, you should think carefully about whether that's okay or

**[44:58]** not. Normally, this is okay because the other part (x > 0) is not zero. But there is a problem where, if a particular neuron is always on the negative side for every single example, it's just going to stay zero. This can result in **dead neurons** — parts of your network that aren't doing anything. That's bad because you're wasting model capacity.

There are a bunch of fixes. The most obvious: don't make the gradient zero. There are things like **Leaky ReLU**, **GeLU**, **Swish** — that essentially bend this a little bit: slide it down, round it out.

**[45:44]** One note: I said "zero gradients" — but **near-zero gradients** are just as bad. Remember the sigmoid function: it is very flat when you go far from zero. Even though nowhere in the sigmoid is the gradient exactly zero, it can get pretty close to zero,

**[46:30]** which means you're not moving — or not moving very much. That's also a problem. It's not literally zero that's the problem — it's being very close to zero.

In some sense, the name of the game for activation functions is balancing the tradeoff between **linear** (which has better gradients) and **nonlinear** (which gives expressivity). For example, you might wisely say, "Well, this is zero, so why don't I just make it y = x?" That has a great gradient — always 1. But then you're back to a linear model, which doesn't help. So that's why ReLU looks like this: you get the nice

**[47:16]** linear region (x > 0) which allows good gradient flow, but you need to put in some nonlinearity (the zeroing out for x < 0) to get expressivity.

Let's train this model. The loss function is going down. You'll see it's maybe going down a little slower, because neural nets generally take longer to train than linear classifiers — but nonetheless, it seems to be working.

So the summary: we've introduced two-layer neural networks or multi-layer perceptrons. You take your input → linear layer → activation (ReLU) →

**[48:02]** another linear layer → logits. Notice that we're calling the same `train_model`, and we can put in basically any function here. PyTorch will do its whole thing: compute the gradient, compute the cross entropy loss, and train the model. That's really nice — you can brainstorm different models and use the same training code.

Now let's move on to **deep neural networks**.

The problem is: maybe you're not even satisfied with a single MLP layer. There's a theorem that says even

**[48:49]** a single two-layer neural network — if you have infinite hidden neurons — can represent any function you want. But you might need a lot of hidden units. The solution: just stack multiple MLP layers. More helpfully, the intuition is that **each layer learns more and more abstract features of the input**. This is a story people tell — there's nothing rigorous or mathematical about why it learns more abstract features — but empirically, this seems to be borne out, and it helps us understand what these models are doing. In computer vision,

**[49:35]** your input might be just a set of pixels. If you have a deep neural net, empirically you see that the earlier layers pick out edges, then object parts, then objects, and so on. It's actually pretty cool that these models — just trained on some task — actually learn these fairly abstract features.

This is why I said we're learning the feature map. We started out by hand-coding feature maps. People in computer vision and NLP used to hand-code a lot of feature maps. The idea with deep learning is that you can actually learn

**[50:21]** the feature map directly.

Mathematically, we're composing multiple MLP layers. We're going to define a model — a deep neural net — and here, I should have done it a slightly different way, but anyway: you stack two MLP layers on top of each other. You do the forward: apply the first layer, the second layer, return the logits.

When you train

**[51:07]** the model, you get something like this. You notice that sometimes this curve doesn't look as nice as before. In particular, the loss isn't going down as fast in the beginning. This is typical as you get larger neural networks — slow learning might be a problem. You can think about it as: you're asking the model to learn a lot. I think you all take it for granted that we can just do gradient descent on some deep neural network and it works — but for decades, people thought this was essentially impossible.

**[51:53]** There's a problem with deep neural networks we should talk about: the **vanishing/exploding gradient problem**. For a long time, people struggled to train deep neural networks, and the problem has to do with what the values of the gradients are. I'm going to look at a 1D problem. Suppose you have input x = 1, and a weight initialized to 0.5. Then you have a bunch of layers — this is linear, but if you put nonlinearities, it's the same idea. Say 20 layers. You see that the x's get really, really small. If you

**[52:40]** compute backwards, the gradient is not identically zero, but it's very, very close to zero. Remember: gradient zero = bad. If you have a gradient very close to zero and a deep network, your update is going to be really, really small, and you're not going to make progress. This is the **vanishing gradient problem**. Deep networks are essentially a bunch of matrix multiplies. If you multiply the same number 20 times — if it's less than 1, it goes to zero.

On the other hand, you have the **exploding gradient**

**[53:25]** problem, where if your weights are initialized to 2 and you do this over and over, you get something ridiculously large. If you try to take a gradient step with that, you're going to move too far, overflow, and things go to infinity. That's no good either.

This simple example illustrates the core problem with deep neural networks: each layer is essentially multiplicative — you're multiplying something over and over. The only thing you can multiply a lot of times and not have things blow up or go to zero is: **1**.

**[54:12]** So ideally, you want w close to 1. It's not that bad — you're not multiplying an infinite number of times. If you have 20 layers, maybe w = 1.01 is okay. This is for the scalar case. The matrix case is the same idea, except instead of talking about magnitude, you have to think about eigenvalues of the matrix — but we'll save that for another day.

So that's the problem with deep networks. Now what I'm going to

**[54:57]** talk about are things one can do to cope with this problem. At some level, this is fundamental — in deep learning, you're really pushing yourself to the limit of what you can learn, and you're always at the danger of falling off a cliff in some sense. But there are things you can do to help.

The first is the idea of **residual connections**. It addresses vanishing gradients — not exploding gradients. The idea has shown up in multiple places since the beginning of deep learning: LSTMs for sequence modeling, residual networks for

**[55:43]** computer vision (this is probably the most popular version — that's why the name "residual connections" has stuck). In the context of transformers, they're also called skip connections and highway networks.

The basic idea is very simple. Normally, each layer of a network applies some function to its input: x → f(x). The idea with a residual connection: you're just going to **add the input to the output**: x → x + f(x).

**[56:29]** That's why it's also called a skip connection — there's a path that sort of skips f. The reason you want to skip f: if f has a zero gradient, and you don't have this skip path, your gradients are multiplicative (chain rule), and everything else gets zeroed. But here, you have an escape hatch — even if f is messed up and has zero gradients, your gradients can still flow backwards through the skip connection.

**[57:14]** Another way to think about it: if f is just multiplication by w, and we were worried about w being too small — in this residual setup, each layer computes x → (1 + w)x. So even if w is very close to zero, this quantity is close to 1. And remember, 1 is our friend. We want to keep things close to 1. Now, of course, if w is a million, we still have problems — but we can deal with that separately.

Let's try

**[58:00]** to train a model — DNN with residual. I'm just going to add a residual connection. It's a very simple thing — just adding like three characters. The result is that the training improves quite a bit. Before, if you remember, the training curve wasn't so great without a residual connection. Now, with a residual connection, training is a lot faster.

> **Student question:** Why does the loss function look linear?

**[58:47]** Part of it is that this data set is very simple — there are three points and I have something like 20+ parameters. In some sense, it's overkill. The actual function is probably different. But a lot of loss functions kind of look like this if you're doing things properly.

Let's look at **layer normalization**. The second idea is normalization — because, remember, 1 is our friend, and normalization makes things 1 or close to 1. We don't want magnitudes to grow too

**[59:32]** big or too small. Normalization shows up in deep learning — in batch norm for computer vision — but that's annoying to deal with for various reasons. So there's a simpler idea called **layer norm**.

The basic idea: take an input x (a tensor). Compute the mean, compute the standard deviation, and then standardize — subtract the mean, divide by standard deviation:

$$y = \frac{x - \mu}{\sqrt{\sigma^2}}$$

If I apply this to [1, 2, 3], it shifts it down. If I have something

**[1:00:19]** a lot bigger — normally this might cause trouble because these are fairly large numbers, and if you see large numbers like this when learning, something is probably blowing up. But if you apply layer norm, you get the same output regardless of input scale.

The **real LayerNorm** adds a few bells and whistles:

$$\text{LayerNorm}(x) = \gamma \cdot \frac{x - \mu}{\sqrt{\sigma^2 + \epsilon}} + \beta$$

First, you have **ε** (epsilon) — anytime you're dividing, you should be scared of dividing by zero.

**[1:01:04]** Then you add some scaling parameters (γ) and shifting parameters (β) to give it a bit more expressivity. γ is the scaling, β is the shifting. Normally, you initialize γ = 1 and β = 0, which

**[1:01:53]** reproduces the original normalization — but then you let γ and β be learnable parameters. That gives you a bit more expressivity.

In PyTorch, there's also a `nn.LayerNorm` function you can call. It has `weight` instead of γ, and `bias` instead of β — but you apply this layer norm to your input and get something much more reasonable. The way you use layer norm: you just stick it in. Exactly how you stick it in does matter — for transformers — but I don't have

**[1:02:40]** time to go through that. The idea: layer norm keeps the magnitude of activations away from zero and away from infinity.

Another thing I want to talk about is **initialization**. Again, we don't want magnitudes of activations to be too big or small. At the very least, when you start out — if they're too big or small — we should just stop and fix that problem. Initialization of your network is actually pretty important. Suppose we have — I'm deliberately making this a large network because that's where problems generally arise.

**[1:03:25]** You have a 16,384 × 32 weight matrix, and your input x. Even for linear models, you can have a problem. What happens when you multiply this matrix by this vector? You get something with pretty large values — numbers in the hundreds. If you see values like this, you should probably go and address it.

Why is this? For

**[1:04:13]** every output, you're taking the dot product of two input-dimensional vectors. The input dimension is 16,000, which means you're adding 16,000 standard normal variables. If you remember your probability, the magnitude scales roughly as √(input_dim), which can be quite large — and again, that can be unstable.

What you want is an initialization that's **invariant to the input dimension**. You just divide by √(input_dim), and things will be okay. Now these numbers look good.

**[1:04:58]** If you see numbers like this, you can sleep at night.

This is essentially **Xavier initialization** [Glorot and Bengio, 2010]. If you're super paranoid — you know that in a normal distribution, there's some tail probability of getting a fairly large value — and if you want none of that, you can truncate the normal at, say, −3 and +3 to avoid any chance of outliers.

**[1:05:44]** Another thing you can do: look at initialization and make sure you're dividing by √(input_dim) so that any activations are reasonably controlled.

Finally, let me talk about **optimizers**. This part — I was going to talk about Adam, but I don't think we have time, because Adam is another way to keep your training stable. Instead, I'm going to talk about the difference between **stochastic gradient descent** and **gradient descent**.

So far we've done gradient descent: pick up all your training examples, compute all the gradients, sum/average them, take a step. But if you have 10 million examples,

**[1:06:31]** you're doing a lot of work just to create one update. Instead, we should use a **stochastic optimizer**, where each step you take a random subset (mini-batch) of examples, compute the gradient, and update on that instead. It's called stochastic because in expectation, taking a random subset gives you an **unbiased estimate** of the actual gradient.

Let's do an example. Suppose I have four points, and I've computed the gradients: [1, 2], [3, 4], [5, 6], [7, 8].

**[1:07:17]** If doing gradient descent, I average all of these and get a gradient of [4, 5]. If doing stochastic gradient descent, you typically have a mini-batch size. Suppose I set it to 2. You choose two indices at random — say, 1 and 3 — giving you this batch of gradients: [3, 4] and [7, 8]. Average those: [5, 6]. Notice: the stochastic gradient isn't the same as the actual gradient, but

**[1:08:04]** it's somewhat close. If you average over multiple trials, it will be — in expectation — the actual gradient. You could do this every step: sample a batch, update. That would be stochastic gradient descent.

One problem: sometimes you might get unlucky — a data point you don't even look at, or duplicates (with replacement). So what you typically do is sample **without replacement**. Implementation-wise: you permute the training examples

**[1:08:49]** at the beginning of every epoch (a pass over your examples). Take a random permutation, group it into batches. Because it's random, you can just take consecutive indices in this array. The stochastic gradients — for every batch, you have a stochastic gradient. And if you look at the mean of the stochastic gradients, you actually get the real gradient.

Notice that the optimizer —

**[1:09:40]** it's called SGD, but it's actually agnostic to where the gradients come from. If your gradients come from a batch of examples, you're running stochastic gradient descent. If your gradient represents all examples, you're doing gradient descent.

Separately: you can read up about this and experiment. If you replace the optimizer from SGD to **Adam**, it's a one-line change. You can play around with different learning rates — you'll see that, in general (but not always), your learning curves are going to be better, the loss going

**[1:10:26]** down faster, and so on.

To summarize: **PyTorch** gives you essentially NumPy plus automatic differentiation (via `.backward()`) and a bunch of predefined modules like `nn.LayerNorm`, `nn.CrossEntropyLoss`, `nn.Linear`, etc. We talked about introducing multiple layers — nonlinear layers — to get more expressivity. The intuition: you're learning your feature map. And then you have to do a bunch of things to make sure you can actually train these networks:

- Choose activation functions to avoid dead neurons.
- Use **residual connections** to make sure you don't have vanishing gradients.
- Use **layer normalization** to make sure your activations aren't blowing up.
- **Initialize**

**[1:11:13]** properly to make sure activations are not too large or too small out of the gate.
- Use better **optimizers** (like Adam).

That was a whirlwind tour of deep learning. Hopefully you have some intuitions, but at the end of the day, it really takes time and experience to develop an understanding of how to train neural networks. That's it. See you next time.
