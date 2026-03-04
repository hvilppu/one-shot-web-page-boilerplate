# Tietokannan (MSSQL) ohje

## Tietokannan nimeäminen

- Tietokanta: `OneShotDb`
- Skeema: `dbo` (tai domain-kohtainen)

## Yhteysmerkkijonon malli

```text
Server=localhost;Database=OneShotDb;User Id=sa;Password=YourStrong!Passw0rd;TrustServerCertificate=True;
```

## Suositellut aloitustaulut

- `Users`
- `Items`
- `AuditLogs`

## Migraatiotyönkulku (EF Core)

```powershell
cd backend
dotnet ef migrations add InitialCreate --project src/OneShot.Infrastructure --startup-project src/OneShot.Api
dotnet ef database update --project src/OneShot.Infrastructure --startup-project src/OneShot.Api
```

TODO: päivitä `--project` ja `--startup-project` polut, jos EF Core -DbContext sijaitsee eri projektissa.

## SQL Server -tarkistukset

- [ ] SQL Server -palvelu on käynnissä
- [ ] Kirjautumistiedot ovat kelvolliset
- [ ] Tietokanta on luotu
- [ ] Palomuuri- ja verkkopääsy on konfiguroitu
- [ ] Tuotantoon on käytössä vähimmän oikeuden DB-käyttäjä

## Suorituskyvyn perusteet

- Lisää indeksit usein käytetyille WHERE/JOIN-kentille
- Vältä `SELECT *` -kyselyjä tuotannossa
- Lisää sivutus listaus-endpointeille
- Seuraa hitaita kyselyitä ja suorituskykysuunnitelmia
