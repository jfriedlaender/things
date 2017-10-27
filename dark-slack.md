# Make slack dark

Based on https://github.com/widget-/slack-black-theme, find instructions there.

```
// First make sure the wrapper app is loaded
document.addEventListener("DOMContentLoaded", function() {

   // Then get its webviews
   let webviews = document.querySelectorAll(".TeamView webview");

   // Fetch our CSS in parallel ahead of time
   const cssPath = 'https://cdn.rawgit.com/widget-/slack-black-theme/master/custom.css';
   let cssPromise = fetch(cssPath).then(response => response.text());

   let customCustomCSS = `
   :root {
      /* Modify these to change your theme colors: */
      --primary: #61AFEF;
      --text: #d4d7d6;
      --text-bright: #cc3e44;
      --background: #0e1112;
      --background-elevated: #1d2325;
      --background-bright: transparent;
      --primary: #cbcb41;
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
