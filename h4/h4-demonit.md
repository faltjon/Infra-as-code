## x) Lue ja tiivistä ##

### Karvinen 2023: Salt Vagrant - automatically provision one master and two slaves ###
*Infra as code:*  
- Aluksi luodaan statelle uusi kansio /srv/salt/ hakemistoon esim. hello `$ sudo mkdir -p /srv/salt/hello`
- Tähän uuteen kansioon luodaan init.sls tiedosto, johon tulee YAML-syntaksilla kirjoitettua koodia `$ sudoedit /srv/salt/hello/init.sls`
- Koodi suoritetaan komennolla `$ sudo salt '*' state.apply hello`

*top.sls:*  
- top.sls tiedostossa määritellään mille orjille suoritetaa mitkäkin statet
- Määritysten jälkeen ei tarvitse `state.apply` komennolla enää nimetä moduuleja

### Salt contributors: Salt overview ###
*Rules of YAML:*  
- Useimmat salt-tiedostot käyttävät käyttävät YAML-tulkkia
- Data määritellään `key: value` pareilla
- Isoilla ja pienillä kirjaimilla on merkitystä
- Tabulaattori on kielletty, vain välilyönnit hyväksytään sisennyksissä

*YAML simple structure:*  
- YAMLI koostuu kolmesta peruselementti tyypistä:
- Scalars, `key: value ` mapping, jossa arvo voi olla numero, tekstistringi tai boolean
- Lists, listat koostuu avaimesta, jonka alla on sen listan arvot
- Dictionaries, Koostuu kahdesta ylemmästä eli Scalars ja Lists
  
*Lists and dictionaries - YAML block structures:*
- YAML järjestäytyy palikkarakenteeksi
- Sisennykset tehdään kahdella välilyönnillä, kun määritellään arvoja

  ### Salt contributors: Salt states ###
*State modules:*  
En aivan ymmärtänyt tätä kohtaa

*The state SLS data structure*
- State tiedosto sisältää seuraavat komponentit:
- Identifier, kertoo missä kohdassa statea Identifier määritetään
- State, state-moduulin nimi, joka sisältää funktioita esim. `pkg`
- Function, funktio, jota kutsutaan moduulissa esim. `installed`
- Name, state-kutsun nimi. Yleensä se on sen tiedoston nimi, jota aiotaan hallita tai paketin nimi, joka asennetaan
- Arguments, argumentit, jotka state-funktio hyväksyy

*Organizing states:*
- Salt-state puu kuuluisi kirjoittaa mahdollisimman selkeästi niin, että toinen kehittäjä näkee nopeasti state-puun rakenteen

*The top.sls file:*
- Jotkut ympäristöt sisältävät satoja state-tiedostoja, jolloin ei ole kätevää suorittaa stateja yksitellen
- Saltilla on kaksi toimintoa, jolla tätä helpotetaan:
- top.sls tiedosto, jossa määritetään mitkä statet menevät kullekkin orjalle
- `highstate`, suorittaa kaikki top.sls olevat statet yhdellä salt toiminnolla

*Create the SSH state:*
- SSH-state näyttää tältä:
```
install_openssh:
  pkg.installed:
    - name: openssh

push_ssh_conf:
  file.managed:
    - name: /etc/ssh/ssh_config
    - source: salt://ssh/ssh_config

push_sshd_conf:
  file.managed:
    - name: /etc/ssh/sshd_config
    - source: salt://ssh/sshd_config

start_sshd:
  service.running:
    - name: sshd
    - enable: True
```
*Create the Apache state:*
- Apache state näyttää tältä:

```
implement_httpd:
  pkg.installed:
    - name: httpd

http_conf:
  file.managed:
    - name: /etc/httpd/conf/httpd.conf
    - source: salt://apache/httpd.conf

start_httpd:
  service.running:
    - name: httpd
    - enable: True
```
## Lähteet: ##

https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file  
https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml  
https://docs.saltproject.io/salt/user-guide/en/latest/topics/states.html#state-modules  

*Käytin tässä raportissa Windows 10 käyttöjärjestelmässä pyörivää virtualbox versiota 7.0.10, jossa on asennettuna 64 bittinen debian 12.1.0 versio.*
## a) Hello SLS! Tee Hei maailma -tila kirjoittamalla se tekstitiedostoon ##

Käytin teron ohjetta apuna: https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file

Aluksi loin kansiot /srv/salt ja /srv/salt/hello `$ sudo mkdir /srv/salt` ja `$ sudo mkdir /srv/salt/hello/`  
![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/1-kansiot.png " ")

Hello state kansioon loin tiedoston init.sls, jonka sisältö näkyy kuvassa.  
Testasin toimiiko hello state komennolla `$ sudo salt '*' state.apply hello`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/2-hello.png " ")
Onnistui

## b) Top. Tee top.sls niin, että tilat ajetaan automaattisesti, esim komennolla "sudo salt '*' state.apply". ##

Loin uuden tiedoston top.sls, johon määritellään mitkä statet ajetaan millekkin orjalle.  `$ sudoedit top.sls`
Kuvassa näkyy tiedoston sisältö. Kokeilin toimiiko `sudo salt '*' state.apply` komento nyt ilman hello määritystä 

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/3-error.png " ")

Error! top.sls tiedosto oli väärässä kansiossa. Loin sen uudelleen /srv/salt kansioon.

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/4-onnistui.png " ")  

Nyt state.apply onnistui

## c) Apache. Asenna Apache, korvaa sen testisivu ja varmista, että demoni käynnistyy. ##

Suoritin asennuksen ensin käsin:  
Asensin aluksi apachen. `$ sudo apt-get install apache2`
![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/5-apache.png " ")  

Asennuksen jälkeen testasin selaimella avautuuko apachen testisivu
![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/6-sivu.png " ")  

Apache käynnistyi automaattisesti asennuksen jälkeen

Seuraavaksi korvasin testisivun muokkaamalla tiedostoa /var/www/html/index.html `micro index.html`
Uusi sivu tuli näkyviin selaimessa: 

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/7-uusisivu.png " ")  

Sitten ryhdyin automatisoimaan:

Käytin tässä apuna teron ohjetta: https://terokarvinen.com/2018/apache-user-homepages-automatically-salt-package-file-service-example/  

Aluksi loin uuden kansion apache ja sinne init.sls tiedoston.  
![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/8-kansio.png " ")  

Loin myös html tiedoston, jonka salt hakee korvaamaan testisivun. `$ sudo micro testisivu.html`

init.sls tiedoston sisältö: 
```
apache2:
 pkg.installed
/var/www/html/index.html:
 file.managed:
   - source: salt://apache/testisivu.html
a2enmod userdir:
 cmd.run:
   - creates: /etc/apache2/mods-enabled/userdir.conf
apache2service:
 service.running:
   - name: apache2
   - watch:
     - cmd: 'a2enmod userdir'
```

Testataan state: `$ sudo salt '*' state.apply apache`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/9-onnistui.png " ")  

Kaikki läpi!

Katsoin vielä selaimella, että sivu on vaihtunut:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/10-moro.png " ")  

## d) SSHouto. Lisää uusi portti, jossa SSHd kuuntelee. ##

Käytin tätä lähdettä apuna: https://www.ionos.com/help/server-cloud-infrastructure/getting-started/important-security-information-for-your-server/changing-the-default-ssh-port/

Aluksi muutin sshd_config tiedostosta porttinumeron 22 -> 1234 `sudo micro sshd_config`
Sen jälkeen käynnistin sshd:n uudelleen `$ systemctl restart sshd` ja testasin yhteyttä `$ nc -vz localhost 1234`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/12-test.png " ")  

Portti on nyt auki

Automaatiossa käytin apuna teron ohjetta: https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh

Loin uuden staten sshd `$ cd /srv/salt/` `$ sudo mkdir sshd`  
Tähän kansioon loin 2 tiedostoa init.sls ja sshd_config

init.sls sisältö:

```
openssh-server:
 pkg.installed
/etc/ssh/sshd_config:
 file.managed:
   - source: salt://sshd/sshd_config
sshd:
 service.running:
   - watch:
     - file: /etc/ssh/sshd_config
```

sshd_config sisältö:

```
# DON'T EDIT - managed file, changes will be overwritten
Port 1234
Protocol 2
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
UsePrivilegeSeparation yes
KeyRegenerationInterval 3600
ServerKeyBits 1024
SyslogFacility AUTH
LogLevel INFO
LoginGraceTime 120
PermitRootLogin prohibit-password
StrictModes yes
RSAAuthentication yes
PubkeyAuthentication yes
IgnoreRhosts yes
RhostsRSAAuthentication no
HostbasedAuthentication no
PermitEmptyPasswords no
ChallengeResponseAuthentication no
X11Forwarding yes
X11DisplayOffset 10
PrintMotd no
PrintLastLog yes
TCPKeepAlive yes
AcceptEnv LANG LC_*
Subsystem sftp /usr/lib/openssh/sftp-server
UsePAM yes
```

Kokeilin toimiiko state `$ sudo salt '*' state.apply sshd`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h4/kuvat/13-sshd.png " ")  
