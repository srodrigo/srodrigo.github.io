---
layout: page
title: ISAs exercise
permalink: /isas/
---

## Introduction

This exercise requires adding support for ISAs for retail customers. This functionality must be separate from ISAs for employees of employers.

## Main assumptions

1. The user is already logged in when they start using the new feature.
2. The list of available funds is server-side rendered and available when the user lands on the page. Therefore, the website doesn't need to make a request to retrieve the funds.
3. The user ID can be retrieved from the API token, so it doesn't need to be included in the requests.
4. There is a set of shared API services (ISAs, Users, probably Pensions) that web and mobile APIs, which act as a facade, can make use of.
5. While the exercise doesn't specify this detail, we assume that the website could support multiple currencies.

## Solution

### Overview

The solution focuses on Web, but it could be easily adapted for Mobile apps. Only the happy path (no errors occur during the user journey) has been considered.

Since the exercise specifies that we can choose how to document or present the solution to provide a starting point for a discussion, here is a brief summary of the approach taken:

1. Design the user journey for the new feature by providing a mockup.
2. Design a new API for retail ISAs, explaining the assumptions made regarding existing API services that could be used to implement the feature.
3. Define the main interactions between user, web page and APIs via activity diagram.

### User Journey

The user lands on the web page to create a retail ISA. There is a list of funds available. The user must follow the steps below:

1. Select a fund from the list.
2. Enter an amount to deposit. The input component is of type number, so the only validation at this stage would be to forbid £0.
3. Submit the data to make the deposit. Both fund and amount should be provided for this button to be visible and enabled.
4. The user can see a success screen to confirm that their deposit was successful.

Here is a mockup of the user journey:

<div style="text-align:center">
<img src="{{ '../assets/img/isa-user-journey.gif' | relative_url }}" alt="Happy path - User journey"/>
</div>

### API Design

There is a requirement to keep employee and retail ISAs separate. Assuming there is an existing API for employee ISAs, retail ISAs could have their own API under `/isa/retail`. Using REST naming conventions, a POST to `/isa/retail` creates a new retail ISA.

We are assuming that the request includes an authentication token (i.e. JWT). Therefore, the user ID doesn't need to be sent as part of the request.

There is another requirement to anticipate users selecting multiple funds in the future. Each fund should get an amount to be deposited into. While the exercise will use a single fund, we need to make the solution future-proof for multiple funds. One way of solving this challenge is to send an array of deposits with a single entry and validate on the API that only one fund is included for now. The payload for the POST `/isa/retail` request is an object with a list of _deposit_ objects that include _fundId_, _amount_ and _currency_. Later on, when the requirement to allow multiple funds needs to be implemented, the backend can remove the single fund restriction but the shape of the payload wouldn't need to change.

The exercise expects assumptions regarding the backend services already available. We are assuming that there is a set of shared backend services that can be used by both `/isa/employee` and the new `/isa/retail`, as well as any other APIs such `/pensions`. On the diagram below, there are 3 backend services (Users, ISAs, Pensions) and 2 of them are used by both `/isa` frontend APIs.

<div style="text-align:center">
<img src="{{ '../assets/img/isa-dependencies-diagram.png' | relative_url }}" alt="Depencencies diagram"/>
</div>

### Sequence diagram

The sequence diagram below highlights the main interactions between user, web page, retail API and backend services. It follows the steps on the _User Journey_ section and follows the _API design_ on the previous section.

<div style="text-align:center">
<img src="{{ '../assets/img/isa-sequence-diagram.png' | relative_url }}" alt="Happy path - Sequence diagram"/>
</div>
