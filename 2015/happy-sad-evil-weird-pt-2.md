# Happy, Sad, Evil, Weird: Putting Use Case Planning Into Practice

In [part one](https://blog.engineyard.com/2015/happy-sad-evil-weird-feature-planning) of this miniseries, we introduced formal Use Case Analysis and a simplified version called Use Case Planning which fits a rapid, iterative development process. That post went over the high-level concepts, and explained how this planning method will help you catch problems with your design before you start to implement.

In this post, the final post of this miniseries, we’ll step through a concrete example so you can see how to put Use Case Planning into practice.

## An Example

We'll imagine that we work for a company that is building a multi-tenant Software as a Service (SaaS) platform where people can set up shops and sell products. Tenants will be able to charge their customers through the platform.

We're part of a team that's getting ready to create a credit card payment acceptance feature. It will be a credit card form common to all of our tenants. We'll be writing the markup by hand and using of [Stripe](https://stripe.com/) for processing cards. We're entering a sprint planning meeting to define the scope of the work to be done and decide how long it will take to build.

During this meeting, we'll talk about many different aspects of the billing process. For the purposes of this post, let's hone in on one specific feature: once a user has clicked *Buy*, they are presented with a credit card form. We want to plan what will happen when they try to make use of this form.

Let's walk through the Use Case Planning process for this scenario.

## Step 1. Identifying the Actors and Their Roles

People:

*User*: exchanges money for goods
*Merchant*: exchanges goods for money and money for tenancy
*Platform Owner*: exchanges tenancy for money

Services:

*SaaS Platform*: provides the space for tenancy and goods to be exchanged for money
*Stripe*: verifies and charges credit cards, handles much of [PCI compliance](https://www.pcisecuritystandards.org/)
*Credit Card Company*: transfers funds between other actors

## Step 2. Describe The Purpose of The Feature

Why do we want to build a credit card form? So that we can debit the user and credit the merchant and platform owner.

## Step 3. Identify Use Case Packages

In this step, each of the stakeholders will contribute their point of view to the discovery of behaviors that we should consider.

When considering a set of use cases, I often like to think through the alternative paths first. As I mentioned above, they can yield interesting decisions that affect the way the happy path will be built.

### The Sad Path

This time, we'll start with the sad path. Considering the sad path means thinking through what should happen when one of the actors does something differently than we want them to.

Let's identify some of the sad path use cases of filling out a credit card form.

Here are a few examples:

1. User fills out the credit card form with invalid credit card information

When this use case is identified, the designer might chime in that when this happens, the invalid fields should be highlighted and an error message should be displayed explaining what went wrong.

The QA technician might point out that these validations should be ironclad; no special characters should be allowed to pass through.

2. Card is rejected by credit card company

Here, the product owner might insist that it should take as few steps as possible to resubmit the form, so that the user doesn't become frustrated and decide not to buy the product.

3. Stripe accepts card when submitted via JavaScript, but fails on subsequent server charge request

The developer would want to make sure that passing error handling from the server back to the front end is captured in this use case.

### The Evil Path

Coming up with evil paths requires to you to think like an attacker. How many ways can you come up with to exploit the feature you’re trying to build?

For example:

1. Price as set in a hidden form field and user figures out that they can change it to zero

In this scenario, the developer would want to make sure that the form is built correctly.

2. Hacker steals credit card info from the database, server logs, or an insecure network request sent over HTTP

The product owner would want to mitigate against this as much as is possible so as the protect the customer’s data. From a legal perspective, the product owner would also want to ensure that the SaaS company could not be held liable for any losses, and crucially, that PCI compliance was met.

3. If we were to save the credit card information to the user account, a hacker could launch a [CSRF attack](http://en.wikipedia.org/wiki/Cross-site_request_forgery), leveraging a logged in user's account information to order products without authorization.

The developer would suggest using a CSRF token, and the QA technician would want to make sure that form submission failed when the token was changed.

4. Security holes in session or authentication opens users up to charges

Here, the QA technician might ask what would happen when cookie or local storage data is changed. Does it fail as it should?

### The Weird Path

Coming up with weird paths requires a little more creativity.

Consider each component that your feature interacts with (both internally and externally, locally and remotely, and so on) and think through what would happen if that component failed or behaved in an unexpected way.

For instance:

1. JavaScript is disabled in the user’s browser, and  the event listener that would prevent the form from being submitted doesn’t fire. The `form` element falls back to its default behavior, which is to submit it to the SaaS server. The credit card number now appears in the server logs, making it vulnerable to information theft.

The developer would want to ensure that the form is built such that it would never be submitted to the SaaS server by mistake.

2. Stripe server is down

In this case, the designer would ask for some kind of error page, perhaps with a link to a status page where users could check for the servers to come back online.

3. Connection to Stripe is interrupted during transaction

The product owner might ask if we can resend the submission if the connection was interrupted. The developer would probably push back on that request, for security reasons. As a compromise, the designer might offer to invent an error state to be shown in this case.

### The Happy Path

This one is easy. How do you want the feature to work?

1. User successfully fills out form and clicks submit

Again, the designer would probably like to display some kind of success message here.

2. Credit card charge is accepted by Stripe

In this scenario, the product owner might ask to have the page views leading to the successful charge tracked in an analytics service, so we could track down and encourage the same behavior in the future.

## Step 4. Name and Diagram Use Cases

We've now identified twelve use cases for the credit card form feature.

I tried my hand at documenting them all in a Use Case Diagram. Green arrows represent good requests or responses, and red arrows represent errors. Bombs represent a broken network connection.

<img src="" alt="" />

As you can see, there are tons of arrows.

There are a lot of possible scenarios, and a lot of possible communications between the actors in the system. If we hadn't taken the time to think them through and uncover them all, there's high probability that we would have left some of these out.

## Converting To User Stories

Now that we've gone through these four steps, we've come out with something very valuable: bite-sized sentences that can be translated directly into stories and entered into our tracker software.

Here's what the first sad path case might look like when worded as a story:

```
As a user, when I complete the form with invalid information and click submit, I should see the invalid inputs become highlighted, and I should see validation errors telling me what went wrong so that I can correct my error and successfully buy products.
```

As you can see, we have an actor (user), action (fill out and submit form), a result (show validation errors), and a business purpose (the user can give us money).

These twelve stories are small and clear, and lend themselves to being prioritized based on the priorities of our business. Once we enter them into our tracker, we can be assured that they will all be built, and we can estimate how long it will take to happen.

## Conclusion

The Use Case Planning process is not super complicated. It consists of identifying who/what is involved, why we care, what should happen in the happy and alternative scenarios, and how do the scenarios relate?

It's a relatively low level of effort to answer these four questions and break down the scenarios, but the result is worth its weight in gold. It enables us to identify architectural concerns and edge cases early on and change them at a low cost. We've also ended up with a set of small stories with clear acceptance criteria that we can track, providing a huge value both in accountability and estimating timelines.

P. S. What do you think of Use Case Planning? Do you have any other tricks to make planning features easier? Leave us a comment!

