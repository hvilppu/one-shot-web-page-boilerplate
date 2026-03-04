# OneShot testausohjeet

Tämä ohje kertoo, miten rakennat testauksen kolmella tasolla:

- Käyttöliittymän yksikkötestit
- Taustapalvelun yksikkötestit
- End-to-end-testit (E2E)

Ohje on tarkoitettu OneShot-projektiin niin, että voit ottaa sen käyttöön vaiheittain.

## 1) Testausstrategia lyhyesti

Pidä testit pyramidimallissa:

1. Eniten yksikkötestejä (nopeita)
2. Vähemmän integraatio-/rajapintatestejä
3. Vähiten E2E-testejä (hitaampia, mutta kriittisiä)

Suositus OneShotille:

- 70% yksikkötestit
- 20% integraatio-/API-testit
- 10% E2E-testit

## 2) Käyttöliittymän yksikkötestit (Vue)

### Suositellut työkalut

- `vitest`
- `@vue/test-utils`
- `jsdom`

### Asennus

Aja frontend-kansiossa:

```powershell
cd frontend
npm install -D vitest @vue/test-utils jsdom
```

### Skriptit `package.json`-tiedostoon

```json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage"
  }
}
```

### Suositeltu testirakenne

```text
frontend/
├─ src/
│  ├─ components/
│  ├─ services/
│  └─ stores/
└─ tests/
   ├─ unit/
   │  ├─ components/
   │  ├─ services/
   │  └─ stores/
   └─ setup.ts
```

### Mitä testata käyttöliittymässä

- Komponentin renderöinti eri syötteillä
- Painikkeiden ja lomakkeiden toiminta
- Virhe- ja lataustilojen näyttö
- API-kutsuja käyttävien palveluiden logiikka (mockeilla)
- Tilanhallinnan toiminnot

### Esimerkkiajo

```powershell
cd frontend
npm run test
npm run test:coverage
```

## 3) Taustapalvelun yksikkötestit (C#/.NET)

### Suositellut työkalut

- `xUnit`
- `FluentAssertions`
- `Moq` (tai `NSubstitute`)

### Testiprojektin luonti

Aja backend-kansiossa:

```powershell
cd backend
dotnet new xunit -n OneShot.UnitTests -o tests/OneShot.UnitTests
```

Lisää viittaukset testattavaan projektiin ja testikirjastoihin:

```powershell
dotnet add ./tests/OneShot.UnitTests/OneShot.UnitTests.csproj reference ./src/OneShot.Application/OneShot.Application.csproj
dotnet add ./tests/OneShot.UnitTests/OneShot.UnitTests.csproj package FluentAssertions
dotnet add ./tests/OneShot.UnitTests/OneShot.UnitTests.csproj package Moq
```

### Suositeltu testirakenne

```text
backend/
├─ src/
│  ├─ OneShot.Functions/
│  ├─ OneShot.Application/
│  ├─ OneShot.Domain/
│  └─ OneShot.Infrastructure/
└─ tests/
   └─ OneShot.UnitTests/
      ├─ Application/
      ├─ Domain/
      └─ Common/
```

### Mitä testata taustapalvelussa

- Sovelluslogiikan säännöt
- Syötteiden validointi
- Virhepolut ja poikkeukset
- Muunnokset (DTO <-> domain)
- Aikarajat / reunaehdot

### Esimerkkiajo

```powershell
cd backend
dotnet test ./tests --configuration Release
```

## 4) End-to-end-testit (E2E)

### Suositellut työkalut

- `Playwright`

### Asennus

Aja frontend-kansiossa (tai erillisessä e2e-kansiossa):

```powershell
cd frontend
npm install -D @playwright/test
npx playwright install
```

### Suositeltu rakenne

```text
frontend/
└─ tests/
   └─ e2e/
      ├─ auth.spec.ts
      ├─ main-flow.spec.ts
      └─ error-handling.spec.ts
```

### Mitä testata E2E-tasolla

- Sovelluksen avautuminen ja reititys
- Kirjautuminen (jos käytössä)
- Kriittinen päätöksestä loppuun -polku
- Virhetilanne käyttäjän näkökulmasta
- Onnistunut luku- ja kirjoituspolku APIin

### Esimerkkiajo

```powershell
cd frontend
npx playwright test
npx playwright test --headed
```

## 5) Testidatan ja ympäristöjen hallinta

- Käytä erillistä testitietokantaa
- Älä käytä tuotantodataa testeissä
- Tee testidatan siivous jokaisen ajon jälkeen
- Pidä testit deterministisinä (ei satunnaisia riippuvuuksia)

## 6) CI-putken minimivaatimukset

Aja vähintään nämä jokaisessa pull requestissa:

1. Taustapalvelun build + unit-testit
2. Käyttöliittymän build + unit-testit
3. E2E-testit vähintään smoke-joukolle

Suositus aikarajoille:

- Unit-testit < 3 min
- E2E smoke < 10 min

## 7) Valmis testauksen tarkistuslista

- [ ] Käyttöliittymän unit-testit määritetty
- [ ] Taustapalvelun unit-testit määritetty
- [ ] E2E-runko määritetty
- [ ] Testikomennot lisätty skripteihin
- [ ] CI ajaa testit automaattisesti
- [ ] Vähintään yksi testi per kriittinen toiminto

## 8) OneShotin ensimmäinen testipaketti (suositus)

Aloita näistä 6 testistä:

1. Käyttöliittymä: kirjautumislomake renderöityy oikein
2. Käyttöliittymä: virheilmoitus näkyy epäonnistuneessa API-kutsussa
3. Taustapalvelu: validointi estää virheellisen syötteen
4. Taustapalvelu: onnistunut käsittely palauttaa odotetun DTO:n
5. E2E: käyttäjä avaa sovelluksen ja näkee etusivun
6. E2E: käyttäjän kriittinen pääpolku onnistuu loppuun asti
