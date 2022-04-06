[![Generate Static Pages](https://github.com/Jianbo-Zhu/cloudflare-page-sample/actions/workflows/Generate-static-pages.yml/badge.svg)](https://github.com/Jianbo-Zhu/cloudflare-page-sample/actions/workflows/Generate-static-pages.yml)
# Integrate Ghost with Cloudflare
1. Export Ghost content with gssg into static pages
2. store the static pages in Github repository
3. Setup Cloudflare Page project with Github repo

## Challenge
The Challenge here is the search feature no longer available.

## The solution to retain the search feature even with static pages

### How the search works

1. Pull all posts from Ghost via its content api
2. Index the posts and cache it in localStorage
3. when user search for something, it actually seach the cached index

### The solution

1. Create a Github repository for the static pages `help-static`
2. On Ghost admin page, added a Ghost webhook, triggers Github workflow on `Site changed(rebuilt)` event
3. The Github workflow would generate static pages from `https://api.help.testapp.io` for `https://help.testapp.io` with command like `gssg --domain https://api.help.testapp.io -url https://help.testapp.io`
4. Commit the changes back to git repository
5. Copy `static/assets/js/app.bundle.min.js` to `resources/app.bundle.min.js` and apply below changes.
   1. From something like `for(var t="".concat(this.config.url,"/ghost/api/"` to `for(var t="".concat("https://help.testapp.io","/ghost/api/"`
   2. From something like `return t+='<a href="'+e.url+'">` to `return t+='<a href="'+e.url.replace('help.testapp.io','help.superjambo.net')+'">`
6. Create a Cloudflare Page project for the Github repository. Put following line to the build comamnd. `cp -f resources/app.bundle.min.js static/assets/js/app.bundle.min.js` and change root directory to `/static/`
