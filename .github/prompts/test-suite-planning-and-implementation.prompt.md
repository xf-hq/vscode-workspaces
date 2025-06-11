---
mode: 'agent'
description: 'Ask the agent to execute a test-driven development workflow'
---

Your task is to follow a systematic test-driven development approach as per the current editor and/or what the user has indicated and/or attached alongside this request. Before proceeding, please first review [this document](../docs/testing.md) for important guidelines and instructions. Once you've reviewed the document, please step through the following list of instructions with care and precision.

First review the [test-driven development guidelines](../docs/testing.md).

1. Fully familiarise yourself with the functionality that the test suite is designed to test. Follow these steps:
   - (1.1) Make sure you're clear which functionality the test suite is targeting. If this is uncertain or ambiguous and you're unable to figure this out for yourself with a high degree of confidence, ask the user what you want to know, and proceed no further until you have the answers you seek.
   - (1.2) Once you know exactly what functionality the test suite is targeting, carefully read through the functionality's implementation:
     - (1.2.1) Try to reason about the implemented functionality and behaviour so that you really get a sense of how it works, how it's used, and how it compares to other code, solutions and implementation patterns you've been exposed to in the past.
     - (1.2.2) After your initial assessment of the code, if you feel like you need more information in order to feel confident in its usage and expected behaviour:
       - (1.2.2.1) Search the codebase to see if there are examples of other code which makes use of this functionality. Note that _some_ functionality in the codebase is not currently in use, so if you can't find any usages or examples, just move on to the next step.
       - (1.2.2.2) If your lack of clarity is due at least in part to uncertainty regarding the role, purpose and/or behaviour of one or more of the types or objects imported by the code you're reviewing, please follow this same review process for each of those types so as to expand your mental model to include an understanding of each of the imports in question and how and why they are being used by the functionality that the test suite is targeting. If, after doing all of this, you still lack clarity regarding aspects of the code the test suite is supposed to cover, again - ask the user for the information you require _before_ proceeding any further.

2. To verify that you've comprehensively completed step 1, as a prerequisite to the main task you need to make sure that the JSDoc documentation for the functionality in question is comprehensive and detailed:
   - (2.1) JSDoc comments:
     - JSDoc comments are required for exported types, objects, functions and classes, and for public methods and properties. We want to document usage and behaviour that would matter to a consumer.
     - For each JSDoc comment block, consider whether what is being documented has any non-obvious usage patterns, behaviours or other nuances that might matter to whoever is utilising the thing being documented. If so, list them clearly, detail the relevant points concisely, and make sure you're providing sufficient _relevant_ detail without being overly verbose.
   - (2.2) In-code comments:
     - Code whose logic and behaviour is not obvious at a glance should include internal comments (`//`) indicating details relating to the broader role played by that which you're commenting about:
       - DON'T just restate what the syntax itself tells us.
       - DO summarise more complex blocks of code to reduce cognitive load for developers trying to scan through the code to understand what it's doing.
       - When you identify implementation patterns that confusingly deviate from what you think one would normally expect to see in that sort of context, comment on the implementation choices made and why they fit in this particular context.
       - Assume your target is audience consists of seasoned, highly experienced senior developers. They are your peers and so your choice of comments should reflect what you'd expect to be useful given their average level of skill and experience.

3. Make sure there is a comprehensive test plan documented at the very top of the test file:
   - (3.1) IF and ONLY IF a test plan is already present:
     - (3.1.1) Review the existing plan, cross-referencing its structure, content and comprehensiveness with points 3.2 and especially 3.3 (below).
     - (3.1.2) If revisions are required as per steps 3.2 and/or 3.3, please make that your focus before proceeding further.
   - (3.2) Location and formatting:
     - (3.2.a) The plan is positioned BEFORE any `import` statements.
     - (3.2.b) The plan is formatted using a JSDoc comment block. The purpose of using a JSDoc comment (`/**`) over a regular block comment (/`/*`) in this context is that VSCode will syntax highlight any markdown syntax you use in the block and will automatically mark up properly-annotated links to make them clickable, making it easier for developers to read what you've written.
   - (3.3) Content of the plan:
     - (3.3.1) Think deeply about the functionality you're going to be writing tests for:
       - (3.3.1.a) There is a slight possibility that after you write the plan, implementation of the test suite itself may end up being delayed or reassigned to another developer. Therefore you want to make sure the plan you've written is comprehensive, detailed and accurate so that the next developer can follow it efficiently and systematically in order to implement the required tests. Try to imagine that after writing up the plan you're interrupted with other work, and when you return, you've completely forgotten everything up until this point. In such a scenario, what would the plan need to include in order for you to still successfully satisfy your mandate to deliver a high quality test suite?
       - (3.3.1.b) PUBLIC functionality is the primary focus of the test suite. Regardless of the broader scope of tests that are ultimately implemented overall for this test suite, at a MINIMUM the _public_ feature set must be comprehensively covered by tests.
       - (3.3.1.c) DON'T test PRIVATE functionality.
       - (3.3.1.d) DON'T EVER make private functionality public purely for the sake of testing.
       - (3.3.1.e) DO be discerning regarding any decision to test INTERNAL functionality (functionality which is _technically_ public, but intended only for use within by the internal implementation). Tests targeting internal functionality are of LOW priority and should ONLY be considered if they genuinely assist with breaking down complex subsystems into smaller pieces whose behaviours are difficult to verify without testing internal subsets of functionality in isolation from each other.
     - (3.3.2) Write out the plan:
       - (3.3.2.a) Use a hierarchical bullet point list aiming for the most optimal mix of brevity/conciseness and detail/clarity.
       - (3.3.2.b) Top-level bullets should represent each major group of concern(s) to be tested.
       - (3.3.2.c) Second-level bullets should be used when it makes sense to break down top-level items into smaller subgroups.

4. Bring the test suite implementation up to par with the test plan:
   - (4.1) If any tests are already implemented:
     - (4.1.1) Fully familiarise yourself with their content, logic, structure and coverage, contrasting the existing test suite implementation with what is outlined by the test plan as it now stands following the work you did in step 3.
     - (4.1.2) If there are any inconsistencies with the plan, add temporary comments regarding any additions, changes or deletions that need to be made. For additions, make sure the comments appear at the correct locations in the code where the test implementations will actually appear.
   - (4.2) If you're going to be writing new tests, revising existing tests or making structural changes to the test suite in general:
     - (4.2.1) First implement a comprehensive set of test _stubs_ as per the test plan. Your goal here is first to focus on making sure the structure and coverage of the test suite is an accurate reflection of the test plan. You can worry about making the tests pass later - right now your focus is on ensuring that the test suite is consistent with the test plan. Each stub you implement should act as a placeholder, resulting in a deliberate test failure when run. Example: `expect(true).toBe(false); // This test is not yet implemented`
     - (4.2.2) Think over the work you've done up to this point and double check that the arrangement, composition and coverage of the stubs you've implemented matches the test plan. If there are any compliation problems, fix them. Do a dry run of the test suite, anticipating failures for any test stubs you introduced.

5. IMPLEMENTATION! Only proceed to this step after you've completed and double checked your work on steps 1 through 4.
   - (5.1) Run the test suite and determine which tests are failing.
   - (5.2) If any tests are failing:
     - (5.2.1) Pick the simplest, most self-contained failure that you can address with the least amount of effort.
     - (5.2.2) If the test itself is only a stub or its implementation is incomplete or incorrect:
       - (5.2.2.1) Fix the implementation of the test so that when it runs, it will properly test whatever functionality it is intended to cover.
       - (5.2.2.2) Rerun the test, making sure it compiles and executes. If it doesn't, return to step 5.2.2.1.
     - (5.2.3) If the test implementation is complete and correct, and is currently failing:
       - (5.2.3.1) Find and fix the problem in the code which implements the functionality that the test is covering.
       - (5.2.3.2) Rerun the test, making sure it compiles and executes. If it doesn't, return to step 5.2.3.1.
   - (5.3) Repeat step 5.3 until all tests are passing.

6. FINAL REVIEW: Do a quick walkthrough of steps 1 through 5 of these instructions and double check that everything you've done is correct, complete, and fully consistent with everything that the instructions outline.
   - (6.1) Syntax/type error checking:
     - (6.1.1) Are any problems currently being reported by the language service/type checker? You _should_ have a dedicated tool for retrieving problems - don't try to use the terminal for this purpose.
     - (6.1.2) If any problems are being reported, fix them and return to step 6.1.1. If there are no problems currently being reported, proceed to 6.2.
   - (6.2) Rerun the test suite a final time and carefully check each line of the output. If any failures are reported, return to step 5.2 and move forward from there.
