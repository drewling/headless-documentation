---
title: Get started with Faust
description: The tutorial will introduce you to the Faust framework, and the complete cycle of application creation, configuration, and deployment as a developer of a Headless site using WordPress.
---

The tutorial will introduce you to the **Faust framework**, and the complete cycle of application creation, configuration, and deployment as a developer of a Headless site using WordPress. It assumes you are comfortable with the command line, and understand the basics of JavaScript, WordPress and Bash.

- Quick Start
  - Installing Faust Framework
  - Installing Faust Plugin
  - Connecting Your WordPress Site
  - Set your Front-end Site URL
  - Check Your Permalinks
- Example Project Walkthrough
  - Understanding the Example Project
  - Getting the Example Working Locally
  - The File Structure Rationale
  - Changing Example Files

## Quick Start

To create a Faust project, run:

```
npx create-next-app \
    -e https://github.com/wpengine/faustjs/tree/main \
    --example-path examples/next/faustwp-getting-started \
    --use-npmCode language: Shell Session (shell)
```

Now, `cd` into your new app and copy the sample environment template:

```
cp .env.local.sample .env.localCode language: CSS (css)
```

Finally, run the dev server:

```
npm run dev
```

You can now visit [http://localhost:3000](http://localhost:3000/) to see your new project.

Currently, the posts and pages you see are coming from our WordPress site at [https://faustexample.wpengine.com](https://faustexample.wpengine.com/). In the next step, we’ll show you how to hook up your own WordPress site.

## Connecting Your WordPress Site

The example app above loads WordPress content from the demo site at [https://faustexample.wpengine.com](https://faustexample.wpengine.com/).

To point it to a different WordPress site, first, make sure you have installed and activated the necessary WordPress plugins:

- [Faust](https://wordpress.org/plugins/faustwp/)
- [WPGraphQL](https://wordpress.org/plugins/wp-graphql/)

Once the necessary plugins have been installed, open the `.env.local` file you created earlier, it should look something like this:

```
# Your WordPress site URL
NEXT_PUBLIC_WORDPRESS_URL=https://faustexample.wpengine.com

# Plugin secret found in WordPress Settings->Faust
FAUST_SECRET_KEY=YOUR_PLUGIN_SECRETCode language: PHP (php)
```

If you are using a version of Faust previous to 0.2.1, use `FAUSTWP_SECRET_KEY` instead of `FAUST_SECRET_KEY`.

Update the `NEXT_PUBLIC_WORDPRESS_URL` value with your WordPress site URL (be sure to include `http://` or `https://`).

Additionally, update the `FAUST_SECRET_KEY` value with the secret key found in Settings → Faust in your WordPress admin area to support previews.

![The FaustWP WordPress admin interface with a red rectangle around the Secret Key field.
](https://faustjsorg.wpengine.com/wp-content/uploads/2023/05/headless-admin-secret-1024x743.png)

**NOTE for Atlas Users**:  
If deploying from the Atlas portal using your own repository without beginning with a blueprint, you will need to set your environment variables manually. Learn how by taking a look at our [FAQs](https://faustjs.org/explanation/faq/).

## Set your Front-end Site URL

To ensure certain features of Faust function as expected, such as previews, be sure to set the Front-end Site URL in the Faust WordPress Plugin Settings. It is the URL to your headless front-end. This is used for authenticated post previews and for rewriting links to point to your front-end site.

1.  Navigate to the `Faust WordPress Plugin` settings page.
2.  Enter in the base url of your headless WordPress site, i.e. `http://localhost:3000` or `http://www.mysiteurl.com` in the Front-end site URL setting.

![The FaustWP WordPress admin interface with a red rectangle around the Front-end site URL field.
](https://faustjsorg.wpengine.com/wp-content/uploads/2023/05/frontend-site-url-settings-1024x668.png)

Click `Save Changes` .

## Check Your Permalinks

Finally, ensure your permalink structure in `WP Admin -> Settings -> Permalinks` **is not** set to “plain”:

![WordPress Admin Settings Page for Permalinks](https://faustjsorg.wpengine.com/wp-content/uploads/2023/05/permalinks-1024x801.png)

Faust resolves templates similar to the Template Hierarchy in WordPress, but “plain” permalinks are not supported due to constraints in Next.js.

## Start Editing

With your project created and development environment started, edit `wp-templates/front-page.js` and see the updated results in your browser.

## Further Learning

Continue learning about the project structure, how to change styles, layout, etc. by referencing the [Example Project Walkthrough Structure.](https://faustjs.org/guide/how-to-use-the-faust-example-project/)
