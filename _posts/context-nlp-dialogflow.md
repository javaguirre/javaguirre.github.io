---
title: Dialogflow, using context to create a trivia game
date: 2019-05-22 22:21:00 +0000
tags: dialogflow nlp
published: false
---

Dialogflow is the NLP service from Google, where you can create a bot using natural Language Processing and either integrate with messaging clients directly or using the API.

[Image or video showing the trivia game]()

A couple of months ago a well-known Spanish media company hired us to build a chatbot. The conversational assistant would do different things, but one of the most interesting in terms of how to get the most out of Dialogflow contexts was a trivia game.

We'd have a Google Spreadsheet with a question, an id and an answer that we'll use to reply to the user interacting with our bot, and also to check if the answer the question right. This way someone non technical could add as many questions as they want, and with some logic we could synchronise all these questions with Dialogflow.

We'll release the code of the Trivia game soon enough, but today I'm gonna show you how I used contexts to implement the game

# Contexts

To achieve this the simplest way possible, we'll need to use Contexts. Contexts represents the current state of the user request and allows us to carry information throughout the users session as long as we want.

Contexts are possibly the most powerful feature in Dialogflow, you can do almost anything with it. I'll show you how I created a simple Trivia game using two intents and Context.

The conversation flow was as this (picture):

- User says Play.
- enters the game, first question.
- if user answers, then you are asked to play again or leave.

We only needed two intents:

- Game, this intent will get trigger when someone wants to play, it will set the start of the game and reply with the first question.
- Game Fallback, this intent will be tied to the first one, and since we're in the Game intent context, the user will be on this intent as long as we want. When the user replies with the answer, either right or wrong, this intent will get the answer and we'll ask him/her if he/she wants to continue playing, if they do we'll maintain the current context and wait for the next answer, if they don't, we'll remove all the contexts so the user leaves the game and he/she can access all the functionality in our Dialogflow model and our bot. random questions within our Spreadsheet.

Having this models is straightforward, but what happens if the user responds a question, want to continue playing, and it gets the same question again? the user could be frustrated due to our lack of planning, but we have a solution, enter contexts!

We have four contexts to control user input:

- Game, when the user enters the game
- Game fallback, when we're waiting for the user to answer
- Question, the context accepts parameters! so we can store a list of identificators for the questions already answered, so our user doesn't get frustrated due to question repetition.

Once our user says that he/she doesn't want to continue playing we'll reset all these contexts and we start again when the Game intent gets triggered.