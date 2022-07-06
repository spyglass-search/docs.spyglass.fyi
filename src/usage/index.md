# Launching & Using Spyglass

After you've successfully [installed](../install.md) Spyglass, launch the
application from where you normally find newly installed applications.

If the app has been successfully launched, you'll see a little menubar icon
like the following:

<p align="center">
    <img width="256" src="./../assets/menubar-menu.png" alt="Menubar icon & menu">
</p>

> On __Windows__, this will appear as a colorful spyglass icon in your system tray (bottom right).

> On __Ubuntu Linux__, this will appear as a color spyglass icon in the top right.


## Opening the search bar

Once launched, press **`Cmd (Ctrl) + Shift + /`** to open Spyglass. This hotkey
can be configured in your [settings](./settings.md).

If for some reason this hotkey is not working, you can launch the search bar directly
with the `Show search` menu item.


## Finding & Applying a lens

Queries prefixed with `/` will search through your installed lenses. Select the lens
you want to search through and hit `Enter` to apply it to your current search.


## Selecting and Opening a Result

As you're searching, use the arrow keys to select the result you want and hit`Enter`
to open the link in your default browser.

## Viewing crawler status & forcing a recrawl

Open the crawl status window by going into the system tray menu and clicking on
the `Show crawl status` option. This will open a window like below:

<p align="center">
    <img src="./../assets/crawl-status.png" alt="Crawler status window">
</p>

The crawl status window will show the status of all URLs that has been seen by
the system, group by their domain.

> Note: The status does not auto-update, click on the refresh icon in the right corner
> to refresh the status.

* Queued - Indicates a URL that is waiting to be crawled.
* Processing - Currently being crawled.
* Completed - Indicates a URL that has been crawled.
* Indexed - Indicates a URL that has ultimately been indexed.


### `Completed` vs `Indexed`

Often times the number of indexed documents may not match the number of completed.
This does not necessarily mean something has gone wrong. Completed indicates unique
URLs that have been crawled, but this does not always mean that it will be indexed.

For example, [https://en.wikipedia.org](https://en.wikipedia.org) &
[https://en.wikipedia.org/wiki/Main_Page](https://en.wikipedia.org/wiki/Main_Page)
would refer to two separate URLs but ultimately only represent a single indexed document
since they point to the same place.