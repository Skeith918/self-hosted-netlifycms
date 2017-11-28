# Self-hosted Netlifycms
## Tutorial to install an fully selfhosted netlifycms instance, a blog and deploy it automatically

### Note

The authentication and edition **will not work with fork !**

### Requirements
- A static site generator whith its dependencies in your server and which's stored in github (Jekyll, Hugo, etc...), [HERE](https://www.staticgen.com/), a list of the top of static site generator.
- Nodejs 6.x
- [PM2](https://github.com/Unitech/pm2) node package

### Installation

##### The Github Authentication Provider

- Create an Oauth application in your [github settings](https://github.com/settings/developers) with the callback URL : http://your.domain.site:3000/callback

- Clone the [netlify-github-oauth-provider](https://github.com/vencax/netlify-cms-github-oauth-providernetli) repositories
```
git clone https://github.com/vencax/netlify-cms-github-oauth-providernetli
```
- Create in this one the _.env_ file and set in the credentials of your Oauth application :
```
touch netlify-cms-github-oauth-providernetli/.env
```
```
NODE_ENV=production
OAUTH_CLIENT_ID=yourclientid
OAUTH_CLIENT_SECRET=yourclientsecret
```
- Install the dependencies and start the API server with PM2
```
npm install
pm2 start --name="github-oauth-provider" npm -- start
```
##### Configuration of static site
- On the root path of your static site, create the "_admin_" directory and this directory create the file _index.html_ and _config.yml_
```
mkdir admin && touch admin config.yml && touch index.html
```
- Edit the index.html like below:
```
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Content Manager</title>

  <link rel="stylesheet" href="http://cms.your.domain.site/dist/cms.css" />

</head>
<body>
  <script src="http://cms.your.domain.site/dist/cms.js"></script>
</body>
</html>

```
- And the config.yml like this (To create collection take a look to the [official docs](https://github.com/netlify/netlify-cms/blob/master/docs/quick-start.md)):
```
backend:
  name: github
  base_url: http://your.domain.site:3000
  site_domain: your.domaine.site
  repo: owner/repositories # Path to your Github repository

media_folder: "img/uploads" # Folder where user uploaded files should go

collections: # A list of collections the CMS should be able to edit
```
- Then, build your site.

##### NetlifyCMS

- Clone the [netlify-cms](https://github.com/netlify/netlify-cms) repo
```
git clone https://github.com/netlify/netlify-cms
```
- Set a web access to the root path of netlifycms with your favorite web server.
```
server {
    listen 80

    server_name cms.your.domain.site

    location / {
        add_header Access-Control-Allow-Origin "*";
        root /path/to/netlify-cms;
        index index.html;
        }

}
```
- Edit the netlify.toml to link with your static site:
```
[build]
Command = "npm run build && cp /path/to/your/static/site/* dist/"
Publish = "dist"

```
- Install dependencies and build the CMS
```
npm install
npm run build
```

##### Deployment

- We'll use CircleCI for test and automated deployment:
- Where you want, create an ssh key pair without passphrase
```
ssh-keygen
```
- In the root of your Github project, create the file _circle.yml_ and set your tests according to your static site generator, in my case a set a test for a [Jekyll](https://jekyllrb.com/) site. But in any cases, keep the deployment part
```
machine:
  ruby:
    version: 2.3.1
  java:  # see <https://github.com/svenkreiss/html5validator#integration-with-circleci>
    version: oraclejdk8
  environment:
    NOKOGIRI_USE_SYSTEM_LIBRARIES: true # Faster installation of nokogiri, required by html-proofer

dependencies:
  post:
    - bundle exec jekyll --version # print it out

test:
  override:
    - bundle exec jekyll doctor
    - bundle exec jekyll build

deployment:
  production:
    branch: master
    commands:
      - ./deploy
```
- Also create a _deploy_ file, CircleCI will execute it in its container to deploy your change in your server and build the site, again, in my case the deploy script is adapted to a jekyll site, for an another tool add all ssh command you need to build your site, but keep the __git pull__ command
```
#!/bin/bash

echo "Start deploy"
ssh -tq root@your.domaine.site "bash -lc 'cd /path/to/your/static/site/ && git pull'"
ssh -tq root@your.domaine.site "bash -lc 'cd /path/to/your/static/site/ && bundle exec jekyll build'"
echo "Deployed Successfully!"

exit 0

```
- Connect to [CircleCI](https://circleci.com/vcs-authorize/) with your github account
- On the dashboard, select your github project which match with your site
- In the settings of your project in PERMISSIONS, SSH PERMISSIONS, select _Add SSH Key_
- In this input, set your site domain in _hostname_ and copy the private key you created
- Now, for each push in your git repositories whether this comes from the CMS, You directly on github, or from the server, CircleCI execute your test and your deploy script to build your site automaticly.

##### Usages

- Go to http://your.domain.site/admin ;)
