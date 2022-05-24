# Rivals Deck Generator

Welcome to Rivals Deck Generator

## Introduction

Rivals Deck Generator is a tool to generate custom decks.
At its core, it takes a custom logic in the form of JavaScript code provided by the user
and run it through all possible decks in Rivals.
Then it selects a certain number of decks uniformly randomly and output to the user.

![dataflow][dataflow]

## Components

This application consists of several components.

### **!!! Important Note !!!**

This application is written in pure JavaScript and does not have any backend servers.
Any data created by the user, including custom predicates and deck generated,
are stored in the computer and not sent to anyone anywhere.

The current version of the application will save all data in memory only (it does NOT use Cookie).
This means **all data created will be lost upon refresh of the page**.
Use the navigation bar on top to switch between different components, and do NOT the use browser back button.

### Generator

This is the main user interface for generating decks.
The user need to specify the maximum number of decks, faction, and a predicate (custom logic).
The predicate can be created and edited in Code Editor.
The process of generation will be displayed,
and the generation process can be cancelled if it takes too long.
Once the generation is completed, result will be displayed below.
Each deck has a unique ID. Click on the id will reveal the deck.

**UPDATE(5/23)**
Click on the `Copy Decks` button will copy all the deck ID of the generated decks to clipboard.
This string can be shared. To reavel the decks, paste this string in Deck Viewer.

### Code Editor

Code editor allows user to write predicates that are used to generate decks.
Predicate are written in the form of JavaScript code.
There are a few built in predicates provided as an example.
Here is a [guide][predicate] and [tutorial][tutorial] on how to write predicates.

### Deck Viewer

Deck Viewer is a utility that allows user to query decks using the deck ID.

Deck ID is a string in the form of `<faction>-<number>`, such as `gdi-12345` or `nod-7890`.
Each deck has a unique ID and each ID correspond to only 1 deck.

**UPDATE(5/23)**
Deck Viewer now support viewing multiple decks if a comma-separated list of decks is supplied.
For example, `gdi-12345, nod-7890` will display 2 decks (white spaces are ignored).

### Data Editor

Data Editor is a utility that allows user to view and edit unit and commander data (or load external data).
This component is current not yet implemented and may be added in the future.


[dataflow]: Rivals-Deck-Generator-Dataflow.svg
[predicate]: predicate.md
[tutorial]: tutorial.md
