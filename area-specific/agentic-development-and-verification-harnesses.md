# Agentic Development and Verification Harnesses

[Paul Dix's "The end of programming"](https://pauldix.com/the-end-of-programming) is the starting
point for this path. It is not part of the time budget because it has already been read.

The central question is not whether an agent can write a large amount of code. It can. The harder
question is where rigor moves when a human no longer reads every line.

This path covers two connected subjects:

1. How experienced developers are changing the software development loop around coding agents.
2. How classical testing methods can become a behavioral search and verification system.

This is a reading-only path. It has no implementation exercises. The core path takes about 2 hours
and 20 minutes. The optional reads add counterarguments and deeper testing methods.

Here, a **verification harness** means a system that collects repeatable evidence about software.
It does not mean a mathematical proof.

---

## The mental model to carry

A harness is more than a large test suite. It is a control system around a non-deterministic
worker.

- **Humans define the target:** intended behavior, invariants, unacceptable failures, architecture
  limits, and release criteria.
- **Agents search:** they implement, generate examples, explore states, run tools, and fix observed
  failures.
- **The harness supplies independent feedback:** tests, types, linters, reference models, fault
  injectors, performance limits, and checks of external system state.
- **The meta-harness checks the feedback:** mutation testing, held-out scenarios, independent
  reviewers, and production signals help reveal weak or gameable checks.

The practical shift is not from tests to prompts. It is from hand-enumerating every case to
designing **search spaces and independent oracles**.

Keep this vocabulary in mind:

- A **characterization oracle** records what an existing system does. It is strong for a rewrite,
  but it can preserve old bugs and blind spots.
- A **differential oracle** compares independent implementations of the same behavior.
- A **property** or **invariant** must hold across many inputs or state transitions.
- A **metamorphic relation** says how outputs must relate after an input transformation. It helps
  when the exact answer is unknown.
- A **model-based test** applies generated actions to both the real system and a smaller reference
  model.
- A **fuzzer** or **generator** searches the input or state space. It still needs an oracle that
  can detect a bad result.
- A **fault injector** searches environmental failures, such as crashes, full disks, or dropped
  messages.
- **Mutation testing** inserts plausible defects and asks whether the test suite detects them.
- An **outcome evaluation** checks the final system state. It does not trust the agent's claim that
  the task succeeded.

---

## Core path: about 2 hours 20 minutes

Read these in order. The sequence moves from the current debate to a framework, then to the testing
methods that can make the framework real.

### 1. The case study: a rewrite driven by an oracle

1. **[Bun is being rewritten in Rust](https://bun.com/blog/bun-in-rust)** - Jarred Sumner
   - **Tags:** Cutting Edge, Pattern Recognition, Practical
   - **Time:** 20 minutes
   - **Read:** Start at "Claude, rewrite Bun in Rust." Focus on the workflow loops, adversarial
     review, and test-suite sections.
   - **Why read this:** This is the primary account behind Dix's essay. The useful unit is not the
     million lines of code. It is the complete system around them: a mechanical porting plan, a
     language-independent behavior oracle, separate adversarial reviewers, compiler feedback,
     sanitizers, fuzzing, and repeated correction.
   - **Watch for:** Bun had an unusually strong oracle and a source implementation to compare
     against. A rewrite with feature parity is more harnessable than a new product with ambiguous
     behavior.

### 2. Move the human from the code loop to the control loop

2. **[Humans and Agents in Software Engineering Loops](https://martinfowler.com/articles/exploring-gen-ai/humans-and-agents.html)**
   - **Tags:** Mental Model, Cutting Edge
   - **Time:** 15 minutes
   - **Why read this:** Kief Morris separates the human "why" loop from the agent "how" loops. His
     strongest idea is to put humans **on the loop**. When an agent produces a bad result, improve
     the system that produced it instead of only fixing the artifact.
   - **Carry forward:** The human still owns outcomes and decides where human judgment is needed.

3. **[Harness Engineering](https://martinfowler.com/articles/harness-engineering.html)** - Birgitta
   Böckeler
   - **Tags:** Mental Model, Practical, Cutting Edge
   - **Time:** 25 minutes
   - **Read:** Focus on guides versus sensors, computational versus inferential controls, the three
     harness categories, and the section on behavior harnesses.
   - **Why read this:** This is the clearest current framework for the topic. Guides shape work
     before it starts. Sensors report what happened. Deterministic tools and model-based judgment
     have different costs and trust levels.
   - **Key warning:** Maintainability and architecture are easier to harness than behavior. A green
     AI-generated test suite is not yet enough to remove human supervision.

### 3. Define evidence before asking an agent to optimize it

4. **[Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)**
   - **Tags:** Foundational, Practical, Cutting Edge
   - **Time:** 20 minutes
   - **Read:** "The structure of an evaluation," "Why build evaluations?", "Capability vs.
     regression evals," "Evaluating coding agents," and the roadmap near the end.
   - **Why read this:** The article is about evaluating agents, but its vocabulary applies directly
     to agent-written software. It separates tasks, trials, graders, traces, outcomes, harnesses,
     capability checks, and regression checks.
   - **Carry forward:** Check external state when possible. A convincing transcript is not proof
     that the system reached the correct outcome.

### 4. Replace isolated examples with properties and relations

5. **[Choosing properties for property-based testing](https://fsharpforfunandprofit.com/posts/property-based-testing-2/)**
   - **Tags:** Foundational, Pattern Recognition, Practical
   - **Time:** 15 minutes
   - **Why read this:** Scott Wlaschin gives a practical vocabulary for finding properties:
     inverses, invariants, idempotence, commutativity, smaller reference implementations, and
     results that are hard to find but easy to verify.
   - **Carry forward:** A human does not need to list every input. The human must identify what
     remains true across the generated input space.

6. **[Metamorphic Testing](https://www.hillelwayne.com/post/metamorphic-testing/)** - Hillel Wayne
   - **Tags:** Foundational, Pattern Recognition, Mental Model
   - **Time:** 20 minutes
   - **Why read this:** Wayne addresses the oracle problem directly. When the exact answer is hard
     to state, transform the input and assert a required relation between the outputs.
   - **Carry forward:** The richest behavioral checks often compare executions rather than one
     output with one hand-written expected value.

### 5. See a mature verification system, not one testing technique

7. **[How SQLite Is Tested](https://sqlite.org/testing.html)** - SQLite
   - **Tags:** Foundational, Practical, Pattern Recognition
   - **Time:** 25 minutes
   - **Read:** Sections 1 through 4. Focus on independent test harnesses, SQL Logic Test, anomaly
     testing, fault injection, crash testing, and fuzzing.
   - **Why read this:** SQLite shows what a behavior harness looks like as a system. It combines
     examples, parameterized cases, differential testing, branch coverage, fault injection,
     integrity checks, fuzzing, and independent implementations.
   - **Carry forward:** Confidence comes from several oracles with different failure modes. Test
     count or coverage alone is not the goal.

---

## Optional thought leaders and counterarguments

8. **[My AI Adoption Journey](https://mitchellh.com/writing/my-ai-adoption-journey)** - Mitchell
   Hashimoto
   - **Tags:** Practical, Mental Model
   - **Time:** 10 minutes for "Step 5: Engineer the Harness"
   - **Why read this:** A concise practitioner account of turning every repeated agent mistake into
     a durable tool, instruction, or feedback path.

9. **[Vibe coding and agentic engineering are getting closer than I'd like](https://simonwillison.net/2026/May/6/vibe-coding-and-agentic-engineering/)**
   - **Tags:** Perspective, Mental Model
   - **Time:** 15 minutes
   - **Why read this:** Simon Willison explains why he has stopped reading every line in some
     production changes, then examines the accountability and normalization risks that follow.

10. **[The Tower Keeps Rising](https://lucumr.pocoo.org/2026/7/13/the-tower-keeps-rising/)** -
    Armin Ronacher
    - **Tags:** Perspective, Mental Model
    - **Time:** 10 minutes
    - **Why read this:** A strong counterweight to the "code becomes an unread implementation
      detail" view. Tests can pass while the shared human model of boundaries, ownership, and
      invariants disappears.

11. **[TDD inside the agent loop: theater or actual value?](https://martinfowler.com/articles/exploring-gen-ai/tdd-in-the-agent-loop.html)**
    - **Tags:** Cutting Edge, Practical, Mental Model
    - **Time:** 15 minutes
    - **Why read this:** A useful early experiment that challenges a simple answer. If one agent
      writes the implementation and its tests, red-green TDD does not guarantee an independent
      oracle. Tests can restate the implementation or compare output with itself.

12. **[Specification gaming: the flip side of AI ingenuity](https://deepmind.google/discover/blog/specification-gaming-the-flip-side-of-ai-ingenuity/)**
    - **Tags:** Foundational, Mental Model
    - **Time:** 10 minutes
    - **Why read this:** Any capable optimizer can satisfy the written score while violating the
      intended goal. This is the core risk when a visible test suite becomes an agent's target.

---

## Optional testing deep dives

13. **[An Introduction to Rule-Based Stateful Testing](https://hypothesis.works/articles/rule-based-stateful-testing/)**
    - **Tags:** Foundational, Practical, Pattern Recognition
    - **Time:** 20 minutes
    - **Why read this:** Property-based testing grows from generated values into generated programs.
      The human defines actions, preconditions, and a small model. The framework searches action
      sequences and shrinks a failure to a useful trace.

14. **[Simulation and Testing](https://apple.github.io/foundationdb/testing.html)** - FoundationDB
    - **Tags:** Foundational, Pattern Recognition, Practical
    - **Time:** 10 minutes
    - **Why read this:** Deterministic simulation turns time, networks, disks, machines, and failures
      into controllable test inputs. One seed can reproduce a whole distributed-system failure.

15. **[Simulation Testing for Liveness](https://tigerbeetle.com/blog/2023-07-06-simulation-testing-for-liveness/)** -
    TigerBeetle
    - **Tags:** Cutting Edge, Pattern Recognition, Practical
    - **Time:** 15 minutes
    - **Why read this:** A concrete example of improving the harness rather than fixing one bug.
      The team changes its fault model so the simulator can search for an entire class of liveness
      failures.

16. **[Sensors for Coding Agents](https://martinfowler.com/articles/sensors-for-coding-agents.html)** -
    Birgitta Böckeler
    - **Tags:** Cutting Edge, Practical
    - **Time:** 30 to 40 minutes
    - **Why read this:** A detailed field report on static analysis, architecture rules, mutation
      testing, inferential review, drift checks, and runtime feedback. It also shows where these
      signals create noise or false confidence.

---

## Questions to keep beside the reading

1. What is the oracle, and how independent is it from the implementation?
2. Can the implementation agent weaken or rewrite the success criteria?
3. Does the harness inspect the final system state or only the agent's report?
4. Which inputs, action sequences, failures, and operating conditions can the harness generate?
5. How do we know the harness would detect a plausible defect?
6. Which important behaviors are still subjective, unmodeled, or too expensive to check?
7. What system knowledge must remain shared by humans even when every check is green?

The aim is not to stop writing individual regression tests. Those remain useful evidence. The aim
is to place them inside a larger system that searches behavior, challenges its own oracles, and
directs human attention to the risks that cannot yet be automated.

---

*All 17 links were verified on August 27, 2026.*
