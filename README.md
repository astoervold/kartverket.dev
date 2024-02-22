# [Backstage](https://backstage.io)

This is your newly scaffolded Backstage App, Good Luck!

## Local dev 

### prerequisites
1. install [nvm](https://github.com/nvm-sh/nvm)
2. `nvm install 18`
3. `nvm use 18`

#### GitHub integration
1. Create a personal access token on GitHub with `repo` and `workflow` scopes. Authorize for Kartverket after creation.
2. Create app-config.local.yaml:
```yaml
integrations:
  github:
    - host: github.com
      token: your-token
```
### Persistent sqlite

1. mkdir db
2. add this snippet to your app-config.local.yaml

```yaml
backend:
  database:
    client: better-sqlite3
    connection:
      directory: /<absolute>/<path>/<to>/<repo>/db
```

### Getting user data and orgs
#### Using the anonymized data from Kartverket
```yaml
catalog:
  rules:
    - allow: [ Component, API, Location, Template, Group, User ]
  locations:
    - type: file
      target: ../../test_data/org.yaml
```
To refresh the data, delete your local sqlites and sync with microsoft provider following the instructions below.   
Then run the script `extract_entities.py` in `/test_data`

#### Using AZ Cli to get org data   
Delete the `sqlite` files in the `db` folder before syncing.   
For this you need to have the [az cli](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=apt) and [microsoft intune](https://learn.microsoft.com/en-us/mem/intune/user-help/microsoft-intune-app-linux) installed.    
After setting up intune you can log in to az cli using: `az login --allow-no-subscriptions --scope https://graph.microsoft.com//.default --use-device-code`   
You might have to ask IT to enroll your device.   
Now you can use the following configuration in `app-config.local.yaml` to get user data from microsoft graph. (it might take 5-10 minutes to sync)
```yaml
catalog:
  providers:
  microsoftGraphOrg:
    default:
      tenantId: 7f74c8a2-43ce-46b2-b0e8-b6306cba73a3
      queryMode: 'advanced'
      user:
        filter: accountEnabled eq true and userType eq 'member'
      group:
        filter: >
          startswith(displayName, 'CLOUD_SK')
      schedule:
        frequency: PT1H
        timeout: PT50M
```
 

### Testing OAuth locally
Check this [README](oauth2-proxy/README.md)

### start the app

```sh
yarn install
yarn dev
```


## Plugins
[Linguist](https://github.com/backstage/backstage/tree/master/plugins/linguist) - Plugin to show languages in github repositories