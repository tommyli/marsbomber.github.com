---
layout: post
title: Doctrine tricks
wordpress_id: 159
wordpress_url: http://blog.elinkmedia.net.au/?p=159
date: 2010-01-30 13:45:47 +11:00
---
I have been using Doctrine ORM for a while now. Here I want to discuss 2 tricky issues I have encountered and resolved.

First, I'm paranoid of deleting records from the database. Things like registered users, I would rather have them marked as "DELETED", but still keep the record in the database, just in case ...

With Doctrine, the SoftDelete behaviour is perfect for this purpose. When a model is enabled with the SoftDelete behaviour, an extra field "deleted_at" is automatically added to the corresponding database table by default. So, when you call the delete() function on a model object, the corresponding data row in the database receives a timestamp on the "deleted_at" field. Data having values on the "deleted_at" field will not be returned by any find methods in the future.

It all sounds good, right? Not until you start trying to cascade "delete" on associations. Since we are not really deleting the record, DBMS level delete cascading will simply not work (In Doctrine YAML schema term, the "onDelete: CASCADE" will not work). Now, I'll illustrate the problem and the solution with a typical blog example.

In a blog application, we have 2 entities, article and comment. 1 article has many comments. When an article is "soft deleted", all the associated comments should go to. The schema YAML is something like this
{% highlight php %}
Article:
  actAs: [Timestampable, SoftDelete]
  columns:
    article_content:
    type: string()
  relations:
    Comments:
      foreign: article_id
      type: many
      cascade: [delete]

Comment:
  actAs:[Timestampable, SoftDelete]
  columns:
    article_id:
      type: integer
    comment_content:
      type: string()
  relations:
    Article:
      foreign: id
      type: one
{% endhighlight %}

So, you can see, the trick is done on line 10 of the yaml file. With "cascade: [delete]" specified. You now can soft delete an article and all associated comments by doing something like this
{% highlight php %}
$article = Doctrine_Core::getTable('Article')->findOneById(1);
$article->delete();
{% endhighlight %}
Now it works as expected.

One thing I need to point out is performance. Doing SoftDelete means Doctrine will do a lot separate update queries behind the scene (to set the deleted_at field). For models with a big association list, performance could degrade dramatically. I normally enable SoftDelete on all necessary models to start with. As the project continues, my understanding of the problem domain improves too, then I look at taking the SoftDelete off some unnecessary models. If some models do need to keep historical data, and performance is becoming a problem because of SoftDelete, I then look at other data archiving strategies so that we could fix up the bottle neck again by removing the SoftDelete behaviour on the offending models.

Let's move onto the next issue.

There are times that your application will need to obtain a model record, hold it memory, massage some attribute values, then save it back to the database. The issue raised is that after a record (we call this record A) is retrieved and before it's saved back, if another part of your application code retrieves the same record again. By default, Doctrine will flush record A and set all attributes back to its original values. This means any changes you make in memory on record A are lost. Let's use the article model again here to illustrate the issue in code.
{% highlight php %}
$articleOne = Doctrine_Core::getTable('Article')->findOneById(1);
$articleOne->article_contents = "replace old with new";

$articleOneDup = Doctrine_Core::getTable('Article')->findOneById(1);

// you will see the article_contents will be reset back to its original value, changes are lost
var_dump($articleOne);
{% endhighlight %}
This is the default behaviour of Doctrine. If you ever want to change this behaviour, you could use something like this
{% highlight php %}
Doctrine_Manager::getInstance()->setAttribute(Doctrine::ATTR_HYDRATE_OVERWRITE, false);
{% endhighlight %}

That's it. Hope someone find this useful.
