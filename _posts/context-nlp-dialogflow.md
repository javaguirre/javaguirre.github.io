---
title: Dialogflow, using context to create a trivia game
date: 2019-05-22 22:21:00 +0000
tags: git
published: false
---

Dialogflow is the NLP service from Google, where you can create a bot using natural Language Processing and either integrate with messaging clients directly or using the API.

A couple of months ago we werehired to do a chatbot for a well known Spanish media corporation, the conversational assistant would do different things, but one of the most interesting in terms of how to get the most out of Dialogflow was a trivia game.

Contexts are possibly the most powerful feature in Dialogflow, you can do almost anything with it. I'll show you how I created a simple Trivia game using two intents and Context.

The conversation flow was as this (picture):

- User says Play.
- enters the game, first question.
- if user answers, then you are asked to play again or leave.

We have four contexts to control user input:

- Question