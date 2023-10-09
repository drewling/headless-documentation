---
title: Get started with WP Content Blocks
description: The tutorial will introduce you to the WP Content Blocks
---



## Introduction




Head over to the [GitHub repo](https://github.com/wpengine/wp-graphql-content-blocks) and download the plugin from the releases tab.



Install the plugin into the `wp-content/plugins` folder on a brand new WordPress installation.



Activate the plugin within WordPress plugins page:



![The plugin activation page in WordPress](https://faustjsorg.wpengine.com/wp-content/uploads/2023/06/activate-wp-graphql-content-plugin-1024x49.png)



There is no other configuration is needed once you install the plugin.



## Getting started




Once the plugin is installed, head over to the GraphiQL IDE and you should be able to perform queries for the block data (This plugin is an extension of [WP-GraphQL](https://www.wpgraphql.com/), so make sure you have it installed first.). There is a new field added in the Post and Page models called `editorBlocks`. This represents a list of available blocks for that content type:



![Example query for editorBlocks using GraphiQL IDE](https://faustjsorg.wpengine.com/wp-content/uploads/2023/06/querying-with-content-blocks-1024x582.png)



If you search in GraphiQL’s documentation explorer tab for the `editorBlocks` type you will be able to see the available block fields. The most important ones are:




`renderedHTML`: It’s the HTML of the block as rendered by the [render_block](https://developer.wordpress.org/reference/functions/render_block/) function.



`name`: The actual name of the block taken from it’s `block.json` spec.



`__typename`: The type of block transformed from the `name` field in camel-case notation.



`apiVersion`: The apiVersion of the block taken from its `block.json` spec.



`innerBlocks`: The inner block list of that block.



`isDynamic`: Whether the block is dynamic or not, taken from its `block.json` spec.



`clientId`, `parentClientId`: Unique identifiers for the block and the parent of the block. We will explain their usage later.




## How does the plugin work?




The plugin essentially iterates over the whole list of `block.json` types as registered within WordPress and creates WPGraphQL types and resolvers based on that specification. As long as your blocks use the [register_block_type](https://developer.wordpress.org/reference/functions/register_block_type/) function passing a `block.json`, it will be available in the system without any extra steps.



As an example, we are given the following `block.json` definition of a block:


.wp-block-code {
	border: 0;
	padding: 0;
	-webkit-text-size-adjust: 100%;
	text-size-adjust: 100%;
}

.wp-block-code > span {
	display: block;
	overflow: auto;
}

.shcb-language {
	border: 0;
	clip: rect(1px, 1px, 1px, 1px);
	-webkit-clip-path: inset(50%);
	clip-path: inset(50%);
	height: 1px;
	margin: -1px;
	overflow: hidden;
	padding: 0;
	position: absolute;
	width: 1px;
	word-wrap: normal;
	word-break: normal;
}

.hljs {
	box-sizing: border-box;
}

.hljs.shcb-code-table {
	display: table;
	width: 100%;
}

.hljs.shcb-code-table > .shcb-loc {
	color: inherit;
	display: table-row;
	width: 100%;
}

.hljs.shcb-code-table .shcb-loc > span {
	display: table-cell;
}

.wp-block-code code.hljs:not(.shcb-wrap-lines) {
	white-space: pre;
}

.wp-block-code code.hljs.shcb-wrap-lines {
	white-space: pre-wrap;
}

.hljs.shcb-line-numbers {
	border-spacing: 0;
	counter-reset: line;
}

.hljs.shcb-line-numbers > .shcb-loc {
	counter-increment: line;
}

.hljs.shcb-line-numbers .shcb-loc > span {
	padding-left: 0.75em;
}

.hljs.shcb-line-numbers .shcb-loc::before {
	border-right: 1px solid #ddd;
	content: counter(line);
	display: table-cell;
	padding: 0 0.75em;
	text-align: right;
	-webkit-user-select: none;
	-moz-user-select: none;
	-ms-user-select: none;
	user-select: none;
	white-space: nowrap;
	width: 1%;
}
// block.json
{
  "$schema": "https://schemas.wp.org/trunk/block.json",
  "apiVersion": 2,
  "name": "my-plugin/notice",
  "icon": "star",
  "version": "1.0.3",
  "attributes": {
    "message": {
      "type": "string",
      "source": "html",
      "selector": ".message"
    }
  }
}
Code language: JSON / JSON with Comments (json)


The plugin will create the following WPGraphQL type:


type MyPluginNotice {
    attributes: MyPluginNoticeAttributes;
}
type MyPluginNoticeAttributes {
    message: String;
}
Code language: CSS (css)


When you request to resolve the `message` attribute for `MyPluginNotice`, the plugin will use a resolver that tries to extract the field by sourcing the text element using the `selector`. As an example, with the following HTML:


`&lt;div class="message"&gt;Hello World&lt;/div&gt;`Code language: HTML, XML (xml)


Since the `block.json` message attribute uses the `.message` selector to source the text for that field, this will resolve to:



`"Hello World"`



Currently the plugin handles the following attribute types taken from the [reference list](https://developer.wordpress.org/block-editor/reference-guides/block-api/block-attributes/):




boolean



number



integer



string



object




**NOTE**If you see a specific attribute missing that you need for your project, please open a [new Feature Request](https://github.com/wpengine/wp-graphql-content-blocks/issues/new) so that the Faust team can make it available for you.



## How do I query block data?




To query specific block data you need to define that data in the `contentBlock` as the appropriate type. For example, to use `CoreParagraph` attributes you need to use the following query:


{
  posts {
    nodes {
      editorBlocks {
        __typename
        name
        ... on CoreParagraph {
          attributes {
            content
            className
          }
        }
      }
    }
  }
}



If the resolved block has paragraph blocks, it will return the relevant fields, otherwise it will return `null`.


{
  "__typename": "CoreParagraph",
  "name": "core/paragraph",
  "attributes": {
    "content": "Hello world",
    "className": null
  }
}
Code language: JSON / JSON with Comments (json)


## What about innerBlocks?




All the blocks available (both blocks and innerBlocks) will be returned as a flat list in the same list by default.



For example, given the following HTML Content:


&lt;columns&gt;
  &lt;column&gt;
    &lt;p&gt;Example paragraph in Column&lt;/p&gt;
  &lt;/column&gt;
&lt;/columns&gt;
Code language: HTML, XML (xml)


It will resolve the following blocks:


[
  {
    "__typename": "CoreColumns",
    "name": "core/columns",
    "id": "63dbec9abcf9d",
    "parentId": null
  },
  {
    "__typename": "CoreColumn",
    "name": "core/column",
    "id": "63dbec9abcfa6",
    "parentId": "63dbec9abcf9d"
  },
  {
    "__typename": "CoreParagraph",
    "name": "core/paragraph",
    "id": "63dbec9abcfa9",
    "parentId": "63dbec9abcfa6",
    "attributes": {
      "content": "Example paragraph in Column 1",
      "className": null
    }
  }
]
Code language: JSON / JSON with Comments (json)


The `CoreColumns` contains one or more `CoreColumn` block, and each `CoreColumn` contains a `CoreParagraph`. If you want to resolve this hierarchy, without returning a flat list, you want to add a `flat: false`  in the following query and nest the block types:


editorBlocks(flat: false) {
    __typename
    name
    ...on CoreColumns {
        innerBlocks {
            ...on CoreColumn {
                ...on CoreParagraph {
                    attributes {
                        content
                        className
                    }
                }
            }
        }
    }
}
Code language: JavaScript (javascript)


In that case, it clearly becomes a scalability issue when you do that since  you don’t really know how many levels deep the query must traverse to resolve all block levels. It could be two levels or it could be 50. Using [block patterns](https://developer.wordpress.org/block-editor/reference-guides/block-api/block-patterns/) for example, poses a real issue since it may contain a deep hierarchical structure. In each case we would have to copy the whole fragment list again and again. 



This is one of the limitations of the `wp-graphql-gutenberg` plugin, but with this plugin we have a more elegant solution.



This is how you request all blocks as a flat list:


editorBlocks {
    __typename
    name
    id: clientId
    parentClientId
    ... on CoreColumns {
        attributes {
            className
        }
    }
    ... on CoreColumn {
        attributes {
            className
        }
    }
    ...on CoreParagraph {
        attributes {
            content
            className
        }
    }
}



This will traverse the hierarchy of blocks and put them back in a single list. Given the flattened list of blocks though, how can you put it back? 



Well that’s where you use the `clientId` and `parentClientId` fields to assign temporary unique ids for each block.



The `clientId` field assigns a temporary unique id for a specific block and the `parentClientId` will be assigned only if the current block has a parent. If the current block does have a parent, it will get the parent’s `clientId` value.



So to put everything back in the Headless site, you want to use the `flatListToHierarchical` function as mentioned in the [WPGraphQL docs](https://www.wpgraphql.com/docs/menus#hierarchical-data). This is available in the `@faustwp/core` package and it is used to transform a flat list into a tree structure.



This way you can reconstruct the block tree as before and pass it on to the `WordPressBlocksViewer` component:


import { WordPressBlocksViewer } from '@faustwp/blocks';
import { flatListToHierarchical } from '@faustwp/core';
...
const { editorBlocks } = props.data.post;
const blocks = flatListToHierarchical(editorBlocks);

&lt;WordPressBlocksViewer blocks={blocks} /&gt;
...
Code language: JavaScript (javascript)


**NOTE **Currently the `clientId` field is only unique per request and is not persisted anywhere. If you perform another request each block will be assigned a new `nodeId` each time.



The `flatListToHierarchical` has the following signature:


function flatListToHierarchical(
  data: Data[] = [],
  {
    idKey = 'id',
    parentKey = 'parentId',
    childrenKey = 'children',
  }: Params = {},
)

export interface Params {
  idKey?: string;
  parentKey?: string;
  childrenKey?: string;
}

type Data = Record&lt;string | number, unknown&gt;;
Code language: TypeScript (typescript)


It accepts a list of `Data` items that contain arbitrary objects. Each need to contain two properties for the unique `id` and `parentId` fields. The `parentId` should point to an `id` of the parent `Data` item.The second parameter is used to configure which fields to use to configure the `id` and `parentId` fields if they are different than the default. The last parameter `childrenKey` is used for placing all the children `Data` items in the result set.For example when given following list of items:


const items = [
  { id: '1', name: 'abc', parentId: '2' },
  { id: '2', name: 'abc', parentId: '' },
  { id: '3', name: 'abc', parentId: '5' },
  { id: '4', name: 'abc', parentId: '2' },
  { id: '5', name: 'abc', parentId: '' },
  { name: 'abc', parentId: '' },
  { id: '6', name: 'abc', parentId: '2' },
  { id: '7', name: 'abc', parentId: '6' },
  { id: '8', name: 'abc', parentId: '6' },
];Code language: JavaScript (javascript)


The result of calling `flatListToHierarchical` with the default parameters will be:


{
    id: '2',
    name: 'abc',
    parentId: '',
    children: [
      { id: '1', name: 'abc', parentId: '2', children: [] },
      { id: '4', name: 'abc', parentId: '2', children: [] },
      {
        id: '6',
        name: 'abc',
        parentId: '2',
        children: [
          { id: '7', name: 'abc', parentId: '6', children: [] },
          { id: '8', name: 'abc', parentId: '6', children: [] },
        ],
      },
    ],
  },
  {
    id: '5',
    name: 'abc',
    parentId: '',
    children: [{ id: '3', name: 'abc', parentId: '5', children: [] }],
  },Code language: CSS (css)
