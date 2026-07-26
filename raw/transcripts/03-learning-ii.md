---
title: Lecture 3: Learning II
lecture: 3
video: https://www.youtube.com/watch?v=Mbe5ICIUw5Q
source: edited YouTube transcript — see original/ for verbatim captions
edited: true
---

# Lecture 3: Learning II — transcript

Edited from auto-generated captions. Punctuation, capitalization, and technical terms have been corrected. Filler words and false starts have been removed. Student questions are marked. The verbatim original is in `original/03-learning-ii.md`.

**[0:05]** Today's lecture is going to continue our journey in machine learning. We're going to talk about linear classification. Last time we talked about linear regression, where the prediction task is: you're given some sort of input and you're trying to predict an output which is a real number — think of it as a price or some sort of score. Because it's linear regression, the hypothesis class are linear functions. Today we're going to talk about linear classification. Classification means the prediction task is taking some input and producing an output which is no longer a real-valued number but a discrete choice —

**[0:54]** also known as a class or a label. It's usually one of K things, where K might be 2, 5, or even 1,000. And because it's linear classification, the hypothesis class is going to be linear functions. But as we'll see later, it's not quite linear — it's almost linear — because you have to do something at the end. Let's go through the motions. Remember what we did for linear regression and see if that applies to linear classification. Let's start with a prediction task. An example of a classification task might be image classification: you're given an image, for example this image, and you're supposed to output a label which is what kind of object this is. In

**[1:39]** this case it's a cat, I think. Another task might be sentiment classification: you're given an input text document and you're trying to output the sentiment that the document is conveying — either positive or negative. So what is actually the input here? For an image — last time remember we talked about images as essentially tensors which are width × height × number of channels (RGB, so three channels). That's fairly straightforward. For text — well, it's a string, and a string is not a tensor. We're going to come back and revisit this, but for now let's just assume that our input is a

**[2:26]** vector or tensor. So what is the output? For classification, the output is always one of K choices. When K is 2, that's called binary classification. Usually the output space is denoted as −1 for the negative class and +1 for the positive class. Sometimes you also see 0 and 1, but in this class we're going to use −1 and +1 as a convention. Later we'll also see multiclass classification where the output is one of K choices. Usually in code it would be 0 through K−1; in math you might see 1 through K, but the ideas are the same. A predictor is a function that takes

**[3:11]** an input and produces a predicted output. For binary classification, it might look like this. You have an input — let's say x_a, the vector [1, 2] — and I'm going to send it through the simple binary classifier. What this will do is take the first component and subtract the second component, minus one: 1 − 2 − 1 = −2. Then I look at whether this is greater than zero. If so, I predict 1; otherwise I predict −1. In this case I'm predicting −1.

**[3:58]** Here's another example: x_b = [2, 0]. I send it through the predictor and get a logit of 1 — 2 − 0 − 1 = 1. In this case I predict +1. So the first example is labeled negative and the second example is labeled positive. You can visualize this here. The first example is at (1, 2) and the second one is at (2, 0). Each data point is two-dimensional, so I can just plot all the input points. One useful concept is the idea of the **decision boundary**.

**[4:45]** The decision boundary consists of points where it's sort of a toss-up — could be either way. If you look at what this predictor is doing: x₀ − x₁ − 1. If this is zero, you're at the boundary of positive and negative. If I plot the decision boundary x₀ − x₁ − 1 = 0, I get this boundary here. The decision boundary in the input space separates all the points classified negative on one side from all the points classified positive on the other. In this case I've broken ties somewhat arbitrarily by saying if the logit is zero, that's still −1, but you could put greater-equal

**[5:32]** if you wanted to. So I've shown you an example of a particular predictor — which happens to be linear — and given this predictor, I can show you what it does on inputs: it classifies inputs either as +1 or −1, and we can visualize it in the input space. There's no machine learning yet, because the key question is: how do we get this predictor? So far I just wrote down a particular predictor by hand, but of course when we're doing machine learning we're not interested in writing predictors down by hand — we want to learn them. Remember from last time, just a bit of terminology: the

**[6:18]** **training data** is a set of examples that demonstrates the task. In particular, each example is an input-output pair where the input is usually denoted x and the output is denoted y, the target — what we want an ideal predictor to produce. In classification, the training data might be: [1, 2] with target −1; [2, 0] with target +1; [0, 0] with target −1. Those are three examples in my training data, and I can plot them here and visualize them. Points are easy to visualize in 2D. I have a

**[7:04]** negative point here — sorry, a positive point here — and two negative points here. Usually you would do machine learning with way more points, but this is just an illustrative example. So that's the training data, and a learning algorithm is going to take this training data and produce a predictor. And remember: what is a predictor? It's a function that maps input to output — for classification, represented as a decision boundary. Effectively, I want to figure out a predictor, equivalently a decision boundary, that cuts up the space into positives and negatives. Intuitively, in classification, if I see a bunch of minus points here and plus points here, I probably want to draw the decision boundary somewhere along here. That

**[7:52]** corresponds equivalently to a function that takes any input point and produces a label +1 or −1. So that's the learning problem. And now the key questions to actually concretize this are three-fold. What are the possible predictors? That's the question of the **hypothesis class**. How good is a predictor? To determine that, we define a **loss function**. And how do we compute the optimal or best predictor? That's going to be within the realm of the **optimization algorithm**. We did this for linear regression. Now we're going to go through the three steps for classification and see how far we can get.

**[8:39]** So far I talked about the prediction task and what a learning algorithm is meant to do: data → predictor. Let's move on now to the first question: which predictors are we going to consider? I've sort of foreshadowed this already, so this part shouldn't be too surprising. As before, we want to **parameterize** our predictors, which means defining a set of parameters for each predictor. In the case of linear classifiers, the parameters include a weight vector and a scalar bias term. In this case, we might have a weight vector of [1, −1] and a bias of −1.

**[9:26]** And I might have an input: [1, 1], a two-dimensional point. I'm going to define the predictor which takes these parameters and an input and makes a prediction. What this binary classifier does: it takes parameters and an input, and computes what is called a **logit**. It just takes the dot product between the weight vector and the input vector, and adds the bias. In this case: 1×1 + (−1)×1 = 0, add the bias (−1), and I get −1.

**[10:12]** That's the logit. As before with a fixed predictor, I can look at whether the logit is greater than zero and use that to make a prediction. In this case the logit is negative, so I predict −1. I've drawn the decision boundary of this particular predictor, and this particular point gets classified as −1. Again, I can look at another predictor: same weight vector but a different bias.

**[10:59]** I feed in the same x and see what happens. I compute a logit: this time the dot product is still zero, and I add the bias which is 1, and therefore I predict +1. The point is still in the same place, but the decision boundary has shifted up, and now the point is lying on the positive side of the decision boundary. So I've shown you two predictors. In general, you can use your imagination: there are an infinite number of predictors, corresponding to all the different ways you can cut up the two-dimensional space with a straight cut. The **hypothesis class** is the set

**[11:44]** of all predictors you can get by choosing weight and bias in any way you want. All these decision boundaries are straight-line cuts — no curves allowed, because this is linear classification.

> **Student question:** Is it most of the time linear?

Most of the time the actual task is probably not linear. Linearity is a very fragile and special notion. But people often use linear classifiers because, as we'll see, they're fairly simple, there are few parameters so you don't overfit as much, there are nice properties — they're convex and so on. You might think, "Well,

**[12:32]** linear classifiers are clearly really limited." But it turns out that if you are in high dimensions — if you have, say, 10,000 dimensions — linear classifiers are actually very expressive. So it's not as bad as it looks. Of course, you can do more if you go beyond linear classifiers, which we'll talk about when we talk about neural nets.

Now let's go to the second question: defining a **loss function**. A loss function is a judge: give me a predictor, give me a training set, and tell me how well the predictor fits the training data. Let's consider our predictor here and get this training data.

**[13:18]** We are defining a loss function that takes these two things and returns a number — basically, how happy are we? How well does params fit the training data? To do that, we're going to start with one data point — let's say this one — and define a loss function that measures how unhappy one point is with the parameters. Remember: for linear regression — regression in general — we used the **squared loss**. The intuition is that we measure loss based on how far away the actual prediction is from the target. If you recall, the squared loss takes an example, takes the input,

**[14:04]** multiplies by the weight plus the bias — the prediction is −2. The target is −1. So we sort of missed; we're close but we missed. The amount by which we missed is the **residual**, which is −1. If you square the residual — that's why it's called squared loss — you get a loss of 1. So we missed, and we would suffer a loss of 1 on this example with these parameters. We could plot what that loss function looks like as a function of the residual — how far we're off

**[14:50]** the target. As you see, zero is where we've landed. Generally you want the property: if you get the target correct — if you land on target — you should have zero loss. The only question is: if you're off, in what ways are you penalized? The squared loss has pretty steep curves, which means that if you go away even a little bit, you're going to have quite a large loss. So we could just use this loss function for classification. Why might we want to use it or not use it for classification?

> **Student question:** The loss wouldn't tell you which side of the classifier it fell on.

**[15:37]** So to put it in other terms, we're trying to predict a number. I'll give you the pro first: the squared loss works, because if you get the answer correct — if the true target is 1 and I predict 1 — I get a loss of zero. If I predict −1 where the true answer is 1, then I get 2² = 4. In some sense it works, right? Because if I minimize the loss all the way to zero, I get the training set perfectly. But the idea of a residual doesn't quite make sense. It's a little

**[16:24]** bizarre. It type-checks — because you have 1 and −1 — but there's only really two values. I can get a residual of 0 or a residual of 2 or −2, which gives a loss of 4. Somehow this idea of classifying based on which side of the decision boundary isn't really being captured here. In particular, let's say the target is 1 and I predict 4 — then I'm going to get penalized. And if I predict 8, I'm going to be penalized even more. That feels kind of wrong. If you interpret the logit of 8 as positive with very strong confidence,

**[17:09]** I'm getting penalized a lot more for that. So this loss, I would say, is okay — it's not completely crazy — it's just a little bit off.

Let's think from first principles. What do we want in binary classification? We want to classify correctly. So we're going to define the **zero-one loss**. The 0-1 loss says: does the prediction essentially have the same sign as the target? We don't care about the magnitude, because we have a decision boundary — everything on one side should be positive, everything on the other side is negative. I don't really care how far it is.

**[17:56]** Let's do a quick example. If I have this data point [2, 0] and the target is 1, I send it through these parameters. First I make a prediction using my current parameters. I showed you this binary classifier before: I compute the logit, threshold the logit, and if it's greater than zero, I predict 1. In this case, the predicted y is 1 and the target y is 1. Then I define the loss to be whether these are different. This returns a boolean, and `int` just

**[18:42]** converts True to 1 and False to 0. That's why it's called a zero-one loss — it's 0 or 1, where 0 means "I'm correct" and 1 means "I'm wrong." Let's do another example: [0, −2] with target −1. In this case I make a prediction of 1 and my target is −1, so that gives me a loss of 1 because I misclassified this example. Fairly straightforward. The zero-one loss basically says: did I

**[19:28]** classify it incorrectly or not? One thing to note: I want you to start thinking of the loss function as something that's really custom. You can define a loss function to capture what you care about. I know there are these standard things like zero-one loss, you might have heard of logistic loss or squared loss — these are just standard things people use. But they really are meant to capture what you care about in a problem. You can define your loss to be essentially any function, and that will give you your own custom variant of a learning procedure.

**[20:13]** So far so good. I'm going to define the zero-one loss as whether the parameters-driven predictor classifies an example correctly or incorrectly. Now I'm going to play with the zero-one loss a bit. I'm going to write it in terms of this quantity called the **margin**, which will make it easier to do later things. Bear with me for now. The 0-1 loss inline is basically the same thing as the zero-one loss, but I'm just going to write it differently. What I'm going to do is compute the **logit** — the dot product of x and the weight vector plus the bias.

**[21:00]** Now think about what the logit is doing. The **sign** — whether it's positive or negative — is the prediction, and the **magnitude** is essentially how confident we are in the prediction. This is intuitive: if you have a large number, say you predict 1,000, that means "I really think it's positive," as opposed to a logit of 0.1, which means it's barely positive. But the sign is what determines the ultimate classification. Now I'm going to define the **margin** as simply the logit times the target y.

**[21:46]** The target y is +1 or −1, and you can think through the cases. What is the sign of the margin? The sign of the margin measures whether you're correct or not. If the logit is positive and the target is +1, this will be positive. If the logit is negative and the target is −1, this will also be positive. Regardless of the label, as long as the sign of the logit matches the target, I'm going to get a positive margin. If they have different signs, I'm going to get a negative margin. This is a shorthand trick to compress into

**[22:33]** one number something whose sign measures correctness. Now I'm going to define the loss as whether the margin is not positive. If the margin is negative, the loss is 1; if the margin is positive, the loss is 0. I'll let you mull this over for a bit and convince yourself that this is indeed exactly the same function as the zero-one loss, just written in this somewhat mysterious way.

I've introduced

**[23:18]** a few notions here. There's the **logit**, which is basically the raw prediction; the **margin**, which measures correctness; and finally the **loss**. Maybe it would help if I visualize this. If I plot margin versus the zero-one loss, I see that if the margin is negative, the loss is 1; if the margin is positive, the loss is 0. In general for classification, I'm going to look at these plots which

**[24:05]** measure loss as a function of margin — whereas for regression, I talk about loss as a function of residual. Margin is sort of the classification analog to residual. So now the **training loss** is again the average over the per-example losses. If I have a set of parameters and define the train zero-one loss, I compute the losses — one for every training data point — and take the average. That gives me the training loss. If I use the zero-one loss, the training loss is, in other words,

**[24:54]** the **error rate**. Because I get a 1 if I messed up and 0 if I don't, so the average over those zeros and ones is exactly the fraction of examples that I misclassified. It's a very nice, intuitive quantity.

To summarize: the **logit** is the raw score from the linear part of the model — the sign of the logit is the prediction (positive or negative) and the magnitude is how confident you are. The **margin** is the logit multiplied by the target — now the sign is whether the prediction is correct or not, and the magnitude

**[25:41]** of the margin is essentially how correct you are. The **zero-one loss** is defined in terms of margin: 1 if wrong, 0 if right. The **training loss** is the error rate, averaging over the training examples.

We're done with the second question: what loss function am I optimizing? I want to optimize the zero-one loss. And now we're off to the races — we can do optimization. Remember that for every setting of the parameters, we can compute a training loss.

**[26:27]** In linear regression, we optimized the parameters using gradient descent. So let's try to do the same thing here. I have my parameters, I have my data set, and I have the ability to compute the training loss. Now I just want to do gradient descent on this loss function. Let's compute the gradient of the zero-one loss — let's start with one example. I have the logit and the margin, and I notice that the gradient of the zero-one loss is

**[27:14]** zero basically everywhere — almost everywhere, except when the margin is zero, and then it's undefined. But that's not that interesting. That might be a problem. The gradient is zero everywhere. If you look at this, you should be able to see why: if you're sitting up here (margin positive), the gradient is zero. If you're sitting down here (margin negative), the gradient is zero. The gradient is literally zero everywhere except at margin = 0. So what happens if you run gradient descent? You compute the gradient, you move in the direction of the gradient (which is zero), and you don't move. Gradient descent is

**[28:03]** a completely ineffective way to optimize the zero-one loss.

So intuitively, what's going on here? With the 0-1 loss, you're very binary or discrete about your choices. If you happen to have a margin over here — and remember, the gradient is a very local notion: it says, "If I move my parameters a little bit, how much can I reduce the loss?" — the answer is: not at all. You can't make small changes to your parameters and move the zero-one loss. You have to move it a lot to get from over here

**[28:49]** to over here, but there's nothing you can do locally. Local notions just don't work.

It turns out there are actually many ways to fix this — an infinite number of ways, actually. But I'm going to give you one particular way to fix it, and this will require changing the loss function. Remember, I told you the loss function is a custom thing you can play with. You can put in whatever loss function you have, and you want ideally the loss function to capture what you care about in the problem. But as we see, we care about the zero-one loss, but we also want the loss function to be something that's amenable to optimization. Sometimes these things are in

**[29:34]** conflict, but we make do with what we have.

We're actually going to take a deeper conceptual turn and think about what the classifier outputs. Let's go to this example: I have a set of parameters and my example with the target. So far our predictor computes a logit and outputs a single prediction. It's really here in this binary classifier where things have gone wrong with respect to how we can solve this problem — because the binary classifier produces

**[30:20]** a predicted y and there's no margin. It got swallowed up. I've already lost if I'm outputting just a single prediction. This idea of thresholding — if you remember, the binary classifier does this thresholding: "if this, then something; if it's the other way, do something else" — that's just a very discrete operation. If-statements are very discrete.

What we're going to do instead is make things continuous. The nice thing about regression was that it was nice and continuous: any small change in the inputs or

**[31:06]** weights caused small changes in the output, and therefore gradient descent had some signal to latch onto. But if I'm thresholding, I kill that. So the conceptual leap here is: instead of a classifier outputting a single label, I'm going to have a classifier output a **distribution over labels** — a probability distribution over labels. Probability distributions are a continuous object, and I might imagine that with that, I can make some progress.

We're going to step through this. The key to doing this will be

**[31:53]** what is called the **logistic function**. Let's talk about the logistic function a bit and explore its properties. Remember, a logit is just a number — a real number from −∞ to +∞. It could be anything. And we want to convert the logit into a probability — and probabilities, as you know, are between 0 and 1. There are many functions that do this, but the logistic function is a standard choice. It has some nice mathematical properties, and it looks like this.

**[32:39]** If the logit is very negative, it gets mapped to 0; there's a smooth transition; and if it's large and positive, it gets mapped to 1. Mathematically, I'll step through what the logistic function is. If logit = 0, I pass zero into the logistic function:

$$\sigma(z) = \frac{1}{1 + e^{-z}}$$

If I punch in zero, that gives me 1/(1+1) = 0.5 — that corresponds to this point. If I put in 1, I get 0.73 — that's up here. If I put in 8,

**[33:27]** I get 0.999. So you can see that it doesn't take very many logits — the logit doesn't have to be very big for the probability to be very close to 1. This is because of the exponential. Whenever you see exponentials, think: crazy stuff happens. I'm going to get very close to 1 as a result. In the other direction, if I have a logit of −1, my probability is 0.269; at −8, it gets very close to 0.

This is the logistic function. You might ask where it comes from. There's another interpretation which, just for

**[34:14]** your edification: you can think about mapping in the other direction — from probability to logit. Imagine you have a probability of 0.2. If you divide the probability by 1 minus the probability, this is called the **odds**. So 0.2/(0.8) = 0.25, odds of 1/4. That's a familiar notion if you think about betting. And if you take the log of the odds, that is exactly the **logit**.

**[35:01]** To double-check: you can take that logit and push it back through the logistic function to get the same probability. So another way to think about the logistic function is that it's the inverse of mapping from probability to log-odds.

Hopefully this part is clear: as logit → −∞, probability → 0; as logit → +∞, probability → 1; and at 0, probability = 0.5. Another fun fact: if you take a probability corresponding to logit = 3 and negate the logit to −3,

**[35:48]** you'll notice these add to 1. σ(z) + σ(−z) = 1 always. In other words, σ(−z) = 1 − σ(z).

Let's look at the derivative. It's actually pretty nice and simple:

$$\frac{d}{dz}\sigma(z) = \sigma(z)(1 - \sigma(z))$$

I'll leave this as an exercise — you can do it on a piece of paper, it's

**[36:34]** not particularly enlightening, but the result is neat. What this means is that the derivative of the logistic function looks like a bell shape: if you're far away from zero, the derivative vanishes — goes to zero. The derivative peaks at 0.25 when the logit is 0. One thing you might start to think about: zero gradients are bad. We already saw this with the zero-one loss. It's also going to be bad for neural nets in general. With gradient descent, zero gradients are not your friend. And that means

**[37:22]** that if you have logits that are super big, your gradient might be in bad shape. Not necessarily — this is only one piece of the equation — but you might be a little worried.

So: logistic function takes logits between −∞ and +∞ and maps them to probabilities between 0 and 1. The logistic function actually comes from the 19th century, but it was first used in statistics in the context of **logistic regression**. Actually, what I'm going to talk about is identical to

**[38:10]** logistic regression, and this comes from the 1940s [Berkson, 1944].

Now we have our logistic function. How do we use it? Remember we have this predictor, and we didn't like how it was outputting just a single prediction. I'm now going to have it output a distribution over classes. I compute the logit, and I can just declare: if I take this logit and push it through the logistic function, I'm going to interpret that as the probability of the positive class. And because of that cool identity I showed you, σ(−logit) is the probability of the negative class. These

**[38:57]** conveniently sum to 1, which is always good for probabilities. In this case I have 0.5 and 0.5.

Now I can come back to the **margin**. Margin, remember, is how correct I am. If I look at the logistic function applied to the margin, I get the probability that I hit the target y. In this case, because the target is 1, the probability of the target is the probability of the positive label — so I have 0.5

**[39:42]** probability of the target y. You can see this might be helpful, because before I was just saying, "I predict 1 — good enough." Now I get a very fine-grained notion of how much probability mass I'm putting on the correct label. 0.5 is good, but maybe I want to increase it to 0.97. And because everything is continuous, I can actually look at the gradient — it'll tell me the direction to move to increase the probability. Even though I'm classifying this correctly, I might want to increase the probability of classifying it correctly.

At this point, if I have an example and a set of

**[40:29]** parameters, I can get a number which tells me the probability that this classifier with these parameters is going to output the target y.

There's a general principle — which also comes from statistics, but we'll see it again in other contexts — called the **maximum likelihood principle**. It's very intuitive: it says, maximize the probability of the training targets. Maximize — or more generally, maximize the probability of the data under a probabilistic model. In general, if you have multiple examples,

**[41:16]** you end up multiplying the probabilities, because it's: "I want for x₁ to get the target label y₁, and for x₂ to get y₂." So I'm multiplying the probabilities. This is equivalent to summing the log probabilities, because the log of a product is the sum of the logs. I'm trying to make progress toward defining the logistic loss. Now I'm going to look at the log probability of the target, not just the probability. And then the final step: I want the probability of

**[42:03]** the target to be high — I want to maximize this. I also want to maximize the log probability of the target. But in the context of loss minimization, to turn this into a loss, I just flip the sign:

$$\text{loss} = -\log p(y = y_{\text{target}} \mid x)$$

It depends on if you're a pessimist or optimist. If you're an optimist, you say "maximize likelihood." If you're a pessimist, you say "minimize loss." They're two sides of the same coin. For this particular example, I went through all these derivations to compute a loss — a number I want to drive down.

Let me package it up into a nice

**[42:48]** function. The logistic loss takes an example and a set of parameters. I compute the logit (sign = prediction, magnitude = confidence), the margin (sign measures correctness), the probability of the target is the logistic function applied to the margin, and the loss is the negative log of that. I now have this nice function: the logistic loss.

Let's see what it looks like. Remember the zero-one loss we didn't really like because the gradient was zero everywhere. If I plot the logistic loss,

**[43:34]** I see this beautiful curve. The gradient is actually not zero anywhere — which is interesting. Over here, it's definitely not zero. You can think about what happens if you put a ball here: you're going to start rolling down. This says that if you're very wrong, you should move — increase the margin. But interestingly, even if you're here — even at margin 3 — the gradient is not zero. That means the logistic loss is a bit of an overachiever: even if you've gotten the example correct, there's always room to improve

**[44:20]** the probability. So that's the logistic loss.

Once I define a loss function per example, I can define the **training logistic loss** as an average over all the examples. I define losses for each training example, take the average, and that gives me a single number that defines, for this particular classifier, how well it fits my data. This is a very nice continuous function — everything is continuous and differentiable — and I can take gradients of this.

> **Student question:** Why do I have to take log probability to get the loss?

**[45:09]** One could say: probabilities — I want to maximize these probabilities. Why don't I just maximize the probabilities? By convention, if you think about what the training error is — generally an average over individual errors — in order to be able to average, I need to take the log so that I'm averaging log probabilities. The other reason is that if you work directly with probabilities, you're going to get underflow very quickly, because probabilities are small. If you get a probability of 10⁻²⁰⁰,

**[45:54]** everything's going to go to zero, whereas if you take the log, you have much more space to work with.

All right. It took us a bit of a detour, but we finally got to answering the second question: how should we evaluate predictors? We're now going to use the logistic loss. Now let's go back and try to optimize the logistic loss function, and hopefully this will work this time.

First, we have to compute the gradient of the loss for one example. Let's use this as an example. You might wonder:

**[46:41]** isn't the whole point of computation graphs so you don't have to do this? And yes, indeed — I could have done everything in PyTorch and never actually computed a single gradient. But I'm going to go through this process just to show you that, first of all, at this scale it's very doable; and furthermore, by computing the gradient, you get additional insights sometimes.

Let's take the gradient of the logistic loss. Remember the definition: I have the logit (dot product between input vector and weights), the margin (logit × y),

**[47:26]** and the loss (−log σ(margin)). First, there is the gradient: d(loss)/d(logit). This quantity is: if I change the logit by a little bit, how much does the loss change? This happens to be −σ(−margin). Basically, you take this quantity and differentiate with respect to margin. Then, once you have that, by the chain rule the gradient to the weights is this quantity (gradient

**[48:12]** of the logit with respect to the appropriate parameters: example.target × example.x for weight; for bias it's just example.target).

I can just talk through how this works. You see this log here — the derivative of −log(something) is −1/(something). The derivative of the logistic is σ(z)(1−σ(z)). Then the logistic in the denominator cancels, and you have

**[48:58]** 1 − σ(z), which is the same as σ(−z). If that made sense, great; if not, I'll leave it as a homework exercise. The point is that you can compute the gradient of the logistic loss either using computation graphs or just doing some quick calculus.

Now the gradient of the **training loss** is just the average of the gradients of the examples. If I have my training examples, I compute the gradient of the logistic loss for each example, get these three things, and

**[49:44]** average them. Notice one thing about the type signature: the gradient is the same shape as the parameters. So if the parameters have weight and bias, then the gradients also have weight and bias. I average the weights, average the biases, and this is the gradient.

All right. Now it's not zero! I can do gradient descent. Let's run this. I'm going to initialize. For linear classification, it's okay to initialize the weights and bias to zero.

**[50:30]** (As we'll see, for neural nets, you can't do that.) Let's use a learning rate of 1. Usually this is fine for this example, but generally this would be pretty high for any practical application. We're going to step through iteratively 20 times. Each step, you compute the loss, take the gradient, and update the parameters in the direction of the gradient. You do this over and over again. You can see that the loss goes down.

**[51:17]** I've plotted the **learning curve** — the losses accumulated as a function of step — and you see that the loss is going down. If I let this run for longer, it'll go down even more. So optimization seems to be working. If I look at the final classifier I get, I get something that classifies this point as positive and these points as negative — which means I'm getting the examples correct. But notice that the line is also not hugging this point barely; it's trying to push away from these points, because the larger the margin,

**[52:03]** the farther from the decision boundary, the higher the probabilities. All right. So that concludes binary classification. I've talked about the hypothesis space, the loss function (tried 0-1 — didn't work; tried logistic — it works), and I used gradient descent to optimize the logistic loss.

> **Student question:** Where does this boundary come from?

This boundary is a depiction of the parameters. The parameters define a function that predicts positive for some points and negative for others, and the decision boundary is where the logit is exactly zero.

> **Student follow-up:** So this works for higher dimensions?

**[52:53]** Here there are two dimensions, so there are three parameters: two for the two dimensions plus a bias. If it were 10 dimensions, there'd be 11 numbers and it would be much harder to visualize, but the math all works the same and the code also works the same.

Okay, so that is binary classification. Now I want to talk about **multiclass classification**. Binary classification: output is {−1, +1}. Multiclass: output is {0,

**[53:40]** …, K−1}. Let's review binary classification: we compute just a single logit for the input, and the sign of the logit is the predicted class. If I got a logit of 1, the probability of positive is σ(logit), and probability of the negative class is 1 − σ(logit).

How do I extend this to multiclass? I'm going to define a **weight vector for each class**. You can think of a weight vector as representing information about what each class is

**[54:25]** about. That will allow us to compute a logit for each class. And then, given that vector of logits, I'm going to do some stuff to make it a distribution. Again, we learned our lesson with binary classification: we don't just want to do 0-1 loss — we want to do something probabilistic. And in the multiclass classification setting, I want a probability distribution over all K labels.

Let's work through this example. Suppose I have three classes. My weight matrix is going to be a weight vector of dimension 2 for class 0, class 1, and class

**[55:11]** 2; and the bias is also going to be a bias for each class: 0, 1, 2. Now I have this input here. I can compute the logits — which is conveniently the same notation as before, but now I'm doing it for every class instead of a single class. I get [3, 3, 0] — 1×2 + (−1)×0 + 1 = 3, and so on. I have a logit for every class.

Now, how do I turn logits into

**[55:57]** probabilities? I need another function. Before, I had the logistic function which turned a logit into a probability between 0 and 1. Now I have a vector of logits. What if I just apply the logistic function to each logit individually? Is that going to work? No — because probabilities have to sum to 1. They have to be coordinated.

I'm going to introduce a function called **softmax**. The logistic function maps a single number; the softmax function generalizes to multiple classes and applies to a vector. Let's do this by example. Suppose I have [−1,

**[56:44]** −1, 0]. What does the softmax function do? First, it exponentiates each of the logits: e⁰ = 1, e⁻¹ is something less than 1, and so on. Then it normalizes — sums these up and divides by that number. So I get a distribution that sums to 1:

$$\text{softmax}(\mathbf{z})_i = \frac{e^{z_i}}{\sum_j e^{z_j}}$$

You can see that in general, higher logits means higher probability, but it's a nonlinear relationship. For example, −1 has pretty low probability.

**[57:29]** If you sum these numbers, that's the thing you're dividing by. In some sense, you can think about the softmax as sort of the bare minimum you need to do — like, if you're lazy: "I need a distribution; how do I get a distribution?" You look at these logits and say: "Oh, probabilities can't be negative. What's the easiest way to make something not negative? Exponentiate!" And: "Oh, this doesn't sum to 1. How do I make it sum to 1? Divide!" So it's the lazy way to make a distribution. But it also turns out to be mathematically nice in other ways.

One property, so you

**[58:16]** have some intuition: shifting the logits doesn't change the relative probabilities. If I have these logits, I get these probabilities. And if I just add 2 to all these logits, miraculously I get the same set of probabilities. I'll leave it to you to figure out why — it has something to do with multiplying and then dividing by the same number. This actually is a useful trick later. I don't know if you'll need it in this class, but to avoid

**[59:03]** numerical underflow: whenever you exponentiate something, you should be really scared, because a computer doesn't actually represent real numbers — it has finite range. If you're exponentiating something big, it could blow up. So the idea is that you can shift the logits up and down to keep them in a nice range, then compute the softmax — and that's mathematically identical. Just a fun fact.

All right, so now we have this function, softmax, that takes a vector of logits (one for every class) and produces

**[59:49]** a distribution over the classes. Great. Now I need to turn this distribution into a loss. To do that, I'm going to take another detour and talk about **cross entropy**.

In general, cross entropy is an idea from information theory. It measures the difference between a target distribution and a predicted distribution in general. Suppose the target distribution over three things is [0.5, 0.2, 0.3] and I predict [0.1, 0.5, 0.4]. The question is: do I have a number that tells me how far the predicted is from the target? The intuition is: if the

**[1:00:37]** target says there's high probability on an outcome, and the predicted puts low probability on it, I want to penalize that. So what I do is: take each element in the target, multiply by −log of the predicted probability. So: 0.5 × (−log 0.1) + 0.2 × (−log 0.5) + 0.3 × (−log 0.4). If I sum all these up, that's the cross entropy:

$$H(p, q) = -\sum_i p_i \log q_i$$

Some of this should seem a little familiar — this minus-log you've seen before, when we were looking at logistic loss and how we took

**[1:01:22]** the probability and made it into a loss. Now this is generalized. In fact, if you take K = 2, this multiclass setting should recover the binary classification setting as a special case.

Some other properties: cross entropy is minimized when target = predicted, and that minimum value is the entropy of the target (or predicted, equivalently). In general, if you're designing a loss function, the first thing you check is: if the thing you want is

**[1:02:09]** true, then your loss should be as low as possible. Otherwise you've done something wrong. Our goal is to make the target distribution equal the predicted distribution.

There's much more you can say about cross entropy — you can look at information theory notes. For example, KL divergence is related to entropy and cross entropy (if you subtract off the entropy, you get the KL). But that's not necessary for understanding the rest of this lecture.

I've done cross entropy in general just to define it, but in practice

**[1:02:56]** this is going to be a very special case: the target is usually a single label, represented as a **one-hot vector**. For example, if the target is class 1, I represent that as a distribution where all the probability mass is on index 1. The predicted is whatever distribution I have. If I go through the cross entropy calculation, I see that in the special case, all this is — it's the negative log probability of the

**[1:03:41]** target class. So actually it's the same thing as what we had in the binary case. But people seem to like talking about the cross entropy loss, so I've done it in slightly more general terms. This also allows you to do things like — if your ground truth were not just a single label but an actual distribution, then there's a natural way to incorporate that as well. But at the end of the day, it's still maximum likelihood: if you have a probabilistic classifier, you want to maximize the probability of the target class, or equivalently minimize the negative log of

**[1:04:26]** that probability.

> **Student question:** Why are some of these terms zero?

Because the terms are the element-wise multiplication of target and −log(predicted). If the target element is zero, then the term is zero.

Now we can compute the cross entropy loss for a given example. I compute the logits, take the softmax to get a distribution, and then take the cross entropy as the −log probability of the target.

**[1:05:12]** That's it. Given this loss, we can perform gradient descent to optimize the parameters. I'm not going to bother you with that this time, but hopefully you can do that.

To summarize this part: **softmax** is the main function that turns logits into probabilities. **Cross entropy** measures the discrepancy between the target distribution and predicted distribution. The **cross entropy loss** generalizes the logistic loss and is the predicted probability of the target class.

Now you know how to do multiclass classification. There's one final thing I want to end with, which is **representing text**. Remember,

**[1:05:57]** the input to a classification problem might be strings, but machine learning — everything we've looked at — relies on tensors. So what do we do with a string? This is not a tensor. Typically there's processing that has to happen to turn strings into tensors. I'm going to talk about this in two steps. One: **tokenization** — first convert the string into a sequence of integers. Two: represent each integer as a **one-hot vector**. That's going to give me a tensor.

Tokenization: the idea is this is a string — I want to first split it

**[1:06:43]** into words (split on spaces) and convert them into integers. I'm going to have a Vocabulary class that maps between integers and strings. I split "the cat in the hat" into words. For every one of these words, I call `vocab.get_index(word)`. What does it do? It looks up: "Have I seen this string before?" If I haven't, assign it an index and record it. If I have, use the same index. At the end of the day, I map these words into indices — and you see that "the" occurs both times and gets the same index.

**[1:07:28]** This is the mapping between strings and indices. This is typically what's done to take strings and map them to integers. More recently, with language models, people do fancier things than splitting on spaces — which is problematic for many reasons. For example, periods: if I do this naive thing, periods get attached to words, which is no good. And then you say, "Fine, I'll split out punctuation," but then 3.4 gets split into three things. Some hyphenated words are really long, and if you're in German, you get really long tokens. It's a big mess. So

**[1:08:16]** there are more automatic ways to determine how to split strings into tokens, called **BPE** or Byte-Pair Encoding. This was pioneered — used in NLP, but in language models pioneered by GPT-2 [Sennrich et al., 2015]. There are libraries for this: you define a tokenizer and then you just encode a string into a sequence of integers. There are a few encodings you can use, but that will be outside the scope of this class.

At the end of the day, when you've tokenized, you get a set of indices. Now I'm going to represent each index as a one-hot vector. For example, let's look at

**[1:09:03]** index 3. I represent this as a one-hot vector where I have a 1 exactly in position 3. I can represent the entire string as a matrix where every row corresponds to a position in the token sequence, and that row is a one-hot vector: [0, 1, 2, 0, 3]. Now I have a tensor, and I know what to do with it.

In practice, we can store the indices — not the one-hot vectors — because this is going to be sequence_length × vocab_size if you do it

**[1:09:49]** naively. And you can directly operate on the indices. Suppose we want to multiply this matrix by a vector w. You can do this simply in PyTorch (or NumPy): `matrix @ w` gives you a vector. But you can also just use these indices directly into w: `w[indices]` — and that gives you the same thing.

Finally, what I want to talk about is the **bag of words** representation. The tokenization

**[1:10:35]** gives you a vector for every position. But one thing I can do is average all of these row vectors together and get something that's kind of an average vector — that's called the bag of words representation. Using this bag of words representation, I can take dot products and do whatever I want. And equivalently, I could have just operated on the indices directly as well.

In your assignment, you're going to actually look at the bag of words representation. The nice thing is that it's very simple and it

**[1:11:21]** doesn't depend on the length of the text. No matter how long your document is, you get yourself a fixed-dimensional vector — which is convenient, because the number of parameters you have is generally fixed. The con is that it doesn't pay attention to word order. "Dog bites man" is the same representation as "man bites dog," which are clearly very different sentences. But when we talk about transformers, we'll talk about how we can get around that limitation.

To sum up: we convert strings into tensors using tokenization and one-hot encoding. One-hot encoding — it's convenient to think about them mathematically as vectors, but in code sometimes it's useful to work with the indices directly.

**[1:12:08]** In this lecture, we've talked about linear classification: mapping to a fixed set of K choices. We looked at the zero-one loss — which is what we ideally want to measure — but we can't optimize that. So we relaxed the space into probabilistic classifiers and defined the logistic loss, which we could optimize. We saw multiclass classification as an extension. And then we saw how we can represent text as tensors.
