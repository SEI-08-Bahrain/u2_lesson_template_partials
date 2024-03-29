<img src="https://i.imgur.com/Z5WdcWY.jpg">

# EJS Partial Templates

### Learning Objectives

| Students Will Be Able To: |
|---|
| Connect & Sync With a Remote GitHub Repo |
| Use EJS Partial Templates |

## Road Map

1. Setup 
2. What are Partial Templates?
3. Why use Partial Templates?
4. Refactor `express-todos` to Use Partials

## Setup

This lesson builds upon our existing `express-todos` project.

### Connect to a Remote GitHub Repo

So that you can sync your code with that of your instructor's if need be during this lesson or the next, let's make your current project a local Git repo and then add a **remote** that links/connects to the repo of your instructor:

1. Move into your `express-todos` project folder:
    ```
    cd ~/code/express-todos
    ```
2. Make the project a local Git repo: 
    ```
    git init
    ```
3. Add a link to the **remote** that you can sync with during the code-along from this point forward:
    ```
    git remote add origin https://git.generalassemb.ly/sei-blended-learning/express-todos-code-along.git
    ```
4. Fetch the remote's existing code - note that this only fetches the code for future use if need be and will not change the code currently in your project:
    ```
    git fetch --all
    ```
5. Open the project in VS Code:
    ```
    code .
    ```

### Syncing Your Code

The code that was just fetched is hidden within the repo but is available to sync with!

If your current `express-todos` project is not running properly, of if you just want to make sure that you start this lesson with the same code as your instructor, you can sync your code with the starter code for this lesson!

First, make sure that there are no unsaved files in your project (simply save them), then run the following command:

```
git reset --hard origin/sync-1-partials-starter
```

Note that syncing your code now, or any time in the future will result in your project's code mirroring that of the remote repo's **exactly**.  Any extra comments, code, etc. that you have made will be replaced.

## 2. What are Partial Templates?

Simply put, EJS Partial Templates, AKA "partials", are a way to render a template within another template.

Here's a simple example to demonstrate...

Consider the following "Nav" EJS template meant to be used repeatedly as desired:

```html
<!-- nav.ejs -->
<a href="/">Click for About Page</a>
```

and this "Home" EJS template:

```html
<body>
  <h1>Home Page</h1>
</body>
```

Using EJS's [include() function](https://github.com/mde/ejs#includes), we can render the "Nav" partial within the "Home" template like this:

```html
<body>
  <!-- The path is relative to the current template -->
  <%- include('./partials/nav') %>
  <h1>Home Page</h1>
</body>
```

Notice the use of the EJS "raw output" tag `<%-` instead of `<%=`. This prevents EJS from "escaping" the HTML contained in the partial. 

Failure to use `<%-` would result in the browser displaying the HTML as if it were text:

```
<a href="/">Click for About Page</a>

Home Page
```

Instead of the desired:

```html
Click for About Page

Home Page
```

## 3. Why use Partial Templates?

We use EJS Partial Templates to stay DRY, that is, reuse template code.

So far, we've had to repeat the same HTML boilerplate in every template making maintainability difficult.

Let's put an end to the lack of DRYness...

## 4. Refactor `express-todos` to Use Partials

### Organize Partials Within a `views/partials` Folder

It makes sense to organize partial templates within their own folder.

Use Terminal or VS Code's GUI to create a `partials` folder within the existing `views` folder.

### Create `header.ejs` and `footer.ejs` Templates

We'll use a `header.ejs` template to hold the `<head>`, etc. plus all of the boilerplate that we want to show at the top of every page, e.g., navigation and even the page title.

We'll use a `footer.ejs` template to hold what we want to show at the bottom of every page.  Although we don't need a "footer" for express-todos, perhaps you'll want to have a footer that displays a copyright, etc. in your project, so let's see how to set it up.

Use Terminal or VS Code's GUI to create `header.ejs` and `footer.ejs` files within the newly created `views/partials` folder.

With the following structure in place, let's add some content:

<img src="https://i.imgur.com/DdhucKs.png">

### Code `header.ejs`

Open **views/todos/show.ejs**, and cut and paste the following content into **views/partials/header.ejs**:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Express To-Do</title>
  <link rel='stylesheet' href='/stylesheets/style.css' />
</head>
<body>
  <a href="/">Home</a>
   | 
  <a href="/todos">All To-Dos</a>
```

Yes, including the navigation in header.ejs can be convenient.  In a future lesson you see how to customize the navigation according to what page is currently being viewed by the user.

Let's also add a dynamic _page title_ below the navigation:

```html
  <!-- header.ejs -->
  ...
  <a href="/todos">All To-Dos</a>
  <h1><%= title %></h1>
```

Now, we just make sure that whenever we render a page, we pass a `title` property for the page's title...

### Pass a `title` Property With Every `res.render()`

Wherever we call `res.render()` we need to ensure that we pass a `title` property otherwise we will receive an error.

We are currently calling `res.render()` from both the **index** and **show** functions within **controllers/todos.js**.  Let's update by adding a `title` property:

```js
// controllers/todos.js

function show(req, res) {
  res.render('todos/show', {
    todo: Todo.getOne(req.params.id),
    // Don't forget to add the comma above
    title: 'To-Do Details'
  });
}

function index(req, res) {
  res.render('todos/index', {
    todos: Todo.getAll(),
    title: 'All To-Dos'
  });
}
```

Finally, let's update the `title` for the Home page within **routes/index.js**:

```js
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Home Page' });
});
```

### Code `footer.ejs`

Back in **views/todos/show.ejs** let's cut and paste the following content into **views/partials/footer.ejs**:

```html
</body>
</html>
```

Again, not much here, but for your projects you may want to include a `<footer>` element like this:

```html
  <footer></footer>
</body>
</html>
```

### Rendering Partials Using `includes()`

With the **header.ejs** and **footer.ejs** partials set up, let's render them within the other templates!

#### `views/index.ejs`

Update the home page as follows:

```html
<%- include('./partials/header') %>
<%- include('./partials/footer') %>
```

Yep, all we need is the partials for our landing page.

#### `views/todos/index.ejs`

Simply provide the content you want between the two partials:

```html
<!-- Be sure the path is relative to this template -->
<%- include('../partials/header') %>

  <ul>
    <% todos.forEach(function(t) { %>
      <li>
        <a href="/todos/<%= t.id %>"><%= t.todo %></a>
          - 
        <%= t.done ? 'done' : 'not done' %>
      </li>
    <% }); %>
  </ul>

<%- include('../partials/footer') %>
```

Note the `../` instead of the `./` we used in the home page because the path is relative to that of the current template. 

#### `views/todos/show.ejs`

**show.ejs** is just missing the includes:

```html
<%- include('../partials/header') %>

  <h3><%= todo.todo %></h3>
  <h3>Complete: <%= todo.done ? 'Yes' : 'No' %></h3>

<%- include('../partials/footer') %>
```

### Test it out - congrats!
