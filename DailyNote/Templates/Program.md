---
title: <% tp.file.title %>
created: <% tp.file.creation_date() %>
updated: <% tp.file.last_modified_date() %>
tags:
  - 学习/编程/<% tp.system.prompt("标签：学习/编程/?") %>
---

<% tp.file.cursor(1) %>
