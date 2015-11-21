---
layout: post
title: 'Refactoring: Improving the code or just understanding it better?'
date: 2012-02-26 18:44:20.000000000 +01:00
type: post
published: true
status: publish
categories:
- Clean code
tags: []
---
During a brief discussion about refactoring at last iteration's retro, a colleague mentioned that the feeling that refactoring has improved the code might be somewhat illusive.

Some changes are more obvious than others. For example, changing a data structure from a linked list to a hash table for a function that is used for searching will decrease the function's time complexity from O(n) to O(1). The way these sort of refactorings change the function can easily be measured. Removing a magic number will increase readability and can be measured.

Other refactorings will be much harder to measure, such as changing names or moving around loops if-then-else-blocks. To be able to make changes to the names of functions and variables, one has to go through them to understand their meaning. Changing their names might not decrease the time someone else needs to understand the code though. In this unfortunate case, the refactoring has been pointless.