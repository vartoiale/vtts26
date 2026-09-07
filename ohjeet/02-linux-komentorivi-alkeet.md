# Ohje: linuxin komentorivin alkeet

Näissä ohjeissa tutustutaan liikkumiseen linuxin hakemistojärjestelmässä, ja tarkastellaan yleisiä tiedosto- ja kansio-operaatioita.

## Lista komennoista

Alla on esitelty komentorivitulkissa käytettyjä komentoja. Huomaa syntaksiin liittyen seuraavat seikat:

* `[...]` - hakasulkeet (`[` ja `]`) eivät ole osa komentoa, eikä niitä siis kirjoiteta komentoriville. Sen sijaan ne kuvaavat lippuja tai parametreja, jotka eivät ole pakollisia.
* `<...>` - nuolilla (`<` ja `>`) ja niiden sisällöllä kuvataan parametria, ja kerrotaan minkälainen arvo parametrina pitäisi komennolle antaa. Niitäkään ei siis kirjoiteta osaksi komentoa.

Lista käsitellyistä komennoista:

* `pwd` - näytä nykyinen polku, eli siis se sijainti tiedostojärjestelmässä, joka meillä on auki komentorivitulkissa nyt, eli se sijainti, jossa olemme `pwd`-komentoa kirjoittaessa.
* `cd <kohde>` - siirrytään kohteeseen tiedostojärjestelmässä. katso alta esimerkkejä.
  * `cd <hakemiston nimi>` - siirrytän parametrina annettuun hakemistoon.
  * `cd ..` - siirry nykyisen hakemiston vanhempaan (esim. jos nykyinen hakemisto on `~/Lataukset`, tällöin siirrytään `~/`-hakemistoon)
  * `cd /` - siirry tiedostojärjestelmän juureen (juuri on vähän kuin `c:/`-asema windows-koneessa.
  * `cd ~` - siirry nykyisen käyttäjän kotihakemistoon.
* `ls` - listaa nykyisen sijainnin kansion sisältö, eli siis ne tiedostot ja kansiot, jotka löytyvät siitä kansiosta, jossa komentoa kirjoittaessa ollaan.
  * `ls` - näytä nykyisen hakemiston näkyvien tiedostojen nimet horisontaalisena listana
  * `ls -la` - näytä nykyisen hakemiston sisältö, mutta käytä lippuja `l` (kerro kansiosta pitkästi) ja `a` (näytä kaikki tiedostot, myös piilotiedostot).
     Näyttää jokaisen tiedoston ja hakemiston omalla rivillään, ja kertoo jokaisesta paljon tietoa, mm. onko kyse kansiosta vai tiedostosta, oikeudet, omistajat ja viimeisen muokkauspäivän.
  * `ls <sijainti>` - näytä sijaintina annetun hakemiston sisältö.
* `man <komennon nimi>` - näytä ohjesivu (engl. manual) parametrina annetusta komennosta. Voit sulkea avautuvan ohjesivun `q`-näppäimellä. 
* `mkdir <uuden hakemiston nimi>` - luo uusi hakemisto antamallasi nimellä
* `cat <tiedoston nimi>` - tulosta tiedoston tekstuaalinen sisältö komentoriville. Sopii lyhyiden tiedostojen lukemiseen. 
* `less <tiedoston nimi>` - avaa tiedosto katseluohjelmaan, jossa voit liikkua ylös ja alas nuolinäppäimillä. Sopii pitkien tiedostojen lukemiseen.
   Voit sulkea avautuvan näkymän `q`-näppäimellä. 
* `nano <tiedoston nimi>` - Avaa tiedosto nano-tekstieditorilla muokkausta varten.
   Nano on yksinkertainen komentorivipohjainen tekstieditori. Sitä voi käyttää vaikka ssh-yhteyden yli, koska se ei käytä ns. graafista käyttöliittymää.
   Ohjeet tallenukseen ja muihin toimintoihin näkyvät nano:ssa alarivissä. `^<kirjain>` tarkoittaa näppäinyhdistelmää `ctrl + <kirjain>`. `^`-merkkiä kutsutaan "hatuksi".
   Voit tallentaa muokkaukset `ctrl + O`-näppäinyhdistelmällä, jonka jälkeen syötetään tiedoston nimi, enterin hyväksyessä tiedoston nimen.
   Voit sulkea nanon `ctrl + X`-näppäinyhdistelmällä. 
* `mv <tiedoston alkuperäinen nimi> <tiedoston uusi nimi>` - siirrä tiedosto uuteen paikkaan. Toimii myös tiedoston uudelleennimeämiseen, jos sijainti pysyy samana.
   * esim. `mv tiedosto.txt tiedosto.md` - nimeä tiedosto uudelleen samassa hakemistossa.
   * esim. `mv tiedosto.txt harjoitus1/tiedosto.txt` - siirrä tiedosto uuteen paikkaan `harjoitus1`-hakemiston alle, mutta älä vaihda tiedoston nimeä. 
* `cp <tiedoston nimi> <kopion nimi>` - kopioi tiedosto uuteen paikkaan tai uudella nimellä.
* `touch <tiedoston nimi>` - luo uusi tiedosto annetulla nimellä. 
* `rm <tiedoston nimi>` - poista tiedosto
   * `rm -r <tiedoston nimi>` - poista kansio ja kaikki sen sisältö. HUOM! VAARALLINEN KOMENTO! Tarkista aina, että kansion nimi on kirjoitettu oikein.

Tarvitset näitä komentoja, komentorivillä liikkumiseen ja työskentelyyn.

Kyseisten komentojen hallinta on esitietona seuraavissa ohjeissa.

## Seuraavaksi: `apt`- js `sudo`-ohjeet

Seuraavaksi [apt- ja sudo-ohjeet](./03-debian-apt.md)
