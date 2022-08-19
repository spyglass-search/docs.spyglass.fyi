# Indexing website topics/sites

There are two ways to start indexing websites you're interested in.

1. First is to install one from our [list of community built lenses](../lenses/community.html)

![Lens manager](./../../assets/lens-manager.png)

2. [Building your own lens](../lenses/build.html), which are simple text files which
tell the application what to crawl & index. For example, here is a lens for recipes
that I like to search for on a routine basis:

``` rust
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