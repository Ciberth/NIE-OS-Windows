# Lesweek 5

(Op 23/10/2017)

Note: "zware les, neem notities met een korreltje zout" zie cursus en lees dit


## Doel (?)

ADSI laag zorgt voor de toegang via com objecten.

Veel toepassingen zijn eenvoudiger geworden dankzij AD:

- deployment services (toestel van scratch installeren)
- browser (alle toestellen staan geregistreerd in AD dus browsing is hierdoor mogelijk, analoog met shares die gerigstreerd zijn (de cloud computing binnen dezelfde lan))
- lijsten met gedeelde resources zoals shares en printers


DHCP verdient het om in DC opgenomen te worden; dat is echt een toepassing waarvan we achterover moeten vallen dat da niet in AD zit. DNS wordt best in AD geimplementeerd. 

Van zodra je een toestel opstart dat in het domein zit, zal dit een inlogprocedure starten maar vooraf moet hij weten waarin hij zich bevindt en die vraag gebeurt via dns. Als da nie draait/lukt dan kan hij zijn opstartprocedure niet starten. Ze moeten de DC vinden. DNS is dus als het ware de achilleshiel (bij microsoft). 


## Voordelen tov SAM-database


In oude implementaties als je met domeinen werkten dan moest je met **trust-relaties** werken. Probleem: ze moeten gemaakt en onderhouden worden. In de huidige implementaties zijn die trust-relaties automatisch (je kan ze zelf nie afbreken). Elk object kan je specifieke attributen geven maar in de praktijk gebruikt men OUs (hierarchische containers van objecten). Dit noemt men dan het **delegeren van beheersfuncties**. Een beheerder kan zeggen dat sommige attributen door bepaalde beheerders wel gewijzigd kunnen worden. 

Als daar goed over nagedacht w dan neemt da de noodzaak voor meerdere domeinen/forests weg!

Meerdere DCs zijn cruciaal voor redundancy/backups. Nadeel: ze moeten up to date blijven en synchroniseren. En da synchn is altijd een zwakte geweest. Het is een noodzakelijk kwaad maar het zorgt voor minpunten. 

Je kon DCs ook niet tegenhouden. Stel 80 dcs dan tegen da de sync rond was dan begon het al opnieuw! Een oplossing van toen was via sites. 

Het moment dat een toestel opstart MOET hij de DC contacteren. En het gebeurde ook dat da beestje den DC van een andere site contacteerde met de nodige overhead op het netwerk van dien. Gelukkig is da met de komst van AD opgelost. Het is dus pas als den DC van u site er nie is da hij verder ga kijken nu.

Nu: pas na werkuren/weekend gaan ze syncrhoniseren om bandbreedte te sparen. Note: de gegevens zijn dan tot die tijd niet gesynct. 

De manier om die sites te maken is via AD door objecten te maken in AD. Dus het beheer en configuratie zit in AD zelf.



Het recht om iets te doen w op locale computer toegewezen. En veel van de instellingen op de pc zijn eigenlijk geconfigueerd op lokale computerbasis. Om dat tegen te gaan zijn er group policies (foutieve naam, verkeerd te interpreteren). Dat is een groep policies die je centraal kunt beheren. En als de gebruiker inlogt krijgt hij de GPs over zich heen. En daarom kan je dus zeggen als die pc/gebruiker in die verzameling zit, dan krijg/verlies je dat recht. Vb. als excel geinstalleerd is mag hij het toch niet gebruiken. 


Verzameling pcs wordt bepaald door de hierarchie in AD. Een computerobject heeft een bepaalde naam in de boomstractuur. Het noemt groepsbeleid maar da is is dus beetje een misleidende naam. Het is dus NIET dat je dingen krijgt omdat je lid bent van een groep. Het is binnen de hierarchie nie omda je binnen een groep zit.

Groepen is voor later (machtigingen)

Microsoft heeft dan toch beslist om zoiets mogelijk te maken.

"We gaan ervoor zorgen dat iedereen het beleid over zich heen krijgen behalve als w verhinderd w da je groepsbeleid nie kan lezen, dus als je da nie kan lezen dan kan je het nie verwerken en men noemt da het filteren van GPs. Men gaat aan iedereen alles opleggen maar omdat ze aan iets behoren gaat er dus iets gebeurden waardoor je ze nie kan lezen en je dus niet daar aan onderworpen kan zijn. Iedereen kan alles over zich heen krijgen behalve door het onverwerkbaar maken (door lidmaatschap van een bepaalde groep). En da maakt het leven van een sysadmin veel moeilijker. Het verzamelen van wat er gebeurt is nog te doen maar kijken wat er gebeurt als ge moet rekening houden met alle uitgefilterde groepsobjecten dat is een moord om dat te onderzoeken."

Daardoor is groepsbeleid een mooi wapen die nauwelijks van de grond gekomen is. Maar het is echt een systeem dat meer gebruik verdiende. 

Men heeft er zelf voor gezorgd dat ge eerst die gefilterde dingen krijgt en dan nog eens een WMI filter die erop kan zijn (en daar kan je alles mee doen). Zolang het true/false terug geeft is het in orde. Achterhalen waarom iets nie werkt is dus hierdoor wel verschrikkelijk. 

- lidmaatschap groep
- locaal iets
- wmi filter 
- ... (debuggen verschrikkelijk)

Da is een beetje nadat profielen (iets gelijkaardigs) geflopt zijn geworden. Een concept die initieel flop was maar nadien door (bij)scholing (?) verbeterd en da zit er nu dus nog beetje in. 



Positief: AD is zeer schaalbaar, indextabellen en dergelijke heb je volledig in de hand	



Elk object w ondergebracht in een hierarchie die lijkt op snmp maar waren de takken zelf geen informatie hier in AD is alles drager van informatie. Wat ge niet moogt verwachten is: "ik wil info over de server" ge moet dan nie zoeken naar object die server voorsteld. Dikwijls stellen verschillende objecten op verschillende takken te samen de fysieke entiteit voor (dus niet 1 object). Bij wmi is da ook een beetje zo.

Ge moet ze wel uniek kunnen identificeren vooraleer je programmatisch je aan da object kan koppelen. 

Manier om te browsen in windows ldap is via ad console ``adsiedit.msc``



standaard heb je 3 partities in AD, en die koppeling moet 1 per 1 gebeuren

rechtermuisknop > connect to > onderste vakje onder computer de DC waarmee je moet verbinden < ``satan.hogent.be`` in de labos mag je satan gebruiken

en in da vakje dat lichtblauw staat moet je kiezen welke partitie (default naming context = domeingegevens) 

daarna op advanced > specify credentials (niet op labo computers als je al ingelogt zijt op het domein)

ok > ok dan krijg je koppeling

ons gebruikersobject is een object acher > ou=iii> ou=studenten

door vpn ellendig traag ; merk op de filter anders zie je sommige attributen niet!! 


DistinguishedName da is de waarde van da path om het programmatisch op te halen
da is samengesteld uit verschillende segmenten in volgorde van hierarchie

voor userclasses staat er ingesteld dat het sleutelattribuut ingesteld is op die klasse (CN)

``CN=Voornaam Achternaam, OU=oudercontainer, OU=grootoudercontainer, ..., DC=.... (root)`` vaak korte namen omdat de concatinatie u volledig path bepaald.

Hier root:``DC=iii, DC=hogent, DC=be``

Bij canonical name is het omgekeerd met slashes 

elk nieuw object krijgt nieuwe objectguid die vrijwel uniek zou moeten zijn


Stel dat je object verplaatst naar een andere container:
dan moet je programmatisch dingen verhuize, wat blijft er bewaard. 
objectguid blijft bewaard maar de distinguished en canonical namen veranderen natuurlijk wel


Gebruikersobjecten en computerobjecten

gebruikersobjecten moeten kunnen inloggen
en er moet gekeken worden naar wat hij met een object mag doen.

Bij het inloggen krijgt hij dan een ticket wa hij wel of niet mag doen. 

objectSID = security identifier

``whoami /all``

daar zie je dan de SIDs waar je toe behoort

Ge moet op 1 niveau toegang krijgen en op ander niveau geen verbod krijgen.


```
S-1-5-21-3265127454-4149502789-69696580-1001
---------
gelijk voor iedereen 
         ------------------------------
         mbt het domein
                                       -----
                                       het eigenlijke sid dat daar mee veranderd

```

sIDHistory = attribuut die alle sIDs accumuleert.


domeinen, computers en gebruikers die drie moeten kunnen inloggen

we gaan een extra naam geven en die extra naam waarmee je u inlogprocess moet afmaken is de **userPrincipalName** naamprefix@suffix (in principe volledige domeinnaam maar da mag ook de hoogste zijn) en dan configureer je die ergens (zie later). Dus er is hier ergens een configuratie die die iii mogelijk maakt. Hier: Voornaam Achternaam@iii. Dit kan je zelf beslissen.

De naam waarmee je inlogt dus.

De meeste gebruiken echter nog de **sAMAccountName** (Voornaam Achternaam)

PCs hebben ook: dNSHostName & naam die door applicaties kan gebruikt worden
sAMAccountName bestaat ook 

en **servicePrincipalName** multivalue: kan meerdere namen hebben


Satan = computer zelf is nen container en deze zijn attributenlijst van serviceprincipalname zal een stuk groter zijn.

schema Container =~ komt grotendeels overen met de repository van wmi



andere partitie:

connect to opnieuw en dan kies je voor schema bij naming context rest is gelijkaardig.

de meeste vlakke container die je u kan voorstellen; alle objecten zitten daarin (drie soorten objecten)

er is 1 subschema object (later) en de andere zijn klasse schema objecten en attribute schema objecten.

hier heb je niet de attributen IN de klasses. Beide zijn onafhankelijk van elkaar gemaakt (veel op veel relatie)


aan elke attribuut zit object en die zit in schema container

de naamkopppeling is niet verplicht (soms lijken ze op mekaar maar zijn ze niet gelijk)


klassenaam = lDAPdisplayName

vb applicationSettings terwijl bij name: Application-Settings (dit is vaak zo maar niet altijd)


Tweede tool voor door AD te browsing

``dsquery``


```
dsquery * "ou=EM7INF,ou=studenten,ou=iii,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interm J" -p "Interm J" -limit 0 -attr cn logoncount

standaard 100 via -limit 0 alles
-attr naamattributen (geen kommas tussen)



dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interm J" -p "Interm J" -limit 0 -attr ldapdisplayname cn | sort | nl

1500 attributen
200 klassen




dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interm J" -p "Interm J" -limit 0 -filter (objectclass=attributeschema) -attr ldapdisplayname cn | sort | nl | tail



dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interm J" -p "Interm J" -limit 0 -filter (objectclass=classschema) -attr ldapdisplayname cn | sort | nl | tail



geef waar er comment in voorkomt


dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interm J" -p "Interm J" -limit 0 -filter (ldapdisplayname=*comment*) -attr ldapdisplayname cn 



dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interm J" -p "Interm J" -limit 0 -filter (cn=*comment*) -attr ldapdisplayname cn 


dan zie je dat je gaat moeten opzoeken en queries aan mekaar koppelen

```

andere tool is ldifde

```

f filehandle naar stdout
en bij attributen via komma


ldifde -f con: -d "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -a "Interm J" "Interm J"  -r (cn=*comment*) -l ldapdisplayname,cn 



```

wel interessant om massaal veel updates uit te voeren 


