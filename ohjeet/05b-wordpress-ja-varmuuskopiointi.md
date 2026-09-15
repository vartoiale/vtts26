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

#### Phpmyadmin:in asennus

Saat asennettua phpMyAdmin-työkalun apt:illa:

```sh
sudo apt install phpmyadmin
```

Komento käynnistää asennuksen johon kuuluu kaksi sinistä ikkunaa:

1. Ensimmäisessä sinisessä ikkunassa valitse `space`-näppäimellä `apache2` (siten, että `apache2` viereen tulee `*`-merkki), 
   ja hyväksy valinta `enter`-näppäimellä.
2. Toisessa sinisessä ikkunassa valitse `no`, painamalla `enter`-näppäintä,
   koska olemme jo konfiguroineet tietokannan.

##### Phpmyadmin:in uudelleenkonfigurointi

Jos phpmyadmin ei käynnisty, esimerkiksi valittuasi väärät asetukset asennuksen yhteydessä,
voit ajaa uudelleenkonfiguroinnin:

```sh
sudo dpkg-reconfigure phpmyadmin
```

Uudelleenkonfiguroinnin yhteydessä:

1. Ensimmäisessä ikkunassa, paina `sarkain`-näppäintä (engl. "tab" tai "tabulator", löytyy `CapsLock`-näppäimen yläpuolelta), jotta pääset "ok"-napille, ja tämän jälkeen paina `enter`-näppäintä.
2. Toisessa ikkunassa, jossa kysytään "reinstall database for phpmyadmin?", valitse "no", ja paina `enter`-näppäintä.
3. Kolmannessa ikkunassa, jossa kysytään "Web server to configure automatically:", valitse `space`-näppäimellä `apache2` (siten, että `apache2` viereen tulee `*`-merkki), käytä `sarkain`-näppäintä siirtyäksesi "ok"/"cancel"-riville, ja valitse "ok" painamalla sen kohdalla `enter`-näppäintä.

Nyt sinulla pitäisi olla asennus kunnossa.

#### Phpmyadmin:in käyttö

Asennuksen jälkeen avaa phpmyadmin selaimessa osoitteesta: `localhost/phpmyadmin`.

Kun phpmyadmin kysyy käyttäjää ja salasanaa, anna tietokannan käyttäjänimi ja salasana,
jotka määritit `~/wp.sql`-tiedostossa (käytit niitä myös `/etc/wordpress/config-localhost.php`-tiedostossa):

* käyttäjänimi on `wordpress`, paitsi jos vaihdoit sen,
* salasana on asettamasi.

Huomaa, että phpmyadmin ei siis yritä kirjautua wordpressiin, 
vaan se haluaa kirjautua mariadb-tietokantaan.
Et siis voi käyttää kirjautumiseen wordpress:in käyttäjätunnusta tai salasanaa.

Tämän jälkeen voit kokeilla tehdä phpmyadmin:in avulla:

1. varmuuskopioinnin
   - https://developer.wordpress.org/advanced-administration/security/backup/#simple-backup-with-phpmyadmin, tai
   - https://developer.wordpress.org/advanced-administration/security/backup/database/
2. palautuksen varmuuskopiosta
   - https://developer.wordpress.org/advanced-administration/security/backup/database/#using-phpmyadmin-2

Kun olet luonut varmuuskopion, mutta ennen palautusta, muokkaa sivustoa, niin,
että huomaat eron aiemman tilan ja palautetun tilan välillä.

## Sisällön luominen varmuuskopiointia varten

Tässä harjoituksena oletus on, että olet vasta luonut uuden wordpress-asennuksen,
eikä wordpress-sivusto vielä sisällä mitään oikeaa, tärkeää sisältöä.

Jotta näkisimme, että varmuuskopion tekeminen ja palautus onnistuu,
luomme aluksi sisältöä wordpress-sivustollemme.

1. Luomme muutaman uuden sivun,
2. Lataamme sivustolle kuvia, ja lisäämme ne sivuille,
3. Lataamme asennukseen pluginin, ja käytämme niitä ainakin yhdellä sivuista.

### 1. Luomme muutaman uuden sivun

Luo uusia sivuja `localhost/wp-admin`-näkymän kautta.

Voit luoda esim. seuraavat sivut:

1. kirahvi (engl. giraffe)
2. koira (engl. dog)
3. marsu (engl. guinea pig)

#### Miksi luomme sivuja varmuuskopio-harjoitusta varten?

Normaalit sivut tallentuvat wordpress:issä tietokantaan.

Kun palautuksen jälkeen näemme, että sivut ovat palautuneet takaisin wordpress-sivustolle,
tiedämme että tietokannan palautus varmuuskopiosta on toiminut tältä osin.

### 2. Lataamme sivustolle kuvia, ja lisäämme ne sivuille

Lataa jokaiseen uuteen sivuusi yksi tai useampi otsikkoon liittyvä kuva.

#### unsplash.com tarjoaa ilmaisia valokuvia

Voit ladata ilmaiseksi, vapaahkolle lisenssillä kuvia esim.:

* [unsplash.com](https://unsplash.com)-sivustolta

Unsplash tarjoaa tätä kirjoittaessa ilmaisia ja maksullisia kuvia:

* maksullisissa kuvissa on `+`-merkki vasemmassa yläkulmassa
* ilmaisissa kuvissa ei ole `+`-merkkiä

Kun klikkaat kuvaa, aukeavassa popup-ikkunassa on vihreä nappi oikeassa yläkulmassa,
jossa lukee "download free".

Muista tallentaa kuvat ensin koneellesi, ja sen jälkeen lataa (engl. upload) ne wordpress-palvelimelle, 
jotta ne varmasti tallentuvat tiedostoina palvelimelle.

#### Miksi lisäämme kuvia varmuuskopio-harjoitusta varten?

Kuvat tallentuvat tiedostoina wordpress-asennuksemme tiedostokansioon.

Samalla niiden käyttöpaikka tallennetaan kuitenkin wordpressin tietokantaan.

Jos palautuksen jälkeen kuvat näkyvät luomillamme wordpress-sivuilla,
tiedämme että palautus on tältä osin onnistunut tiedostojen ja tietokantojen palautuksen osalta.

### 3. Lataamme asennukseen pluginin, ja käytämme niitä ainakin yhdellä sivuista

Asenna wordpress-sivustollesi jokin plugin.

_Localhost:issa plugineita asennettaessa `/etc/wordpress/config-localhost.php`-tiedostossa pitää olla rivi: `define('FS_METHOD', 'direct');`, 
jotta pystyt asentamaan plugineita selaimen kautta.
Tätä riviä ei kuitenkaan tule käyttää silloin kun asennus on internetistä löytyvällä palvelimella,
koska se on suuri tietoturvariski._

Hyvä plugin on esim. automattic:in (wordpressin tekijän) luoma bauhaus-plugin:

* [bauhaus centennary block](https://arg.wordpress.org/plugins/bauhaus-centenary/)

#### Bauhaus Centennary Block -plugin

Tarvitsemme siis jonkin hyvän, mutta luotettavan pluginin, ja automattic:in luoma Bauhaus Centennary Block -plugin on enemmän tai vähemmän tällainen.

#### Bauhaus Centennary Blockin asennus

Asennus onnistuu seuraavasti:

1. mene `localhost/wp-admin`-näkymään, jos et vielä siinä ole,
2. klikkaa vasemmasta sivupalkista "plugins",
3. klikkaa sen alta "add plugin",
4. etsi hakukentästä "bauhaus",
5. valitse löytyneiden plugineiden joukosta "bauhaus centennary block", jossa tekijänä on "automattic",
6. asenna plugin "install plugin"-napista.

Jos asennus jää pyörimään, eikä valmistu (sen pitäisi asentua nopeasti), tee vielä seuraavat vaiheet:

1. klikkaa vasemmasta sivupalkista "plugins"-osion alta "installed plugins",
2. jos selain kysyy popupilla, haluatko poistua kesken kaiken, vastaa "poistu" yms,
3. klikkaa installed plugins-näkymässä "Bauhaus Centennary Block"-pluginin alta "aktivoi"-nappia (engl. activate).

Nyt Bauhaus Centennary Block -pluginin pitäisi olla asentunut, 
ja voit seuraavaksi käyttää sitä jollain luomistasi wordpress-sivuista.

#### Bauhaus Centennary Blockin käyttö

Kun olet asentanut pluginin,
käytä sen tarjoamaa Bauhaus Centennary -blockia jollain sivulla.

1. avaa esim. kirahvi-sivu muokkausta varten,
2. avaa block:ien lisäyssivupalkki painamalla vasemman yläkulman `+`-nappia,
3. kirjoita hakukenttään "bauhaus", ja valitse "bauhaus centennary"-block listalta, (on mahdollista, että wordpress-ilmoittaa, että bauhaus centennary pitää ensin asentaa, jos näin on, tee se ensin),
4. etsi sivun lopusta äsken lisäämäsi bauhaush centennary -block, ja klikkaa siitä löytyvää pientä "1919"-nappia.

Nyt sinulla pitäisi olla sivullasi monivärinen bauhaus-teksti/-numero.

#### Miksi asennamme ja käytämme plugineita varmuuskopio-harjoitusta varten?

Pluginit asennetaan wordpress-asennuksen sisältökansioihin tiedostoina.

Samoin kuin kuvien osalta, niiden käyttö wordpress-sivuilla tallentuu kuitenkin tietokantaan.

Eli kun asennamme ensin pluginin, ja sen jälkeen käytämme sitä jollain wordpress-sivulla,
pystymme myöhemmin helposti tarkistamaan, 
että varmuuskopiointimme ja siitä palautus on onnistunut.

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

### välisoitto: varmuuskopioinnin ja siitä palautuksen aikana tapahtuvat asiat

Olet nyt saanut wordpress:in varmuuskopioitua, eli olet varmuuskopioinut:

* wordpressin tietokannan
* wordpressin tiedostot

Tyypillisesti varmuuskopioita tarvitaan muutamassa eri tapauksessa, tällaisia voivat olla:

* tehdään varmuuskopio siltä varalta, että järjestelmä hajoaa, ja varmuuskopio, on nimensä mukaisesti varmuuden vuoksi otettu kopio järjestelmästä,
* tehdään varmuuskopio, koska halutaan siirtää wordpress-sivusto toiselle koneelle.

Voit esimerkiksi kokeilla toteuttaa simuloidun järjestelmän vaihdon, muokkaamalla jotenkin wordpress-sivustoasi, tai sen asennusta.

Voit esimerkiksi wordpress:in ylläpitäjäkäyttöliittymän kautta poistaa tekemiäsi sivuja.
Tätä ei kuitenkaan kannata tehdä, jos sivustosi sisältää oikeaa sisältöä. On nimittäin mahdollista, että varmuuskopiosi onkin jollain tavalla epäonnistunut tai korruptoitunut,
eikä siitä lopulta pystykään palauttamaan sivustoa takaisin järjestelmään.

Tässä harjoituksessa oletus on, että wordpress-asennuksesi on uusi, eikä vielä sisällä mitään tallennuksen arvoista. Jos näin on, voit halutessasi poistaa sieltä kaikki sivut ja mediat. 

Tällöin, varmuuskopiosta sisältöä palauttaessasi tiedät, että varmuuskopiosta palautus on onnistunut, kun sisältö taas näkyy wordpress-sivustollasi.

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
