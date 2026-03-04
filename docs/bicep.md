# OneShot Bicep -ohje

Tämä dokumentti määrittää minimaalisen Azure Bicep -suunnitelman OneShotille:

- Vue-käyttöliittymä
- C#-taustapalvelu (Function App / API-ajoympäristö)
- MSSQL-taustatietovarasto

## Tavoiteltu Azure-arkkitehtuuri

- **Käyttöliittymä:** Azure Static Web Apps (tai App Service -staattinen ylläpito)
- **Taustapalvelu:** Azure Function App (HTTP-triggerit)
- **Tietokanta:** Azure SQL Server + Azure SQL Database
- **Havainnointi:** Application Insights

## Suositeltu Infra-kansio

```text
infra/
├─ main.bicep
├─ main.dev.bicepparam
├─ main.prod.bicepparam
└─ modules/
   ├─ functionApp.bicep
   ├─ sql.bicep
   └─ staticWebApp.bicep
```

## Ympäristöt on erotettava

OneShotissa `dev` ja `prod` luodaan aina erikseen:

- Eri resurssiryhmät (`rg-oneshot-dev`, `rg-oneshot-prod`)
- Eri parametritiedostot (`main.dev.bicepparam`, `main.prod.bicepparam`)
- Eri nimikonventiot resursseille
- Eri salaisuudet per ympäristö

## Minimaalinen `main.bicep` -runko

```bicep
targetScope = 'resourceGroup'

@description('Julkaisun sijainti')
param location string = resourceGroup().location

@description('Ympäristön nimi (dev/test/prod)')
param environment string

@description('Taustapalvelun Function App -nimi')
param functionAppName string

@description('Käyttöliittymän Static Web App -nimi')
param staticWebAppName string

@description('SQL-palvelimen nimi')
param sqlServerName string

@description('SQL-tietokannan nimi')
param sqlDatabaseName string = 'oneshotdb'

@secure()
@description('SQL-järjestelmänvalvojan salasana')
param sqlAdminPassword string

@description('SQL-järjestelmänvalvojan käyttäjänimi')
param sqlAdminLogin string = 'oneshotadmin'

resource appInsights 'Microsoft.Insights/components@2020-02-02' = {
  name: 'appi-${environment}-oneshot'
  location: location
  kind: 'web'
  properties: {
    Application_Type: 'web'
  }
}

resource sqlServer 'Microsoft.Sql/servers@2023-08-01-preview' = {
  name: sqlServerName
  location: location
  properties: {
    administratorLogin: sqlAdminLogin
    administratorLoginPassword: sqlAdminPassword
    publicNetworkAccess: 'Enabled'
  }
}

resource sqlDb 'Microsoft.Sql/servers/databases@2023-08-01-preview' = {
  parent: sqlServer
  name: sqlDatabaseName
  location: location
  sku: {
    name: 'Basic'
    tier: 'Basic'
  }
}

output appInsightsConnectionString string = appInsights.properties.ConnectionString
output sqlDatabaseResourceId string = sqlDb.id
```

## Minimaalinen `main.dev.bicepparam` -esimerkki

```bicep
using './main.bicep'

param environment = 'dev'
param functionAppName = 'func-oneshot-dev'
param staticWebAppName = 'swa-oneshot-dev'
param sqlServerName = 'sql-oneshot-dev-001'
param sqlDatabaseName = 'oneshotdb'
param sqlAdminLogin = 'oneshotadmin'
param sqlAdminPassword = 'REPLACE-IN-PIPELINE-OR-KEYVAULT'
```

## Minimaalinen `main.prod.bicepparam` -esimerkki

```bicep
using './main.bicep'

param environment = 'prod'
param functionAppName = 'func-oneshot-prod'
param staticWebAppName = 'swa-oneshot-prod'
param sqlServerName = 'sql-oneshot-prod-001'
param sqlDatabaseName = 'oneshotdb'
param sqlAdminLogin = 'oneshotadmin'
param sqlAdminPassword = 'REPLACE-IN-PIPELINE-OR-KEYVAULT'
```

## Julkaisukomennot

### Dev

```powershell
az group create --name rg-oneshot-dev --location westeurope
az deployment group create --resource-group rg-oneshot-dev --template-file infra/main.bicep --parameters infra/main.dev.bicepparam
```

### Prod

```powershell
az group create --name rg-oneshot-prod --location westeurope
az deployment group create --resource-group rg-oneshot-prod --template-file infra/main.bicep --parameters infra/main.prod.bicepparam
```

Suositus: aja `prod`-julkaisu vain suojatun CI/CD-ympäristön kautta.

## Turvahuomiot

- Käytä `@secure()` salaisuuksille (näytetty yllä).
- Siirrä salaisuudet Key Vaultiin tai CI/CD:n salaisiin muuttujiin.
- Rajoita SQL-palomuuri ja poista julkinen pääsy tuotannossa.
- Suosi hallittuja identiteettejä taustapalvelun tietokantayhteyksissä.

## Seuraava vaihe tässä repossa

Kun olet valmis, luo `infra/`-kansio ja jaa resurssit moduuleihin (`modules/sql.bicep`, `modules/functionApp.bicep`, `modules/staticWebApp.bicep`) ylläpidon selkeyttämiseksi.
