# Käyttöliittymän (Vue) ohje

## Suositeltu projektirakenne

```text
frontend/
├─ src/
│  ├─ components/
│  ├─ views/
│  ├─ services/
│  ├─ router/
│  └─ stores/
└─ public/
```

## Keskeiset vastuut

- Renderöi käyttöliittymä
- Kutsu taustapalvelun APIa palvelukerroksen kautta
- Hallitse sovelluksen tilaa
- Käsittele lataus- ja virhetilat selkeästi

## Tyypilliset komennot

```powershell
cd frontend
npm install
npm run dev
npm run build
```

## API-integraation malli

Luo yksi API-asiakas, joka käyttää ympäristökonfiguraatiota:

```ts
const baseUrl = import.meta.env.VITE_API_BASE_URL;
```

## Käyttöliittymän tarkistuslista

- [ ] Ympäristötiedosto konfiguroitu
- [ ] API-asiakas keskitetty
- [ ] Reittisuojaukset suojatuille sivuille (tarvittaessa)
- [ ] Lomakevalidointi lisätty
- [ ] Virheilmoitukset lisätty
