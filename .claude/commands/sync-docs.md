# OneShot dokumentaation synkronointi

Tarkista, että dokumentaatio on ajan tasalla suhteessa tähän projektiin. Käy vaiheet läpi järjestyksessä ja tee tarvittavat päivitykset tiedostoihin.

## 1) Tarkistettavat tiedostot

- `README.md`
- `docs/setup.md`
- `docs/backend.md`
- `docs/frontend.md`
- `docs/database.md`
- `docs/deploy.md`
- `docs/testing.md`
- `docs/github-cicd.md`
- `docs/bicep.md`
- `docs/architecture.mmd`

## 2) Pakolliset sisällön tarkistukset

### README

- Projektin kuvaus vastaa nykyistä toteutusta.
- `docs/`-rakenne sisältää kaikki olemassa olevat dokumentit.
- Pika-aloituksen linkit toimivat.
- PowerShell-ajokomento on oikein.

### Setup / Backend / Frontend / Database

- Komennot vastaavat nykyistä kansiorakennetta.
- Riippuvuudet ja työkalut ovat oikein nimetty.
- Terminologia on yhtenäinen: `taustapalvelu`, `käyttöliittymä`, `päätepiste`.

### Testing

- Ohjeissa on kolme tasoa: frontend unit, backend unit, e2e.
- Testikomennot ovat ajettavia ja johdonmukaisia.

### CI/CD

- `docs/github-cicd.md` vastaa testiohjeita ja build-vaiheita.
- PR/CI/CD-polku on kuvattu ilman ristiriitoja.

### Bicep / Cloud

- `dev` ja `prod` ympäristöt on kuvattu erikseen.
- Parametritiedostot `main.dev.bicepparam` ja `main.prod.bicepparam` on mainittu.
- Julkaisukomennot ovat erikseen deville ja prodille.

### Arkkitehtuurikaavio

- `docs/architecture.mmd` vastaa dokumentoitua arkkitehtuuria.
- Solmut ja yhteydet vastaavat frontend → backend → database -mallia.

## 3) Synkronointisäännöt

- Älä muuta muuttujien nimiä, komentoja tai polkuja ilman tarvetta.
- Korjaa vain dokumentaatiopoikkeamat, älä lisää ylimääräistä scopea.
- Jos jokin tieto puuttuu koodista, merkitse se “TODO” eikä oletuksia.

## 4) Raportoi lopuksi näin

1. **Päivitetyt tiedostot**
2. **Mitä korjattiin** (lyhyt lista)
3. **Avoimet kohdat / TODOt**
4. **Mahdolliset riskit** (esim. komentoa ei voitu validoida)

## 5) Onnistumiskriteeri

Dokumentaatio on synkassa, kun:

- linkit ja tiedostolistat täsmäävät,
- termit ovat yhtenäiset,
- dev/prod erotus on selkeä,
- testaus- ja CI/CD-ohjeet tukevat toisiaan ilman ristiriitoja.
