# How to create custom Neorg modules - part 2

In the part 1 of this tutorial, we've created our first module. Now, let's see how we can make it more generic and use a configuration value that can be customized by the user.

Our current module looks like this:

```lua
require('neorg.modules.base')

local module = neorg.modules.create('external.hello-world')

module.load = function ()
  print('Hello world!')
end

return module
```

We now want to change it so that the user can pass a name to be greeted when opening neovim.

The goal is to have something like this:

```lua
print('Hello ' .. name .. '!')
```

## Creating a configuration for our module

Creating a configuration table with default values is very easy in Neorg.

To do it, let's add this piece of code inside the `module.lua` file:

```lua
module.config.public = {
    name = 'World'
}
```

Now we've notified Neorg that our module supports a configuration variable
called `name` that has a default value of "World".

Let's update our code in the `module.load` method to implement our new configuration variable:

```lua
    print('Hello ' .. module.config.public.name .. '!')
```

Of course, we can also shorten this by assigning the public configuration to a variable:

```lua
    local conf = module.config.public
    print('Hello ' .. conf.name .. '!')
```

Now, if we update our Neorg configuration and set this variable, we will be greeted
by name when opening neovim:

```lua
    -- ...
    opts = {
        load = {
            -- ...
            ['external.hello-world'] = {
                config = {
                    name = 'Andrea'
                }
            }
        }
    }
    -- ...
```

This will result in "Hello Andrea!" being printed in the message box.

The complete code for the `module.lua` file will now look like this:

```lua
require('neorg.modules.base')

local module = neorg.modules.create('external.hello-world')

module.config.public = {
    name = 'World'
}

module.load = function ()
    local conf = module.config.public
    print('Hello ' .. conf.name .. '!')
end

return module
```

## Other options for our module

In this short tutorial, we've seen how to implement `module.config.public`. There are other similar options that we can implement in our module. Here are the most important:

### `module.config.public`

As we've seen, this can be used to create public configuration variables with default values.

### `module.config.private`

This is a place where we can store private settings that we want to use in the current module. The variables stored in this table will not be shared with anyone.

### `module.public`

This is a table that contains a set of functions (it can contain other things too) that will be shared with other modules. It can be considered as the API that our module expose.

### `module.private`

This is a table with a bunch of functions that serve the goals of the module, but are not supposed to be used by anyone else. This functions are usually utilities and other logical functions.

### `module.setup`

This function will let us require other modules in order to use their APIs.

### `module.events.subscribed`

This is a table that let us subscribe to Neorg events. These events might be a keybinding, a Neorg subcommand that was launched or an autocommand that has fired.

### `module.on_event`

This is a function that let us react to the events when they fire. It gets an `event` table as a parameter.

## Coming next

We'll use some of the functionalities shown above to create a new module that will print the Treesitter tree for the node that is under our cursor. This page will be updated with a link to it as soon as it's released.
