---
layout: main_guide
title: Create picture thumbnails
description: "Optimize image uploads by creating thumbnails: smaller versions of the original file uploads."
permalink: thumbnails
---

# Create picture thumbnails

*Originally created by Miha Filej, [@mfilej](https://twitter.com/mfilej)*

{% include main-guide-intro.html %}

The Internet is all about speed. The faster your page loads, the less likely people are to click away. And we want people to stick around to share our great ideas with our ideas app! Also, people with data plans visiting your website will thank you for requiring less data to be transmitted.

One way to speed up page loads is by displaying images in a smaller size. The smaller an image is, the fast it will be transmitted over the Internet.

## Install ImageMagick

We'll be using the ImageMagick tool to resize the pictures uploaded to your ideas.

<div class="os-specific">
  <div class="mac">
    <p>Run the following command in the Terminal app:</p>
{% highlight sh %}
brew install imagemagick
{% endhighlight %}
  </div>
  <div class="nix">
    <p>If you are on Ubuntu, run the following command in the Terminal app:</p>
{% highlight sh %}
sudo apt-get update
sudo apt-get install -y imagemagick
{% endhighlight %}
  </div>
  <div class="win">
    <p>Download and run the <a href="https://www.imagemagick.org/script/download.php#windows">ImageMagick installer</a> (use the first <em>download</em> link). In the installation wizard, make sure you check the checkbox to install legacy utilities.</p>
  </div>
</div>

{% coach %}
Explain what is ImageMagick and how is it different from libraries/gems we used before?
{% endcoach %}

## Install a Ruby gem for image resizing

For Ruby to talk with ImageMagick, we'll be using the `image_processing` Ruby gem. A Ruby gem is a piece of software we can install in the app.

Open `Gemfile` in your Text Editor and find this line:

{% highlight ruby %}
# gem "image_processing", "~> 1.2"
{% endhighlight %}

Remove the `#` sign at the front of the line and save the file. If the line is not in your `Gemfile`, add it without the `#` sign instead.

{% coach %}
Explain the concept of comments in code. Explain what libraries (Ruby gems) are and why they are useful. Describe what Open Source software is.

Resources: RubyGems GitHub [introduction](https://github.com/rubygems/rubygems#rubygems-) and Wikipedia [OSS](https://en.wikipedia.org/wiki/Open-source_software)
{% endcoach %}

In the Terminal app run this command:

{% highlight sh %}
bundle install
{% endhighlight %}

This will install the "image_processing" gem we enabled in the `Gemfile` file.

## Tell your app to use ImageMagick

By default Rails resizes images with a different tool, called libvips, which may not be available on your computer. Let's tell the app to use the ImageMagick tool we just installed.

Open `config/application.rb` in your Text Editor and find the line that looks like this:

{% highlight ruby %}
class Application < Rails::Application
{% endhighlight %}

Below this line, add this line and save the file:

{% highlight ruby %}
config.active_storage.variant_processor = :mini_magick
{% endhighlight %}

Make sure to (re)start your Rails server after changing the configuration.

## Display the thumbnail

Active Storage can create resized versions of the uploaded pictures, called variants. The thumbnail variant is created the first time it is requested, so we only need to ask for it in the view.

Open `app/views/ideas/_idea.html.erb` and change the line:

{% highlight erb %}
<%= image_tag(idea.picture, width: 150, height: 150, class: "img-thumbnail flex-shrink-0") if idea.picture.attached? %>
{% endhighlight %}

to this line:

{% highlight erb %}
<%= image_tag(idea.picture.variant(resize_to_limit: [150, 150]), width: 150, height: 150, class: "img-thumbnail flex-shrink-0") if idea.picture.attached? %>
{% endhighlight %}

Take a look at the [list of ideas](http://localhost:3000/ideas) in the Browser to see if your ideas now have a thumbnail.

{% coach %}
Explain what specifying the image width in HTML and how it differs from resizing images on the server. Both images may look small, but only one is resized as a thumbnail.
{% endcoach %}
