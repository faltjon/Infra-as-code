# h5 CSI Kerava #

## x) Lue ja tiivistä ##

### Karvinen 2018: Apache User Homepages Automatically ###

- Ennen automaatiota kaikki asennetaan ensin käsin
- Kun salt-tiloissa käytetään komentoja, täytyy niiden olla idempotentteja

Lähde: https://terokarvinen.com/2018/04/03/apache-user-homepages-automatically-salt-package-file-service-example/

**Käytin tässä raportissa Windows 10 käyttöjärjestelmässä pyörivää virtualbox versiota 7.0.10, jossa on asennettuna 64 bittinen debian 12.1.0 versio.**
## a) a) CSI Kerava. Näytä 'find' avulla viimeisimmäksi muokatut tiedostot /etc/-hakemistosta ja kotihakemistostasi ##

Ensiksi etsin viimeksi muokatut tiedostot /etc/ hakemistosta `$ sudo find /etc/ -printf "%T+ %p\n"|sort | tail -10`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/1-findetc.png " ")

Sama komento mutta home kansiolle `$ sudo find /home/joni/ -printf "%T+ %p\n" | tail -10` 

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/2-findhome.png " ")

*sudo* = pääkäyttäjän oikeuksilla  
*find /etc/* = aloitetaan etsiminen tästä hakemistosta  
*-printf* = tulostaa tulokset terminaaliin  
*%T+* = näyttää tiedoston muokkausajan  
*%p* = tiedoston nimi ja polku  
*\n* = uusi rivi  
*sort* = järjestää tiedostot muokkausajan mukaan?  
*tail -10* = näyttää viimeisimmät 10 tiedostoa  

Lähteet:   
https://www.tutorialspoint.com/how-to-list-the-last-five-modified-files-in-linux
https://terokarvinen.com/2018/04/03/apache-user-homepages-automatically-salt-package-file-service-example/

## b) Gui2fs. Muokkaa asetuksia jostain graafisen käyttöliittymän (GUI) ohjelmasta käyttäen ohjelman valikoita ja dialogeja. Etsi tämä asetus tiedostojärjestelmästä. ##

Muutin Firefoxissa Do Not Track asetuksen "Always" tilaan. 

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/3-firefox.png " ")

Sen jälkeen katsoin mitkä tiedostot muuttuivat komennolla `$ find -printf "%T+ %p\n"|sort | tail -22`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/4-find.png " ")

Useat tiedostot muuttuivat asetuksen myötä

## c) Komennus. Tee Salt-tila, joka asentaa järjestelmään uuden komennon. ##

Aluksi tein käsin yhden komennon. Menin oikeaan kansioon `$ cd /srv/salt/` ja loin sinne uuden tilan `$ sudo mkdir command`  
command kansioon loin komennon nimeltä "hello". Hello tiedoston sisältö kuvassa:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/5-hello.png " ")

Siirsin hello tiedoston kansioon /usr/local/bin/, jotta komentoa voi käyttää missä vain. `$ sudo mv hello /usr/local/bin/`
Tämän jälkeen muutin tiedostoon read ja execute oikeudet ryhmälle ja muille käyttäjille `$ sudo chmod go=rx hello`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/6-chmod.png " ")

Testasin komentoa `$ hello`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/7-hellotest.png " ")

Komento toimi odotetusti, seuraavaksi ryhdyn automatisoimaan.

Loin command-tilaan uuden tiedoston "init.sls".  
Sisältö:  
![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/8-init.png " ")

Poistin hello-tiedoston /usr/local/bin kansiosta, jotta voidaan testata, että automatisaatio varmasti toimii.

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/9-delete.png " ")

Sitten ajoin command-tilan uudelleen ja testastin että hello-komento toimii:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/10-tila.png " ")

Kuvasta voi päätellä, että automatisointi onnistui

## d) Apassi. Tee Salt-tila, joka asentaa Apachen näyttämään kotihakemistoja. ## 

Aluksi tarkistin, että apache2 ei ole asennettuna `$ sudo systemctl status apache2`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/11-apache.png " ")

Sitten aloin tekemään käsin. Aluksin asensin apachen `$ sudo apt-get install apache2`  
Testasin, että testisivu näkyy selaimessa:  

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/12-testisivu.png " ")

Seuraavaksi annoin käyttäjille oikeudet luoda kotisivuja `$ sudo a2enmod userdir` ja käynnistin apoachen uudelleen `$ systemctl restart apache2`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/13-oikeudet.png " ")

Nyt käyttäjän kotihakemisto rupesivat näkymään:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/14-kotihakemisto.png " ")

Nyt rupesin automatisoimaan saman prosessin. Aloitin poistamalla apache2 `$ sudo apt remove --purge apache2`  
Tarkistin onko apache2 oikeasti poistettu `$ sudo systemctl status apache2`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/15-remove.png " ")

Loin uuden tilan "apache" `mkdir /srv/salt/apache` ja sinne tiedoston init.sls jonka sisältö on seuraava:

```
apache2:
 pkg.installed
a2enmod userdir:
 cmd.run:
   - creates: /etc/apache2/mods-enabled/userdir.conf
apache2service:
 service.running:
   - name: apache2
   - watch:
     - cmd: 'a2enmod userdir'
```
Ajoin tilan komennolla `$ sudo salt '*' state.apply apache`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/16-state.png " ")

Testasin selaimessa toimiiko kotihakemisto vielä (Käytin sama kuvaa kuin aikaisemmin): 

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/14-kotihakemisto.png " ")

Kotihakemisto näkyy, joten voidaan olettaa, että tila on toiminut onnistuneesti.

## e) Ämpärillinen. Tee Salt-tila, joka asentaa järjestelmään kansiollisen komentoja. ##

Tein 2 uutta komentoa "greetings" ja "moro". Kolmantena komentona käytin vanhaa luomaani komentoa hello  
Komentojen sisällöt:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/17-komennot.png " ")

init.sls sisältö:

```
salt_commands:
  file.managed:
    - user: root
    - group: root
    - mode: "0755"
    - names:
      - /usr/local/bin/hello:
        - source: salt://command/hello
      - /usr/local/bin/greetings:
        - source: salt://command/greetings
      - /usr/local/bin/moro:
        - source: salt://command/moro
```

Ajoin tilan `$ sudo salt '*' state.apply command`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/18-apply.png " ")

Ja tein testit:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/19-testit.png " ")

Kaikki komennot toimivat

### Lähteet ###

https://github.com/saltstack/salt/issues/52130
https://terokarvinen.com/2018/04/03/apache-user-homepages-automatically-salt-package-file-service-example/
https://terokarvinen.com/2023/configuration-management-2023-autumn/#h5-csi-kerava
