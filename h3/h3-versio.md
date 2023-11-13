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

Seuraavaksi kävin lisäämässä avaimen githubiin. Oma profiili -> settings -> SSH and GPK keys  
Liitin avaimen, joka luotiin ssh-keygen komennolla ja painoin "Add SSH key"

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/6-github.png " ")

Lopputuloksena avain näkyy nyt SSH-keys alla.

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/7-winter.png " ")

Nyt kun avain on luotu asensin gitin virtuaalikoneelle. `$ sudo apt-get install git`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/8-git.png " ")

Asennuksen jälkeen hain winter-varaston ssh-url:n. Winter-repository -> Code -> SSH

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/9-repo.png " ")

Nyt kloonasin varaston virtuaalikoneelle `$ git clone git@github.com:faltjon/winter-versio.git`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/10-klooni.png " ")

Kloonauksen jälkeen tarkistin vielä, että winter-versio löytyy kotihakemistosta komennolla: `$ ls`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/11-ls.png " ")

Seuraavaksi aloin lisäämään uutta tiedostoa winter-versio varastoon. Aluksi loin uuden tiedoston `$ micro winter.md`  
Sitten valmistelin tiedostot committia varten `$ git add .`  
Tein git pullin, jotta varasto olisi ajan tasalla `$ git pull`  
Tein commitin `$ git commit` ja tässä vaiheessa unohdin kirjoittaa commit messagen, joten tein sen uudelleen viestin kanssa.

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/13-komennot.png " ")

Lisäsin pienen viestin committiin:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/12-commit.png " ")

Lopuksi vielä kerran git pull, jotta varasto on ajan tasalla ja sitten pusketaan tiedostot varastoon `git push`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/14-push.png " ")

Lopputuloksena winter.md ilmestyi githubin varastoon:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/15-github.png " ")


## c) Doh! Tee tyhmä muutos gittiin, älä tee commit:tia. Tuhoa huonot muutokset ‘git reset --hard’. Huomaa, että tässä toiminnossa ei ole peruutusnappia. ##

Poistin tiedostot README.md ja winter.md `$ rm README.md; rm winter.md `  
Sain palautettua tiedostot `$ git reset --hard` 

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/16-rm.png " ")

## d) Tukki. Tarkastele ja selitä varastosi lokia. Tarkista, että nimesi ja sähköpostiosoitteesi näkyy haluamallasi tavalla ja korjaa tarvittaessa. ##

Git logia voi tarkastella komennolla `$ git log --patch`  
Lokista näkee kaikki muutokset mitä varastoon on tehty. Loki kertoo myös muutoksen kellonajan ja tekijän. 


![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h3/kuvat/17-log1.png " ")

## Lähteet ##

https://terokarvinen.com/2023/configuration-management-2023-autumn/#h3-versio
