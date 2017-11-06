# Lesweek 7

(Op 06/11/2017)

## Het gegevensarchief

Minimaal 3 partities:
- domeingegevens
- configuratiegegevens
- het reële schema (gegevens) (vergelijkbaar met wmi repo)

Boomstructuur van verschillende domeinen. 
Voordeel tov wmi is dat ze met dezelfde tools kunnen benaderd worden. 

**Tools**
```
adsieedit.msc
ldifde.exe
csvde.exe
schmmgmt.dll
dsquery * -scope ...-attr
```

3 soorten objecten voor schemagegeven. Zogenaamd attribuutschemaobject dat de attributen beschrijft. Je moet wel degelijk attribuutschemaobject kunnen vastpakken programmatisch om de eigenschappen te weten te komen. Er zijn nog attributen maar eigenlijk vooral interessant als je nieuwe dingen wilt maken. 


Viervoudige unieke naamgeving:
- common-name (cn)
- ldapdisplayname
- ...
- attributeid

Via ADSI edit -> properties vragen --> we zien dat er bijkomende attributen zijn:
- schemaIDGUID (vorm van guid) maar verschil me gewoon guid is da je hier eigenlijk een guid wilt die overal gelijk is voor programmateurische doeleinden. Maar ge moet daar zelf voor zorgen (niet overlaten aan systeem zelf).
- attributeID doet u beetje denken aan snmp zo me puntjes en nummers (1.2.840.113556.1.4.769) (een uniek nummer). Die 1.2 staat voor ldap binnen de onderneming en derde nummer staat voor het land 840 VS en 56 is belgie. Vierde nummer is van de onderneming. Eigenaar 1.2.56 daar kan je nummers aan vragen en hogent had zo een nummerke.

1.2.528 is van holland en 1.2.528.56 van nederland voor belgie

Alternatief voor nummerke te pakken te krijgen via oidgen


```
uuidgen
# en je krijgt een uid
#(zit bij visual studio)

oidgen 
# en je krijgt super diep nummerke onder de microsoft tak een uniek nummer 
```


Lijstje van attributesyntax (steek da in die dsquery)

```
... -limit 0 -filter(objectclass=attributeschema) -attr attributesyntax | tail -n +2 | sort | uniq | nl
... -limit 0 -filter(objectclass=attributeschema) -attr attributesyntax omsyntax | tail -n +2 | sort | uniq | nl

oMSyntax bestaat ook en is een subtype 2 = (INTEGER)


rangeLower en rangeUpper leggen beneden en bovengrens vast

```


nieuwe query
```

dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interim J" -p "Interim J" -limit 0 -filter

&((objectclass=attributeschema) # hier staat nen AND
(|((rangelower=*)(rangeupper=*)))) # dus hier staat er nen OR binnen de AND

-attr attributesystanx omsyntax rangelower rangeupper | tail -n + 2 | sort | nl

# query geeft fouten 
# programmatisch is da ok 
# maar commandprompt doe lastig op die & en | 
# je moet speciale betekenis wegnemen (backslashen) in windows is da circonflex ^



dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interim J" -p "Interim J" -limit 0 -filter

^&((objectclass=attributeschema) # hier staat nen AND
(^|((rangelower=*)(rangeupper=*)))) # dus hier staat er nen OR binnen de AND

-attr ldapdisplayname omsyntax rangelower rangeupper | tail -n + 2 | sort | nl



```


Kijken naar
isSingleValued staat op TRUE
en is dus enkelvoudig attribuut

zoeken op niet singlevalued

```
dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interim J" -p "Interim J" -limit 0 -filter

^&((objectclass=attributeschema)(issinglevalued=true)) # FAALT is case sensitive 

-attr ldapdisplayname omsyntax rangelower rangeupper | tail -n + 2 | sort | nl

# dus


dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interim J" -p "Interim J" -limit 0 -filter

^&((objectclass=attributeschema)(issinglevalued=TRUE))

-attr ldapdisplayname omsyntax rangelower rangeupper | tail -n + 2 | sort | nl


# nu willen we negatie daarop



dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interim J" -p "Interim J" -limit 0 -filter

^&((objectclass=attributeschema)(!(issinglevalued=TRUE)))

-attr ldapdisplayname omsyntax rangelower rangeupper | tail -n + 2 | sort | nl


```


Attribuut da je veel zoekt wel indexering attribuut da je veel aanpast best niet indexeren. 

De eigenschappen van een attribuut is overal dezelfde! Onafhankelijk van welke klasse het gebruikt w


searchFlags staat op 0x0 = ( ) # hier zie je of het indexeert is of nie


```
dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interim J" -p "Interim J" -limit 0 -filter

^&((objectclass=attributeschema)(!(issinglevalued=TRUE)))

-attr ldapdisplayname searchflags | tail -n + 2 | sort | nl

# geven allemaal cijferkes
# tis een bitveld
# en verschillende bits in da bitveld hebben andere betekenis
# feit of iets indexeert is zit in de laatste bit! (dus oneven)

searchflags is nen clusterfuck van bits voor vanalles -.- geen echte OO eigenlijk stom


# geef overzicht van alle attributen die indexed zijn


dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interim J" -p "Interim J" -limit 0 -filter

^&((objectclass=attributeschema)(!(searchflags=1)))

-attr ldapdisplayname searchflags | tail -n + 2 | sort | nl | tail

# Ma da zijn ze nie allemaal want ge moet die bit eruit pietsen
# via vlagje gaat ge de betekenis van = veranderen 
# voor bit operaties

dsquery * "cn=schema,cn=configuration,dc=iii,dc=hogent,dc=be" -s satan.hogent.be -u "Interim J" -p "Interim J" -limit 0 -filter

^&((objectclass=attributeschema)(!(searchflags:1.2.840.113556.1.4.803:=1))) # VLAGJE !!!!!!!!!!!

-attr ldapdisplayname searchflags | tail -n + 2 | sort | nl | tail

# numerieke waarde tss :: van vlagje
# dus 1.2.840.113556.1.4.803

# en nu krijg je dan wel via die bit check het juiste


# :..803:=5

Laagste en op twee na laagste (soort EN)

# :..804=5

Laagste of op twee na laagste (soort OF)


# 1941 is nog nen super handige !!! onthouden


# 803:=2


```



Je kan op je eigen toestel een lichtgewicht ldap ding installeren (adam, lightweight ad services). Ge moet da nie installeren maar die hulpapplicatie is wel interessant.
ADSchemaAnalyzer


```
OPMERKING
> bij adsi > Remove NIET VERGETEN!!


> load target schema
>> satan.hogent.be
>> administrator
>> ww
en da is genoeg

Bij attributen zie je LDAPdisplayname te zien.

> rechtermuisknop properties en das veel sneller


msTPM-SrkPubThumbprint is 11 = INDEX | CONTAINER_INDEX | PRESERVE_ON_DELETE

(samen met container ge-indext)


preserve-on-delete -> als het object verwijdert wordt, blijft het attribuut behouden 
--> Een object die gerepliceerd is moet getagged worden (het "gewoon" verwijderen gaat niet) je moet da taggen "het mag weg". En da zeg je hier.

Een grafsteen object thombe. 


Hier zeg je dus het attribuut dat preserve_on_delete heeft dat behoudt zijn attributen in zijn grafsteen. Dus zelf in zijn te verwijderen versie da attribuut blijft behouden.

Jarenlang behouden

Nu in AD (sinds 2012 fzo) heeft men da ook gebruikt om een soort vuilbakmechanisme te maken.

Een grafsteen echt verwijderen dan duurt soms wel 2 maand.

Da heeft een gevolg:

Stel DC neem je uit bedrijf en je wacht 2 maanden voor je hem weer aanschakeld. Dan zal hij zichzelf niemeer willen syncen met andere DCs. 

```


```
# 803:=4

Op twee na laatste bits aan staan

hier is da lijstje van 14 (5n en 13n)


Ge wilt dus een OR doen op meerdere attributen

en die gaat die attributen taggen als ambigous name resolving
anr staat ingesteld op joris das equivalent met een OR van al die attributen


... -filter (anr=joris) # query geeft geen resultaat
# want je laat het los op schemagegevens
# maar je moet da loslaten op domeingegevens

dsquery * "cn=... enal weg"

dsquery * "dc=iii,dc=hogent,dc=be" -s satan.hogent.be ... -limit 0 -filter(anr = joris)

Werkt wel





# 803:=16 (dus vier laatste bits)

bij searchflags staat er nu 0x10 (COPY) 
merk dus op dat da niets te maken heeft me zoeken


# 803:>31 # groter dan 31
# falikant aflopen
# groter dan of gelijk da besta maar groter dan NIET!

:>=32


binair filteren dus heel die string moet weg
en het moet dus zijn
searchflags^>=32

> omleiden naar bestand! dus circonflex voor cmd eh! nie als je programmeert


1 van die bits is bvb nen security bit

Nen andere bit die interessant 

ben je gerepliceerd naar/van read_only domein controllers
die krijgen een niet wijzigbare copy (voor veiligheidsredenen)
die mogen alle domeingegevens krijgen behalve sommige attributen die ze locaal nie mogen hebben


```


Ook **systemflags** is zo een bitverhaal.

```
...(systemflags:1.2.840.113556.1.4.804:=5)...

Twee interessante

NOT_REPLICATED en SCHEMA_BASE_OBJECT bij ownerBL 
Tis de moeite nie da ge da kopieert, enkel voor locaal.
Locale caches

lastlogoff
lastlogon 

die zijn ook bvb lokaal (moeite nie om te repliceren)

```
