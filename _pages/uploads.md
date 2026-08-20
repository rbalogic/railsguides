---
layout: main_guide
title: Add picture uploads
description: "Make your ideas pop with image uploads in your Rails app."
permalink: uploads
---

# Add picture uploads

{% include main-guide-intro.html %}

The ideas we added in the previous guide can benefit from a visual element, like a picture or drawing to spark the imagination. We can attach pictures by adding a file upload to the Idea model.

## Setting up Active Storage

Rails comes with a built-in feature to handle file uploads, called Active Storage. Before we can use it, we need to set it up in the app.

Open the Terminal app and press <kbd>Ctrl</kbd>+<kbd>C</kbd> to quit the Rails server.

Then run the following commands in the Terminal:

{% highlight sh %}
rails active_storage:install
rails db:migrate
{% endhighlight %}

The first command prepares a change to the database, called a migration. The second command updates the database with the tables Active Storage uses to keep track of uploaded files.

{% coach %}
Explain what Active Storage is and how it stores the uploaded files. What are database migrations and why do we need to run them?

Resource: Rails Guides [Active Storage Overview](https://guides.rubyonrails.org/active_storage_overview.html)
{% endcoach %}

## Attaching the picture to the idea model

Rails now knows a way to store file uploads in your app. It needs a bit of help to understand where you want to attach these uploads to.

Open the `app/models/idea.rb` file in your Text Editor. This file is used to store your ideas in the database and fetch the ideas to show them. We'll change it to tell Rails every idea can have a picture.

Under the following line:

{% highlight ruby %}
class Idea < ApplicationRecord
{% endhighlight %}

add this line and save the file:

{% highlight ruby %}
has_one_attached :picture
{% endhighlight %}

This `has_one_attached` line tells the Idea model that every idea can have one file attached to it, named `picture`. Active Storage will store the file upload and link it to the idea to display it later.

## Adding the picture to the form

Now that your Idea model can have a picture attached, we can change the form to create and edit ideas to select a picture.

Open the `app/views/ideas/_form.html.erb` file in your Text Editor and find these lines near the bottom of the file:

{% highlight erb %}
  <div>
    <%= form.submit %>
  </div>
{% endhighlight %}

Above these lines, add the following lines and save the file:

{% highlight erb %}
  <div>
    <%= form.label :picture, style: "display: block" %>
    <%= form.file_field :picture %>
  </div>
{% endhighlight %}

The `file_field` helper adds a new element to the form: a file chooser, recognizable by either a "Browse..." or "Choose File" button.

## Allowing the picture to be saved

There's one more change we need to make. For safety reasons, Rails only saves the form fields the app explicitly lists as allowed. Our new picture field is not on that list yet, so it would be ignored when the form is submitted.

Open the `app/controllers/ideas_controller.rb` file in your Text Editor and find the following line near the bottom of the file:

{% highlight ruby %}
params.expect(idea: [ :name, :description ])
{% endhighlight %}

Change it to this line and save the file:

{% highlight ruby %}
params.expect(idea: [ :name, :description, :picture ])
{% endhighlight %}

If your file shows `params.require(:idea).permit(:name, :description)` instead, add `:picture` to the end of that list in the same way.

{% coach %}
Explain what strong parameters are and why Rails wants apps to list which form fields can be saved.
{% endcoach %}

## Uploading pictures

Run `rails server`.

In your browser open <http://localhost:3000/ideas/new>. Your "New idea" form will now show the new "Picture" field with the file chooser.

Fill in the form to create a new idea, and select a picture using this new element/button. Any random image you have on your laptop will do, it's just a test.

## Displaying the picture

You've now added a picture to your idea! You can't see it yet after creating the idea, the pages don't show it. Let's change it so it shows the picture.

Open the `app/views/ideas/_idea.html.erb` file in the Text Editor and find the last line:

{% highlight erb %}
</div>
{% endhighlight %}

Above this line, add the following line and save the file:

{% highlight erb %}
<%= image_tag(idea.picture, width: 600) if idea.picture.attached? %>
{% endhighlight %}

Using the `image_tag` we have told Rails to display the file upload as an image if it is present on the idea. Ideas without a picture will not show one.

Refresh the Browser. Your uploaded image should now be visible!

## Where are the pictures stored?

While developing your app, Active Storage saves the uploaded files in the `storage` folder of your app. Rails already configured Git to ignore this folder, so the uploaded pictures won't be saved with your app source code.

{% coach %}
Show the `storage` folder and the entry ignoring it in the `.gitignore` file. Explain why uploads shouldn't be stored in the app source code, and how apps that are online usually store uploads in a cloud storage service instead.
{% endcoach %}
