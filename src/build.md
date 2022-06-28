# Building from Source

## Dependencies

Make sure you have setup the prerequisites for [tauri](https://tauri.app/v1/guides/getting-started/prerequisites).
That should get you most of the way towards the requirements for building spyglass.

Spyglass uses [TailwindCSS]() to make styling the application simple. Make sure you
have [NPM](https://docs.npmjs.com/getting-started) installed before starting the build.

## Building the Application

If you're running on Linux, run this first:
```
make setup-dev-linux
```

And run the following to build the application:

```
make setup-dev
make build-release
```

This will kick off a release build for your platform. Depending on your OS, this
will produce a `dmg` (macos), `msi` (windows) or `appimage` (linux) file.