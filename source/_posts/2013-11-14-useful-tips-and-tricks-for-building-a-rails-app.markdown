---
layout: post
title: "Favorite (Rails) developer tips and tricks"
date: 2013-11-14 20:47
comments: true
categories: 
---
As I've grown as a developer, I often find myself using a set of useful commands, tips and tricks both within Rails applications and more broadly. If you find yourself regularly coming across a pretty generic problem or pattern when coding, the development community has probably found a way to address it. I find this especially true in Rails, where there seems to be a slew of methods geared especially toward humans (e.g., Rails text formatters).

<!--more-->

**The 'presence' method:** `@object.presence`

The presence method goes one step further than `@object.present?` by returning the attribute or value being checked for rather than just true. Both `.present?` and `.presence` return `nil` for falsey evaluations, but using `.presence` helps streamline the common pattern of checking for the presence of a value before returning it in order to prevent nil reference errors. For example, say you have a nested hash that looks like this:

`team = {name: 'Chicago Bears', mascot: 'Bears', coach: {name: 'Marc Trestman', years: '1'}}`

If you want to get the name of a coach for a given team, you could write the following in order to avoid an error when checking for a nested hash key that may or may not be there:

`team[:coach][:name].present? ? team[:coach][:name] : nil`

But it would be more terse to write instead as follows:

`team[:coach][:name].presence`

Here's the documentation that describes the method (very straightforward): [Rails documentation](http://api.rubyonrails.org/classes/Object.html#method-i-presence)

`pp` **for pretty printing**

When printing lengthy and complex data structures, its pretty difficult to read all the text inline. Prepending `pp` when using pry or irb before a data structure prints out the object in a pretty fashion. I find `pp` most helpful when using debugger, which doesn't provide pretty printing by default. Reading the structure of a complex JSON object is never fun; using `pp` makes it considerably more tenable by properly indenting and subordinating attributes as needed.

`@object#changed?`

While a Rails object is in memory, you can call `@object.changed?` on it to see if the object has changed in the application session. This can be helpful in minimizing database transactions in that you can check if an object has changed before persisting the object to the database. No need to save an object if nothing about that object has changed from the last time it was saved.

`wtf?(??????!!)`

Pry is one of my favorite gems because of how simple it is to use and that the console output is by default pretty printed. The developers of pry also seem to have a great sense of humor, in that when you want to see the application trace for an error, you can run `wtf?` in the pry console to see lines of the backtrace. Even better, you can keep adding `?` or `!` to the end of the `wtf` call to see more lines. I love it when software mimics the real world.

`constantize`, `classify`, `demodularize` **and other Rails text helpers**

People joke that Rails abstracts so many implementation details away that you could practically just write `rails new application` and your entire app would be built for you. While that's clearly an exaggeration, there is some truth that Rails provides a slew of helper methods to properly format text or perform simple data transformations. 

For example, `string.constantize` will directly convert a string into a class representation, so you can call `"Article".constantize.new`, and a new article instance will be created. `string.classify` will transform a string of text into a string that can then converted to a class representation. Whereas `"string".constantize` will yield `NameError: wrong constant name string`, `"string".classify` will yield `"String"`, which can then be properly constantized. `string.classify` will also convert table names to object names and handle the pluralization for you (e.g., `"articles".classify` becomes `"Article"`). Finally, `string.demodularize` is used strip a module from its parent namespace so that the return value is just the module name represented as a string (e.g., `"Article::Foreign".demodulize` becomes `"Foreign"`). This can be helpful for when you have a subclasses of a parent class and want to build a view differently based on each subclass; the `demodulize` method is a quick helper that you can use to avoid writing a lengthy chain of `string.gsub` or `string.capitalize`.

`git commit --amend`

This git command opens up a previous git commit and attaches changes that you have in staging to that git commit rather than making an entirely new commit. I use this for whenever I make a git commit, but then realize that I forgot to remove a `debugger` call or have to make some other similarly mundane change. Rather than making a new commit, I'll typically just open the previous commit with `git commit --amend` which will not only include the changes in the previous commit, but also allow me to edit the commit message if needed.

`git diff --cached`

Have you ever added a set of files to staging in git without remembering what you changed? One way to review what you changed is to run `git reset HEAD .` to unstage your changes, or you can just run `git diff --cached` and it will show you a diff between what you have staged and the latest commit.

`git stash` / `git stash list` / `git stash pop`

I use `git stash` commands fairly regularly, primarily because I'm often working on a few branches at once. The git process on my team at the NYT typically consists of feature branches with pull requests into the develop branch, which automatically builds onto the development server environment. But pull requests often take a few hours before they are merged into develop simply because the developers on my team have other things to do besides review PRs. So I'll start working on another feature branch or bug fix, liberally using the suite of `git stash` commands to manage my working files between branches without committing incomplete work.

**cmd-T for Github file quick search**

I'm proud that I showed this quick Github shortcut to my senior developer. Pressing `command + T` when in a Github repo will open a file search bar at the top of the repo, allowing a quick search for any file in the repo. If you use Sublime Text, you likely already use this shortcut to quickly navigate your file directory, so hopefully its not too much of a change to start doing the same in Github.

Those are just a few of my favorite go-to tricks for developing applications in Ruby/Rails that I find myself using on an almost daily basis. What are some of yours?