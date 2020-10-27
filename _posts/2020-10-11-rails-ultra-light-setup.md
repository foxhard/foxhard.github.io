---
layout: post
title:  "Starting your Rails project with an ultra-light setup"
date:   2020-10-11 15:50:00
categories: [coding]
tags: [ruby, rails]
---

![rails](/static/img/posts/2020-10-11-rails-ultra-light-setup/rails.png)

Suppose that we need to create a Rails project that will use the classic ERB views and PostgreSQL. One way to start with is simply by running the following command to create a new Rails project called **my-project**:

I'm using **Ruby 2.7.1** and **Rails 6**

`rails new my-project --database=postgresql`

That command will generate the folder **my-project**, and inside it we will find the **Gemfile** where are defined all of the gems that will be used by our application

This is the content of the generated Gemfile

*I always prefer to delete the generated comments to get a clearer view of the gems*

```ruby
source 'https://rubygems.org'

ruby '2.7.1'

gem 'rails', '~> 6.0.3', '>= 6.0.3.4'
gem 'pg', '>= 0.18', '< 2.0'
gem 'puma', '~> 4.1'
gem 'sass-rails', '>= 6'
gem 'webpacker', '~> 4.0'
gem 'turbolinks', '~> 5'
gem 'jbuilder', '~> 2.7'
gem 'bootsnap', '>= 1.4.2', require: false

group :development, :test do
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
end

group :development do
  gem 'web-console', '>= 3.3.0'
  gem 'listen', '~> 3.2'
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
end

group :test do
  gem 'capybara', '>= 2.15'
  gem 'selenium-webdriver'
  gem 'webdrivers'
end

gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

Those are the default gems for Rails 6 installed after ran the last command. For the case of our application, we are not going to need all of them ([YAGNI](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it)), so lets clean up the Gemfile a little bit 

We are going to add some options more to our `rails new` command. You can see the full list of options by running `rails new -h` 

*Remember to delete the last generated folder before generate it again. To do it, you can use this command `sudo rm -r my-project`*

```zsh
rails new my-project --database=postgresql --skip-keeps --skip-action-mailer --skip-action-mailbox --skip-action-text --skip-active-storage --skip-action-cable --skip-puma --skip-test --skip-bundle --skip-webpack-install
```

**Explanation of the options**

- `--skip-keeps` **why?** This avoids adding empty folders to your git repository (I want not commit empty files in my repository)
- `--skip-action-mailer` **why?** I don't need this stuff to start with my project
- `--skip-action-mailbox` **why?** I don't need this stuff to start with my project 
- `--skip-action-text` **why?** I don't need this stuff to start with my project
- `--skip-active-storage` **why?** I don't need this stuff to start with my project
- `--skip-action-cable` **why?** I don't need this stuff to start with my project
- `--skip-puma` **why?** Puma web server is suitable for Production environment, but for development I can use the default WebBrick web server
- `--skip-test` **why?** I'm going to use RSpec, so I don't need the default Minitest stuff
- `--skip-bundle` **why?** I'm going to make some changes to my **Gemfile** then I will manually run `bundle install` command
- `--skip-webpack-install` **why?** I'm going to manually run the command `rails webpacker:install` after the changes on the Gemfile are done

This is the resulting Gemfile after run the last command
```ruby
source 'https://rubygems.org'

ruby '2.7.1'

gem 'rails', '~> 6.0.3', '>= 6.0.3.4'
gem 'pg', '>= 0.18', '< 2.0'
gem 'sass-rails', '>= 6'
gem 'webpacker', '~> 4.0'
gem 'turbolinks', '~> 5'
gem 'jbuilder', '~> 2.7'
gem 'bootsnap', '>= 1.4.2', require: false

group :development, :test do
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
end

group :development do
  gem 'web-console', '>= 3.3.0'
  gem 'listen', '~> 3.2'
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
end

gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

**Cleaning more the Gemfile**

Although you have already cleaned the Gemfile by using the `rails new` command options, you can even clean it more by removing unneeded gems manually. Let's do that

**Gems that we can keep**
- `rails` and `pg` gems **why** These two gems are basics to develop the application with Rails and PostgreSQL
- `sass-rails` and `webpacker` gems **why** These two gems are needed to Sprokets (default CSS bundler) and WebPack (default JS bundler)
- `turbolinks` gem **why** This gem is needed to speed up navigation between pages
- `bootsnap` gem **why** This gem is needed to optimization loading of the application
- `byebug` and `web-console` gems **why** These two gems are useful to debugging the application
- `spring`, `spring-watcher-listen`, and `listen` gems **why** These three gems work together to speed up the development by caching some common operations like running the rails console, tests execution,  and so on

**Gems that we can remove**
- `tznfo-data` **why** This gem is [not necessary on unix-based systems](https://github.com/tzinfo/tzinfo-data/issues/12) (Mac or Ubuntu), so if you are using Mac or Ubuntu like I, then you can remove it
- `jbuilder` **why** I'm not going to work with JSON data (at least not for now) which is commonly used in API development

This is the final Gemfile

```ruby
source 'https://rubygems.org'

ruby '2.7.1'

gem 'rails', '~> 6.0.3', '>= 6.0.3.4'
gem 'pg', '>= 0.18', '< 2.0'
gem 'sass-rails', '>= 6'
gem 'webpacker', '~> 4.0'
gem 'turbolinks', '~> 5'
gem 'bootsnap', '>= 1.4.2', require: false

group :development, :test do
  gem 'byebug'
end

group :development do
  gem 'web-console', '>= 3.3.0'
  gem 'listen', '~> 3.2'
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
end
```

Note that, I have removed the platform part of `byebug`. Since the default **platform** on unix-based systems is **ruby** and I'm not going to developing on Windows, the platform parameter is not necessary

One downside of this approach is that everytime you want to start with this lighweight setup like I have shown you here, you will have to execute the same long command every time
```zsh
rails new my-light-project --database=postgresql --skip-keeps --skip-action-mailer --skip-action-mailbox --skip-action-text --skip-active-storage --skip-action-cable --skip-puma --skip-test --skip-bundle --skip-webpack-install
```

There are shortcuts to some flags that you could use to reduce a little bit this long command:
**rails new command option shortcuts**
- `-d postgresql` instead of `--database=postgresql`
- `-M` instead of `--skip-action-mailer`
- `-C` instead of `--skip-action-cable`
- `-P` instead of `--skip-puma`
- `-T` instead of `--skip-test`
- `-B` instead of `--skip-bundle`

So, the command will look like this:

```zsh
rails new my-light-project -d postgresql --skip-keeps -M --skip-action-mailbox --skip-action-text --skip-active-storage -C -P -T -B --skip-webpack-install
```

Ok, that is quite better, but it is still very long. So, the better way is to move that long command to the `.railsrc` file (You can take a look on mine [here](https://github.com/foxhard/dotfiles/blob/master/rails/.railsrc))

Just put the flags in the `.railsrc` file like this
```zsh
--database=postgresql
--skip-keeps
--skip-action-mailer
--skip-action-mailbox
--skip-action-text
--skip-active-storage
--skip-puma
--skip-action-cable
--skip-test
--skip-bundle
--skip-webpack-install
```

Once you have moved the flags to your `.railsrc` file, you can run only this command to create your rails project `rails new my-project` by applying the changes based on the options defined in the `.railsrc` file

## Summary of all steps to create a lightweight project
1. Create your `.railsrc` file with the needed options
2. Run `rails new my-project` 
3. Edit your Gemfile and remove not necessary stuff (as explained lines above)
4. Run `bundle install`
5. Run `rails webpacker:install`
6. Run `rails db:setup`
7. Run `rails s`

Note that this strategy to clean up your Gemfile and got a lightweight project setup is though to a new project with a very basic stuff that will use server-side rendering

The final recommendation here would be that always starting with the simplest stuff and grow up from that instead of starting with a lot of unknown stuff that you don't need right now and likely you are going to not need it

![rails](/static/img/posts/2020-10-11-rails-ultra-light-setup/my-project-running.png)
