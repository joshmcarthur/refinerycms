# Multiple Resources in an Extension

This guide will show you how to:

* Create multiple tables on one single extension

__WARNING__: This only works on Refinery versions 2.0.0 and greater.

## Generating an extension

Refinery ships with an extension generator that makes adding your own functionality a breeze. It works just like the Rails scaffold generator.

```shell
$ rails generate refinery:engine singular_model_name attribute:type [attribute:type ...]
```

__TIP__: to see all the options supported by the `refinery:engine` generator just run `rails g refinery:engine`.

Here is a list of the different field types are what they give you:

```shell
  **field type**       **description**
  text                 a multiline visual editor
  resource             a link which pops open a dialog which allows the user to select an existing file or upload a new one
  image                a link which pops open a dialog which allows the user to select an existing image or upload a new one
  string and integer   a standard single line text input
```

If you remember, we told Rick that we'll give him an area to post up events he'll be at. Although he could technically create a new page in Refinery to add this content there, areas that have special functionality are much better suited as an extension.

Rick is going to want to enter the following information about each event:

* The event title
* The event date
* A photo
* A little blurb about the event.

Run this command to generate the events extension for Rick:

```shell
$ rails generate refinery:engine event title:string date:datetime photo:image blurb:text
```

__TIP__: if you want to create a model without any front-end code (i.e. code only for the administrative interface), add `--skip-frontend`.

__TIP__: if you want to use a custom namespace for your extension, add `--namespace <namespace_name>`. If you use it, you have to remember to specify this namespace for every scaffold you generate for this extension.

This results in the following:

```shell
 create vendor/extensions/events/app/controllers/refinery/admin/events_controller.rb
 create vendor/extensions/events/app/controllers/refinery/events_controller.rb
 create vendor/extensions/events/app/models/refinery/event.rb
 create vendor/extensions/events/app/views/refinery/admin/events/_actions.html.erb
 create vendor/extensions/events/app/views/refinery/admin/events/_form.html.erb
 create vendor/extensions/events/app/views/refinery/admin/events/_events.html.erb
 create vendor/extensions/events/app/views/refinery/admin/events/_records.html.erb
 create vendor/extensions/events/app/views/refinery/admin/events/_event.html.erb
 create vendor/extensions/events/app/views/refinery/admin/events/_sortable_list.html.erb
 create vendor/extensions/events/app/views/refinery/admin/events/edit.html.erb
 create vendor/extensions/events/app/views/refinery/admin/events/index.html.erb
 create vendor/extensions/events/app/views/refinery/admin/events/new.html.erb
 create vendor/extensions/events/app/views/refinery/events/index.html.erb
 create vendor/extensions/events/app/views/refinery/events/show.html.erb
 create vendor/extensions/events/config/locales/en.yml
 create vendor/extensions/events/config/locales/es.yml
 create vendor/extensions/events/config/locales/fr.yml
 create vendor/extensions/events/config/locales/lolcat.yml
 create vendor/extensions/events/config/locales/nb.yml
 create vendor/extensions/events/config/locales/nl.yml
 create vendor/extensions/events/config/routes.rb
 create vendor/extensions/events/db/migrate/20111031210430_create_events.rb
 create vendor/extensions/events/db/seeds.rb
 create vendor/extensions/events/lib/generators/refinery/events_generator.rb
 create vendor/extensions/events/lib/refinerycms-events.rb
 create vendor/extensions/events/lib/tasks/events.rake
 create vendor/extensions/events/readme.md
 create vendor/extensions/events/refinerycms-events.gemspec
 ...
------------------------
Now run:
bundle install
rails generate refinery:events
rake db:migrate
rake db:seed
------------------------
```

As the output shows, next run:

```shell
$ bundle install
$ rails generate refinery:events
$ rake db:migrate
$ rake db:seed
```

A `refinery:events` generator is created for you to install the migration to create the events table. Run all the commands provided in the terminal.

__TIP__: When new extensions are added, it's a good idea to restart your server for new changes to be loaded in.

As you can see inside your text editor, the event extension is now stored in `vendor/extensions/events/`. This is where all your files -- including your migration file -- will be placed. The folder structure of `events` is nearly identical to a normal Rails app, but has a few additions to provide Refinery functionality.

Once you have generated an extension, it's time to create another scaffold to put inside of it.

To do that, run the following command.

```shell
 $ rails g refinery:engine place name:string --extension events --namespace events
```

__TIP__: You can additionally specify `--pretend` to simulate generation, so you may inspect the outcome without actually modifying anything.

Notice the last arguments (`--extension <extension_name> --namespace <extension_name>`). This is how Refinery knows which extension to insert your new code. The `--namespace` argument is necessary because Refinery will create a namespace for your extension by default. If you don't specify one, it's the name of the first scaffold you created. In this
case, the namespace is `events`. If you look inside, for example, `vendor/extensions/events/app/controllers/refinery/events/events_controller.rb`, you will see that the opening lines look something like this:

```ruby
module Refinery
  module Events
    class EventsController < ::ApplicationController
[...]
```

The first two lines indicate that the extension is namespaced using `Refinery::Events`. Refinery will automatically add the `Refinery` for you, but you will have to manually specify the namespace.

Running this command will produce the following output:

```shell
 ...
 create vendor/extensions/events/app/controllers/admin/places_controller.rb
 create vendor/extensions/events/app/controllers/places_controller.rb
 create vendor/extensions/events/app/models/place.rb
 create vendor/extensions/events/app/views/admin/places/_actions.html.erb
 create vendor/extensions/events/app/views/admin/places/_form.html.erb
 create vendor/extensions/events/app/views/admin/places/_states.html.erb
 create vendor/extensions/events/app/views/admin/places/_records.html.erb
 create vendor/extensions/events/app/views/admin/places/_place.html.erb
 create vendor/extensions/events/app/views/admin/places/_sortable_list.html.erb
 create vendor/extensions/events/app/views/admin/places/edit.html.erb
 create vendor/extensions/events/app/views/admin/places/index.html.erb
 create vendor/extensions/events/app/views/admin/places/new.html.erb
 create vendor/extensions/events/app/views/places/index.html.erb
 create vendor/extensions/events/app/views/places/show.html.erb
 create vendor/extensions/events/config/locales/tmp/en.yml
 create vendor/extensions/events/config/locales/tmp/fr.yml
 create vendor/extensions/events/config/locales/tmp/lolcat.yml
 create vendor/extensions/events/config/locales/tmp/nb.yml
 create vendor/extensions/events/config/locales/tmp/nl.yml
 create vendor/extensions/events/config/tmp/routes.rb
 create vendor/extensions/events/db/migrate/create_places.rb
 create vendor/extensions/events/db/seeds/places.rb
 create vendor/extensions/events/features/manage_places.feature
 create vendor/extensions/events/features/step_definitions/place_steps.rb
 create vendor/extensions/events/features/support/tmp/paths.rb
 create vendor/extensions/events/lib/generators/refinerycms_places_generator.rb
 create vendor/extensions/events/lib/refinerycms-places.rb
 create vendor/extensions/events/lib/tasks/places.rake
 create vendor/extensions/events/refinerycms-places.gemspec
 create vendor/extensions/events/spec/models/place_spec.rb
 append vendor/extensions/events/lib/refinerycms-events.rb
 ...
------------------------
Now run:
bundle install
rails generate refinery:events
rake db:migrate
rake db:seed
------------------------
```

__WARNING__: If you are presented with a conflict in the`events_generator.rb` file, say no! This happens at the moment because Refinery thinks you are generating a Places extension, and this may cause all kinds of havoc if you agree to it. If you have accidentally agreed to it, you can revert that file, and check your `db/seeds.rb` file to see if you have accidentally appended an additional line reading `Refinery::Events::Engine.load_seed`.

Run the commands listed above. Notice `rails generate refinery:events`. The `rails generate refinery:events` will copy the migration files from `vendor/extensions/events/db/migrate` to `db/migrate` and will prepend a timestamp to each migration. This is so an extension that was written before your application will declare its migrations chronologically
later than the migrations you wrote for the app. For instance, if you wrote your app on Tuesday and then inserted the extension on Wednesday, you would want the timestamps on the migrations to show Wednesday rather than the date it was created -- perhaps Monday. If the extension didn't do this, when you deployed your application, it would run the migrations for your extension first, potentially failing because it couldn't find the Refinery database tables it needs to complete.

## Crudify: The Backbone of Refinery Engines

Any Refinery extension, even the built-in ones, that focuses on the standard create, read, update and delete operations are driven by `crudify`. Crudify is a highly reusable module included with Refinery that gives you all the standard CRUD actions as well as reordering, searching and paging.

Open up `vendor/extensions/events/app/controllers/refinery/events/admin/events_controller.rb` and look at its contents:

```ruby
module Refinery
  module Events
    module Admin
      class EventsController < ::Refinery::AdminController

        crudify :'refinery/events/event', :xhr_paging => true
      end
    end
  end
end
```

Most of the time, crudify's defaults are bang on, but if you need to, you can easily customise how it works.

By default `crudify` assumes your records will be sortable. But events should not be manually sortable; it makes more sense to order them by their event date. Update the contents of the file to this:

```ruby
module Refinery
  module Events
    module Admin
      class EventsController < ::Refinery::AdminController

        crudify :'refinery/events/event', :xhr_paging => true, :order => "date DESC", :sortable => false

      end
    end
  end
end
```

This will tell `crudify` to sort by our event date field and to turn off manual sorting by the user.

Finally edit
*vendor/extensions/events/app/controllers/refinery/events/events_controller.rb*
and replace the *find_all_events* method with this one:

```ruby
module Refinery
 module Events
 class EventsController < ::ApplicationController

      # code

      protected

        def find_all_events
          # Order by event date
          @events = Event.order("date DESC")
        end

        # code

    end
  end
end
```

This will sort your events by date, rather than the default, which is
its ID (a rather information-less order).
