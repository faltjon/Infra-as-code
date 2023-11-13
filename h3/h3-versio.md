**Käytin tässä raportissa Windows 10 käyttöjärjestelmässä pyörivää virtualbox versiota 7.0.10, jossa on asennettuna 64 bittinen debian 12.1.0 versio.** 

## a) Online. Tee uusi varasto GitHubiin ##

Aluksi loin uuden varaston painamalla githubissa + -> new repository (kuten kuvassa)

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/1-new.png " ")

Varaston nimeksi laitoin "Winter-versio" ja kuvaukseen "Winter is cold"  
Täppä "Add a README file" ja lopuksi painoin "create repository" 

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/2-settarit.png " ")

Lopputulos:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/3-valmis.png " ")

## b) Dolly. Kloonaa edellisessä kohdassa tehty uusi varasto itsellesi, tee muutoksia, puske ne palvelimelle, ja näytä, että ne ilmestyvät weppiliittymään. ##

Ensiksi loin uuden ssh-avaimen ilman avainsanaa `$ ssh-keygen`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/4-keygen.png " ")

Julkinen avain on tiedostossa id_rsa.pub ja sitä voi tarkastella komennolla `$ cat $HOME/.ssh/id_rsa.pub`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/5-avain.png " ")

