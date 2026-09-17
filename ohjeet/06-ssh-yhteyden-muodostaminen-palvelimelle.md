# Ohje 6: ssh-yhteyden muodostaminen palvelimelle, ja varmuuskopioiden lataaminen talteen läppärille

## Huomautus liittyen tietoturvaan

Tämä ohje on tarkoitettu käytettäväksi vain luokassa käytettävillä palvelimilla, 
eikä sen tietoturvaominaisuuksiin ole kiinnitetty huomiota.

Jos sinun pitää muodostaa ssh-yhteys internetin yli palvelimeen, 
varmista, että teet sen tietoturvallisesti. 
Tällöin kannattaa käyttää apuna vpn-ratkaisuja, kuten esimerkiksi tailscale:a.

## alkutilanne

Tässä oletetaan seuraava lähtötilanne:

* Sekä debian-palvelin, että läppäri on kiinnitetty samaan kytkimeen, ja ovat näin ollen samassa lähiverkossa.
* Molemmat koneet saavat kytkimeltä dhcp-protokollan avulla dynaamisesti ip-osoitteen, ja ovat samassa aliverkossa.

## esivalmistelu: asennetaan tarvittavat paketit debian-palvelimelle

Päivitä tieto uusimmista saatavilla olevista paketeista:

```sh
sudo apt update
```

Asennetaan `net-tools`, paketti, jotta saadaan myöhemmin tarvittava `ifconfig`-työkalu käyttöön:

```sh
sudo apt install net-tools
```

Asennetaan ssh-palvelin debianille (`openssh-server`-paketti pitää sisällään openssh-paketin):

```sh
sudo apt install openssh-server
```

## Asetusten määritys yhteyden muodostamista varten

### ssh-palvelimen käyttöönotto

Otetaan ssh-palvelin käyttöön:

```sh
sudo systemctl enable --now ssh
```

Tarkistetaan, että ssh-palvelin on päällä ja toimii:

```sh
sudo systemctl status ssh
```

tämän `status`-komennon pitäisi näyttää, että ssh-palvelin on aktiivinen, 
ja kuuntelee porttia `22`.

### ssh-palvelimen ip-osoitteen selvittäminen

Jotta voimme ottaa läppäriltä etäyhteyden debian-palvelimelle, 
meidän pitää ensin palvelimella selvittää, minkä ip-osoitteen se on saanut dhcp-palvelimelta.

Palvelimella aja komento:

```sh
sudo ifconfig
```

Tämä listaa yhden tai useamman verkkosovittimen (engl. Network Interface Card, NIC), 
sekä niiden asetukset.

Nämä ovat muotoa: `sovittimen nimi: pitkä lista asetuksia`

Tyypillisesti listalta löytyy ns. virtuaalinen loopback-laite (nimi yleensä `lo`), mahdollinen langaton sovitin (nimi alkaa yleensä `w`), sekä todennäköisesti langallinen ethernet-sovitin (nimi alkaa `en`).

Tässä yhteydessä olemme kiinnostuneita langallisesta ethernet-sovittimesta.

Sen nimi alkaa merkeillä `en` (ethernet).

Ethernet-sovittimen asetuksista löytyy kenttä `inet` ja sitä seuraa IPv4 osoite (esim. `192.168.101.1`). 
Tämä on palvelimen ip-osoite lähiverkossa, ja se tarvitaan, jotta pystymme ottamaan palvelimeen yhteyden läppäriltä.

_Debianin verkkosovittimien nimeämiskäytännöistä löytyy kiinnostuneille oma artikkelinsa debianin wikistä:

* [NetworkInterfaceNames](https://wiki.debian.org/NetworkInterfaceNames)_

Systemd:n vastaava dokumentaatio:

* [Predictable Network Interface Names](https://systemd.io/PREDICTABLE_INTERFACE_NAMES/)

## Yhteyden muodostus windows-koneelta debian-palvelimelle

### Tarkista windows-koneella, että se löytää palvelimen

Käynnistä windows-läppärillä `cmd`, ja suorita komento:

```cmd
ping <palvelimen IPv4-osoite>
```

Eli jos palvelimen ip-osoite olisi edellisessä kohdassa ollut `192.168.50.2`, tämä komento näyttäisi seuraavalta:

```cmd
ping 192.168.50.2
```

Windowsin komentokehotteessa suoritettuna, ping yrittää neljästi ottaa debian-palvelimelle yhteyden.

Kaikki on hyvin, jos ping palauttaa neljä riviä, joista ainakin yksi alkaa sanoilla "reply from".
Tällöin ping on onnistunut löytämään debian-palvelimemme.

Jos ping ei palauta vastausta palvelimelta, jotain on pielessä. 
Tällöin kannattaa tarkistaa, että kaikki ethernet-kaapelit ovat kiinni, vieläpä samassa kytkimessä, ja olet kirjoittanut ip-osoitteen oikein.

### Asenna luokan windows-läppärille tarvittavat ohjelmat

#### winscp:n asennus

Lataa winscp osoitteesta: https://winscp.net, ja valitse sieltä: download -> other downloads -> portable executables: download.

Älä klikkaa sivulla olevia mainoksia.

#### winscp:n käyttö

Pura lataamasi zip, ja käynnistä winscp-sovellus.

Sovelluksen käynnistyttyä tulee näkyviin login-ikkuna.

Syötä kirjutumisikkunassa:

* hostname: `<debian-palvelimen IPv4-osoite>` - tämä on debian-palvelimen osoite, jonka sait aiemmin `sudo ifconfig`-komennolla,
* port number: `22`,
* user name: `<debian-palvelimen käyttäjän nimi>` (voit jättää myös tyhjäksi, jolloin sitä kysytään myöhemmin),
* password: `<debian-palvelimen käyttäjän salasana>` (voit jättää myös tyhjäksi, jolloin sitä kysytään myöhemmin).

Klikkaa login.

Tämän jälkeen avautuvassa ikkunassa on:

* vasemmalla puolella läppärin tiedostojärjestelmä, ja
* oikealla puolella debian-palvelimen tiedostojärjestelmä.

Siirrä varmuuskopio talteen palvelimelta, läppärille, haluamaasi kansioon.

Koska tulet tekemään useita varmuuskopioiden tallennuksia, 
kannattaa lisätä varmuuskopioinnin päivämäärä joko:

1. tiedoston nimeen, tai
2. luoda erillinen päivämäärän sisältävä kansio, johon tiedostot tallennat.

Näin pystyt helposti löytämään tiettynä ajankohtana tehdyn varmuuskopion nimen perusteella.

## Lopuksi

Olet nyt saanut siirrettyä varmuuskopiosi toiselle laitteelle.

Voit nyt vaihtaa alkuperäisen ethernet-kaapelin takaisin, jotta pääset palvelimella internetiin.
