This was forked from https://github.com/mallowigi/slack-one-dark-theme in an attempt to get slack to recognize other URLS. 
For some reason changing  const csspath to point to a different file or URL causes the theme not to load. 
This is a work in progress, USE AT YOUR OWN RISK!!!



# One Dark Theme for Slack

Atom One Dark Theme for Slack!

# Preview

![Screenshot](preview.png)

# Installing into Slack - This was tested on Slack 3.4.2

# Note: Slack 4.1.0 beta3 custom Theme support.
Slack 4.1.0 beta3 does not include the "ssb-interop.js" file that needs to be modified. To get a custom theme working you will need to unpack the app.asar file located "%localappdata%\slack\app-4.1.0-beta3\resources\" After unpacking you will need to depete app.asar(not recommended) or rename/move the file. Once complete you will need to modify the file "%localappdata%\slack\app-4.1.0-beta3\resources\app\dist\ssb-interop.bundle.js" using one of the methods below. I used Alternative 2 method in windows 10 and got it working. using a local CSS file.

Alot has changed in this beta and any existing themes will need to be updated. With this beta you can theme the sidebar used for switching slack workspaces for those signed into more than 1 workspace.

I am making changes to the custom.css file and may prevent the themes below from working. If you don't like the layout of my theme you can replace the url: and use https://raw.githubusercontent.com/mallowigi/slack-one-dark-theme/master/custom.css
Find your Slack's application directory.

* Windows: `%homepath%\AppData\Local\slack\`
* Mac: `/Applications/Slack.app/Contents/`
* Linux: `/usr/lib/slack/` (Debian-based)


Open up the most recent version (e.g. `app-3.4.2`) then open
`resources\app.asar.unpacked\src\static\ssb-interop.js`

At the very bottom, add

```js
// First make sure the wrapper app is loaded
document.addEventListener("DOMContentLoaded", function() {

   // Then get its webviews
   let webviews = document.querySelectorAll(".TeamView webview");

   // Fetch our CSS in parallel ahead of time
   const cssPath = 'https://raw.githubusercontent.com/omniterm/slack-one-dark-theme/master/custom.css';
   let cssPromise = fetch(cssPath).then(response => response.text());

   let customCustomCSS = `
   :root {
      /* Modify these to change your theme colors: */
     --primary: #E5C17C;
     --accent: #568AF2;
     --text: #ABB2BF;
     --background: #282C34;
     --background-elevated: #3B4048;

     /* These should be less important: */
     --background-hover: lighten(#3B4048, 10%);
     --background-light: #AAA;
     --background-bright: #FFF;

     --border-dim: #666;
     --border-bright: var(--primary);

     --text-bright: #FFF;
     --text-dim: #555c69;
     --text-special: var(--primary);
     --text-accent: var(--text-bright);

     --scrollbar-background: #000;
     --scrollbar-border: var(--primary);

     --yellow: #fc0;
     --green: #98C379;
     --cyan: #56B6C2;
     --blue: #61AFEF;
     --purple: #C678DD;
     --red: #E06C75;
     --red2: #BE5046;
     --orange: #D19A66;
     --orange2: #E5707B;
     --gray: #3E4451;
     --silver: #9da5b4;
     --black: #21252b;
      }
   `

   // Insert a style tag into the wrapper view
   cssPromise.then(css => {
      let s = document.createElement('style');
      s.type = 'text/css';
      s.innerHTML = css + customCustomCSS;
      document.head.appendChild(s);
   });

   // Wait for each webview to load
   webviews.forEach(webview => {
      webview.addEventListener('ipc-message', message => {
         if (message.channel == 'didFinishLoading')
            // Finally add the CSS into the webview
            cssPromise.then(css => {
               let script = `
                     let s = document.createElement('style');
                     s.type = 'text/css';
                     s.id = 'slack-custom-css';
                     s.innerHTML = \`${css + customCustomCSS}\`;
                     document.head.appendChild(s);
                     `
               webview.executeJavaScript(script);
            })
      });
   });
});
```

If that doesn't work you can try one of the following.

Alternative 1 - Note this one does not include local CSS options in the file. You will need to point the URL to your own .css file if you want to edit the theme using this Method.

```js
document.addEventListener('DOMContentLoaded', function() {
 $.ajax({
   url: 'https://raw.githubusercontent.com/omniterm/slack-one-dark-theme/master/custom.css',
   success: function(css) {
     $("<style></style>").appendTo('head').html(css);
   }
 });
});
```


Alternative 2 - The below code is for windows (Tested in win7 and win10). If you would like to use it in Linux you will need to change modify the code. I will add the Linux version When it's been tested and verified to work. This code is used to load a custom.css file from your computer without needing to run a web server. you will need to replace the filePath with the location of your custom.css. When specifying the file location you will need to use / so replace all \ with /  ex. 'C:\slack\theme\custom.css'  would become 'C:/slack/theme/custom.css'

```js
document.addEventListener('DOMContentLoaded', function() {
  var fs = require('fs'),
  filePath = 'C:/Users/dphelps/AppData/Local/slack/app-3.4.2/resources/app.asar.unpacked/src/static/theme/custom.css';
  fs.readFile(filePath, {encoding: 'utf-8'}, function(err, data) {
    if (!err) {
      var css = document.createElement('style')
      css.innerText = data;
      document.getElementsByTagName('head')[0].appendChild(css);
    }
  })
});
```


Notice that you can edit any of the theme colors using the custom CSS (for
the already-custom theme.) Also, you can put any CSS URL you want here,
so you don't necessarily need to create an entire fork to change some small styles.

That's it! Restart Slack and see how well it works.

NB: You'll have to do this every time Slack updates.

# Color Schemes

Here's some example color variations you might like.

## Default
![Default](https://cloud.githubusercontent.com/assets/7691630/24120350/4cbb643e-0d82-11e7-8353-5d4eb65dfd6a.png)
```
--primary: #09F;
--text: #CCC;
--background: #080808;
--background-elevated: #222;
```

## One Dark
![One Dark](https://user-images.githubusercontent.com/806101/27455546-826b3d88-5752-11e7-8a6b-87285b90eb3e.png)
```
--primary: #61AFEF;
--text: #ABB2BF;
--background: #282C34;
--background-elevated: #3B4048;
```

## Low Contrast
![Low Contrast](https://cloud.githubusercontent.com/assets/7691630/24120352/4ccdedf2-0d82-11e7-8ff7-c88e48b8e917.png)
```
--primary: #CCC;
--text: #999;
--background: #222;
--background-elevated: #444;
```

## Navy
![Navy](https://cloud.githubusercontent.com/assets/7691630/24120353/4cd08c4c-0d82-11e7-851a-4c62340456ad.png)
```
--primary: #FFF;
--text: #CCC;
--background: #225;
--background-elevated: #114;
```

# Development


## Inspect

Open Slack on the browser. It has the useful Developer Tools available to them so you can debug with ease.

To test your CSS, install a Stylish-like extension (https://chrome.google.com/webstore/detail/stylish-custom-themes-for/fjnbnpbmkenffdnngjfgmeleoegfcffe?hl=en) then create
a new style for slack and paste the CSS inside and save.

## Local Dev

`git clone` the project and `cd` into it.

Change the CSS URL to `const cssPath = 'http://localhost:8080/custom.css';`

Run a static webserver of some sort on port 8080:

```bash
npm install -g static
static .
```

In addition to running the required modifications, you will likely want to add auto-reloading:

```js
const cssPath = 'http://localhost:8080/custom.css';
const localCssPath = '/Users/username/Code/slack-one-dark-theme/custom.css';

window.reloadCss = function() {
   const webviews = document.querySelectorAll(".TeamView webview");
   fetch(cssPath + '?zz=' + Date.now(), {cache: "no-store"}) // qs hack to prevent cache
      .then(response => response.text())
      .then(css => {
         console.log(css.slice(0,50));
         webviews.forEach(webview =>
            webview.executeJavaScript(`
               (function() {
                  let styleElement = document.querySelector('style#slack-custom-css');
                  styleElement.innerHTML = \`${css}\`;
               })();
            `)
         )
      });
};

fs.watchFile(localCssPath, reloadCss);
```

Instead of launching Slack normally, you'll need to enable developer mode to be able to inspect things.

* Mac: `export SLACK_DEVELOPER_MENU=true; open -a /Applications/Slack.app`

* Linux: (todo)

* Windows: (todo)

# Acknowledgements

Thanks to https://github.com/widget-/slack-black-theme for the idea!

# License

Apache 2.0
