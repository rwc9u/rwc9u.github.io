---
layout: post
title: Migrations Without a Restart on Heroku
date: '2012-06-19T02:02:00-06:00'
tags: 
tumblr_url: http://neutronflux.net/post/25423652036/migrations-without-a-restart-on-heroku
---
I ran into a situation today where I had 10 long running background processes that were getting bogged down because we were missing an index. The app is running on Heroku on a shared database, so I don’t have the luxury of using psql to add the needed index. However, I wanted to figure out a way to add the index without restarting the long-running processes.

My solution was to log in via the console, and create the migration by
hand.

{% gist 2952889 gistfile1.rb %}

Once complete, I added a new migration within the source code that has the option :quiet => true (_updated: see note below), so that all environments/developers get the manually created index that resides in production. Had I just done a git push to heroku, the background processes would have restarted. The results of adding the needed index can be seen in our New Relic stats below:

{% img /images/new_relic_throughput.jpg [New Relic Stats [New Relic Stats After Index Added]] %}


Note: I thought :quiet was a valid option for add_index, but it is actually a patch that you will need to add for your specific database. The following article describes a [patch for MySQL](http://grosser.it/2009/06/06/add_index-and-remove_index-now-quiet-on-duplicate-index/). See mine for PostGreSQL.

{% gist 2952889 add_index_patch.rb %}
