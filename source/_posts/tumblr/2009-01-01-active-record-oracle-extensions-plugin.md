---
layout: post
title: Active Record Oracle Extensions Plugin
date: '2009-01-01T22:00:00-07:00'
tags:
- oracle
- plugins
- programming
- ruby
tumblr_url: http://neutronflux.net/post/792701437/active-record-oracle-extensions-plugin
---
__NOTE:__ This post is OBE. Check out the active record oracle
adapter. Most/all changes have been added to it.

The [active_record_oracle_extensions plugin](http://github.com/eyestreet/active_record_oracle_extensions/tree/master) provides support for adding foreign keys to migrations, adding synonyms to migrations, and disabling foreign key constraints during fixture loading. These are features that I needed in my applications, but either did not exist in other plugins, or the plugins seem to have been abandoned.

<!-- more -->

{% codeblock lang:console Installing into a Rails app %}
$ script/plugin install git://github.com/eyestreet/active_record_oracle_extensions.git 
{% endcodeblock %}


## Adding Foreign Keys In a Migration

The example below shows a snippet from a migration that adds a foreign key.

{% codeblock lang:ruby %}
def self.up
    create_table :assets, :force => true do |t|
      t.integer :media_record_id
      t.string  :media_file_name
      t.string  :media_content_type
      t.integer :media_file_size
      t.datetime :media_updated_at
      t.timestamps
    end
    add_foreign_key_constraint :assets, :media_record_id, 
                                        :media_records, :id
 end
{% endcodeblock %}

## Adding a Synonym in Migration

The example below shows the creation of a synonym named events for other_user.events. The force option adds “or replace” to the sql that generates the synonym. It is assumed that the user under which your are creating the synonym already has the needed grants to allow the creation of the synonym on other_user.

{% codeblock lang:ruby %}
add_synonym :events, :other_user, :events, :force => true
{% endcodeblock %}


## Disabling Constraints During Fixture Loading

If you are using sys or system as your user for your rails application, then do not use this plugin, or use it at your own risk. During fixture loading, this plugin disables constraints for the user that has established the connection to oracle, and then re-enables the constraints after the load.
