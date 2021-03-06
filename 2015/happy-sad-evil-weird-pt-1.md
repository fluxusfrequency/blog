# Happy, Sad, Evil, Weird: Driving Feature Development With Feature Planning

When building software iteratively, feature planning has to  be done early and often. But it can be a complicated process due to all of the stakeholders involved, each with different viewpoints and goals.

What's more, it's easy to overlook key behaviors of a feature, which can lead to expensive and rushed code later. It's usually intuitive to figure what should happen when everything goes according to plan, but what about edge cases? What should happen when a user supplies bad data? A hacker launches a malicious attack on our application? What about when [chaos makes the whole system unstable](https://github.com/Netflix/SimianArmy)?

In the first post of this miniseries, we'll take a look at one way to get everyone's voice heard in the planning process, including the product owner, developer, designer, and QA engineer. Using this approach, teams can draw on their diverse perspectives to tease out a detailed blueprint of a feature that costs less and performs better.

## Introducing Use Case Planning

Use Case Planning is a term that I've come up with to represent a simplified version of [Use Case Analysis](http://en.wikipedia.org/wiki/Use-case_analysis). I'm aiming to simplify the software feature planning process into a simple, reusable procedure that will save teams money and build more robust systems.

With Use Case Planning, teams can stay flexible early in the game, when it's still cheap to make big changes to the system.

It also helps us look ahead and find edge cases. For many people, it's easy to press forward naïvely, writing stories about how a feature should behave entirely in terms of the best-case scenario (also known as the [Happy or Golden Path](http://en.wikipedia.org/wiki/Happy_path)). But there are many other cases to consider. What about bad data (Sad Path), hacker attacks (Evil Path), or web services going down (Weird Path)?

In the end, thinking through these scenarios in advance of development will save a company money and result in features that provide a better user experience.

## What Is Use Case Planning?

### Background

If you're not familiar with Use Case Analysis, it's an academic approach based in [Object Oriented Analysis](http://en.wikipedia.org/wiki/Object-oriented_analysis_and_design)—a way of describing any kind of system (not just software) in terms of conceptual objects.

I first became aware of Use Case Analysis through the work of [Mark Shacklette](http://people.cs.uchicago.edu/~mark/), a professor of Computer Science at the University of Chicago. In [this paper](http://people.cs.uchicago.edu/~mark/51023/Ucstyleg.html), he lays out a very detailed process for building systems with Use Case Analysis. However, I found it to be over-complicated for regular use. I build a lot of software for clients, and need a "boiled down" version that I can reach for when planning sessions are  constrained by time. Use Case Planning is my attempt at creating that.

### What's Required?

In the software industry, when we're getting ready to build a feature, we have to answer three basic questions:

What's required to build it?
How should it behave?
What people and computer systems are involved?

Through the Use Case Planning process, we'll answer these questions and come out with a  blueprint detailing what we need, the dependencies involved, and the ways our feature should work.

### Who's Involved?

Feature planning is usually carried out by a team of stakeholders rather than an individual. Product owners, developers, designers, QA technicians, and so on all bring a unique perspective and set of concerns to the table. Drawing on these varied mindsets helps us make a more comprehensive plan than we could come up with on our own

### Scenarios

Let's imagine we're on such a team, and we want to plan a feature with Use Case Planning. We'll need to break it down into as many use cases, or scenarios, as we can, weaving a story of interactions between people of various roles and the computer systems involved.

## Why Think About Use Cases?

There are many benefits to breaking down a feature into use cases.

First of all, talking through software's desired behavior in simple everyday language opens the conversation up to all stakeholders. The whole team can work together to determine how a feature should work without jargon getting in the way.

The team involved in the planning process is essentially trying to define a _contract_ of what will be built and what it will do. Use Case Planning makes this contract more resistant to change, as unforeseen circumstances are accounted for before the first line of code is ever written. Also, functional parts of the system are less likely to fall through the cracks when a variety of scenarios are considered up front.

This approach can also help guide architectural decisions. Sometimes, a feature can be ruled out entirely, before any time is ever spent building it. For example, concern about having bots or script-generated users in a system could push a team toward including an email confirmation workflow, or choosing [oAuth](http://oauth.net/) over traditional authentication.

In my mind, perhaps the most valuable benefit of use case analysis is that it gives teams a way to describe a system that costs very little to modify. In talking through alternate paths early, it's easy to change requirements before anything's ever built. This is a huge win, because as time goes on, code is written, and the system begins to take shape, the cost of change increases significantly.

Adding and removing features from a use case story or diagram is easy. Changing them in UX flow diagrams and wireframes is harder, in design comps harder still, in development code even harder, and in a production app it can be extremely hard. But when you're planning, making changes is as easy as throwing away a sticky note and writing a new one, so it pays to think through as many of the potential scenarios as possible at this early stage.

## How To Do Use Case Planning

Now that we've talked about the benefits and goals of use case planning, _what exactly is it_? As I mentioned above, this is my attempt at boiling Use Case Analysis down into a set of memorable, repeatable steps. They mirror the steps in Mark Shacklette's original paper pretty closely, but I've tried to rework them to the minimum of what I think _I_ would need when planning a feature before beginning work.

### The Four Steps

To complete the four steps, answer these four questions:

1. Who are the actors and what are their roles?
2. What's the purpose of this feature?
3. What are the use cases?
4. How do the use cases relate to each other?

In thinking about the first question, we'll expand our definition of "actors" from just people to include everything that interacts with the system: users, administrators, our client-side application, our API, external APIs, cloud services, and hardware interfaces. Each of these actors has a role and a responsibility in the system that we should identify.

In identifying the purpose of the feature, we are just looking for a high-level summary of why we care about it. What's the business value?

When we get to the third question, we're ready to dig into the meat of this process. We'll think through all of the use and misuse cases, considering the Happy, Sad, Evil and Weird paths. For each one, we'll choose a a noun (the actor), verb (actions taken), and a brief description of the desired result. We can also optionally add a more specific purpose (like we identified in step two) to each case as we go.

After step three, we will have identified many use cases, each with a name, an actor, and a breakdown of all the required behavior. Using this information, we can answer the last question (how do they relate?) by diagramming their interactions. Here's an example of a use case diagram for ordering at a restaurant [from Wikipedia](http://en.wikipedia.org/wiki/Use_Case_Diagram#/media/File:Use_case_restaurant_model.svg).

<img src="" alt=""/>

This diagram is in a formal Use Case Analysis style. For the purposes of planning software, it might make sense to replace this style of diagramming with a UX flow diagram, using the screens in the app to delineate how the feature should behave.

### Wrapping It Up

Once we've identified the various use cases associated with our feature, we can translate these results directly into agile stories. Using a tracking tool like [Sprint.ly](https://sprint.ly), we can then prioritize the work that needs to be done and estimate the time it will take to complete the feature. Each user case can be sized, tagged, and tracked, providing a helpful look into the progress being made toward bringing our feature to fruition.

## Conclusion

In this post, we introduced formal Use Case Analysis and a simplified version called Use Case Planning which fits a rapid, iterative development process. We went over the benefits, the steps involved, and explained how this planning method will help your team catch problems with your design before you start to implement it.

That’s it for this post, but tune in next week for part two (the final part) of this miniseries, where we’ll be stepping through a concrete example so you can see how to put Use Case Planning into practice.

P.S. How well you do you think Use Case Planning would fit into with your team’s development process? Have any thoughts or questions you’d like me to address in part two of this post? Drop us a comment.

