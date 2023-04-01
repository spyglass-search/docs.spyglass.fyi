# Settings

The `settings.ron` file can be found by "Show Settings folder". If there is no
file found in their directory on startup, a default one will be created.

``` ron
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
    // Disables sending any telemetry. Currently the only telemetry is for bug/error reporting.
    disable_telemetry: false,
    // Disables launching the application when your computer first boots up.
    disable_autolaunch: false,
    filesystem_settings: (
        enable_filesystem_scanning: true,
        watched_paths: [
            "<PATH TO WATCH>",
        ],
        supported_extensions: [
            "docx",
            "html",
            "md",
            "txt",
            "ods",
            "xls",
            "xlsx",
        ],
    ),
    // Port that the search daemon runs on. Change this if you have another service
    // already running at this port number.
    port: 4664,
    user_action_settings: (
        actions: [
            (
                label: "Copy URL to Clipboard",
                status_msg: Some("Copying..."),
                action: CopyToClipboard("{{ open_url }}"),
                key_binding: "CmdOrCtrl+C",
            ),
        ],
        context_actions: [],
    ),
    audio_settings: (
        enable_audio_transcription: false,
    ),
)
```

### Updating the Shortcut

The Shortcut can be modified directly through the `User Settings` menu. The field will automatically record the keystrokes used. You can also type in the desired shortcut using the information below.

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

### Updating User Actions

Currently user actions must be configured directly in the settings configuration file. The user actions configuration allows a user to define any number of actions that can be taken on a selected search result. When a result is selected the actions menu provides the list of currently available actions.

There are currently three types of actions that can be configured. 

* **OpenApplication** \
  Open application takes two arguments the application to open and the path to pass to the application. One Windows and Linux the application is any application that is in the systems path. On Mac the application must be a valid application in the application folder. The path is the path you want to open with the application. The below example works on Windows to open the selected file in Visual Studio Code

  ```ron
  (
      label: "Open In Vs Code",
      status_msg: Some("Opening..."),
      action: OpenApplication("code", "{{ open_url }}"),
      key_binding: "CmdOrCtrl+O",
  )
  ```
* **OpenUrl** \
  Open Url works the same as OpenApplication but only takes a single argument, the url to open. This action utilizes the default system application to open the url. Note that by default pressing `enter` on a selected result executes the OpenUrl action with the {{ open_url }} argument. This custom action is useful if you want to modify the url. The below example modifies the url to work with Obsidian to open a markdown file that is part of an Obsidian vault

  ```ron
  (
      label: "Open In Vault",
      status_msg: Some("Opening..."),
      action: Open("obsidian://open?vault={{slice_path url 5 count=1}}&file={{slice_path url 6}}"),
      key_binding: "CmdOrCtrl+V",
  )
  ```
* **CopyToClipboard** \
  The copy to clipboard action is used to copy the argument to the clipboard. This allows the user to copy the url or other information about the document to the clipboard. The below example is part of the default configuration and is used to copy the documents url to the clipboard

  ```ron
  (
      label: "Copy URL to Clipboard",
      status_msg: Some("Copying..."),
      action: CopyToClipboard("{{ open_url }}"),
      key_binding: "CmdOrCtrl+C",
  )
  ```
#### Action Configuration

The action configuration as seen above is made up of four properties 

* label - This is the label shown on the action menu
* status_msg - This is the message shown in the bottom left of the search window when the action is being executed
* action - This is the action with arguments that should be executed
* key_binding - The key binding is the keyboard shortcut that will trigger the action. This allows the keyboard to be used instead of selecting the action with the mouse

As part of the action configuration, templates are typically used to define arguments. The template syntax uses the [handlebars](https://docs.rs/handlebars/latest/handlebars/) template framework. All of the default behavior defined in the [handlebars](https://docs.rs/handlebars/latest/handlebars/) documentation is supported as well as one custom function.

#### Context Action Configuration

Actions configured under the `actions` section are available for all search results. Actions defined in the `context_actions` section will only be available for results that match the context. Note that the same key binding for an action and context action can be used and the context action will override the action when triggering from a keyboard. 

To configure context actions define a context and the action definition 

```ron
(
    context: (
        has_tag: Some([
            ("type", "directory"),
        ]),
        has_tag_type: None,
        exclude_tag: None,
        exclude_tag_type: None,
        url_like: None,
    ),
    actions: [
        (
            label: "Open in VS Code",
            status_msg: None,
            action: OpenApplication("code", "{{ open_url }}"),
            key_binding: "CmdOrCtrl+Enter",
        ),
    ],
)
```

Multiple context actions can be defined and for every matching context one or more actions can be defined. The action definition follows the standard action definition. The context definition is as follows

* has_tag - Optional list of tags to match against the search result
* has_tag_type - Optional list of tag types to match against 
* exclude_tag - Optional list of tags to exclude. Any search result that has one of these tags will not have these actions available
* exclude_tag_type - Optional list of tag types. Any search result that has a tag of this type will not have these actions available
* url_like - Optional list of urls that must match the url of the search result. 

In the example above that action to open the url in VS Code would only be valid for search results that have the tag `type=directory` 

#### Template Fields

The template fields available for each entry are as follows

* doc_id - The unique id of the document
* crawl_uri - The uri used to crawl the result
* domain - The domain for the result
* title - The title of the result
* description - The description of the result
* url - The url for the result
* open_url - The url used by default to open the result, this is a normalized url for the os
* tags - The list of tags for the result
* score - The documents score
* url_schema - The schema of the url
* url_userinfo - The user info for the url if present
* url_parent - The parent of the url
* url_port - The port of the url
* url_path - The full url path as a list of strings
* url_path_length - The length of the path
* url_query - The query string for the url if present

#### Custom Function

**slice_path** custom helper function has been added to help select parts of a path. The function requires a valid url. The syntax is as follows

`{{slice_path <Template Var> <Start Index> [<End Index>] [count=<Count>] [full_uri=<true|false>]}}`

* slice_path - is the function name to execute
* \<Template Var\> - is required and is the field to access from the selected search result, typically `url`
* \<Start Index\> - is required and is the index to start from (0 indexed)
* \[\<End Index\>\] - Optional end index, if not defined and count is also not defined then the last index in the path is used
* \[count=\<Count\>\] - Optional count is used to define the number of elements to access. This is helpful when the start index is a negative number so the end index cannot be known statically
* \[full_uri=\<true\|false\>\] - Optional property used to identify if the output of slice_path should just be the path or the full uri.

Examples

url = http://test.com/a/b/c/d/e \
`{{slice_path url 1}}` \
**Output** - b/c/d/e \

url = http://test.com/a/b/c/d/e \
`{{slice_path url -1}}` \
**Output** - e \

url = http://test.com/a/b/c/d/e \
`{{slice_path url -2}}` \
**Output** - d/e \

url = http://test.com/a/b/c/d/e \
`{{slice_path url 1 3}}` \
**Output** - b/c/d \

url = http://test.com/a/b/c/d/e \
`{{slice_path url 1 -1}}` \
**Output** - b/c/d \

url = http://test.com/a/b/c/d/e \
`{{slice_path url -2 count=1}}` \
**Output** - d \

url = http://test.com/a/b/c/d/e \
`{{slice_path url -2 count=2}}` \
**Output** - d/e \

url = http://test.com/a/b/c/d/e \
`{{slice_path url 0 -1 full_uri=true}}` \
**Output** - http://test.com/a/b/c/d \

url = http://test.com/a/b/c/d/e \
`{{slice_path url 0 -2 full_uri=true}}` \
**Output** - http://test.com/a/b/c 