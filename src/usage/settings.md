# Settings

The `settings.ron` file can be found by "Show Settings folder". If there is no
file found in their directory on startup, a default one will be created.

``` rust
(
    // The max number of pages to index per domain
    domain_crawl_limit: Finite(1000),
    // The max number of crawlers per domain
    inflight_domain_limit: Finite(2),
    // The max number of crawlers in total
    inflight_crawl_limit: Finite(10),
    // Not used... yet!
    run_wizard: false,
    // Not used... yet!
    allow_list: [],
    // Domains to completely ignore, regardless of the lenses you have installed.
    block_list: [
      "web.archive.org",
      "w3schools.com"
    ],
    // Shortcut to launch the search bar
    shortcut: "CmdOrCtrl+Shift+/",
    // Where to store your index and index metadata
    // The exact default location is dependent on your OS
    //
    // - NOTE: If you're updating this for Windows, the path needs to use double backward slashes
    //   like so: "E:\\spyglass\\data"
    // - Linux & macOS uses paths like below
    //
    data_directory: "/Users/<username>/Library/Application Support/com.athlabs.spyglass",
    // By default, Spyglass will only crawl things as specified in your lenses. If you want
    // to follow links without regard to those rules, set this to true.
    crawl_external_links: false,
)
```

### Updating the Shortcut

To update the shortcut combine the following modifiers w/ an appropriate
[keycode](https://docs.rs/tao/0.8.3/tao/keyboard/enum.KeyCode.html) combining each key with a "+".

Supported Modifiers:

* "Option" / "Alt"
* "Control" / "Ctrl"
* "Command" / "Cmd" / "Super"
* "Shift"
* "CmdOrCtrl"

Examples:

* "CmdOrCtrl+/" => Launches the app w/ `Cmd` or `Ctrl` + `/`
* "CmdOrCtrl+Shift+/" => Launches the app w/ `Cmd` or `Ctrl` + `Shift` + `/`
* "Shift+4" => Launches the app w/ `Shift` + `4`

NOTE: Shortcuts are allowed to have any number of modifiers but only a *single* key.
For example, `Shift+4` will work but not `Shift+4+2`
