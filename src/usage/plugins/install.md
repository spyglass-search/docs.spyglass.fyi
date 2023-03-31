# Installing Plugins

Plugins are located in the `plugins` subfolder of the spyglass data directory. To install a plugin a folder with the same name as the plugin must be placed in the `plugins` folder and two files added to the plugin folder. The files required for a plugin are the plugin file `main.wasm` and the manifest file `manifest.ron`

The data directory (`DATA_FOLDER` in the example below) is configurable and can be found in the user settings GUI under `Data Directory`

The folder structure is as follows

```
<DATA_FOLDER>\plugins\<PLUGIN_NAME>
                            ------> main.wasm
                            ------> manifest.ron
```

##### main.wasm

The wasm file is the web assemble module that will be loaded into spyglass. 

##### manifest.ron

The manifest file is the configuration file that defines details about the plugin and the list of settings needed for the plugin.

### Install

To install a plugin simply place the `main.wasm` file and the `manifest.ron` files into a subfolder of the plugins folder. The proper structure can be seen above. 

After copying the files into the proper directory the application will need to be restarted. After restart the plugin should be listed in the `Plugins` configuration section. The plugin will need to be enabled in the GUI to be loaded. 

Note that if the plugin does not show up in the `Plugins` list there was likely an error reading the plugin configuration. Any errors reading the configuration can be found in the server log. 
