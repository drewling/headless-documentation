---
title: Converting React Components to Gutenberg Blocks
description: This tutorial guides users on how to transform their React components into Gutenberg blocks, enabling seamless integration with the WordPress block editor. It covers the foundational steps, including setting up the necessary tools, creating block configurations, and registering the blocks in WordPress.
---

The **@faustwp/block-editor-utils** package provides helper functions for converting existing React components into Gutenberg blocks. This is very useful for when you already have some components and you want to test them in Gutenberg without needing to write any code to port them. You will also be able to re-use the same React component in both places; the Headless side and the Editor side.

Make sure you have completed the initial setup for Faust at [Getting Started.](https://faustjs.org/tutorial/get-started-with-faust/)

#### Quick Start

Follow the quick-start guide of the [create-block](https://developer.wordpress.org/block-editor/reference-guides/packages/packages-create-block/#quick-start) package and create a new blocks plugin that you can use to test your blocks.

In this guide we are creating a new plugin named `my-first-block`. First, navigate to the plugins folder of your WordPress app. Then run this command:

```
$ npx @wordpress/create-block@latest my-first-block
```

Install the **block-editor-utils** package:

```
$ npm i @faustwp/block-editor-utils
```

You should see the following file and folder structure now:

```
tree -L 2 .

├── my-first-block.php
├── package-lock.json
├── package.json
├── readme.txt
├── src
│   ├── block.json
│   ├── edit.js
│   ├── editor.scss
│   ├── index.js
│   ├── save.js
└── └── style.scssCode language: CSS (css)
```

Now we're ready to explore the process of using this package's helpers to convert a React component to blocks.

Then start the development server:

```
$ npm run start
```

#### Porting A Simple Component

For the purposes of this tutorial we are using the a simple React component. Add this code inside the `src` folder:

```
// MyFirstBlock.jsx
function MyFirstBlock({ style, className, attributes, children, ...props }) {
const styles = {
...style,
backgroundColor: attributes.bg_color,
color: attributes.text_color,
};
return (
<div
{...props}
style={styles}
className={className}
dangerouslySetInnerHTML={{ __html: attributes.message }}
/>
);
}

export default MyFirstBlock
Code language: JavaScript (javascript)
```

This pure React component consists of `div` element with three attributes that controls the content and the style of the box. Let's describe them briefly:

- **message**: is a text message that gets displayed.
- **bg_color**: controls the background color.
- **text_color**: controls the text color.

We would like to use this React component in WordPress using the Block editor. Traditionally, this step would require us (the developers) to register a block within WordPress, provide a `block.json` and write code for the [Edit and Save](https://developer.wordpress.org/block-editor/reference-guides/block-api/block-edit-save/) functions for the editor. Once those steps are done, then the block would be usable in the block editor list.

That is a lot of know-how and development effort for simply trying to use the React component in the editor side. What if we just provided the React component and let the framework handle all the block registration and creating the editor form fields for changing the content?

This is what `@faustwp/block-editor-utils` package tries to do. It provides a `registerFaustBlock` helper function, that handles all the necessary configuration, registration and generation of Editor Form fields and [Inspector controls](https://developer.wordpress.org/block-editor/how-to-guides/block-tutorial/block-controls-toolbar-and-sidebar/) for you. From the perspective of the Headless Developer, they provide the React component and are able to review this within the WordPress block editor and re-use it in both places.

Within your `src` folder replace the `index.js` contents with the following code:

```
// index.js
import './style.scss';
// import block.json
import metadata from './block.json';

// import our React component
import MyFirstBlock from './MyFirstBlock';

import { registerFaustBlock } from '@faustwp/block-editor-utils';

// Register the React component in Gutenberg
registerFaustBlock(MyFirstBlock, {blockJson: metadata})
Code language: JavaScript (javascript)
```

The `registerFaustBlock` helper takes the following arguments:

- **component**: the actual React component to convert into a Gutenberg block. (**Required**).
- **metadata**: a metadata object that contains several fields:
  - **blockJson**: the `block.json` object that describes the component attributes. (**Required**).
  - **edit**: provides a custom Edit function that describes the structure of your block in the context of the editor. (**Optional**).
  - **save**: provides a custom Save function that defines the way in which the different attributes should be combined into the final markup. (**Optional**).

Now let's take a look at the `block.json` that was created when we ran the block generator. Since we declared three configurable attributes for our component, we need to declare them as attributes here.

Here is the final `block.json` with the assigned attributes object:

```
{
"$schema": "https://schemas.wp.org/trunk/block.json",
"apiVersion": 2,
"name": "create-block/my-first-block",
"version": "0.1.0",
"title": "My First Block",
"category": "widgets",
"icon": "smiley",
"description": "Example block scaffolded with Create Block tool.",
"supports": {
"html": false
},
"attributes": {
"message": {
"type": "string",
            "default": "My First Block"
},
"bg_color": { "type": "string", "default": "#000000" },
        "text_color": { "type": "string", "default": "#ffffff" }
},
"textdomain": "my-first-block",
"editorScript": "file:./index.js",
"editorStyle": "file:./index.css",
"style": "file:./style-index.css"
}
Code language: JSON / JSON with Comments (json)
```

#### Trying out the Component in the Block Editor

We can now try to use the component straight in the editor side. This is what it will look like at first glance in Edit mode:

![React Component in Edit Mode](https://faustjsorg.wpengine.com/wp-content/uploads/2023/09/Screenshot-2023-09-08-at-13.15.57-1024x420.png)

You can interact with the form fields, and then click outside the block contents where you will see the component rendered in Preview mode.

![React Component in Preview Mode.](https://faustjsorg.wpengine.com/wp-content/uploads/2023/09/Screenshot-2023-09-08-at-13.18.52-1024x222.png)

#### Configuring the Form Controls

So far we've been able to render the React component in the Block Editor, change some of the attributes, and reflect the changes in the page.

However, a few of the attributes that control the color are using [text field](https://developer.wordpress.org/block-editor/reference-guides/components/text-control/) controls, which may prove problematic since they allow invalid values. What if we wanted to use a proper color picker component?

Since the `block.json` [attribute types](https://developer.wordpress.org/block-editor/reference-guides/block-api/block-attributes/#type-validation) do not allow `color` as a value, we will have to provide a different configuration to allow that option.

When registering the React component using `registerFaustBlock` , it allows extra configuration to be used in case you want to declare which kinds of controls to use on each attribute.

Add the following config object as a property to the `MyFirstBlock` function:

```
// MyFirstBlock.jsx
...
MyFirstBlock.config = {
name: "MyFirstBlock",
editorFields: {
bg_color: {
location: "inspector",
control: "color",
},
text_color: {
location: "inspector",
control: "color",
},
},
};
Code language: JavaScript (javascript)
```

Here we included an object with the following properties:

- `name`: the name of the block.
- `editorFields`: the list of Editor metadata configuration. This consists of two attributes that we want to specify: the type of control to use and the location within the editor. For example, by using `location: "inspector",` we are telling this control to appear in the [Block Sidebar](https://developer.wordpress.org/block-editor/how-to-guides/block-tutorial/block-controls-toolbar-and-sidebar/) section. By using `control: "color"`, we are indicating that we want to use a [ColorPicker](https://developer.wordpress.org/block-editor/reference-guides/components/color-picker/) component instead of the regular `TextControl`.

Once you update the component, you can refresh the page and create a new block. Now instead of having two text fields inside the block, we have two `ColorPicker` fields in the sidebar section:

![Using ColorPicker controls for the color attributes.](https://faustjsorg.wpengine.com/wp-content/uploads/2023/09/Screenshot-2023-09-08-at-14.04.12-1024x649.png)

#### Form Control Reference List

So far we've seen examples of two controls: The `ColorPicker` handled by the `control: "color"` and the `TextControl`, which is set as default for every `type: "string"` in the `block.json` attributes list.

The corresponding table represents the mapping logic between the `block.json` attributes and their associated fields:

| type    | field           | comment                                                                                                                                |
| ------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| string  | TextControl     | Renders a [TextControl field](https://developer.wordpress.org/block-editor/reference-guides/components/text-control/) of type `text`   |
| boolean | RadioControl    | Renders a [RadioControl field](https://developer.wordpress.org/block-editor/reference-guides/components/radio-control/)                |
| integer | TextControl     | Renders a [TextControl field](https://developer.wordpress.org/block-editor/reference-guides/components/text-control/) of type `number` |
| number  | TextControl     | Renders a [TextControl field](https://developer.wordpress.org/block-editor/reference-guides/components/text-control/) of type `number` |
| object  | TextAreaControl | Renders a [TextAreaControl field](https://developer.wordpress.org/block-editor/reference-guides/components/textarea-control/)          |

Table of `block.json` attributes types to Editor Controls

The following control types will also be available when using the `editorFields` metadata when specifying a `control` property:

| control  | field           | comment                                                                                                                                |
| -------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| color    | ColorPicker     | Renders a [ColorPicker field](https://developer.wordpress.org/block-editor/reference-guides/components/color-picker/)                  |
| text     | TextControl     | Renders a [TextControl field](https://developer.wordpress.org/block-editor/reference-guides/components/text-control/) of type `text`   |
| textarea | TextAreaControl | Renders a [TextAreaControl field](https://developer.wordpress.org/block-editor/reference-guides/components/textarea-control/)          |
| radio    | RadioControl    | Renders a [RadioControl field](https://developer.wordpress.org/block-editor/reference-guides/components/radio-control/)                |
| select   | SelectControl   | Renders a [SelectControl field](https://developer.wordpress.org/block-editor/reference-guides/components/select-control/)              |
| range    | RangeControl    | Renders a [RangeControl field](https://developer.wordpress.org/block-editor/reference-guides/components/range-control/)                |
| number   | TextControl     | Renders a [TextControl field](https://developer.wordpress.org/block-editor/reference-guides/components/text-control/) of type `number` |
| checkbox | CheckBoxControl | Renders a [CheckBoxControl field](https://developer.wordpress.org/block-editor/reference-guides/components/checkbox-control/)          |

**NOTE**:  
The `editorFields` configuration provides necessary hints for the helper to render the specified controls. It always overrides any configuration that is declared in the `block.json` attributes section. If you are not seeing the appropriate control used, check that your `editorFields` contain the correct attribute name and the correct `control` property.

## Reference

The following [RFC document](https://github.com/wpengine/faustjs/issues/1522) explains in detail the different options and configurations regarding the usage of the `@faustwp/block-editor-utils`.
