# Lesweek 10

(Op 27/11/2017)

vervolg uitleg wnr domeincontrollers maken

(aantal dcs tot minimum beperken omda je replicatie nodig hebt)

# Replicatie

**NT (<= 4.0)** -> master/slave model

Masters moeten dus ervoor zorgen dat slave up to date zijn. 
Enorm veel netwerkverkeer voor die replicatie. 

Hoge lat bij 2000 en hoger (grote ambitites).
Vb. is individuele waardes van attributen ipv de hele objecten. 
(komt redelijk overeen met ospf tov rip maar hier is het nog erger)
Eigenlijk overgenomen van exchange (die ze opgekocht hadden).

Telkens een DC een aanvraag krijgt voor wijziging, zijn eigen tellerke verhoogt voor update (update sequence number). Hij gaat da aan zijn replicatiepartners zeggen. Da nummerke + identificatie vormt een **up to date"ness" vector.** Da bijhouden mag je enkel doen als je alle wijzigingen verwerkt hebt. 

Programma repadmin (zwitsers mes)

```
# repadmin /showutdvec satan # of sterreke ipv satan

repadmin /showutdvec * dc=iii,dc=hogent,dc=be

# resultaat over alle domeincontrollers 
# maar meer regels dan het aantal DCs (hier maar 3 DCs maar 7 lijntjes)
# verzameling van oorzakers van wijziging en je moet da laten staan (je kan niet anders)
# je ziet ook tijdstip van de wijziging

repadmin /showutdvec * dc=iii,dc=hogent,dc=be | grep -v "Time 201"


# je ziet dat ze niet syncroon zijn
# lucifer heeft 23 wijzigingen die satan nie heeft
# omgekeerd satan heeft 1 wijziging die lucifer nog nie heeft

# dus via update sequence nummer (USN) en tijd
# het verschil tss ..707 en ...708 willen we weten enkel da
# da kennen we via meta-data van een object (attribuut die nummerke heeft)
# kunnen we ook opvragen via


repadmin /showobjmeta satan cn=jomoreau,ou=docenten,ou=iii,dc=iii,dc=hogent,dc=be

# dan krijg je op satan de metadata van alle attributen van dat object



repadmin /showobjmeta satan cn=jomoreau,ou=docenten,ou=iii,dc=iii,dc=hogent,dc=be | grep -v "200.-"

# zien we lastlogontimestamp, mail en nog iets mssfu30gecos, die 3 zijn gewijzigd geweest 

===> pullmechanisme (vraag naar versienummer)

# latency periode van 15 minuten of nauwer zetten
# in de werkelijkheid loopt zo'n toestand altijd achter


# satan loopt achter op lucifer
# satan nen stamp geven via 

repadmin /syncall satan /A /D


# nu is satan effectief op de hoogte van de wijzigingen

# geef je nu lucifer nen stamp


repadmin /syncall lucifer /A /D


```



Comprimeren kan het maar wordt het vaak achterwege gelaten. In LAN-omgeving is de kost voor compressie vaak hoger dan de bandbreedte die men wint. 


Het is gevaarlijk als je wijzigingen doet op twee toestellen op het zelfde moment. De laatste wijziging (tijdstip) kiest men dan. 

Ander probleem is grafsteenobject. Als iedereen van da object op de hoogte is dan pas kan het afgehandeld worden. 

Als ge iets nieuws weet dan wil je da aan alle DCs zeggen, da wil men weg. Ge wilt dus nen graaf die zegt in een paar stappen moet da terecht komen ma nie overal (beperkt aantal replicatiepartners).

KCC = knowledge conistency checker die gaat periodiek (15 min) vaak van scratch af kijken bij wijzigingen in DCs. Ook da kan je nen stamp geven anders moede weer 15 min wachten.

KCC tekent replicatietopologie. Je wil da het pad max 3 hops heeft. Of ook je wil bvb maximum 3 rechtstreekse partners en laatste stap is ik wil sowieso een ander pad hebben. 

Altijd ring met soms spaken tss als die ring te groot is; in werkelijkheid is da nie per se heen EN terug. Die spaken voor de max 3 hops voorwaarde. 

Een replicatiepartner is ook in 1 richting. Maak het zelf bidirectioneel als dat moet. 

### Impact op replicatieverkeer

DC neemt zelf initiatief (om de drie uur eens pollen).
In LAN geen probleem maar in WAN kan wel duur uitvallen.

## Sites

indeling in fysieke locaties (terwijl domeinen en OUs da niet zijn).

Alles waar ik eenzelfde ip adres bereik kan zetten daar veronderstel ik da het via LAN.
En site is een verzameling van ip-adress bereiken (netwerkadresbereiken). En daarbinnen laat ik replicatie vollenbak toe. 

Site = vereniging van ip subnetadressen.
Site zonder ip subnet heeft dus geen zin, enige da je kan plaatsen zijn controllers. Want die hebben geen ip nodig om daar toegevoegd te worden. 

Voordeel sites -> gaat zoeken naar iets tot dezelfde fysieke locatie (via dns). Ander voordeel is het onderscheid tss intra en inter site replicatie. 

Pijl = replicatieobject aangemaakt door KCC. 
In lan is da dan eigenlijk iedereen mag me iedereen.
In site omgeving maak je sitekoppelingen. Die zeggen dan er MAG (ipv MOET) een verbinding zijn. 
Aan die sitekoppelingen kan je dan prijs hangen. Of bvb tijdseenheiden van "het mag gebruikt worden of niet". Bvb enkel buiten werkuren mag er replicatie zijn. 


Op elke site wordt er 1 dc verkozen en dat heet de inter site topologie generator. Dienen dude bepaalt wie de bridge head server is van de site (hij kan zichzelf verkiezen) maar die bepaalt wie er de site koppeling mag zijn en da zijn dan de bridge head servers.

(...moeten terugvallen op pollingmechanisme...)

Da configureren zelf gebeurt in AD zelf. Maar helaas sommige van de items zitten in het register van de controllers!! Waardoor je als admin die individuele moet gaan aanpassen. U up to date vector snelheden zit bvb in het register. 

Er bestaan toolkes (zie slide). 

Zit in de Configuratiegegevens 

via adsi edit

```
CN=Configuration
> Sites container

>> Default-First-Site-Name
>>> Servers # met daarin BELIAL, LUCIFER, SATAN

# Subnets zie je pas als er meerdere sites zijn 

# we doen alsof 
# op subnets > new object
# select a class - subnet (4bytes)
# 1.2.3.0/24
# geen andere verplichte attr wel optionele
# select a property to view = siteObject moet 1 van de bestaande sites zijn
# eens je dat doet maak je subnet lid van de site en omgekeerd ook backlink 

# in het siteobject zelf
> Default-... > properties

# in de site zou onder msDS-BridgeHead staan wie er bridge head is

>> NTDS site settings
interSiteTopologyGenerator # wie gaat er voor zorgen dat er bridge heads gekozen worden

>>> schedule = bitmap die voor elk kwartierke van de week ga vertelle mag er al dan niet gerepliceerd worden

# NTDS settings van de servers bestaat ook
# daaronder krijg je twee objecten
# en da zijn de pijlen
# als server mij meld en ik moe hem informatie geven da sta daar
# onder belial zal je als je properties opvraagt zien pijl naar satan/lucifer
# fromServer daar staat SATAN
# SMTP (restant uit het verleden want we doe het repliceren via smtp)
# ip container bevat de koppeling

in 1 koppeling kan je 5 sites toevoegen
je moet dus niet x aantal koppelingen maken!!


options attribuut => laagste bit al dan niet comprimeren; op twee na laagste bit zeg je da het toch over grenzen automatisch moet informeren.

sitelist attribuut dar moeten er twee staan anders is de koppeling zinloos

```


## shares

Workstation service en server service.


```
net session

c$ # sharenaam van c 
# dollars worden nie getoond zoda gebruiker geen idee heeft

dir \\192.168.16.147\c$\users

# dus geen expliciete koppeling nodig 
# sommige programmas breken hun tanden daarop
# en vaak koppelen die dan een drive map hieraan

# ugent cloud is ook nie meer dan nen share

# letterke koppelen aan een pad tov nen share op een ander toestel
net use f: \\files.ugent.be\jmoreau\home /user:ugent\jmoreau

# \home is submap van die share
# en dan user samaccountnaam 
> passw

# nu heb je f 
dir f:

# koppeling loslaten
net use f: /del

# je mag nie een schijfnaam gebruiken die al in gebruik is

net use * \\...

# nu krijg je z

net use z: /del


# of unix tool die vriendelijker is 

pushd \\belial\c$\home

# dan krijg je automatisch netwerkkoppeling en je wordt verplaatst 
# z:\home
# en doe je dan popd en is de koppeling ook verwijderd.



# oudbollige manier is (enkel eigen toestel)
net share

# beter om ook bvb op andere toestellingen te maken
rmtshare


rmtshare \\belial\home=c:\home
rmtshare \\belial\home /delete

# sinds 2012 r2 ebde meerdere interfaces afh van wa je nodig hebt -> spijtige zaak

"membra jesu nostri"

# via explorer > sharing > advanced sharing
permissions 

standaard staat het op read operaties 

share + machtiging op object zelf en meest destructieve bepaalt 

vroeger meestal object beveiliging en de poort (share) volledig open, nu recenter is het wat omgekeerd.

Read = enkel lezen
Change = lezen en schrijven
Full Control = change + machtiging kunnen aanpassen

Handig voor tijdelijke wijzigingen aan server;dan kan je de share permissies even wijzigen.

caching --> interessant!

vlag die twee ladingen dekt
afh van de interface w da offline files genoemt
twee aspecten:
- 1 in omstandigheden gelijk hier, je moet continu bestand aanspreken op server, telkens je da doet is da altijd op en af, staat da aan dan w de eerste interactie dan gaat die lokaal cachen omda hij daar kijkt en dan gaat da ook sneller
- 2 netwerkkabel uittrekken en zonder netwerkconnectie kan je blijven werken met die versie, wijzigingen zijn natuurlijk niet synhroon maar maak je weer verbinding dan gaat hij zien da er verchil is. Dan gaat hij zo centraal mogelijk alles uploaden. En je hebt dan werkelijk het idee da je op die drive kan werken ookal zit je er niet echt op. 

Bij conflict krijg je dan pop up (maar zoveel mogelijk gebeurd er achter unen rug)

Instellen of je automatisch cached of enkel op expliciet verzoek.

Je kan ook zeggen hoeveel toestellen er gebruik mogen maken van de share. (Niet waterdicht)

"Offline settings"


```

```

# shared folders management

fsmgmt.msc

> new share
nogmaals permisies op toegangspoort niet op bestanden zelf



# derde manier is via server manager

> local server (links)
file server installed
dan zie je tak 
file and storage services
> Shares
> da zit onder tasks -> new share...
> advanced > path c:\home
> naam
> caching
> permissies (read, change full control)
> iets me gpos
> interssante QUOTAs leggen
# zie volgende les

```