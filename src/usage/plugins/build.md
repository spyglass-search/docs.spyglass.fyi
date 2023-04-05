# Building Plugins

Currently plugins are a part of the spyglass system and require the same build environment as spyglass. See [Spyglass Build Setup]("./../../../build.md") for information on how to setup your environment. 

The Spyglass makefile can be used to build plugins found in the plugin directory.  Before running a build verify your makefile is properly configured

> Fill in `YOUR_PLUGIN_FOLDER_NAME` and the folder to your `spyglass-dev` folder

##### Windows

```makefile
# Copy the Makefile.dev.template over to Makefile.dev to setup dev related
# variables.
-include Makefile.dev

.PHONY: build-backend build-client build-plugins-dev build-plugins-release \
    build-styles build-release check clippy fmt test test-with-ignored \
    setup-dev setup-dev-linux run-client-dev

TARGET_ARCH := $(shell rustc -Vv | grep host | awk '{print $$2 " "}')

PLUGINS := YOUR_PLUGIN_FOLDER_NAME

# On Windows the dev folder is typically found here
PLUGINS_DEV_FOLDER := C:\\Users\\<YOUR_USER>\\AppData\\Roaming\\athlabs\\spyglass-dev\\data
```

##### Mac 

```makefile
# Copy the Makefile.dev.template over to Makefile.dev to setup dev related
# variables.
-include Makefile.dev

.PHONY: build-backend build-client build-plugins-dev build-plugins-release \
    build-styles build-release check clippy fmt test test-with-ignored \
    setup-dev setup-dev-linux run-client-dev

TARGET_ARCH := $(shell rustc -Vv | grep host | awk '{print $$2 " "}')

PLUGINS := YOUR_PLUGIN_FOLDER_NAME

# On Mac the dev folder is typically found here
PLUGINS_DEV_FOLDER := /Users/<YOUR_USER>/Library/Application\ Support/com.athlabs.spyglass-dev/
```

> To build run the following command

``` bash
$> make build-plugins-dev
```

This command will build the spyglass plugin and copy it to the supplied PLUGINS_DEV_FOLDER. For more information on how the plugin is installed into spyglass see [Install Plugins](./install.md).


