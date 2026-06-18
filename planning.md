
**Community:** r/nba is a basketball discussion forum where the discourse ranging from analytical approach to an emotional reaction.
**Breakdown** posts make structured arguments supported by specific, verifiable evidence (stats, contract figures, historical comparisons); **hot_take** posts state bold or contrarian opinions confidently without meaningful supporting evidence.
**Reaction** posts are immediate emotional responses to specific events like games, trades, or award announcements, where expressing a feeling is the entire point and these distinctions matter to the community because r/nba regulars' discourse are misconstrued.


**Labels**

**breakdown**

A post that makes a structured argument supported by specific, verifiable evidence — statistics, contract figures, historical comparisons, or film observations — and draws a conclusion grounded in that evidence.

Example 1:


"Cade Cunningham while defended by Dean Wade in Round Two — 107 possessions, 17 points, 9 assists, 6 turnovers, 5/17 shooting."



Example 2:


"Jokic is not struggling because he individually has been shit. He's been struggling because the modern NBA doesn't favor the way the Nuggets have been built. Look at the most successful offenses in recent playoff years. All of them have strong shooting and multiple players who can reliably attack downhill. In 2024 the Nuggets were running a 7-man rotation that lacked shooting. Only slightly more than half of the rotation could reliably space the floor. Murray really isn't a strong primary ball handler against physical teams."




**hot_take**

A post that states a bold, confident, or contrarian opinion without meaningful supporting evidence, or with evidence too thin to genuinely carry the conclusion — the claim is asserted rather than argued.

Example 1:


"Spurs will be a top 3 favorite to win the title next year and their entire core is under 23. They surpassed expectations this season tenfold."



Example 2:


"Frankly watching all the eastern conference teams struggle in the first round I would say a healthy Nuggets, Wolves and Lakers team would beat them all in a best of 7 series."




**reaction**

A post that is an immediate emotional response to a specific triggering event — a game result, trade, award announcement, or play — where expressing a feeling in the moment is the primary purpose, not making a generalizable claim.

Example 1:


"As a life-long Knicks fan I've watched us suffer through the 17-win seasons of 2014-15 and 2018-19. I was there contemplating life when Andrea Bargnani was considered our best player. And you know what? I'm glad I did because IT'S SO MUCH SWEETER NOW. WE ARE EASTERN CONFERENCE CHAMPS. BING. FUCKING. BONG."



Example 2:


"Honestly after losing Schroeder and Malik I didn't think this team would ever get to 60 wins this season. It was a great season overall and even though the result wasn't perfect the journey is what matters more. These players all got better and I hope Detroit shows up again next year."




**Hard Edge Cases**

Primary boundary: breakdown vs. hot_take

The hardest case is a post that cites one specific statistic to support a strong opinion. The stat is real and verifiable, but it is selected to prosecute a conclusion the author already holds rather than to genuinely investigate a question.

Example of the boundary post:


"Jayson Tatum is really the only player who can make four straight All-NBA First Teams and still have people questioning if he's a top-5 player. I've seen people claiming Anthony Edwards is top 5 this year yet Ant hasn't even made a First Team All-NBA."



This could be breakdown (cites All-NBA selections, a specific and verifiable credential) or hot_take (the conclusion — "stop disrespecting Tatum" — goes beyond what the stat proves, and the stat is used rhetorically rather than analytically).

Decision rule: Apply the "stripped frame" test. Remove the opinion framing and ask: does the evidence still stand as a meaningful observation? If yes, label breakdown. If removing the framing leaves nothing — if the stat only exists to win an argument — label hot_take. The Tatum post fails the test: the All-NBA comparison is rhetorical grievance, not analysis. → hot_take.

Secondary boundary: reaction vs. breakdown

A post triggered by a specific event that also makes a forward-looking analytical claim.

Decision rule: Label by dominant purpose. If the author's primary goal is expressing a feeling about what just happened, label reaction. If the analytical claim would make sense posted on any day — not just in response to this specific event — label breakdown. When genuinely uncertain, mark the example with a ? flag in the annotation sheet and return to it after annotating 20 more examples to see if the decision rule has become clearer.


**Data Collection Plan**

Source: r/nba via the Reddit JSON API (reddit.com/r/nba/top.json?t=year&limit=100) supplemented by comment sections from game threads and news posts.

Target distribution (200 total):

LabelTarget countRationalebreakdown80Naturally dominant in top posts; collect from top-voted submissionshot_take70Common in comments; sample from game threads and controversy postsreaction50Common in game threads immediately after results; sample from post-game threads

If a label is underrepresented after 150 examples:


reaction underrepresented → go directly to post-game threads (sorted by New, not Top) where emotional responses dominate
hot_take underrepresented → go to comment sections on trade news or MVP/GOAT debate posts, where confident opinions without evidence are densest
breakdown underrepresented → unlikely given r/nba's analytical culture, but if needed, search for [OC] tagged posts and film breakdown threads


Annotation process: Annotate in batches of 50. After each batch, check label distribution and adjust sampling source if any label is more than 15 percentage points below target. Track source (top posts vs. game thread vs. comment section) as a metadata column for later analysis.


**Evaluation Metrics**

Primary metrics: Per-class F1 score and macro-averaged F1.

Why not accuracy alone: The dataset will not be perfectly balanced (target is 40/35/25 split), so accuracy is misleading — a classifier that always predicts breakdown would score ~40% accuracy while being useless. Macro F1 weights each class equally regardless of frequency, which is the right choice here because all three label types matter equally to the tool's usefulness.

Per-class F1 matters separately because the three classes have different costs of error:


False breakdown (calling a hot_take a breakdown) is the most damaging error for a tool meant to surface quality discourse — it degrades the user's trust in the tool's curation.
False reaction (calling a breakdown a reaction) is less damaging but causes the tool to miss substantive content.
False hot_take errors are moderate — misclassifying a reaction as a hot_take is a relatively minor mistake given how similar their discourse function is.


Additional metric: Confusion matrix, specifically the hot_take/breakdown off-diagonal cells, since that is the primary known boundary problem.

Unknown rate: Track how often the model's output falls outside the three valid labels (if using a generative classifier). Target < 2%.


**Definition of Success**

Minimum threshold for the classifier to be considered useful:


Macro F1 >= 0.75 across all three classes
Per-class F1 >= 0.70 for each individual label (no label left behind)
breakdown precision >= 0.80 (the tool's primary use case is surfacing quality breakdowns, so false positives on this label are especially costly)


"Good enough for deployment" threshold:


Macro F1 >= 0.82
breakdown precision >= 0.85
hot_take recall >= 0.75 (catching most hot takes matters if the tool is used to filter them out)
Unknown/invalid output rate < 1%


Objective determination test: At evaluation time, compute per-class F1 from the held-out test set (20% of 200 examples, stratified by label). If all four conditions above are met simultaneously, the classifier meets the deployment threshold. If macro F1 >= 0.75 and all per-class F1 >= 0.70 but breakdown precision is below 0.80, the classifier meets the useful threshold but not the deployment threshold — it can be reported as a working prototype requiring one more round of prompt refinement.

These criteria are specific enough to evaluate objectively: the test set is fixed before evaluation, the metrics are computed from ground-truth labels, and there is no judgment call involved in determining whether the thresholds are met.


**AI Tool Plan**

1. Label stress-testing (do this before annotating)

What to do: Paste the three label definitions and the breakdown vs. hot_take decision rule into Claude, and ask it to generate 10 posts that sit at the boundary between breakdown and hot_take — posts that cite exactly one stat to support a strong opinion.

Prompt to use:

Here are my three label definitions for an r/nba discourse classifier:
[paste label definitions]
[paste decision rule]

Generate 10 Reddit comments that sit at the boundary between breakdown
and hot_take. Each should cite one specific statistic or fact, but use
it to support an opinion that goes beyond what the evidence proves.
Make them feel like real r/nba comments.

What to do with the output: Try to apply the decision rule to each generated post. If you hesitate on more than 3 out of 10, the definition or decision rule needs tightening. Revise the decision rule before starting annotation. Do not start annotating 200 examples until you can consistently classify the stress-test posts.


2. Annotation assistance

Plan: Use Claude to pre-label the second batch of 100 examples (after the first 50 are hand-labeled to establish a baseline). Pre-labeling reduces annotation time from ~30 seconds per example to ~5 seconds (review rather than decide from scratch).

Tool: Claude (claude.ai), using this prompt structure:

You are annotating r/nba Reddit posts for a discourse classifier.
Label each post as exactly one of: breakdown, hot_take, reaction.

breakdown: structured argument with specific verifiable evidence
hot_take: bold opinion without meaningful supporting evidence
reaction: immediate emotional response to a specific event

Decision rule for breakdown vs. hot_take: if removing the opinion
framing leaves a meaningful observation, label breakdown. If the
evidence only exists to win an argument, label hot_take.

Post: [text]
Label:

Tracking: Add a pre_labeled column (boolean) to the annotation CSV. Any row pre-labeled by Claude gets True; hand-labeled rows get False. Report the pre-labeling rate and the agreement rate between Claude's labels and final labels in the evaluation report.


3. Failure analysis

Plan: After evaluation, export all misclassified examples to a text file and paste them into Claude with this prompt:

Here are [N] posts that my r/nba discourse classifier got wrong.
Each shows the post text, the true label, and the predicted label.
Identify 2-3 patterns that explain why these posts were misclassified.
Be specific — name the linguistic features or content patterns,
not just "ambiguous posts."

[paste misclassified examples]

What to look for:


Are breakdown posts being called hot_take when they use aggressive or emotional language alongside stats? (Framing confusing the classifier)
Are short breakdown posts (one-stat posts) being called hot_take? (Length as a spurious signal)
Are reaction posts about trades (rather than games) being called breakdown? (Domain-specific trigger events)
