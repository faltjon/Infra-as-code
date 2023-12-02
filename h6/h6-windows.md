# h6 windows #

## x) Lue ja tiivistä. ##

*Sannnir raportti saltin käytöstä windowsilla*  


- Aluksi asennettaan salt windowsille menemällä lataukset kansioon, jossa .msi asennustiedosto on ladattu `cd C:\Users\user\Downloads`
- .msi tiedosto avataan komennolla `./tiedostonnimi`
- Saltin toimintaa voi testata esim. komennolla `salt-call --local cmd.run ”echo hello”`

*Halonen, Rajala ja Ollikainen 2023: Installing Windows 10 on a virtual machine*

- Windows 10 levykuvan (.iso tiedosto) voi ladata osoitteesta https://www.microsoft.com/en-us/evalcenter/download-windows-10-enterprise
- Virtualboxissa laita käyttöjärjestelmäksi windows 10 ja aseta lataamasi levykuva ISO IMAGE-kohtaan. Anna koneelle muistia vähintään 8GB ja aseta prosessorien määräksi 4 tai enemmän
- Käynnistä windows 10 kone virtualboxissa ja seuraa asennuksen ohjeita

*LSB Workgroup, The Linux Foundation 2015: Filesystem Hierarchy Standard* 

- /home = käyttäjän oma kotihakemisto
- /etc = Sisältää järjestelmän yhteiset konfiguraatiotiedostot
- /bin = Sisältää järjestelmän komennot, joita voi käyttää millä tahansa käyttäjällä

Lähteet:  
 https://github.com/sannnir/h5-Windows  
 https://github.com/therealhalonen/PhishSticks/blob/master/notes/ollikainen/windows.md  

## a) Asenna Windows virtuaalikoneeseen. ##

Käytin apuna ohjetta: https://github.com/therealhalonen/PhishSticks/blob/master/notes/ollikainen/windows.md

Aluksin latasin windows 10 levykuvan osoitteesta: https://www.microsoft.com/en-us/evalcenter/download-windows-10-enterprise  
Levykuvaksi valitsin English (United States) 64 bit  

Sitten aloitin asennuksen virtualboxissa painoin "new" nappia, annoin koneelle nimeksi "windows 10", valitsin ISO IMAGE kohtaan lataamani levykuvan ja versioksi "windows 10 (64-bit)"

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/1-vbox.png " ")

Annoin koneelle seuraavat resurssit:  

4 GB muistia  
4 prosessoria  
50gb levytilaa  

Käynnistin koneen ja asensin windows 10 seuraamalla asennuksen ohjeita.  
Asennuksen jälkeen pääsin windows 10 työpöydälle:  

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/2-win10.png " ")

## b) Asenna Salt Windowsille. Osoita 'salt-call --local' komentoa ajamalla, että asennus on onnistunut. ##

Ensimmäiseksi etsin uusimman 64-bittisen Salt-Minion version saltprojectin reposta:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/3-repo.png " ")

Latasin sen ja käynnistin asennuksen powershellissä  
`cd C:\Users\joni\Downloads\`  
`.\Salt-Minion-Latest-Py3-AMD64.msi`  

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/4-powershell.png " ")

Asennustiedosto avautui:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/5-asennus.png " ")

Etenin asennuksessa seuraavasti:  
Painoin Next  
Hyväksyin lisenssin

Minionin konfiguraatiossa annoin masterin osoitteeksi virtuaalikoneen IP-osoitteen

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/6-minion.png " ")

Jostain syystä windows 10 virtuaalikone ei näkynyt masterilla, joten siirryin kotikoneelleni, jossa on myös windows10 asennettuna.

Nyt minion näkyi masterilla ja hyväksyin avaimen:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/7-keys.png " ")

Kokeilin seuraavaksi komentoja paikallisesti:  
`cd '.\Program Files\Salt Project\Salt\'`  
`./salt-call --local test.ping`  
`./salt-call --local cmd.run "echo hello"`

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/8-test.png " ")

Salt-minion toimii odotetusti

## c) Kerää Windows-koneesta tietoa grains.items -toiminnolla. ##

Hain tietoja prosessorien määrästä, saltin versiosta ja prosessorimallista `./salt-call --local grains.item num_cpus saltversion cpu_model`  

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/9-grains.png " ")

cpu_model = Intelin 11 sukupolven i5-11400F prosessori 2.6GHz kellotaajuudella  
num_cpus = Prosessorien määrä on 12  
saltversion = Saltin versio on 3006.4

## d) Kokeile Saltin file -toimintoa Windowsilla. ##

Kokeilin file-toimintoa luomalla uuden tiedoston nimeltä "hello" hakemistoon C:\tmp `salt-call --local state.single file.managed 'C:\tmp\hello'`  

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/10-file.png " ")

Tiedosto luotiin onnistuneesti. Kävin katsomassa kansiosta, että tiedosto on varmasti luotu:

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/10-file2.png " ")

Tiedosto näkyy tmp kansiossa.

## e) Kokeile jotain itsellesi uutta toimintoa Saltista Windowsilla. ##

Käytin tätä lähdettä apuna: https://docs.saltproject.io/en/latest/topics/tutorials/walkthrough.html  

Ajoin komennon `salt-call --local disk.usage`, joka antaa tietoa levyn käytöstä.

![alt text](https://github.com/faltjon/Infra-as-code/blob/main/h6/kuvat/11-disk.png " ")

1K-blocks = tilan määrä kilotavuina  
available = vapaa tila kilotavuina  
capacity = käytetty tila prosentteina  
filesystem = Tiedostojärjestelmä, josta tiedot on laskettu  
used = käytetyn tilan määrä kilotavuina

### Lähteet ###

https://unix.stackexchange.com/questions/179274/what-does-1k-blocks-column-mean-in-the-output-of-df  
https://github.com/sannnir/h5-Windows  
https://github.com/therealhalonen/PhishSticks/blob/master/notes/ollikainen/windows.md  
https://docs.saltproject.io/en/latest/topics/tutorials/walkthrough.html  
