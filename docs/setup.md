# Asennusohje

Tämän ohjeen avulla saat one-shot C# + Vue + MSSQL -sovelluksen käyntiin paikallisesti.

## Vaatimukset

- .NET SDK asennettuna (`dotnet --version`)
- Node.js + npm asennettuna (`node -v`, `npm -v`)
- SQL Server käynnissä (paikallinen, kontti tai etäpalvelin)
- Valinnainen: SSMS tai Azure Data Studio

## 1) Kloonaa ja valmistele

```powershell
git clone <your-repo-url>
cd one-shot-web-page-boilerplate
```

## 2) Taustapalvelun konfigurointi

Luo `appsettings.Development.json` tai käytä ympäristömuuttujia.

Esimerkkimuotoinen yhteysmerkkijono:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=OneShotDb;User Id=sa;Password=YourStrong!Passw0rd;TrustServerCertificate=True;"
  }
}
```

## 3) Käyttöliittymän konfigurointi

Luo `.env` käyttöliittymäprojektiin:

```env
VITE_API_BASE_URL=http://localhost:5000
```

## 4) Käynnistä molemmat palvelut

Taustapalvelu:

```powershell
cd backend
dotnet restore
cd src/OneShot.Functions
func start
```

Käyttöliittymä:

```powershell
cd frontend
npm install
npm run dev
```

## 5) Varmista toimivuus

- Taustapalvelun terveystarkistuksen päätepiste vastaa (esim. `/health`)
- Käyttöliittymä latautuu selaimessa
- API-kutsut onnistuvat käyttöliittymästä taustapalveluun
- Datan luku/kirjoitus MSSQL:ään onnistuu

TODO: päivitä `src/OneShot.Functions`-polku, jos backendin projekti on nimetty toisin. Vaatii Azure Functions Core Tools (`func`).
