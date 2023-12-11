# h7 miniprojekti #

Host-kone:

Käyttöjärjestelmä: Windows 10  
Prosessori: 11th Gen Intel(R) Core(TM) i5-11400F @ 2.60GHz   2.59 GHz  
RAM: 16 Gt  
Näytönohjain: NVIDIA GeForce GTX 1660 SUPER  


Tein käsin yhden Windows-koneen ja yhden Ubuntu-koneen
```
Ympäristö koostuu kahdesta orja-koneesta ja yhdestä master-koneesta

Master:

Debian 12.1.0 (64-bit)
4GB muistia
40GB tallennustilaa

UbuntuSlave:

Ubuntu 22.04.3 (64-bit)
4GB muistia
25GB tallennustilaa

windowsV:

Windows10 (64-bit)
4GB muistia
50GB tallennustilaa


```

Projektin tarkoituksena on asentaa molemmille orjille Discord, Firefox ja kuvankaappaustyökalu (ShareX ei ole saatavilla Ubuntulle, joten käytän sen tilalla Shutteria). Kotikoneella minulla on käytössä Windows 10 ja haluan kokeilla miten Saltin käyttö eroaa Ubuntu ja Windows koneilla.

Koneiden asennuksen jälkeen loin niille lähiverkon 192.168.1.0.  
Hyväksyin avaimet masterilla:  

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/1-avaimet.png " ")¨

Ja poistin turhan avaimen "Joni", joka oli jäänyt testauksista.

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/2-poisto.png " ")¨

Asensin ohjelmat ensin käsin ja sitten automatisoin. Aloitin ubuntu-koneesta asentamalla Discordin, Firefoxin ja Shutterin.

### Ubuntu ###

Asensin ensiksi Discordin

Asennuksessa käytetään snap paketinhallintajärjestelmää. `$ snap install discord`  

Seuraavaksi automatisoin prosessin:

Loin init.sls tiedostot tilojen omiin kansioihin discord, firefox ja shutter. Nimesin tilat käyttörjestelmän perusteella windows=w ubuntu=u 

`$ sudo mkdir /srv/salt/discord_u` ja `$ sudo micro init.sls`

Käytin tässä cmd.run, koska en löytänyt parempaa tapaa asentaa snap-paketinhallinnan kautta.  
Komennosta on tehty idempotentti `- creates` avulla. 
init.sls:

```
sudo snap install discord:
  cmd.run:
    - creates: /snap/bin/discord
```

Siirryin firefoxin pariin. Ilmeisesti tämä snap-paketinhallinta on yleinen tapa asentaa ohjelmia ubuntulla. Firefoxin asennuksessakin suositellaan käyttämään `$ snap install firefox`.  

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/5-firefox.png " ")¨

Käytin taas tätä "huonoa" cmd.run tapaa saltissa.

init.sls:

```

sudo snap install firefox:
  cmd.run:
    - creates: /snap/bin/firefox
``` 

Lopuksi asensin Shutter -kuvankaappaustyökalun. Tämän asennus onnistuu suoraan apt-getin kautta. `$ sudo apt-get install shutter`


init.sls

```
shutter:
  pkg.installed
```

### Windows 10 ###

Siirryin nyt windows10 orjalle.  

Ohjelmien asennusta varten asensin Chocolatey -paketinhallinnan. Tein sille oman tilan.

init.sls:
```
install_chocolatey:
  cmd.run:
    - shell: powershell
    - name: "Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))"

``` 
Ajoin tilan `$ sudo salt windowsV state.apply chocolatey`  
Tarkistin vielä orjalla, että chocolatey asentui onnistuneesti:

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/6-choco.png " ")¨

Koitin toimiiko chocolatey ja ajoin tilan:

```
choco_pkgs:
  chocolatey.installed:
  - name:
    - firefox  
```
Ja sain virheviestin:

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/7-error.png " ")¨

Yritin pitkään löytää syytä miksi tila ei toimi. Chocolatey on kyllä asennettuna vaikka powershell väittää ettei ole. Ajan puutteen vuoksi päätin tehdä nämäkin asennukset cmd.run tyylillä.  
Discordin asennuksessa tila jäi jumiin, enkä keksinyt syytä sille, joten jätin sen asentamatta.

Loin tilan nimeltä win10 `sudo mkdir win10` ja sinne init.sls tiedoston:

```
install_apps:
  cmd.run:
    - shell: powershell
    - names:
      - choco install firefox -y
      - choco install sharex -y
```
Nyt minulla on molemmille orjille tilat valmiina. Seuraavaksi loin top.sls tiedoston, jotta voi määrätä oikeat tilat oikealla orjalle. `$ sudo micro /srv/salt/top.sls`

top.sls:

```
base:
  'UbuntuSlave':
    - firefox_u
    - shutter_u
    - discord_u
  'windowsV':
    - win10
```




## Raportti kesken ##

