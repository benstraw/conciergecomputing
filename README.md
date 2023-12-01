## Steps taken to create this project:

** install hugo with homebrew **
```bash
brew install hugo
```
- run these commands to create a new site and add the ananke theme
```bash
hugo new site quickstart
cd quickstart
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
echo "theme = 'ananke'" >> hugo.toml
hugo server
```

-add content to the site
```bash
hugo new content posts/my-first-post.md
```

Edit the file you just created using markdown syntax.

Run the server again to see the changes, use the following commands to see drafts and posts marked as draft in the front matter.
```bash
hugo --buildDrafts    # or -D
hugo --buildExpired   # or -E
hugo --buildFuture    # or -F
```

### Commands
** Automatic redirection to changed content **
```bash
hugo server --navigateToChanged
```

### Site configuration
- Edit the config/_default/hugo.toml file to change the site title, add a description, and add a language.

- A list of all the configuration options is available in the [configuration section](https://gohugo.io/getting-started/configuration/) of the documentation.

- To run a different configuration environment, use the -e flag: 
```bash
hugo -e production
```

### Templates
- Hugo uses Go's [html/template](https://golang.org/pkg/html/template/) and [text/template](https://golang.org/pkg/text/template/) libraries as the basis for the templating.
