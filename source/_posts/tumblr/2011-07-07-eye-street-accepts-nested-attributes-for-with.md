---
layout: post
title: 'Accepts Nested Attributes with Polymorphic Associations and a Custom Autosave'
date: '2011-07-07T08:11:24-06:00'
tags: 
tumblr_url: http://neutronflux.net/post/7341736056/eye-street-accepts-nested-attributes-for-with
---
Edit: This was originally posted on
[my companies blog](http://blog.eyestreet.com/post/7309604903/accepts-nested-attributes-for-with-polymorphic).

Wheew… what a title… kinda dry, very geeky, but if you landed here, hopefully this will help. This blog documents some of the code wrangling that we did for a recent app, VRCompliance. I needed to have the ability to create multiple phone numbers when inputting either addresses or people in our system. It seemed like a great time to use models with with the :accepts_nested_attributes_for :nested_model declaration. When doing this I found out that the default creation of a nested object was not what I wanted. Essentially, I wanted  a find_or_create for the nested object. After some searching I came across this StackOverflow article that describes a solution. This post adds to the discussed solution by going into additional detail and fleshing out the answer described in the previous link.

<!-- more --> 

For this post I’ve simplified our objects so our sample application has people, addresses, and phone numbers. Both a person and an address can have many phone numbers. Additionally, with our application’s data it is possible to have a many-to-many relationship between the phone numbers, people and addresses. Instead of using two separate join tables to describe these relationships, I went with a polymorphic association, calling our resulting common join table, phonings, and the polymorphic relations a phonable.

Our  models look similar to the snippets below:

{% gist 1038766 address.rb %}

{% gist 1038766 person.rb %}

{% gist 1038766 phone_number.rb %}

And the initial phoning model was:

{% gist 1038766 phoning_original.rb %}


With the models and their relationships defined, I built the forms for creating an address, and wanted to nest the creation of the phone numbers within the forms for the creating of both a person and address. Initially, I think I had  :accepts_nested_attributes_for :phone_numbers in the address object, but there were issues with that approach. 

First, deletion of the phone number deleted the object, and second, if I added the phone number to two different objects then I would get validation errors due to uniqueness constraints that I have on our real phone number object.  After digging around, I found some links that discussed making the nested attributes for the join table so that on deletion you are really deleting the join object and not the object it relates. This resulted in :accepts_nested_attributes_for :phonings in the address object (as seen above).

Deleting of an object no longer deleted my phone number object, just
the phoning object. However, I still had the issue that if the same
phone number was added to two addresses via nested attributes then two
distinct phone number objects were created. The test below shows
passing tests that document this issue. Two addresses are initialized
and saved with the same nested phone number. After saving both, we
find that we have two phone number objects with the same phone number.

{% gist 1038766 address_test.rb %}


Based on the StackOverflow thread from above, I realized that I needed to change the autosave creation of phone numbers. It should be noted that accepts_nested_attributes_for automatically sets :autosave for the model. Autosave means that associated records are automatically saved when the parent object is saved. In our examples this means that when an Address object is saved the Phoning object and the PhoneNumber object are saved. The default functionality is to create a new object. The gist below shows the necessary changes to our test to generate the desired functionality of the find or create for the nested phone numbers.

{% gist 1038766 address_test_2.rb %}


We can then move that functionality back in to our model:

{% gist 1038766 phoning.rb %}


In a future post, I will walk through the view and controllers used for these nested forms.
