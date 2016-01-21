##Setting the Scene

**背景铺垫**

> My team and I were excited by the prospect of adding fingerprint login to the iOS version of our banking App. It was something our users had been asking for. As a team, we wanted to try out the new technology and create a more user friendly and quick way for customers to login and access their bank information. 

我们团队曾经很兴奋地想在我们iOS版的银行应用中实现指纹登录，这不仅是我们用户的需求，同时也是我们的目标：作为一个专业团队，我们既想尝试新的技术，也想为我们的用户登录并获取他们的银行信息提供更快更方便的方式。

> As a team (comprising of Developers, BAs, QAs and myself for XD), we’d considered different user scenarios and security issues for fingerprint login. We’d researched how other Apps had implemented this  functionality; but the more we delved into how Apple required us to implement their API, the more I questioned the usability for the end user.

作为一个功能完备（这里有开发工程师，有业务分析师，有质量控制工程师，还有我这个体验设计师）的团队，我们就指纹登录功能设想了多种用户场景和安全隐患。当然，在这一过程中，我们认真研究了其他应用是如何实现这一功能的。然而，随着我们对苹果要求实现他们API的方式了解得越多，我就越质疑指纹登录对最终用户到底有多少价值。

> The functionality was fairly new in the marketplace. Although users were familiar with unlocking their phones using TouchID, I was skeptical if they would find the process of logging in to the banking App with their fingerprint (over a pin) intuitive.

在当时的市场上，指纹登录是很新颖的功能。所以即使用户能很熟练的使用TouchID解锁他们的手机，我还是很怀疑他们是否能凭直觉正确使用指纹（而不是密码）来登录我们的银行应用。

> Our App was receiving great reviews and had won awards for the user experience it provided. Although we had time constraints, I didn’t want a third party API to undo all the hard work that had gone into creating a great experience.

我们的应用有着很高的评价，还凭借优秀的用户体验获过奖。因此就算我们有时间上的限制，我还是不想冒着毁掉用户体验的风险而采用第三方API。

> [When comparing Android and Apple, we had found that Android had solved the concerns we had with Apple, but the actual scanning mechanism on the button was better on Apple. Ideally, for the optimum experience you need to combine both solutions.]​

[当拿安卓的指纹登录方案和苹果作比较时，我们发现安卓上应用的方案解决了我们对苹果所抱有的顾虑，但是在指纹扫描机制方面还是苹果占优。所以为了最佳的体验，你最好还是考虑结合这两种解决方案。]

##Your Time Starts Now!

**下面进入正题！**

> We release an updated version of the bank App every 6-8 weeks. With each release we not only add new features - fingerprint login or eStatements, for example; we also needed to ensure that it created the best experience for the user, met stakeholder requirements and was within the budget. Therefore, user testing couldn’t be a drawn out process which lasted several days. I had one day to user-test the fingerprint login.

我们每6到8周就会对我们的应用进行一次版本更新。每次更新不仅仅是增加新功能，比如指纹登录和eStatements功能，我们还需要保证最佳的用户体验，满足业务的需求，同时还得控制成本。所以，我们没法抽出几天来单独做用户测试，我只有一天来模拟用户测试指纹登录功能。

##Who to Test?

**谁是测试对象？**

> Although the main target audience of the App is the baby boomer generation, the app does have a lot of users in the lower age brackets.Taking this into consideration, we conducted our user testing across different age groups, some of whom were already TouchID users. Having a good mix allowed us to understand if problems were consistent across the board, which would, in turn, allow us to create a better experience for all users. 

虽然我们应用的主要用户是婴儿潮一代，但也有众多年轻的拥趸。考虑到这一点，我们的用户测试横跨多个年龄段，包括一些早已熟悉TouchID的用户。这样做的好处是，能让我们了解问题是不是出现在所有条件之下，换而言之，这有助于我们为所有用户创造更好的体验。

##Morning session

**早会时分**

> Luckily, for the user testing, we already had a production build of the App. This allowed the user to experience the entire process of registering and scanning a fingerprint. We started by asking them to register their fingerprint on the iPhone (the App would allow users to set-up a fingerprint login only if their fingerprint was already registered on the device). Taking them through this process allowed us to create an ‘as real to life’ experience as possible, under the conditions. They were then asked to login to the App and complete the flow to setup the fingerprint login for mobile banking. Users passed this process without any concerns.

幸运地是，我们已经有一个产品版本的应用，它能允许用户体验整个注册流程和扫描指纹。我们从请求用户使用他们指纹注册开始（用户只有事先在设备上绑定了他们的指纹，才能使用我们的应用所提供的指纹登录功能），带领用户在尽可能贴近真实生活的条件下体验整个注册流程。之后我们请求用户登录应用，独立完成指纹登录设置的各个步骤。随后用户毫不费力地就完成了这一操作。

![Apple TouchID example](https://www.thoughtworks.com/sites/default/files/styles/retina_ready/public/assets/AppleTouchID.jpg?itok=pxsN2PYP)

> It was in the next process that we asked users to complete which validated our concerns. After registering their fingerprint on the device and completing the flow inside the App, they had to login with their fingerprint. We asked them to log out of the App and log back in using their fingerprint. Once they tapped the ‘login with fingerprint’ button, the standard Apple TouchID modal appeared. At this point, the user is meant to scan their fingerprint. Instead, we saw the user instantly getting confused and quickly frustrated as they tried various ways to get it to work.

我们请求用户完成的下一个步骤才坐实了我们的担忧。那就是在用户绑定指纹，完成注册之后，他们还需使用指纹重新登录应用。此时我们请求用户退出账户，再使用指纹重新登录。每当用户按下了'指纹登录'的按钮，标准的苹果TouchID模态框就出现了。按理说此时用户应该扫描他们的指纹，但是我们却发现用户直接懵了，在使用多种方式尝试登录之后就泄气了。

![Problems with Apple TouchID](https://www.thoughtworks.com/sites/default/files/styles/retina_ready/public/assets/TouchIDforTWBankExample_0.jpg?itok=u9FtTnJj)
 

##What was so confusing?

**问题出在哪儿？**

> I had concerns that the restrictions and style of the modal would confuse users. On observing users and chatting with them, these concerns were proven.  

我一直都很担忧苹果对指纹验证的限制，以及TouchID模态框的风格会误导用户，在观察用户并和他们交流之后，这种担忧得到了证实。

> Once the modal was activated, people saw what they thought was an error message, even though they had been shown a similar modal when unlocking their phone. In this new context, they panicked and pressed ‘cancel’, which meant that they had to start the login process again.

一旦TouchID模态框被激活，人们会认为那是一个错误提示，即使他们在解锁手机时曾见过相似的方框。在这种情况下，他们会抓狂，然后按下‘取消’，也就说他们不得不重新开始登录的流程。

> From regular user testing that we do for every release of the mobile App, we’ve noticed that people, particularly a younger audience, simply don’t read lengthy text. They dismiss information in the screen or inside a modal, and simply click the ‘ok’ button during onboarding flows, rather than reading what they are doing. After several failed attempts of trying to login with their fingerprint, they finally read what little copy was available inside the modal, but this just confused them more. Most gave up and logged in with their pin.

从我们每次发布都会做的定期用户测试里，我们发现人们，尤其是年轻人，根本不会阅读很长的文字。他们直接关掉屏幕或者应用里的提示信息，甚至在注册时看都没看他们在做什么就直接点‘确认’按钮。在多次错误的尝试使用指纹登录之后，他们终于注意到了模态框内那小小的文字。然而这只不过让他们更疑惑而已，大多数人直接放弃，改用密码登录了。

![Issues with Apple TouchID](https://www.thoughtworks.com/sites/default/files/styles/retina_ready/public/assets/Issues_with_Apple_TouchID_0.jpg?itok=mM4MvwVR)

> 
* Everything about the modal design looks like an error message
* The icon is red. Red represents danger or error in a majority of countries
* The hierarchy of the text is incorrect. The name of the App and the ‘cancel’ button are more prominent than the call to action or localised reason as Apple refers to it .
* Restrictions imposed by Apple:
* Apple only allows third parties to edit the localised reason, resulting in the App name being more prominent.
* The number of characters for the localised reason is also restricted, resulting in the instructions being very vague and unhelpful for the user.
* The modal activates a dark screen, which prevents us from adding any useful information or directional arrows that say ‘scan here’.

* TouchID模态框的各种设计都让它看起来像一个错误提示信息
* 指纹图标是红色的，而红色在大多数国家里都代表危险或者错误
* 文字的层级结构不对。在苹果的要求下，应用名和‘取消’按键显得远比操作说明更为显眼。
* 以下是苹果的霸王限制：
    * 苹果只允许第三方修改操作说明，这使得应用名看起来更显眼
    * 操作说明的字数也被限制了，导致说明文字含糊不清，对用户毫无裨益。
    * 模态框的出现伴随着暗色的背景，让我们没法添加有用信息或是画一个写着“扫描这里呀”的箭头指向Home键。

> Throughout the testing process, only one user instantly understood what was required. On further questioning, she revealed that she had used the same functionality with another bank. She said that she was initially frustrated, but eventually understood how it worked.

在整场测试当中，只有一个用户流畅地完成了登录操作。在后来的调查问卷里，她回应说那是因为她曾在另一个银行的应用里使用过相同的功能。同时她也表示在其实最开始她也搞不懂，不过好在最后还是明白怎么操作了。

## Lunchtime:

**午餐时间**

> After we’d finished the morning session of testing, we decided we had enough information to confirm our concerns, even though we’d only tested a small number of users. To fully maximise the time we had, we felt it was best to spend the testing session in the afternoon trying alternative solutions.

在完成上午的测试之后，即使样本容量不大，但我们已然获得足够证据来证实我们的担忧。为了最高效地利用时间，我们决定在下午的测试里试验另一种方案。

> Adding a simple set of text instructions above the login button would be very quick to implement; however, we knew that our users seldom read text that was more than a few words long. This made it a less than ideal solution. Adding a simple arrow below the modal to say ‘scan your finger here’ would have been more intuitive, but this was prevented by the Apple API.

在登录按钮之上添加一段简单的文字说明是能非常快就实现的方案，然而我们也清楚用户几乎不会去阅读稍微长点的文字；在模态框之下放置一个简单的箭头并配上文字“于此扫描你的指纹”会更符合直觉，但是苹果的API让我们没法这么做。

> I sketched two paper prototypes to test:

我画了两个原型草图来进行测试：

![Paper prototypes to fix the Apple TouchID problem](https://www.thoughtworks.com/sites/default/files/styles/retina_ready/public/assets/finalpaperprototype.gif?itok=0zzaYOle)

> **Prototype A:** We gave the user a set of written instructions. This would be the quickest to build, but knowing our users, we weren’t convinced this was the best solution.

原型A：给用户一些文字提示。这是能快速实现的方案，然而考虑到用户习惯，我们不敢说这就是最佳方案。

> **Prototype B:** We used a visual graphic with very short text.

原型B：用配图加很短的文字。

## Afternoon Testing Session:

**午后测试**

We went through the same process of registering the fingerprint and setting it up within the App on the device. We then moved to the paper prototype flow to test our new versions. We conducted these tests with a new set of users. We also managed to ask some of the users from the morning session which of the new prototypes they preferred.



## The Results:

Very quickly, we realised that our users preferred the visual graphic approach. By visually showing them the action rather than explaining it, we made it far easier to understand.

## Late in the Day and Running out of Time!

We had validated our concerns and tested two options. The more complex option was the preferred one, but did we have the time to implement it?

Although the team had been kept informed of our findings throughout the day, and the main stakeholder had been involved in the user testing, we still needed to decide as a team what we were going to do, given the time it would take to implement this on iPhones, iPads and then test it.

We gathered the entire team together to share what we had discovered and the two possible solutions. Although option A was far easier to implement we decided that option B would give the best experience.

We were up against the clock. I paired with our lead developer which meant he could start building the page, while I created the graphics. By the following morning, the new flow had been implemented across all iOS devices which had TouchID capabilities.

Final Solution for Apple TouchID app

## Final Solution

For any onboarding flows, we usually present the user with large ‘Ok’ or ‘Cancel’ buttons. However, for our new help screen, we wanted to change this usual behavior (of tapping a large ‘OK’ button to proceed), to ensure that they looked at the instructions. Replacing the large ‘Ok’ button with the familiar ‘close’ icon (X) in the top right, not only changed their usual behaviour by ensuring their eyes spent enough time looking at the graphical instructions before proceeding, but, also used an icon they were familiar with from using other applications.

## Mission Accomplished!

Soon after launching the fingerprint login, the App reviews from customers started rolling in. And they were good!

## What can we learn from this?

***Why should we care about testing the user experience on a third party API?***

Many Apps in the market use the ‘default’ from Apple and have just accepted what they’ve been given, rather than thinking about its usability and the frustration that it causes users. But, why? It’s the quicker option, but how does that help your brand give a better user experience? The user won’t know the constraints you’ve been given by Apple. They’re just tapping on your App icon, looking at your brand being displayed and not enjoying the experience. Ultimately, if they become frustrated, they will blame your brand.

We wanted our App to ensure a great user experience. Especially, when we could solve the problem.

***What could Apple have done better?***

When you’re dealing with third party APIs, there are inevitably constraints. It appears as though Apple’s focus was skewed more towards launching the new feature as quickly as possible, overshadowing the user’s experience. Whether or not they had the time or money, if they’d considered ‘real users’, they could have created something that was more intuitive. As Steve Jobs once said:

> “You’ve got to start with the customer experience and work back towards the technology – not the other way round.”

Hopefully, the next release will be more intuitive and have some of the refinements that Steve Jobs might have demanded.

***What can brands, developers and user experience designers learn from this?***

Mobile phone developers need to understand that people are not just using their phones to access Apps developed by them. They’re downloading third party Apps. If you truly want your mobile device to provide a great experience, you need to allow third party Apps to integrate seamlessly with your software. Apple is known for restricting users from integrating with their software. In today’s world, this is not something you want to be known for.

When you’re developing a new product, don’t just look inwards. Look at the wider picture to stay ahead of the game.

If you’re building an App that uses an third party API (like many Apps need to), consider the restrictions imposed by the API. Sketch out the path that a user will take when performing a task and think about how you can make the process as easy as possible. Then, look at restrictions imposed by the API or even a third party library that you’re using, and consider what impact this has to your user flow. Follow this up with user testing to find an ideal solution which best fits with the user experience and the effort it requires.

Remember your user will judge your brand on the experience that you give them.