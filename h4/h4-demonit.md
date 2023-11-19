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
  
