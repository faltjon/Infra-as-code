# h7 miniprojekti #

### Ympäristö ###
Host-kone:

Käyttöjärjestelmä: Windows 10  
Prosessori: 11th Gen Intel(R) Core(TM) i5-11400F @ 2.60GHz   2.59 GHz  
RAM: 16 Gt  
Näytönohjain: NVIDIA GeForce GTX 1660 SUPER  
Virtualisointi: VirtualBox


Käytin aikaisemmin luomaani Debian -konetta Masterina ja tein käsin kaksi orjaa.
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

Projektin tarkoituksena on asentaa molemmille orjille kolme ohjelmaa: Discord, Firefox ja ShareX (ShareX ei ole saatavilla Ubuntulle, joten käytän sen tilalla Shutteria). Kotikoneella minulla on käytössä Windows 10 ja halusin kokeilla miten Saltin käyttö eroaa Ubuntu- ja Windows koneella.

Koneiden asennuksen jälkeen loin niille lähiverkon 192.168.1.0/24  
Hyväksyin avaimet masterilla:  

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/1-avaimet.png " ")

Ja poistin turhan avaimen "Joni", joka oli jäänyt testauksista.

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/2-poisto.png " ")

Asensin ohjelmat ensin käsin ja sitten automatisoin. Aloitin ubuntu-koneesta asentamalla Discordin, Firefoxin ja Shutterin.

### Ubuntu ###

Asensin ensiksi Discordin

Asennuksessa käytetään snap paketinhallintajärjestelmää. `$ snap install discord`  

Seuraavaksi automatisoin prosessin:

Nimesin tilat käyttörjestelmän perusteella windows=w ubuntu=u

Tilan ja init.sls tiedoston luonti:  
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

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/5-firefox.png " ")

firefox_u tilan  
init.sls:

```

sudo snap install firefox:
  cmd.run:
    - creates: /snap/bin/firefox
``` 

Lopuksi asensin Shutter -kuvankaappaustyökalun. Tämän asennus onnistuu suoraan apt-getin kautta. `$ sudo apt-get install shutter`

shutter_u tilan  
init.sls

```
shutter:
  pkg.installed
```

### Windows 10 ###

Siirryin nyt windows10 orjalle.  

Ohjelmien asennusta varten asensin Chocolatey -paketinhallinnan.  
Tein sille oman tilan, joka suorittaa orjan PowerShellissä chocolatey asennus-skriptin  

init.sls:
```
install_chocolatey:
  cmd.run:
    - shell: powershell
    - name: "Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))"

``` 
Ajoin tilan `$ sudo salt windowsV state.apply chocolatey`  
Tarkistin vielä orjalla, että chocolatey asentui onnistuneesti:

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/6-choco.png " ")

Kokeilin toimiiko chocolatey ja ajoin tilan:

```
choco_pkgs:
  chocolatey.installed:
  - name:
    - firefox  
```
Sain virheviestin:

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/7-error.png " ")

Yritin pitkään löytää syytä miksi tila ei toimi. Chocolatey on kyllä asennettuna vaikka powershell väittää ettei ole. En saanut chocolatey.installed toimimaan ja päätin tehdä nämäkin asennukset cmd.run tyylillä.  

Käsin kaikki kolme ohjelmaa asentuivat, mutta automatisoidessa Discordin asennus jäi jumiin, joten päätin olla asentamatta sitä.

Halusin ShareX:lle samat konfiguraatiot mitä käytän kotikoneella, joten kopioin HotkeysConfig.json tiedoston /srv/salt/win10 hakemistoon, josta se lähtee orjalle.

Loin tilan nimeltä win10 `sudo mkdir win10` ja sinne init.sls tiedoston, jolla asennetaan firefox ja sharex sekä lähetetään ShareX:n HotkeysConfig.json -tiedosto orjalle.

init.sls:
```
win10_apps:
  cmd.run:
    - shell: powershell
    - names:
      - choco install firefox -y
      - choco install sharex -y
  file.managed:
      - source: salt://win10/HotkeysConfig.json
      - name: 'C:\Users\Joni\Documents\ShareX\HotkeysConfig.json'
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

Lopuksi poistin molemmila orjilta kaikki asentamani ohjelmat ja testasin moduulin toimintaa ensiksi `$ sudo salt '*' state.apply`  
ja sitten vielä lyhennettynä idempotenssin testi `$ sudo salt '*' state.apply --state-output=terse`  

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/12-terse.png " ")


Katsoin vielä paikallisesti, että ohjelmat asentuivat:

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/11-ubuntu.png " ")

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/10-win10.png " ")

### Lopputulos ###

Halusin nähdä miten saltin toiminta eroaa Ubuntun ja Windows10 välillä. Ubuntulla ohjelmien asennukset sujuivat hyvin ja se vei aikaa noin 20%. Windowsilla minulla tuli paljon erilaisia ongelmia, joiden ratkaisemiseen kului paljon aikaa ja myös muutama ongelma jäi ratkaisematta. Moduulissa idempotenssi toteutui kaikkien paitsi Windows ohjelmien kanssa, koska en saanut `- creates` tarkistusta toimimaan.

