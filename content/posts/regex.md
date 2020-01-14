---
title: "Regex tips"
date: 2020-01-14T21:17:40+02:00
---


`[0-9](?=[a-z])` will match any one digit followed by a lowercase letter.  
`[0-9](?![a-z])` will match any one digit NOT followed by a lowercase letter. (this is called a negative lookahead)  
`(?<=[a-z])[0-9]` will match any one digit preceeded by a lowercase letter.  
`(?<![a-z])[0-9]` will match any one digit NOT preceeded by a lowercase letter.(this is called a negative lookbehind)  


