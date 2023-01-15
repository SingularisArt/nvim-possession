<h1 align="center">
  <br>
  <img width="250" height="250" src="https://user-images.githubusercontent.com/15387611/212337464-ef0a605f-378b-4a6f-aed2-1b6598b28348.png">
  <br>
  nvim-possession
  <br>
</h1>

<h2 align="center">
  <a href="#" onclick="return false;">
    <img alt="PR" src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat"/>
  </a>
  <a href="#" onclick="return false;">
    <img alt="lua" src="https://img.shields.io/badge/lua-%232C2D72.svg?&style=flat&logo=lua&logoColor=white"/>
  </a>
  <a href="https://github.com/gennaro-tedesco/nvim-possession/releases">
    <img alt="releases" src="https://img.shields.io/github/release/gennaro-tedesco/nvim-possession"/>
  </a>
</h2>

<h4 align="center">No-nonsense session manager</h4>

You are puzzled by neovim sessions and are not using them, are you? Start your pos-sessions journey, fear no more!

This plugin is a no-nonsense session manager built on top of [fzf-lua](https://github.com/ibhagwan/fzf-lua) (required) that makes managing sessions quick and visually appealing: dynamically browse through your existing sessions, create new ones, update and delete with a statusline component to remind you of where you are. See for yourself:

![demo](https://user-images.githubusercontent.com/15387611/211946693-7c0a8f00-4ed8-4142-a8aa-a4dc75f42841.gif)

## 🔌 Installation and quickstart

Install `nvim-possession` with your favourite plugin manager (`fzf-lua` is required) and invoke `require("nvim-possession").setup({})`; in order to avoid conflicts with your own keymaps we do not set any mappings but only expose the interfaces, which means you would need to define them yourself. The suggested quickstart configuration is, for instance

- with [lazy.nvim](https://github.com/folke/lazy.nvim)

```lua
{
    "gennaro-tedesco/nvim-possession",
    dependencies = {
        "ibhagwan/fzf-lua",
        -- OR
        "nvim-telescope/telescope.nvim",
    },
    config = true,
    init = function()
        local possession = require("nvim-possession")
        vim.keymap.set("n", "<leader>sl", function()
            possession.list()
        end)
        vim.keymap.set("n", "<leader>sn", function()
            possession.new()
        end)
        vim.keymap.set("n", "<leader>su", function()
            possession.update()
        end)
    end,
}
```

Exposed interfaces

| function                             | description                                                     | interaction                                                         |
| :----------------------------------- | :-------------------------------------------------------------- | :------------------------------------------------------------------ |
| `possession.list()`                  | list all the existing sessions. see below for more information  | `<CR>` load selected session<br>`<Ctrl-x>` delete selection session |
| `possession.new()`                   | prompt for name to create new session                           | session folder must alredy exist, return a message error otherwise  |
| `possession.update()`                | update current session (if new buffers are open)                | do nothing if no session is loaded                                  |
| `possession.status()`                | print the current session you're in                             | do nothing if no session is loaded                                  |

The `possession.list()` function lists the session via `fzf` or `telescope.nvim`. See below on how to set that up.

## 🛠 Usage and advanced configuration

As shown above the main use of the plugin is to show all existing sessions (say via `<leader>sl`) and load the selected one upon `<CR>`. Once a session is loaded a global variable is defined containing the session name (to display in a statusline - see below - or to validate which session is currently active). New sessions can also be created and updated on the fly, and they will show when you next invoke the list.

Default configurations can be found in the [config](https://github.com/gennaro-tedesco/nvim-possession/blob/main/lua/nvim-possession/config.lua) and can be overriden at will by passing them to the `setup({})` function: in particular the default location folder for sessions is `vim.fn.stdpath("data") .. "/sessions/",`. You should not need to change any of the default settings, however if you really want to do so:

```lua
require("nvim-possession").setup({
  sessions = {
    sessions_path = "...", -- folder to look for sessions, must be a valid existing path
    sessions_variable = "...", -- defines vim.g[sessions_variable] when a session is loaded
    sessions_icon = "...",
  },

  viewer = "fzf|telescope", -- which plugin you want to use to display the sessions
  dressing = true, -- enable a ui when asking for a new session name
  autoload = false, -- detect and autoload sessions in cwd

  telescope = {
    theme = "get_dropdown", -- the telescope theme you want to use
  },

  fzf_winopts = {
    -- any valid fzf-lua winopts options, for instance
    width = 0.25,
    preview = {
      horizontal = "down:40%",
    },
  },
})
```

### Autoload in cwd

If you want to automagically load sessions defined for the current working directory at startup, specify

```lua
require("nvim-possession").setup({
  autoload = true
})
```

This autoloads sessions when starting neovim without file arguments (i. e. `$ nvim `) and in case such sessions explicitly contain a reference to the current working directory (you must have `vim.go.ssop+=curdir`); this is by design as this plugin intends to be as less invasive as possible.

## 🚥 Statusline

You can call `require("nvim-possession").status()` as component in your statusline, for example with `lualine` you would have

```lua
lualine.setup({
  sections = {
    lualine_a = "...",
    lualine_b = "...",
    lualine_c = { { "filename", path = 1 }, { "require'nvim-possession'.status()" } },
  },
})
```

to display

<p align="center">
  <img width="300" height="100" src="https://user-images.githubusercontent.com/15387611/211811964-037c6233-21d6-4ee1-815c-6da068dd3595.png">
</p>

the component automatically disappears or changes if you delete the current session or switch to another one.

## Feedback

If you find this plugin useful consider awarding it a ⭐, it's a great way to give feedback! Otherwise, any additional suggestions or merge request is warmly welcome!
