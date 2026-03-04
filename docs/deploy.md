# Käynnistyksen ja julkaisun tarkistuslista

## Paikallisen ajon tarkistuslista

- [ ] Taustapalvelu käynnistyy ilman virheitä
- [ ] Käyttöliittymä käynnistyy ilman virheitä
- [ ] Yhteys MSSQL:ään toimii
- [ ] API:n ydinkohdat testattu
- [ ] Käyttöliittymän ja taustapalvelun integraatio testattu

## Ennen julkaisua -tarkistuslista

- [ ] Tuotannon ympäristömuuttujat asetettu
- [ ] Turvallinen salaisuuksien hallinta käytössä
- [ ] Yhteysmerkkijonoja ei ole kovakoodattu
- [ ] Lokitus ja valvonta käytössä
- [ ] Migraatiot ajettu kohdeympäristössä

## One-Shot-julkaisun vaiheet

1. Rakenna taustapalvelu (`dotnet publish`)
2. Rakenna käyttöliittymä (`npm run build`)
3. Julkaise taustapalvelu/function
4. Julkaise käyttöliittymän staattiset tiedostot
5. Aja smoke-testit
6. Vahvista DB-yhteys ja keskeiset työnkulut

Esimerkkikomento taustapalvelun julkaisuun:

```powershell
dotnet publish ./backend/src/OneShot.Api --configuration Release
```

TODO: päivitä julkaistava projektipolku, jos backendin käynnistysprojekti on nimetty toisin.

## Julkaisun jälkeinen validointi

- [ ] Terveystarkistuksen päätepiste palauttaa OK
- [ ] Kirjautuminen/ydinpolku toimii
- [ ] Kriittiset luku/kirjoitusoperaatiot tietokantaan toimivat
- [ ] Loki ei sisällä merkittäviä virheitä

## Palautussuunnitelma

- Pidä edellinen taustapalvelun julkaisuartefakti saatavilla
- Pidä edellinen käyttöliittymän build saatavilla
- Ota tietokannasta snapshot/varmuuskopio ennen skeemamuutoksia
- Määritä palautuksen vastuuhenkilö ja päätöskriteerit
