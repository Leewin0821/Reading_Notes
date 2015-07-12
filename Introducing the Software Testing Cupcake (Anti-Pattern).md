I believe that the Testing Pyramid is one of the best analogies to help a team develop a strategy for writing tests in a reliable and scalable manner. I have used it many times, and have found its application to be immensely helpful.

However, I often see organizations fall into some traps when attempting to implement a testing strategy. As Alister Scott pointed out, one of the common traps is the ice-cream cone anti-pattern. This happens when there is not enough low-level testing (unit, integration and component), too many tests that run through the Graphical User Interface (GUI) and an even larger number of manual tests.

This anti-pattern in on the decline though. Test automation is now prevalent in the software development community. Practices like Test Driven Development (TDD) and Behavior Driven Development (BDD) are widespread and applied almost unquestionably. It has been a while since I’ve seen teams that do not have considerable tests at the lower levels (unit, integration, component).

On the other hand, I have seen organizations fall into another very dangerous trap. Here are some common characteristics that lead to this anti-pattern:

* There are different teams who write different levels of tests.
* Writing tests usually spans 3 teams:
    * Developers write unit, integration and component tests.
    * Another team writes black box tests through the GUI.
    * Manual testers have a set of scenarios that they execute manually.
* Generally, these teams work in isolation and do not collaborate much.
* Work happens sequentially, as opposed to in-sync. First developers write their code and corresponding tests, then manual testers run their tests, then GUI testers will write their tests. What does this scenario look like? A mini-waterfall.
* The 3 teams do not agree on the scenarios that have to be tested or the level of test automation. This results in duplication - the same scenario ends up being automated at many different levels.


While discussing this with my colleagues Patrick and Tarso, we were comparing this new trap with the ice-cream cone and wondering what this new anti-pattern would look like. It is basically something that has a large base, large middle and also a huge top. Tarso said: "It's a cupcake!" and then we high-fived. 

Introducing the Cupcake Anti-Pattern for software testing:


Here are some tips to avoid the Cupcake, and possibly “tweak” it back to the Test Pyramid:

* Collaborate: Allow teams to collaborate and discuss where the best level is to write a particular test. Some techniques to get started:
    * Work In-Sync: Encourage the teams to work in sync, and not in a sequential mini-waterfall, while a particular feature is work in progress.
    * Cross-role Pairing: Support cross role pairing. For example, a developer and a tester pairing at the end of a story to figure out where to automate the scenarios.
    * Story Kickoff: Practices like The Three Amigos, or what some people call story kickoff, helps get everyone on the same page
* Test at the Lowest Level: Whenever possible, test a particular feature at the level closest to the code, using a shallow depth of test.
* Merge Teams when possible: Sometimes you don't need different teams, what you need is different people playing different roles. For example, a developer can be the GUI tester for a story that (s)he didn't develop.
* Agree on goals...: Make sure everyone has the same goals. For example, coming to a consensus on what “done” really entails for the entire team and together work towards getting it “done- not just “dev done” or “test done”.
* ...And metrics: As far as metrics go, avoid “horizontal metrics” that only apply to one level of testing, for instance the GUI testers measuring the number of scenarios that have been automated. Instead “vertically” splice your metrics, so all levels are included. Applying this to the example above, change the metric to this - every story needs at least 90% of automation, regardless of the level (UI, Unit, etc.) Thus the metrics are shared, making it a win-win situation.

When the team of developers, manual testers and GUI testers work together to achieve the same goal, collaborating and helping each other, I'm sure we can all achieve a much better testing strategy that will ensure the quality of our software.
 
Your thoughts on the Cupcake?