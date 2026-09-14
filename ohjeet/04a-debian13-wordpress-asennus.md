# Ohje 4A: Wordpressin asennus debian 13 (trixie) -käyttöjärjestelmään

## Helppo asennus

Debian tarjoaa oman ohjeen wordpressin asentamiseen:

* [wordpress](https://wiki.debian.org/WordPress?pow_referer=https%3A%2F%2Fwww.google.com%2F)-ohje

Huomaa, että `Upgrading the installed wordpress version`-otsikon alla alkaa ohjeissa seuraava osio,
eikä sitä pidemmälle tarvitse mennä.

Kun olet päässyt yllä olevaan otsikkoon asti,
voit avata wordpressin selaimessa (paikallisesti kehitettäessä osoitteessa `http://localhost`).

Alta löydät vielä muutokset, jotka sinun tulee tehdä paikallisesti wordpressiä asentaessasi.

## Paikalliseen kehitykseen tarvittavat muutokset ja debuggaus virhetilanteissa

Kun konfiguroit wordpressin paikallista kehitystä varten, sinun tulee tehdä seuraavat muutokset:

1. Lisää jokaisen komennon alkuun `sudo`-komento.
2. Vastaa ohjeiden mukaisesti `mysql_secure_installation`-komennon kyselyihin.
3. Käytä apachen `/etc/apache2/sites-available/wp.conf`-konfiguraatiotiedostoa täyttäessä paikallisen asennuksen yhteydessä `localhost`-arvoa ja `.local`-osoitetta.
4. Nimeä debianin wordpressin konfiguraatiotiedosto nimellä: `/etc/wordpress/config-localhost.php`, jotta sitä käytetään kun sivu avataan `http://localhost` -url-osoitteella.
5. Aseta debianin wordpressin konfiguraatiotiedostoon sama salasana, jota myöhemmin käytät tietokantoja luodessa `~/wp.sql`-tiedostossa.
6. Lisää `~/wp.sql`-tiedostoon LOCK-rooli käyttäjälle.

Jos satut tekemään esimerkiksi kirjoitusvirheitä ohjeita seuratessasi,
et välttämättä onnistukaan saamaan wordpressiä käyntiin.
Tällöin joudut tekemään debuggausta, eli etsimään virheitä konfiguraatiotiedostoista.

Debuggauksesta kerrotaan lisää tämän tiedoston lopussa.

Näistä siis lisää vielä alla.
Ensin muutoksista ohjeisiin,
ja sen jälkeen debuggauksesta, eli vian etsinnästä.

### Muutokset ohjeisiin

Alla vielä tarkemmin jokainen ohje.

#### 1. käytä `sudo`-komentoa

Debianin wordpress-ohjeissa oletetaan, että komennot ajetaan pääkäyttäjällä (engl. "root" tai "superuser").
Tämän takia yhdessäkään komennossa ei käytetä alussa `sudo`-komentoa.

Huomaamme jo heti alusta, että ensimmäisenä ohjeissa käytetään koko järjestelmää muokkaavaa `apt`-pakettienhallintakomentoa, ilman `sudo`-komentoa.
Kuten aiemmin totesimme, `apt`-komennon käyttö vaatii pääkäyttäjän oikeudet, koska sillä tehdään muutoksia koko järjestelmään.
Tämän perusteella voimme olettaa, että komentoa ajetaan pääkäyttäjänä ja, että myös muissa komennoissa tulee käyttää `sudo`-komentoa, jos niitä ei ajeta suoraan pääkäyttäjänä.

Koska haluamme välttää pääkäyttäjän roolin ottamista pysyvästi, käytämme jokaisen rivin edessä `sudo`-komentoa.

Huomaa myös, että `|`-putkioperaation kohdalla joudumme lisäämään `sudo`-komennon myös putken oikeanpuoleiseen komentoon.

Kertauksena, `a | b`-putkioperaatio yhdisti kaksi komentoa (tässä `a` ja `b`) siten, että ensimmäisen `a`-komennon tuloste ohjataan jälkimmäisen `b`-komennon viimeiseksi parametriksi.

Tällöin, jotta voimme ohjeiden mukaisesti ajaa jälkimmäisen `b`-komennon vaadittavilla pääkäyttäjän oikeuksilla, tulee sen eteen lisätä taas `sudo`-komento.

Näin ollen `a | b`-komennon tulisi näyttää pääkäyttäjän oikeuksilla ajettuna seuraavalta `sudo a | sudo b`.

Debianin wordpress-ohjeessa vaihdamme rivin:

```sh
cat ~/wp.sql | mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

muotoon:

```sh
sudo cat ~/wp.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Oikeastaan ensimmäinen `sudo`-komento on turha, ja parempi olisi:

```sh
cat ~/wp.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

#### 2. Vastaa ohjeiden mukaisesti `mysql_secure_installation`-komennon kyselyihin.

`mysql_secure_installation`-komennon kysymykset saattavat aluksi tuntua monimutkaisilta.
Tästä ei kannata hätääntyä. 
Komento neuvoo kysymys kerrallaan, mitä käyttäjän tulee vastata.

Käytännössä vastaustapoja on vain kaksi:

1. Kun `mysql_secure_installation` sanoo, että voit käyttää `n`-vaihtoehtoa, vastaa kysymykseen `n`-vaihtoehdolla. (suomeksi myös `e`-vaihtoehto käy)
2. Muissa tapauksissa valitse oletusvaihtoehto painamalla enter-näppäintä.

#### 3. Käytä apachen `/etc/apache2/sites-available/wp.conf`-konfiguraatiotiedostoa täyttäessä paikallisen asennuksen yhteydessä `localhost`-arvoa ja `.local`-osoitetta.

Apachen `/etc/apache2/sites-available/wp.conf`-konfiguraatiotiedostoa täytettäessä tulee tehdä kaksi muutosta, kun ajetaan koodia paikallisessa kehitysympäristössä:

1. Rivillä 2, aseta `ServerName`-avaimen arvoksi `localhost`.
2. Rivillä 4, aseta `ServerAdmin`-avaimen arvoksi paikallinen osoite, esim `a@b.local`

Ensimmäinen muutos tehdään, jotta worpress toimii paikallisesti localhost-osoitteella.

Jälkimmäinen muutos tehdään, jottei paikallista kehitysympäristöä ajettaessa virheistä missään tapauksessa lähetetä viestiä todellisiin sähköpostiosoitteisiin.

##### Lyhyesti `localhost`-osoitteesta

`localhost` on url-osoite, joka on sovittu osoittamaan paikalliseen koneeseen.
Eli siis kun avaamme läppärillämme `localhost`-osoitteen selaimessa, selain tietää, että haluamme ladata sisällön samalla läppärillä pyörivältä palvelinohjelmistolta.
`localhost` on siis koodi sana, jolla voimme kertoa selaimelle, tai muulle ohjelmalle, että palvelin löytyy omalta koneelta, eikä sitä tarvitse lähteä etsimään internetistä.
Sanallista `localhost`-osoitetta vastaa ip4-numeroavaruudessa ip-osoite: `127.0.0.1`.

##### Lyhyesti `.local`-osoitteesta

Samalla tavalla kuin `localhost` on varattu omalle koneelle, `.local`-tld on varattu käytettäväksi lähiverkossa. 
Toisin sanoen, kukaan ei ole voinut ostaa omakseen keksimäämme kuvitteellista `a@b.local`-sähköpostiosoitetta.
Eikä wordpress näin ollen pysty lähettämään sähköpostia vahingossakaan väärille tahoille paikalliseen asennukseemme liittyen.

Jos ajat wordpressiä tuotantoympäristössä, kyseiseen `ServerAdmin`-kohtaan kannattaa laittaa oikea sähköpostiosoite.

`.local` on kuitenkin vähän ongelmallisempi, ja microsoft, windowsin kehittäjänä, ei suosittele sen käyttöä. 
(Tästä löytyy paremmin tietoa alla olevasta wikipedia-artikkelista.)

Meidän tapauksessamme `.local`-osoitteen käyttö on kuitenkin vähän hyväksyttävämpää, 
koska emme oikeasti suunnittele luovamme oikeaa palvelinta lähiverkkoon, joka vastaisi keksimäämme `a@b.local`-osoitetta, 
vaan haluamme vain, ettei sähköposteja lähde lähiverkon ulkopuolelle.

Voit lukea lisää `.local`-osoitteesta [wikipediasta](https://en.wikipedia.org/wiki/.local).

#### 4. Nimeä debianin wordpressin konfiguraatiotiedosto nimellä: `/etc/wordpress/config-localhost.php`, jotta sitä käytetään kun sivu avataan `http://localhost` -url-osoitteella.

Debian valitsee oikean konfiguraatiotiedoston wordpress:ille sen mukaan, mistä osoitteesta wordpress:iä yritetään avata.

Wordpressin konfiguraatiotiedostot ovat debianissa nimeltään muotoa: `/etc/wordpress/config-<osoite>.php`, jossa `<osoite>` on tiedoston nimeen kovakoodattu osoite.

Koska paikallisessa kehitysympäristössä yritämme avata wordpressiä osoitteesta `http://localhost`, tulee meidän luoda sitä vastaava konfiguraatiotiedosto nimellä `/etc/wordpress/config-localhost.php`.

Jos emme näin tee, wordpress ei osaa löytää konfiguraatiotiedostoamme, kun yritämme kutsua wordpressiä osoitteesta `http://localhost`.

Toinen vaihtoehto olisi luoda yleisempi konfiguraationimellä `/etc/wordpress/config-default.php`.
Yleensä kuitenkin kannattaa käyttää mahdollisimman tarkkaa nimeä.
Tällöin tarkempi nimi `/etc/wordpress/config-localhost.php` on parempi kuin yleisempi nimi `/etc/wordpress/config-default.php`.

#### 5. Aseta debianin wordpressin konfiguraatiotiedostoon sama salasana, jota myöhemmin käytät tietokantoja luodessa `~/wp.sql`-tiedostossa. 

Lopuksi wordpressin `/etc/wordpress/config-localhost.php`-konfiguraatiotiedostossa ja tietokanna määrittävässä `~/wp.sql`-tiedostoissa määritetään salasanat järjestelmälle.

Vaihda molempiin tiedostoihin pienellä kirjoitetun `password`-sanan tilalle haluamasi salasana. 

Huomaa, että salasana tulee asettaa `~/wp.sql`-tiedostossa kahteen eri paikkaan.

#### 6. Lisää `~/wp.sql`-tiedostoon LOCK-rooli käyttäjälle.

Lisää 3 rivin loppuun vielä LOCK oikeus:

```sql
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER,LOCK TABLES
```

Tämä tarvitaan, jotta käyttäjä voi suorittaa varmuuskopioinnin, ja palauttaa varmuuskopion.

### Debuggauksesta

Jos seuraat ohjeita, mutta tulet tehneeksi kirjoitusvirheitä, sinun pitää jotenkin pystyä keksimään missä kohdin olet virheet tehnyt.

Alla on muutama vinkki:

1. Lue kaikki kirjoittamasi komennot läpi, ja tarkista, että olet kirjoittanut kaiken ohjeiden mukaan merkilleen.
2. Lue kaikki kirjoittamasi tiedostot läpi, ja tarkista, että olet kirjoittanut kaiken ohjeiden mukaan merkilleen.

Huomaa myös, että:

* isot ja pienet kirjaimet tarkoittavat linuxissa eri asioita.
* välilyöntien määrällä ei ole väliä, kunhan niitä on oikeissa paikoissa, ja vain oikeissa paikoissa.

Jos kohdassa pitää olla välilyönti, ei haittaa, jos välilyöntejä on useampi.
Muihin paikkoihin ei kuitenkaan voi laittaa välilyöntejä.

Poikkeuksena ensimmäiseen on konfiguraatiotiedostot, joiden sisennyksessä välilyöntien määrällä on väliä. Tarkista siis nekin.

#### Kun komento toimii, se ei kerro mitään

Linuxin komentorivi on siitä erikoinen, että onnistuessaan komennot eivät yleensä tulosta mitään ruudulle.

Päinvastoin, virheen sattuessa komentoriville yleensä tulostuu jonkinlainen virheilmoitus, parhaassa tapauksessa kenties jopa kuvaus virheestä. 

Eli jos komento ei näytä tehneen mitään, koska se ei tulostanut mitään, se onkin oikeasti suoritettu onnistuneesti.

#### Kun apache antaa virhettä, katso logia

Kun käynnistät apachen uudelleen ohjeiden mukaisesti:

```sh
sudo service apache2 reload
```

mutta se antaakin virheen,
tarkoittaa se, ettet ole täysin onnistunut seuraamaan ohjeita pilkulleen, vaan olet tehnyt jossain virheen.

Se missä virhe on tapahtunut ei välttämättä ole ihan heti selvää.

Onneksi apache kertoo, monien muiden linux-ohjelmien tavoin, logissaan, missä virhe on tapahtunut.

Apachen logia pääset lukemaan komennolla:

```sh
sudo service apache2 status
```

_Tätä varten kannattaa laajentaa komentoriviohjelman ikkuna mahdollisimman isoksi. 
Mahdollisesti voit joutua ajamaan komennon uudelleen, tehtyäsi ikkunasta isomman, jotta komento osaa hyödyntää laajennetun ikkunan koko alan._

Virhe todennäköisesti näkyy punaisella tekstillä.

Virheen yhteydessä mainitaan todennäköisesti rivi, jolla virhe on tapahtunut,
sekä tiedosto, jossa virhe on tapahtunut.

Etsi siis virheen sijainti status-logista, ja korjaa se mainitussa sijainnissa (riviltä tiedostossa).

Kun olet korjannut virheen, yritä ajaa `sudo service apache2 reload`-komento uudelleen.

Jos saat vieläkin virhettä, tarkista uudelleen `sudo service apache2 status`-logista, onko virhe samalla rivillä vai uudella rivillä:

* Jos virhe on samalla rivillä, samassa tiedostossa, korjasit mahdollisesti virheen väärin, tai samalla rivillä oli toinenkin virhe.
* Jos virhe on eri rivillä, tai eri tiedostossa, virheitä oli vain useampi, ja sinun pitää korjata seuraava.

Jatka näin, kunnes olet saanut kaikki virheet korjattua.

#### SQL-tiedoston ajo epäonnistuu (käyttäjän luonti epäonnistuu)

Kun ohjeen mukainen `~/wp.sql` ajo epäonnistuu, tämä saattaa johtua siitä, että olet jo ajanut osittain tietokannan luovan koodin, ja se on osittain epäonnistunut.

Jos haluat ajaa tietokannan luonnin uudestaan, helpointa lienee, jos poistat tietokannan ensin kokonaisuudessaan. 
Huomaa kuitenkin, että tätä ei kannata tehdä, jos olet jo luonut wordpressiin sisältöä, koska tietokannan poisto poistaa myös tämän sisällön.

Tietokannan resetointi onnistuu seuraavien neljän komennon avulla:

```sh
sudo systemctl stop mysql
sudo rm -rf /var/lib/mysql/*
sudo -u mysql mysql_install_db
sudo systemctl start mysql
```

Tämän jälkeen voit taas ajaa uudelleen tietokannan generoivan lausekkeen:

```sh
cat ~/wp.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```
