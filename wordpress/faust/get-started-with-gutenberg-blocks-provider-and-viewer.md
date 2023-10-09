---
title: Introduction to Gutenberg Blocks Provider and Viewer with Faust.js
description: Dive into the functionalities of the Gutenberg Blocks Provider and Viewer with Faust.js. This tutorial elucidates how these paired functions offer a smooth integration with the Faust template hierarchy, simplifying block discovery, customization, and rendering processes.
---

The **Gutenberg Blocks Provider and Viewer** are a pair of functions that work together to provide seamless integration with the Faust template hierarchy and abstracts concepts like block discovery, customization and rendering.

#### Quick Start

Make sure you have completed the initial setup for Faust at [Getting Started.](https://faustjs.org/tutorial/get-started-with-faust/)

Install the blocks package with its peer dependencies:

```
npm i @wordpress/style-engine @faustwp/blocksCode language: CSS (css)
```

Create a new folder inside your application root that you will place all the blocks. For conventional reasons, we name it `wp-blocks`.

```
// wp-blocks/index.js
export default {};Code language: JavaScript (javascript)
```

Open `_app.js` and import the blocks provider, passing the list of blocks in the `config` property:

```
import { WordPressBlocksProvider } from '@faustwp/blocks';
import blocks from '../wp-blocks';

<FaustProvider pageProps={pageProps}>
  <WordPressBlocksProvider
    config={{
      blocks,
    }}>
    <Component {...pageProps} key={router.asPath} />
  </WordPressBlocksProvider>
</FaustProvider>
Code language: JavaScript (javascript)
```

Then, inside your templates you need to pass on the `editorBlocks` data in your `WordPressBlocksViewer`. The helper function `flatListToHierarchical` is referenced [here](https://www.wpgraphql.com/docs/menus#hierarchical-data):

```
// wp-templates/single.js
import { WordPressBlocksViewer } from '@faustwp/blocks';
import components from '../wp-blocks';

export default function Component(props) {
  const { editorBlocks } = props.data.post;
  const blocks = flatListToHierarchical(editorBlocks);

  return <WordPressBlocksViewer blocks={blocks}/>
}
Code language: JavaScript (javascript)
```

Example `editorBlocks` GraphQL query fragment that you need to use to fetch the editorBlocks.

```
${components.CoreParagraph.fragments.entry}
editorBlocks(flat: false) {
  __typename
  renderedHtml
  id: clientId
  parentClientId
  ...${components.CoreParagraph.fragments.key}
}
Code language: JavaScript (javascript)
```

**Note:** Setting `flat: false` above returns separate nodes with their own arrays. By default, `editorBlocks` brings all the nodes back in one array instead.

#### A Simple Block Example

This is a simple block called `CoreParagraph`. The block is a `p` tag that sets its content to `attributes.content` which is passed in from the props.

`CoreParagraph.fragments` does a WPGraphQL query for the `content` and sets it as the fragment `CoreParagraphFragment`.

```
import { gql } from '@apollo/client';
import React from 'react';

export default function CoreParagraph(props) {
  const attributes = props.attributes;
  return (
    <p
      dangerouslySetInnerHTML={{ __html: attributes.content }}></p>
    );
}

CoreParagraph.fragments = {
  entry: gql`
    fragment CoreParagraphFragment on CoreParagraph {
      attributes {
        content
      }
    }
  `,
  key: `CoreParagraphFragment`,
};

CoreParagraph.displayName = 'CoreParagraph';
// This also works
// CoreParagraph.config.name = 'CoreParagraph'
Code language: JavaScript (javascript)
```

**Note:** We added a `displayName` property here to make sure that the `__typename` field in the editorBlocks query matches this value. For production builds, it is required to use either a `displayName="NameOfBlock"` or a `config.name="NameOfBlock"` properties for the`WordPressBlocksViewer` component to resolve and render the block properly.

Export the block in `wp-blocks/index.js`:

```
import CoreParagraph from './CoreParagraph';
export default {
  CoreParagraph,
};Code language: JavaScript (javascript)
```

#### Further Learning

More details on the [WordPressBlocksProvider](https://faustjs.org/reference/wordpressblocksprovider/).

More details on the [WordPressBlocksViewer](https://faustjs.org/reference/wordpressblocksviewer/).

Continue learning about the project structure, how to change styles, layout, etc. by referencing the [Example Project Walkthrough Structure](https://faustjs.org/guide/how-to-use-the-faust-example-project/).
