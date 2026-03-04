# OneShot GitHub CI/CD -ohje

Tämä ohje tarjoaa käytännöllisen GitHub Actions -asetuksen OneShotille:

- Vue-käyttöliittymä (Node.js)
- C#-taustapalvelu (Azure Functions, isolated worker)
- MSSQL-integraatiotestit (valinnainen)
- Julkaisuartefaktien paketointi julkaisua varten

## Suositeltu työnkulkurakenne

```text
.github/
└─ workflows/
   ├─ ci.yml
   └─ cd.yml
```

## CI-työnkulku (`ci.yml`)

Käytä CI:tä pull requestien ja main-haaran committien validointiin.

### Suositellut käynnistimet

- `pull_request` haaraan `main`
- `push` haaraan `main`

### Mitä CI:n tulee tehdä

1. Palauta riippuvuudet ja rakenna taustapalvelu
2. Aja taustapalvelun yksikkötestit
3. Asenna riippuvuudet ja rakenna käyttöliittymä
4. Aja käyttöliittymän yksikkötestit
5. (Valinnainen) Aja E2E smoke-testit
6. Lataa build-julkaisuartefaktit

### Minimaalinen CI-esimerkki

```yaml
name: CI

on:
  pull_request:
    branches: [ main ]
  push:
    branches: [ main ]

jobs:
  build-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.0.x'

      - name: Restore backend
        run: dotnet restore ./backend/src/OneShot.Functions

      - name: Build backend
        run: dotnet build ./backend/src/OneShot.Functions --configuration Release --no-restore

      - name: Test backend
        run: dotnet test ./backend/tests --configuration Release

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
          cache-dependency-path: ./frontend/package-lock.json

      - name: Install frontend deps
        working-directory: ./frontend
        run: npm ci

      - name: Build frontend
        working-directory: ./frontend
        run: npm run build

      - name: Test frontend
        working-directory: ./frontend
        run: npm run test

      - name: Publish backend
        run: dotnet publish ./backend/src/OneShot.Functions --configuration Release --output ./backend-publish

      - name: Upload backend artifact
        uses: actions/upload-artifact@v4
        with:
          name: backend-publish
          path: ./backend-publish

      - name: Upload frontend artifact
        uses: actions/upload-artifact@v4
        with:
          name: frontend-dist
          path: ./frontend/dist
```

## CD-työnkulku (`cd.yml`)

Käytä CD:tä julkaisuun vasta, kun CI onnistuu.

### Suositellut käynnistimet

- `workflow_dispatch` manuaaliseen julkaisuun
- `push` haaraan `main` (jos automaattinen julkaisu on haluttu)

### Mitä CD:n tulee tehdä

1. Lataa CI-julkaisuartefaktit tai rakenna lähdekoodista
2. Julkaise taustapalvelun ajoympäristö
3. Julkaise käyttöliittymän staattiset tiedostot
4. Aja smoke-tarkistukset

### Minimaalinen CD-runko

```yaml
name: CD

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Download frontend artifact
        uses: actions/download-artifact@v4
        with:
          name: frontend-dist
          path: ./frontend-dist

      - name: Deploy steps
        run: |
          echo "Add backend deployment command here"
          echo "Add frontend deployment command here"
```

TODO: jos käytössä on ratkaisutiedosto (`.sln`) tai eri kansiorakenne, päivitä CI-komennot vastaamaan toteutusta.

## Secretit ja muuttujat

Konfiguroi GitHub-repon asetuksissa:

- `AZURE_CREDENTIALS` (jos julkaiset Azureen)
- `SQL_CONNECTION_STRING` (jos käytössä julkaisussa)
- `JWT_KEY`
- `VITE_API_BASE_URL`

Käytä GitHub **Environments** -ympäristöjä (`dev`, `staging`, `production`) hyväksyntäporteille ja ympäristökohtaisille salaisuuksille.

## Haarautus- ja julkaisustrategia

- Suojaa `main`-haara
- Vaadi PR + CI -tarkistukset ennen yhdistämistä
- Tägää julkaisut (`v1.0.0`, `v1.1.0`)
- Aja tuotannon CD vain tageista tai manuaalisella hyväksynnällä

## MSSQL CI:ssä (valinnaiset integraatiotestit)

Voit ajaa SQL Serverin palvelukonttina integraatiotesteille.

```yaml
services:
  sql:
    image: mcr.microsoft.com/mssql/server:2022-latest
    env:
      ACCEPT_EULA: Y
      SA_PASSWORD: YourStrong!Passw0rd
    ports:
      - 1433:1433
```

Aseta sen jälkeen testiyhteysmerkkijono työnkulun ympäristöön:

```yaml
env:
  ConnectionStrings__DefaultConnection: Server=localhost,1433;Database=OneShotDb;User Id=sa;Password=YourStrong!Passw0rd;TrustServerCertificate=True;
```

## OneShot CI/CD -tarkistuslista

- [ ] CI ajetaan pull requesteille
- [ ] Taustapalvelun build ja testit menevät läpi
- [ ] Käyttöliittymän build menee läpi
- [ ] Julkaisuartefaktit generoidaan
- [ ] CD käyttää suojattua ympäristöä
- [ ] Salaisuudet tallennetaan vain GitHub Secrets/Environments -kohtiin
- [ ] Smoke-testit ajetaan julkaisun jälkeen
