# Ohjeet wordpress:in asentamiseen debian 13 -käyttöjärjestelmässä

Nämä ohjeet on kopioitu pitkälti debianin wikistä löytyvistä [wordpress:in asennusohjeista](https://wiki.debian.org/WordPress).

Niitä on muokattu siten, että ne sisältävät ne muutokset, 
joita tarvitaan wordpress:in asentamiseen paikalliselle kehityspalvelimelle.

Muutokset on erikseen listattu [laajennetuissa versiossa 4-ohjeesta](./laajemmat-ohjeet/04-debian13-wordpress-asennus.md)-ohjetiedostossa.

## Pakettien asennus

Päivitä tiedostojakeluun uusimmat tiedot tarjolla olevista paketeista.

```sh
sudo apt update
```

### Tarvitut paketit

Asenna tarvitut paketit:

* `wordpress` - wordpress:in paketti debianissa,
* `curl` - työkalu tiedostojen lataamiseen komentoriviltä,
* `apache2` - html-tiedostopalvelin,
* `mariadb-server` - tietokanta, tarkemmin palvelinversio sen paketista.

```sh
sudo apt install wordpress curl apache2 mariadb-server
```

### Mariadb-asennuksen tietoturvan parantaminen

Tee mariadb asennuksesta tietoturvallisempi:

```sh
sudo mysql_secure_installation
```

`mysql_secure_installation`-komennon kysymykset saattavat aluksi tuntua monimutkaisilta. 
Tästä ei kannata hätääntyä. 
Komento neuvoo kysymys kerrallaan, mitä käyttäjän tulee vastata.

Käytännössä vastaustapoja on vain kaksi:

* Kun `mysql_secure_installation` sanoo, että voit käyttää `n`-vaihtoehtoa,
  vastaa kysymykseen `n`-vaihtoehdolla. (suomeksi myös `e`-vaihtoehto käy)
* Muissa tapauksissa valitse oletusvaihtoehto painamalla `enter`-näppäintä.

## Asennus

### Apache2-konfiguraation määritys

Avaa nano-tekstieditorilla `wp.conf`-konfiguraatiotiedosto:

```sh
sudo nano /etc/apache2/sites-available/wp.conf
```

Lisää tiedostoon seuraava koodi:

```apache2
<VirtualHost *:80>
  ServerName localhost

  ServerAdmin a@b.local
  DocumentRoot /usr/share/wordpress

  Alias /wp-content /var/lib/wordpress/wp-content
  <Directory /usr/share/wordpress>
    Options FollowSymLinks
    AllowOverride Limit Options FileInfo
    DirectoryIndex index.php
    Require all granted
  </Directory>
  <Directory /var/lib/wordpress/wp-content>
    Options FollowSymLinks
    Require all granted
  </Directory>

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Yllä oleva koodi poikkeaa debianin koodista `ServerName`- ja `ServerAdmin`-arvojen osalta.

Tallenna muutokset (`ctrl + O`) ja hyväksy tallennustiedoston nimi (`enter`).

Poistu nano-tekstieditorista (`ctrl + X`).

#### Ota käyttöön `wp.conf`-konfiguraatio

##### `a2dissite 000-default`

Ota ensin pois käytöstä apachen oletuksena näyttämä sivu.

_Voit tarkistaa, miltä apachen oletussivu näyttää,
käymällä selaimella osoitteessa http://localhost, 
ennen seuraavan komennon ajoa._

```sh
sudo a2dissite 000-default
```

Etymologisesti `a2dissite` tulee suurin piirtein sanoista:

* `a2` - apache2
* `dis` - disable
* `site` - site

`a2dissite` ottaa parametrinaan konfiguraatiotiedoston nimen, 
jonka se yrittää oletuksena etsiä hakemistosta `/etc/apache2/sites-available`.
Eli siis siitä samasta hakemistosta, johon loimme äsken `wp.conf`-tiedoston.

Yllä olevassa komennossa mainittu `000-default` viittaa siis tiedostoon
`/etc/apache2/sites-available/000-default.conf`.

##### `a2ensite wp`

Tämän jälkeen ota käyttöön äsken luomamme `wp.conf`-konfiguraatiotiedoston.

```sh
sudo a2ensite wp
```

Etymologisesti `a2ensite` tulee suurin piirtein sanoista:

* `a2` - apache2
* `en` - enable
* `site` - site

Myös `a2ensite` ottaa parametrinaan `conf`-tiedoston, 
jota se etsii samasta `/etc/apache2/sites-available`-polusta.

`a2ensite`-komennolle parametrina annettu `wp` viittaa siis `/etc/apache2/sites-available/wp.conf`-tiedostoon,
jonka loimme aiemmin.

##### Päivitä apache2-palvelin käyttämään uusia asetuksia

Apache2 määritetään ottamaan käyttöön uudet asetukset komennolla:

```sh
sudo service apache2 reload
```

Jos tässä vaiheessa saat virheviestin, 
olet todennäköisesti kirjoittanut jotain väärin aiempaan `wp.conf`-konfiguraatioon.

Voit tarkistaa missä virhe on tapahtunut katsomalla apachen logia:

```sh
sudo service apache2 status
```

Tätä varten konsolin ikkunasta kannattaa tehdä mahdollisimman iso, 
jotta näet huonosti rivittyvän virheviestin.

Virheviesti näkyy logissa todennäköisesti punaisella, 
ja sisältää rivinumeron ja tiedostonimen, 
jossa virhe on tapahtunut.

Käy korjaamassa virhe. 
Tämän jälkeen voit kokeilla uudelleen `sudo service apache2 reload`-komentoa.

Jos saat yhä uuden virheen, tarkista tämä taas `sudo service apache2 status`-komennolla.

Jatka tätä, kunnes `sudo sercive apache2 reload`-komento menee läpi ilman virheilmoituksia.

### Wordpress-asennuksen tarvitsemien ympäristömuuttujien määritys

Wordpress olettaa saavansa ns. ympäristömuuttujissa tiedon mm. tietokannan käyttäjän nimestä, salasanasta ja monesta muusta asiasta.

Nämä määritetään erityisessä konfiguraatiotiedostossa, 
jonka nimi määräytyy käyttöpaikan mukaan.

Tiedoston nimi on muotoa: `/etc/wordpress/config-<osoite>.php`.

Koska haluamme sivuamme käytettävän kehityksen aikana `localhost`-osoitteesta,
samalta koneelta, on konfiguraatiotiedostomme nimi `/et/wordpress/config-localhost.php`.

Avaa siis konfiguraatiotiedosto nano-tekstieditorissa:

```sh
sudo nano /etc/wordpress/config-localhost.php
```

Lisää tiedostoon seuraavat rivit:

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'password');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/var/lib/wordpress/wp-content');
define('FS_METHOD', 'direct');
?>
```

Vaihda kuitenkin `password`-sanan tilalle haluamasi salasana.

Tarkemmin sanottuna, määrität seuraavassa `~/wp.sql`-tiedostossa tämän tietokannan salasanan.
Voit kuitenkin keksiä sen jo nyt. 
Tärkeintä on, että salasana on sama sekä `/etc/wordpress/config-localhost.php` ja `~/wp.sql`-tiedostoissa.

#### `FS_METHOD`-ympäristömuuttujan asetus

Huomaa myös, että yllä olevasta koodista löytyvä `FS_METHOD`-ympäristömuuttujan asetus ei ole mukana debianin ohjeissa.

Tarkoittaen siis riviä:

```php
define('FS_METHOD', 'direct');
```

Tämä ympäristömuuttuja, ja sen arvo sallii selaimesta käsin tapahtuvan tiedostojen muokkauksen.
Sen asettaminen on siis tietoturvanäkökulmasta erittäin huono idea.

**Sitä ei tule missään nimessä käyttää tuotannossa olevalla palvelimella.**

Käytämme sitä kuitenkin tässä yhteydessä, 
koska tiedämme, että ajamme palvelintamme vain omassa lähiverkossamme kehityksen aikana.

Se ei silti ole välttämättä kovin hyvä idea.

Tarvitsemme sitä kuitenkin, jotta voimme asentaa wordpress:in plugineja selaimen kautta.

Lähteitä:

* Tästä voi lukea lisää stackoverflow-kysymyksestä: [wordpress on localhost lamp doesn't let me install plugins](https://wordpress.stackexchange.com/questions/19649/wordpress-on-localhost-lamp-doesnt-let-me-install-plugins).
* Wordpress:in [`wp-config.php`-tiedoston dokumentaatio](https://developer.wordpress.org/advanced-administration/wordpress/wp-config/).

### Tietokanta-asetusten määrittäminen `~/wp.sql`-tiedostossa

Lopuksi määritämme vielä tietokanta-asetukset.

#### Kootaan tietokanta-asetukset aputiedostoon `~/wp.sql`

Avataa nano-tekstieditoriin `~/wp.sql`-tiedosto:

```sh
nano ~/wp.sql
```

Luomme siis poikkeuksellisesti kotihakemistoomme sql-tiedoston, ilman pääkäyttäjän oikeuksia.

Teemme tämän tilapäisen ratkaisun, jotta meidän ei tarvitse määrittää sql-lauseita komentorivillä.

Lisää tiedostoon seuraavat rivit:

```sql
CREATE USER wordpress@localhost IDENTIFIED BY 'password';
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER,LOCK TABLES
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
```

Tämä tekee seuraavat asiat:

* luo mariadb-käyttäjän `wordpress`, tarkemmin `wordpress@localhost`,
* määrittää `wordpress`-käyttäjälle salasanan `password`,
* luo tietokannan `wordpress`,
* antaa `wordpress`-käyttäjälle `wordpress`-tietokantaan seuraavat oikeudet: `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `CREATE`, `DROP`, `ALTER`, `LOCK TABLES`,
* ottaa luodun käyttäjän, oikeuksineen käyttöön.

Vaihda ylle taas `password`-kohtien tilalle aiemmin `/etc/wordpress/config-localhost.php`-tiedostossa käyttämäsi tietokannan salasana. 

#### Otetaan käyttöön tietokanta-asetukset `~/wp.sql`-aputiedoston avulla

Aiemman `~/wp.sql`-tiedoston luonti ei vielä tehnyt mitään.

Jotta tietokantaa varten määrittämämme asetukset tulevat voimaan,
ne pitää vielä ensin välittää mariadb:lle:

```sh
cat ~/wp.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Tässä käytämme siis apuna `|`-putkioperaattoria.
Sen avulla luemme ensin `cat ~/wp.sql` komennolla aiemmin luomamme tiedoston sisällön komentoriville,
ja `|`-putkioperaattorin avulla annamme sen edelleen `sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf`-komennolle.

Jos kaikki on mennyt oikein, meillä pitäisi nyt olla toimiva versio wordpress:istä asennettuna.

Jos saat virheviestin, tarkista kirjoititko kaiken oikein `~/wp.sql`-tiedostoon.

##### Debuggaus: kun `mysql`-komento valittaa, että `CREATE user` epäonnistuu

Jos edellisen vaiheen lopussa `mysql`-komento valittaa virheestä, jonka korjasit,
ja nyt se valittaa `CREATE user`-osion epäonnistuvan, joudumme nyt tekemään muita korjauksia.

Jos kirjoitusvirheesi oli sql-tiedoston lopussa, on mahdollista, että sql-komennoista ensimmäinen (wordpress-käyttäjän luonti) onnistui ensimmäisellä kerralla,
mutta uudella yrittämällä se ei enää onnistu (koska käyttäjä luotiin jo ensimmäisellä kerralla).

Tähän on muutama eri ratkaisu:

1. poista wordpress-käyttäjä käsin tietokannasta,
2. poista koko tietokanta.

Tällä kertaa teemme helpomman vaihtoehdon, ja poistamme koko tietokannan.
Teemme näin, lähinnä sen takia, etteivät sql-kielen perusteet mahdu tämän tutkinnon osan sisältöihin.

Voit alustaa koko tietokannan seuraavalla komennolla (mutta huomioi, että se kannattaa tehdä vain näin alussa, kun wordpress:iin ei ole vielä luotu sisältöä, koska myös wordpressin sisältö poistuu seuraavalla komennolla):

```sh
sudo systemctl stop mysql
sudo rm -rf /var/lib/mysql/*
sudo -u mysql mysql_install_db
sudo systemctl start mysql
```

Tämän jälkeen mariadb-tietokannan piätisi olla palautettu tehdasasetuksiin, 
ja voit ajaa uudelleen aiemman putkitetun mysql-komennon yläpuolelta.

Toista tätä (korjaa,suorita,alusta) -looppia, niin kauan, että msql-komento menee läpi ilman virheitä.

## Wordpress:in käyttöönotto

Siirrytään selaimeen, ja avataan siinä osoite: `http://localhost`.

Tämä siirtää meidät ylläpitäjän näkymään (`http://localhost/wp-admin`), jossa voimme määrittää wordpress-sivustomme.

Jatkossa:

* `http://localhost` - näyttää valmiin wordpress-sivumme
* `http://localhost/wp-admin` - avaa wordpressin ylläpitäjänäkymän, jossa voimme luoda sisältöä sivullemme.

Ensimmäisellä kerralla, kun wordpress-sivustoa ei ole vielä julkaistu,
vie myös `http://localhost`-osoite meidät ylläpitäjän näkymään.

Tämä kuitenkin muuttuu, kun julkaisemme sivustomme.
Sen jälkeen `/wp-admin`-näkymään pääsee vain `http://localhost/wp-admin`-osoitteen kautta.

### Wordpress-asennuksen viimeistely selaimessa

Viimeistelemme vielä asennuksen selaimessa, syöttämällä seuraavat kentät:

* sivun nimi - voit valita mitä vain, voit myös vaihtaa arvon myöhemmin,
* ylläpitäjän käyttäjänimi - kirjautumista varten käyttäjänimi,
* ylläpitäjän salasana - kirjautumista varten salasana,
* ylläpitäjän sähköpostiosoite - aseta kehitysympäristössä esim arvoksi: `a@b.local`.

Tämän jälkeen, kirjauduttuasi asennus on valmis, ja pääset käyttämään uutta wordpress-asennustasi.

## Seuraavaksi

Seuraavaksi: [5. wordpress ja varmuuskopiointi](./05-wordpress-ja-varmuuskopiointi.md).
