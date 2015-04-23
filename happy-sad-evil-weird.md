# Happy, Sad, Evil, Weird: Driving Feature Development With Feature Planning

## Introduction

When building software iteratively, planning features is something that must be done early and often. But doing so is a process that can be complicated, due to all of the stakeholders involved having different viewpoints and goals.

What's more, it's easy to overlook key behaviors of a feature, which can lead to expensive and rushed code later. It's usually intuitive to figure what should happen when everthing goes according to plan, but about edge cases? What should happen when a user supplies bad data? A hacker launches a malicious attack on our application? What about when chaos makes the whole system unstable?

In this post, we'll take a look at one way to get everyone's voice heard, including the product owner, developer, designer, and QA engineer: Use Case Planning. Using this process, teams can draw on their diverse perspectives to tease out a detailed blueprint of a feature that costs less and performs better.

## Introducing Use Case Planning

Use Case Planning is a term that I've come up with to represent a simplified version of [Use Case Analysis](http://en.wikipedia.org/wiki/Use-case_analysis). I'm aiming to simplify the software feature planning process into a simple, reusable approach that will save teams money and build more robust systems.

Using this process, teams can stay flexible early in the game, when it's cheaper to make big changes to the system.

It also helps us look ahead and edge cases. For many people, it's easy to go blindly forward, writing stories about how a feature should behave entirely in terms of the best-case scenario ([Happy or Golden Path](http://en.wikipedia.org/wiki/Happy_path)). But there are many other cases to consider. What about bad data (Sad Path), hacker attacks (Evil Path), or web services going down (Weird Path)?

In the end, thinking through these scenarios in advance of development will save a company money, and result in features that provide a better user experience.

## What Is Use Case Planning?

### Background

"Use Case Planning" is just another way of saying Use Case Analysis, made easy. If you're not familiar with Use Case Analysis, it's an academic approach based in [Object Oriented Analysis](http://en.wikipedia.org/wiki/Object-oriented_analysis_and_design), a way of describing any kind of system (not just software) in terms of objects.

I first became aware of Use Case Analysis through the work of [Mark Shacklette](http://people.cs.uchicago.edu/~mark/), a professor of Computer Science the University of Chicago. In [this paper](http://people.cs.uchicago.edu/~mark/51023/Ucstyleg.html), he lays out a very detailed process for building systems with Use Case Analysis. However, I found it to be a lot of steps for my everyday needs. I build a lot of software for clients quickly, and need a "boiled down" version that I can reach for when planning is constrained by time. Use Case Planning is my attempt at creating just that.

### What's Required?

As people in the software industry, when we're getting ready to build a feature, we have to answer three basic questions. What's required to build it? How should it behave? What people and computer systems are involved? Through this process, we'll answer these questions and come out with a plan for making sure what we build has those behaviors.

### Who's Involved?

Feature planning is often carried out by a team of stakeholders. Product owners, developers, designers, QA technicians and others all bring a unique perspective and set of concerns to the table. Drawing on these varied mindsets will help us make a comprehensive plan.

### Scenarios

Let's imagine we're on such a team, and we want to plan a feature using Use Case Planning. We'll need to break it down into as many use cases, or scenarios, as we can. We'll weave a story of interactions between people of various roles and the computer systems involved.

## Why Think About Use Cases?

There are many benefits to breaking down a feature into use cases.

First of all, talking through software's desired behavior in simple everyday language opens the conversation up to all of the stakeholders. can work together to determine how a feature should work.

The team involved in this process is essentially trying to define a _contract_ of what will be built and whit it will do. Use case planning makes this contract resistant to change, as unforeseen circumstances are accounted for before the first line of code is ever written. Functional parts of the system are less likely to fall through the cracks when a variety of scenarios are considered up front.

The Use Case Planning approach can also help guide architectural decisions. Sometimes, a feature can be ruled out entirely, before any time is spent building it. For example, concern about having bots or script-generated users in a system could push a team toward including an email confirmation workflow, or choosing oAuth over traditional authentication.

In my mind, perhaps the most valuable benefit of use case analysis is that it gives teams a way to describe a system that costs very little to modify. In talking through alternate paths early, it's easy to change requirements before anything's ever built. This is a huge win, because as time goes on, code is written, and the system begins to take shape, the cost of change increases significantly.

Adding and removing features from a use case story or diagram is easy. Changing them in UX flow diagrams and wireframes is harder, in design comps harder still, in development code even harder, and in a production app it can be extremely hard. But when you're planning, making changes is as easy as throwing away a post-it note and writing a new one, so it pays to think through as many of the potential scenarios as possible at this nascent stage.

## How To Do Use Case Planning

Now that we've talked about the benefits and goals of use case planning, _what is it_? As I mentioned above, this is my attempt at boiling Use Case Analysis down into a set of memorable, repeatable steps. They mirror the steps in Mark Shacklette's Use Case Analysis paper pretty closely, but I've tried to rework them to the minimum of what I think _I_ would need when planning a feature before beginning work.

### The Four Steps

The four steps consist of answering four questions:

1. Who are the actors and what are their roles?
2. What's the purpose of this feature?
3. What are the use cases?
4. How do the use cases relate to each other?

In thinking about the first question, we'll expand our definition of "actors" from just people to include everything that interacts with the system: users, administrators, our client-side application, our API, external APIs, cloud services, and hardware interfaces. Each of these actors has a role and a responsibility in the system that we should identify.

In identifying the purpose of the feature, we are just looking for a high-level summary of why we care. What's the business value?

When we get to the third question, we're ready to dig into the meat of this process. We'll think through all of the use and "misuse" cases, considering the Happy, Sad, Evil and Weird paths. For each one, we'll figure out a noun (the actor), verb (actions taken), and a description of the desired result. We can also add a more specific purpose (like we identified in step two) to each case as we go.

After step three, we will have identified many use cases, each with a name, an actor, and a breakdown of all the required behavior. Using this information, we can answer the last question by diagramming their interactions. Here's an example of a use case diagram for ordering at a restaurant [from Wikipedia](http://en.wikipedia.org/wiki/Use_Case_Diagram#/media/File:Use_case_restaurant_model.svg).

<img src="" alt=""/>

This diagram is in a formal Use Case Analysis style. For the purposes of software development, it might make sense to replace this style of diagramming with a UX flow diagram, using the screens in the app to delineate how the feature should behave.

### Wrapping It Up

Once we've identified the various use cases associated with our feature, we can translate these results directly into agile stories. Using a tracking tool like [Sprint.ly](https://sprint.ly), we can then prioritize the work that needs to be done and estimate the time it will take to complete the feature. The use cases can be sized, tagged, and tracked, providing a great look into the progress being made toward bringing our feature to fruition.


## An Example

Now that we've examined the Use Case Planning process in the abstract, let's take a look at how a development team might work together to plan a specific feature.

We'll imagine that we work for a company that is building a multi-tenant Software as a Service (SaaS) platform where people can set up shops and sell products. Tenants will be able to charge their customers through the platform.

We're part of a team that's getting ready to create a credit card payment acceptance feature. It will be a a credit card form common to all of our tenants. We'll be writing the markup by hand and using of [Stripe](https://stripe.com/) for processing cards. We're entering a sprint planning meeting to define the scope of the work to be done and decide how long it will take to build.

During this meeting, we'll talk about many different aspects of the billing process. For the purposes of this post, let's hone in on one specific feature: once a user has clicked "buy", they are presented with a credit card form. We want to plan what will happen when they try to make use of this form. Let's walk through the Use Case Planning process for this scenario.

### Step 1. Identifying the Actors and Their Roles

#### People

*User* - exchanges money for goods
*Merchant* - exchanges goods for money and money for tenancy
*Platform Owner* - exchanges tenancy for money

#### Services

*SaaS Platform* - provides the space for tenancy and goods to be exchanged for moeny
*Stripe* - verifies and charges credit cards, handles much of [PCI compliance](https://www.pcisecuritystandards.org/)
*Credit Card Company* - transfers funds between other actors

### Step 2. Describe the Feature's Purpose

To take money from the user and give it to the merchant and platform owner.

### Step 3. Identify Use Case Packages

In this step, each of the stakeholders will contribute their point of view to the discovery of behaviors that we should consider.

#### The Sad Path

When considering a set of use cases, I often like to think through the alternative paths first. As I mentioned above, they can yield interesting decisions that affect the way the happy path will be built. This time, we'll start with the sad path. Let's identify some of the "sad path" use cases of filling out a credit card form.

1. User fills out the credit card form with invalid credit card information

When this use case is identified, the designer might chime in that when this happens, the invalid fields should be highlighted and an error message should be displayed explaining what went wrong.

The QA technician might point out that these validations should be ironclad; no special characters should be allowed to pass through.

2. Card is rejected by credit card company

Here, the product owner might insist that it should take as few steps as possible to resubmit the form, so that the user doesn't become frustrated and decide not to buy the product.

3. Stripe accepts card when submitted via JavaScript, but fails on subsequent server charge request

The developer would want to make sure that passing error handling from the server back to the front end is captured in this use case.

#### The Evil Path

1. Price as set in a hidden form field and user figures out that they can change it to zero

In this scenario, the developer would want to make sure that the form is built correctly.

2. Hacker steals credit card info from the database, server logs, or an insecure network request sent over HTTP

The product owner would want to ensure that the SaaS company could not be help liable, and that PCI compliance was being met.

3. If we were to save the credit card information to the user account, a hacker could launch a [CSRF attack](http://en.wikipedia.org/wiki/Cross-site_request_forgery), leveraging a logged in user's account information to order products without authorization.

The developer would suggest using a CSRF token, and the QA technician would want to make sure that form submission failed when the token was changed.

4. Security holes in session or authentication opens users up to charges

Here, the QA technician might ask what would happen when cookie or local storage data is changed. Does it fail as it should?

#### The Weird Path

1. JavaScript is diabled or fails, form is accidentally submitted to SaaS server and credit card number shows up in logs

The developer would want to ensure that the form is built such that it would never be submitted to the SaaS server by mistake.

2. Stripe server is down

In this case, the designer would ask for some kind of error page, perhaps with a link to a status page where users could check for the servers to come back online.

3. Connection to Stripe is interrupted during transaction

The product owner might ask if we can resend the submission if the connection was interrupted. The developer would probably push back on that request, for security reasons. As a compromise, the designer might offer to invent an error state to be shown in this case.

#### The Happy Path

1. User successfully fills out form and clicks submit

Again, the designer would probably like to display some kind of success message here.

2. Credit card charge is accepted by Stripe

In this scenario, the product owner might ask to have the page views leading to the successful charge tracked in an analytics service, so we could track down and encourage the same behavior in the future.

### Step 4. Name and Diagram Use Cases

We've now identified twelve use cases for the credit card form feature. I tried my hand at documenting them all in a Use Case Diagram. Green arrows represent good requests or responses, and red arrows represent errors. Bombs represent a broken network connection.

<img src="" alt="" />

As you can see, there are tons of arrows. There are a lot of possible scenarios, and a lot of possible communications between the actors in the system. If we hadn't taken the time to think them through and uncover them all, there's high probability that we would have left some of these out.

### PROFIT

Now that we've gone through these four steps, we've come out with something very valuable: bite-sized sentences that can be translated directly into stories and entered into our tracker software.

Here's what the first sad path case might look like when worded as a story:

```
As a user, when I complete the form with invalid information and click submit, I should see the invalid inputs become highlighted, and I should see validation errors telling me what went wrong so that I can correct my error and successfully buy products.
```

As you can see, we have an actor (user), action (fill out and submit form), a result (show validation errors), and a business purpose (the user can give us money).

These twelve stories are small and clear, and lend themselves to being prioritized based on the priorities of our business. Once we enter them into our tracker, we can be assured that they will all be built, and we can estimate how long it will take to happen.

## Conclusion

The Use Case Planning process is not super complicated. It basically just consists of identifying who/what is involved, why we care, what should happen in the golden and alternative scenarios, and how do the scenarios relate?

It's a relatively low level of effort to answer these four questions and break down the scenarios, but the result is worth its weight in gold. It enables us to identify architectural concerns and edge cases early on and change them at a low cost. We've also ended up with a set of small stories with clear acceptance criteria that we can track, providing a huge value both in accountability and estimating timelines.

P. S. Do you have any other tricks to make planning features easier? Leave us a comment!


