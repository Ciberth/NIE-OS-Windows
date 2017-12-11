# Lesweek 12

(Op 11/12/2017)



Op elk toestel heb je lokale groepen/accounts (vs.) domeinaccounts/groepen.

Op toestellen die geen DC zijn dan blijven die lokale accounts bestaan. Bij DC is da iets speciaals. Zijn eigen groepen en accounts moet hij opofferen. Op ogenblik da je die degradeert van DC naar gewoon toestel dan zijn die terug (dus niet verwijderd, enkel onzichtbaar en onbruikbaar). 

Configuratie lokale -> niet behandelen, domein wel (zie labo). 

``dsa.msc`` Doet niet meer dan attributen verpakken in tabpaginas. 
Je kan wel meerdere gebruikers tegelijk aanduiden en meerdere tegelijk wijzigen! Dat is wel iets moois. Je moet de deelverzameling aanduiden op alle geselecteerde gebruikers. 

Als je adsi goed beheerst zijn al die cmd programmas overbodig. Ook eigen conventies zijn niet handig, attribuutnaam =/= ldapnaam. 

``Wel handig: dsquery en dsmove (bij verkeerd geplaatste zaken), movetree kan zelf over domeingrenzen, redirusr (die gebruikers moeten op die locatie staan)``

``net user`` (komt uit de beginjaren van windows) als usernaam moet je wel samaccountnaam gebruiken. 

Groepen die in het domein bestaan 2 eigenschap:
1. (waardeloos) type van de groep beveiligingsgroepen vs distributiegroepen (voor mailaccounts die je groepeert bvb nl externe emailaddressen)
Er is een limiet aan beveiligingsgroepen! Je kan beide groepen naar mekaar laten converteren. 
2. onderscheid da veel belagrijker is, is de **scope** waar zijn die groepen allemaal zichtbaar. Universeel vereist dat je functioneel niveau hoog genoeg is. Veel van die aspecten worden maar toegankelijk als functioneel niveau hoog genoeg is (we veronderstellen da hier).
De transitioneele (lokaal, globale) zijn belangrijker dan de universele (zo goed als niet nodig tenzij da je voor gemakszucht kiest).

De ene groep met lokale scope - da zijn niet de lokale groepen die op toestel staan (zonder ad) das iets totaal iets anders. Een DC ziet enkel domein lokale en niet lokaal lokale.

Domein groepen met lokale scope - maak je aan op een domeincontroller, de eigenschappen van die groep w onmiddelijk gerecipleerd naar andere DCs en zijn zichtbaar voor toestellen die geen DC zijn (vroegere/lagere functioneel niveaus dan waren die enkel zichtbaar op DCs; vroeger was praktisch alles DCs da komt daarvan ma tis dus pas later gekomen da het op werkstationnen ook gekomen is.) Wat kunne die bevatten: minst kieskeurige (ze kunne bijna alles bevatten) leden van eigen domein maar ook externe domeinen waarmee een vertrouwensrelatie gelegd is (is die relatie er niet dan niet). Andere domein moet getrust worden. Ze kunnen ook alle andere soorten groepen bevatten (en ook gebruikers). Zeer weinig kieskeurig. Maar wat hun zichtbaarheid betreft; ze zijn enkel zichtbaar in domein zelf en lager functioneel niveau enkel op dcs zelf. 
Gebruikt worden om machtigingen te definieren. Je moet de discipline hebben om groepen te maken op individuele basis. Typisch zo een lokale groepen en dan machtigingen. Dus machtigingen nooit op gebruiker doen maar maak groep en steek hem daar dan in. Groep zelf komt niet in de global catalog terecht! 

Groepen met globale scope. Die hebben inverse karakteristieken van locale.
Zeer kieskeurig wat ze kunnen bevatten maar zichtbaarheid is veel groter. Binnen elk domein ga je mensen groeperen in globale groepen. "allemaal in die faculteit", "allemaal student", "da vak", "die opleiding"... administratie.

Voor een externe in ander domein is het dan makkelijk om die globale groep lid te maken van een locale groep.

3 stapsprocess

Je bent lid van globale groep (geeft u een profiel) kan meerdere zijn, dan gaan globale groepen lid worden van locale groep en daardoor krijg je toegang tot resources. Volg deze gang van zaken! Iets meer werk int begin maar beste manier van werken. Steek user in globale groep en steek die groep in locale groep. Lidmaatschap van groepe zijn veel gemakkelijker. De activiteiten die je wil doen als admin. 

Eig van globale groep:
enkel leden van eigen domein (ze kunne geen locale groepen, universele groepen ehbben) maar zichtbaarheid is overal. Zit in AD dus w gedeeld over gans domein. 

resources worden beheerd door lokaal lokale groep "Users". Gewone gebruiker is vaak lid van "domain users" en die groep is in elke werkpost opgenomen in groep "users"

Administrators en Users zijn lokaal lokaal groepen en bij het toevoegen gaat, die volgorde dan in orde komen (zie slide globale of lokale groepen). 

De vertrouwenrelatie is altijd 1 richting (niet te vergeten). 


Zie 4 laag figuur. 
Pijl = kan opgenomen worden in die groep
pijl naar resource = opgenomen worden in acl van bron

kruislings (over domeinen heen)
pijl van user naar global group ga nie 
pijl van domain local group naar resource ga nie


Wie is er verantwoordelijk beheer van groepen. 


ACL van bronnen da moet ik zelf doen (admin) en da doe ik met nen lokale groep

wie dat er lid is van welke globale groep da trek ik mij nie aan als admin (wespennest), wie wel? Personeelsdiensten! Zei kunnen lidmaatschap toekennen, zorg da zij het kunnen. Globale groep dus en alleen dat! Je wilt dat deel van een objectinvulling overlaten (lidmaatschap globale groepen) laten uitvoeren door iemand anders. 

Ze moeten da kunnen doen (machtigingen) geven.
En moet op makkelijk manier uitgevoerd worden. Kan ook via nen excel zijn (zorg da om 5 uur da klaar staat en ge verwerkt da). User interfaces op maat is meestal wa moeilijker de manier waarop das onderling af te spreken. 

IT auditers - geef ze de mogelijkheid om hun regels toe te passen. 

Da was er altijd al en da werkt perfect! 

Mensen begonnen te klagen - microsoft geven toe - en voeren iets in nl hier universele groepen

op papier - beste van alle twee.

stel 2 domeinen 2 kleurenprinters - dus 2 lokale groepen - laten we daar universele groep van maken en ge zegt dan vergeet lokaal en lokaal ge hebt universele.

Gevolg/nadelen:
- bestaan enkel in global catalog (bestaan niet in domein niveau)
- daardoor meer replicatieverkeer op alle domeinen
- bij inloggen spek aan u been; groepen met verbod; ge moet bij het inloggen beschikking hebben over global catalog om te weten tot welke universele groepen ge behoort
- (tssdoor) globale groepen zitten in GC maar lokale niet (trouwens)
- performantie zeker nie goe, samenstelling van toegangsticket duurt lang (caching aanzetten wa ook nie al te goe is)

Dus eigenlijk enkel goe bij meerdere domeinen die gelijkaardig zijn ma meestal ebde da nie voor. 

Microsoft zelf gebruikt da bijna niet. Maar 2 universele groepen zijn gedefinieerd. Al de rest blijft lokaal/globaal (driestapsproces). Eerder gemakszucht. Ma universele groepen bestaan niet op domeinniveau. Elke user van gelijk welk domein kan toegevoegd worden. En daarmee kan je gelijk welke locale gaan beveiliging. Werkelijk tussen gefriemeld tss de twee niveaus. 


3 mogelijkheden dan - ofwel geen universele groepen (aan te raden) - ofwel enkele universele groepen (bij 1 domein als je daar SOWIESO in blijft en da weet je nie naar de toekomst toe) - ofwel de twee (of drie eigenlijk) mengen.

In de praktijk 2 omstandigheden bij gebruik; bij installatie bestaan er 2 Schema Admins en Enterprise Admins (partitions container aanpassen; nieuwe partities/domeinen maken) Bij schemas moet je zeer selectief zijn! Laat da maar zo gelijk het is.

**Conversies** is ook mogelijk!


Van universaal -> globaal ja da ga gaan op voorwaarde da er enkel entiteiten in zitten van 1 domein (in universeel kan da over het domein heen) dus ge moet daar rekening mee houden! Die regels moeten bewaard blijven. Van globaal naar locaal gaat ook (via universele) zolang je alle regels respecteerd. 


Mmember (zonder s) voor creatie! Niet memberof da ga nie rechtstreeks!!

Domain users = iedereen lid (wijsgemaakt) ge gaat zien niemand is daar lid van. 
Van welke groep ben ik lid. 

```
net user thoma

dsquery * dc=iii,dc=hogent,dc=be -filter (objectclass=group) |tail 

# alle groepen waar jomoreau lid is
dsquery * dc=iii,dc=hogent,dc=be -filter (^&(objectclass=group)(member=cn=jomoreau,ou=docenten,ou=ii,dc=iii,dc=hogent,dc=be)) |tail 

# domein users niet te zien nie ingevuld
# ingewikkeld gemaakt
# u groeplidmaatschap voor alle groepen bijgehouden in member attribuut behalve voor die ene groep (domain users) een uitzondering
# en dat is het attribuut primaryGroupId -> je zal 513 zien staan bij iedereen staan (aangekleefd als relatieve identifier) en zo is iedereen lid van één groep (domain users) verwijzen naar de laatste twee bytes Ge moet nen primaire groep hebben! 

# Groep big

net group big # members jomoreau

net group bigger # niemand lid
# nesting kan da beestje nie aan


dsquery * cn=bigger,ou=docenten,ou=ii,dc=iii,dc=hogent,dc=be -attr member

# daar zit big in

dsquery * cn=big,ou=docenten,ou=ii,dc=iii,dc=hogent,dc=be -attr member

# in biggest zit bigger
dsquery * cn=biggest,ou=docenten,ou=ii,dc=iii,dc=hogent,dc=be -attr member


# en als je da zo opvroeg daarnet zie je enkel big (terwijl je ook via big ook lid bent van bigger en biggest)

Geneste groepen als vraag!!




dsquery * dc=iii,dc=hogent,dc=be -filter (^&(objectclass=group)(member:1.2.840.113556.1.4.1941:cn=jomoreau,ou=docenten,ou=ii,dc=iii,dc=hogent,dc=be)) |tail 

# VLAG 1941 KENNEN!!

# en nu sta hierarchische groep erbij geen recursie nodig!
# bespaart veel werk! 
# zo zoek je dus ook de groepen waar je onrechtstreeks lid van bent. 

```


Gebruikers en gebruikersgroepen

Wie doet het invullen van groepen. 



Delegeren van beheersrechten; ACL elementen aanpassen - afwijken van de standaard daarom noemt da zo. Het is ervoor zorgen dat sommige mensen sommige objecten of attributen van objecten kunnen wijzigen.

OUs : hierarchische objecten (achterwege laten als je niet moet delegeren en als je geen gpos gaat hebben)
hoofdzaak = afwijkende regels instellen op hierarchie. 




Speciale lokale groepen -- builtin lokale groepen (die kan je niet van plaats veranderen); die definitie verwijst naar vaste plaats dus die mogen niet van plaats verwijderd worden binnen AD.

Voor de rest gedraagd zich da als lokale groep. 

Aantal lokale groepen die door automatisch procedure ingevuld worden.

2 belangrijke

1. everyone = iedereen die ook geen inlogprocedure heeft ondergaan
2. authenticated users = users die ingelogd hebben/zijn (dus je wordt lid hiervan na inlogprocedure)


Alle acl zeggen dat de creatorowner (impliciete groep = leden ga je niet kunnen zien) alles mag doen met da object en wnr ben je lid, als je de owner bent van da object. 

Creator owner heeft alle toegang, en ook administrator heeft ook toegang ma de een kan de andere uitsluiten. Microsoft heeft vanaf 2008 een nieuwe impliciete groep gemaakt (de owner rights). Die doen default niets; maar als je admin wilt dat je de enige bent die verantwoordelijk is dan moet je owner rights deny zetten bij change permissions. Zo kunnen de owners (creator owner) er ook nie aan. In da opzicht is die **owner rights** wel belangrijk!



Terug naar hierarchie.


Bij elk object heb je evenveel bits het dubbel aantal bits van het aantal attributen. 

```
dir dss* # in system32

dssec.dat

...=7 # da attribuut moe je in user interface niet tonen
# 1 of 2 enkel lees of schrijfpermissie
@=7 # da oject in die klasses moeten niet getoond worden als ik iets laat overerveren

# hiermee kan je de interface dus enorm beperken
# smijt het hierbij = 7 en interface w altijd maar eenvoudiger en eenvoudiger

# het beperkt de zienswijze permissions acl security per container aanpassen dus


# om 1 keer gui en dan emuleren doe gebruik je tool dsacls

dsacls ...


```




Delegation of control wizard

> lijst van 13 voorkomende taken die je delegeerd. 
Da wordt gevoed door file en je kan da uitbreiden

**delegwiz.inf**  (in windows)



Hoe ga je gebruikers da laten doen

- geef interface waar jij mee werkt

onnozel: je kan van elk object zeggen "managedby" das louter informatief altijd geweest - ma microsoft kreeg klachten en nu kunde daar 1 gebruiker (geen groep) die het member attribuut kan wijzigen. Das dwaas. 


Moleculaire wijziging -> klein aantal attributen

klasse 200 attributen andere 150.

Je kan een aantal atomaire ZELF verpakken tot moleculaire eigenschap. En de definitie zit in AD. De definitie is Property Set (goe weggemoffeld). 

Configuratie gegevens bekijken > configuration > extended rights > daarin zit er een lijst van property sets da zijn dus allemaal verzamelingen van atomaire attributen. Welke attributen zijn het da zit nie in da object zelf; da is "gewoon" een definitie van een moleculair aspect. Maar wa zit er in zie je niet. Om te weten wa er al dan nie in zit. Je kan nie tot meerdere behoren maar je moet niet. Daarvoor moet je het schema ernaast leggen. 

Attribuut van moleculaire eigenschap is er een rightsguid =128 bits lang numerieke identificatie. En nu kan je in securityguid of je deel wil uitmaken van moleculaire property sets. 


Geef overzicht van property sets en alle attributen die er bij horen. 

wrightsguid en je loopt alle attributen af en van zodra die matchen weet je waarbij die horen.


Je kan het gemakkelijk zien ook namelijk 

AD DS/LDS schema analyzer gebruiken en daar staat property sets ook daar zie je atomaire attributen die deel uit maken van de properties; 