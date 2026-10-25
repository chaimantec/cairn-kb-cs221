---
title: Lecture 19 — Economics of AI (Slides)
lecture: 19
source_format: google-slides-deck
slides: 70 PDF pages; the deck prints no slide numbers, so slide N is PDF page N
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_commit: f700637 (2025-12-01, "update Rishi"; README entry only)
source_files:
  - https://docs.google.com/presentation/d/1jCn1OV4H1HKzQ0PWzRn2_bfOWKw43eS33wyramBt2z8/edit
note: >
  Lecture 19 is a guest lecture, given on Mon Dec 1 2025 by Rishi Bommasani
  (slide 1) and introduced by Percy Liang. The course schedule lists it as
  "AI Supply Chains"; the deck is titled "CS 221 - Economics of AI". It has
  no program and no PDF in the lecture repo. The repo's only record of it is
  commit f700637 ("update Rishi", Percy Liang, Mon Dec 1 2025 17:48 PST,
  after the 1:30pm lecture), which adds the README entry "Mon Dec 1:
  Economics of AI (lecture 19)" linking the Google Slides document above.
  A Google Slides document is live, not pinned: this file was transcribed
  from a PDF export taken on 2026-10-26, and the deck may have been edited
  after the lecture. The transcript follows these slides in order from
  slide 2 to slide 69, and every slide the lecturer describes is present,
  so the export is taken as what was shown; differences between what was
  said and what is printed are noted on the wiki page. Every page was read
  as an image on 2026-10-26 by claude-sonnet-5-5 in three parallel parts
  (pages 1-24, 25-47, 48-70), and the chart-, table- and diagram-heavy
  pages were then audited against 600 dpi renders (see "Figure audit" at
  the end).
---

# Lecture 19 Slides — Economics of AI

Text and figures of the Google Slides deck for lecture 19, transcribed page by page. Diagrams, charts, screenshots and photographs are described in prose, since this file is the only representation of them in the knowledge base.

**Slide numbers vs PDF pages.** The deck prints no slide numbers, so **slide N is PDF page N** of the export throughout, and the headings below use the page number. Several runs of consecutive pages are *builds*, incremental reveals of one slide; each build page is transcribed as its changes from the page before ("Build of slide K").

The deck reproduces many third-party figures: charts from papers by Brynjolfsson and colleagues, Longpre et al. (2023), METR, Epoch AI, Our World in Data and Chad Jones; screenshots of the first pages of Bresnahan and Trajtenberg's paper and of the foundation-models report; a supply-chain map; a news page about the Anthropic copyright settlement; a Wall Street Journal opinion page; photographs of people; and book and report covers. They are described here, not reproduced.

Companion pages: [wiki page for this lecture](../../wiki/19-economics-of-ai.md) · [transcript](../transcripts/19-economics-of-ai.md)

## Contents

| Slides | Section |
| ------ | ------- |
| 1 | Title (Rishi Bommasani, Stanford CS 221) |
| 2–4 | Questions in the economics of AI: the "Mag 7" share of the S&P 500's market capitalization; software-developer headcount by age group; the effect of an AI tool on call-centre agents by tenure |
| 5 | Outline: (1) the dual lens of technologies and organizations, (2) current supply chains, (3) growth economics |
| 6–10 | Technology and organizations: why organizations matter; the Artificial Analysis Intelligence Index; "Google = Anthropic = OpenAI" in capability, but not in their other choices |
| 11–12 | Supply chains: the foundation-model supply chain, from the data and compute layers to affected persons |
| 13–19 | Compute: a data center; the AI semiconductor value chain; ASML, TSMC and Nvidia; why it matters (resilience, who accrues value, geopolitics); cloud market share; a data-center construction site |
| 20–25 | Data: acquisition methods; web-crawling restrictions over time (Longpre et al. 2023); pricing by acquisition method; the Anthropic copyright settlement; why it matters (running out of data, compliance, competition) |
| 26–28 | Distribution: the spectrum of model access from fully closed to fully open; how distribution shapes the downstream supply chain |
| 29 | Questions? |
| 30–33 | Growth: reading list; micro vs. macro; scope |
| 34–44 | The magnitude of AI's impact: the technological Richter scale (Nate Silver); LEAP forecasts; forecasts labelled on the scale, from modest to utopia and doom; "normal technologies" |
| 45–49 | General-purpose technologies (Bresnahan and Trajtenberg); is AI a GPT, and is it governed by GPT principles; the three characteristics (Bommasani, …, and Liang 2021) |
| 50–58 | The three characteristics for AI: pervasiveness (ChatGPT users, usage share by occupation); improvements over time (METR time horizons, Epoch AI prices); innovation complementarities (Brynjolfsson on electricity) |
| 59 | The productivity J-curve of GPTs (Brynjolfsson et al. 2018) |
| 60–64 | GDP: global GDP over the long run; US GDP per person at 2.0% a year (Chad Jones 2023); "the computer age everywhere but in the productivity statistics"; why Internet-era GPTs do not show up; GDP-B and consumer surplus |
| 65–69 | How AI might affect the economy: one sector made super productive (Baumol's cost disease); the production function; an injection of cheap labor; new ideas (Romer) |
| 70 | Questions? |

## Slide 1 — Economics of AI

**Economics of AI**

Rishi Bommasani

Stanford CS 221

(Plain white title slide, centred text, no figures.)

## Slide 2 — Health of the world economy

A line chart (a Reuters/LSEG-style graphic) with chart title "**Concentration risk and the Mag 7**" and subtitle "Over a third of the S&P 500's market capitalization comes from seven stocks collectively." Legend (in this order, as printed): Microsoft, NVIDIA, Alphabet, Meta, Amazon, Apple, Tesla, Amazon. The legend repeats "Amazon" twice (once purple, once orange-red); the second, orange-red "Amazon" swatch is presumably a labelling slip, since the orange-red marker appears only as a dot at the start of the top line (see below).

Axes: x-axis is time, 2015 to 2025 (ticks every year, 2015 through 2025, data running to about late 2025). Y-axis is a percentage, 0 to 40% (ticks at 0, 5, 10, ..., 35, 40%). The y-axis has no title; the subtitle implies it is share of S&P 500 market capitalization. The lines are cumulative (stacked): each line is the running total of the share of the companies up to and including it, and the region under the top line is shaded light grey. Seven stacked lines, from bottom to top, with approximate end values (late 2025):

- Bottom, purple (Microsoft): about 2% in 2015 (almost hidden beneath the blue NVIDIA line until about 2020), about 6.7% at the end.
- Blue (NVIDIA): from about 2% in 2015 to about 14.5% at the end, with its increments rising sharply after 2023 (NVIDIA's own band widens from about 1% to about 8%).
- Yellow (Alphabet): about 4% in 2015, about 19.5% at the end.
- Brown (Meta): about 5% in 2015, about 22% at the end.
- Purple/magenta (Amazon): about 6% in 2015, about 26.5% at the end.
- Green (Apple): about 9.5% in 2015, about 33% at the end.
- Top, dark grey-brown (Tesla): starts near 9.5% (marked with an orange-red dot at the left edge in 2015), ends at **35.34%** (labelled at the right end of the top line).

The top line passes about 17.5% in 2019, about 27% in late 2020, about 29% in 2021 to 2022, a dip to about 20% in late 2022, and about 35% by 2025. Each line ends in a coloured dot at the right.

Source line: "Source: LSEG | Dhara Ranasinghe".

## Slide 3 — Future of work

A multi-line chart titled "Headcount Over Time by Age Group / Software Developers (Normalized)". X-axis labelled "Date", ticks 2021-04, 2021-10, 2022-04, 2022-10, 2023-04, 2023-10, 2024-04, 2024-10, 2025-04 (rotated). Y-axis (unlabelled) from about 0.8 to 1.2 (ticks 0.8, 0.9, 1.0, 1.1, 1.2). A vertical dashed black line sits at about 2022-10 (where all lines are normalized to 1.0). Six lines, with legend (top to bottom): "Early Career 1 (22-25)" blue; "Early Career 2 (26-30)" orange; "Developing (31-34)" green; "Mid-Career 1 (35-40)" red; "Mid-Career 2 (41-49)" purple; "Senior (50+)" brown.

Before the dashed line all six lines rise together from about 0.87 (2021-01) to 1.0 (blue is a bit higher in 2021-10 at about 1.0). After the dashed line:

- Early Career 1 (22-25), blue: falls steadily to about 0.8 by 2025-04 to 2025-06 (approx. 0.82 at the last point).
- Early Career 2 (26-30), orange: drifts down to about 0.95 to 0.96.
- Developing (31-34), green: rises to about 1.08 around 2024-04, ending about 1.07.
- Mid-Career 1 (35-40), red: rises to about 1.05, ending about 1.07.
- Mid-Career 2 (41-49), purple: rises to about 1.10 at the end.
- Senior (50+), brown: peaks at about 1.08 around 2024-04 to 2024-07, ending about 1.04.

Citation printed under the chart: "Brynjolfsson et al. (2025)".

## Slide 4 — Inequality

A dot-and-whisker chart titled (small caps) "Impact of AI on Resolutions Per Hour, by Tenure at Deployment". Y-axis "Change in Log(RPH)" from -.1 to .4 (ticks -.1, 0, .1, .2, .3, .4), with a dashed horizontal line at 0. X-axis "Agent Tenure at AI Deployment" with five categories: "0 Mos.", "1-2 Mos.", "3-6 Mos.", "7-12 Mos.", ">12 Mos.". One series of blue diamond point estimates with vertical error bars (approximate values):

- 0 Mos.: about 0.38 (error bar about 0.31 to 0.45)
- 1-2 Mos.: about 0.21 (about 0.17 to 0.25)
- 3-6 Mos.: about 0.18 (about 0.11 to 0.24)
- 7-12 Mos.: about 0.12 (about 0.08 to 0.17)
- >12 Mos.: about -0.02 (about -0.07 to 0.02; the interval spans zero)

Citation printed under the chart: "Brynjolfsson et al. (2023)".

## Slide 5 — Outline

1. The dual lens: technologies and organizations
2. Current supply chains
3. Growth economics

## Slide 6 — Technology and Organizations

(Section-divider slide: only the centred title "Technology and Organizations".)

## Slide 7 — Why we need to think about organizations?

- Overall economic impact depends heavily on non-AI orgs
- Tech alone doesn't say who wins and loses
- Even model developers have lots of other choices

## Slide 8 — Current capabilities

A bar chart (a screenshot from Artificial Analysis) headed "INTELLIGENCE" with subtitle "Artificial Analysis Intelligence Index; Higher is better". No y-axis labels, only light horizontal gridlines. Twelve vertical rounded bars, one per model, in descending order, each with a company logo beneath and a rotated model name (each name is followed by a small lightbulb-like icon). Value printed in white inside each bar (colours are the company's):

1. Gemini 3 Pro Preview (high), Google logo, green bar: 73
2. Claude Opus 4.5, Anthropic logo, brown/terracotta bar: 70
3. GPT-5.1 (high), OpenAI logo, black bar: 70
4. Kimi K2 Thinking, Moonshot (Kimi) logo, blue bar: 67
5. Grok 4, xAI logo, purple bar: 65
6. Grok 4.1 Fast, xAI logo, purple bar: 64
7. Claude 4.5 Sonnet, Anthropic logo, brown bar: 63
8. gpt-oss-120B (high), OpenAI logo, black bar: 61
9. Qwen3 235B A22B 2507, Alibaba logo, orange bar: 57
10. DeepSeek V3.2 Exp, DeepSeek logo, dark blue bar: 57
11. Gemini 2.5 Flash (Sep), Google logo, green bar: 54
12. Llama 4 Maverick, Meta logo, light blue bar: 36

(The first label is partly cut off at the left edge of the slide, reading "Gemini 3 Pro Preview (high)"; the label text overlaps with the second label.)

## Slide 9 — [Current capabilities, with conclusion]

Build of slide 8: the same Artificial Analysis Intelligence Index bar chart ("INTELLIGENCE", "Artificial Analysis Intelligence Index; Higher is better"; twelve bars with the same values: Gemini 3 Pro Preview (high) 73, Claude Opus 4.5 70, GPT-5.1 (high) 70, Kimi K2 Thinking 67, Grok 4 65, Grok 4.1 Fast 64, Claude 4.5 Sonnet 63, gpt-oss-120B (high) 61, Qwen3 235B A22B 2507 57, DeepSeek V3.2 Exp 57, Gemini 2.5 Flash (Sep) 54, Llama 4 Maverick 36) is shifted to the left and the slide title is gone. At the right, in large grey text:

Conclusion:
Google = Anthropic = OpenAI

## Slide 10 — Lots of other choices and context!

Left, bullet list:

- When to release model
- How to price
- Which products to build
- Who to partner with

Right, smaller grey text:

Reality
Google != Anthropic != OpenAI

## Slide 11 — Supply Chains

(Section-divider slide: only the centred title "Supply Chains".)

## Slide 12 — [diagram: AI supply chain layers, upstream to downstream]

A vertical staircase/chevron diagram of the foundation-model supply chain, with a double-headed vertical arrow on the far left labelled "UPSTREAM" (top) and "DOWNSTREAM" (bottom). Seven stacked boxes step from top to bottom, alternating left and right of a central chevron spine, each with a "WHAT" and "WHO" description (small print). Colours go from pale blue/pink at the top to red and then white at the bottom.

1. **DATA LAYER** (pale blue, top left). WHAT: Gathers data at scale. WHO: Company collecting/processing data.
2. **COMPUTE LAYER** (pale pink, top right). WHAT: Provides processing power for training. WHO: Company providing computational resources.
3. **FOUNDATION MODEL DEVELOPER LAYER** (blue-grey, right, below the first two). WHAT: Designs foundation model and trains foundation model using data and compute. WHO: Foundation model company. Five sub-items listed to the right of this box in small capitals: DATA CLEANING AND ANNOTATION; PRE-TRAINING A MODEL; FINE-TUNING A MODEL; ADDING SAFEGUARDS; INTERNAL TESTING.
4. **HOST LAYER** (pink, left). WHAT: Hosts and makes models available to downstream users via download or API access. WHO: In-house at foundation model company or cloud service provider.
5. **APPLICATION LAYER** (blue-grey, right). WHAT: Builds and releases an application incorporating the foundation model or a fine-tuned version. WHO: Application developer (may be same as foundation model company or a third party).
6. **APPLICATION USER** (red, left). WHAT: Uses application. WHO: End users (public, organisations, companies, etc.).
7. **AFFECTED PERSONS** (white outlined box, bottom right). WHAT: Communities impacted by the use of a foundation model or application. WHO: Can include service users e.g., patients or job applicants, people represented in the outputs of a foundation model, or people whose jobs may change as a result of model use for certain tasks. This can also include impacts on society such as the environmental costs of manufacturing components for foundation models or the resources required for compute.

(The Data layer's WHAT/WHO text sits to the left of its box and the Compute layer's to the right; for the Foundation Model Developer, Host, Application, Application User and Affected Persons boxes the text sits on the opposite side of the staircase from the box. The pairing above follows the printed layout and was checked at 600 dpi.)

Right-hand brackets group the layers into phases: "DEVELOPMENT" (spanning the Data, Compute and Foundation Model Developer layers), "PRE-DEPLOYMENT" (from the "INTERNAL TESTING" line through the Host and Application layers), and "POST-DEPLOYMENT" (spanning Application User and Affected Persons). The Development bracket ends after "ADDING SAFEGUARDS"; the Pre-Deployment bracket starts at "INTERNAL TESTING" and ends at the bottom of the Application Layer box.

No citation is printed on the slide.

## Slide 13 — Compute

(Section-divider slide: only the centred title "Compute".)

## Slide 14 — [photo: Nvidia data center]

A full-slide rendered image (looks like a computer-generated or marketing image) of a data-center hall in perspective: rows of white server racks with ventilated doors on both sides of a metal grated-floor aisle, a white-tiled floor, and a ceiling with cable trays. In the foreground a white door/panel carries the black Nvidia logo (eye mark) and the word "NVIDIA". No other text, no caption.

## Slide 15 — [diagram: AI Semiconductor Value Chain]

A grey infographic titled "**AI Semiconductor Value Chain**" with a small logo (a stylised "V" mark) at the top right. It is organised into three dashed columns, "Design", "Manufacturing" and "Chip", each containing white boxes of company logos, with arrows showing flow. Footnote at the bottom: "*Not an exhaustive list of companies/segments. Companies may fall into multiple segments. Several inference-focused chips can be used for training."

**Design column (left)**
- Box "Co-designer": Broadcom, Marvell. An arrow goes down to
- Box "Chip Designer": Intel, AMD, AWS, Google, Nvidia, Microsoft.
- Two boxes feed up into Chip Designer through a joined arrow: "EDA" (Synopsys, Cadence, Siemens) and "IP" (ARM, Synopsys, Cadence).

**Manufacturing column (middle)**
- Dotted group "Wafer Fab Equipment" with four boxes: "Litho" (ASML); "Deposition" (Applied Materials, as its logo mark only, without the wordmark it carries in the lower Packaging box; Lam Research, TEL, ASM); "Process" (KLA, Camtek, Onto); "Etch" (Lam Research, TEL). An arrow runs from this group down to Foundries.
- Box "Foundries": Intel, TSMC, Samsung. An arrow goes right to
- Box "Packaging": ASE Group, TSMC, Samsung, JCET, Amkor Technology, Intel.
- Dotted group "Other Semicap & Equipment" with four boxes: "Ion Implant/CMP" (Applied Materials); "Packaging" (Besi, Applied Materials); "Materials" (Entegris, DuPont, Shin-Etsu, SUMCO); "Testing" (Advantest, TER). An arrow runs up from this group to Foundries.
- A grey label "Generative Value" sits between Packaging and the lower equipment group, with no arrow.

**Chip column (right)** (arrows from Packaging fan out to all three boxes)
- Box "GPUs & ASICs": Google, Nvidia, AMD, Intel, Microsoft, Meta, SambaNova, AWS, Cerebras, Graphcore.
- Box "Inference-Focused": Tenstorrent, Groq, d-Matrix, Furiosa, Recogni, NeuReality, Fractile; and a sub-heading "Model-Focused Silicon": Taalas, Etched, MatX.
- Box "Edge": Google, Qualcomm, Nvidia, Apple, Hailo, Untether AI, SiMa.ai, Intel, Blaize, AMD.

## Slide 16 — Critical companies

- ASML: Global monopoly on extreme UV lithography
  - \$400B valuation (most valuable European company)
- TSMC: The top manufacturer of advanced chips
  - \$1.5T valuation (most valuable Asian company)
- Nvidia: Dominant market share of chips market
  - \$4.4T valuation (most valuable company)

## Slide 17 — Why does this matter?

- Supply chain resilience
- Who accrues value from growth of AI
- Geopolitics (US-China)
  - TSMC (Taiwan)
  - Chip export controls

## Slide 18 — Compute service providers (major clouds)

A line chart titled "**Cloud Provider Market Share Trend**" with subtitle "(IaaS, PaaS, Hosted Private Cloud)". Y-axis "Share of Worldwide Revenues", 0% to 35% (ticks every 5%). X-axis quarterly, from "Q4 18" to "Q2 24" (labels Q4 18, Q1 19, Q2 19, Q3 19, Q4 19, Q1 20, ..., Q4 23, Q1 24, Q2 24). Source line at the bottom right: "Source: Synergy Research Group". Eight series, each labelled at the right end by name or logo (values approximate, first quarter Q4 18 to last quarter Q2 24):

- Amazon (logo, orange line): about 34% at Q4 18; stays between about 31% and 34%, ending about 31.5%.
- Others (grey dashed line): about 27% at Q4 18, declining to about 22.5% at the end; it is about 25% in 2020 and about 23% in 2022. Microsoft first overtakes Others at Q4 22; the two lines are within about a point of each other and swap places through Q3 23, and Microsoft is clearly above from Q4 23 (about 24.4% against 22.3%).
- Microsoft (logo, light blue line): about 15.5% at Q4 18, rising to about 24.5% in Q1 24, ending about 23.5%.
- Google (logo, green line): about 7% at Q4 18, rising steadily to about 12% at the end.
- Alibaba (orange text label, orange line): about 4.5% at Q4 18, about 5 to 6% through 2020 to 2021, falling to about 3.5% at the end.
- Oracle (red text label, red line): about 2% throughout, about 2.3% at the end (label order at right: Alibaba, Oracle, Salesforce, IBM).
- Salesforce (black text label, black line): about 3.5% at Q4 18, falling to about 2.5% at the end.
- IBM (blue-grey text label, blue-grey line): about 7% at Q4 18 (same start as Google), falling to about 2.5% at the end.

The last four lines converge at roughly 2.3 to 3.7% by Q2 24 (Alibaba about 3.5%, the others about 2.5%).

## Slide 19 — Compute is more than just chips

A full-width screenshot of an interactive satellite-imagery map, beneath the slide title. Across the top is a toolbar with tabs "Notes", "Graphs", "Info" and a teal timeline bar with dots running from 2024 to 2026 (year labels "2024", "2025", "2026"); the selected date at the right reads "September 26, 2025", with previous/next arrows.

Left "NOTES" panel (white, with a close X): "Anthropic-Amazon New Carlisle is part of the Anthropic-Amazon Project Rainier collaboration. It is similar to other Amazon locations, forgoing most external cooling infrastructure in favor of direct air based cooling." "View our calculations here." "Sources" with three links: "Air Construction Permit application for the northern portion"; "Backup diesel generator referenced in the permit"; "Announcement of Project Rainier coming online". "Read more about our methodology."

Top-right panel "Construction status": "Roof complete on building 16. Generators are being installed for buildings 8-15. In the north, Building 17 and 18 are under construction. To the south of buildings 8-16, a concrete pad is visible, and an expanded car park next to it."

The map is a dark aerial view of farmland (green and brown fields) with a data-center campus near the centre: several long, narrow rectangular buildings outlined in teal and numbered with circled numbers (for example 1, 2, 5 and 3, 6 in the upper cluster, 17 and 18 as small outlined buildings, and further numbered buildings in a southern cluster), with orange outlines marking areas such as a pad/yard. A road label "Zeigler" appears at the top. A legend bar at the bottom reads "Building" (teal), "Grid Power" (orange-red), "On-site Power" (magenta), "Backup Power" (purple), "Cooling" (blue), and "Hide all overlays". Zoom buttons (+ and -) at the bottom right. No source citation is printed on the slide beyond the interface text.

## Slide 20 — Data

(Section-divider slide: only the centred title "Data".)

## Slide 21 — Data acquisition methods

- Data within the firm
  - Synthetic data (e.g. RLAIF)
  - Usage data (e.g. ChatGPT interactions, Gmail)
- Data that is public
  - Public datasets (e.g. SQuAD, The Pile)
  - Web crawling
- Data that third parties have
  - New data (e.g. Scale, Mercor)
  - Old data (e.g. New York Times, Reddit)

## Slide 22 — [chart: web data restrictions over time]

No printed title. A figure with three stacked panels, each with its own title and legend printed below it, all on a shared x-axis from 2016 to 2025 (ticks each year). The part after about mid-2024 is a forecast: in panels 1 and 2 a vertical divider with lighter fill and the label "Forecast", in panel 3 a grey shaded box. Vertical black lines mark events labelled in boxes. "ChatGPT" (about Dec 2022), "GPT-4" (about Mar 2023), "GPTBot" (about Aug 2023) and "G-Ext." (about Oct 2023) appear in all three panels; "GDPR Ad." (about Apr 2016) and "GDPR Eff." (about mid-2018) appear only in the middle panel. Citation under the figure: "Longpre et al. (2023)".

**Panel 1 (top), stacked-area chart of Robots.txt restrictions**, y-axis 0% to 100% (share of sites/tokens, unlabelled axis). Legend (printed below this panel, under the heading "Robots.txt Restrictions"): Full restrictions (dark red), Pattern-based restrictions (red-brown), Disallow private directories (tan/orange-brown), Other restrictions (pale peach), Crawl delay specified (very pale blue), Sitemap provided (light blue), No restrictions or sitemap (mid blue), No Robots.txt (light grey). Trend, bottom to top: light-grey "No Robots.txt" is about 15% of the area in 2016 and shrinks to nearly nothing by about 2019. Mid-blue "No restrictions or sitemap" is about 10% to 12% throughout. The pale "Crawl delay specified" and light-blue "Sitemap provided" bands are about 5% each. The pale-peach "Other restrictions" band is the largest, about 45% to 50% of the area; tan "Disallow private directories" is a very thin line. Red-brown "Pattern-based restrictions" is about 15% in 2016, growing to about 25% by 2023. Dark-red "Full restrictions" is a thin band of about 3% at the top until late 2023, then, after the GPTBot and G-Ext. markers, jumps to roughly 25% to 30% of the area by mid-2024 (from about 72% up to 100%); the forecast region shows about 40% in lighter red. (Corrected by the figure audit: the first reading had "No Robots.txt" at 25-30% shrinking to 8-10%, and "Full restrictions" reaching only 5-8%.)

**Panel 2 (middle), stacked-area chart of ToS (terms of service) restrictions**, y-axis 0% to 100%. Legend (printed below, under the heading "ToS Restrictions"): No Crawling & AI (dark red), No Crawling (orange-tan), No AI (salmon), Non-Commercial Use (pinkish salmon), Non-Compete (pink), No Re-Distribution (pale peach), Conditional Use (pale blue), Unrestricted Use (mid blue), No Terms Pages (light grey). Trend: "No Terms Pages" (grey) is about 85% in 2016, falls to about 55% in 2017, about 40% by 2019, about 20% in 2022 to 2023, and about 10 to 15% by 2024. "Unrestricted Use" (blue) is about 20% to 30% from 2017 to 2021 and about 30% by 2023 to 2024. The salmon/orange restriction bands above them make up about 28% in 2017 (the top of the blue is at about 72%), about 40% by 2019 and about 55% by 2024, with a dark-red band appearing at the top after 2023. The forecast region shows a thin grey sliver (about 8%) at the bottom, a lighter mid-blue Unrestricted Use band up to about 40%, and a larger restriction share above.

**Panel 3 (bottom), line chart "Restrictions by Org. Agent"**, y-axis log-scale percentage from 0.1% to 100% (ticks 0.1%, 0.2%, 1%, 2%, 10%, 20%, 100%), x-axis 2016 to 2025, forecast shown as a grey shaded region with dashed continuations. Legend with each agent's final percentage as printed: OpenAI (25.9%) black; Anthropic (13.3%) light blue; Common Crawl (13.3%) yellow; Google (9.8%) orange-gold; False Anthropic (6.0%) light grey; Cohere (4.9%) green; Meta (4.1%) dark blue; Internet Archive (3.2%) dark orange; Google Search (1.0%) pink/magenta. Nine series. All lines are flat at about 1% to 3% from 2016 to about early 2023 (Internet Archive highest at about 2% to 3%, Google Search lowest at about 0.7% to 1%). Following the GPTBot and G-Ext. markers (mid to late 2023) several lines rise sharply: OpenAI up to about 20% to 25%, Common Crawl to about 12%, Google to about 10%, and Anthropic, Cohere, Meta and the grey line to about 4% to 6%. Internet Archive stays about 3% and Google Search about 1%. Dashed forecast lines extend each series, with OpenAI heading toward about 30% to 35%.

## Slide 23 — Pricing depends on acquisition method

- Data within the firm
  - Synthetic data (pay for compute)
  - Usage data (pay for services)
- Data that is public
  - Public datasets (free)
  - Web crawling (currently free)
- Data that third parties have
  - New data (pay for labor
  - Old data (pay as negotiated large-scale transaction)

(The parenthesis after "pay for labor" is missing a closing bracket on the slide.)

## Slide 24 — [screenshot: Anthropic copyright settlement news article]

Left: a screenshot of a news article. Headline: "Anthropic settles with authors in first-of-its-kind AI copyright infringement lawsuit". Date line: "SEPTEMBER 5, 2025 · 8:19 PM ET". Byline: "Chloe Veltman" with a small round author photo. A photo shows a smartphone in an orange case lying on a laptop keyboard, its screen displaying the "ANTHROPIC" logo with an abstract hand-and-nodes drawing. Photo caption: "A case against Anthropic AI brought by a group of authors was settled on Friday." and a credit in tiny print ("Riccardo Milani/Hans Lucas/AFP via Getty Images"). Article text: "In one of the largest copyright settlements involving generative artificial intelligence, Anthropic AI, a leading company in the generative AI space, has agreed to pay \$1.5 billion to settle a copyright infringement lawsuit brought by a group of authors."

Right, in grey text:

\$1.5B for 500k works
= \$3k/work

## Slide 25 — Why does this matter?

- Will we run out of data?
  - Current data mix, data generating rates, total data
- Compliance
  - Copyright, piracy, CSAM, privacy, ...
- Competition
  - Exclusive access to certain types of data

## Slide 26 — Distribution

Section-divider slide: the single word "Distribution", centred on an otherwise blank page.

## Slide 27 — [table: levels of access to a model]

A screenshot of a table (no slide title; the table is left of centre on a white page). Columns: Level of Access, What It Means, Examples, Traditional Software Analogy. The first four rows' "Level of Access" cells are shaded teal, darkest at the top fading lighter downward; the last three are unshaded.

| Level of Access | What It Means | Examples | Traditional Software Analogy |
| --- | --- | --- | --- |
| Fully Closed | Users cannot directly interact with the model at all | Flamingo (Google) | Trading algorithms used by private hedge funds |
| Hosted Access | Users can only interact through a specific application or interface | Midjourney (Midjourney) | Cloud consumer software (e.g. Google Docs) |
| API Access to Model | Users can send requests to the model programmatically, allowing use in external applications | Claude 3.5 Sonnet (Anthropic) | Cloud-based API (e.g. website builders such as Squarespace) |
| API Access to Fine-Tuning | Users can fine-tune the model for their specific needs | GPT-4o (OpenAI) | Enterprise software with customisation APIs (e.g. Salesforce Development Platform) |
| Open-weight: Weights Available For Download | Users can download and run the model locally | Llama 3 (Meta), Mixtral (Mistral) | Proprietary desktop software (e.g. Microsoft Word) |
| Weights, Data, and Code Available for Download with Use Restrictions | Users can download and run the model as well as the inference and training code, but have certain licence restrictions on their use | BLOOM (BigScience) | Source-available software (e.g. Unreal Engine) |
| Fully Open: Weights, Data, and Code Available for Download with no Use Restrictions | Users have complete freedom to download, use, and modify the model, full code, and data | GPT-NeoX (EleutherAI) | Open source software (e.g. Mozilla Firefox and Linux) |

Caption printed under the table: "Table 2.5: There is a spectrum of model sharing options ranging from fully closed models (models are private and held only for proprietary use) to fully open, open source models (model weights, data, and code are freely and publicly available without restriction of use, modification, and sharing). This section focuses on the three rightmost columns. Source: adapted from Bommasani et al., 2024 (900)." (Checked at 600 dpi.)

## Slide 28 — Distribution shapes downstream SC

- Vertical control
- Pricing
- Data flows (privacy, security)
- Adaptability (fine-tuning, distillation, ...)

## Slide 29 — Questions?

Section-divider slide: the single word "Questions?", centred on an otherwise blank page.

## Slide 30 — Growth

Section-divider slide: the single word "Growth", centred on an otherwise blank page.

## Slide 31 — How will AI affect economic growth?

I was inspired by:

- Bharat Chandar [AI and Labor Markets: What We Know and Don't Know](https://bharatchandar.substack.com/p/ai-and-labor-markets-what-we-know)
- Tom Cunningham [Economics and Transformative AI](https://tecunningham.github.io/posts/2025-09-19-transformative-AI-notes.html)
- Boaz Barak [Thoughts by a non-economist on AI and economics](https://windowsontheory.org/2025/11/04/thoughts-by-a-non-economist-on-ai-and-economics/)
- Phil Trammell [Workflows and Automation](https://philiptrammell.com/static/Workflows_and_Automation.pdf)
- Pascual Restrepo [We wont be Missed: Work and Growth in the AGI Era](https://www.nber.org/system/files/chapters/c15315/c15315.pdf)

(Each title is a link, underlined in teal on the slide; the URLs are taken from the PDF's link annotations. "We wont" is as printed.)

Folks at Stanford: Erik Brynjolfsson, Chad Jones, Bharat, Phil

## Slide 32 — micro vs macro

- Micro econ: Individual decisions
- Macro econ: Whole economy
- Computer science: predominantly micro orientation

## Slide 33 — Scope

- AI = Frontier AI as currently understood
  - Not in scope: Waymo, industrial robots, FB rec sys
- Economics = Aggregate macro outcomes
  - Not in scope: distribution outcomes, economic policy
  - Not in scope: AI for students, leisure, misuse, ...

## Slide 34 — Magnitude of AI impact

Below the title, a single thin horizontal black line spans most of the slide width (from about x=50 to x=800 of 1000, at about 47% of the page height). Nothing else is on the page; no labels or ticks.

## Slide 35 — Technological Richter Scale (Nate Silver)

Build of slide 34. The same horizontal line is now labelled above it with six evenly spaced category labels, left to right: "Technology of the Month", "Technology of the Year", "Technology of the Decade", "Technology of the Century", "Technology of the Millennium", "Technology of the Epoch". (Each label is two lines: "Technology of" / "the Month", etc.)

## Slide 36 — Magnitude of AI impact w/ examples

Build of the scale in slide 35 with an example added in parentheses under each label, on the same horizontal line:

- Technology of the Month (e.g., a smartphone app)
- Technology of the Year (e.g., the VCR)
- Technology of the Decade (e.g., credit cards)
- Technology of the Century (e.g., electricity)
- Technology of the Millennium (e.g., agriculture)
- Technology of the Epoch (e.g., rise of humans)

## Slide 37 — Forecasts of AI impact (LEAP, Murphy et al.)

A bar chart titled (small, above the plot) "Technological Richter Scale (Level 5 - 10)". The y-axis is labelled "Probability", with ticks 0, 5, 10, 15, 20, 25, 30 (range about 0 to 33). The x-axis has six categories, the same six labels as slide 36 with the same examples. There is one series: six salmon-pink bars with orange outlines, each with its value printed above it:

- Technology of the Month (e.g., a smartphone app): 7.6
- Technology of the Year (e.g., the VCR): 8.2
- Technology of the Decade (e.g., credit cards): 22
- Technology of the Century (e.g., electricity): 31 (tallest bar)
- Technology of the Millennium (e.g., agriculture): 22
- Technology of the Epoch (e.g., rise of humans): 9.4

The slide does not say what the probability is a probability of, or who is forecasting, beyond the title's "LEAP, Murphy et al."; the unit is presumably percent but the chart does not print a "%".

## Slide 38 — Magnitude of AI impact

Same as slide 36 (title "Magnitude of AI impact", the horizontal line with the six labelled categories and their examples, nothing else); the bar chart has been removed. Start of the labelled-forecasts sequence.

## Slide 39 — Labeled forecasts

Build of slide 38 (same scale). A photograph of a smiling man with dark curly hair, glasses and a pink collared shirt sits below the line, under the "Technology of the Year" / "Technology of the Decade" labels (left edge roughly under "the Year", spanning to the start of "the Decade"). No caption names him. Below the photo is a quotation in serif type: "leads to an updated, more modest increase in TFP and GDP in the next 10 years that can be upper bounded by 0.55% and 0.90%, respectively." (No source is printed on the slide; TFP and GDP are not expanded.)

## Slide 40 — Labeled forecasts

Build of slide 39 in the same layout: the man's photograph and the quotation are gone. In their place, lower centre of the page, are a photograph of two smiling men with dark hair and black-rimmed glasses (one in a checked shirt) and, to the right, a rounded rectangle with a red outline containing the pixel-style text "AI AS NORMAL TECHNOLOGY". Their horizontal position (roughly under the Century / Millennium labels) is below the scale line but not tied to a specific category by a line or arrow. The two men are not named on the slide.

## Slide 41 — Labeled forecasts

Build of slide 40 (same scale line and six labelled categories). The two-men photograph and "AI AS NORMAL TECHNOLOGY" badge from slide 40 are gone. New items below the line:

- Lower left: a quotation on a pale beige panel, in serif type: "• Each of these million copies can act independently on unrelated tasks, or if needed can all work together in the same way humans would collaborate, perhaps with different subpopulations fine-tuned to be especially good at particular tasks." followed by "We could summarize this as a "country of geniuses in a datacenter"." (No source is printed on the slide.)
- Centre right, under the "Technology of the Century" to "Millennium" labels: a cover graphic reading "AI 2027" (AI in black, 2027 in dark red), with a small "AI Futures Project" logo at its top right and the author list "Daniel Kokotajlo, Scott Alexander, Thomas Larsen, Eli Lifland, Romeo Dean".
- Directly below the AI 2027 cover: a photograph of a smiling man with curly dark hair and dark-rimmed glasses, wearing a navy jacket, against a beige background. Not named on the slide.

## Slide 42 — Labeled forecasts

Build with a different layout: the labels are placed along the line and the line now forks. The scale line (same six labelled categories) ends at the right at a branch point (about x=800); from there two thin lines diverge, one going up and to the right to the grey label "Utopia" (top right corner) and one going down and to the right to the grey label "Doom" (bottom right). The slide's photographs from previous builds (the single man, the two men, AI 2027 and its man) are absent in this build.

- Above the line, centre to right: a small-print quotation: "If AI stays on the trajectory that we think it will, then amazing things will be possible. Maybe with 10 gigawatts of compute, AI can figure out how to cure cancer. Or with 10 gigawatts of compute, AI can figure out how to provide customized tutoring to every student on earth. If we are limited by compute, we'll have to choose which one to prioritize; no one wants to make that choice, so let's go build." Next to it (to its right, near the Utopia branch) is a photograph of a man with short dark curly hair in a dark sweater, gesturing with both hands, against a blurred olive and gold background with a blue strip at the bottom. Not named on the slide.
- Below the line, lower right, near the Doom branch: a book cover with a black background, titled "IF ANYONE BUILDS IT, EVERYONE DIES", subtitle "WHY SUPERHUMAN AI WOULD KILL US ALL" (in grey fading to red, over a red glow rising from the bottom edge), authors "ELIEZER YUDKOWSKY & NATE SOARES". Beneath the Doom label is a photograph of a bearded man with glasses wearing a burgundy shirt. Not named on the slide.

## Slide 43 — Labeled forecasts

Build combining slides 39–42 on the forked scale (same line, six labelled categories, branch to "Utopia" top right and "Doom" bottom right). The quotation text and the book cover from slide 42 are gone, leaving only the photographs:

- Above the Utopia branch: the photograph of the gesturing man from slide 42 (no quotation text this time).
- Below the line, under "Technology of the Year" / "the Decade": the photograph of the smiling man with glasses and pink shirt (from slide 39).
- Below the line, to the right of that: the photograph of the two smiling men (from slide 40); the "AI AS NORMAL TECHNOLOGY" badge is not shown.
- Below the line, centre: the "AI 2027" cover (with the five author names), and below it the photograph of the curly-haired man in the navy jacket (from slide 41).
- Lower right, under the "Doom" label: the photograph of the bearded man with glasses in a burgundy shirt (from slide 42).

The Doom photograph is cut off at the bottom edge of the page. No captions or names are printed.

## Slide 44 — Normal technologies

The scale line with its six labelled categories and examples (as in slide 36). A thick red rectangle outline is drawn around the middle three categories: "Technology of the Decade (e.g., credit cards)", "Technology of the Century (e.g., electricity)" and "Technology of the Millennium (e.g., agriculture)". Nothing else is on the page.

## Slide 45 — Generic ~~normal~~ technologies

Title with the word "normal" struck through: "Generic ~~normal~~ technologies". Below it, a scanned excerpt in typewriter-style monospace font:

"In this paper we attempt to forge a link between the details of individual technologies and the aggregate growth process. We put forward a view of innovation and growth in which there are key *technological facts* that may have far reaching consequences for the dynamic performance of the economy as a whole. The central notion is that, at any point in time, there are a handful of "generic", or "general purpose" technologies (GPT's) characterized by their pervasiveness (i.e. they can be used as inputs in a wide range of downstream sectors), and by their technological dynamism. Thus, as the GPT evolves and advances, it spreads throughout the economy, and in so doing it"

(the excerpt is cut off mid-sentence at the right edge). Citation printed below in grey: "Bresnahan and Trajtenberg (1992)".

## Slide 46 — Is AI a general-purpose technology (GPT)?

Text-only slide: the single question "Is AI a general-purpose technology (GPT)?" centred on the page, no other content.

## Slide 47 — If so, is AI governed by GPT principles?

Text-only slide: the single question "If so, is AI governed by GPT principles?" centred on the page, no other content.

## Slide 48 — [screenshot: Bresnahan and Trajtenberg, "General purpose technologies 'Engines of growth'?"]

A full-page screenshot of the first page of a journal article, no slide title. Header: the Elsevier logo (a coat-of-arms emblem with "N·H" above it, the word "ELSEVIER" below), the text "Journal of Econometrics 65 (1995) 83-108", and the "JOURNAL OF Econometrics" logotype at top right.

Title: "General purpose technologies 'Engines of growth'?"

Authors: "Timothy F. Bresnahan a,*,c, M. Trajtenberg b,c". Affiliations: "a Department of Economics, Stanford University, Stanford, CA 94305, USA"; "b Tel-Aviv University, Tel-Aviv, Israel"; "c NBER".

Abstract (as printed): "Whole eras of technical progress and growth appear to be driven by a few 'General Purpose Technologies' (GPT's), such as the steam engine, the electric motor, and semiconductors. GPT's are characterized by pervasiveness, inherent potential for technical improvements, and 'innovational complementarities', giving rise to increasing returns-to-scale. However, a decentralized economy will have difficulty in fully exploiting the growth opportunities of GPT's: arms-length market transactions between the GPT and its users may result in 'too little, too late' innovation. Likewise, difficulties in forecasting the technological developments of the other side can lower the rate of technical advance of all sectors."

Key words: "Technical change; Growth; Social returns; Coordination". "JEL classification: O30; O40; L10".

Start of "1. Introduction": "Economists have known for a long time that technical change is the single most important force driving the secular process of growth (Abramovitz, 1956; Solow, 1957). Yet, relatively little progress has been made in accounting for the" (page cuts off here).

## Slide 49 — The three characteristics of a GPT

A block of text in a serif font, a screenshot of a passage from a paper:

"Foundation models can be thought of as what economists refer to as a *general-purpose technology* [Bresnahan and Trajtenberg 1995]. General-purpose technologies refer to technologies like the steam engine and electricity, which drive waves of transformation and productivity growth due to their pervasiveness, improvement over time, and ability to spawn complementary innovations (a host of products and services that revolve around one core product). While foundation models may not be pervasive at the moment, they seem poised to be the basis of widespread technological innovations, and have the key hallmarks of a general-purpose technology. As a result, these models are likely to be economically important. In considering the impact of foundation models on the economy, we will focus on three broad areas of impact: productivity, wage inequality, and ownership."

(In the passage, "general-purpose technology" is italic and "[Bresnahan and Trajtenberg 1995]" is a blue link.)

Citation beneath the passage, in grey sans-serif: "Bommasani, ..., and Liang (2021)"

## Slide 50 — Pervasiveness

Title only; the rest of the slide is blank.

## Slide 51 — Pervasiveness

Build of slide 50. Adds a line chart at the left, titled "ChatGPT User Growth Over Time".

- Axes: x axis "Date", with six tick labels at irregular spacing: Jan 2023, Aug 2023, Oct 2024, Dec 2024, Feb 2025, Sep 2025. y axis "Users (in millions)", ticks 100 to 800 in steps of 100.
- Legend (top left): a green line with dot marker, "Users (in millions)". One series (green line with a dot at each of the six dates), approximate values:
  - Jan 2023: about 50
  - Aug 2023: about 100
  - Oct 2024: about 250
  - Dec 2024: about 300
  - Feb 2025: about 400
  - Sep 2025: about 800
- The points are joined by straight segments, so the line steepens sharply between Feb 2025 and Sep 2025. Values are read from the plot; no data labels are printed. No source citation appears on the slide.

## Slide 52 — Pervasiveness

Build of slide 51. The ChatGPT user growth chart (same as slide 51, now somewhat larger) stays at the left, and a grid of eight small area/line charts is added at the right.

Grid title (orange): "Usage share trends across economic index reports (V1 to V3)". Eight panels, two rows by four columns. Each panel has a y axis labelled "Percentage" and an x axis with three points: Jan 2025, Mar 2025, Aug 2025. Each panel is one series: an orange line with dot markers and a pale beige filled area under it. Each point is labelled with its value. Panel y axes have different ranges (e.g. 0 to 40 for Computer and Mathematical; 0 to 10 for Arts; 0 to 12 or so for Educational; 0 to 8 for Office).

Top row:
- "Computer and Mathematical": Jan 2025 37.2%, Mar 2025 39.6%, Aug 2025 36.9%.
- "Arts, Design, Entertainment, Sports, and Media": 10.2%, 9.4%, 8.5%.
- "Educational Instruction and Library": 9.3%, 11.0%, 12.7%.
- "Office and Administrative Support": 7.8%, 7.0%, 8.4%.

Bottom row:
- "Life, Physical, and Social Science": 6.3%, 6.8%, 7.4%.
- "Business and Financial Operations": 5.9%, 4.4%, 3.1%.
- "Architecture and Engineering": 4.7%, 3.8%, 2.5%.
- "Management": 4.5%, 3.1%, 2.7%.

The panels are not labelled with a source on the slide. The "economic index reports (V1 to V3)" wording is only what the grid title says; the slide does not name the publisher.

## Slide 53 — Improvements over time

Title only; the rest of the slide is blank.

## Slide 54 — Improvements over time

Build of slide 53. Adds a chart at the left, a METR figure.

Title (in the chart): "AI time horizons are increasing in many domains". Subtitle: "Task length (at 50% success rate)". A METR logo (with the word "METR") at top right of the chart. Bottom corners: "CC-BY" at left and "metr.org" at right.

- Axes: x axis "Model release date", ticks 2020, 2022, 2024, 2026 (range roughly 2019 to 2027). y axis is logarithmic task length, ticks: 1 sec, 4 sec, 15 sec, 1 min, 4 min, 15 min, 1 hr, 4 hrs.
- Main series: green dots (models) with a solid green fit line rising from about 1 sec in 2019, passing roughly 1 min in early 2022 and 15 min in early 2024, ending at about 1 hr in 2025, then continued as a dashed line to about 4 hrs in 2026-27 (the line is also dashed at the far left, before about mid-2019); a pale green band around the line shows the uncertainty. Label in green: "METR-HRS (Original Time Horizons)". About twelve green dots lie near the line (the earliest at about 2-3 sec in 2019; the latest at roughly 2 hrs in early 2025), not all on it: one, at about 25 sec in early 2022, sits well below it.
- Other benchmark trend lines, each a short coloured line labelled in the same colour:
  - "MATH" (brown): from about 15 sec in mid-2020, on the green line, rising to about 20-25 min in late 2023.
  - "Tesla FSD" (olive): from about 15 min at the start of 2022, nearly flat, rising to about 1.5 hr by late 2024.
  - "GPQA Diamond" (purple): steep, from about 6 min in early 2024 to about 1 hr in late 2024.
  - "Mock AIME" (dark blue): steep, from about 3 min in early 2024 to about 3 hrs in early 2025, reaching the top green dot.
  - "LiveCodeBench" (red): from about 10 min in early 2024 to about 40 min in early 2025.
  - "SWE-bench Verified" (black): from about 6 min in mid 2024 to about 30 min in 2025.
  - "OSWorld" (grey): from about 15 sec in 2024 to about 1.5 min in 2025.
  - "WebArena" (pink): from about 6 sec in mid 2023 to about 2 min in early 2025.
  - "RLBench" (crimson): from about 4 sec in mid 2022 to about 15 sec in mid 2023.
- Values for the non-METR lines are read by eye from the plot and are approximate.

## Slide 55 — Improvements over time

Build of slide 54. The METR chart (as on slide 54) stays at the left; a second chart is added at the right.

Right chart: an Epoch AI scatter plot with "EPOCH AI" logo at top right. Y axis: "Price (USD per million tokens)", logarithmic, ticks 0.1, 1, 10, 100. X axis: "Release Date", ticks Oct. 2021, Apr. 2022, Oct. 2022, Apr. 2023, Oct. 2023, Apr. 2024, Oct. 2024, Apr. 2025. Footer: "Data source: Epoch AI, Artificial Analysis" ("Artificial Analysis" underlined as a link).

Legend (four series, by colour):
- teal square: "GPT-3.5 Turbo level or better on general knowledge (MMLU)"
- pink/magenta square: "GPT-4 level or better on Ph.D. level science questions (GPQA)"
- blue square: "GPT-4o level or better on Ph.D. level science questions (GPQA)"
- light grey-teal square: "Other benchmarks and performance levels"

Each series is a set of dots (price of the cheapest model reaching that level at each release date) with a fitted downward line. Annotated lines:
- Teal line, labelled "Slowest 9x/year": dots at about \$2 (around Mar 2023), \$0.7 (Oct 2023), \$0.13 (mid 2024), \$0.1 (Aug 2024), \$0.07 (Oct 2024); line runs from about \$2.5 in Mar 2023 to about \$0.07 in late 2024.
- Pink line, labelled "Mid-range 40x/year": dots at about \$37 (Mar 2023), \$15 (Oct 2023), \$6 (Apr 2024), \$0.5 (Apr 2024), \$0.13 and \$0.12 (late 2024); line runs from about \$50 in Mar 2023 to about \$0.08 at end 2024.
- Blue line, labelled "Fastest 900x/year": dots at about \$8 and \$6 (mid 2024), \$2 (Oct 2024), \$0.12 (end 2024); line runs from about \$11 in Jun 2024 to about \$0.2 in end 2024.
- Several unlabelled pale grey lines (the "other benchmarks" series) decline across the same period.
- All dot and price values are read by eye and are approximate.

## Slide 56 — [quote card: Erik Brynjolfsson on general purpose technologies]

No slide title. A quote card with a black border: at left a photograph of a man with short light-brown hair and stubble, wearing a dark navy blazer over a light-blue open-collared shirt, in front of a window; at right, white text on a black background:

"Electricity is an example of a general purpose technology, like the steam engine before it. General purpose technologies drive most economic growth, because they unleash cascades of complementary innovations, like lightbulbs and, yes, factory redesign."

Attribution in script lettering: "Erik Brynjolfsson".

## Slide 57 — [text: electricity and complementary inventions]

No printed title. Text only (screenshot-style, with two grey-barred block quotes):

Electricity by itself → modest gains
Electricity + factories redesigned + electric motors + night shifts + new appliances → massive productivity takeoff

So instead of:

> "One invention → one productivity boost"

You get:

> "One invention → hundreds of complementary inventions → economy-wide transformation"

## Slide 58 — Innovation complementaries

- Technological: Downstream applications (coding tools)
- Organizational: Workflow redesign (verifying AI outputs)

(The title is printed "Innovation complementaries", as on the slide; the two lines are in grey.)

## Slide 59 — Productivity J-curve of GPTs

An image of an infographic with a black panel at left and a line chart at right. Citation below, in grey: "Brynjolfsson et al. (2018)".

Left black panel, heading underlined: "J-Curve Premise". Text in white:
- "New general purpose technologies (GPTs) require orgs to **learn, adapt, and reorganize**."
- "Early phases of GPT adoption are messy and costly, so productivity often **dips**."
- "Over time, as companies redesign processes, train workers, and invent complementary innovations, the benefits start to **compound**."
- "The result looks like a **"J" shape**: an initial decline followed by a strong upward surge as the technology's full potential is realized."

Right chart, title "Productivity Mismeasurement J-Curve". x axis "Years since AI adoption", ticks 0, 5, 10, ..., 40. y axis "Productivity growth mismeasurement (%)", ticks −0.75, −0.50, −0.25, 0.00, 0.25, 0.50, 0.75, 1.00. One series: a dark navy smooth curve that starts at 0.00 at year 0, falls to a minimum of about −0.70 at year 10, crosses 0.00 at about year 20, reaches about 0.55 at year 25, about 0.9 at year 30, about 1.05 at year 35 and flattens near 1.1 at year 40. A horizontal dashed grey line marks 0.00. Annotations: green text at right of the dashed line, "Realized gains & measurement catch-up"; red text at the bottom, "Implementation & learning phase (Intangible investment buildup)"; bottom right, "Source: NATIONAL BUREAU OF ECONOMIC RESEARCH". The curve values are read by eye; no data labels are printed.

## Slide 60 — GDP as a central measure of growth

An Our World in Data chart (logo "Our World in Data" at top right). Chart title: "Global GDP over the long run". Subtitle: "Total output of the world economy. These historical estimates of GDP are adjusted for inflation. We combine three sources to create this time series: the Maddison Database (before 1820), the Maddison Project Database (1820–1989), and the World Bank (1990 onward)."

- Axes: x axis ticks 1, 500, 1000, 1500, 2024. y axis ticks \$0 to \$180 trillion in steps of \$20 trillion.
- One series: a single blue line with dot markers. It is essentially flat at about \$0 from year 1 to about 1800 (sub-\$1 trillion on this scale), then climbs ever more steeply: about \$10 trillion around 1950, \$20 trillion around 1970, \$60 trillion around 2000, ending at about \$173 trillion in 2024. Values are read by eye.
- Footer: "Data source: Eurostat, OECD, and World Bank (2025); Bolt and van Zanden – Maddison Project Database 2023; Maddison Database 2010". "Note: This data is expressed in international-\$ at 2021 prices." "OurWorldinData.org/economic-growth | CC BY".

## Slide 61 — GDP trends in the last ~150 years

A chart from a paper, citation below in grey: "Chad Jones (2023)".

Chart title: "Figure 1: Real GDP per Person in the United States". Axis caption: "2022 DOLLARS, RATIO SCALE". y axis (log/ratio scale): 4,000; 8,000; 16,000; 32,000; 64,000. x axis "YEAR", ticks 1880, 1900, 1920, 1940, 1960, 1980, 2000, 2020 (data starts about 1870).

Two series:
- A thick blue line, the data: about 4,500 in 1870, about 6,000 in 1880, about 8,000 around 1900, with a dip to about 8,300 in 1933, a spike to about 20,000 around 1944, about 32,000 near 1975, ending near 70,000 in 2022.
- A thin dashed orange-red line, the trend, rising steadily from about 3,800 in 1870 to about 78,000 in 2022; labelled "2.0% per year" (placed in the mid-right of the plot).

Source note: "Source: Barro and Ursua (2010) until 1928 and after using the NIPA data from the FRED database (GDPCA and B230RC0A052NBEA)." (Barro and Ursua (2010) and "2010" are in blue link colour.)

## Slide 62 — [quote: "You can see the computer age everywhere"]

No title. Large centred text: "You can see the computer age everywhere but in the productivity statistics"

Citation below in grey: "Solow; Brynjolfsson (1993)"

## Slide 63 — Why do Internet-era GPTs not show up?

- Clearly the Internet is a big deal in changing our lives
- Many Internet technologies are free
  - Google Search/Maps, Facebook
- GDP is about payments.

## Slide 64 — GDP-B: measuring consumer surplus

Left text:
- Choice experiments
  - Willingness to pay
- 2025 survey
  - 40% of adults use genAI
  - WTP = \$98/month
  - \$97B consumer surplus/yr

Right: a screenshot of a Wall Street Journal opinion page (the "WSJ OPINION" masthead; a navigation bar with "English Edition", "Print Edition", "Video", "Audio", "Latest Headlines", and section links "World, Business, U.S., Politics, Economy, Tech, Markets & Finance, Opinion, Arts, Lifestyle"). Tags "OPINION" and "COMMENTARY" and a "Follow" button. Headline: "AI's Overlooked \$97 Billion Contribution to the Economy". Standfirst: "The AI 'dividend' may not be evident yet in estimates of gross domestic product but it's making life better and more productive." Byline: "By Avinash Collis and Erik Brynjolfsson", dated "Aug. 3, 2025 3:54 pm ET".

Citation below, in grey: "Brynjolfsson et al. (2025)"

## Slide 65 — How might AI impact the economy

1. Make one sector super productive
2. Act as an injection of cheap labor
3. Generate new ideas

## Slide 66 — If AI makes one sector productive, then?

- GDP will grow **slightly** even if sector is very productive
- Prices fall in this sector, even if consumption grows
  - Example: Illumination sector
- Baumol's cost disease:
  - Other sectors match wages of productive sector
  - "Good/cheap software, expensive healthcare"
  - These sectors dominate GDP share

## Slide 67 — Modeling GDP

The equation is printed as plain text, not typeset: "Y = A · K^α · L^(1−α)". In LaTeX:

$$Y = A \cdot K^{\alpha} \cdot L^{1-\alpha}$$

Definitions as printed:
- Y = Output (GDP, production)
- A = Total Factor Productivity (TFP)
- K = Capital
- L = Labor
- α = Capital share (0 < α < 1)

## Slide 68 — If AI injects labor, then?

- GDP curve may shift up (if one-time) or exponential rate may grow (if sustained) assuming capital keeps up
- Labor in developed economies has been plateauing
  - Population stagnation
  - Labor share stagnation
- What matters is how much labor & which tasks

## Slide 69 — If AI generates ideas, then?

- Most likely changes the exponential rate of GDP growth
- Romer: ideas are the heart of growth
  - Unlike traditional goods, ideas are nonrival (reusable)
  - Example: linear algebra
- What matters is how AI changes R&D/science
- See Ben Jones (2025) for more specific models

## Slide 70 — Questions?

Title only, centred; the rest of the slide is blank.

## Figure audit

Two claude-sonnet-5-5 audit passes on 2026-10-26 checked 20 pages against 600 dpi crops: every chart with data, every table, and the densest diagrams and screenshot pages (slides 2, 3, 4, 8, 12, 15, 18, 19, 22, 27, 37, 42, 51, 52, 54, 55, 59, 60, 61, 64). Eleven were exact: slides 3, 4, 8, 19, 37, 51, 52, 55, 59, 60, 61 and 64, including every printed value on slides 8, 37, 52 and 55 and all 28 cells of slide 27's table. The corrections, all applied above:

- **Slide 22**, the one substantive error. The first reading had the robots.txt panel's "Full restrictions" band reaching only 5–8% by mid-2024 and "No Robots.txt" shrinking from 25–30% to 8–10%. The page shows "Full restrictions" jumping from about 3% to roughly 25–30% after the GPTBot and G-Ext. markers, and "No Robots.txt" falling from about 15% to nearly nothing by 2019; the terms-of-service panel's restriction share starts at about 28% in 2017, not 15%; and the GDPR markers appear only in the middle panel. The parent confirmed the robots.txt reversal on its own small render of the page.
- **Slide 2**: Microsoft starts at about 2%, not 4%.
- **Slide 12**: which boxes have their WHAT/WHO text on the opposite side, and where the Development and Pre-Deployment brackets meet.
- **Slide 15**: the "unnamed" logo in the Deposition box is Applied Materials' mark.
- **Slide 18**: Microsoft and "Others" swap places several times between Q4 2022 and Q3 2023.
- **Slide 27**: the caption reads "fully open, open source models"; "(900)" is as printed.
- **Slide 42**: the photograph's background and the book subtitle's colour.
- **Slide 54**: the end points of the MATH, Tesla FSD and Mock AIME lines, and the number and placement of METR's green dots.
- **Slide 3**: the Senior line's peak date.

Values described as read by eye on the unlabelled charts (slides 2, 3, 4, 18, 22, 51, 54, 55, 59, 60, 61) are approximate; the printed numbers are exact.
