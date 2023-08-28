# Building from Source

## 🔧 Prerequisites & Dependencies

Before diving into the build process, ensure you have all necessary tools and dependencies installed:
1. **make**:
   - **macOS/Linux**: Use your package manager (like [Homebrew](https://brew.sh/) or `apt-get`) to install `make`.
   - **Windows**: Either [download Make for Windows](https://gnuwin32.sourceforge.net/packages/make.htm) or use [Chocolatey](https://chocolatey.org/install) with `choco install make`.

2. **Tauri**:
   - Follow the [prerequisites for Tauri](https://tauri.app/v1/guides/getting-started/prerequisites/) to set up your environment.

3. **NPM & TailwindCSS**:
   - Spyglass utilizes [TailwindCSS](https://tailwindcss.com/) for styling. Ensure you have [NPM](https://docs.npmjs.com/getting-started) set up.

A quick check-list of things you should have installed before continuing.
At the time of writing, you should be able to see similar versions below:

``` bash
$> rustc -V
rustc 1.61.0 (fe5b13d68 2022-05-18)

$> cargo --version
cargo 1.61.0 (a028ae42f 2022-04-29)

$> npm --version
8.10.0
```
> NOTE: `$>` represents your command line prompt.

If you're on Linux, start by setting up system-specific dependencies:
``` bash
$> make setup-dev-linux
```

And finally run these commands to setup the server/client dependencies.
``` bash
$> make setup-dev
```

## 🏗️ Development Builds

When running in development, it is best to have the spyglass client & backend running
in two different terminal tabs/windows. Both will have their own logs that you'll
need to watch.

First, build & run the backend service:
``` bash
$> make run-backend-dev
```

Second, build & run the client
``` bash
$> make run-client-dev
```

If you're actively working on the theming of the application itself, it's best
to have `tailwind` in watch mode so that the CSS can be continually checked &
built.

``` bash
$> npx tailwindcss -i ./public/input.css -o ./public/main.css --watch
```

If you're working on the default plugins, setup the `PLUGINS_DEV_FOLDER` variable
inside the `Makefile` to point to the spyglass dev/debug data folder. This will be
one of the following:

| Platform | Path                                                                         |
|----------|------------------------------------------------------------------------------|
| linux    | `/home/<username>/.local/share/spyglass-dev/`                                |
| macOS    |`/Users/<username>/Library/Application Support/com.athlabs.spyglass-dev/`     |
| windows  | `C:\Users\<username>\AppData\Roaming\spyglass-dev\data`                      |


To deploy plugin changes, run the following and restart your dev spyglass instance to reload
the plugins

```
$> make build-plugins-dev
```

## 🚀 Release Builds

Run the following to build the application:

``` bash
$> make build-release
```

This will kick off a release build for your platform. Depending on your OS, this
will produce a `dmg` (macos), `msi` (windows) or `appimage` (linux) file.
