# OneShot Web Page

## Mistä tässä on kyse

OneShot Web Page on yhden toteutuskierroksen (one-shot) full-stack-pohja, jossa:

- Vue toimii käyttöliittymänä
- C#/.NET toimii taustapalveluna (Azure Function App, HTTP-triggerit)
- MSSQL toimii päätietokantana

Tavoite on saada toimiva kehitysympäristö nopeasti ylös, dokumentaatio edellä, ja jatkaa siitä tuotantovalmiiseen julkaisuun (CI/CD + testit + infra).

Tässä repositoriossa dokumentaatio on ensin, jotta voit rakentaa ja julkaista nopeasti.

## Projektin rakenne

```text
one-shot-web-page-boilerplate/
├─ README.md
├─ docs/
│  ├─ architecture.mmd
│  ├─ bicep.md
│  ├─ github-cicd.md
│  ├─ testing.md
│  ├─ setup.md
│  ├─ backend.md
│  ├─ frontend.md
│  ├─ database.md
│  └─ deploy.md
```

## Pika-aloitus

1. Lue [Asennusohje](docs/setup.md)
2. Määritä [Tietokanta](docs/database.md)
3. Käynnistä [Taustapalvelu](docs/backend.md)
4. Käynnistä [Käyttöliittymä](docs/frontend.md)
5. Vahvista kokonaisuus ja käytä [Julkaisun tarkistuslista](docs/deploy.md)
6. Katso arkkitehtuurikaavio [Mermaid MMD](docs/architecture.mmd)
7. Tutustu infraohjeeseen [Bicep](docs/bicep.md)
8. Määritä putki [GitHub CI/CD](docs/github-cicd.md)
9. Ota testit käyttöön [Testausohje](docs/testing.md)

## Suositellut stack-versiot

- .NET 8 (tai nykyinen LTS-versiosi)
- Azure Functions Core Tools v4 (`npm install -g azure-functions-core-tools@4`)
- Node.js 20+
- Vue 3 + Vite
- SQL Server 2019+

## Ympäristömuuttujat

Käytä näitä lähtötasona.

### Taustapalvelu

Määritä `backend/src/OneShot.Functions/local.settings.json`-tiedostoon:

- `AZURE_FUNCTIONS_ENVIRONMENT=Development`
- `ConnectionStrings__DefaultConnection=Server=localhost;Database=OneShotDb;User Id=sa;Password=YourStrong!Passw0rd;TrustServerCertificate=True;`
- `JWT__Issuer=oneshot`
- `JWT__Audience=oneshot-web`
- `JWT__Key=change-this-long-secret`

### Käyttöliittymä

- `VITE_API_BASE_URL=http://localhost:5000`

## Tila

- [ ] Taustapalvelun Azure Function App -päätepisteet kytketty
- [ ] Käyttöliittymä kytketty APIin
- [ ] MSSQL-migraatiot ajettu
- [ ] End-to-end-testi läpäisty

## Huomiot

Päivitä kaikki dokumentit toteutuksen edetessä. Pidä tämä tiedosto korkean tason aloituspisteenä.

## Hyvä komento koodin generointiin (PowerShell)

Jos tarkoitus on, että Claude generoi rungon, käytä projektin juuressa Claude-komentoa:

```powershell
claude "Luo tähän projektiin backend (C# .NET Azure Functions, HTTP-triggerit, isolated worker model) ja frontend (Vue + Vite + TypeScript), lisää tarvittavat package/projektiriippuvuudet, sekä päivitä README.md ja docs/* vastaamaan toteutusta."
```

## Hyvä komento ajamiseen (PowerShell)

Käynnistä taustapalvelu ja käyttöliittymä yhdellä komennolla projektin juuresta:

```powershell
Start-Process powershell -ArgumentList '-NoExit','-Command','cd backend/src/OneShot.Functions; func start'; cd frontend; npm run dev
```

TODO: päivitä `src/OneShot.Functions`-polku, jos backendin projekti on nimetty toisin. Vaatii Azure Functions Core Tools (`func`) asennettuna.

## Mitä jää käsin tehtäväksi

### Aina generaation jälkeen

- `.gitignore` sisältää `local.settings.json` ja `.env` — Claude saattaa lisätä, mutta varmista
- JWT-avain on vaihdettu pois oletuksesta (`change-this-long-secret`)
- CORS-origin vastaa frontendisi porttia (oletus `5173`, varmista)
- EF-migraatiot ajettu käsin ensimmäistä kertaa (`dotnet ef database update`)

### Ennen Bicep-ajoa

- `az login` ja oikea tilaus aktiiviseksi: `az account set --subscription <id>`
- Resource providerit rekisteröity: `Microsoft.Web`, `Microsoft.Sql`, `Microsoft.Insights`
- `sqlAdminPassword` ei ole `.bicepparam`-tiedostossa — anna pipeline-muuttujana tai Key Vaultista
- GitHub Secret `AZURE_CREDENTIALS` luotu: `az ad sp create-for-rbac --name oneshot-deploy --role Contributor`

### Bicep-ajon jälkeen

- Storage Account lisätty Bicepiin ja `AzureWebJobsStorage` asetettu Function Appin asetuksiin (puuttuu `main.bicep`-rungosta)
- `FUNCTIONS_WORKER_RUNTIME=dotnet-isolated` asetettu App Settingsiin
- `ConnectionStrings__DefaultConnection` asetettu App Settingsiin (ei kovakoodattuna)
- SQL-palomuurisääntö avattu: `az sql server firewall-rule create ...`
- SWA deployment token haettu ja lisätty GitHub Secreteihin: `AZURE_STATIC_WEB_APPS_API_TOKEN`

### Mitä Claude ei todennäköisesti tee

- Ei luo oikeita testejä — vain tyhjät testirakenteet / esimerkit
- Ei luo `infra/`-kansiota (Bicep) — se on erikseen
- Ei aja `dotnet restore` / `npm install` — komennot ovat vain ohjeissa