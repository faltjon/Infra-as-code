# h5 CSI Kerava #

## x) Lue ja tiivistä ##

### Karvinen 2018: Apache User Homepages Automatically ###

- Ennen automaatiota kaikki asennetaan ensin käsin
- Kun salt-tiloissa käytetään komentoja, täytyy niiden olla idempotentteja

Lähde: https://terokarvinen.com/2018/04/03/apache-user-homepages-automatically-salt-package-file-service-example/

**Käytin tässä raportissa Windows 10 käyttöjärjestelmässä pyörivää virtualbox versiota 7.0.10, jossa on asennettuna 64 bittinen debian 12.1.0 versio.**
## a) CSI Kerava ##

Ensiksi etsin viimeksi muokatut tiedostot /etc/ hakemistosta `$ sudo find /etc/ -printf "%T+ %p\n"|sort | tail -10`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h5/kuvat/1-findetc.png " ")
