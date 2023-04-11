# gatsby-plugin-advanced-sitemap-webrication

This is a fork of [gatsby-plugin-advanced-sitemap](https://github.com/tryghost/gatsby-plugin-advanced-sitemap) with a few changes:

-   "Ghost" attribution line is removed from XSL stylesheet.
-   trailing slash set to gatsby config's [trailingSlash](https://www.gatsbyjs.com/docs/reference/release-notes/v4.7/#trailingslash-option) option

#

The default Gatsby sitemap plugin generates a simple blob of raw XML for all your pages. This **advanced sitemap plugin** adds more power and configuration, generating a single or multiple sitemaps with full XSL templates to make them neatly organised and human + machine readable, as well linking image resources to encourage media indexing.

**Demo:** https://www.teenyfy.com/sitemap.xml

&nbsp;

![example](https://teenyfy-assets.s3.amazonaws.com/screenshots/sitemap-screenshot-3.png)

_NOTE: This plugin only generates output in `production` mode! To test, run: `gatsby build && gatsby serve`_

&nbsp;

## Install

`npm install --save gatsby-plugin-advanced-sitemap-webrication`

## How to Use

By default this plugin will generate a single sitemap of all pages on your site, without any configuration needed.

```javascript
// gatsby-config.js

siteMetadata: {
    siteUrl: `https://www.example.com`,
},
plugins: [
    `gatsby-plugin-advanced-sitemap-webrication`
]
```

&nbsp;

## Options

If you want to generate advanced, individually organised sitemaps based on your data, you can do so by passing in a query and config. The example below uses Strapi, but this should work with any data source - including Pages, Markdown, Contentful, etc.

**Example:**

```javascript
// gatsby-config.js

plugins: [
    {
        resolve: `gatsby-plugin-advanced-sitemap-webrication`,
        options: {
             // 1 query for each data type
            query: `
            {
              allStrapiBlogPage {
                edges {
                  node {
                    id
                    slug
                  }
                }
              }
            }`,
            // The filepath and name to Index Sitemap. Defaults to '/sitemap.xml'.
            output: "/custom-sitemap.xml",
            mapping: {
                // Each data type can be mapped to a predefined sitemap
                // Routes can be grouped in one of: posts, tags, authors, pages, or a custom name
                // The default sitemap - if none is passed - will be pages
                allStrapiBlogPage: {
                    sitemap: `blog`,
                    // Add a query level prefix to slugs, Don't get confused with global path prefix from Gatsby
                    // This will add a prefix to this particular sitemap only
                    prefix: 'your-prefix/',
                    // Custom Serializer
                    serializer: (edges) => {
                      const siteMapEntries = [];
                      edges.forEach((edge) => {
                        // Custom logic to change final sitemap.
                        if (!edge.node.slug.endsWith("/")) {
                          edge.node.slug = edge.node.slug + "/";
                        }
                        siteMapEntries.push(edge);
                      });
                      return siteMapEntries;
                    },
                },
            },
            exclude: [
                `/dev-404-page`,
                `/404`,
                `/404.html`,
                `/offline-plugin-app-shell-fallback`,
                `/my-excluded-page`,
                /(\/)?hash-\S*/, // you can also pass valid RegExp to exclude internal tags for example
            ],
            createLinkInHead: true, // optional: create a link in the `<head>` of your site
            addUncaughtPages: true, // optional: will fill up pages that are not caught by queries and mapping and list them under `sitemap-pages.xml`
            additionalSitemaps: [ // optional: add additional sitemaps, which are e. g. generated somewhere else, but need to be indexed for this domain
                {
                    name: `my-other-posts`,
                    url: `/blog/sitemap-posts.xml`,
                },
                {
                    url: `https://example.com/sitemap.xml`,
                },
            ],
        }
    }
]
```

Example output of ☝️ this exact config 👉 https://www.teenyfy.com/sitemap.xml

## Develop Plugin

-   Pull the repo

1. Install dependencies

```bash
yarn install
```

Build Plugin

```bash
yarn build
```

Run Tests

```bash
yarn test
```

&nbsp;

# Copyright & License

Copyright (c) 2013-2022 [Ghost Foundation](https://ghost.org/) - Released under the [MIT license](LICENSE).
