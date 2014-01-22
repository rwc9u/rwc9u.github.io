---
layout: post
title: Forcing :before_validations to run and save the updated data
date: '2010-12-17T10:44:17-07:00'
tags:
- rails
- active_record
tumblr_url: http://neutronflux.net/post/2349799138/forcing-before-validations-to-run-and-save-the-updated
---
I meant to write this up awhile ago, but just want to get it out… Have you ever decided to massage incoming data in a :before_validation. I ran into an issue recently where I added a change to a before_validation to remove extraneous characters from phone numbers. I already had a ton of numbers in the system, so I thought I would then be able to just iteration through the list of phone numbers and resave them, and voila… my data would be updated and I could then enable additional validation checks.

Nothing happened. After a bit of digging I realized that Rails was assuming that nothing was changing because the new phone number value was the same as the old value so no save was occurring and validations (and before_validation hooks) were not being run. Turning off partial updates temporarily in the console allowed me to resave my data and have the new before_validation hook massage my data.

 
