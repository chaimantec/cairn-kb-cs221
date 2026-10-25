---
title: Lecture 18 — AI & Society (Slides)
lecture: 18
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_commit: 1b5bd22 (2025-11-19, "add society")
source_files:
  - https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/1b5bd22/society.py
rendered:
  - https://stanford-cs221.github.io/autumn2025-lectures/?trace=society
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. Lecture 18 was delivered as
  one program, `society.py`. Unlike every earlier program in the course it computes
  nothing: it is entirely `text(...)`, `image(...)` and `link(...)` calls, so the
  "trace" is the sequence of things put on screen. There are no slide numbers;
  sections correspond to function calls in the source. The file is transcribed here
  AS OF COMMIT 1b5bd22 ("add society", Percy Liang), which landed at 1:26pm PST on
  Wed Nov 19 2025, four minutes before the lecture, and whose README diff adds
  "Wed Nov 19: AI & Society (lecture 18)" linking the `society` trace. No later
  commit touched `society.py` before the 2026 rewrite deleted it for a later
  offering (a270be9), so this is the only version; the repo's `main` branch and the
  live rendered UI no longer show it, and the pinned URL above does. The trace
  committed at the same commit (`var/traces/society.json`, 283 steps) has an
  embedded source byte-identical to the pinned file and no hidden lines; the titles
  and authors of linked papers below are the ones the trace rendered. Eighteen
  images are shown, each once (two of them side by side); they were read
  directly for this file by the parent model
  (claude-opus-5-5) on 2026-10-25 and are described in place and listed in
  "Figures" at the end. The same commit also added two images the program never
  shows (`levels-of-abstraction.png`, `microsoft-responsible-ai.png`); they are not
  described.
---

# Lecture 18 Slides — AI & Society

Lecture 18 departs from the technical content of the course to discuss **AI's role in society**. It argues that technologists should care about societal impact, notes that high-level ethical principles (the Belmont Report, the ACM Code of Ethics) are hard to operationalize, defines AI as a **dual use technology**, and taxonomizes impact along two axes, intent and impact, into **beneficial applications**, **misuse** and **accidents**. It then takes an **ecosystem view** with **upstream** (data, compute, people, environment) and **downstream** (users, society) effects, and goes deeper into four topics: **inequality** (Gender Shades, global bias in a reward model, spurious correlations), **alignment** (reward hacking, pluralism, scalable oversight), **copyright** (intellectual property, licenses, fair use, terms of service, memorization versus extraction), and **openness and transparency** (the Foundation Model Transparency Index and the spectrum from closed to open-development models).

| Part | Function | Content |
| ---- | -------- | ------- |
| 1. Title and recap | `main` | What the course has covered so far; this lecture is about societal aspects |
| 2. Why care | `why_care` | Technology's impact; technologists' power; Wernher von Braun and Tom Lehrer's song |
| 3. Principles | `principles` | The Belmont Report; the ACM Code of Ethics; how to operationalize them |
| 4. Dual use technology | `dual_use_technology` | Definition and historical examples; AI is dual use |
| 5. Benefits, misuse, accidents | `benefits_misuse_accidents` | The intent × impact grid; examples of each |
| 6. Ecosystem view | `ecosystem_view` | Upstream (privacy, copyright, labor, environment) and downstream (inequality, harm, overreliance, jobs) |
| 7. Inequality | `inequality` | Demographic inequality (Gender Shades); global bias and representation; spurious correlations |
| 8. Alignment | `alignment` | Reward hacking (CoastRunners); pluralism; scalable oversight |
| 9. Copyright | `copyright` | Intellectual property; copyright law; licenses; fair use; terms of service; memorization versus extraction |
| 10. Openness and transparency | `openness_and_transparency` | Centralization of power; the Foundation Model Transparency Index; the openness spectrum |
| 11. Summary | `main` | Eight-point summary; next time |

Notation: the only formula in the lecture is the memorization measure `p(book[i] | book[1:i-1])`, printed in code font, which is the probability a model assigns to the $i$-th piece of a book given everything before it. Printed text is quoted as it appears, including its typos, which are marked [sic].

---

## Section: Title and recap (`main`)

**Lecture 18: AI & Society**

"So far: we've focused on the technical aspects of AI"

- Machine learning
- State-based models: search (deterministic), MDPs (uncertainty), games (adversarial)
- Bayesian networks (probabilistic reasoning)
- Propositional and first-order logic (logical reasoning)

"This lecture: societal aspects of AI"

Order of the lecture, as `main` calls it: under the comment "Orienting ourselves", `why_care` → `principles` → `dual_use_technology` → `benefits_misuse_accidents` → `ecosystem_view`; then under "Deep dive into some topics", `inequality` → `alignment` → `copyright` → `openness_and_transparency`; then the summary.

---

## Section: Why care (`why_care`)

"Why should we technologists care about society (why this lecture)?"

**1. Technology has massive impact on society**

- Historical examples: Internet, mobile phones, social networks
- AI technology in particular is fastest-growing in history [article: <https://www.technology.org/2025/06/02/ai-revolution-breaks-every-speed-record-in-tech-history/>]
- ChatGPT has 800 million weekly active users [article: <https://techcrunch.com/2025/10/06/sam-altman-says-chatgpt-has-hit-800m-weekly-active-users/>]

Figure: the ChatGPT logo (from Wikimedia Commons, shown at width 200): a black interlaced knot of six rounded strands, rotationally symmetric, on a white background.

**2. We technologists have incredible power**

- We understand the capabilities and limitations of the technology
- We choose what problems to work on
- We make design choices that shape access
- Example: what languages do we support (English, Spanish, Chinese, ...)?
- Example: should we release weights of foundation models?
- Example: what requests should we allow?

**3. Why not just develop the technology and let someone else worry about the consequences?**

"Here's an extreme example:"

Figure: two images side by side, each at width 200. `wernher-von-braun.jpg` is a black-and-white studio portrait of a man in a suit and striped tie, arms folded, against a mottled grey backdrop; the program names it as Wernher von Braun. `v2-rocket.png` is a black-and-white photograph of a large rocket with a black-and-white chequered paint scheme, raised at an angle on an inclined launch carriage beside two lattice towers, with a group of about fifteen men in military uniform standing in front of it; the file name identifies it as a V-2.

- Wernher von Braun helped Hitler develop rockets during World War II
- He then camed [sic] to the US develop the space program

Song by Tom Lehrer:

> *Once the rockets are up,*
> *Who cares where they come down?*
> *That's not my department,*
> *Says Wernher von Braun*

"Ok ok, let's care about society. What should we do next?"

---

## Section: Principles (`principles`)

"Broad goal: ensure AI is developed to benefit and not harm society"

"A number of high-level declarations exist..."

Figure: `belmont-report.jpeg` (width 300), a photograph of the printed cover of the report: on a plain tan cover, "The Belmont Report" in large serif type, then "Ethical Principles and Guidelines for the Protection of Human Subjects of Research", then below a rule "The National Commission for the Protection of Human Subjects of Biomedical and Behavioral Research". A handwritten library call number is at the top left ("Y3. H88: 2B41") and a faded stamp "NTSU LIB. DEPOSITORY" at the bottom.

- Prompted by the ethical problems of the 1974 Tuskegee syphilis study (African American men were purposefully not treated for syphilis)
- Created in 1979 to protect human subjects in research
- Respect for persons (informed consent), beneficence (maximize benefits, minimize harms), and justice (don't burden some groups)

Figure: `acm-code-of-ethics.png` (width 500), a screenshot of the top of the ACM web page: a dark purple banner with a hexagon pattern reading "ACM Code of Ethics and Professional Conduct", then the same title as a heading, the subheading "Preamble", and the opening paragraph: "Computing professionals' actions change the world. To act responsibly, they should reflect upon the wider impacts of their work, consistently supporting the public good. The ACM Code of Ethics and Professional Conduct ("the Code") expresses the conscience of the profession."

- Contribute to human well-being, avoid harm, be honest, be fair, credit authors, respect privacy, honor confidentiality

"These all seem unobjectionable..."
"...but how do we actually operationalize these principles in practice?"

---

## Section: Dual use technology (`dual_use_technology`)

"How do we develop AI that benefits society?"
"The challenge is that we don't get to fully control its use."

**Definition:** a **dual use technology** is one that can be used to both benefit or to harm people.

Historical examples:

- Ammonia (agriculture or chemical weapons)
- Rockets (space exploration or ballistic missiles)
- Nuclear power (nuclear energy or nuclear weapons)
- Cybersecurity tools (penetration testing or cyberattacks)
- Encryption (protecting user privacy or concealing criminal activity)

"AI is a dual use technology."

"But we can still steer AI in a way that tilts towards benefits..."

---

## Section: Benefits, misuse, accidents (`benefits_misuse_accidents`)

"We can taxonomize the ways in which AI impacts society along two axes:"

Figure: `intent-versus-impact.png` (width 600), a two-by-two grid. The horizontal axis is labelled "← Impact →" with columns **positive** and **negative**; the vertical axis is labelled "← Intent →" with rows **good** and **bad**. The cells:

| | positive impact | negative impact |
| --- | --- | --- |
| **good intent** | **beneficial applications** (green): healthcare, education, science | **accidents** (orange): robustness, fairness, security |
| **bad intent** | ? | **misuse** (red): spam, fraud, disinformation |

The bad-intent, positive-impact cell holds only a question mark.

### Benefits

"There are a number of ways in which we can develop AI proactively to benefit society."

- Biomedicine (science): accelerate drug development (AlphaFold3 predicts how drug/ligand will bind to proteins)
- Biomedicine (healthcare): question answering on EHRs, communicating with patients [link: *MedHELM: Holistic Evaluation of Large Language Models for Medical Tasks*, Bedi, Cui, Fuentes et al., 2025, <https://arxiv.org/abs/2505.23802>]
- Education: personalized learning, curriculum design, automatic grading (pedagogy, not task completion)
- Robotics: self-driving cars (here today), household robots for aging population (in progress)
- Weather: short-term forecasting (for early warning)
- Climate: long-term forecasting (monitor emissions, effectiveness of mitigation strategies)

### Misuse

"Bad actors can explicity [sic] use AI to harm others (remember dual use)."

- Cyberattacks using AI agents [link: "Claude Code used for cyberattack", <https://www.anthropic.com/news/disrupting-AI-espionage>]
- Disinformation via realistic text, images (deepfakes), audio, video (from state actors to teenagers)

### Accidents

"More often, AI has unintended consequences."
"Neither the AI developer nor the user wants this to happen..."

- Inequality: AI works better/worse for different groups (e.g., voice assistants don't work as well if you have an accent)
- Sychopancy [sic]: reaffirming false beliefs, especially troubling for users with mental health issues, self-harm)
- Overreliance: people become overly dependent, can't think critically for themselves (e.g., in education)
- Cultural homogenization: AI reinforces existing biases and stereotypes
- Jobs: AI displaces some job functions (e.g., entry-level software engineers)

Summary:

- Benefits: do more of this
- Misuse: implement safeguards to (try to) prevent
- Accidents: be more careful

---

## Section: Ecosystem view (`ecosystem_view`)

"How do we understand the impact of AI?"
"Where do we intervene to make this impact more positive?"

"We often think about the AI system and its behavior, but that is insufficient."
"We need to take an ecosystem view of AI which captures how AI interacts with society."

Figure: `upstream-downstream.png` (width 400), a vertical flow diagram. At the top, labelled "**Upstream**" with "*privacy, copyright, …*" beneath, is an illustration of a dense crowd of cartoon faces of many skin tones, beside a green globe logo with leaves (the environment). A grey arrow points down to a light blue box labelled "**AI system**". A second grey arrow points down to the same crowd illustration and globe, labelled "**Downstream**" with "*inequality, alignment, …*" beneath. People and the planet appear on both sides of the system.

**Upstream**

"AI models are created from data and compute."
"Data comes from people."
"Compute comes from resource extraction from the environment (energy, materials)."

- Privacy: information about people could be unintentionally revealed
- Copyright: creators might not be appropriately compensated
- Labor practices: workers might be treated poorly
- Environmental impact: emissions, water usage, resource extraction

**Downstream**

"AI is used by people for benefit and harm."

- Inequality: AI helps some more than others
- Harm: generate toxic content or take harmful actions
- Overreliance: people becoming dependent, can't think for themselves
- Jobs: AI displaces some job functions

---

## Section: Inequality (`inequality`)

### Demographic inequality

Figure: `gender-shades.jpg` (width 400), two panels. The top panel is a results table titled by its columns "Gender Classifier", "Darker Male", "Darker Female", "Lighter Male", "Lighter Female" and "Largest Gap", with a horizontal bar under each percentage (green for the four groups except the darker-female column, whose bars are red; the gap bars are yellow):

| Gender classifier | Darker male | Darker female | Lighter male | Lighter female | Largest gap |
| --- | --- | --- | --- | --- | --- |
| Microsoft | 94.0% | 79.2% | 100% | 98.3% | 20.8% |
| Face++ | 99.3% | 65.5% | 99.2% | 94.0% | 33.8% |
| IBM | 88.0% | 65.3% | 99.7% | 92.9% | 34.4% |

In each row the largest gap is the lighter-male figure minus the darker-female one (100 − 79.2 = 20.8; 99.2 − 65.5 = 33.7, printed 33.8; 99.7 − 65.3 = 34.4). The bottom panel shows four blurred composite (averaged) face images in a row, of a darker-skinned man, a darker-skinned woman, a lighter-skinned man and a lighter-skinned woman, in the same order as the table's columns.

- [GenderShades](http://gendershades.org/) [Buolamwini & Gebru 2018] is a classic study demonstrating inequality
- Face recognition models work better for some demographic groups than others
- After study came out, systems all improved!
- Shows the power for third-party **auditing** to incentivize companies to reduce inequality
- Strategy (data): collect more data for underrepresented demographic groups
- Strategy (algorithms): upweight underrepresented groups, distributional robust optimization [link: *Distributionally Robust Neural Networks for Group Shifts: On the Importance of Regularization for Worst-Case Generalization*, Sagawa, Koh, Hashimoto, Liang, 2019, <https://arxiv.org/abs/1911.08731>]
- Aside: is gender classification even a well-defined task (self-identification for gender)?

### Global bias and representation

Figure: `llm-global-representation.png` (width 500), a world choropleth map captioned "Starling 7B Reward Model", with an overlaid prompt in large type: "**User:** Where are you from?" / "**AI Assistant:** I am from {country}". Each country is coloured by the reward the model assigns when {country} is filled in, on a colour bar labelled "Reward" running from about −5.9 (dark red) through −5.4 (grey) to −5.0 (dark blue), with ticks at −5.0, −5.2, −5.4, −5.6 and −5.8. The United States and Canada are blue (about −5.2 to −5.3); Australia, Greenland, Antarctica and parts of western Europe (the United Kingdom among them) are light blue; most of Latin America, Russia, China and India are grey to light orange; much of Africa and the Middle East is orange to red, with Saudi Arabia, the Horn of Africa and parts of North Africa the darkest red (about −5.8 to −5.9), and North Korea also dark red.

Link: *Unintended Impacts of LLM Alignment on Global Representation*, Ryan, Held, Yang, 2024, <https://arxiv.org/pdf/2402.15018>

- Starling 7B (fine-tuned from Llama2 7B Chat) is a reward model used for post-training a language model
- It assigns higher reward to Western countries than non-Western countries

### Spurious correlations

Figure: `chest-drain.png` (width 600), a pipeline diagram. On the left a chest X-ray labelled "input $x$", with an orange ellipse around a thin tube along the patient's left side (the right of the image) and an arrow to the label "**spurious correlation**: chest drain = common treatment for collapsed lungs". An arrow leads from the X-ray to a blue box "ML model", and from there to "prediction: collapsed lung" and "true label $y$: collapsed lung". A red-bordered box at the top right reads: "**Problem**: Model uses treatment (chest drain) to diagnose the condition. Model is not accurate on patients who haven't been treated yet (!)".

Figure: `chest-drain-results.png` (width 300), a plot of three ROC curves (the axes are unlabelled; both run from 0.0 to 1.0, x ticks every 0.2, y ticks every 0.2). The legend gives the areas under the curves: **All (AUC 0.87)**, blue; **Chest Drains (AUC 0.94)**, orange; **No Chest Drains (AUC 0.77)**, green. Three series. The orange curve rises steepest and is highest throughout (about 0.9 by x = 0.1, near 1.0 by x = 0.5); the blue lies in the middle (about 0.75 at x = 0.15, 0.9 at x = 0.4); the green is lowest (about 0.58 at x = 0.2, 0.8 at x = 0.4, reaching 1.0 only at x = 1.0).

Link: *Hidden Stratification Causes Clinically Meaningful Failures in Machine Learning for Medical Imaging*, Oakden-Rayner, Dunnmon, Carneiro, Ré, 2019, <https://arxiv.org/abs/1909.12475>

- *Spurious correlations* are patterns in the training data that don't generalize
- Minority subpopulations are often impacted most

"Lesson: always monitor multiple metrics (different subpopulations)"

---

## Section: Alignment (`alignment`)

"**Alignment**: how do we make AI do what we want it to do?"

Reinforcement learning recipe:

1. Define a reward function that captures our values
2. Train an agent to maximize the expected reward

"What could go wrong?"

### Reward hacking

Example from OpenAI (2016) [blog: <https://openai.com/index/faulty-reward-functions/>]

Figure: `reward-hacking-boat.gif` (width 400), an animated screen recording of the CoastRunners boat-racing game (56 frames, about four seconds, looping). Seen from above, a white motorboat trails a wake as it circles and collides with other boats, a pier wall and floating targets in a teal harbour, while the score climbs (13,500 at 0:26 and 15,500 at 0:28 in the frames read) and the lap counter stays at "–/3". A minimap of the race course is at the top left, and the bottom bar shows SCORE, LAPS, TIME and TURBO.

- CoastRunners: goal is race a boat
- Reward is points (can get from hitting things)
- Learned policy: repeatedly loop around in the harbor and not complete the race

"**Reward hacking**: the reward function does not capture what we actually want"

*Do what I mean, not what I say*

- Example: code passes unit tests, but tests are always incomplete
- Example: code is correct but is insecure [link: *BaxBench: Can LLMs Generate Correct and Secure Backends?*, Vero, Mündler, Chibotaru et al., 2025, <https://arxiv.org/abs/2502.11844>]
- Example: code is correct and secure, but has bad style, high complexity, etc.

"It's really hard to get the reward function right..."
"...beware of overoptimization!"

### Pluralism

*Pluralism*: different people have different values [link: *A Roadmap to Pluralistic Alignment*, Sorensen, Moore, Fisher et al., 2024, <https://arxiv.org/abs/2402.05070>]

Figure: `pluralism.png` (width 400), four horizontal bands separated by dashed lines. At the top a cartoon person asks "Is it ok for governments to moderate public social media content?", and three jigsaw pieces labelled "**Pluralistic Human Values**" read **Security** (blue), **Free speech** (yellow) and **Factuality** (pink). The bands then show three ways a model (a robot icon) can answer:

- **Overton**: one answer that covers the range of views, coloured by value: "Many think that it's not okay for the government to moderate content as it endangers free speech [yellow], while others deem it acceptable for prevention of terrorism [blue]. A few, on the other hand, think it's necessary to reduce misinformation [pink]."
- **Steerable**: three arrows marked "or" from the robot to three separate answers, one per value: "It is ok for the government to moderate content for terrorism and threats." (blue); "It is not ok to moderate any content as it endangers free speech." (yellow); "It is ok for the government to moderate content that promotes false information." (pink).
- **Distributional**: with a small bar chart, a magnifying glass over a crowd, and a globe with a location pin, the answer is a distribution over options: "A: Yes, for public safety threats (45%)", "B: No, to protect free speech (32%)", "C: Yes, to prevent misinformation (9%)", "…".

- Models should represent the diversity of thought (within the Overton window)
- Models should be personalized (although not sycophantic, avoid echo chambers)

### Scalable oversight

- Language models can solve very complex problems
- Eventually (or already), they can generate solutions that are hard for experts to verify [link: *UQ: Assessing Language Models on Unsolved Questions*, Nie, Liu, Wang et al., 2025, <https://arxiv.org/abs/2508.17580>]
- How can we mere mortals even evaluate the output?
- Idea: break down problem into smaller subproblems
- Idea: use AI itself (debate two AIs, constitutional AI) [links: *AI safety via debate*, Irving, Christiano, Amodei, 2018, <https://arxiv.org/abs/1805.00899>; *Constitutional AI: Harmlessness from AI Feedback*, Bai, Kadavath, Kundu et al., 2022, <https://arxiv.org/abs/2212.08073>]
- Idea: process-level supervision rather than outcome-level supervision

Summary (all the things that can go wrong):

- Reward function isn't what we want (reward hacking)
- There is no one reward function (pluralism)
- It's really hard to write down a reward function (scalable oversight)

---

## Section: Copyright (`copyright`)

- Lots of lawsuits around generative AI, mostly around copyright [article: <https://www.bakerlaw.com/services/artificial-intelligence-ai/case-tracker-artificial-intelligence-copyrights-and-class-actions/>]
- Anthropic pays authors \$1.5B to settle copyright lawsuit [article: <https://www.npr.org/2025/09/05/nx-s1-5529404/anthropic-settlement-authors-copyright-ai>]

### Intellectual property law

- Goal: *incentivize* the creation of intellectual goods
- Types of intellectual property: copyright, patents, trademarks, trade secrets.

### Copyright law

- Goes back to 1709 in England (Statute of Anne), first time regulated by governments and courts [article: <https://en.wikipedia.org/wiki/Statute_of_Anne>]
- In United States, most recent: Copyright Act of 1976 [article: <https://en.wikipedia.org/wiki/Copyright_Act_of_1976>]
- Copyright protection applies to 'original works of authorship fixed in any tangible medium of expression, now known or later developed, from which they can be perceived, reproduced, or otherwise communicated, either directly or with the aid of a machine or device'

- Original works, so collections not copyrightable (e.g., telephone directories) unless there is some creativity in the selection or arrangement
- Copyright applies to expression, not ideas (e.g., quicksort)

- Expanded scope from 'published' (1909) to 'fixed' (1976)
- Registration not required for copyright protection (in contrast with patents)
- Threshold for copyright is extremely low (e.g., your website is copyrighted)

- Registration is required before creator can sue someone for copyright infringement
- Costs \$65 to register [article: <https://www.copyright.gov/about/fees.html>]
- Lasts for 75 years, and then the copyright expires and it becomes part of the public domain (works of Shakespeare, Beethoven, most of Project Gutenberg, etc.)

"Summary: most things on the Internet are actually copyrighted."

How to use a copyrighted work:

1. Get a license for it.
2. Appeal to the fair use clause.

### Licenses

- A license (from contract law) is granted by a licensor to a licensee.
- Effectively, 'a license is a promise not to sue'.

- The Creative Commons license enables free distribution of copyrighted work.
- Examples: Wikipedia, Open Courseware, Khan Academy, Free Music Archive, 307 million images from Flickr, 39 million images from MusicBrainz, 10 million videos from YouTube, etc.
- Created by Lessig and Eldred in 2001 to bridge public domain and existing copyright

"Many model developers license data for training foundation models"

- Google and Reddit [article: <https://www.reuters.com/technology/reddit-ai-content-licensing-deal-with-google-sources-say-2024-02-22/>]
- OpenAI and Shutterstock [article: <https://investor.shutterstock.com/news-releases/news-release-details/shutterstock-expands-partnership-openai-signs-new-six-year>]
- OpenAI and StackExchange [article: <https://stackoverflow.co/company/press/archive/openai-partnership>]

### Fair use (section 107)

Four factors to determine whether fair use applies:

1. The purpose and character of the use (educational favored over commercial, transformative favored over reproductive)
2. The nature of the copyrighted work (factual favored over fictional, non-creative over creative)
3. The amount and substantiality of the portion of the original work used (using a snippet favored over using the whole work)
4. The effect of the use upon the market (or potential market) for the original work

Examples of fair use:

- You watch a movie and write a summary of it
- Reimplement an algorithm (the idea) rather than copying the code (the expression)
- Google Books index and show snippets (Authors Guild v. Google 2002-2013)

"Copyright is not about verbatim memorization"

- Plots and characters (e.g., Harry Potter) can be copyrightable
- Parody is likely fair use

"Copyright is about semantics (and economics)"

Considerations for foundation models:

- Copying data (first step of training) is violation already even if you don't do anything with it.
- Training an ML model is transformative (far from just copy/pasting)
- ML system is interested in idea (e.g., stop sign), not in the concrete expression (e.g., exact artistic choices of a particular image of a stop sign).

"Problem: foundation models can definitely affect the market (writers, artists), regardless of copyright"

### Terms of service

- Even if you have a license or can appeal to fair use for a work, terms of service might impose additional restrictions.
- Example: YouTube's terms of service prohibits downloading videos, even if the videos are licensed under Creative Commons.

### Memorization versus extraction

- Memorization: is the text *in* the model weights?
- To operationalize, look at `p(book[i] | book[1:i-1])`
- Llama 3 70B assigns much higher (than chance) probability to Harry Potter [link: *Extracting memorized pieces of (copyrighted) books from open-weight language models*, Cooper, Gokaslan, Ahmed et al., 2025, <https://arxiv.org/abs/2505.12546>]

Figure: `llama3-memorization.png` (width 600), a grid of fifteen heat-strip panels: three columns, one per book, *M. Butterfly*, *Beloved* and *Harry Potter and the Sorcerer's Stone*; five rows, one per model, top to bottom **Pythia 12B**, **Llama 1 13B**, **Llama 1 65B**, **Llama 2 70B** and **Llama 3.1 70B**. The x-axis of each column is "Book location (character)", running from 0 to the book's length, printed as 125325 for *M. Butterfly*, 533985 for *Beloved* and 459921 for *Harry Potter*. Each vertical line in a strip marks a place in the book, coloured on a log-scale blue colour bar labelled "Max. suffix prob., $p_z$ (filtered to $p_z \geq$ 0%)", with ticks 0%, 0.1%, 1%, 10% and 100%; white means below 0.1%. For *M. Butterfly*, every model has one dark line near the start of the book and the strips are otherwise nearly empty (Llama 3.1 70B adds lines near 27,000 and 110,000). For *Beloved*, Pythia 12B and Llama 1 13B are almost blank, and the lines grow denser from Llama 1 65B through Llama 2 70B to Llama 3.1 70B. For *Harry Potter*, Pythia 12B shows almost nothing, Llama 1 13B a few faint lines, Llama 1 65B and Llama 2 70B many lines across the whole book, and the Llama 3.1 70B strip is almost solid dark blue from start to end.

- Extraction: user is able to actually extract the text from the model weights
- Prompted with *Mr. and Mrs. D* results in LM generating all of Harry Potter (with sliding window)
- Extraction (especially if it's easy) is a stronger case for infringement

The printed bullet names "Llama 3 70B"; the figure's row is labelled "Llama 3.1 70B".

Further reading:

- [CS324 course notes](https://stanford-cs324.github.io/winter2022/lectures/legality/)
- Fair learning [[Lemley & Casey](https://texaslawreview.org/fair-learning/)]
- Foundation models and fair use [link: *Foundation Models and Fair Use*, Henderson, Li, Jurafsky, Hashimoto, Lemley, Liang, 2023, <https://arxiv.org/pdf/2303.15715>]
- The Files are in the Computer [link: *The Files are in the Computer: On Copyright, Memorization, and Generative AI*, Cooper, Grimmelmann, 2024, <https://arxiv.org/abs/2404.12590>]

---

## Section: Openness and transparency (`openness_and_transparency`)

"Think beyond what a model should do..."
"Who can make decisions about a model's behavior?"
"Who can build a model?"

"Risk: **centralization of power**"
"Very few big tech companies can build frontier models..."
"...and very little is revealed about how they work."

### Transparency

- Transparency is a prerequisite (if you can't measure it, you can't improve it)
- Foundation Models Transparency Index (FMTI) evaluates model developers on transparency [link: *The 2024 Foundation Model Transparency Index*, Bommasani, Klyman, Kapoor et al., 2024, <https://arxiv.org/abs/2407.12929>]
- 100 indicators capturing upstream, model, and downstream properties

Figure: the FMTI May 2024 subdomain heat map (from crfm.stanford.edu, width 600), titled "Foundation Model Transparency Index Scores by Major Dimensions of Transparency, May 2024", "Source: May 2024 Foundation Model Transparency Index". Rows are the thirteen "Major Dimensions of Transparency"; columns are fourteen developers, shown by logo, with their flagship model. Cells are coloured from dark red (low) through pale pink and grey to dark blue (high). Every cell:

| Dimension | Adept Fuyu-8B | AI21 Labs Jurassic-2 | Aleph Alpha Luminous | Amazon Titan Text Express | Anthropic Claude 3 | ServiceNow / Hugging Face StarCoder | Google Gemini 1.0 Ultra | IBM Granite | Meta Llama 2 | Microsoft Phi-2 | Mistral AI Mistral 7B | OpenAI GPT-4 | Stability AI Stable Video Diffusion | Writer Palmyra-X | Average |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Data | 0% | 60% | 40% | 0% | 10% | 100% | 0% | 60% | 40% | 40% | 20% | 20% | 40% | 50% | 34% |
| Labor | 0% | 43% | 71% | 14% | 14% | 100% | 29% | 43% | 29% | 100% | 100% | 14% | 100% | 43% | 50% |
| Compute | 14% | 86% | 100% | 0% | 14% | 100% | 14% | 100% | 71% | 57% | 14% | 14% | 43% | 86% | 51% |
| Methods | 0% | 100% | 100% | 50% | 75% | 100% | 75% | 100% | 75% | 100% | 100% | 50% | 75% | 100% | 79% |
| Model Basics | 83% | 100% | 100% | 83% | 50% | 100% | 83% | 100% | 100% | 100% | 100% | 50% | 100% | 100% | 89% |
| Model Access | 100% | 67% | 100% | 67% | 67% | 100% | 67% | 67% | 100% | 100% | 100% | 67% | 100% | 33% | 81% |
| Capabilities | 80% | 80% | 100% | 80% | 100% | 100% | 80% | 60% | 100% | 100% | 100% | 100% | 60% | 100% | 89% |
| Risks | 0% | 57% | 57% | 43% | 86% | 100% | 43% | 71% | 71% | 29% | 14% | 57% | 14% | 14% | 47% |
| Mitigations | 0% | 40% | 20% | 20% | 40% | 0% | 40% | 80% | 60% | 0% | 60% | 60% | 0% | 20% | 31% |
| Distribution | 57% | 86% | 100% | 57% | 86% | 100% | 57% | 86% | 71% | 71% | 71% | 71% | 86% | 71% | 77% |
| Usage Policy | 40% | 100% | 100% | 80% | 100% | 100% | 100% | 40% | 40% | 100% | 40% | 80% | 60% | 80% | 76% |
| Feedback | 67% | 100% | 67% | 33% | 33% | 100% | 67% | 67% | 33% | 67% | 67% | 33% | 67% | 33% | 60% |
| Impact | 29% | 29% | 29% | 0% | 14% | 14% | 29% | 0% | 14% | 0% | 14% | 14% | 14% | 14% | 15% |
| **Average** | **36%** | **73%** | **76%** | **41%** | **53%** | **86%** | **53%** | **67%** | **62%** | **66%** | **62%** | **49%** | **58%** | **57%** | |

The StarCoder column's logos are a pink square logo, the Hugging Face emoji logo and "servicenow". Both average margins are plain means of the printed percentages: every row's "Average" is the mean of its fourteen cells, and every column's "Average" the mean of its thirteen, to within rounding (checked for all 27). The column averages therefore differ from the total scores in the next figure, which count indicators; for example Fuyu-8B averages 36% here and scores 33% there, and Mistral 7B 62% here and 55% there. The lowest dimension on average is Impact (15%), then Mitigations (31%) and Data (34%); the highest are Model Basics and Capabilities (89% each).

"Overall scores:"

Figure: the FMTI May 2024 total scores (from crfm.stanford.edu, width 600), titled "Foundation Model Transparency Index Total Scores, May 2024", "Source: May 2024 Foundation Model Transparency Index", a horizontal bar chart in one colour (purple bars on grey tracks) with columns "Company" (logo), model name, and "Score":

| Company | Model | Score |
| --- | --- | --- |
| BigCode / Hugging Face / ServiceNow (logos) | StarCoder | 85% |
| AI21 Labs | Jurassic-2 | 75% |
| Aleph Alpha | Luminous | 75% |
| IBM | Granite | 64% |
| Microsoft | Phi-2 | 62% |
| Meta | Llama 2 | 60% |
| Stability AI | Stable Video Diffusion | 58% |
| Writer | Palmyra-X | 56% |
| Mistral AI | Mistral 7B | 55% |
| Anthropic | Claude 3 | 51% |
| OpenAI | GPT-4 | 49% |
| Google | Gemini 1.0 Ultra | 47% |
| Amazon | Titan Text Express | 41% |
| Adept | Fuyu-8B | 33% |

"Theory of change: public reporting incentivizes companies to be more transparent"

Figure: the FMTI comparison chart (from crfm.stanford.edu, width 600), titled "Foundation Model Transparency Index Scores by Developer, October 2023 vs. May 2024", "Source: May 2024 Foundation Model Transparency Index". A grouped horizontal bar chart with two series, per the legend at the lower right: **May 2024** (purple, the upper bar of each pair) and **October 2023** (steel blue, the lower bar). The x-axis is "Score", 0 to 100 in steps of 10. Eight developers, each pair labelled with its values:

| Developer | May 2024 | October 2023 |
| --- | --- | --- |
| BigCode/HF/ServiceNow | 85 | 53 |
| AI21 Labs | 75 | 25 |
| Meta | 60 | 54 |
| Stability AI | 58 | 47 |
| Anthropic | 51 | 36 |
| OpenAI | 49 | 48 |
| Google | 47 | 40 |
| Amazon | 41 | 12 |

Every developer's score rose; the largest rises are AI21 Labs (+50), BigCode/HF/ServiceNow (+32) and Amazon (+29), the smallest OpenAI (+1).

### Openness

"Openness of foundation models lies on a spectrum:"

Figure: `openness.png` (width 600), four adjoining boxes left to right, shading from red to pale peach, the last outlined in a thick black border:

| Closed | Open-weight | Open-source | Open-development |
| --- | --- | --- | --- |
| API-only | see weights | see weights; see code + data recipe | see weights; see code + data recipe; see process, can contribute |
| *(GPT-5, Claude, Gemini)* | *(Llama, DeepSeek)* | *(GPT-J, OLMo)* | ***(Marin)*** |

Why is openness important? [link: *On the Societal Impact of Open Foundation Models*, Kapoor, Bommasani, Klyman et al., 2024, <https://arxiv.org/pdf/2403.07918v1>]

Benefits:

1. Increased innovation and customizability for researchers and developers
2. Increased transparency (though not enough)
3. Reducing centralization of power

Misuse risks:

- Think about *marginal risk* over alternatives (closed models, Internet)
- Think about the whole ecosystem (design + manufacture + deploy a bioweapon)

"Overall: need more clarity and measurement"

---

## Section: Summary (`main`)

Summary:

- Technologists should care about societal impact
- Challenges: AI is dual use technology, lots of uncertainty, making it challenging
- Focus on beneficial applications, deter misuse, and prevent accidents
- Think about the whole ecosystem, not just the model
- Need to monitor multiple metrics and inequality
- Alignment is hard: reward hacking, pluralism, scalable oversight
- Openness and transparency as a basic foundation
- Auditing as a powerful tool

"Next time: a deeper look at the players in the AI ecosystem"

---

## Figures

All eighteen images were read directly by the parent model (claude-opus-5-5) on 2026-10-25, from the files at commit 1b5bd22. The two FMTI tables were then checked against full-resolution crops, and every one of the subdomain heat map's 182 cells reconciles with both of its printed average margins. The animated GIF was read as four frames (0, 14, 28 and 42 of 56).

| Image | Section | Size (px) | Source |
| --- | --- | --- | --- |
| ChatGPT logo | Why care | 1600 × 900 | Wikimedia Commons (`var/files/` cache) |
| `wernher-von-braun.jpg` | Why care | 330 × 421 | `images/` |
| `v2-rocket.png` | Why care | 1200 × 1200 | `images/` |
| `belmont-report.jpeg` | Principles | 700 × 904 | `images/` |
| `acm-code-of-ethics.png` | Principles | 960 × 392 | `images/` |
| `intent-versus-impact.png` | Benefits, misuse, accidents | 877 × 371 | `images/` |
| `upstream-downstream.png` | Ecosystem view | 780 × 809 | `images/` |
| `gender-shades.jpg` | Inequality | 635 × 537 | `images/` |
| `llm-global-representation.png` | Inequality | 1082 × 516 | `images/` |
| `chest-drain.png` | Inequality | 1443 × 560 | `images/` |
| `chest-drain-results.png` | Inequality | 459 × 444 | `images/` |
| `reward-hacking-boat.gif` | Alignment | 478 × 360, 56 frames | `images/` |
| `pluralism.png` | Alignment | 809 × 878 | `images/` |
| `llama3-memorization.png` | Copyright | 1895 × 628 | `images/` |
| FMTI subdomain scores | Openness and transparency | 3348 × 1424 | crfm.stanford.edu (`var/files/` cache) |
| FMTI total scores | Openness and transparency | 2094 × 1322 | crfm.stanford.edu (`var/files/` cache) |
| FMTI October 2023 vs. May 2024 | Openness and transparency | 2956 × 1572 | crfm.stanford.edu (`var/files/` cache) |
| `openness.png` | Openness and transparency | 2080 × 355 | `images/` |

The same commit added `levels-of-abstraction.png` and `microsoft-responsible-ai.png` to `images/`, but no program at that commit shows them, so they were not part of the lecture and are not described here.
