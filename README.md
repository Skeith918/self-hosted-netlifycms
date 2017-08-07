# Self-hosted Netlifycms
## Tutorial to install an fully local netlifycms installation.

#### Requirements
- A static site generator whith its dependencies which linked with github (Jekyll, Hugo, etc...), [HERE](https://www.staticgen.com/), a list of the top of static site generator.
- Nodejs 6.x
- [PM2](https://github.com/Unitech/pm2) node package

#### Installation

- Create an Oauth application in your [github settings](https://github.com/settings/developers) with the callback URL : http://your.domain.site:3000/callback

- Clone the [netlify-github-oauth-provider](https://github.com/vencax/netlify-cms-github-oauth-providernetli) repositories
```
git clone https://github.com/vencax/netlify-cms-github-oauth-providernetli
```
- Create the _.env_ file and set in the credential of your Oauth application :
```
touch netlify-cms-github-oauth-providernetli/.env
```
```
NODE_ENV=production
OAUTH_CLIENT_ID=yourclientid
OAUTH_CLIENT_SECRET=yourclientsecret
```



git clone https://github.com/netlify/netlify-cms
[netlify-cms](https://github.com/netlify/netlify-cms) and


- On the root path of your static site, create the "_admin_" directory and this directory create the file _index.html_ and _config.yml_
```
mkdir admin && touch admin config.yml && touch index.html
```
