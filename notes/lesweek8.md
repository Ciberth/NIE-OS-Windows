# Lesweek 8

(Op 13/11/2017)


vervolg attribuutschema objecten

sommige attributen worden (mis)(ge)bruikt om bitsgewijs

(803:=1)

Laatste bit op 1 -> komen we attributen tegen die niet gerepliceerd worden.

Een aantal van die attributen dienen om locale cache op te bouwen.


(803:=4)
(laatste twee bits op 1)

aNR = pseudoattribuut
canonicalname = andere formatering van distinguished

al die anderen zijn dus "niet fysieke" attributen en als iemand die vraagt worden die bewaard aan de hand van een formule van andere zaken die wel opgeslagen zijn. 


Je kan die dus nie deftig opvragen je moet er iets speciaals voor doen en zo'n adders zitten er altijd in. --> u script moet er dus tegen bestend zijn!



### linkID (BELANGRIJK !!)

alle attributen waar linkid is ingevuld

```
dsquery * .... -filter (^&(objectclass=attributeschema)(linkid=*)) -attr linkid ldapdisplayname
```

sorteren is interessander 

```
dsquery * .... -filter (^&(objectclass=attributeschema)(linkid=*)) -attr linkid ldapdisplayname | nen niet windows sort
```


je ziet staan

hasMasterNCs en masteredBy

hasMasterNCs = eigenschap van DC 
masteredBy = eigenschap van een partitie (door welke DCs ben je ondersteund)
die twee zijn dus equivalent

Je kan niet het een zonder het andere (je bent dus als programmeur verantwoordelijk voor beide)

een ander is member en memberOf

member = eigenschap van gebruikersobject (tot welke groep behoor je)
memberof = eigenschap van groepsobject (welke zijn u groepsleden)

Als linkid op mekaar volgt Automatisch integriteit maar da heeft beperking -> je kan enkel de even id aanpassen.

Dus linkid 2 kan je aanpassen en 3 w automatisch gedaan. Dus je kan memberOf NIET aanpassen

"Heeft object memberOf attribuut zoja kijk naar linkid eentje kleiner en da aanpassen".

Forward (even) en backward link (oneven).


## Classeschemaobject

aantal class schemaobjecten da is orde 250 

ook da heeft een identificatie (naam in die container) 
enige verschil tss attributenschema is da het attributeID het heet attribute governsid ma voor de rest ist gelijk.

klasse die overerft erft alle eigenschappen.

Je kan ook nieuwe klassen maken, probeer zoveel mogelijk bestaande klassen te hergebruiken. Probeer een bestaande zo weinig mogelijk te wijzigen (er kunnen objecten bestaan) -> toevoegen kan wel
Doorgaans voeg je beter zelf 1 toe, da wordt veel te weinig gedaan!! 
Mensen zijn bang voor maar onterecht.

Je kan maar informatie afleiden van 1 klasse (geen meervoudige overerving mogelijk). In principe zijn ze allemaal afgeleid van oerklasse top.


top > person > organizationalperson > user 
vaak zie je ook dat een optionele in de ouderklasse verplicht wordt in de kindklasse.

hulpklasses (auxiliary)
- statisch
- dynamisch

Inhoudsregels (optioneel enzo) en je hebt ook structuurregels:

(objecten hierarchie bestaat ook; objecten kunnen nie overal geplaatst worden)
Possible superioirs zijn dan die structuurregels 

In OU staat nooit welke kindobjecten je kan hebben, het is het kindobject die verwijst naar welke container die kan staan.

Een user object kan da beschouwd worden als een containerobject?
Waarschijnlijk nee - maar toch wel zo!

Omdat objecten naar het userklasse verwijzen. Het is de verwijzing die ervoor zorgt dat je status krijgt van potentieel containerobject en niet omgekeerd. 


Programmatisch meh - je moet alle klasses afgaan en kijken en van zodra ze bestempeld kan worden dan kan je da pas eruit halen ookal staan er geen objecten in. 

Userobjecten zijn dus ook typische containerobjecten.

Die structuurregels moet je dus wel weten: De distinguised naam van de container; dus bij creatie van een object van een bepaalde klasse moet je kijken welke structuur regels heb ik zelf gedefineerd en in alle hogerliggende niveaus. 

Bij creatie moet je alle verplichte waarden al ingeven, dus ge moet een accumulatie maken van alle verplichte attributen. De optionele kan ook al. 

Dus structuurregels + alle verplichte inhoudsregels.

classschema objecten

- cn, ldapdisplayname, schemaidguid, governsid


computer > rechtermuisknop > properties

unie van mustcontainer en systemmustcontain 

mustcontain - standaard leeg en die mag je aanpassen
andere moede me u pollen van blijven

perlarray voor must en maycontain 

wil je lijsten dan ist me die systemmaycontain bij computer


overzicht van alle mogelijke 

rdnAttId - mogelijk waardes

```
`dsquery * ... -filter (^&(objectclass=classschema)(rdnattid=*)) -attr rdnattid | tail -n +2 | sort | uniq

# die tail is zonder header
# dan zie je

c
cn
dc
l
msTAPI-uid
o
ou
uid

```


waarom cn bij user - das door die rdnattid
elk object heeft da nodig voor identificatie binnen container


defaultsecuritydescriptor

via adsi edit 
```
schema -> user klasse-> properties

# zie je


defaultsecuritydescript = ...

```

Elk object heeft eigen security descriptor (wie kan er de eigenschappen aanpassen/verwijderen). 
Hier default
elk object/ instantie krijgt als initiele inhoud dienen inhoud 
voor beheerders een droom
je zegt de samenstelling van groepen -> kgeef da aan nen andere groep mensen maar da ga nie want die kunnen geen groepsobjecten aanpassen (user heeft geen zin). Non administrators kunne da nie. 

Dus da kan je daar verpakken, je kan hier aangeven, iedereen kan groepsdingen aanpassen.

Dus dit 1 keer in schema aanpassen is handiger dan telkens opnieuw

hoe doede da nu, der staan daar verwijzingen naar gebruikers enzo, manueel gade fouten maken

daarom en enkel daarom is er een andere interface handig

active directory schema plugin - daar is da schoner ma da is de enige reden wrm je deze interface zou gebruiken


HEEL BELANGRIJK ATTRIBUUT



isdefunct - een klasse vernietigen kunt ge nooit
als ge het maakt zal het altijd in het schema blijven bestaan
enige da je kan doen is klasse uitschakelen - bij fout - obsolete/deprecated
dan wil je nie da er nog objecten gemaakt worden -> dan zet je er isDefunct 

anders denkt men van zodra er klassen zijn dan kunnen er instanties zijn dus moet blijven

ookal zijn er geen objecten ik kan de klasse nie verwijderen



In de subClassOf (single valued attribuut) zit u onmiddelijke ouder
en van welke hulpclasses kan je overeveren da is auxiliaryClass en systemAuxiliaryClass (weer de unie daar kunde alles van halen).


objectClass 	objectClassCategory
objectCategory 	defaultObjectCategory

1e : attributen die elk object heeft, elke instantie van een object heeft een objectclass attribuut en die is ingevuld met de volledige rij van klasses van zijn eigen klasses en de klasses waarvan hij rechtstrees is overgeerft


objectcategory = manueel ingevulde verwijzing naar een klasse, een invulling naar een klasse die een van de klases moet zijn van u hierarchie van klasses. bvb computer (single value), printer is 1 van de onderste lagen je vraagt objectcategory da is ingevuld me printer. Je zou denken da is altijd de laagste maar das nie zo dan had men da nie gebruikt. objectcategory van user staat ingesteld op person (grootouderklasse). Waarom? Omdat in het klasseschemaobject de de defaultObjectCategory op person staat. 


Linkerkant voor elk object rechterkant enkel voor klasseschemaobjecten.


Nut/gevolgen:

Stel da je een query wil lanceren en je vraagt geef mij keer alle objecten waarvan de objectclass gelijk is aan user, dan krijg je alle objecten die in hun objectclass een multivalue attribuut een userclasse hebben staan, dus alle users maar bijgevolg ook alle computers omdat die afgeleid is van user. 

Accounts kun je dus zo nie opvragen.

Als je email adres maakt maar da moe verwijze naar persoon ma die moe nie kunnen inloggen -> da noemt men een contactklasse. En die contactklasse heeft in default ook person staan.

Dus vraag je alle elementen waarvan objectCategory ingevuld is op user (voor alle acounts) dan krijgde ook alle emails. :/

Dus objectcategory = person en class = user voor accounts (denk ik, niet zeker!)



ObjectClassCategory - slechte naam 


``-objectclass=classschema en -attr objectclasscategory``

dan krijg je 0,1,2,3

0 en 2 eventuele klasses hoger in de hierarchie. Deze kan je nie gebruiken om instanties te maken

```
... (objectclasscategory=0)) -attr ldapdisplayname  --> zijn er 6
bij = 2 --> zijn er 10

klasse 1 da zijn de echte zoals we verwachten -> 244

klasse 3 = hulpklases (kan je ook geen instanties van maken maar enkel om inhoud en structuurregels te laten overerven) zijn er 18

```


DynamicObject gaan we eens openen

systemMayContain: entryTTL en msDS-Entry-Time-To-Die (een is berekend op het andere (hier ttl afgeleid van den andere denken we)). 

Ofwel laten overerven van die hulp klasse, bij creatie zeg je ik erf over van die hulpklasse en je geeft aan dat het op zoveel staat. 

Denk aan dhcp


possSuperiors - daar moe je mee spelen als je een nieuwe wil maken
poss -> possible (dus potentiele container)





CN=Aggregate (danku nonkel microsoft) subschema object

Das nie voor menselijke interpratie vatbaar


Maakt soort accumulatie van vanalles voor de programmeeur.
Al da accumulatiewerk wordt voor u voorgekouwd. 

adsi laag gebruik maken 

en da object dient voor de adsi laag te hulp te zijn. Ge krijgt een com object da ldap representeerd.

Hierdoor verlost van het brute werk. 

MandatoryProperties: systemmustcontain, mustcontain van alle klasses en jij krijgt 1 attribuut
PossibleSuperiors: juist zelfde op basis van alle (system)possSuperiors van alle klassen




## Opsporen in AD

LDAP programma - heeft een tcp poort nodig van de server.
389

ge kunt maar in 1 schema tegelijk zoeken

250 tabellen (klassen) met per rij een instantie en per kolom alle mogelijke attributen

versnellen kan via indexering. 

GLOBAL CATALOG = gedraagt zich als 1 grote tabel met in de rijen elk object da je in AD hebt staat daar in maar over alle domein heen (forest) globaal over een verzameling van domein. 

In rijen enorm veel, maar in kolommen wil je maar paar attributen en de attributen die in global catalog staan en da attribuut noemt ismemberofpartialattributeset. Standaard zullen er wel wa op true staan. Dus heb je iets da erbij wil dan zet je da op true

om idee te hebbenhoeveel da gebruikt wordt


```
dsquery * .... -filter (^&(objectclass=attributeschema)(searchflags:...)) 1e bit
10 percent is indexed

(ismemberofpartialattributeset=TRUE) -> 191
dit is die reusachtige tabel
```

Is essentieel voor in te loggen die global catalog

een ingelogde gebruiker gaat eerst eens zeggen wie is DC voor dat toestel ook al is hij hetzelf 
en hij vraagt da aan de global catalog en hij doet da op basis van een suffix. 

en tweede reden een soort groepen die we gaan zien, da zijn de universele groepen die worden enkel maar bijgehouden in de global catalog en lidmaatschap van die groepen ga je nie weten als global catalog nie draait.

Het lid zijn van groep is mes langs twee kanten, kan rechten geven of nemen. 


Concl global catalog MOET beschikbaar zijn anders uitloggen

Opl: laat alle DC global catalog zijn (bij 1 domein is da ok) alle dc's = global catalog.

Dan is bijkomende tabel overal beschikbaar

Maar bij meerdere domeinen in elk domein ga alles repliceert worden. En dus ook die global catalog en das nie zo goe voor performatie je zou kunne zegge een paar is genoeg. 


Hoe kan je van DC global catalog maken.

En das in configuration in adsi edit in CN=sites en dan in subtak waar het zit (default) en daar heb je subtak servers die servers zijn containerobject van ander object. Hier NTDS Settings en tis daar da het verpakt wordt of een DC global catalog is of niet > opvragen properties

en die kiekens hebbe da gefoefelt in nen bit in **options** == binair ding en laagste bit geeft aan ben ik GC of niet 0x1 = (IS_GC). 

Zorg ervoor dat er altijd nen GC is en zorg ervoor dat er minstens 1 DC nen GC is. (Documentatie)
1 minstens GC 

bij 1 domein maak ze gwn allemaal gc bij meerdere neem wa gas terug neem er genoeg maar niet allemaal

(THEORIEVRAAG)


Microsoft heeft caching toegelaten (ookal is er geen GC) da hij gaat toelaten om u in te loggen. 

Cachen van credentials. Ge gaat kunnen inloggen maar de situatie kan dus wel veranderd zijn door een groep. 


Da aan en uitzetten is weer iets bitsgewijs

attribuut vinden -> tzit in default-first-site-name en daar staat er ook ntds site settings en da is een ander object van daarnet (hier ist specifiek voor de site dus voor alle servers, daarnet wast per server).

Weer een opties attribuut en de op vijf na laagste bit, die aan staat. hexadeciaal 20. En da duidt aan dat er gebruik gemaakt wordt van caching. Dit geldt nie voor mensen die nooit zijn ingelogt 

IS_GROUP_CACHING_ENABLED




Hoe maak je van een toestel een DC. Licentie nodig. Je moet service licentie kopen. Van onderbouw gelijkaardig. Maar die laat toe da LDAP draait. dcpromo applicatie. Ma da sta nergens op. 

Server manager > add roles en features (roles zijn specifiek voor windows server, features ook op andere teostellen). > Active Directory Domain Services dan krijg je die dcpromo.

Heb je keuze dan nee DC is zwaar, laat achterwege als nie nodig is.

Vroeger moest je da bij installatie kiezen. 

Nen gevaarlijke update van een machien bvb is best da je wegneemt van DC en dan voeg je het nadien terug toe. Dus nie in kritische toestand updaten. Zelf hardware toevoegen. Netwerk, geheugen, cpu is allemaal hoger bij DC. Er MOET nen DNS server aanwezig zijn die u moet herkennen. Moet nie windows zijn maar zal makkelijker zijn.

Everything is freaking dns problem.


Forest -> dns, let op bij fusies.

Mag dus ook meedere boomstructuren zijn en da is een forest van verschillende bomen, en toch gemeenschappelijke global catalog, en technisch zal er toch wel 1 onderschikt zijn maar het lijkt niet zo. 


je hoeft dns naamgeving niet identiek te zijn naar domeinnaamgeving.

nieuwe forest/tree/dc enal allemaal via dcpromo

om van toestel promoveren:

twee interessante opties:

1 van de opties is /answer 

al die antwoorden in dialoogvenster kan je hier koppelen 
dus super handig als je aantal DCs moet maken (automatisatie)

/adv van advanced ook nuttig

opt ogenlijkblik da je nieuew maakt begint hij te syncen met domein
in da bestaand domein da kan gigabytes gegevens zijn, en da moet dus allemaal overgezet worden en u netwerk uren platleggen of andere syncrhonatie nie mogelijk maken

hoeveelheid syncronisatie op netwerk is enorm

bij banken uitgesloten om dc te maken, de hoeveelheid data was te groot en alles lag plat

en daarvoor heb je advance zoda je op hdd, usb een copy geeft van domeincontroller van oude toestand (liefst zo recent mogelijk) maar dan ga hem tijdens de installatie van die lokale copy kan binnenhalen en daarna pas syncrhoniseren met de actuele sitatuie en de hoeveelheid is dan veeeeel minder!


NetBIOS naam = naam afgekort tot 15 hou da gewoon kort en al zeker niet dc maken

NTDS.bit -> zet da nie op een ram schijf zet da op een fysieke schijf, bij voorkeur logbestanden zelf op andere schijf en eigenlijk zelf op andere controller.

Twee schijven op twee verschillende controllers; op voorhand dns in orde brengen!

### kijken of toestel dc kan zijn 

dcdiag en da ga testen of je wel in upgradable toestand zijn

dcdiag /c --> zware test 


vroeger was de domeinstructuur vast; vroeger kon je ook dc nie van naamveranderen

