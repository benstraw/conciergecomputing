
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
- edit the archetypes/default.md file to customize the default front matter for new content.
- I removed `draft` and added `tags` and `description` to the front matter.
```toml
+++
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
date = {{ .Date }}
tags = []
description = ""
+++
```

- add content to the site
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

- use a featured image on category pages
Within the front matter of the _index.md file for a category, add the following:
```toml
featured_image = '2023-11-08\ 10.03.14.jpg'
```


### Commands
** Automatic redirection to changed content **
```bash
hugo server --navigateToChanged
```

### Site configuration
- Edit the config/_default/hugo.toml file to change the site title, add a description, and add a language.

- A list of all the configuration options is available in the [configuration section](https://gohugo.io/getting-started/configuration/) of the documentation.

- cascade is a useful config item which allows passing variables to the theme. For example, in a category index front matter, add the following to have the featured_image variable passed to the theme and it's descendant pages.:
```toml
[[cascade]]
    featured_image = '2023-11-08-100314.jpg'
```

- To run a different configuration environment, use the -e flag: 
```bash
hugo -e production
```

### Templates
- Hugo uses Go's [html/template](https://golang.org/pkg/html/template/) and [text/template](https://golang.org/pkg/text/template/) libraries as the basis for the templating.

### Setting up AWS Amplify
- Create a new app in the AWS Amplify console
- Connect the app to the github repo
- I decided to switch my DNS to AWS Route 53 for this application because it was easier to manage the DNS records and SSL certificate in one place. Also amplify uses a domain name not an IP address, so setting that to an A record in enom (my registrar) was not standard, and caused an error.

#### Problem with Hugo and AWS Amplify build
-  When I build locally, the site has 11 Pages, but when I build in AWS Apmplfy it only has 5 pages.
- Local build output:
```bash
(base) ➜  conciergecomputing git:(develop) ✗ hugo 
Start building sites … 
hugo v0.120.4-f11bca5fec2ebb3a02727fb2a5cfb08da96fd9df+extended darwin/amd64 BuildDate=2023-11-08T11:18:07Z VendorInfo=brew
```
- AWS Amplify build output:
```bash
Executing command: hugo version
2023-12-01T23:49:27.988Z [INFO]: Hugo Static Site Generator v0.75.1-A4A7BAB7 linux/amd64 BuildDate: 2020-09-15T06:46:04Z
2023-12-01T23:49:27.990Z [INFO]: # Completed phase: preBuild
                                 # Starting phase: build
                                 # Executing command: hugo
2023-12-01T23:49:28.033Z [INFO]: Start building sites …
2023-12-01T23:49:28.035Z [INFO]: WARN 2023/12/01 23:49:28 found no layout file for "HTML" for kind "page": You should create a template file which matches Hugo Layouts Lookup Rules for this combination.
2023-12-01T23:49:28.035Z [INFO]: WARN 2023/12/01 23:49:28 found no layout file for "HTML" for kind "section": You should create a template file which matches Hugo Layouts Lookup Rules for this combination.
2023-12-01T23:49:28.035Z [INFO]: WARN 2023/12/01 23:49:28 found no layout file for "HTML" for kind "taxonomy": You should create a template file which matches Hugo Layouts Lookup Rules for this combination.
2023-12-01T23:49:28.036Z [INFO]: WARN 2023/12/01 23:49:28 found no layout file for "HTML" for kind "taxonomy": You should create a template file which matches Hugo Layouts Lookup Rules for this combination.
```
#### Solution
- Updated the amplify.yaml file to include the following:
```yaml
frontend:
  phases:
    preBuild:
      commands:
        - echo "Installing Hugo v0.120.4"
        - wget https://github.com/gohugoio/hugo/releases/download/v0.120.4/hugo_0.120.4_Linux-64bit.tar.gz
        - tar -zxvf hugo_0.120.4_Linux-64bit.tar.gz
        - mv hugo /usr/local/bin/hugo
        - hugo version
    build:
      commands:
        - hugo
```
- This will install the same version of hugo that I am using locally, and then run the hugo command to build the site.

#### Updated Solution
- If you need to use a different, perhaps newer, version of Hugo than the version currently supported by AWS Amplify:

- Visit the AWS Amplify Console, and click the app you would like to modify
- In the side navigation bar, Under App Settings, click Build settings
- On the Build settings page, near the bottom, there is a section called Build image settings. Click Edit
- Under Live package updates, click Add package version override
- From the selection, click Hugo and ensure the version field says latest
- Click Save to save the changes.

### Menus
- I used the hugo.toml to configure the menus for the site. I added the following to the hugo.toml file:
```toml
[menu]
[[menu.main]]
  identifier = "contact"
  name = "Contact Us"
  url = "/contact"
  weight = 10
```

### Sections
- Create a new section by creating a folder in the content directory with the name of the section. Then create an _index.md file in that directory. The front matter for the _index.md file will be used to configure the section.
```bash
hugo new content/newsletter/_index.md
```


- Some links to move later

### Analytics
- For analytics I setup a google analytics account and added the tracking code to the hugo.toml file under `config/production/hugo.toml`
```toml
googleAnalytics = "UA-XXXXXXXXX-X"
```