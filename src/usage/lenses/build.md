# Building your own lens

You can also create your own lenses. Once created you can drop these in your "lens"
folder, which can be opened through the `My Library` window.

Here are some examples that I've been personally using:

## Curated recipe searching

Interested in cooking & recipes? Add a `recipe` lens which will go index a
curated set of websites with high quality recipes.

```rust,noplayground
(
    version: "1",
    // Be proud of your creation :). Maybe soon we can share these ;)
    author: "Andrew Huynh",
    name: "recipes",
    description: Some(r#"
        A curated collection of websites with useful, high-quality recipes.
    "#),
    // Set to false if you want to disable this lens
    is_enabled: true,
    domains: [

        // Major sites that often have really good recipes
        "www.seriouseats.com",
        "cooking.nytimes.com",
        ...

        // Specific cuisines/sites that I've found randomly w/ high-quality recipes
        "www.hungryhuy.com",
        "www.vickypham.com",
    ],

    urls: [
        // URLs are considered prefixed, i.e. anything that starts w/ the following
        // will be matched and crawled.
        //
        // https://www.reddit.com/r/recipes/ -> matches
        // https://www.reddit.com/r/recipes_not/ -> does not match, notice the end slash.
        "https://www.reddit.com/r/recipes/",
    ],

    // Rules allow you to refine how the crawler determine whether it should crawl
    // a URL or not.
    rules: [
        // SkipURL is a simple regex (similar to ones in robots.txt) that when matches
        // a URL will skip crawling it.
        //
        // For example, below I'm skipping over any URLs that have the word "broccoli"
        // in the path, despite the benefits to my health.
        SkipURL("https://www.seriouseats.com/*broccoli*"),
        // Limits the depth of a URL to a certain depth.
        // For example:
        //  - LimitURLDepth("https://example.com/", 1) will limit it to https://example.com/<path 1>
        //  - LimitURLDepth("https://example.com/", 2) will limit it to https://example.com/<path 1>/<path 2>
        //  - etc.
        // In this case, we're limiting to only https://www.reddit.com/r/recipes/<post> so we don't
        // index too many comments/etc. from reddit.
        LimitURLDepth("https://www.reddit.com/r/recipes", 1),
    ]
)
```


## Narrowing down by a specific topic

Interested in the Rust programming language? Add the `rustlang` lens which will
index the Rust book, rust docs, crate.io, and other sites that are related to the
programming language and not the Rust game / The Rust Belt / oxidation / etc.

```rust,noplayground
(
    version: "1",
    author: "Andrew Huynh",
    name: "rustlang",
    description: Some("Rustlang targeted websites"),
    is_enabled: true,
    domains: [
        // Support for wildcards in domain names
        "*.rust-lang.org",
        "docs.rs",
        "rustconf.com",
        "crates.io",
        "this-week-in-rust.org",
        ...
    ],

    urls: [
        // A `$` at the end will *only* index that particular URL and will not go
        // deeper into the site.
        "https://www.reddit.com/r/rust/$",
        "https://www.reddit.com/r/rust_gamedev/$",
    ],

    rules: []
)
```
