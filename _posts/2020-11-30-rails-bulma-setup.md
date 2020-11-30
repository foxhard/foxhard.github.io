---
layout: post
title:  "Setup Bulma CSS framework in your Rails application"
date:   2020-11-29 21:40:00
categories: [coding]
tags: [ruby, rails, bulma]
---


![bulma-header](/static/img/posts/2020-11-29-rails-bulma-setup/bulma-header.png)

In this tutorial, I will show you how to setup the CSS framework [Bulma](https://bulma.io/) in a Rails application

**Prerequisites**

- Rails 6.x

**Create new rails project**

Go to your terminal and execute `rails new bulma-test`. You can read the guide to [Starting your Rails project with an ultra-light setup](/coding/2020/10/11/rails-ultra-light-setup.html)

Enter to folder project, run your application by executing `rails s`, and go to url `localhost:3000`

![rails](/static/img/posts/2020-11-29-rails-bulma-setup/rails-page.png)


**Setup Bulma in the project**

Install bulma package by executing `yarn add bulma`

![bulma](/static/img/posts/2020-11-29-rails-bulma-setup/bulma-instalation-completed.png)

Open the file `/app/assets/stylesheets/application.css`, replace all its content by 

```scss
@import "bulma/bulma"; 
```

Finally, rename the file to `application.scss`

Ok, the setup is done, let's try it


**Create your first rails view styled with Bulma**

Create `Home` controller and `index` view by executing this command `rails g controller Home index`. 

Open the file `app/views/home/index.html.erb` and replace its content by:

```erb
<div class="box m-6">
  <div class="content">
    <div class="title is-2 has-text-info">Hello Bulma!</div>
    <div class="button is-success">Success</div>
  </div>
</div>
```

Open the file `config/routes.rb`, and add a route to our new view

```rb
Rails.application.routes.draw do
  get 'home/index'
  root 'home#index' # <- This one
end
```

Refresh the browser and you should see this page

![bulma](/static/img/posts/2020-11-29-rails-bulma-setup/bulma-ready.png)

That's it!
