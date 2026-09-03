# Ohje: `apt`-työkalun käyttö Debian pohjaisissa järjestelmissä

## Mikä on `apt`?

`apt` on komentorivityökalu, jolla asennetaan (myös päivitetään ja poistetaan) debianin ohjelmistojakelun kautta asennettavat ohjelmat.

Debianin ohjelmistojakelu tarjoaa valmiin paketin monesta yleisestä linux-sovelluksesta. 
Näin on myös wordpress:in osalta.
Käytämme siis `apt`-komentorivityökalua wordpressin asentamiseen.

## Komentorivin käynnistäminen

Jos seurasit aiempaa ohjettamme ([debian13-asennus.md](./debian13-asennus.md)), olet asentanut xfce-käyttöliittymän.

Xfce:ssä ruudun alareunasta löytyy kuuden ohjelman käynnistyspalkki. Komentorivisovellus on näistä toinen vasemmalla (kuvassa musta ruutu, jossa teksti ">_").

Komentorivisyöte on tekstipohjainen käyttöliittymä, jonka kautta voidaan suorittaa komentoriviohjelmia, ja muokata järjestelmän tilaa.

Yleisesti linux-palvelimilla järjestelmän konfigurointi ja ylläpito tapahtuu juurikin komentorivin kautta.
Yksittäiset ohjelmat, kuten wordpress saattavat tarjota graafisen käyttöliittymän, mutta järjestelmän hallinta yleensä tehdään ilman graafista käyttöliittymää.

## root-käyttäjä ja `apt`-työkalun käyttö `sudo`-työkalun kautta

Koska `apt`-työkalulla asennetaan järjestelmään ohjelmia, sen käyttö vaatii yleensä linux-järjestelmässä pääkäyttäjän oikeudet.

Pääkäyttäjäksi voidaan pysyvästi siirtyä komennolla `su -`, mutta koska yleensä riittää suorittaa vain yksittäinen komento pääkäyttäjänä, voidaan komennon alkuun lisätä `sudo` komento.

`sudo` ja `su`-työkalujen nimien alkuperästä (eli etymologiasta):

* `sudo`-komennon nimi tulee sanoista `super user do` tai `substitute user do`, 
* `su`-työkalun nimi tulee sanoista "substitute user".

Käytännössä molemmilla yleensä omaksutaan pääkäyttäjän (engl. superuser) rooli, tilapäisesti (`sudo`) tai pysyvämmin (`su`). 
Yleensä `su`-komentoa käytetään vain erikoistapauksissa, koska sillä on helppoa vahingossa rikkoa asioita.

Esimerkiksi seuraava komento ajaa cat työkalun normaalina käyttäjänä:

```sh
cat /etc/apt/sources.list
```

Kun taas seuraava tekee saman, mutta pääkäyttäjän oikeuksilla:

```sh
sudo cat /etc/apt/sources.list
```

_`sudo` vaatii toimiakseen pääkäyttäjän salasanan. Tämä on se `root`-käyttäjän salasana, jonka asetit asennuksen aikana.
Huomaa, että `sudo` ei näytä tähtiä, tai muita merkkejä, kun syötät salasanaa, vaan joudut syöttämään salasanan ummikkona.
Kirjaimet kuitenkin rekisteröityvät, vaikkeivät ne näykään komentorivisyötteessä.
Taustalla tässä on välttää paljastamasta tietoa salasanastasi mahdollisille hyökkääjille.
Esim. takanasi istuva hyökkääjä voi päätellä jo paljon, jos pääkäyttäjän salasanasi on vain 3 merkkiä pitkä, kuten eräässäkin elokuvassa._

Tässä tapauksessa molemmat komennoista toimivat samalla tavalla, koska normaalilla käyttäjällä on sama pääsy lukemaan komennossa mainittua `/etc/apt/sources.list`-tiedostoa.
Jotain toista komentoa käytettäessä eroja kuitenkin saattaisi olla.

Esimerkiksi `apt` itsessään vaatii pääkäyttäjän oikeudet, eikä seuraava toimi:

```sh
apt update
```

Jotta voimme käyttää `apt`-komentoa, meidän pitää käyttää `sudo`-komentoa sen edellä:

```sh
sudo apt update
```

Jos ajoit kaikki komennot, huomasit varmasti, että `sudo` kysyy ensimmäisellä ajokerralla pääkäyttäjän salasanan, mutta ei enää seuraavilla kerroilla.

### sudo ja "käyttäjänimi ei ole sudoers-tiedostossa"-virheviesti

Jos `sudo`-komentoa käyttäessäsi sait "käyttäjänimi ei ole sudoers-tiedostossa"-virheviestin, sinun pitää lisätä käyttäjä sudo-oikeudelliseksi.

Tämä onnistuu varsin helposti, mutta sisältää hetkellisen, mutta silti vaarallisen root-käyttäjäksi siirtymisen.

Siirry ensin `root`-pääkäyttäjäksi:

```sh
su -
```

Syötä pääkäyttäjän salasana.

#### Lyhyt huomautus pääkäyttäjän tunnuksesta

Nyt komentorivin alussa (vasen reuna, kun et ole vielä mitään kirjoittanut) oleva merkki pitäisi muuttua `$`-merkistä `#`-merkiksi.

* `$`-merkki tarkoittaa normaalia käyttäjää
* `#`-merkki tarkoittaa pääkäyttäjää (pääkäyttäjän rajoittamattomilla oikeuksilla) - VAARA! - nyt kannattaa olla tarkkana.

Ennen näitä merkkejä (merkin vasemmalla puolella) on nykyinen polkusi. 
Jollet ole siirtynyt minnekään, on se luultavasti `~`, joka on lyhenne käyttäjäsi kotihakemistosta.

#### Jatketaan asettamalla käyttäjätunnuksesi sudoers-tiedostoon

Aseta käyttäjänimesi sudoers-ryhmän jäseneksi (vaihda `<käyttäjänimi>`-kohdan tilalle asennuksessa valitsemasi käyttäjänimi):

```sh
usermod -aG sudo <käyttäjänimi>
```

Voit halutessasi lukea `usermod`-työkalun ohjeen komennolla:

```sh
man usermod
```

Saat suljettua manuaalin näppäinkomennolla `q`.

Palaa lopuksi takaisin turvalliseen normaalin käyttäjän maailmaan:

```sh
exit
```

Käyttäjämerkkisi pitäisi olla taas tuttu ja turvallinen `$`. Vaara vältetty. Voit huokaista helpotuksesta.

Jotta, pääsisit käyttämään `sudo`-komentoa, sinun pitää vielä kirjautua ulos käyttäjälläsi, ja kirjautua uudelleen sisään.
Klikkaa oikeasta yläkulmasta käyttäjänimeäsi, ja valitse avautuvasta valikosta "Kirjaudu ulos...", ja sen jälkeen vielä uudemman kerran "Kirjaudu ulos".

Nyt kun kirjaudut takaisin sisään, ja käynnistät komentorivisyötteen, `sudo apt update`-komennon pitäisi onnistua.

## `apt`-työkalun käyttö

`apt`-työkalun yleisin käyttö koostuu seuraavista kolmesta käyttötapauksesta:

* `sudo apt update` - päivitetään tieto uusista ohjelmistoversioista `apt`:iin,
* `sudo apt upgrade` - päivitetään uudet versiot ohjelmista järjestelmään,
* `sudo apt install <ohjelmiston nimi> <toisen ohjelmiston nimi> <kolmannen ohjelmiston nimi>` - asenna yksi tai useampi ohjelma (korvaa `<>` merkinnällä ohjelmiston nimi, jonka haluat asentaa, voit asentaa samalla komennolla niin monta ohjelmaa kuin jaksat kirjoittaa)

Tämä on yleensä myös se järjestys, jossa `apt`-komentoa käytetään, jos halutaan asentaa uusia ohjelmia.

### `apt`-työkalu hakee ohjelmat repositorioista

Jos kokeilit edellisiä komentoja, ne saattoivat toimia nopeasti, kirjoittamat riviäkään ruudulle suorituksensa jälkeen.

Mahdollisesti, jos seurasit debianin asennusohjeitamme, järjestelmääsi ei ole määritetty ensimmäistäkään ohjelmistojakelua (engl. repository)

Jotta pääsemme asentamaan ohjelmia, meidän pitää lisätä puuttuvat ohjelmistojakelut.

Teemme sen käyttämällä `nano`-tekstinkäsittelyohjelmaa:

```sh
sudo nano /etc/apt/sources.list
```

Komento avaa tekstieditorin, jossa pitäisi näkyä tekstiä.

Todennäköisesti jokainen rivi avatussa tiedostossa alkaa `#`-merkillä. Tämä tarkoittaa, että kyseiset rivit ovat kommentteja, eivätkä tee mitään.

Tiedostosta pitäisi löytyä seuraavat kommentoimattomat rivit (tai jotain niiden näköistä kommentoimatonta):

```
deb http://deb.debian.org/debian trixie main
deb http://security.debian.org/debian-security trixie-security main
deb http://deb.debian.org/debian-updates trixie-updates main
```

Jos niitä ei löydy, lisää ne itse.

Tämän jälkeen tallenna ja sulje tiedosto:

1. Tallenna muutokset näppäinkomennolla `ctrl + o`. Paina enteriä hyväksyäksesi tiedostonimen.
2. Sulje tekstieditori näppäinkomennolla `ctrl +  x`.

Voit halutessasi lukea lisää sources.list -tiedostosta debianin dokumentaatiosta: https://wiki.debian.org/SourcesList

## Seuraavaksi: asennetaan wordpress

Nyt sinun pitäisi olla valmis asentamaan wordpress: [ohje: wordpress-asennus debian 13:ssa](./debian13-wordpress-asennus.md)
