# h6 windows #

## x) Lue ja tiivistä. ##

*Sannnir raportti saltin käytöstä windowsilla*  
Raportin linkki: https://github.com/sannnir/h5-Windows

- Aluksi asennettaan salt windowsille menemällä lataukset kansioon, jossa .msi asennustiedosto on ladattu `cd C:\Users\user\Downloads`
- .msi tiedosto avataan komennolla `./tiedostonnimi`
- Saltin toimintaa voi testata esim. komennolla `salt-call --local cmd.run ”echo hello”`

*Halonen, Rajala ja Ollikainen 2023: Installing Windows 10 on a virtual machine*

- Windows 10 levykuvan (.iso tiedosto) voi ladata osoitteesta https://www.microsoft.com/en-us/evalcenter/download-windows-10-enterprise
- Virtualboxissa laita käyttöjärjestelmä windows 10, muistia vähintään 8GB ja prosessorien määräksi 4 tai enemmän
- Käynnistä windows 10 kone virtualboxissa ja seuraa asennuksen ohjeita
