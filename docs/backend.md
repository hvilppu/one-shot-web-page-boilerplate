# Taustapalvelun (C# Azure Functions) ohje

## Suositeltu projektirakenne

```text
backend/
├─ src/
│  ├─ OneShot.Functions/
│  ├─ OneShot.Application/
│  ├─ OneShot.Domain/
│  └─ OneShot.Infrastructure/
└─ tests/
```

## Keskeiset vastuut

- Tarjoa HTTP-triggeröidyt Azure Functions -päätepisteet
- Käsittele liiketoimintalogiikka sovelluskerroksessa
- Käytä EF Corea tai Dapperia MSSQL-yhteyksiin
- Palauta DTO-objektit käyttöliittymän käyttöön

## Tyypilliset komennot

```powershell
cd backend
dotnet restore
dotnet build ./src/OneShot.Functions
dotnet test ./tests
cd src/OneShot.Functions
func start
```

Vaatii: Azure Functions Core Tools (`npm install -g azure-functions-core-tools@4 --unsafe-perm true`)

TODO: jos käytät ratkaisutasoa (`.sln`) tai eri projektinimiä, päivitä komennot vastaamaan toteutusta.

## Tietokantayhteys

- Määritä `DefaultConnection` asetuksiin
- Pidä SQL/migraatiot versionhallinnassa
- Lisää retry- ja timeout-asetukset tietokantakutsuihin

## Function App -tarkistuslista

- [ ] Terveystarkistuksen HTTP-funktio saatavilla
- [ ] Autentikointi konfiguroitu (tarvittaessa)
- [ ] Validointi ja virheenkäsittely standardoitu
- [ ] Lokitus käytössä
- [ ] CORS sallii käyttöliittymän alkuperäosoitteet

## Esimerkki CORS-käytännöstä (isolated worker)

```csharp
var host = new HostBuilder()
    .ConfigureFunctionsWorkerDefaults(worker =>
    {
        worker.UseFunctionExecutionMiddleware();
    })
    .ConfigureServices(services =>
    {
        services.AddCors(options =>
        {
            options.AddPolicy("Frontend", policy =>
                policy.WithOrigins("http://localhost:5173")
                      .AllowAnyHeader()
                      .AllowAnyMethod());
        });
    })
    .Build();
```
