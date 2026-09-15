# Wordpress ja varmuuskopiot

Kun olemme saaneet wordpressin asennettua, seuraavaksi on tarpeen opetella varmuuskopioiden tekeminen.

Tulemme tekemään palvelimillamme vielä kaikenlaista, ja tällöin on mahdollista saada järjestelmä niin solmuun, ettei sen pelastaminen välttämättä ole enää helppoa. 
Tällöin on hyvä olla varmuuskopio tallessa, ja sitä ennen on hyvä tietää miten varmuuskopioita tehdään.

Varmuuskopioita kannattaakin tehdä säännöllisesti.
Kurssilla säännöllisesti tarkoittaa vähintään päivän päätteeksi.
Mahdollisesti useamminkin, jos koneella tehdään päivän aikana jotain riskialtista.

Tuotannossakin varmuuskopioita kannattaa opetella tekemään säännöllisesti.

Alla tutustumme, miten varmuuskopiot tehdään wordpressin ulkopuolella.

Wordpress:ille on tarjolla myös erilaisia plugin:eita, joilla varmuuskopiointia voi tehdä,
mutta tässä vaiheessa emme tutustu niihin.

## Wordpress:in omat ohjeet varmuuskopiointiin

Wordpress:in omat ohjeet varmuuskopiointiin:

* [advanced administration: backup](https://developer.wordpress.org/advanced-administration/security/backup/)

Huomaa, että osa ohjeista on alasivuilla.

## Varmuuskopioinnin ja palautuksen vaiheet

Suositellusti varmuuskopioinnin tulisi tapahtua seuraavassa järjestyksessä:

1. Tietokannan varmuuskopiointi
2. Tiedostojen varmuuskopiointi

Varmuuskopioinnin palautuksen tulisi tapahtua päinvastaisessa järjestyksessä:

3. Tiedostojen palautus varmuuskopiolta
4. Tietokannan palautus varmuuskopiolta

Teemme tämän myöhemmin tässä tiedostossa, edellä mainitussa järjestyksessä.

Nyt kuitenkin katsomme nopeasti välissä phpmyadmin-työkalun asennusta, 
joka on wordpress:in omilla sivuilla huonosti dokumentoitu.

### Tietokannan varmuuskopiointi phpmyadmin-näkymän avulla

Emme tässä mene phpmyadmin-työkalun käyttöön.

Sen käyttö on kuitenkin helppoa, 
ja siihen löytyy hyvät ohjeet ylläolevasta wordpress:in omasta ohjeesta.

Asennusta ei kuitenkaan ole wordpress:in ohjeessa hyvin dokumentoitu,
joten ohjeet siihen löytyvät alta.

## Phpmyadmin:in asennus

Saat asennettua phpMyAdmin-työkalun apt:illa:

```sh
sudo apt install phpmyadmin
```

Komento käynnistää asennuksen johon kuuluu kaksi sinistä ikkunaa:

1. Ensimmäisessä sinisessä ikkunassa valitse `space`-näppäimellä `apache2`, 
   ja hyväksy valinta `enter`-näppäimellä.
2. Toisessa sinisessä ikkunassa valitse `no`, painamalla `enter`-näppäintä,
   koska olemme jo konfiguroineet tietokannan.

Asennuksen jälkeen avaa phpmyadmin selaimessa osoitteesta: `localhost/phpmyadmin`.

Kun phpmyadmin kysyy käyttäjää ja salasanaa, anna tietokannan käyttäjänimi ja salasana,
jotka määritit `~/wp.sql`-tiedostossa:

* käyttäjänimi on `wordpress`, paitsi jos vaihdoit sen,
* salasana on asettamasi.

Huomaa, että phpmyadmin ei siis yritä kirjautua wordpressiin, 
vaan se haluaa kirjautua mariadb-tietokantaan.
Et siis voi käyttää kirjautumiseen wordpress:in käyttäjätunnusta tai salasanaa.

## Komentoriviltä varmuuskopion tekeminen ja palautus

Lähdetään seuraavaksi tekemään varmuuskopiota ja sen jälkeen palauttamaan se.

### Varmuuskopiointi-kansion luominen

Kannattaa aloittaa varmuuskopiointi luomalla `~/varmuuskopio`-niminen kansio.

Tällöin varmuuskopiot löytyvät yhdestä paikasta, josta ne on helppo kopioida usb-muistille tai läppärille.

Mennään aluksi kotihakemistoon:

```sh
cd ~
```

Luodaan `~/varmuuskopio`-kansio:

```sh
mkdir varmuuskopio
```

Mennään `~/varmuuskopio`-kansioon.

```sh
cd varmuuskopio
```

Nyt voimme ajaa varmuuskopiot luovat komennot tässä `~/varmuuskopio`-kansiossa, 
jolloin komennoilla luodut varmuuskopiot löytyvät tästä `~/varmuuskopio`-kansiosta.

### varmuuskopioiden luonti

#### Tietokannan varmuuskopiointi

Varmuuskopioi komennolla:

```sh
mysqldump --add-drop-table -h localhost -u wordpress -p wordpress > wordpress.varmuuskopio.sql
```

Tämä luo, kansioon jossa olet komentoa ajaessasi, `wordpress.varmuuskopio.sql`-nimisen varmuuskopion.

Tee varmuuskopiosta kooltaan pienempi zip-tiedosto:

```sh
bzip2 wordpress.varmuuskopio.sql
```

Tämä luo uuden tiedoston nimellä `wordpress.varmuuskopio.sql.bz2` 
ja poistaa aiemman pakkaamattoman `wordpress.varmuuskopio.sql`-tiedoston.

Voit nyt tallettaa pakatun `wordpress.varmuuskopio.sql.bz2`-tiedoston esim. usb-tikulle,
tai siirtää talteen omalle koneellesi.

#### Tiedostojen varmuuskopiointi

Mitä kansioita tarvitsee varmuuskopioida:

1. `/var/lib/wordpress/wp-content` - pääasiallinen sisältö - tärkein varmuuskopioitava (pluginit, lataukset, teemat).
2. `/etc/wordpress` - asetustiedostot.
3. `/etc/apache2/sites-available` - ei niin tärkeää tässä vaiheessa, kun asetetaan ohjeen mukaan. Mutta jos näitä muokattaisiin enemmän olisivat ne tärkeät.

Varmuuskopioi seuraavalla komennolla:

```sh
sudo tar -zcvf wordpress_tiedostot.varmuuskopio.tar.gz /var/lib/wordpress/wp-content/ /etc/wordpress/ /etc/apache2/sites-available/
```

Vastaavasti, voit tämän jälkeen tallentaa `wordpress_tiedostot.varmuuskopio.tar.gz`-varmuuskopion usb-tikulle tai omalle koneellesi talteen.

### varmuuskopiosta palautus

#### Tiedostojen palautus varmuuskopiotiedostosta

Palauta varmuuskopioitu kansiorakenne sisältöineen takaisin debianin `/`-juureen komennolla:

```sh
sudo tar -zxvf wordpress_tiedostot.varmuuskopio.tar.gz -C /
```

Tässä oletuksena oli, että `wordpress_tiedostot.varmuuskopio.tar.gz` löytyi hakemistosta, jossa suoritit komennon.

##### Oikeuksien palautus

Jotta kaikki toimii, palautetuille kansioille pitää vielä antaa takaisin oikeat oikeudet:

```sh
sudo chown -R www-data:www-data /var/lib/wordpress/wp-content/
```

ja

```sh
sudo chown -R root:www-data /etc/wordpress/
sudo chmod 640 /etc/wordpress/config-localhost.php
```

##### apachen uudelleenkäynnistys palautuksen jälkeen

Lisäksi apachen konfiguraatio pitää vielä käynnistää uudelleen:

```sh
sudo a2ensite wp
sudo systemctl restart apache2
```

#### Tietokannan palautus varmuuskopiotiedostosta

Siirry ensin hakemistoon, jossa tietokannastasi tekemäsi varmuuskopio sijaitsee.

Tämän jälkeen, pura paketti:

```sh
bzip2 -d wordpress.varmuuskopio.sql.bz2
```

`bzip2`-työkalu poistaa tällöin `wordpress.varmuuskopio.sql.bz2`-tiedoston 
ja luo samaan hakemistoon pakkaamattoman `wordpress.varmuuskopio.sql`-tietokantatiedoston.

Palauta tietokanta varmuuskopiosta:

```sh
sudo mysql -h localhost -u wordpress -p wordpress < wordpress.varmuuskopio.sql
```

Tässä:

* `-h` saa arvokseen palvelimen osoitteen `localhost`,
* `-u` saa arvokseen tietokantakäyttäjän nimen `wordpress`,
* `-p` saa arvokseen tietokannan nimen `wordpress`.

Lopulta arvot siirretään varmuuskopiotiedostosta vasemmalle osoittavalla nuolella (`<`) `mysql`-komennolle.

## Loppu

Nyt jos kaikki meni oikein, wordpress-asennuksesi pitäisi olla palautettu varmuuskopiosta.

Voit testata, että kaikki toimii, tarkastamalla sivustosi toiminnan selaimessa osoitteella:

* `http://localhost/wp-admin` - ylläpitonäkymä
* `http://localhost` - (paikallisesti) julkaistu sivusto

Nyt voit taas jatkaa wordpress:in käyttöä, kuin varmuuskopiota ei olisi koskaan tapahtunut.
