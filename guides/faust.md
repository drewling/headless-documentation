# Get started with Faust

The tutorial will introduce you to the Faust framework, and the complete cycle of application creation, configuration, and deployment as a developer of a Headless site using WordPress. It assumes you are comfortable with the command line, and understand the basics of JavaScript, WordPress, and Bash.

## Quick Start

### Installing Faust Framework
### Installing Faust Plugin
### Connecting Your WordPress Site
### Set your Front-end Site URL
### Check Your Permalinks
### Example Project Walkthrough
### Understanding the Example Project
### Getting the Example Working Locally
### The File Structure Rationale
### Changing Example Files

To create a Faust project, run:
```
npx create-next-app \
    -e https://github.com/wpengine/faustjs/tree/main \
    --example-path examples/next/faustwp-getting-started \
    --use-npm
```

Now, `cd` into your new app and copy the sample environment template:
```
cp .env.local.sample .env.local
npm run dev
```

Visit [http://localhost:3000](http://localhost:3000) to see your new project.

## Connecting Your WordPress Site

The example app above loads WordPress content from the demo site at [https://faustexample.wpengine.com](https://faustexample.wpengine.com).

To point it to a different WordPress site, first, make sure you have installed and activated the necessary WordPress plugins:
- Faust
- WPGraphQL

Once the necessary plugins have been installed, open the `.env.local` file you created earlier.

Update the `NEXT_PUBLIC_WORDPRESS_URL` value with your WordPress site URL.

Additionally, update the `FAUST_SECRET_KEY` value with the secret key found in Settings → Faust in your WordPress admin area to support previews.

## Set your Front-end Site URL

To ensure certain features of Faust function as expected, such as previews, be sure to set the Front-end Site URL in the Faust WordPress Plugin Settings.

1. Navigate to the Faust WordPress Plugin settings page.
2. Enter in the base url of your headless WordPress site, i.e. [http://localhost:3000](http://localhost:3000) or [http://www.mysiteurl.com](http://www.mysiteurl.com) in the Front-end site URL setting.
3. Click Save Changes.

## Check Your Permalinks

Ensure your permalink structure in WP Admin -> Settings -> Permalinks is not set to "plain".

Faust resolves templates similar to the Template Hierarchy in WordPress, but "plain" permalinks are not supported due to constraints in Next.js.

## Start Editing

With your project created and development environment started, edit `wp-templates/front-page.js` and see the updated results in your browser.

## Further Learning

Continue learning about the project structure, how to change styles, layout, etc. by referencing the Example Project Walkthrough Structure.

