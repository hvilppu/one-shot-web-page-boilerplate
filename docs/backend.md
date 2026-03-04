# Taustapalvelun (C#) ohje

## Suositeltu projektirakenne

```text
backend/
├─ src/
│  ├─ OneShot.Api/
│  ├─ OneShot.Application/
│  ├─ OneShot.Domain/
│  └─ OneShot.Infrastructure/
└─ tests/
```

## Keskeiset vastuut

- Tarjoa REST-päätepisteet tai HTTP-triggeröidyt funktiot
- Käsittele liiketoimintalogiikka sovelluskerroksessa
- Käytä EF Corea tai Dapperia MSSQL-yhteyksiin
- Palauta DTO-objektit käyttöliittymän käyttöön

## Tyypilliset komennot

```powershell
cd backend
dotnet restore
dotnet build ./src/OneShot.Api
dotnet test ./tests
dotnet run --project src/OneShot.Api
```

TODO: jos käytät ratkaisutasoa (`.sln`) tai eri projektinimiä, päivitä komennot vastaamaan toteutusta.

## Tietokantayhteys

- Määritä `DefaultConnection` asetuksiin
- Pidä SQL/migraatiot versionhallinnassa
- Lisää retry- ja timeout-asetukset tietokantakutsuihin

## API-tarkistuslista

- [ ] Terveystarkistuksen päätepiste saatavilla
- [ ] Autentikointi konfiguroitu (tarvittaessa)
- [ ] Validointi ja virheenkäsittely standardoitu
- [ ] Lokitus käytössä
- [ ] CORS sallii käyttöliittymän alkuperäosoitteet

## Esimerkki CORS-käytännöstä

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("Frontend", policy =>
        policy.WithOrigins("http://localhost:5173")
              .AllowAnyHeader()
              .AllowAnyMethod());
});
```
