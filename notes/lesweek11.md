# Lesweek 11

(Op 04/12/2017)


## Vervolg file sharing

Waar worden die shares opgeslaan? Je zou denken AD maar das nie waar; per definitie in het locaal register van van die machine.

``Computer > local machine > System > currentcontrolset > services > Lanmanserver > shares (met subtak security = wie wat mag doen)``

Export van die shares tak > geeft tekstversie en dan wordt die volledige registry tak voor u opnieuw geconstrueerd. 

Het staat niet in AD maar das ook nie geheel waar je kan u definities in AD zetten. Maar dan moet je een configuratie aanzetten - distributed file systeem. 

1 van de 3 voordelen is da je dus de share in AD kan laten registreren. AD zal wel de omleiding doen naar het toestel zelf;

2 de share zelf kan samengesteld zijn uit verschillende deelcomponenten ; als applicatie weet je niet waar de share zich bevind

3 stukken van de hierarchy kun je laten repliceren (dan moeten ze op DC staan) maar je kan replicatie gebruiken die da op DC ondersteunen. Stukken die veel gebruikt worden kunnen als duplicaat aangeboden worden. 




Meerdere mogelijkheden 1 gemeenschappelijke share waar iedereen subtak/map heeft waar enkel daar de student aan kan andere mogelijkheid is da iedereen eigen share heeft. 

Nadeel van eerste share ; je koppelt je aan die share als je dan dir' t dan zie je al die mappen staan (ookal heb je geen toegang hebt). Je ziet dat ze bestaan. Er kan niets mis gaan maar nen audit er die gaat er wel op vallen. Microsoft heeft dat opgevangen door access based numeration toe te passen. De file service is aangepast dat als je nen dir doet in nen map waar er submappen zijn waar je geen toegang op hebt dan ziede het niet meer (= uitvoeringstijd is er wel dus er is vertraging). Dus da zet je dan aan op de opper share. 


Quotas kunnen ingesteld worden - het is niet het nuttigste werk want tis nie da ze da per se goe gaan doen maar welk werk bespaard voor sysadmin.

In windows heb je 2 methodes. 

1 manier geintroduceerd in 2000 (oudste manier): quotas op volumes. Alles wa onder zelfde gebruikersid samenzit worden tesamen gezet. Per object sid. Eigenaar krijgt een som. Limiet die je kan zetten kan soft of hard zijn. Soft - applicatie krijgt een warning (interactief krijg je visueel melding, maar applicatief gaat da soms verloren). Hard - die write operatie ga niemeer lukken (applicatieonafhankelijk). Heb je takken die gecomprimeerd zijn - het is toch de logische size die telt. Geen mogelijkheden om dat op groepen te maken. Het zit in het filesystem (ntfs) zelf. Het besturingssysteem moet er zelf niet veel voor doen.

2 manier in 2008 en dat is map gebasseerd ipv volume gebasseerd. Ongeacht wie is het op bestandshierarchie. Zinvol als er op een andere manier restricties zijn. Gekoppeld aand individuen of groepen van de gebruikers. Dat mechanisme wordt softwarematig door de fileservice gerealiseerd. Hier wordt er wel rekening gehouden met compressie. Doordat het softwarematig is kan je de reactie ook beter finetunen. 


Ipv quotums kan je ook file screens (extensies) zetten. Werkt volledig gelijk map gebasseerde quotums.


Toegangsmechanisme is van kracht op alle windows objecten. De toegang w bepaald door zelfde stramien.

Je krijgt bij inloggen een toegangstechniek en da verwijst naar uzelf en groepen waarvan je lid bent. 

Met dat inlogticket moet je handel openen (nadeel) vooraleer je iets anders doet. Het openen van die handel heeft als doel om u ticket te vergelijken met de machtigingsrechten van een object. Zolang je die handel open hebt liggen die operaties vast. Mocht het machtigingsmechanisme aangepast worden ge zou het niet weten. Mocht je gebruikersobject wijzigen zonder nieuwe login je zou het niet weten. Die opening is voor elk specifiek object. 

Bij bestanden - dubbel mechanisme - share (toegang) maar ook machtiging op individueel object. Zowel op mappen als individueel object. Meest destructieve heeft voorrang. Hoe het eruit ziet en waar het w bewaard is object afhankelijk. Security van file in file zelf; eigenschap van ad in ad. In ntfs is een file een object, 1 van zijn attributen is de inhoud, een ander security. Verschillende versies van een bestand kan in 1 container zitten (in macintosh niet op windows). U windows bestand kan dus ook meerdere dingen hebben ma da is afgeschermd voor windows clients. Het default attribuut heeft geen naam. : is scheidingsteken voor alternatief attribuut. 

Elk object bezit een security descriptor. 
- header
- owner sid (verantwoordelijke voor instellen van security descriptor, eigenaar laat toe dat dat het voor zichzelf w gedaan)
- primary group sid (POSIX applicaties, nu minder relevant)
- twee lijsten:
	* system access control list = zegt welke operaties er gelogt worden (kan uitgevoerd worden, maar zet je best uit voor performantie)
	* discretionary access control list = gaat machtigingsset bepalen, wie wa mag doen (of niet). Dit bevat een lijst een verwijzing wie je bent en een verwijzing wat je moogt doen. Dat is een ACE = access controle entry; Ge kunt er meerdere keren in staan. W gevolgd door een bitreeks. Bij mappen zijn dat 13 bits (ntfs). Bij AD het dubbel van het aantal attributen van een object (mag ik lezen/wijzigen). Da kan 100n bits zijn. (*****)

> Vb. Eigenaar trusted installer in system32 map en den administrator heeft niet het recht om te wijzigen. Dus je moet eerst uw eigen eigenaar maken wil je da aanpassen. Ownership overnemen is 2 stapsproces.

Admin mag overnemen (1) en eens hij het overgenomen heeft (2) dan kunde verder gaan.


(*****) Telkens er iets staat dat je het niet mag doen, dan mag je niet (punt). Staat er geen enkele bit die u verbiedt en 1 die er zegt je mag dan mag je. Je moet expliciete toelating hebben. 


Je kan programmatisch zeggen DACL is leeg, maar je kan ook zeggen w verwijderd. Er is geen! En dan is alles toegelaten. Dat is verschil tussen nen lege en een zonder DACL. 

Volgorde waarin het bewaard w noemt men canonieke volgorde, deny s eerst dan pas de allow s. Dus van zodra je deny vindt mag je stoppen. Je kan dit programmatisch wijzigen (niet aan te raden want zet je allow eerst en daarna deny dan gaat het toch allow zijn). En telkens je user interface opent - maakt hij ze toch canoniek. (Security reference monitor)

Er is gelukkig ook overervering. Als het niet in mijn eigen staat kijk naar iets hoger (ouder). 


Stel da jij geen deny hebt en u niveaus hogerop wel dan mag je toch (de denys hogerop hebben dus een prio lager). 

"Die 13 speciale machtiging moet je niet kennen". In user interface zie je er maar 6 staan maar er zijn er 13 en da heeft te maken met de historiek van vroeger. Atomaire zijn de echte (13). Moleculair (6). In UI heb je dus te maken met die moleculaire. Das voor alle objecten gelijkaardig. Special permission is nen kapstok die ik nie kan overzetten op doe moleculaire. Wijzigingen doe je op 1 niveau tegelijkertijd.

Permissie = machtiging =/= privileges.

Bij advanced settings -> permissions is den DACL en auditing SACL.


Effective access -> soort simulatie doen. Ma je kan het maar tot 1 enkel sid (wat vaak niet het geval is), is het niet zo interessant, zelf alles accumuleren. Weinig betrouwbaar beeld van de werkelijkheid. 

Bij een move:
Blijft behouden maar vermengd. Grafisch via cp doet hij dat niet. Gebruik utilities zoals scp die da wel doet of robocopy.

tools zoals icacls of subinacl, takeown kunnen je leven redden. 



Definieert machtigingen op groep en voeg die 1 user bij die groep! Als iets op speciale manier beveiligd moet worden, voeg groep in. 


## bestandssystemen

nummering heeft niets te maken met besturingssysteem

* ...
* logging van schijfactiviteiten = meer kans op recuperatie van verloren gegevens bij stroomuitval
* grotere volumes (vgln met oude fat systemen ja)
* compressie = standaard maar nie zo goe als nen zip; de compressie die gebruikt w is volgende windows bewaard bestanden in clusters (veelvoud van disksectoren) en compressie zegt ik ga in venster van 16 clusters gaan comprimeren (eerste 16 in zo weinig mogelijk) geen glijdend venster. En in beste geval heb je altijd 1 cluster nodig (ook voor maar 1 bit). Voor zo weinig mogelijk compressing te hebben. Compact is beter dan gui compress
* hardlinks (gelijk in unix) in een map zeggen er staat hier pointer naar zelfde fysieke bestand ; hier (in jaren 90) bestaan nog geen softlinks
* dynamisch uitbreiden zonder herformatie, ook spreiden over verschillende fysieke schijven -> spanned volume
* (hier begin jaren tweeduizend) 
* Opsplitsen deels op filesysteem (alles int blauw op de slide) maar nu ook delen op het besturingssystemen. Dus nen linux kan ook al die blauwe dingen doen of er mee om gaan. Eens er driver is voor ntfs is het goed. Nu in 2000 zegt men we gaan deel van de functies in OS gaan proppen. Wil je dat overhemelen dan dwing je het besturingssysteem linux en windows die logica te introduceren. Gaat niet gebeuren uit vrees op rechten van code. 
* reparsepunten - verwijzigingen die in het bestand zelf zitten en die zeggen door aan i/o dat ge iets speciaals doet
* mounten als in linux gaat ook op windows
* junction points dat zijn symbolische links gelijk in linux, zelfs mappen op vreemde systemen 
* sparse bestanden = ipv normale manier van inhoud in type length formaat opslaan (vooral als bestand weinig inhoud heeft)
* file markers (microsoft weg gedaan) = hier kon je als beheerder zeggen ik ga als schijf vol staat een file verwijderen, dat mag meestal nie van unen baas. Je gaat moeten zeggen ik heb backup gemaakt en dan hebben ze hem toevallig nodig. Hier kon je backup maken en da liet nen file achter precies of hij er stond maar op ogenblik da ze hem aanspreken dan gaan den eerste disk io traag gaan om hem binnen te halen en dan hebben ze hem. Schitterend maar na 2003 weg. 
* encryptie/decodering/compressie eigenlijk = ik moe die persoon zijn om da te kunnen lezen. 
* diskquota op voulmeniveau (eerste quota da mogelijk was)
* (vanaf 2003)
* historische versie van bestanden ; volume shadow copy = soort snapshot; bestanden die op da moment consistent zijn die backuppen. Elke omgeving die bestanden heeft die coherent me mekaar moeten zijn hebben daar voordeel van. 
* encrementeel teruggrijpen naar oudere versies; vorige versie bevat de encrementen om terug te krijgen en locale bevat huidige versie. 
* (vanaf 2008)
* minder corruptie = geen regelmatige checkdisk meer uitvoeren hij doet da voor u
* ondersteuning van transacties = voor sql server bvb. Er moeten twee i/os uitegevoeerd worden en ze moeten consistent zijn. Naart schijnt regedit via transactie.
* globale diskquota (tweede manier) 
* file screens = filteren op basis van extensies


Beviliging is op basis van groepen!