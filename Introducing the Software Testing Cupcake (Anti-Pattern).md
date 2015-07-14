I believe that the Testing Pyramid is one of the best analogies to help a team develop a strategy for writing tests in a reliable and scalable manner. I have used it many times, and have found its application to be immensely helpful.

众所周知，想写出可靠又具有良好扩展性的测试，测试金字塔模型是辅助团队决定测试策略，达成这一目标的最好的类比之一。根据我多次的使用经验来看，测试金字塔模型真的非常有用。

However, I often see organizations fall into some traps when attempting to implement a testing strategy. As Alister Scott pointed out, one of the common traps is the ice-cream cone anti-pattern. This happens when there is not enough low-level testing (unit, integration and component), too many tests that run through the Graphical User Interface (GUI) and an even larger number of manual tests.

同时，我也经常会看到有的团队在尝试实践测试策略时掉进各种陷阱里。正如Alister Scott指出的，一个常见的陷阱就是冰淇淋状的反模式锥体模型。这种模型表示在没有足够多的底层测试（单元测试，集成测试和组件测试）的情况下，创建了太多GUI（图像用户界面）测试，甚至更多的手动测试。

This anti-pattern in on the decline though. Test automation is now prevalent in the software development community. Practices like Test Driven Development (TDD) and Behavior Driven Development (BDD) are widespread and applied almost unquestionably. It has been a while since I’ve seen teams that do not have considerable tests at the lower levels (unit, integration, component).

得益于自动化测试在软件开发界的普及，这种反模式现象正在减少。并且，加上诸如TDD（测试驱动开发）和BDD（行为驱动开发）这些实践的大力推广和应用，我已经有很长一段时间没看到团队担心过底层测试（单元测试，集成测试，组件测试）了。

On the other hand, I have seen organizations fall into another very dangerous trap. Here are some common characteristics that lead to this anti-pattern:

然而，与此同时我还观察到一些团队跌进了另一个非常危险的陷阱。这个反模式的陷阱有如下非常明显的特征：

* There are different teams who write different levels of tests.
* 不同的团队写不同层次的测试。
* Writing tests usually spans 3 teams:
* 一般由如下三类团队来写不同的测试：
    * Developers write unit, integration and component tests.
    * 开发人员写单元测试，集成测试和组件测试。
    * Another team writes black box tests through the GUI.
    * 另外一个团队通过界面来做黑盒测试。
    * Manual testers have a set of scenarios that they execute manually.
    * 手动测试员进行一系列手动测试来测试功能。
* Generally, these teams work in isolation and do not collaborate much.
* 通常这些团队各自独立工作，合作甚少。
* Work happens sequentially, as opposed to in-sync. First developers write their code and corresponding tests, then manual testers run their tests, then GUI testers will write their tests. What does this scenario look like? A mini-waterfall.
* 整个项目的工作流程是依次进行，并没有同步工作。首先是开发人员写出代码和对应的测试，然后测试员手动地测试功能，之后另一部分测试员才写他们的测试去测试界面。这一流程看起来像什么？一个迷你瀑布。
* The 3 teams do not agree on the scenarios that have to be tested or the level of test automation. This results in duplication - the same scenario ends up being automated at many different levels.
* 这三个团队在测试用例应不应该被测试或是否自动化测试的意见上，达不成一致。这就导致了重复浪费——相同的测试用例在不同层次上都进行了自动化测试。


While discussing this with my colleagues Patrick and Tarso, we were comparing this new trap with the ice-cream cone and wondering what this new anti-pattern would look like. It is basically something that has a large base, large middle and also a huge top. Tarso said: "It's a cupcake!" and then we high-fived. 

在和我的同事Patrick和Tarso讨论此事时，我们对比了一下这个新的陷阱和之前提到的冰淇淋状的模型，然后就这个新的反模式陷阱应该长什么样陷入了思考。大致说来，它应该有个庞大的底部，宽阔的中部和一个巨型的顶部。Tarso灵光一闪，突然说道：“这不就是个Cupcake吗！” 他说得简直太对了。

Introducing the Cupcake Anti-Pattern for software testing:

下面介绍一下软件测试中的Cupcake反模式：

![fabio-cupcake-new](http://www.thoughtworks.com/sites/default/files/assets/fabio-cupcake-new1_0.png)

Here are some tips to avoid the Cupcake, and possibly “tweak” it back to the Test Pyramid:

这里有一些小贴士可以助你避开这个Cupcake，甚至很有希望将其“扭转”回理想的测试金字塔模型：

* Collaborate: Allow teams to collaborate and discuss where the best level is to write a particular test. Some techniques to get started:
* 合作：允许团队之间进行合作，然后讨论在哪一层写特定的测试才是最好的。以下是一些实用手段：
    * Work In-Sync: Encourage the teams to work in sync, and not in a sequential mini-waterfall, while a particular feature is work in progress.
    * 同步工作：当开发一个功能时，鼓励不同的团队之间同步工作，而不是线性工作，各自为政，像一个迷你瀑布一样。
    * Cross-role Pairing: Support cross role pairing. For example, a developer and a tester pairing at the end of a story to figure out where to automate the scenarios.
    * 跨角色结对：支持跨角色结对。比如，在一个Story快完结时，一个开发者可以和一个测试员结对决定在哪儿执行自动测试。
    * Story Kickoff: Practices like The Three Amigos, or what some people call story kickoff, helps get everyone on the same page
    * Story Kickoff：这里有很多方法可以采用，比如三驾马车（The Three Amigos），或者有些人称为的story kickoff，目的都是为了帮助团队分享对需求的理解，减少沟通隔阂。
* Test at the Lowest Level: Whenever possible, test a particular feature at the level closest to the code, using a shallow depth of test.
* 从最底层开始测试：在条件允许的情况下，从最靠近代码的地方开始测试一个功能，降低测试的深度。
* Merge Teams when possible: Sometimes you don't need different teams, what you need is different people playing different roles. For example, a developer can be the GUI tester for a story that (s)he didn't develop.
* 如果可能，合并团队：有时你并不需要不同的团队，你所需要的只是在不同职位上的不同的人。比如说一个开发者可以成为某个功能的界面测试员，即使他（她）并没有开发过这项功能。
* Agree on goals...: Make sure everyone has the same goals. For example, coming to a consensus on what “done” really entails for the entire team and together work towards getting it “done- not just “dev done” or “test done”.
* 在目标上达成一致：确保每个人都有相同的目标。比如，整个团队要对什么是“完成”达成一致意见，而不是一起工作“完成开发”或者是“完成测试”。
* ...And metrics: As far as metrics go, avoid “horizontal metrics” that only apply to one level of testing, for instance the GUI testers measuring the number of scenarios that have been automated. Instead “vertically” splice your metrics, so all levels are included. Applying this to the example above, change the metric to this - every story needs at least 90% of automation, regardless of the level (UI, Unit, etc.) Thus the metrics are shared, making it a win-win situation.
* 同时要达成一致的，还有衡量测试工作的方式。而一旦合适的衡量方式确立了，就要避免只能应用于某一特定层次的“横向衡量”，比如靠自动化测试的用例数目来衡量界面测试的质量。更合适的衡量方式应该是“纵向”的，这样就能把所有层次都囊括进来。就用上面的例子来讲，衡量方式应该改成这样——每个story不管在哪一层（UI测试，单元测试等）都至少需要有90%的自动化测试覆盖率。如此一来，衡量方法就被共享了，达到了双赢的效果。

![fabio-cupcake-new](http://www.thoughtworks.com/sites/default/files/assets/fabio-cupcake-new4.png)

When the team of developers, manual testers and GUI testers work together to achieve the same goal, collaborating and helping each other, I'm sure we can all achieve a much better testing strategy that will ensure the quality of our software.

当一个由开发者，手动测试员和界面测试员组成的团队为了达成同一目标，齐心协力，相互帮助的时候，我确信这个团队能够完成更好的测试策略，更好地确保软件质量。
 
Your thoughts on the Cupcake?

最后，你觉得这个Cupcake怎么样？