# Django models

What we want to create now is something that will store all posts in our blog. But to be able to do that we need to talk a little bit about things called `objects`.

## Objects

There is a concept in programming called `Object-oriented programming`. The idea is that instead of writing everything as a boring sequence of programming instructions we can model things and define how they interact with each other.

So what is an object? It is a number of properties and actions. It sounds weird, but we will give you an example.

If we want to model a cat we will create an object `Cat` that has some properties, i.e. `color`, `age`, `mood` (i.e. good, bad, sleepy ;)), `owner` (that is a `Person` object or maybe, in case of a strayed cat, this property is empty).

And then the `Cat` has some actions: `purr`, `scratch` or `feed` (in which we will give the cat some `CatFood`, which could be a separate object with properties, i.e. `taste`).

    Cat
    --------
    color
    age
    mood
    owner
    purr()
    scratch()
    feed(cat_food)


    CatFood
    --------
    taste

So basically the idea is to describe real things in code with properties (called `object properties`) and actions (called `methods`).

How will we model blog posts then? We want to build a blog, right?

We need to answer the question: what is a blog post? What properties should it have?

Well, for sure our blog post needs some text with its content and a title, right? It would be also nice to know who wrote it - so we need an author. Finally, we want to know when the post was created and published.

    Post
    --------
    title
    text
    author
    created_date
    published_date

What kind of things could be done with a blog post? It would be nice to have some `method` that publishes the post, right?

So we will need `publish` method.

Since we already know what we want to achieve, we can start modeling it in Django!

## Django model

Knowing what an object is, we can create a Django model for our blog post.

A model in Django is a special kind of object - it is saved in the `database`. You can think of model in a database as of a spreadsheet with columns and rows.

### Creating an application

To keep everything tidy, we will create a separate application inside our project. It is very nice to have everything organized from the very beginning. To create an application we need to run the following command in the console (from `djangogirls` directory where `manage.py` file is):

    (myvenv) ~/djangogirls$ python manage.py startapp blog

You will notice that a new `blog` directory is created and it contains a number of files now. Our directories and files in our project should look like this:

    djangogirls
    ├── mysite
    |       __init__.py
    |       settings.py
    |       urls.py
    |       wsgi.py
    ├── manage.py
    └── blog
            __init__.py
            admin.py
            models.py
            tests.py
            views.py

After creating an application we also need to tell Django that it should use it. We do that in the file `mysite/settings.py`. We need to find `INSTALLED_APPS` and add a line `blog` just above `)`. So the final product should look like this:

    INSTALLED_APPS = (
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'blog',
    )

### Creating a blog post model

In the `blog/models.py` file we define all objects called `Models` - this is a place in which we will define our blog post.

Let's open `blog/models.py`, remove everything from it and write code like this:

    from django.db import models
    from django.utils import timezone
    from django.contrib.auth.models import User

    class Post(models.Model):
        author = models.ForeignKey(User)
        title = models.CharField(max_length=200)
        text = models.TextField()
        created_date = models.DateTimeField(
                default=timezone.now)
        published_date = models.DateTimeField(
                blank=True, null=True)

        def publish(self):
            self.published_date = timezone.now()
            self.save()

        def __str__(self):
            return self.title

It is scary, right? But no worries, we will explain what these lines mean!

All lines starting with `from` or `import` are lines that add some bits from other files. So instead of copying and pasting the same things in every file, we can include some parts with `from ... import ...`.

`class Post(models.Model):` - this line defines our model (it is an `object`).

- `class` is a special keyword that indicates that we are defining an object.
- `Post` is the name of our model, we can give it a different name (but we must avoid special characters and whitespaces). Always start a name with an uppercase letter.
- `models.Model` means that the Post is a Django Model, so Django knows that it should be saved in the database.

Now we define properties we were talking about: `title`, `text`, `created_date`, `published_date` and `author`. To do that we need to define a type of field (is it text? A number? A date? A relation to another object, i.e. a User?).

- `models.CharField` - this is how you define text with a limited number of characters.
- `models.TextField` - this is for long texts without a limit. It will be ideal for a blog post content, right?
- `models.DateTimeField` - this is a date and time.
- `models.ForeignKey` - this is a link to another model.

We will not explain every bit of code here, since it would take too much time. You should take a look at Django's documentation, if you want to know more about Model fields and how to define things other than those described above (https://docs.djangoproject.com/en/1.6/ref/models/fields/#field-types).

What about `def publish(self):`? It is exactly our `publish` method we were talking about before. `def` means that this is a function/method. `publish` is a name of the method. You can change it, if you want. The rule is that we use lowercase and underscores instead of whitespaces (i.e. if you want to have a method that calculates average price you could call it `calculate_average_price`).

Methods very often `return` something. There is an example of that in the `__str__` method. In this scenario, when we call `__str__()` we will get a text (**string**) with a Post title.

If something is still not clear about models, feel free to ask your coach! We know it is very complicated, especially when you learn what objects and functions are at the same time. But hopefully it looks slightly less magic for you now!

### Create tables for models in your database

The last step here is to add our new model to our database. It is as easy as typing `python manage.py syncdb`. It will look like this:

    (myvenv) ~/djangogirls$ python manage.py syncdb
    Creating tables ...
    Creating table blog_post
    Installing custom SQL ...
    Installing indexes ...
    Installed 0 object(s) from 0 fixture(s)

It would be nice to see this Post model, right? Jump to the next chapter to see what your Post looks like!



