# Ohje 6: 

Tässä ohjeessa luodaan ethernet-kaapelia käyttäen yhteys 
luokassa sijaitsevan palvelimen ja luokassa sijaitsevan läppärin välille.

## Huomautus liittyen tietoturvaan

Tämä ohje on tarkoitettu käytettäväksi vain luokassa käytettävillä palvelimilla, 
ja luokan labrakoneilla, eikä sen tietoturvaominaisuuksiin ole kiinnitetty huomiota.

Jos sinun pitää muodostaa ssh-yhteys internetin yli palvelimeen, 
varmista, että teet sen tietoturvallisesti. 
Tällöin kannattaa käyttää apuna vpn ratkaisuja, kuten esimerkiksi tailscale:a.

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

Nyt et enää tarvitse tämän ohjeen seuraamiseen internet-yhteyttä palvelimen koneella.


## Koneiden liittäminen ethernet-kaapelilla

Voit nyt:

1. ottaa internet-yhteyden tuovan ethernet-kaapelin irti palvelimesta,
2. yhdistää palvelimen ja läppärin toisiinsa ethernet-kaapelilla.

## Asetusten määritys yhteyden muodostamista varten

### Debian-palvelimen asetusten määritys

Tarkista koneen ethernet-sovittimen nimi:

```sh
ip link
```

Ethernet-sovittimen nimi vaihtelee koneen mukaan, mutta se on muotoa `enp1s0`.

Tämän jälkeen, asetetaan koneelle manuaalisesti ip-osoite, 
oletetaan sovittimen nimen olevan `enp1s0`:

```sh
sudo ip addr add 192.168.50.2/24 dev enp1s0
```

Käynnistetään ethernet-sovitin:

```sh
sudo ip link set enp1s0 up
```

Tarkistetaan, että ip-osoite on käytössä `enp1s0`-sovittimella:

```sh
sudo ifconfig
```

Tulosteessa ethernet-sovittimen osoitteen pitäisi nyt olla `192.168.50.2`, 
ja aliverkon peitteen `255.255.255.0`.

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

### ip-osoitteen määrittäminen windows-koneella

Käynnistä windows:in asetukset. 
Siellä siirry Asetukset -> verkko ja internet -> ethernet -> ip-määritys -> muokkaa.

Aseta:

* alasveto valikosta valitse "manuaalinen",
* ipv4: "käytössä",
* ip-osoite: "192.168.50.1",
* aliverkon peite: "255.255.255.0",
* yhdyskäytävä: tyhjä,
* ensisijainen dns: tyhjä.

## Yhteyden muodostus windows-koneelta debian-palvelimelle

### Tarkista windows-koneella, että se löytää palvelimen

Käynnistä windows-läppärillä `cmd`, ja suorita komento:

```cmd
ping 192.168.50.2
```

Tämä on palvelimelle asetettu osoite, ja ping yrittää neljästi ottaa debian-palvelimelle yhteyden.

Kaikki on hyvin, jos ping palauttaa neljä riviä, joista ainakin yksi alkaa sanoilla "reply from".
Tällöin ping on onnistunut löytämään debian-palvelimemme.

### Asenna luokan windows-läppärille tarvittavat ohjelmat

#### winscp:n asennus

Lataa winscp osoitteesta: https://winscp.net, ja valitse sieltä: download -> other downloads -> portable executables: download.

Älä klikkaa sivulla olevia mainoksia.

#### winscp:n käyttö

Pura lataamasi zip, ja käynnistä winscp-sovellus.

Sovelluksen käynnistyttyä tulee näkyviin login-ikkuna.

Syötä:

* hostname: `192.168.50.2` - tämä on debian-palvelimen osoite,
* port number: `22`,
* user name: `<debian-palvelimen käyttäjän nimi>`,
* password: `<debian-palvelimen käyttäjän salasana>`.

Klikkaa login.

Tämän jälkeen avautuvassa ikkunassa on:

* vasemmalla puolella läppärin tiedostojärjestelmä, ja
* oikealla puolella debian-palvelimen tiedostojärjestelmä.

Siirrä varmuuskopio talteen palvelimelta, luokan läppärille, haluamaasi kansioon.

## Lopuksi

Olet nyt saanut siirrettyä varmuuskopiosi toiselle laitteelle.

Voit nyt vaihtaa alkuperäisen ethernet-kaapelin takaisin, jotta pääset palvelimella internetiin.
