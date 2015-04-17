# Thinking Through Paths at the Drawing Board

## Introduction

When building software iteratively, planning features is something that must be done early and often. But doing so is a process that can be made more complicated by the all of the stakeholders involved having different viewpoints and goals.

What's more, it's easy to overlook key behaviors that a system should have, potentially leading to expensive or rushed backfilling later. It's easy to identify what should happen when everthing goes according to plan, but what should happen when a user supplies bad data? A hacker launches a malicious attack on our application? What about when chaos makes the whole system unstable?

There is a way to get everyone's voice heard, including the product owner, developer, designer, and QA engineer. By performing a use case analysis during story planning, teams can draw use the multitude of perspectives that each person's role gives them to plan a system that will cost less and perform better.

Use case analysis allows teams to uncover edge cases and make systematic changes before a single line of code is ever written. In this post, we'll take a look at one method of performing use case analysis during story planning to yield the best results.

## Use Case Analysis

Use case analysis a fancy term that comes out of a process called Object Oriented Analysis. It's used to figure out what's requiremed of an application, how it should behave, and the people, systems, and processes that will interact within it.

A scenario is a narrative of interactions between users of varying roles and a technical system. It helps determine a system's requirements and sets a foundation on which the system will be built. It's a functional description of a small slice of what the application will do, or at least what the stakeholders hope it will do.

When doing a use case analysis, a team identifies different scenarios that they expect to play out within an application, and plans the desired results of those scenarios.

If you're a developer or QA technician, perhaps you've thought of scenarios as the Happy Path and the Sad Path when writing your tests. These are just two of the many paths that can be identified. Aside from the Happy (or Golden) Path, there are many alternative paths, such as the exception path and the evil path. When performing a use case analysis, we'll think through these paths up front, before we start writing code.

### Why Think About Use Cases?

There are many benefits from approaching feature planning from a use case perspective.

Talking through and modeling a feature in simple language makes it easy for all the stakeholders to be involved in the conversation. It makes the contract that they come up with together more resistant to change, as unforeseen circumstances are less likely to be revealed later. Functional parts of the system are less likely to fall through the cracks when a variety of scenarios are considered up front. It can prevent saving malformed data into data stores and prevent bugs.

Thinking through negative use cases can also help a team determine which features to include or exclude and guide architectural. For example, concern about having bots or script-generated users in the system could push a team toward including an email confirmation workflow, or choosing oAuth over traditional authentication.

In my mind, perhaps the most valuable benefit of use case analysis is that it gives teams a way to describe a system that costs very little to modify. By talking through alternate paths early, it's easy to change the requirements and described behavior of an application before it's even built. As time goes on, code is written, and a system begins to take shape, complexity increases significantly.

Changing features in a use case story or diagram is easy. Changing them in UX flow diagrams and wireframes is harder, in design comps harder still, in development code even harder, and in a production app it's extremely hard. During planning, making changes is easy, so it pays to think through as many of the potential scenarios as possible at this nascent stage.

### Goals of Use Case Planning

When deciding to adopt a use case planning based approach, it can be good to keep the goals of the approach in mind. Here are some of the things that use case planning can help accomplish:

- Design the system from the user's perspective
- Communicate the system's behavior in the user's terms
- Specify all of the externally visible behaviors

When complete, the plan should clearly communicate the following to all members of the team:

- System requirements
- How the system is to be used
- The roles the user plays in the system
- What the system does in response to user stimulus
- What data or feedback the user receives from the system
- The value the customer will receive from the system



## The Process

Now that we've talked about the benefits and goals of use case planning, _what is it_? There are many opinions on the subject, and just as many ways to carry it out. What follows is a summary of [an approach outlined by Mark Shacklette](http://people.cs.uchicago.edu/~mark/51023/Ucstyleg.html) at the University of Chicago Computer Science department.

### Steps

1. Identify actors and their roles
2. Describe the system's purpose
3. Identify use case packages
4. Name and diagram use cases

In the first step, _identify actors and their roles_, the team should identify all of the "actors" in the system. Actors include everything that interacts with the system - people, external services, APIs, and hardware. The team should talk through each actor's relationship to the system and identify his/her/its responsibilities within the context of the feature.

Next, we'll want to _describe the system's purpose_ at the highest level. Why do we want this feature to be built?

In the third step, we _identify use and misuse case packages_. We go beyond just defining a single use case for a given feature, trying to uncover all of the different scenarios (good, bad, and ugly) that might occur when the actor interacts with the feature. We can group these scenarios together into a use case package. We also consider any information flows from the actor to the system or vice versa.

Finally, in the last step, we _name and diagram use cases_. For each use (or misuse) case, we give it a name (noun), theme (verb), and description. The description should encapsulate all of the required behavior. We can also check if it uses or extends another use case. If so, we can translate that into a `requires #X` callout in the ticket for that story.

After completing the four steps above, we will have several use cases packages. Each will be made up of several individual use cases that have a name, an actor, and a breakdown of all the required behavior. Using this information, we can diagram their interactions in a use case diagram. Here's an example of what that looks like:

<img src="" alt=""/>

Using a tracking tool like [Sprint.ly](https://sprint.ly), we can use the results of this process to prioritize tasks and estimate the time it will take to build the feature. Using the tracker, we can translate each use case into a story, and label all the use cases in the package with a common tag.


## An Example

Let's take a look at an example feature, and how a development team might work together to plan it.

We'll imagine that we work for a company that is building a multi-tenant SaaS platform where people can set up shops and sell services. Tenants will be able to charge their customers through the platform.

We're part of a team that has been charged with creating the payment acceptance features. We're entering a sprint planning meeting to define the scope of the work to be done and decide how long it will take to build.

During this meeting, we'll talk about many different aspects of the billing process. For the purposes of this post, let's hone in on one specific feature: once a user has clicked "buy", they are presented with a credit card form. We want to plan what will happen when they try to make use of this form. Let's walk through the process and see what it yields.

### Step 1. Identifying the Actors and Their Roles

User
Merchant
Platform Owner

Card
Stripe
Credit Card Company
SaaS Platform

### Step 2. Describe the System's Purpose

To take money from the user and give it to the merchant and platform owner.

### Step 3. Identify Use Case Packages

We'll think through four possible scenarios for this feature: the sad path, the happy path, the evil path, and the weird path.


### The Sad Path

When considering a set of use cases, I often like to think through the sad path first. As I mentioned in the "Why Think About Use Cases?" section above, it can yield interesting decisions that affect the way the happy path will be built.


- User fills out the credit card form with invalid credit card information
- Card is rejected by credit card company => feedback

#### Product Owner
#### Designer
#### QA Technician
#### Developer


### The Happy Path

AKA The Golden Path
Business value

- User successfully fills out form
- CC is checked by Stripe
- Token is sent to backend
- Backend creates one time charge and saves it

#### Product Owner
#### Designer
#### QA Technician
#### Developer


### The Evil Path

AKA Bad path
Security

- Hacker steals CC info from database (not qualified by PCI compliance), logs, or insecure network request (HTTP)
- Price as a hidden form field -- user can change it
- Malicious hacker launches CSRF attack, leveraging logged in user's CC info to order products without authorization
- Security holes in session or authentication opens users up to charges if CC info is saved

#### Product Owner
#### Designer
#### QA Technician
#### Developer


### The Weird Path

Exception path
Reliability

- JavaScript blows up, form is submitted to server instead of being prevented & shows up in logs
- Stripe is down
- Connection to Stripe is interrupted during transaction
- Card is accepted in form, but fails when charge is created on backend

#### Product Owner
#### Designer
#### QA Technician
#### Developer


### Step 4. Name and Diagram Use Cases




## Sources

http://en.wikipedia.org/wiki/Use_case

http://people.cs.uchicago.edu/~mark/51023/Ucstyleg.html

http://www.nishantverma.com/2010/03/test-case-paths-happy-sad-bad.html

http://adam.goucher.ca/?p=399
