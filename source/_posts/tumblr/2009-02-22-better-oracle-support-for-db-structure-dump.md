---
layout: post
title: Better Oracle Support For db:structure:dump
date: '2009-02-22T22:00:00-07:00'
tags:
- ruby
- programming
- plugins
- oracle
tumblr_url: http://neutronflux.net/post/792712939/better-oracle-support-for-db-structure-dump
---
If you have tried

{% codeblock lang:ruby %}
$ rake db:structure:dump
{% endcodeblock %}

in a rails app when using Oracle as your database, then you have probably been slightly disappointed. The same rake task for MySQL and Postgres dumps everything you need to recreate the database. The task for Oracle only dumps SQL for the table creation (no constraints), and for the sequences. The [active_record_oracle_extensions](http://github.com/eyestreet/active_record_oracle_extensions/tree/master) plug now supports dumping sql for the following:

* primary keys
* indexes
* foreign keys
* synonyms

I have added enough functionality to scratch my itch. If you need something else or find a bug let me know.
