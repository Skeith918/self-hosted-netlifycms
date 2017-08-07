# Self-hosted Netlifycms
## Tutorial to install an fully local netlifycms installation.

### Requirements
- A static site generator whith its dependencies which linked with github (Jekyll, Hugo, etc...), [HERE](https://www.staticgen.com/), a list of the top of static site generator.
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

### Usages
- Just go to http://your.domain.site/admin ;)
