# Laajennettu ohje 5: Wordpress ja varmuuskopiot

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

## Phpmyadmin ja sen asennus

Saat asennettua phpMyAdmin-työkalun apt:illa:

```sh
sudo apt install phpmyadmin
```

Sinisessä terminaalin käyttöliittymässä valitse `space`-näppäimellä `apache2`, ja hyväksy valinta `enter`-näppäimellä.

Toisessa sinisessä ikkunassa valitse `no`, koska olemme jo konfiguroineet tietokannan.

Avaa phpmyadmin selaimessa osoitteesta: `localhost/phpmyadmin`.
Kun phpmyadmin kysyy käyttäjää ja salasanaa, anna käyttäjänimi ja salasana,
jotka määritit `~/wp.sql`-tiedostossa.

Phpmyadmin ei siis yritä kirjautua wordpressiin, vaan se haluaa kirjautua mariadb-tietokantaan.

## Komentoriviltä

Huomioi, että koska emme antaneet käyttäjälle LOCK-oikeuksia, joudumme lisäämään mysqldump-komennolle `--single-transaction`- nimetyn parametrin osaksi komentoa.

Toisaalta, joudumme kuitenkin lisäämään oikeuden käyttäjälle, jotta pystymme palauttamaan varmuuskopion tiedot.

## Tiedostojen varmuuskopiointi

Mitä kansioita tarvitsee varmuuskopioida:

1. `/var/lib/wordpress/wp-content` - pääasiallinen sisältö - tärkein varmuuskopioitava (pluginit, lataukset, teemat).
2. `/etc/wordpress` - asetustiedostot.
3. `/etc/apache2/sites-available` - ei niin tärkeää tässä vaiheessa, kun asetetaan ohjeen mukaan. Mutta jos näitä muokattaisiin enemmän olisivat ne tärkeät.

Varmuuskopioi seuraavalla komennolla:

```sh
sudo tar -zcvf wordpress_varmuuskopio.tar.gz /var/lib/wordpress/wp-content/ /etc/wordpress/ /etc/apache2/sites-available/
```

### Tiedostojen palautus varmuuskopiotiedostosta

Palauta vastaavasti `/`-juureen komennolla:

```sh
sudo tar -zxvf wordpress_varmuuskopio.tar.gz -C /
```

#### Oikeuksien palautus

Jotta kaikki toimii, palautetuille kansioille pitää vielä antaa takaisin oikeat oikeudet:

```sh
sudo chown -R www-data:www-data /var/lib/wordpress/wp-content/
```

ja

```sh
sudo chown -R root:www-data /etc/wordpress/
sudo chmod 640 /etc/wordpress/config-localhost.php
```

#### apachen uudelleenkäynnistys palautuksen jälkeen

Lisäksi apachen konfiguraatio pitää vielä käynnistää uudelleen:

```sh
sudo a2ensite wp
sudo systemctl restart apache2
```
