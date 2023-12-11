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
6GB muistia
40GB tallennustilaa

Orja1:

Ubuntu 22.04.3 (64-bit)
4GB muistia
25GB tallennustilaa

Orja2:

Windows10 (64-bit)
4GB muistia
50GB tallennustilaa


```

Projektin tarkoituksena on asentaa ohjelmat Discord, ShareX(Kuvankaappaus ohjelma) ja Firefox kahdelle orjalle. Haluan nähdä miten Saltin käyttö eroaa orjilla, joilla on Ubuntu ja Windows käyttöjärjestelmät. Alunperin oli tarkoituksena asentaa Steam, mutta sen kanssa oli liikaa ongelmia Ubuntu-koneella joten vaihdoin sen ShareX ohjelmaan. 

Koneiden asennuksen jälkeen loin niille lähiverkon 192.168.1.0.  
Hyväksyin avaimet masterilla:  

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/1-avaimet.png " ")¨

Ja poistin turhan avaimen "Joni", joka oli jäänyt testauksista.

![alt text](https://github.com/faltjon/infra-as-code/blob/main/h7/kuvat/2-poisto.png " ")¨

Sitten rupesin asentamaan ohjelmia käsin. Aloitin ubuntu-koneesta asentamalla ensiksi Discordin.

Asennuksessa käytetään snap paketinhallintajärjestelmää. `$ snap install discord`


## Raportti kesken ##

