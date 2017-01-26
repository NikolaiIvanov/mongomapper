---
layout: documentation
title: Dynamic Querying
---

MongoMapper provides support for Dynamic Querying in its finders, familiar to many Rails developers. Similar to [dynamic finders in ActiveRecord](http://guides.rubyonrails.org/active_record_querying.html#dynamic-finders) , MongoMapper provides a finder for every key defined in your document.

Let's start with a simple User model:

{% highlight ruby %}
class User
  include MongoMapper::Document

  key :first_name, String
  key :last_name, String
  key :age, Integer

  scope :smiths, where(:last_name => "Smith")
  scope :johns, where(:first_name => "John")
end
{% endhighlight %}

Let's create some users that we can query dynamically later:

{% highlight ruby %}
User.create(:first_name => "Ann", :last_name => "Smith", :age => 24)
User.create(:first_name => "John", :last_name => "Smith", :age => 42)
User.create(:first_name => "John", :last_name => "Zoidberg", :age => 46)
{% endhighlight %}

We can now dynamically query for a user by one or multiple keys like so:

{% highlight ruby %}
User.find_by_first_name("Ann")
User.find_by_first_name_and_last_name_and_age("John","Zoidberg",46)
{% endhighlight %}

In order to query for all possible users simply add the ***all*** keyword to the query which will return an array of results

{% highlight ruby %}
User.find_all_by_last_name("Smith").length # => 2
{% endhighlight %}

Remember the scopes that we defined in our User class earlier? Well MongoMapper allows you to dynamically query those scopes too:

{% highlight ruby %}
User.johns.find_by_last_name("Zoidberg")
User.smiths.find_all_by_age(24)
{% endhighlight %}

Another convenient feature that MongoMapper provides (similar to ActiveRecord) is the ability to find or initialize a record by one or more keys:

{% highlight ruby %}
User.find_or_initialize_by_last_name("Nunemaker")
User.find_or_initialize_by_first_name_and_last_name("Haris","Amin")
{% endhighlight %}

In the above queries, if a user is not found, the user is then ***initialized*** with the attributes given to the dynamic query. If you want to ***save*** and actually ***create*** those records using dynamic finders, you can write the above queries like so:

{% highlight ruby %}
User.find_or_create_by_last_name("Nunemaker")
User.find_or_create_by_first_name_and_last_name("Haris","Amin")
{% endhighlight %}

Dynamic Querying also has support for the ***!*** operator. So if a document is not found in a dynamic query using the ***!*** operator, an exception will be raised likewise:

{% highlight ruby %}
User.find_by_first_name_and_last_name!("Why", "The Lucky Stiff") # => MongoMapper::DocumentNotFound: Couldn't find Document with {"first_name"=>"Why", "last_name"=>"The Lucky Stiff"} in collection named users
{% endhighlight %}