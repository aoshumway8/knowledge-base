# questions — Interview Question Design

## Core Knowledge Base

### Question Crafting Anti-Patterns

**Failure signatures (mechanical detection):**

- `leading-prompt` — question contains evaluative adjectives, social proof cues, or embedded assumptions ("Was it frustrating when...?", "How easy did you find...?", "Most users find this saves time — have you noticed that?")
- `closed-framing` — question permits a yes/no answer and shuts down elaboration ("Did you use it?", "Was that clear?")
- `hypothetical-future` — question asks what the participant *would* do rather than what they *did* ("Would you use this feature?"); generates fiction dressed as data
- `compound-question` — single prompt bundles two or more distinct asks ("How did you find it and what would you change?")
- `interpreter-injection` — clarifying restatement adds meaning not said by the participant ("So you mean it was confusing?")
- `validation-seeking` — question embeds the researcher's hypothesis as the frame, distinguishable from leading-prompt in that it smuggles *research intent* rather than evaluative language ("Which feature made you visit our site?" vs. "What brought you to our site today?"; "To what extent was the visual design a factor?" vs. "What factors influenced your decision?")
- `atypical-omission` — question asks about general or "typical" behavior rather than specific instances ("How do you usually handle this?" instead of "Walk me through the last time this came up"); typical behavior is reconstructed and rationalized, not recalled
- `ambiguous-prompt` — question is underspecified enough that different participants will interpret it differently, making responses impossible to compare or synthesize ("What do you think about the experience?")

**Correction logic:**
- Rewrite `Did/Was/Would` openers into `How/What/Tell me/Walk me through`
- Split compound prompts into sequential single-focus questions
- Replace assumptions with concrete recall prompts ("Tell me about the last time you...")
- Replace typical-behavior questions with specific-instance prompts ("When did that last happen?", "Can you walk me through that experience?")
- Strip researcher hypothesis from the question frame entirely — test whether removing the framing changes what answers become possible
- Use language that permits positive, neutral, and negative responses equally
- For sensitive topics, use **probe without presuming** (Portigal): surface the range of possible views without tying them to anyone ("Some people have strong feelings about X, others don't — what's your take?")

**Fluff detection (Mom Test):** Three response types signal unreliable data and require immediate redirection:
- Generic claims — "I always...", "I usually..." → redirect: "When did that last happen?"
- Hypothetical maybes — "I might...", "I could see myself..." → redirect: "Have you actually done that? Tell me about it."
- Future-tense promises — "I would...", "I'd definitely..." → redirect: "What have you done so far to solve this problem?"

### Facilitation Mistakes

Distinct from question-crafting errors — these occur during the session itself. NNG identifies five core facilitation failures:

- **Insufficient rapport-building** — jumping into research questions without warm-up produces guarded, performative answers; participants haven't established trust and default to socially acceptable responses
- **Under-probing** — accepting the first answer and moving on; surface responses are almost never the real insight; probing is where signal is found
- **Multitasking during the session** — splitting attention between note-taking, observing, and listening prevents the interviewer from catching weak signals, unexpected threads, or emotional cues
- **Observer influence** — allowing stakeholders in the room (or on a call) to react, interject, or visibly signal approval/disapproval trains participants toward desirable responses
- **Body language and verbal reinforcement** — nodding enthusiastically, saying "Great!" or "Interesting!" at specific responses trains participants to produce more of whatever triggered the positive signal; use neutral verbal acknowledgments ("Thank you for sharing that") instead
- **Note-taking bias** — recording only information that connects to the research question signals to participants which topics are "wanted," skewing their responses toward those areas

**Over-rapport warning (IDEO):** Building too much friendliness before the session creates a friendliness bias where participants agree with everything to maintain the pleasant social dynamic. The goal of warm-up is trust and comfort, not friendship.

### Depth Techniques

- **The Mom Test** (Rob Fitzpatrick) — three rules: (1) talk about their life, not your idea — mentioning your solution contaminates the conversation with social pressure; (2) ask about specifics in the past, not generics or opinions about the future; (3) talk less, listen more. The reframe: you're not there to find out if people like your idea, you're there to find out if they have the problem you think they have and how they're currently dealing with it. Use when teams are attached to a solution concept or when guides are oriented around validating a feature.

- **TEDW prompts** — four narrative openers, each serving a distinct purpose: **Tell** encourages elaboration from hesitant or terse participants; **Explain** surfaces reasoning and rationale; **Describe** enables free-form storytelling and sensory detail; **Walk me through** creates chronological narratives that reveal the participant's actual thought process. Use at topic openings to replace closed or leading openers. "Did you like the checkout flow?" → "Walk me through what happens when you go to check out."

- **Laddering** (Means-End Chain Theory, Reynolds and Gutman) — connects product attributes to personal values that drive decisions. **Laddering up**: ask "Why is that important?" repeatedly to move from concrete features → functional consequences → psychosocial consequences → core values. **Laddering down**: ask "Can you give me an example?" to move from vague statements to actionable specifics. Example sequence: "What do you like about this tool?" → "The Kanban view" (attribute) → "Why does that matter?" → "I can see everything at a glance" (functional consequence) → "Why does that matter?" → "I don't miss deadlines and look unprepared" (psychosocial) → "Why is that important?" → "I value being reliable and competent in my team" (core value). The design insight shifts from "users like Kanban boards" to "users need to protect their professional reputation through visible reliability signals."

- **5 Whys** — iteratively asks why a behavior or feeling occurred. Key improvement (Peter Horvath): categorize responses into four depth levels: (1) events — what happened; (2) patterns — how often; (3) structures and processes — systematic causes; (4) mental models and values — underlying beliefs. Most researchers stop at level one; root causes live at level three or four. Critical rule: never literally repeat "Why?" each time — rephrase creatively ("What made you stop there?", "What was driving that?"). Warn participants you'll probe deeply and frame it as genuine curiosity, not interrogation.

- **Critical Incident Technique** (John C. Flanagan, 1954; recommended by NNG) — ask participants to recall specific times when a product or process had a *significant* positive or negative impact. The distinction from generic recall matters: "Tell me about a time you used the tool" yields a random, possibly trivial example. "Tell me about a time you used the tool and it made you really effective" yields a significant, memorable event with causal information. Always collect both positive and negative incidents, starting with positive. Give participants time — memory retrieval takes genuine effort, and rushing produces thin answers.

- **Echo/mirroring** (Chris Voss, FBI hostage negotiation) — repeat one to three emotionally charged words from the participant's last statement with rising intonation. Participant: "I was really frustrated with the checkout." Researcher: "Frustrated with the checkout...?" The technique is less threatening than "Why?", feels conversational rather than clinical, and opens space for elaboration without the researcher directing content. Requires practice to feel natural; once internalized it becomes one of the most efficient depth tools available.

- **Strategic silence** — pause deliberately after a response before speaking. In most Western interview contexts, four seconds of silence feels uncomfortable and motivates participants to fill the void with more authentic, less socially managed responses. NNG recommends counting to seven before responding. The documented effect: without silence a participant's surface claim goes unchallenged; with five seconds of silence the same participant volunteers contradiction, context, and the real insight. Silence is most powerful immediately after wrap-up questions and after responses that feel rehearsed or incomplete.

- **Jobs to Be Done switching interviews** (Clayton Christensen; practitioner Jason Evanish) — reframe the investigation around the moment someone "hired" a new product and what forces caused them to switch away from their previous solution. Four forces: **push** (frustration and inadequacy of the current solution), **pull** (attraction toward the new solution), **anxiety** (fears and risks associated with switching), **habit** (inertia and comfort keeping them with the status quo). The richest insight typically emerges 20–25 minutes into the session, after context and comfort are established; the first portion is essential preparation, not wasted time. Use when investigating adoption, churn, or switching behavior.

### Interview Architecture

**Session flow with timing:**

1. **Setup and contract (3–5 minutes)** — introduce yourself, explain the format ("chat" not "interview" — NNG; lower-anxiety framing), confirm recording consent, establish psychological safety. Portigal calls this "setting the contract": a thumbnail outline of what to expect without creating pressure or signaling what answers are wanted.

2. **Warm-up and rapport (5–7 minutes)** — ask easy, open-ended questions about participant background and context. Goal is trust, not friendship. IDEO warns against over-rapport: excessive friendliness creates a social dynamic where participants agree with everything to preserve the pleasant interaction. The warm-up should feel relaxed, not performative.

3. **Deep dive (20–40 minutes)** — open with a **grand tour question** ("Walk me through a typical week when you...") to let the participant establish their own frame before the researcher introduces any structure. Then funnel into specifics using the **Funnel Technique** (NNG, Maria Rosala and Kate Moran): broad open-ended question → open-ended follow-ups → targeted closed questions only at the end of each topic. Apply TEDW prompts, CIT, laddering, and 5 Whys as conditions warrant. Target participant talk-time at approximately 80% of session time. If following an unexpected but genuinely revealing thread, follow it — return to the guide afterward.

4. **Wrap-up and silence (3–5 minutes)** — ask "Is there anything else you'd like to share that we haven't covered?" and then wait. Most participants will initially say no. Wait again. The final silence regularly produces the single most valuable insight of the session. Do not turn off the recorder until the participant has had the full opportunity to fill that silence. (Stéphanie Walter)

**The Funnel Technique** (NNG) — foundational to session architecture and individual topic sequences alike. Starting with broad questions avoids influencing perceptions before the participant has established their own narrative. Introducing specifics or closed questions too early risks introducing bias and causing the researcher to miss unexpected data. Every major topic should follow its own funnel: broad open question → open follow-ups → closed questions only to confirm or clarify.

**Guide construction:**
- Prioritize 5–8 major open-ended questions per session
- Attach planned follow-up probes to each major question
- Maintain a reusable generic probe bank ("Tell me more about that." "What made you feel that way?" "Can you walk me through what happened?")
- Treat the guide as a launchpad, not a script — Portigal: good interviewing is like jazz improvisation; learn the chords so thoroughly you can forget about them and follow the participant
- If a participant takes the conversation in an unexpected and genuinely interesting direction, follow them; the most valuable insight is often the one you didn't think to plan for

### Researcher Psychology and Self-Management

The most underappreciated variable in interview quality is the researcher's own psychology. Confirmation bias, social desirability effects, and sunk-cost attachment to hypotheses all operate below conscious awareness and distort both the questions asked and how answers are interpreted.

**Before the interview:**
- Write your assumptions down explicitly before drafting the guide — acknowledgment makes confirmation-seeking visible
- Run an assumptions mapping session with the team to surface shared beliefs that the research might accidentally validate rather than test
- Frame the study as hypothesis testing, not design validation: the goal is to discover what is true, not to confirm what you believe
- Peer-review the guide with someone uninvolved in the project — ask them to flag leading language, embedded hypotheses, and loaded framing
- Apply the **"scary question" test** (Fitzpatrick): if every question in the guide feels comfortable, you're probably seeking validation; include at least one question whose answer could disprove the core assumption

**During the interview:**
- Use neutral verbal acknowledgments only — "Thank you for sharing that" not "Great!" or "Interesting!"; positive reinforcement at specific responses trains participants to produce more of whatever triggered it
- Monitor body language — nodding, leaning in, or showing surprise at confirming responses all signal to participants which content is "wanted"
- Avoid selective note-taking — recording only information connected to the research question signals that some topics are more interesting or desirable than others, skewing subsequent responses toward those areas
- Resist the urge to fill silence, correct misunderstandings, or share your own opinions and experiences

**After the interview:**
- Resist forming conclusions after any single session — synthesis before all data is collected produces premature patterns shaped by early, potentially unrepresentative sessions
- Use triangulation: combine interview data with analytics, behavioral logs, support records, and survey data; a single finding can be twisted to match a hypothesis; multiple independent data sources are far harder to distort
- Have team members from different backgrounds independently review the same data before comparing interpretations to surface where readings diverge

### Signal Quality Heuristics

**Low-signal surface indicators:**
- Generic claims and abstract language ("I always...", "I usually...")
- Fast agreement with interviewer framing — participant mirrors the question's vocabulary back
- Short responses lacking concrete context
- Hypothetical or future-tense language ("I would probably...", "I might...")
- Absence of people, places, times, or consequences in the account

**High-signal insight indicators:**
- Specific events with time, place, people, and actions — the hallmark of genuine recall, not reconstruction
- Emotional nuance, contradiction, and self-correction — participant catches themselves ("Actually wait, that's not quite right...")
- Unprompted elaboration and causal narrative detail — going beyond what the question required
- Voice changes — slowing down, pausing to think, shifts in tone indicating genuine processing rather than a rehearsed answer
- Surprise to the participant themselves — "Huh, I never thought about it that way" indicates the conversation has reached genuine reflection, not performance
- Emergence of detail the participant didn't initially volunteer but produces after silence or a light probe

**When you see high-signal indicators: slow down, stay with it.** These are the moments where deeper probing — echo technique, 5 Whys, laddering — should be deployed rather than moving on to the next guide question.

### Named Frameworks and Sources

- **Nielsen Norman Group** (Maria Rosala, Kate Moran, Kate Kaplan, Therese Fessenden) — interview and moderation guidance; Funnel Technique; six question-crafting mistakes and five facilitation mistakes; strategic silence documentation
- **Steve Portigal** — interviewing and ethnographic research practices; "probe without presuming" technique; "setting the contract"; the guide-as-jazz-improvisation framing; concept of "user-centered theater" (conducting research to confirm, not discover)
- **Rob Fitzpatrick — The Mom Test** — ask about life, not the idea; past, not future; listen more than talk; fluff detection (generic claims, hypothetical maybes, future promises); the "scary question" test
- **IDEO** — interviewing approach and practical facilitation patterns; over-rapport / friendliness bias warning
- **Kvale and Brinkmann** — qualitative interviewing methods and academic grounding
- **Clayton Christensen / JTBD** — Jobs to Be Done framework; switching behavior and the four forces (push, pull, anxiety, habit)
- **Jason Evanish** — JTBD practitioner; "the best stuff comes around 20–25 minutes in"
- **John C. Flanagan (1954)** — originator of the Critical Incident Technique
- **Reynolds and Gutman** — Means-End Chain Theory; the academic basis for the laddering technique
- **Peter Horvath** — four depth levels for 5 Whys application (events → patterns → structures → mental models)
- **Chris Voss — Never Split the Difference** — FBI hostage negotiation; origin of the echo/mirroring technique for depth probing
- **Nikki Anderson (Dscout)** — research practitioner; documented warm-up transformation effect on session depth
- **Stéphanie Walter** — documented the final silence phenomenon in wrap-up ("don't turn off the recorder")

---

## Scope Boundaries

**In scope:**
- Interview guide design and critique
- Question crafting and bias detection
- Moderation and facilitation technique
- Probe bank construction
- Signal quality assessment
- Synthesis framing and evidence criteria

**Out of scope:**
- Quantitative survey design
- Usability test scripting
- Data synthesis and affinity mapping (downstream of this lane)
- Method selection across the full research toolkit (handled by design-evaluation)

**Overlap handling:**

| Topic | Primary | Secondary |
|-------|---------|-----------|
| Research method selection | design-evaluation | questions |
| Synthesis and triangulation | design-evaluation | questions |
| Facilitation rigor in usability sessions | questions | design-evaluation |

---

## Domain Checklist

**Pre-interview / guide design:**
1. Identify the research objective and the decision it must inform
2. Identify participant type and context
3. Write down team assumptions explicitly before drafting — make confirmation-seeking visible
4. Flag assumption-heavy areas and likely confirmation-bias traps before drafting
5. Review each question for leading language, closed framing, and hypothetical structure
6. Check for validation-seeking framing — strip researcher hypothesis from question entirely
7. Check for atypical-omission — ensure questions ask about specific past instances, not just "typical" behavior
8. Check for ambiguous prompts — each question should be interpretable only one way
9. Check for compound questions and split them into single-focus prompts
10. Verify funnel structure: broad → specific across the session arc, and within each topic
11. Confirm the guide opens with a grand tour question before introducing structure
12. Confirm 5–8 major questions with planned probes attached to each
13. Verify the probe bank covers likely vague-answer scenarios
14. Apply the scary question test — at least one question should feel risky to ask because the answer could disprove a core assumption
15. Peer-review the guide with someone uninvolved in the project for embedded assumptions
16. Check that phase timing is realistic: setup 3–5 min, warm-up 5–7 min, deep dive 20–40 min, wrap-up 3–5 min

**During session / facilitation:**
17. Assess participant talk-time dominance vs. interviewer talk-time (target: ~80% participant)
18. Flag guides that script facilitation so tightly that unexpected threads cannot be followed
19. Verify the probe bank covers likely vague-answer and fluff-response scenarios

**Session output / synthesis:**
20. Check wrap-up includes an open floor, deliberate silence, and no premature recorder shutdown
21. Flag guides optimizing for idea validation over behavioral discovery
22. Flag guides that will produce premature synthesis shaped by existing stakeholder assumptions
23. Confirm evidence criteria are defined — what will count as actionable insight
24. Confirm post-interview triangulation plan — other data sources to combine with interview findings

---

## Severity Mapping

| Severity | Threshold |
|----------|-----------|
| High | Leading questions or hypotheticals that will systematically bias all responses; validation-seeking framing on primary research questions; no funnel structure; participant talk-time below 50%; guide has no probes; no warm-up phase |
| Medium | Compound questions on core topics; missing probe bank; closed framing on primary questions; atypical-omission on key behavioral questions; ambiguous prompts on core topics; no scary question in the guide |
| Low | Minor wording choices that slightly narrow response range; probe coverage gaps on secondary topics; atypical-omission on lower-priority questions |
| Polish | Probe phrasing that could be tightened; session flow order that could be optimized; missing grand tour question at deep dive opening |

---

## Output Contract

**agent field value:** `questions`

**rootCause slug format:** kebab-case, 3–6 words, neutral framing
- Examples: `leading-question-framing`, `missing-behavioral-probe`, `hypothetical-not-behavioral`, `compound-question-structure`, `low-participant-talk-time`, `premature-synthesis-framing`, `validation-seeking-frame`, `atypical-behavior-only`, `ambiguous-prompt-structure`, `missing-scary-question`, `researcher-hypothesis-embedded`, `missing-grand-tour-question`, `over-rapport-friendliness-risk`

**title constraints:** name the question pattern failure, not the symptom it produces

**description structure:** principle → mechanism → failure
- State the interviewing principle at stake; explain how the violation occurs in this question or guide; describe the resulting bias or signal loss

**testerEvidence default:** cite at least one observed interview behavior or participant response pattern that triggered the finding

**ethics flag:** include when question design risks participant harm, deception without debriefing, data misuse, or privacy boundary violation

**Feedback style:** Direct and practical. Deliver concrete rewrites alongside the diagnosis. Recommendations prioritize truth-seeking, behavioral evidence, and decision-ready outputs. Explicitly call out bias risks and name the mitigation tactic — do not leave the reader to infer the fix.

**When drafting interview guides:**
- Objective-aligned interview structure with broad-to-specific flow
- Primary questions rewritten to avoid bias and hypotheticals
- Probe bank mapped to likely vague-answer scenarios
- Facilitation guardrails for neutrality and listening discipline
- Evidence criteria defining what counts as actionable insight

**When reviewing existing guides:**
- Line-by-line issue list with severity and rationale
- Before/after rewrites for weak or leading questions
- Coverage gaps by objective, participant context, and decision risk
- Recommended techniques to improve depth and truthfulness
- Post-interview synthesis and triangulation recommendations
