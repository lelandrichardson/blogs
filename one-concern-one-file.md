
One Concern, One File
=====================

There is this notion in software engineering called "Separation of Concerns". The phrase is swung around a lot as if
it's an unbreakable law; something we mustn't question, only do.

It's a good rule. Digging deeper into *why* it's a good rule might lead someone to



### Model View Whatever

When people talk about "separation of concerns", it often means separating things such as "business logic" from other
things like "presentation logic". Heaven forbid, if you were even *thinking* about mixing in data retrieval logic with
any of those other concerns, you *must* be crazy.

The MVC (Model View Controller) pattern is everywhere these days. I mean *everywhere*. Even if something isn't following
a pattern even remotely close to MVC, library authors will try and somehow map elements of their library on to those
three letters in hopes that someone might be fooled into thinking it's as well thought out as the one design pattern
to rule them all: MVC.

There is something that is particularly frustrating to me that comes about when using the MVC design pattern. Let's
assume for a moment that I am building a blog. I have this notion of a "Blog" entity then. My folder structure at this
point might look something like this:

```
- Server
  - Models
      Blog.js
  - Views
      Blog.ejs
  - Controllers
      Blog.js
```

*Note: For the sake of example, I'm assuming this is a node app with js files on the server, but it doesn't make any real
difference in this case*

So we just created *three* files for our *one* concern: the blog entity. Okay, okay. That's a little bit disingenuous. With
our earlier definition of "concerns", we were talking about presentation logic (our view), business logic (our model... sorta),
data retrieval (yikes, we haven't even touched that yet), etc. According to MVC, we are separating our concerns here.

But we aren't done implementing our blog yet...

We may be generating HTML here, and we don't want to have a bunch of inline styles so we create a `Styles/blog.css` file.

We have to keep up with the trends and make our blog fancy and interactive, so of course at some point we are going to
plop some JS onto the page; let's go ahead and create a `Scripts/blog.js` file.

We know that our controller code is supposed to be minimal, so we make sure and move that code into a repository and we
create a `Repositories/Blog.js` file.


So just to recap, our project may look a little like this now:

```
- Server
  - Repositories
      Blog.js
  - Models
      Blog.js
  - Views
      Blog.ejs
  - Controllers
      Blog.js
- Client
  - Scripts
      Blog.js
  - Styles
      Blog.css
```

This is getting a little crazy. Now of course, this is just one piece (ahem, "concern") of our project. We will have many
more, and chances are each one might might have at least this many files associated with it.

To be clear, I'm not against there being multiple files in a project. Small, simple files are nice and easy to work with.
However, experience tells me that most of the time when we want to change something, we aren't likely able to do it by
just changing one of these files.  Said in another way: these files are highly coupled.




### Are we thinking about the wrong concerns?

The chances of you adding a feature to the blog entity and having to only change/update one of these files is highly
unlikely.  Not only that, but we are editing 6 files here that have the same file name!

I have often heard some people remark about how brilliantly the MVC pattern worked for them, and that they had such good
separation of concerns that they had to completely change their data store technology, and only had to change one file
(or just a few files).

This is a big win for that scenario, since your data storage implementation might only be leaked into the Repository file
or something.

However, which scenario happens more often:

> "Hey, Leland... let's go ahead and change from MySQL to MongoDB"

versus

> "Hey, Leland... let's add {feature} to {some piece of our app}"

If your answer is the former, you should run far, far away from that job and never look back.

We should optimize the ease with which we can make common everyday feature changes, since that's what we as developers
will be doing every day. It's not a perfect metric, but the closer we can get to implementing a change by just modifying
one file, the better off we probably are.



### Flux

This problem isn't unique to MVC. For instance, the Flux design pattern that is being pushed a lot in the React
community has similar issues. In a flux application, I might have a file structures like the following:


```
- Server
  - API
      Blog.js
- Client
  - Actions
      Blog.js
  - Stores
      Blog.js
  - Components
      Blog.js
  - Styles
      Blog.css
```

In this scenario, I still have many files across a single entity. Implementing a single feature will often lead to me
needing 4-5 different files open... I might add an action, subscribe to it in my store, update my component's render,
add some CSS, and finish it all off with an API call... each in different files.




## How can we make it better?

Let's pretend for a moment that you buy into this whole "Single Concern, Single File" madness that I'm spewing. Well,
what's the next step then?

I have observed two recent trends that I think are driven by this goal. Perhaps uncoincidentally, both of these ideas
were born from Facebook engineering.




### CSS in JS

If you haven't looked at the CSS in JS presentation by Christopher Chadeau (@vjeaux) at Facebook, you should take some
time to look at it. There is a whole set of problems that CSS has that are difficult to scale, and using JavaScript with
inline styles solves a lot of them.

For instance, the following two examples are equivalent:

```
// FooComponent.css
.foo-component {
    padding: 10px;
    background-color: red;
}
.foo-component-inner {
    width: 80px;
    border: 1px solid #000;
}

// FooComponent.js

// ...

render() {
    return (
        <div class="foo-component">
            Foo Component
            <div class="foo-component-inner">
                Inner
            </div>
        </div>
    );
}

```

```
// FooComponent.js
var styles = {
    outer: {
        padding: 10,
        backgroundColor: 'red'
    },
    inner: {
        width: 80,
        border: '1px solid #000'
    }
};

// ...

render() {
    return (
        <div style={styles.outer}>
            Foo Component
            <div style={styles.inner}>
                Inner
            </div>
        </div>
    );
}

```

If you've been a web developer for a long time, you might first see this and cringe&mdash;but if you think about it for
a minute, you may find that it's hard not to admit that there are some real advantages to doing it this way.

1. Scope: We know, with absolute certainty, that these styles aren't used elsewhere
2. Semantically speaking, this actually reads more clearly (ie, it makes more sense for styles to go into a `style`
property, rather than a `class` property).
3. Now that we are in JS-land, we can create/mix/compute styles with the full power of javascript behind us!
4. We've eliminated a file! No mare needing to switch between these two coupled files






### Data Retrieval







### What's next?

I really like where I think this is going, and I would like to encourage you (the
reader) to continue thinking along these lines about how we can make developing applications even nicer by minimizing
the coupling between files, even if it goes against established "best practices".