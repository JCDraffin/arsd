# About

This is a collection of modules that I've released over the years (the oldest module in here was originally written in 2006, pre-D1!) for a wide variety of purposes. Most of them stand alone, or have just one or two dependencies in here, so you don't have to download this whole repo. Feel free to email me, destructionator@gmail.com or ping me as `adam_d_ruppe` on the #d IRC channel if you want to ask me anything.

I'm always adding to it, but my policy on dependencies means you can ignore what you don't need. I am also committed to long-term support. Even the obsolete modules I haven't used for years I usually keep compiling at least, and the ones I do use I am very hesitant to break backward compatibility on. My semver increases are *very* conservative.

See the full list of (at least slightly) documented module here: http://arsd-official.dpldocs.info/arsd.html and refer to https://code.dlang.org/packages/arsd-official for the list of `dub`-enabled subpackages.

## Links

I have [a patreon](https://www.patreon.com/adam_d_ruppe) and my (almost) [weekly blog](http://dpldocs.info/this-week-in-d/) you can check out if you'd like to financially support this work or see the updates and tips I write about.

# Breaking Changelog

This only lists changes that broke things and got a major version bump. I didn't start keeping track here until 9.0.

Please note that I DO consider changes to build process to be a breaking change, but I do NOT consider symbol additions, changes to undocumented members, or the occasional non-fatal deprecation to be breaking changes. Undocumented members may be changed at any time, whereas additions and/or deprecations will be a minor version change.

## 12.0

Future release, 2024 or later.

Nothing is planned for it at this time.

## 11.0

Released: Planned for May 2023

arsd.core was added, causing a general build system break for users who download individual files:

simpledisplay.d used to depend only on color.d. It now also depends on core.d.

terminal.d and http2.d used to be stand-along. They now depend on core.d.

minigui.d now also depends on a new textlayouter.d, bringing its total dependencies from minigui.d, simpledisplay.d, color.d up to minigui.d, simpledisplay.d, color.d, core.d, and textlayouter.d

Generally speaking, I am relaxing my dependency policy somewhat to permit a little more code sharing and interoperability throughout the modules. While I will make efforts to maintain some degree of stand-alone functionality, many new features and even some old features may be changed to use the new module. As such, I reserve to right to use core.d from any module from this point forward. You should be prepared to add it to your builds using any arsd component.

Note that arsd.core may require user32.lib on Windows. This is added automatically in most cases, and is a core component so it will be there, but if you see a linker error, this might be why.

I recommend you clone the repo and use `dmd -i` to let the compiler automatically included imported modules. It really is quite nice to use! But, of course, I don't require it and will call out other required cross-module dependencies in the future too.

Also:

	* dom.d's XmlDocument no longer treats `<script>` and `<style>` tags as CDATA; that was meant to be a html-specific behavior, not applicable to generic xml.
	* game.d had significant changes, making the Game object be a manager of GameScreen objects, which use delta time interpolated renders and fixed time updates (before it was vice versa).
	* database.d got its first overhaul in a decade.
	* Support for Windows XP has been dropped (though it may still work in certain places, there's no promises since arsd.core uses some Windows Vista features without fallback.)
	* Support for older compilers has been dropped (arsd.core uses some newer druntime features). The new minimum version is likely gdc 10, the tester now runs gdc version 12. gdc 9 might still sometimes work but I'm going to be removing some of those compatibility branches soon anyway.

### Diagnostics

lld-link: error: undefined symbol: _MsgWaitForMultipleObjectsEx@20
>>> referenced by core.obj:(__D4arsd4core27CoreEventLoopImplementation7runOnceMFZv)

Indicates a missing `user32.lib`.


ACTUALLY WRONG: i need the right one
lld-link: error: undefined symbol: _D4arsd4core21AsyncOperationRequest5startMFZv
>> referenced by yourfile.obj:(_D4arsd4core21AsyncOperationRequest6__vtblZ)

Indicates a missing `core.d` in the build.


## 10.0

Released: May 2021

minigui 2.0 came out with deprecations on some event properties, moved style properties, and various other changes. See http://arsd-official.dpldocs.info/arsd.minigui.html#history for details.

database.d now considers null strings as NULL when inserting/updating. before it would consider it '' to the database. Empty strings are still ''.

## 9.0

Released: December 2020

simpledisplay's OperatingSystemFont, which is also used by terminalemulator.d (which is used by terminal.d's -version=TerminalDirectToEmulator function) would previously only load X Core Fonts. It now prefers TrueType fonts via Xft. This loads potentially different fonts and the sizes are interpreted differently, so you may need to adjust your preferences there. To restore previous behavior, prefix your font name strings with "core:".

http2.d's "connection refused" handler used to throw an exception for any pending connection. Now it instead just sets that connection to `aborted` and carries on with other ones. When you are doing a request, be sure to check `response.code`. It may be < 100 if connection refused and other errors. You should already have been checking the http response code, but now some things that were exceptions are now codes, so it is even more important to check this properly.

## Prehistory:

8.0 Released: June 2020

7.0 and 6.0 Released: March 2020 (these were changes to the terminal.d virtual methods, tag 6.0 was a mistake, i pressed it too early)

5.0 Released: January 2019

4.0 and 3.0 Released: July 2019 and June 2019, respectively. These had to do with dub subpackage configuration changes IIRC.

2.0 Released (first use of semver tagging, before this I would only push to master): March 2018

April 2016: simpledisplay and terminal renamed to arsd.simpledisplay and arsd.terminal

September 2015: simpledisplay started to depend on color.d instead of being standalone

Joined dub (tagged 1.0): June 2015

Joined github: July 2011

Started project on my website: 2008

## Credits

Thanks go to Nick Sabalausky, Trass3r, Stanislav Blinov, ketmar, maartenvd, and many others over the years for input and patches.

Several of the modules are also ports of other C code, see the comments in those files for their original authors.

# Conventions

Many http-based functions in the lib also support unix sockets as an alternative to tcp.

With cgi.d, use

	--host unix:/path/here

or, on Linux:

	--host abstract:/path/here

after compiling with `-version=embedded_httpd_thread` to serve http on the given socket. (`abstract:` does a unix socket in the Linux-specific abstract namespace).

With http2.d, use

	Uri("http://whatever_host/path?args").viaUnixSocket("/path/here")

any time you are constructing a client. Note that `navigateTo` may lose the unix socket unless you specify it again.

