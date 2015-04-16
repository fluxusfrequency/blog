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

Talking through and modeling a feature in simple language makes it easy for all the stakeholders to be involved in the conversation. It makes the contract that they come up with together more resistant to change, as unforeseen circumstances are less likely to be revealed later. Functional parts of the system are less likely to fall through the cracks when a variety of scenarios are considered up front.

In my mind, perhaps the most valuable benefit of use case analysis is that it gives teams a way to describe a system that costs very little to modify. By talking through alternate paths early, it's easy to change the requirements and described behavior of an application before it's even built. As time goes on, code is written, and a system begins to take shape, complexity increases significantly. Changing features in a use case diagram is easy. Changing them in wireframes is harder, in design comps harder still, in development code even harder, and in a production app it's extremely hard. During planning, making changes is easy, so it pays to think through as many of the potential scenarios as possible at this nascent stage.

### Goals of Use Case Planning

Here are some of the goals that use case planning hopes to accomplish:

- Design the system from the user's perspective
- Communicate the system's behavior in the user's terms
- Specify all of the externally visible behaviors

And to clearly communicate the following to all members of the team:

- System requirements
- How the system is to be used
- The roles the user plays in the system
- What the system does in response to user stimulus
- What data or feedback the user receives from the system
- The value the customer will receive from the system


### Happy Paths / Use Cases

### Alternate Paths / Misuse Cases

Why plan for alternate paths?

- Avoid tech debt
- Avoid bad data
- Think through features in advance (e.g. spam -> no comments)
- Uncover associated bugs

## Levels of Complexity

As you go down, it gets more expensive to change

- Use case story
- Use case diagram
- UX flow diagram / storyboard
- Wireframe
- Comp
- Application with live code

## Process

1. Identify actors and their roles
Everything that interacts with the system - people, external services, APIs, hardware
What is their relationship to the system
What are their responsibilities

2. Describe the system's purpose
Highest level

3. Identify use and misuse case "packages" ~ features
Find tasks the actor performs when interacting with the system
Look for CRUD
Does the actor inform the system of anything external?
Does the system inform the actor of anything?

4. Name and diagram use cases
Give it a name and theme (verb and noun)
Give it a description
Does it use or extend another use case?

Deliverables:
- All the required behavior is contained in a use case
- Each use case has an actor
- Each use case has a name




## The Sad Path

### Product Owner

### Designer

### QA Technician

### Developer


## The Happy Path

AKA The Golden Path
Business value

### Product Owner

### Designer

### QA Technician

### Developer



## The Evil Path

AKA Bad path
Security

### Product Owner

### Designer

### QA Technician

### Developer


## The Weird Path

Exception path
Reliability

### Product Owner

### Designer

### QA Technician

### Developer




## Sources

Wikipedia

http://people.cs.uchicago.edu/~mark/51023/Ucstyleg.html

http://www.nishantverma.com/2010/03/test-case-paths-happy-sad-bad.html
