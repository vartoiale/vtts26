# Ohje: debian 13 (koodinimi: trixie) asennus

Seuraavissa ohjeissa neuvotaan debian 13 (trixie) asennus. 

Huomaa, että laitteiden nimien, salasanojen ja käyttäjätunnusten osalta jaetaan luokassa erilliset ohjeet.

## Asennusta varten tarvitset

### Asennukseen tarvittavat laitteet:

* palvelin, jolle asennat debianin
* tietokone, jonka pääkäyttäjäoikeudet sinulla on (paikalliset pääkäyttäjäoikeudet tarvitaan rufus:ta varten)
* fyysinen usb-tikku asennusta varten

### Asennusta varten tarvittavat ohjelmat

* rufus (windows) - rufus.ie - ohjelma usb-tikun valmistelemiseksi asennusmediaksi

Jos luot asennusmedian linuxissa, joudut käyttämään jotain toista ohjelmaa, todennäköisesti dd-ohjelmaa. 
Tämä ohje käsittelee kuitenkin vain windowsissa tapahtuvan asennusmedian luonnin.

### Asennusta varten tarvittavat asennustiedostot

* Debianin ISO-tiedosto

## Asennusmedian valmistelu

### ISO-tiedoston lataus

Tarvitset asennusta varten ISO-tiedoston.

* lataa `debian-13.6.0-amd64-DVD-1.iso` osoitteesta https://cdimage.debian.org/debian-cd/current/amd64/iso-dvd/ (löytyy sivun lopusta).

Tiedosto on noin 4gb kokoinen, ja sen lataamisessa saattaa kestää tovi hitaammilla yhteyksillä.

### Rufus:in asentaminen (windows)

Lataa rufus sen omilta [rufus.ie](https://rufus.ie/en/) -sivuilta.

**Lataa rufus vain sen omilta sivuilta. Älä lataa sitä kolmansien osapuolien sivuilta.** Vaihto

### USB-tikun valmisteleminen asennusmediaksi

Käynnistä edellisessä vaiheessa lataamasi rufus.

Seuraa rufus:in sivuilta löytyviä ohjeita asennusmedian luomiseksi:

* "boot selection"-kohtaan valitse aiemmin lataamasi debianin ISO-tiedosto kovalevyltäsi.
* seuraa ohjeita rufusin sivuilta.
* käynnistä asennusmedian luonti.
* odota asennusmedian luonnin valmistumista.

## Debianin asennus

### Asennuksen aloitus

1. Laita luomasi usb-tikku palvelimeen.
2. Käynnistä palvelin.

Jos kaikki meni hyvin, sinun pitäisi nähdä "Debian 13"-teksti oikeassa alakulmassa.

Jos et näe debian-tekstiä, tai esimerkiksi windows tai jokin muu käyttöjärjestelmä käynnistyy, pitää sinun mahdollisesti vaihtaa palvelimesi käynnistysosioiden järjestystä. 
Käynnistyksen järjestys vaihdetaan biosista. Miten biosin asetuksiin päästään, vaihtelemaan laitteen mukaan. Voit selvittää tätä hakemalla hakukoneesta tai kysymällä tekoälyltä tätä laitteesi nimellä.

### Asennus

1. Aloita asennus valitsemalla "debian 13"-näkymässä "graphical install"

Odota, että varsinainen asennusohjelma käynnistyy. Laitteesta riippuen tässä voi kestää hetki.

#### "Select a language" -näkymä

1. Valitse kieleksi: "finnish - suomi", ja klikkaa "jatka"-nappia.

2. Seuraavassa näkymässä ohjelma kysyy: "Jatketaanko asennusta valitulla kielellä?" - Valitse tähän "Kyllä"

#### "Valitse olinpaikkasi" -näkymä

1. Valitse alueeksi: "suomi", ja klikkaa "jatka"-nappia.

#### "Tee näppäimistön asetukset" -näkymä

1. Valitse käytettäväksi näppäinkartaksi: "suomalainen", ja klikkaa "jatka"-nappia.

Odota hetki asennuksen edetessä.

#### "Tee verkkoasetukset" -näkymä

Mahdollisesti ennen tätä näkymää, asennusohjelma saattaa herjata, että DHCP-yhteyden alustus epäonnistui. Älä välitä tästä.

1. Valitse verkkoasetuksen tekotapa: "älä tee verkkoasetuksia tällä kertaa", ja klikkaa "jatka"-nappia.

   * Asetamme verkkoasetukset vasta asennuksen jälkeen.

2. Seuraavaksi ohjelma kysyy konenimeä. Jos olet asentamassa palvelinta kotiverkossa, voit valita haluamasi nimen. Jos asennat konetta luokassa, selvitä koneellesi määritetty konenimi.

#### "Luo käyttäjätunnukset ja salasanat" -näkymä

1. Aseta pääkäyttäjän salasana kahteen kertaan, ja klikkaa "jatka"-nappia.
   * jos teet asennusta kotona, voit päättää salasanan itse,
   * Jos teet asennusta luokassa, konsultoi erillisiä ohjeita salasanan osalta.

2. Aseta uuden käyttäjän koko nimi, ja klikkaa "jatka"-nappia.
   * jos teet asennusta kotona, voit päättää käyttäjänimen, ei kuitenkaan kannata antaa oikeaa omaa nimeään tietoturvasyistä. "user" on aina hyvä valinta.
   * jos teet asennusta luokassa, konsultoi erillisiä ohjeita koko nimen osalta.
  
3. Aseta käyttäjätunnus, ja klikkaa "jatka"-nappia.
   * jos teet asennusta kotona, voit päättää käyttäjänimen, ei kuitenkaan kannata antaa oikeaa omaa nimeään tietoturvasyistä. "user" on aina hyvä valinta.
   * jos teet asennusta luokassa, konsultoi erillisiä ohjeita koko nimen osalta.

4. Aseta luomasi uuden käyttäjän salasana kahteen kertaan, ja klikkaa "jatka"-nappia.
   * jos teet asennusta kotona, voit päättää salasanan itse,
   * Jos teet asennusta luokassa, konsultoi erillisiä ohjeita salasanan osalta.

Odota hetki asennuksen edetessä.

#### "Tee levyosiot" -näkymä

Seuraava vaihe on vaarallinen, koska se tyhjentää palvelinkoneelta mahdollisesti kaiken aiemman sisällön.

Tarkista vielä tässä vaiheessa, että:

1. olet tekemässä asennuksen erillisellä palvelinkoneella, etkä esim. normaalilla läppärilläsi.
2. olet tallentanut kaiken mahdollisen palvelinkoneen kovalevyllä olevan tärkeän tiedon jollekin muulle medialle. 

Jos olet varma siitä mitä olet tekemässä, jatka asennusta:

1. Valitse _pakotetaanko UEFI-asennus_https://rufus.ie/en/: "Kyllä", ja klikkaa "jatka"-nappia.

Odota hetki asennuksen edetessä.

2. Valitse _levyn osiointitapa_: "ohjattu - käytä koko levyä", ja klikkaa "jatka"-nappia.
3. Valitse _osioitava levy_: se levy, joka ei ole usb-tikkusi. Kysy tässä kohtaa mahdollisesti apua.
4. Valitse _levyn osiointimalli_: "vain yksi levyosio (suositellaan uusille käyttäjille)"
5. Valitse seuraavassa näkymässä: "lopeta osioiden teko ja tallenna muutokset levylle"
6. Valitse _tallennetaanko muutokset levylle_: "kyllä" - tämä on se kohta, jonka jälkeen levyllä oleva aiempi data ylikirjoitetaan.

Odota hetki asennuksen edetessä.

#### "Tee pakettienhallintaohjelman asetukset" -näkymä

1. Valitse _käytetäänkö asennuspalvelimen kopiota_: "ei", ja klikkaa "jatka"-nappia.

Odota hetki asennuksen edetessä.

#### "popularity-contest:n asetusten säätö" -näkymä

1. Valitse _osallistutko debianin pakettimittariin_: "ei", ja klikkaa "jatka"-nappia. - tässä voit halutessasi valita myös kyllä. Valitsemme tällä kertaa "ei"-vaihtoehdon, koska saatamme tehdä harjoitusmielessä hassuja asioita.

Odota hetki asennuksen edetessä.

#### "ohjelmavalikoima" -näkymä

1. Valitse _asennettavista ohjelmista_ vain seuraavat (ota ruksi pois muista):
   a. "debian desktop environment"
   b. "xfce"
   c. "web server"
   d. "ssh server"
   e. "vakiot järjestelmätyökalut" 
2. klikkaa "jatka"-nappia.

Odota hetki asennuksen edetessä.

#### "Päätä asennus" -näkymä

1. Poista asennusmedia (usb-tikku) koneesta,
2. klikkaa "jatka"-nappia, käynnistääksesi palvelin uudelleen.

Kone käynnistyy uudelleen.

## Ensimmäinen käynnistys debian-linuxiin

Riippuen palvelimesi tehoista, asennuksessa saattaa kestää hetki. Odota rauhallisesti käynnistymistä.

### Kirjautuminen

Koneesi käynnistyttyä kirjautumisnäkymään, kirjaudu koneelle käyttäen käyttäjätunnusta ja sitä vastaavaa salasanaa, jotka määritit asennuksen aikana.

Jos olet unohtanut määrittämäsi salasanan, ei hätää, voit tehdä asennuksen uudelleen alusta. 
Varmista kuitenkin tällöin biosista, että käynnistysjärjestyksessä usb-tikkusi on debian-asennusta edellä, jotta saat aloitettua asennuksen uudestaan.

## Seuraavaksi: Ohje: Debianin `apt`-komentorivityökalu

Seuraavaksi jatketaan tutustumalla debianin `apt`-komentorivityökaluun, jolla asennetaan ohjelmia debian-pohjaisissa linux-käyttöjärjestelmissä: [ohje: apt](./debian-apt.md) 
