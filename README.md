# Browserlink.vim
Browserlink is a live browser editing plugin for Vim. Formerly **brolink.vim**.
<img src='http://jaxbot.me/pics/brolinkhtml.gif'>

## Live edit CSS

Browserlink allows you to live edit CSS files, which are sent to the browser on change, without reloading or changing the state of the page.

<img src='http://jaxbot.me/pics/brolinkcss.gif'>

## Live evaluate JavaScript

Browserlink allows you to evaluate buffers or selections of JavaScript directly, or even call individual functions within the buffer, for instant feedback in the browser.

<img src='http://jaxbot.me/pics/brolinkjs.gif'>

## New: Keep in sync with Chrome Inspector

The Chrome inspector allows you to set source maps from network resources to the local filesystem. In the latest version of Browserlink, you can set

```
window.__BL_OVERRIDE_CACHE = true
```

to disable the cache breaker. After setting up Chrome as desired, enable `:set autoread` and you'll get results like this:

<img src='http://jaxbot.me/pics/vim/vim_brolink_sync.gif' alt='Browserlink.vim staying in sync with Chrome inspector'>

## How it works
Browserlink is very simple. The plugin itself hooks autocommands for file changes (and other things) to the provided functions. The functions connect through HTTP to a node.js backend, which your webpage connects also to. The entire process happens extremely fast.

## Installation and Setup
To install, either download the repo, or as I would recommend, use [Pathogen](https://github.com/tpope/vim-pathogen).

	git clone git://github.com/jaxbot/brolink.vim.git

Once installed, make sure you have Node installed, and run the server program like so:

	node brolink.js

Feel free to move (or symlink) the brolink server to a more convenient location.

Once Browserlink is running in a console, you need to include a reference to it in your web project.
Two options:

1. Add this to your page(s)
	
	<script src='http://127.0.0.1:9001/js/socket.js'></script>

2. **OR** Use GreaseMonkey or Tampermonkey to automatically embed in your local projects, e.g.


		// ==UserScript==
		// @name       Browserlink Embed
		// @namespace  http://use.i.E.your.homepage/
		// @version    0.1
		// @description  enter something useful
		// @match      http://localhost/*
		// @copyright  2012+, You
		// ==/UserScript==
		
		var src = document.createElement("script");
		src.src = "http://127.0.0.1:9001/js/socket.js";
		src.async = true;
		document.head.appendChild(src);


I prefer the latter, as it's more universal and I don't have extra development junk in my projects. But it's totally up to you.

## Usage

Once set up, Vim should now call the Node server whenever you save a .html, .js, .php, or .css file. Then just load up your web project like normal, and Vim should send signals over the websocket to reload the pages automatically. Nifty.

In addition:

	BLReloadPage

will reload the current pages

	BLReloadCSS

will reload the current stylesheets 

	BLEvaluateBuffer

will evaluate the current buffer

You can also use <leader>be to evaluate selections or buffers, <leader>br to reload, and <leader>bc to reload stylesheets manually.

	BLConsole

An experimental feature that will print out `console.log` results from the webpage into a buffer. When in console mode:

* `i` - shortcut to `:BLEval`
* `cc` - clears console buffer
* `r` - refreshes console buffer
* `<CR>` - attempts to load the highlighted trace line

	BLErrors

Load accumulated Javascript errors of the current session into the quickfix list

	BLClearErrors

Reset the error list.

If you want to get super efficient, you can hook an autocmd to when you leave insert mode (or other times) to reload, say, the stylesheets:

	au InsertLeave *.css :BLReloadCSS

This function can be easily tweaked to fit your needs/workflow, and I highly recommend you do so to maximize your utility from this plugin.

## Options

	g:bl_no_autoupdate 

If set, Browserlink won't try to reload pages/CSS when you save respective files.

	g:bl_no_eager 

If set, Browserlink won't autostart the server when a command is run and the server does not respond.

	g:bl_no_mappings 

If set, Browserlink won't map be, br, and bc commands.

	g:bl_serverpath 

Set if your server is not hosted on 127.0.0.1:9001. You will also need to change the socket.js file.

	g:bl_urlpaths

A dictionary defining mappings from URLs to filesystem paths. Set this if you want to use the
quickfix list for pages not accessed via a file://-URL.

## Notes

This is an experimental project, but it works really well for me, and I hope you enjoy it! I kept the source as simple as possible, and it's pretty easy to edit to your needs. I'm open to any suggestions, too, so let me know.

## Shameless plug

I hack around with Vim plugins, so [follow me](https://github.com/jaxbot) if you're into that kind of stuff (or just want to make my day) ;)
