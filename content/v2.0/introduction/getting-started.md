---
title: "Getting Started"
order: 10
aliases:
  - "/getting-started"
  - "/introduction/getting-started"
---

<p id="getting-started-lead" class="lead">
  Hi there! <br/><br/>
  You're reading the 'Getting Started' guide for Hanami 2.0.
  It's still in <strong>alpha</strong>, so this guide is a little rough.
  An obvious example is that the generator
  (<code>bundle exec hanami generate ...</code>) commands listed below
  are not implemented yet.<br/><br/>
  Likewise, we'll instruct you to clone a template repository.
  We will have a `hanami new` command that will create all these files
  but that's not done yet either.<br/><br/>
  It'll have the options you expect for customizing your project:
  specifying a database engine, specifying a view template engine, etc.
  Please note that our application template uses `slim` for view templating
  but Hanami 2 will support <a href="https://github.com/rtomayko/tilt">any templating engine</a>.<br/><br/>
  The 'Getting Started' guide generally wants you to follow along,
  but it might be better to just read through it at this point.

  <!-- TODO: I liked the 'letter' that was here but we should write a whole new one for Hanami 2.0 -->
</p>

<br>
<hr>

## Prerequisites

Before we get started, let's get some prerequisites out of the way.
First, we're going to assume basic knowledge of developing web applications.

You should also be familiar with
[Bundler](http://bundler.io),
[Rake](http://rake.rubyforge.org),
working with a terminal,
and building apps using the
[Model, View, Controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) paradigm.

Lastly, in this guide, we'll be using an [SQLite](https://sqlite.org/) database.
If you want to follow along, make sure you have a working installation of Ruby 2.6+ and SQLite 3+ on your system.


## Create a New Hanami Project

<p class="alpha">
  The `hanami new` command in this section doesn't exist for Hanami 2 alpha yet.<br/>
  Instead run:<br/>
  <code>
    $ git clone https://github.com/hanami/hanami-2-application-template.git bookshelf<br/>
    $ cd bookshelf<br/>
    $ ./bin/install bookshelf<br/>
    $ mv .env-example .env<br/>
    $ bundle exec rackup
  </code>
  <br/>
  This runs a 'rack' server using the `config.ru` file.<br/>
  You can checkout the running server at: http://localhost:9292/<br/>
  This simple rack server doesn't include code reloading,
  so you'll have to restart the server with each code change.
  (We'll have a server that has code reloading in development in Hanami 2, just not yet.)<br/><br/>
  Then please skip to the next section ("Hanami's Architecture"), dear alpha reader.
</p>

To create a new Hanami project,
we need to install the Hanami gem from Rubygems.

Then we can use the `hanami new` command to generate a new project:

```shell
$ gem install hanami
$ hanami new bookshelf
```

<p class="notice">
  By default, the project will be setup to use a SQLite database. For real-world projects, you can specify your engine:
  <br>
  <code>
    $ hanami new bookshelf --database=postgres
  </code>
</p>

This creates a new directory `bookshelf` in our current location.
Let's see what it contains:

```shell
$ cd bookshelf
$ tree -L 1
.
├── Gemfile
├── README.md
├── Rakefile
├── config
├── config.ru
├── db
├── lib
├── public
├── slices
└── spec

#{N} directories, #{Y} files
```

Here's what we need to know:

* `Gemfile` defines our Rubygems dependencies (using Bundler).
* `README.md` tells us how to set up and use the project.
* `Rakefile` describes our Rake tasks.
* `slices` contains one or more web applications compatible with Rack, where we can find the first generated Hanami application called `main`. It's the place where we find our actions, views, routes, templates, repositories, and entities.
* `config` contains configuration files.
* `config.ru` is for Rack servers.
* `db` contains our database schema and migrations.
* `lib` contains our common business logic
* `public` will contain compiled static assets.
* `spec` contains our tests.

Go ahead and install our gem dependencies with Bundler; then we can launch a development server:

```shell
$ bundle install
$ bundle exec hanami server
```

And... bask in the glory of your first Hanami project at
[http://localhost:2300](http://localhost:2300)! We should see a screen similar to this:

<p><img src="/v1.3/introduction/welcome-page.png" alt="Hanami welcome page" class="img-responsive"></p>

## Hanami's Architecture

Hanami's architecture revolves around your project containing many `slices`.
These all live together in the same codebase, and (by default) exist in the same Ruby process.

They live under `slices/`.

By default, we have a `main` slices, which can be thought of as a standard, user-facing web interface.
This is the most popular, so you'll probably want to keep it in your future Hanami projects.
However, there's nothing unique about this slice, it's just so common that Hanami generates it for us.

Later (in a real project), we would add other slices, such as an `admin` panel, a JSON `api`, or an analytics `dashboard`.
We could also break our `main` slice into smaller parts, extracting isolated pieces of functionality.
Hanami fully supports that, too!

Different `slices` represent __delivery mechanisms__.
That means they're different ways of interacting with the core of your project, or the "business logic".

Hanami doesn't want us to [repeat ourselves](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) therefore "business logic" is shared.
Web applications almost always store and interact with data stored in a database.
Both our "business logic" and our persistence live in `lib/`.

_(Hanami architecture is heavily inspired by [Hexagonal architecture](https://en.wikipedia.org/wiki/Hexagonal_architecture_(software)).)_

## Writing Our First Test
<p class="alpha">
  The `hanami-2-application-template` already has this spec included.
  It likely won't stick around when we move to generating the new application,
  but you can take a look at the test and run it., then skip to the next section.<br/><br/>
  It's located at: `spec/suite/main/features/home_spec.rb`,
  and you can run the entire suite (which only currently includes this single spec)
  with `bundle exec rake`.<br/><br/>
  You'll see that the test already passes,
  because the template already includes a route, action, view, and template for the root path.<br/><br/>
  The routes file is located at `config/routes.rb`.<br/><br/>
  The action file is located at `slices/main/actions/home/show.rb`.<br/><br/>
  The view file is located at `slices/main/views/home/show.rb`.<br/><br/>
  The template file is located at `slices/main/templates/home/show.html.slim`.<br/><br/>
  Please read through this section <strong>and</strong> the next one ("Following a Request"),
  but then move on to the "Generating New Actions" section.<br/><br/>
</p>

The opening screen we see when we point our browser at our app is a default page which is displayed when there are no routes defined.

Hanami encourages [Behavior Driven Development](https://en.wikipedia.org/wiki/Behavior-driven_development) (BDD) as a way to write web applications.
To get our first custom page to display, we'll write a high-level feature test:

```ruby
# spec/suite/main/features/visit_home_spec.rb
require 'features_helper'

RSpec.describe 'Visit home' do
  it 'is successful' do
    visit '/'

    expect(page).to have_content('Bookshelf')
  end
end
```

Hanami is ready for a Behavior Driven Development (BDD) workflow out of the box, but **it is in no way bound to any particular testing framework**.
It does not come with any special testing integrations or libraries, so if you know RSpec (or Minitest), there's nothing new to learn there.

We have to migrate our schema in the test database by running:

```shell
$ HANAMI_ENV=test bundle exec hanami db prepare
```

The `HANAMI_ENV` at the beginning is an environment variable, which tells Hanami to use the `test` environment.
This is necessary here because the default is `HANAMI_ENV=development`.

<p class="notice">
If you have trouble, your <tt>DATABASE_URL</tt> is defined in <tt>.env.test</tt>
</p>


### Following a Request

Now we have a test; we can see it fail:

```shell
$ bundle exec rake
F.

Failures:

  1) Visit home is successful
     Failure/Error: expect(page).to have_content('Bookshelf')
       expected to find text "Bookshelf" in "404 - Not Found"
     # ./spec/suite/main/features/visit_home_spec.rb:4:in `block (2 levels) in <top (required)>'

Finished in 0.02604 seconds (files took 1.14 seconds to load)
2 examples, 1 failure

Failed examples:

rspec ./spec/suite/main/features/visit_home_spec.rb:4 # Visit home is successful
```

Now let's make it pass.
We'll add the code required to make this test pass, step-by-step.

The first thing we need to add is a route:

```ruby
# config/routes.rb

require "hanami/application/routes"

module Bookshelf
  class Routes < Hanami::Application::Routes
    define do
      slice :main, at: "/" do
        root to: "home.show"
      end
    end
  end
end
```

We pointed our app's root URL to the `index` action of the `home` controller (see the [routing guide](/routing/overview) for more information).

If we run our tests, we'll get a `Hanami::Routing::EndpointNotFound` error.

That makes sense because we need to create the `home#index` action.

```ruby
# slices/main/actions/home/index.rb
module Main
  module Actions
    module Home
      class Index < Action
        def handle(request, response)
        end
      end
    end
  end
end
```

This is an empty action that doesn't do anything special.
Each action in Hanami is defined by [a single class](https://en.wikipedia.org/wiki/Single_responsibility_principle), which makes it simple to test.
Moreover, each action has a corresponding view, which is also defined by its class.
This one needs to be added in order to complete the request.

```ruby
# slices/main/views/home/index.rb
module Main
  module Views
    module Home
      class Index < View::Base
      end
    end
  end
end
```

It's also empty and doesn't do anything special.
Its only responsibility is to render a template, which is what views do by default.

This template is what we need to add, to make our tests pass.
All we need to do is add the bookshelf heading.

```erb
# slices/main/templates/home/index.html.erb
<h1>Bookshelf</h1>
```

Now let's run our test suite again.

```shell
$ bundle exec rake

Finished in 0.01394 seconds (files took 1.03 seconds to load)
2 examples, 0 failures
```

This means all our tests pass!

## Generating New Actions

Let's use our new knowledge about Hanami __routes__, __actions__, __views__, and __templates__.

The purpose of our sample Bookshelf project is to manage books.

We'll store books in our database and let the user manage them with our project.

Our first step is to list out all the books we know about.

Let's write a new feature test describing what we want to achieve:

```ruby
# spec/suite/main/features/list_books_spec.rb
RSpec.describe "List books", :web do
  it "displays each book on the page" do
    visit "/books"

    within "#books" do
      expect(page).to have_css(".book", count: 2)
    end
  end
end
```

The first thing to note is `:web` in the first line of the spec:
this tells our spec to use our 'web' settings,
i.e. that it uses a configured Capybara, including its `visit` method.
You can see that defined in `spec/support/web.rb`.

This test means that when we go to [/books](http://localhost:9292/books),
we'll see two HTML elements that have class `book`,
and both will be inside of an HTML element that has an id of `books`.

Our test suite shows 1 failure: `Unable to find visible css "#books"`.

Not only are we missing that element,
we don't even have a page to put that element on!

Let's create a new action and a new route to fix that.

### Hanami Generators

<p class="alpha">
  We don't have generators for Hanami 2 alpha yet.</br><br/>
  Instead of running a command that doesn't work,
  you can get the <em>already completed</em> version of the files with:</br><br/>
  <code>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/slices/main/actions/books/index.rb -P slices/main/actions/books/</br>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/spec/main/actions/books/index_spec.rb -P spec/suite/main/actions/books/</br>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/slices/main/views/books/index.rb -P slices/main/views/books/</br>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/spec/main/views/books/index_spec.rb -P spec/suite/main/views/books/</br>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/slices/main/templates/books/index.html.erb -P slices/main/templates/books/</br>
  </code>
  You'll need to manually edit the `config/routes.rb` file to make it look like the one below.</br></br>
  Read the section and pick it up again at the 'Layouts' section.
</p>

Hanami ships with a number of **generators**, which are tools that write some code for you.

In our terminal, let's run:

```shell
$ bundle exec hanami generate action main books#index
```

<p class="notice">
  If you're using ZSH and that doesn't work
  (with an error like <tt>zsh: no matches found: books#index</tt>),
  Hanami lets us write this instead: <tt>hanami generate action main books/index</tt>
</p>

This does a lot for us:

- Creating an action at `slices/main/controllers/books/index.rb` (and spec for it),
- Creating a view at `slices/main/views/books/index.rb` (and a spec for it),
- Creating a template at `slices/main/templates/books/index.html.erb`.

_(If you're confused by 'action' vs. 'controller':
Hanami only has `action` classes, so a controller is just a module to group several related actions together.)_

These files are all pretty much empty.
They have some basic code in there, so Hanami knows how to use the class.
Thankfully we don't have to manually create those five files,
with that specific code in them.

The generator also adds a new route for us in the routes file (`config/routes.rb`).

```ruby
# frozen_string_literal: true

require "hanami/application/routes"

module Bookshelf
  class Routes < Hanami::Application::Routes
    define do
      slice :main, at: "/" do
        root to: "home.show"

        get "/books", to: "books.index" # This was added by the generator
      end
    end
  end
end
```

To make our tests pass,
we need to edit our newly generated template file in `slices/main/templates/books/index.html.erb`:

```html
<h1>Bookshelf</h1>
<h2>All books</h2>

<div id="books">
  <div class="book">
    <h3>Patterns of Enterprise Application Architecture</h3>
    <p>by <strong>Martin Fowler</strong></p>
  </div>

  <div class="book">
    <h3>Test Driven Development</h3>
    <p>by <strong>Kent Beck</strong></p>
  </div>
</div>
```

Now our tests pass!

We've used a generator to create a new end-point (page) in our application.

But, we've started to repeat ourselves.

In both our `books/index.html.erb` template,
and our `home/index.html.erb` template from above,
we have `<h1>Bookshelf</h1>`.

This is not a huge deal, but in a real application,
we'll likely have a logo or common navigation shared across all of the pages in our `slice`.

Let's fix that repetition, to show how that works.

### Layouts

<p class="alpha">
  The template uses `slim` as the template rending engine, but this guide uses ERB.<br/><br/>
  So, delete the `slices/main/templates/application.html.slim` file and create the `erb` layout file below.
</p>

To avoid repeating ourselves in every single template, we can modify our layout template.
Let's edit `slices/main/templates/application.html.erb` to look like this:

```rhtml
<!DOCTYPE html>
<html>
  <head>
    <title>Web</title>
    <%= favicon %>
  </head>
  <body>
    <h1>Bookshelf</h1>
    <%= yield %>
  </body>
</html>
```

And remove the `<h1>Bookshelf</h1>` line from the other templates (`slices/main/templates/home/index.html.erb`, `slices/main/web/templates/books/index.html.erb`),
so it's not duplicated.

A **layout template** is like any other template, but it is used to wrap your regular templates.
The `yield` line is replaced with the contents of our regular template.
It's the perfect place to put our repeating headers and footers.

## Modeling Our Data With Entities
<p class="alpha">
  Hanami 2 will ship with <a href="https://rom-rb.org/">ROM</a> as its model layer.<br/><br/>
  The terminology here will corresponsingly change but the rough idea is the same.
</p>

Hard-coding books in our templates is... well... kind of cheating.
Let's add some dynamic data to our application!

We'll store books in our database and display them on our page.
To do so, we need a way to read and write to our database.
There are two types of objects that we'll use for this:

* an **entity** is a domain object (a `Book`) that is uniquely identified by its identity,
* a **repository** is what we use to persist, retrieve, and delete data for an entity, in the persistence layer.

Entities are entirely unaware of the database.
This makes them **lightweight** and **easy to test**.

Since entities are completely decoupled from the database,
we use repositories to persist the data behind a `Book`.

_(We also use repositories to turn the data from the database back into a `Book`, and delete that data, too.)_

Read more about entities and repositories in the [models guide](/models/overview).

<p class="alpha">
  Again, no generators exist, so you can run these commands, again, to get the _completed_ version of the file.<br/><br/>
  <code>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/slices/main/repositories/book_repository.rb -P slices/main/repositories/</br>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/spec/main/repositories/book_repository_spec.rb -P spec/suite/main/main/repositories/</br>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/slices/main/entities/book.rb -P slices/main/entities/</br>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/spec/main/entities/book_spec.rb -P spec/suite/main/entities/</br>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/db/migrations/20220107123456_create_books.rb -P db/migrations/
  </code>
  <br/>
  Read along and move on to the "Displaying Dynamic Data" section.
</p>

Hanami ships with a generator for models,
so let's use it to create a `Book` entity and its corresponding repository:

```shell
$ bundle exec hanami generate model main book
create  slices/main/entities/book.rb
create  slices/main/repositories/book_repository.rb
create  db/migrations/20220107123456_create_books.rb
create  spec/suite/main/entities/book_spec.rb
create  spec/suite/main/repositories/book_repository_spec.rb
```

The generator gives us an entity, a repository, and their associated test files.

It also gives a database migration.

### Migrations To Change Our Database Schema

Let's modify the generated migration to include `title` and `author` fields:

```ruby
# db/migrations/20220107123456_create_books.rb

ROM::SQL.migration do
  change do
    create_table :books do
      primary_key :id

      column :title,  String, null: false
      column :author, String, null: false

      column :created_at, DateTime, null: false
      column :updated_at, DateTime, null: false
    end
  end
end
```

Hanami provides a DSL to describe changes to our database schema. You can read more
about how migrations work in the [migrations' guide](/migrations/overview).

In this case, we define a new table with columns for each of our entities' attributes.
Let's prepare our database for the development and test environments:

```shell
$ bundle exec hanami db prepare
$ HANAMI_ENV=test bundle exec hanami db prepare
```

### Working With Entities

An entity is something really close to a plain Ruby object.
We use them to model the behavior we want from a concept (a book, in this case).
They're decoupled from _persistence_ entirely, but they're easy to persist and
retrieve as we'll soon see.

For now, we need to create a simple entity class:

```ruby
# slices/main/entities/book.rb
module Main
  module Entities
    class Book < Hanami::Entity
    end
  end
end
```

This class will generate getters and setters for each attribute we pass to initialize `params`.
We can verify it all works as expected with a unit test:

```ruby
# spec/slices/main/entities/book_spec.rb
require "spec_helper"
require_relative "../../../slices/main/entities/book" # FIXME: shouldn't be necessary

module Main
  module Entities
    RSpec.describe Book, type: :entity do
      it 'can be initialized with attributes' do
        book = Book.new(title: 'Refactoring', author: 'Martin Fowler')
        expect(book.title).to eq('Refactoring')
        expect(book.author).to eq('Martin Fowler')
      end
    end
  end
end
```

<p class="convention">
  Generally we recommend against "testing the framework" like this in a real app, but it's useful here to demonstrate how Entities work in Hanami.
</p>

### Using Repositories

Now we are ready to play around with our repository.
We can use Hanami's `console` command to launch `irb` with our application pre-loaded, so we can use our objects:

```shell
$ bundle exec hanami console
>> repository = Main::Repositories::BookRepository.new
  # => #<BookRepository relations=[:books]>
>> repository.books.to_a
  # => []
>> book = repository.create(title: 'TDD', author: 'Kent Beck')
  # => #<Bookshelf::Entities::Book id=1 title="TDD" author="Kent Beck" created_at=2022-01-07 12:00:00.12345 -0500 updated_at=2022-01-07 12:00:00.12345 -0500>
>> repository.books.by_pk(1).one
  # => #<Bookshelf::Entities::Book id=1 title="TDD" author="Kent Beck" created_at=2022-01-07 12:00:00.12345 -0500 updated_at=2022-01-07 12:00:00.12345 -0500>
```

Hanami repositories have methods to load one or more entities from our database, and to create and update existing records.
The repository is also the place where you would define new methods to implement custom queries.

To recap, we've seen how Hanami uses entities and repositories to model our data.
Entities represent our behavior, while repositories use mappings to translate our entities to and from our data store.
We can use migrations to apply changes to our database schema.

### Displaying Dynamic Data

With our new experience modeling data, we can get to work displaying dynamic data on our book listing page.
Let's adjust the feature test we created earlier:

```ruby
# spec/suite/features/list_books_spec.rb
require 'features_helper'

RSpec.describe 'List books' do
  let(:repository) { Main::Slice.container["repositories.book_repository"] }

  before do
    repository.create(title: 'Practical Object-Oriented Design in Ruby', author: 'Sandi Metz')
  end

  it 'displays each book on the page' do
    visit '/books'

    within '#books' do
      expect(page).to have_selector('.book', count: 1), 'Expected to find 1 book'
      expect(page).to have_content('Practical Object-Oriented Design in Ruby')
      expect(page).to have_content('Sandi Metz')
    end
  end
end
```

We create a single Book record in our test and then expect that the title and author are displayed on the page.
This test fails since we haven't updated our template yet,
and it still includes the hard-coded books from earlier.

Now we can change our template and remove the static HTML.
Our view needs to loop over all available records and render them.
Let's write a test to force this change in our view:

```ruby
# spec/suite/main/views/books/index_spec.rb

require "spec_helper"

RSpec.describe Main::Views::Books::Index do
  let(:view) { Main::Views::Books::Index.new }
  let(:books) { Array[] }
  let(:rendered) { view.call(books: books) }

  it "exposes #books" do
    expect(rendered.locals[:books].value).to eq(books)
  end

  describe "when there are no books" do
    it "shows a placeholder message" do
      expect(rendered.to_s).to include('<p class="placeholder">There are no books yet.</p>')
    end
  end

  describe "when there are books" do
    let(:book1) { Factory.structs[:book, title: "Refactoring", author: "Martin Fowler"] }
    let(:book2) { Factory.structs[:book, title: "Domain Driven Design", author: "Eric Evans"] }
    let(:books) { [book1, book2] }

    it "lists them all" do
      expect(rendered.to_s.scan(/class="book"/).length).to eq(2)
      expect(rendered.to_s).to include("Refactoring")
      expect(rendered.to_s).to include("Domain Driven Design")
    end

    it "hides the placeholder message" do
      expect(rendered.to_s).to_not include('<p class="placeholder">There are no books yet.</p>')
    end
  end
end
```

We specify that our index page will show a simple placeholder message when there are no books to display;
when there are books, it lists every one of them.
Note how rendering a view with some data is relatively straight-forward.
Hanami is designed around simple objects with minimal interfaces that are easy to test in isolation, yet still work great together.

Now we have 3 failing tests, but we can fix them by rewriting our template to implement these requirements:

```erb
# slices/main/templates/books/index.html.erb
<h2>All books</h2>

<% if books.any? %>
  <div id="books">
    <% books.each do |book| %>
      <div class="book">
        <h2><%= book.title %></h2>
        <p><%= book.author %></p>
      </div>
    <% end %>
  </div>
<% else %>
  <p class="placeholder">There are no books yet.</p>
<% end %>
<a href="/books/new">New book</a>
```

If we run our feature test now, we'll see it fails — because our controller
action does not [_expose_](/actions/exposures) the books to our view.
We can write a test for that change:

```ruby
# spec/suite/main/actions/books/index_spec.rb

RSpec.describe Main::Actions::Books::Index do
  let(:action) { described_class.new }
  let(:params) { Hash[] }
  let(:repository) { Main::Slice.container["repositories.book_repository"] }

  before do
    repository.books.delete
  end

  let(:book) { repository.create(title: 'TDD', author: 'Kent Beck') }

  it "is successful" do
    book
    response = action.call(params)
    expect(response.status).to eq(200)
  end

  it "exposes all books" do
    book
    response = action.call(params)
    expect(response.exposures[:books]).to eq([book])
  end
end
```

Note that we only make expectations of the result of `call`ing the action, which is the `response`.
Now we've specified that the action should end up exposing `books`, we can implement our action:

```ruby
# slices/main/actions/books/index.rb

module Main
  module Actions
    module Books
      class Index < Main::Action
        include Deps["repositories.book_repository"]

        def handle(request, response)
          response[:books] = book_repository.books.to_a
        end
      end
    end
  end
end
```

By setting a value for `:books` on our response, we are adding a `books` key to our exposures.
This means our view can access it, but we still need to tell the view to send it to the template.

```ruby
# slices/main/views/books/index.rb

module Main
  module Views
    module Books
      class Index < View::Base
        expose :books
      end
    end
  end
end
```
That's enough to make all our tests pass again!

```shell
$ bundle exec rake
......

Finished in 0.03745 seconds (files took 1.34 seconds to load)
10 examples, 0 failures
```

## Building Forms To Create Records

One of the last remaining steps is to make it possible to add new books to the system.
The plan is simple: we build a page with a form to enter details.

When the user submits the form, we build a new entity, save it, and redirect the user back to the book listing.
Here's that story expressed in a test:

```ruby
# spec/suite/main/features/add_book_spec.rb
require 'features_helper'

RSpec.describe "Add a book", :db do
  it "can create a new book" do
    visit "/books/new"

    within "form#book-form" do
      fill_in "Title",  with: "New book"
      fill_in "Author", with: "Some author"

      click_button "Create"
    end

    expect(page).to have_current_path("/books")
    expect(page).to have_content("New book")
  end

  it "displays list of errors when params contains errors" do
    visit "/books/new"

    within "form#book-form" do
      click_button "Create"
    end

    expect(page).to have_current_path("/books")

    expect(page).to have_content("There was a problem with your submission")
    expect(page).to have_content("Title must be filled")
    expect(page).to have_content("Author must be filled")
  end
end
```

### Laying The Foundations For A Form

<p class="alpha">
  In the few minutes since you started reading this, we still haven't added generators.<br/>
  In the meantime, you can grab what <em>would</em> be generated with these commands:<br/>
  <code>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/slices/main/actions/books/new.rb -P slices/main/actions/books/<br/>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/spec/main/actions/books/new_spec.rb -P spec/suite/main/actions/books/<br/>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/slices/main/views/books/new.rb -P slices/main/views/books/<br/>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/spec/main/views/books/new_spec.rb -P spec/suite/main/views/books/<br/>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/slices/main/web/templates/books/new.html.erb -P slices/main/web/templates/books/
  </code>
  <br/>
  But you'll need to manually edit `config/routes.rb` to add the route listed below.<br/>
  Also note that these are the _completed_ versions of those files. So, they'll be 'ahead' of the instructions below.<br/>
  Thanks for your patience. (If you weren't patient, you wouldn't be reading an alpha guide. 😉)
</p>

By now, we should be familiar with the working of actions, views, and templates.

We'll speed things up a little, so we can quickly get to the good parts.
First, create a new action for our "New Book" page:

```shell
$ bundle exec hanami generate action main books#new
```

This adds a new route to our app:

```ruby
# config/routes.rb
  ...
    get '/books/new', to: 'books.new'
  ...
```

The interesting bit will be our new template, because we'll be using Hanami's form builder to construct a HTML form around our `Book` entity.

### Using Form Helpers

<p class="alpha">
  Form helpers are still a work-in-progress for Hanami 2 alpha.<br/>
  If you look at your Gemfile, you'll see that we're using a special branch for Hanami 2 view compatibility.<br/>
  The reason for this is that Hanami's view layer is **entirely** new. In fact, it used to be named `dry-view`.<br/>
  This means that this area is rougher than the rest. Bear with us! 🐻<br/>
</p>

Let's use [form helpers](/helpers/forms) to build this form in `slices/main/templates/books/new.html.erb`:

```erb
# slices/main/templates/books/new.html.erb
<h2>Add book</h2>

<%=
  form_for(:book, "/books", params: params) do
    div class: 'input' do
      label      :title
      text_field :title
    end

    div class: 'input' do
      label      :author
      text_field :author
    end

    div class: 'controls' do
      submit 'Create Book'
    end
  end
%>
```

We've added `<label>` tags for our form fields, and wrapped each field in a
container `<div>` using Hanami's [HTML builder helper](/helpers/html5).

### Submitting Our Form

<p class="alpha">
  You know the drill by now! Grab these files with:<br/>
  <code>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/slices/main/actions/books/create.rb -P slices/main/actions/books/<br/>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/spec/main/actions/books/create_spec.rb -P spec/suite/main/actions/books/<br/>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/slices/main/views/books/create.rb -P slices/main/views/books/<br/>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/spec/main/views/books/create_spec.rb -P spec/suite/main/views/books/<br/>
    $ wget https://raw.githubusercontent.com/hanami/bookshelf/work-in-progress/upgrade-to-hanami-2/slices/main/web/templates/books/create.html.erb -P slices/main/web/templates/books/
  </code>
</p>
To submit our form, we need yet another action.
Let's create a `Books::Create` action:

```shell
$ bundle exec hanami generate action main books#create
```

This adds a new route to our app:

```ruby
# config/routes.rb
  ...
    post '/books', to: 'books#create'
  ...
```

### Implementing Create Action

Our `books#create` action needs to do two things.
Let's express them as unit tests:

```ruby
# spec/suite/main/actions/books/create_spec.rb

RSpec.describe Main::Actions::Books::Create, type: :action do
  let(:action) { Main::Actions::Books::Create.new(book_repository: repository) }
  let(:repository) { BookRepository.new }

  describe "with valid params" do
    it "creates a new book" do
      action.call(params)
      book = repository.last

      expect(book.id).to_not be_nil
    end

    it "redirects the user to the books listing" do
      response = action.call(params)

      expect(response[0]).to eq(302)
      expect(response[1]["Location"]).to eq("/books")
    end
  end
end
```

Making these tests pass is simple enough.

We've already seen how we can write entities to our database, and we can use `redirect_to` to implement our redirection:

```ruby
# slices/main/actions/books/create.rb
module Main
  module Actions
    module Books
      class Create < Action
        def handle(request, response)
          book_repository.create(request.params[:book])

          response.redirect_to routes.path(:books)
        end
      end
    end
  end
end
```

This minimal implementation should suffice to make our tests pass.

```shell
$ bundle exec rake
........

Finished in 0.07168 seconds (files took 1.4 seconds to load)
16 examples, 0 failures
```

Congratulations!
We've created a simple web app action to add a book to a database.

### Securing Our Form With Validations

Hold your horses! We need some extra measures to build a truly robust form.
Imagine what would happen if the user were to submit the form without entering any values?

We could fill our database with bad data or see an exception for data integrity violations.
We clearly need a way of keeping invalid data out of our system!

To express our validations in a test, we need to wonder: what _would_ happen if our validations failed?
One option would be to re-render the `books#new` form, so we can give our users another shot at completing it correctly.
Let's specify this behaviour as unit tests:

```ruby
# spec/suite/main/actions/books/create_spec.rb

RSpec.describe Web::Controllers::Books::Create, type: :action do
  let(:action) { described_class.new(book_repository: repository) }
  let(:repository) { instance_double(Main::Repositories::BookRepository) }

  describe "with valid params" do
    let(:params) { {book: {title: "Confident Ruby", author: "Avdi Grimm"}} }

    it "calls `create` on repository, redirects to books listing page" do
      allow(repository).to receive(:create).with(params[:book])
      response = action.call(params)

      expect(repository).to receive(:create).with(params[:book])
      expect(response.status).to eq(302)
      expect(response.headers["Location"]).to eq("/books")
    end

    it 'creates a new book' do
      action.call(params)
      book = repository.last

      expect(book.id).to_not be_nil
      expect(book.title).to eq(params.dig(:book, :title))
    end
  end

  describe "with invalid params" do
    let(:params) { {book: {}} }

    it "re-renders the books#new view" do
      response = action.call(params)
      expect(response.status).to eq(422)
    end

    it "sets errors attribute accordingly" do
      response = action.call(params)
      errors = response.request.params.errors

      expect(errors.dig(:book, :title)).to eq(["is missing"])
      expect(errors.dig(:book, :author)).to eq(["is missing"])
    end
  end
end
```

<p class="alpha">
  Notice that we also leverage dependency injection here.<br/>
  We strongly encourage it in Hanami applications. We'll add a section later about it later 🙂<br/>
</p>

Now our tests specify two alternative scenarios: our original happy path, and a new scenario in which validations fail.
To make our tests pass, we need to implement validations.

Although you can add validation rules to the entity,
Hanami also allows you to define validation rules as close to the source of the input as possible, i.e., the action.
Hanami controller actions can use the `params` class method to define acceptable incoming parameters.

This approach both explicitly declares which `params` are allowed
(others are discarded to prevent mass-assignment vulnerabilities from untrusted user input)
_and_ it adds rules to define which values are acceptable — in this case,
we've specified that the nested attributes for a book's title and author should be non-empty strings.

With our validations in place, we can limit our entity creation and redirection to cases where the incoming `params` are valid:

```ruby
# slices/main/actions/books/create.rb
# frozen_string_literal: true

module Main
  module Actions
    module Books
      class Create < Main::Action
        include Deps["repositories.book_repository"]

        # FIXME: Duplicated from books.new action
        params do
          required(:book).schema do
            required(:title).filled(:str?)
            required(:author).filled(:str?)
          end
        end

        def handle(request, response)
          if request.params.valid?
            book_repository.create(request.params[:book])

            response.redirect_to routes.path(:books)
          else
            response.status = 422
          end
        end
      end
    end
  end
end
```

When the `params` are valid, the Book is created, and the action redirects to a different URL.
However, when the `params` are not valid, what happens?

First, the HTTP status code is set to
[422 (Unprocessable Entity)](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#422).
Then the control will pass to the corresponding view, which needs to know which template to render.
In this case, `slices/main/templates/books/new.html.erb` will be used to render the form again.

```ruby
# slices/main/views/books/create.rb
module Main
  module Views
    module Books
      class Create < View::Base
        template 'books/new'
      end
    end
  end
end
```

This approach will work nicely because Hanami's form builder is smart enough to inspect the `params` in this action and populate the form fields with values found in the `params`.
If the user fills in only one field before submitting, they are presented with their original input, saving them the frustration of typing it again.

Run your tests again and see they are all passing again!

### Displaying Validation Errors

Rather than just showing the user the same form they entered when something has gone wrong,
we should give them a hint of what's actually expected of them.
Let's adapt our form to show a notice about invalid field values.

First, we expect a list of errors to be included in the page when `params` contains errors:

```ruby
# spec/suite/main/views/books/new_spec.rb
# frozen_string_literal: true

require "spec_helper"
require "hanami/view"

RSpec.describe Main::Views::Books::New do
  let(:view)      { Main::Views::Books::New.new }
  let(:rendered)  { view.call(params: params).to_s }
  let(:params) do
    instance_double(
      Hanami::Action::Params,
      valid?: false,
      error_messages: ["Not an acceptable book"]
    )
  end

  # FIXME: This accesses `csrf_token`, but the context can't access the `request`
  # in this test. Need to inject the request somehow, or stub `csrf_token`
  skip "displays list of errors when params contains errors" do
    expect(rendered).to include("There was a problem with your submission")
    expect(rendered).to include("Not an acceptable book")
  end
end
```

We should also update our feature spec to reflect this new behavior:

```ruby
# spec/suite/main/features/add_book_spec.rb
require "features_helper"

RSpec.describe "Add a book" do
  # Spec written earlier omitted for brevity
  # ...

  it "displays list of errors when params contains errors" do
    visit "/books/new"

    within "form#book-form" do
      click_button "Create"
    end

    expect(page).to have_current_path("/books")

    expect(page).to have_content("There was a problem with your submission")
    expect(page).to have_content("Title must be filled")
    expect(page).to have_content("Author must be filled")
  end
end
```

We now have two failing tests, but that's OK. It's simple to fix them.

In our template, we can loop over `params.error_messages` (if there are any) and display a friendly message.
Open up `slices/main/templates/books/new.html.erb` and update the top of the file, before the `form_for` call, to include this:

```diff
# top: slices/main/templates/books/new.html.erb
<h2>Add book</h2>

<% if params && !params.valid? %>
  <div class="errors">
    <h3>There was a problem with your submission</h3>
    <ul>
      <% params.error_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
    </ul>
  </div>
<% end %>

<%# ... %>
```

Run your tests again and see they are all passing again!

```shell
$ bundle exec rake
........
Finished in 0.07811 seconds (files took 1.35 seconds to load)
18 examples, 0 failures
```

### Improving Our Use Of The Router

<p class="alpha">
  This whole section doesn't work in Hanami 2 alpha yet either 😅<br/>
  It will be completed before launch, but read over it so you can know what to expect.
</p>

The last improvement we are going to make is in the use of our router.
Let's open up the routes file application and read it:

```ruby
# config/routes.rb
module Bookshelf
  class Routes < Hanami::Application::Routes
    define do
      slice :main, at: "/" do
        post '/books',    to: 'books#create'
        get '/books/new', to: 'books#new'
        get '/books',     to: 'books#index'
        root              to: 'home#index'
      end
    end
  end
end
```

Hanami provides a convenient helper method to build these REST-style routes that we can use to simplify our router a bit:

```ruby
# config/routes.rb
module Bookshelf
  class Routes < Hanami::Application::Routes
    define do
      slice :main, at: "/" do
        root to: 'home#index'
        resources :books, only: [:index, :new, :create]
      end
    end
  end
end
```

To get a sense of what routes are defined, now we've made this change, you can
use the special command-line task `routes` to inspect the end result:

```shell
$ bundle exec hanami routes
     Name Method     Path                           Action

     root GET, HEAD  /                              Web::Controllers::Home::Index
    books GET, HEAD  /books                         Web::Controllers::Books::Index
 new_book GET, HEAD  /books/new                     Web::Controllers::Books::New
    books POST       /books                         Web::Controllers::Books::Create
```

The output for `hanami routes` shows you the name of the defined helper method (you can suffix this name with `_path` or `_url` and call it on the `routes` helper), the allowed HTTP method, the path and finally the controller action that will be used to handle the request.

Now we've applied the `resources` helper method; we can take advantage of the named route methods.
Remember how we built our form using `form_for`?

```erb
# slices/main/templates/books/new.html.erb
<h2>Add book</h2>

<%# ... %>

<%=
  form_for :book, '/books' do
    # ...
  end
%>
```

It's silly to include a hard-coded path in our template when our router is already perfectly aware of which route to point the form to.
We can use the `routes` helper method that is available in our views and actions to access route-specific helper methods:

```erb
# slices/main/templates/books/new.html.erb
<h2>Add book</h2>

<%# ... %>

<%=
  form_for :book, routes.path(:books) do
    # ...
  end
%>
```

We can make a similar change in `slices/main/controllers/books/create.rb`:

```ruby
...
redirect_to routes.books_path
...
```

and in `slices/main/templates/books/index.html.erb`:

```erb
...
<a href="<%= routes.path(:books) %>/new">New book</a>
...
```

You could also extend this to the specs if you'd like,
but changing the path can affect users so you may want to repeat yourself there
(to make a potentially breaking change for users harder to implement).

## Wrapping Up

**Congratulations on completing your first Hanami project!**
<p class="alpha">
  Thanks for checking out this Hanami 2 alpha Getting Started guide! We sincerely appreciate it.<br/>
  Please <a href="https://github.com/hanami/guides">open a PR or Issue</a> for any mistakes you find!
</p>

Let's review what we've done: we've traced requests through Hanami's major frameworks to understand how they relate to each other; we've seen how we can model our domain using entities and repositories; we've seen solutions for building forms, maintaining our database schema, and validating user input.

We've come a long way, but there's still plenty more to explore.
Explore the [other guides](/), the [Hanami API documentation](https://docs.hanamirb.org), read the [source code](https://github.com/hanami) and follow the [blog](http://hanamirb.org/blog).

**Above all, enjoy building amazing things!**