# Writing Plugins

Currently plugins are written in Rust and compiled using the same build system as Spyglass. An example plugin can be found [here](https://github.com/spyglass-search/spyglass/blob/main/plugins/example-plugin/src/main.rs) 

### Folder Structure

The folder structure for the plugin in spyglass follows the outline below

```
<SPYGLASS_REPO>/plugins/<PLUGIN_NAME>/
                        -------------> .cargo
                        ---------------------> config.toml
                        -------------> src
                        ------------------> main.rs
                        ------------------> manifest.ron
                        -------------> Cargo.toml
```

##### Cargo.toml

The cargo file is a standard rust project cargo file ([Manifest Format](https://doc.rust-lang.org/cargo/reference/manifest.html)). Below is from the example plugin. Note that the spyglass-plugin dependency is required, but all other dependencies depend on the type of plugin being written.

```toml
[package]
name = "example-plugin"
version = "0.1.0"
edition = "2021"
license = "AGPL"

[[bin]]
name = "example-plugin"
path = "src/main.rs"

[dependencies]
serde_json = "1.0"
spyglass-plugin = { path = "../../crates/spyglass-plugin" }
url = "2.2"
```

##### config.toml

Required configuration file to define the build target

```toml
[build]
target = "wasm32-wasi"
```

##### manifest.ron

The manifest file defines the metadata associated with the plugin.

```ron
(
	name: "example-plugin",
	author: "spyglass-search",
	description: "Example plugin to provide an example on how to create a plugin.",
	version: "1",
	plugin_type: Lens,
	trigger: "example-plugin",
	// User settings w/ the default value, this will be added the plugin environment
	user_settings: {
	   "API_KEY": (
	        label: "Example Plugin API Key",
	        value: "",
	        form_type: Text,
	        restart_required: false,
	        help_text: Some("Example with custom string configuration")
	    ),
	    "ENABLE_API": (
	        label: "Example Plugin Enable API boolean",
			value: "",
			form_type: Bool,
			restart_required: false,
			help_text: Some("Example with custom boolean configuration")
		),
	}
)
```

**Name**: The name of the plugin\
**Author**: The developer of the plugin\
**Description**: Description shown in the GUI used to explain what the plugin does\
**Version**: Plugin version \
**Plugin_type**: Currently we only support the `Lens` plugin type\
**Trigger**: The lens trigger automatically used in search GUI\
**User_settings**: Optional user settings. This can be used to define configuration needed by the plugin. This configuration is used by the GUI to provide input fields in the `User Settings` menu. All configured values are passed to the plugin through environmental variables. \

##### main.rs

The main.rs is where the plugin code begins. The full source of an example plugin can be found [here](https://github.com/spyglass-search/spyglass/blob/main/plugins/example-plugin/src/main.rs) . Below are snippets of code from the example plugin.

```rust
use spyglass_plugin::*;

#[derive(Default)]
struct Plugin;

register_plugin!(Plugin);
```

The most basic start of a plugin is defining a struct and registering it with the system using the `register_plugin!` macro. After the plugin has been defined and registered the `Plugin` struct needs to implement the `SpyglassPlugin` trait. 

> The `SpyglassPlugin` trait defines the set of required methods for a plugin

```rust
pub trait SpyglassPlugin {

/// Initial plugin load, setup any configuration you need here as well as
/// subscribe to specific events.
fn load(&mut self);

/// Asynchronous updates for plugin events
fn update(&mut self, event: PluginEvent);
}
```

> Implement the `SpyglassPlugin` trait and define the plugins functionality

```rust
impl SpyglassPlugin for Plugin {

    fn load(&mut self) {
		let _ = subscribe_for_updates();
	}

	fn update(&mut self, event: PluginEvent) {

		match event {
			PluginEvent::IntervalUpdate => {
				//DO STUFF HERE
			}
			PluginEvent::HttpResponse { url: _, result } => {
			   // DO STUFF HERE
			}
			PluginEvent::DocumentResponse {
				request_id: _,
				page_count: _,
				page: _,
				documents,
				} => {
				// DO STUFF HERE
			}
		}
	}
}
```

The `load` method is called each time the plugin is loaded. Plugins are loaded when Spyglass starts and when a user toggles the plugin to the enabled state. 

The `update` method is called when asynchronous events occur. This method will only be called in response to an action taken by the plugin. Example calling `subscribe_for_updates()` will trigger the update method to be called with the `IntervalUpdate` event every 10 minutes

#### API Helper Methods

Following is the list of API methods available for plugins. Note plugins are in active development and the API can change. [Shims](https://github.com/spyglass-search/spyglass/blob/main/crates/spyglass-plugin/src/shims.rs) is a good place to check to see the current methods available.

> Helper Functions

Note that all helper functions are asynchronous. All requested data will be provided through the update method asynchronously.

Log is used to write a string message into the server log. This is used for debugging. Note that due to the nature of the wasm environment `println!` cannot be used
```rust
pub fn log(msg: &str)
```

Enqueue all is used to add urls to the list of urls to crawl and index. After calling this method the Spyglass system will add the urls in question to the crawl queue and index the documents as part of normal processing

```rust
pub fn enqueue_all(urls: &[String])
```

Delete doc will delete a document from the index. This can be used to remove documents that were added by the plugin, but are no longer needed.

```rust 
pub fn delete_doc(url: &str)
```

Modify tags is used to add or remove tags for all documents that match the query. This allows plugins to add custom tags to documents found in the index.

```rust
pub fn modify_tags(query: DocumentQuery, modification: TagModification) -> Result<(), ron::Error>
```

Query documents can be used to access documents found in the index based on the document query.

```rust
pub fn query_documents(query: DocumentQuery) -> Result<(), ron::Error>
```

Subscribe for documents is the same as query documents, but will run the query at a regular interval and send the results to the update method. This is useful if the plugin is designed to watch for new documents and update tags for those documents. 

```rust
pub fn subscribe_for_documents(query: DocumentQuery) -> Result<(), ron::Error>
```

Subscribe for updates will tell spyglass to send an interval update to the plugin every 10 minutes.  This can be used to allow the plugin to run updates at a regular interval.

```rust
pub fn subscribe_for_updates() -> Result<(), ron::Error>
```

Add document will add a document to the index. If a document with the same `url` already exists the document will be updated with the provided contents

```rust
pub fn add_document(documents: Vec<DocumentUpdate>, tags: Vec<Tag>) -> Result<(), ron::Error>
```

> Helper Structs

`Http` is an available struct that can be used to make http requests. Due to the type of wasm module used popular http libraries like reqwest will not work, instead we provide a basic Http helper that can be used for requests. Just like the helper functions all requests return results asynchronously through the `update` method.

```rust
impl Http {
	pub fn get(url: &str, headers: Vec<(String, String)>)
	pub fn request(url: &str) -> HttpRequestBuilder 
}
```

`Http::get("...", vec![])`

Generates a get request for the specified url. The request is the same as calling `Http::request(url).headers(headers).get().run()`

For more options in building http requests the `request` method can be used.
`Http::request("...")`

`HttpRequestBuilder` is a helper used to build an http request.

```rust
impl HttpRequestBuilder {
	// Builds a request that is a get request
	pub fn get(&self) -> Self 
	// Builds a request that is a put request
	pub fn put(&self) -> Self 
	// Builds a request that is a post request
	pub fn post(&self) -> Self 
	// Builds a request that is a patch request
	pub fn patch(&self) -> Self 
	// Builds a request that is a delete request
	pub fn delete(&self) -> Self 
	// Adds a body to the request
	pub fn body(&self, new_body: String) -> Self 
	// Adds headers to the request
	pub fn headers(&self, new_headers: Vec<(String, String)>) -> Self 
	// Add basic authentication to the request
	pub fn basic_auth(&self, key: &str, val: Option<String>) -> Self 
	// Adds bearer auth to the request
	pub fn bearer_auth(&self, key: &str) -> Self 
	// Runs the http request. Results will be sent to the update method
	pub fn run(&self) 
}
```

