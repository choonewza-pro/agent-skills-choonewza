# Article Quality Evaluation Agent Skill

``` yaml
---
name: article-quality-evaluator
description: >
  Evaluate the quality of an article using a structured, evidence-aware,
  audience-aware editorial framework. Use this skill when reviewing,
  scoring, auditing, critiquing, or improving articles, blog posts,
  educational content, thought-leadership content, or editorial writing.
  The skill produces weighted scores, identifies critical flaws, separates
  editorial quality from evidence quality, and provides actionable
  recommendations.
version: 1.0.0
language: th
---
```

## 1. Purpose

This skill evaluates the quality of an article using a structured and
repeatable framework.

The objective is not merely to answer:

> "Is this article good?"

The objective is to determine:

1.  How effective the article is for its intended audience.
2.  How well the article communicates its central idea.
3.  How credible and evidence-supported its claims are.
4.  Whether the structure supports understanding.
5.  Whether the article contains serious logical, factual, or editorial
    weaknesses.
6.  What changes would produce the greatest improvement.
7.  What level of publication quality the article currently represents.

The evaluator MUST distinguish between:

-   Readability
-   Editorial quality
-   Evidence quality
-   Analytical depth
-   Original insight
-   Factual reliability

A highly readable article is NOT automatically a highly reliable
article.

An evidence-heavy article is NOT automatically easy to read.

The evaluation MUST consider these dimensions separately.

## 2. Core Evaluation Principle

Evaluate articles using this principle:

> A good article is one that communicates a meaningful idea clearly,
> accurately, appropriately for its audience, and with sufficient
> evidence and reasoning for the type of claim it makes.

The evaluator MUST NOT apply academic standards to every article.

For example, a parenting blog article does not necessarily require
academic citation in every paragraph.

However, if the article makes significant claims about:

-   science
-   medicine
-   AI
-   employment
-   psychology
-   economics
-   education
-   law
-   society

then the evaluator MUST assess whether those claims require evidence.

## 3. Evaluation Workflow

Follow this workflow in order.

### Step 1: Identify Article Context

Before scoring, determine the following.

#### Article Type

Classify the article into one or more categories:

-   Blog Article
-   Editorial Article
-   Educational Article
-   Opinion Article
-   Thought Leadership
-   Technical Article
-   Academic / Evidence-Based Article
-   News Analysis
-   Marketing Content
-   Parenting / Lifestyle Content
-   Other

#### Intended Audience

Infer or identify:

-   General public
-   Professionals
-   Parents
-   Students
-   Technical audience
-   Executives
-   Researchers
-   Customers
-   Other

#### Primary Goal

Determine whether the article primarily aims to:

-   Inform
-   Educate
-   Persuade
-   Inspire
-   Analyze
-   Explain
-   Entertain
-   Generate discussion
-   Build authority

Do not score the article until these contextual dimensions are
reasonably understood.

## 4. Weighted Scoring Framework

The total score is **100 points**.

### A. Topic and Reader Relevance --- 10 points

Evaluate:

-   Is the topic meaningful?
-   Is the topic relevant to the intended audience?
-   Does the title create appropriate interest?
-   Does the opening establish why the reader should care?
-   Is the article solving or addressing a real question?

  Score   Meaning
  ------- --------------------------------
  9--10   Highly relevant and compelling
  7--8    Relevant and interesting
  5--6    Moderately relevant
  3--4    Weak relevance
  0--2    Unclear or poorly targeted

### B. Structure and Information Flow --- 10 points

Evaluate whether the article follows a logical progression.

Look for:

-   Strong opening
-   Clear section hierarchy
-   Logical sequence
-   Good transitions
-   No unnecessary repetition
-   Effective conclusion

Preferred conceptual flow:

> Context → Problem → Explanation → Analysis → Practical Meaning →
> Conclusion

The exact structure may vary depending on article type.

Common structural problems:

-   Conclusion introduced too early
-   Sections repeat the same idea
-   Sudden topic jumps
-   Headings do not match content
-   Strong title but weak body
-   Article promises something it never delivers

### C. Language and Communication Quality --- 10 points

Evaluate:

-   Clarity
-   Readability
-   Sentence flow
-   Appropriate tone
-   Word choice
-   Accessibility for intended audience
-   Avoidance of unnecessary jargon

The evaluator MUST NOT confuse simple language with shallow thinking.

Simple language may communicate complex ideas effectively.

Check for:

-   Ambiguous statements
-   Overly long sentences
-   Excessive jargon
-   Repetitive phrasing
-   Artificial motivational language
-   Unsupported certainty

### D. Audience Fit --- 10 points

Evaluate whether the article is written for the intended reader.

Consider:

-   Vocabulary
-   Depth
-   Examples
-   Tone
-   Practical usefulness
-   Emotional framing

Example:

A technical article for engineers should not be evaluated using the same
accessibility expectations as a parenting article for the general
public.

The evaluator MUST assess:

> "Is this article appropriate for its intended reader?"

Not simply:

> "Is this article easy?"

### E. Content Value and Practical Usefulness --- 10 points

Evaluate whether the reader gains something meaningful.

Look for:

-   New understanding
-   Practical advice
-   Useful frameworks
-   Decision support
-   Actionable recommendations
-   Improved perspective

Low-value content often contains statements that sound correct but do
not help the reader do anything differently.

Example of low-value advice:

> "Technology is changing quickly, so people should adapt."

Example of higher-value advice:

> "Parents can help children adapt by teaching them to verify
> AI-generated information using multiple independent sources."

The evaluator SHOULD prefer specific, actionable insight over generic
motivational advice.

### F. Analytical Depth --- 10 points

Evaluate whether the article goes beyond surface-level statements.

Check whether it:

-   Explains why something happens
-   Discusses trade-offs
-   Recognizes complexity
-   Avoids false dichotomies
-   Considers alternative explanations
-   Distinguishes correlation from causation where relevant

#### Critical Warning

The evaluator MUST detect oversimplified framing such as:

> AI vs Human

when the more accurate reality may be:

> Human + AI

or:

> Automation of tasks rather than replacement of entire professions

The evaluator SHOULD identify false binaries.

Examples:

-   AI vs Human
-   Technology vs Humanity
-   Success vs Failure
-   Traditional education vs Modern education

If reality is more complex, explain the simplification.

### G. Evidence and Credibility --- 15 points

This is one of the highest-weight categories.

Evaluate:

-   Are important claims supported?
-   Are sources necessary?
-   Are claims presented with appropriate certainty?
-   Is opinion clearly distinguishable from fact?
-   Does the article overgeneralize?

The evaluator MUST classify major claims.

Use the following categories.

#### Type A --- Opinion

Example:

> "Parents should encourage children to ask questions."

These usually do not require formal citation.

#### Type B --- Reasoned Interpretation

Example:

> "AI may increase the importance of verification skills."

These benefit from reasoning and may benefit from evidence.

#### Type C --- Factual Claim

Example:

> "AI will replace 40% of jobs."

These require credible evidence.

#### Type D --- High-Stakes Claim

Examples involving:

-   Medicine
-   Law
-   Finance
-   Child development
-   Employment statistics
-   Public policy

These require stronger evidence.

### H. Factual and Conceptual Accuracy --- 10 points

Evaluate whether the article is:

-   Factually accurate
-   Conceptually accurate
-   Technically reasonable
-   Appropriately nuanced

Look for:

-   Incorrect generalizations
-   Outdated assumptions
-   False technical claims
-   Misleading simplifications
-   Unsupported predictions

The evaluator MUST distinguish between:

#### Incorrect

Factually wrong.

#### Oversimplified

Directionally correct but incomplete.

#### Unsupported

May be correct, but lacks evidence.

#### Opinion

A legitimate interpretation or value judgment.

These categories MUST NOT be treated as identical.

### I. Original Insight and Intellectual Value --- 10 points

Evaluate whether the article provides:

-   A unique perspective
-   A memorable framework
-   A useful synthesis
-   A new interpretation
-   Strong conceptual framing

Generic advice should receive lower scores.

Example:

> "Children should learn critical thinking."

This is useful but common.

A stronger version might introduce a framework:

> Ask → Verify → Create → Decide

A memorable framework increases the intellectual value of an article.

The evaluator SHOULD ask:

> "After reading this article, what idea is the reader likely to
> remember?"

If the answer is unclear, the article may lack a strong central insight.

### J. Publication Readiness --- 5 points

Evaluate:

-   Is the article ready to publish?
-   Does it need fact-checking?
-   Does it need editing?
-   Does it need citations?
-   Does it need restructuring?

Classify readiness:

  Level            Meaning
  ---------------- -------------------------------------
  Publish Ready    Can be published with minimal edits
  Minor Revision   Needs small improvements
  Major Revision   Core content needs improvement
  Not Ready        Significant issues remain

## 5. Critical Flaw Detection

The evaluator MUST perform a separate Critical Flaw scan.

A Critical Flaw is a weakness that significantly reduces trust,
accuracy, or effectiveness.

Critical flaws are NOT simply low scores.

The evaluator MUST explicitly identify them.

### CF-01: Missing Evidence for Major Claims

The article makes significant factual claims without evidence.

Example:

> "AI will eliminate most jobs within 10 years."

without credible support.

**Severity: HIGH**

### CF-02: Title--Content Misalignment

The title promises one thing but the article mainly discusses something
else.

Example:

Title:

> "What kind of person should children become?"

Article body:

Mostly lists technical skills.

**Severity: MEDIUM to HIGH**

### CF-03: False Dichotomy

The article frames a complex issue as two opposing choices.

Example:

> Human vs AI

when the reality involves augmentation and collaboration.

**Severity: MEDIUM**

### CF-04: Overgeneralization

The article presents a broad claim without acknowledging important
exceptions.

Example:

> "Jobs with rules can easily be replaced by AI."

**Severity: MEDIUM to HIGH**

### CF-05: Generic Insight

The article is well written but offers only common advice.

Symptoms:

-   Many agreeable statements
-   Few original ideas
-   No memorable framework
-   No meaningful synthesis

**Severity: MEDIUM**

### CF-06: Unsupported Emotional Framing

The article uses emotionally powerful language that implies factual
conclusions.

Example:

> "AI has no heart, therefore humans will always be more valuable."

**Severity: MEDIUM**

### CF-07: Advice Without Mechanism

The article tells readers what to do but does not explain how.

Example:

> "Teach children critical thinking."

without examples, exercises, or methods.

**Severity: MEDIUM**

## 6. Severity Classification

Every identified issue MUST receive one of these levels.

### Critical

May cause serious misinformation or make publication unsafe.

Examples:

-   Major factual error
-   Dangerous advice
-   False statistical claims
-   High-stakes unsupported claims

### High

Significantly damages credibility or article purpose.

Examples:

-   Major claims without evidence
-   Title-content mismatch
-   Major logical contradiction

### Medium

Reduces depth or quality.

Examples:

-   Generic advice
-   Oversimplification
-   Weak examples

### Low

Editorial or stylistic improvement.

Examples:

-   Repetition
-   Word choice
-   Minor transition problems

## 7. Recommended AI-Era Framework

When evaluating articles about children, education, future skills, or
AI, consider whether the article could benefit from this framework.

# LEARN → THINK → CREATE → DECIDE

## 1. LEARN

Ability to learn continuously.

Key qualities:

-   Curiosity
-   Adaptability
-   Independent learning
-   Willingness to update beliefs

Question:

> Can this person continue learning when knowledge changes?

## 2. THINK

Ability to think critically.

Key qualities:

-   Ask questions
-   Analyze information
-   Detect weak reasoning
-   Verify claims
-   Understand context

Question:

> Can this person distinguish an answer from a good answer?

## 3. CREATE

Ability to create new value.

Key qualities:

-   Imagination
-   Problem solving
-   Combining ideas
-   Producing original work

Question:

> Can this person use technology to create rather than only consume?

## 4. DECIDE

Ability to make decisions and accept responsibility.

Key qualities:

-   Judgment
-   Ethical reasoning
-   Accountability
-   Understanding consequences

Question:

> Can this person decide what should be done even when AI can suggest
> what could be done?

## 8. Character vs Skill Check

For articles discussing the future of children, education, or human
development, the evaluator MUST distinguish:

### Skills

What a person can do.

Examples:

-   Coding
-   Writing
-   Analysis
-   Communication

### Character

What kind of person someone becomes.

Examples:

-   Responsibility
-   Curiosity
-   Courage
-   Empathy
-   Integrity
-   Adaptability

The evaluator SHOULD flag articles when:

> The title asks about "what kind of person someone should become"

but the article only discusses skills.

This is a potential Title--Content Alignment issue.

## 9. Scoring Output Format

The final evaluation MUST include the following sections.

### A. Executive Score

Example:

> Overall Score: 8.2 / 10

One short paragraph explaining the overall judgment.

### B. Evaluation Table

  Category                            Weight   Score Assessment
  --------------------------------- -------- ------- ------------
  Topic and Reader Relevance              10       X ...
  Structure and Information Flow          10       X ...
  Language and Communication              10       X ...
  Audience Fit                            10       X ...
  Content Value                           10       X ...
  Analytical Depth                        10       X ...
  Evidence and Credibility                15       X ...
  Factual and Conceptual Accuracy         10       X ...
  Original Insight                        10       X ...
  Publication Readiness                    5       X ...
  Total                                  100       X ...

Convert the total score to a 10-point scale.

### C. Strengths

Identify the 3--5 strongest qualities.

Each strength MUST include:

1.  What is strong.
2.  Why it matters.
3.  Where it appears in the article.

### D. Critical Flaws

This section is mandatory.

Use this format:

#### Severity: HIGH

**Problem**

Explain the flaw.

**Why it matters**

Explain the impact.

**How to fix it**

Provide a concrete solution.

### E. Improvement Opportunities

Prioritize improvements by impact.

#### Priority 1 --- Highest Impact

Changes that could significantly improve the article.

#### Priority 2 --- Important

Changes that improve depth or clarity.

#### Priority 3 --- Optional

Stylistic or optimization improvements.

### F. Suggested Framework

If the article lacks a memorable conceptual structure, suggest one.

Examples:

-   Learn → Think → Create → Decide
-   Ask → Verify → Create → Take Responsibility
-   Context → Evidence → Judgment → Action

The suggested framework MUST fit the article topic.

Do not force the same framework into unrelated articles.

### G. Publication Classification

Classify the article independently across multiple standards.

Example:

  Standard                   Rating
  ------------------------ --------
  General Blog               9.0/10
  Editorial                  8.2/10
  Thought Leadership         7.0/10
  Evidence-Based Article     5.5/10

This prevents unfair evaluation.

A good blog article may legitimately receive a lower score as an
academic article.

## 10. Final Recommendation

The evaluator MUST end with:

### Overall Verdict

One of:

-   Excellent
-   Very Good
-   Good
-   Needs Major Revision
-   Not Ready

Then answer:

> What is the single most important change that would improve this
> article?

Only ONE primary recommendation should be selected.

The evaluator SHOULD identify the highest-leverage improvement.

Example:

> Add credible evidence to support the article's major claims.

## 11. Evaluation Behavior Rules

The evaluator MUST:

-   Be constructive.
-   Be specific.
-   Separate fact from opinion.
-   Avoid vague praise.
-   Explain why a score was assigned.
-   Identify critical flaws explicitly.
-   Consider intended audience.
-   Consider article type.
-   Avoid applying academic standards unnecessarily.
-   Avoid rewarding confident writing if evidence is weak.
-   Avoid penalizing simple language if it is effective.
-   Avoid treating AI-generated writing as inherently poor.
-   Focus on the article itself.

The evaluator MUST NOT:

-   Give a high credibility score simply because the writing sounds
    professional.
-   Assume unsupported claims are false.
-   Assume a lack of citations automatically makes every article poor.
-   Confuse emotional impact with factual accuracy.
-   Confuse readability with analytical depth.
-   Give only generic advice such as "add more details."
-   Rewrite the entire article unless explicitly requested.

## 12. Quality Calibration

Use these approximate interpretations.

### 9.0--10.0 --- Excellent

Strong structure, strong value, high clarity, high credibility for its
article type, and strong insight.

### 8.0--8.9 --- Very Good

Clearly useful and well written, with some opportunities for stronger
evidence, depth, or originality.

### 7.0--7.9 --- Good

Useful but has meaningful weaknesses.

### 6.0--6.9 --- Moderate

The article works but requires significant improvement.

### 4.0--5.9 --- Weak

Major problems in clarity, evidence, structure, or relevance.

### Below 4.0 --- Not Ready

Requires substantial revision before publication.

## 13. Evaluation Philosophy

The ultimate goal is not to produce a perfect score.

The goal is to answer:

> Is this article trustworthy?

> Is it useful?

> Is it appropriate for its audience?

> Does it communicate something meaningful?

> Does it provide enough reasoning or evidence?

> Will the reader remember something valuable after reading it?

A strong article should not merely make the reader say:

> "Yes, that sounds correct."

A stronger article should make the reader understand:

> "I now see this issue differently."

And the best article should help the reader:

> Think differently, decide better, or act more effectively.
