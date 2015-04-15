# Thinking Through Paths at the Drawing Board

## Introduction

When building software iteratively, there comes a point in the story writing process where a team must consider all the edge cases of a feature. What do we expect to happen when all goes as planned? What about when a user supplies bad data? Can we consider malicious attacks? What about when chaos makes the whole system unstable?

In this post, we'll consider the way that each of these cases might affect the job of different stakeholders in a product, including the product owner, QA technician, designer, and the developer. We'll also think about how the perspective that each of these roles provides can help teams build better software.


## Use-case Analysis
Scenarios / Paths

Narrative of foreseeable interactions of user roles and the technical system

Identify a system's requirements

Golden path, alternate paths, exception path

"Foundation on which a system will be built" -Wikipedia

Has a functional goal


### Why Think About Use Cases?

- Makes a contract between stakeholders more resistant to change
- Written in plain language so all stakeholders can discuss together
- Describe system in a way that costs little to modify
- Avoid functional parts of the system from "falling through the cracks"

Goals:

- design system from user's perspective
- communicate system behavior in user's terms
- specify all externally visible behaviors

- clearly communicate system requirements
- clearly communicate how the system is to be used
- clearly communicate the roles the user plays in the system
- clearly communicate what the system does in response to user stimulus
- clearly communicate what the user receives from the system
- clearly communicate the value the customer or user will receive from the system


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
