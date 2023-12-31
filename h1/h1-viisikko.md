# h1 viisikko #

## x) ##

**Create a Web Page Using Github**
- Aluksi rekisteröidy githubiin
- Luo uusi repository ja laita täppä kohtaan "add readme.md"
- Luo uusi tiedosto jonka tiedostopääte on .md
- Kirjoita vähän tekstiä ja paina "Commit changes"

Lähde: https://terokarvinen.com/2023/create-a-web-page-using-github/

**Run Salt Command Locally**
- Saltia käytetään suuren määrän orjakoneiden ohjaamiseen verkon yli
- Salt on funktiota, jotka tarkistavat onko jokin asia tosi tai epätosi
- pkg.installed/removed tarkistaa onko ohjelmisto asennettu/poistetu
- file.managed/absent tarkistaa onko tiedostoa olemassa
- service.running/dead kertoo onko palvelu käynnissä tai sammunut
- user.present/absent katsoo onko käyttäjää olemassa

Lähde: https://terokarvinen.com/2021/salt-run-command-locally/

## a) Asenna Salt (salt-minion) koneellesi. ##

Asensin salt-minionin ohjeessa olevilla komennoilla:

`sudo mkdir /etc/apt/keyrings`

`sudo curl -fsSL -o /etc/apt/keyrings/salt-archive-keyring-2023.gpg https://repo.saltproject.io/salt/py3/debian/11/amd64/SALT-PROJECT-GPG-PUBKEY-2023.gpg`

`echo "deb [signed-by=/etc/apt/keyrings/salt-archive-keyring-2023.gpg arch=amd64] https://repo.saltproject.io/salt/py3/debian/11/amd64/latest bullseye main" | sudo tee /etc/apt/sources.list.d/salt.list `

`sudo apt-get update`

`sudo apt-get install salt-minion`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h1/kuvat/1-salt.png " ")

## b) Viisi tärkeintä. Näytä esimerkit viidestä tärkeimmästä Saltin tilafunktiosta: pkg, file, service, user, cmd. Analysoi ja selitä tulokset. ##

### pkg.installed - An Application Should Be Installed ###
Kokeilin micro editorilla:

`sudo salt-call --local -l info state.single pkg.installed micro`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h1/kuvat/2-micro.png " ")

**"Funktio: pkg.installed"** kertoo funktion nimen  
**"Result: True"** Tulos onnistui  
**"Comment: The following packages were installed/updated: micro"** Kertoo mitä tapahtui  
**"Started: 15:09:32.004889"** Aika, jolloin funktio suoritettiin  
**"Duration: 2703.135 ms"** Suorituksen kesto  
**"Changes:"** Kertoo tapahtuneet muutokset  
**"Succeeded: 1 (changed=1)"** 1 funktio onnistui ja tehtiin 1 muutos  

### File.managed - There Should be a File ###

Kokeilin poistaa aikaisemman luodun tiedoston komennolla:  
`sudo salt-call --local -l info state.single file.absent /home/joni/testit/testi`  

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h1/kuvat/3-testi.png " ")

Funktio onnistui ja testi tiedosto poistettiin.

### service.running - A Daemon is Running ###

Komento: `sudo salt-call --local -l info state.single service.running apache2 enable=True`  

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h1/kuvat/4-apache.png " ")

**"ID: apache2"** Palvelun nimi  
**"Function: service.running"** Funktion nimi  
**"Result: False"** Funktio epäonnistui  
**"Comment: The named service apache2 is not available"** Apache palvelua ei ole asennettuna  

### user.present - User Should Exist ###

Komento: `sudo salt-call --local -l info state.single user.present joni`  

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h1/kuvat/5-user.png " ")

Joni käyttäjä on jo valmiina eikä muutoksia tarvita.

### cmd.run - Running a Command ###

En täysin ymmärtänyt tätä funktiota. Ohjeessa mainittiin "Use file, service or user instead of cmd.run". Käytin siis file.managed.  
komento:  
`sudo salt-call --local -l info state.single file.managed '/home/joni/testit/testi' unless="/tmp/foo"`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h1/kuvat/6-cmd.png " ")

Tyhjä tiedosto "testi" luotiin. En ole varma mitä "unless="/tmp/foo" tekee tässä.

Lähde: https://terokarvinen.com/2021/salt-run-command-locally/

## c) Idempotentti. Anna esimerkki idempotenssista. Aja 'salt-call --local' komentoja, analysoi tulokset, selitä miten idempotenssi ilmenee. ##

Ajoin 2 komentoa  

`sudo salt-call --local -l info state.single file.managed /home/joni/testit/testi` 

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h1/kuvat/7-idempotenssi.png " ")

Testi tiedosto on jo olemassa, joten muutoksia ei tarvita.

`sudo salt-call --local -l info state.single pkg.installed micro`  

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h1/kuvat/8-idempotenssi2.png " ")

Myös micro editori oli jo asennettuna, joten sitä ei tarvitse asentaa uudelleen. 

Lähde: https://terokarvinen.com/2021/salt-run-command-locally/

## d) Tietoa koneesta. Kerää tietoja koneesta Saltin grains.items -tekniikalla. Poimi kolme kiinnostavaa kohtaa, näytä tulokset ('grains.item osfinger virtual') ja analysoi ne. ##

Valitsin machine_id, mem_total ja ipv4

Komento `sudo salt-call --local grains.item machine_id mem_total ipv4`  
![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h1/kuvat/9-grains.png " ")

**ipv4** 
10.0.2.15 on virtualboxin antama IP-osoite, jolla se yhdistää internetiin isäntäkoneen kautta.  
127.0.0.1 on localhost osoite  

Lähde: https://medium.com/@fengliplatform/not-getting-10-0-2-15-on-vm-7b738d391ab2

**machine_id** Tietokoneen yksilöllinen ID. Se muodostuu käyttöjärjestelmän asennuksen yhteydessä. 

Lähde: https://wiki.debian.org/MachineId

**mem_total**

Kertoo tietokoneen muistin määrän. 3819 megatavua.
