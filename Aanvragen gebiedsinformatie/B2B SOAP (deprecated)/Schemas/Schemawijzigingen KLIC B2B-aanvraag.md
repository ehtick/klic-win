﻿# Schemawijzigingen KLIC B2B-aanvraag

In dit document wordt een toelichting gegeven op de wijzigingen die worden/zijn doorgevoerd in de schema's die het interface met Kadaster KLIC definiëren.  \
Let wel dat de WSDL's ook worden/zijn aangepast, zodat ze verwijzen naar de juiste versie van de schema-definitie.


---------------------------------------------------------
## KlicB2BAanvraag

### Toelichting overgang tussen KlicB2BAanvraag-1.1 en KlicB2BAanvraag-1.2

- KlicB2BAanvraag-1.1 kan gebruikt blijven worden (backwards compatible), tot einde IMKL-upgrade overgang (1 mei 2022)
  - Uitzondering: Meldingen vanaf 3 januari 2022 zonder referentieveld (in XSD optioneel) worden dan wél afgekeurd. Dit is vanaf 3 januari 2022 namelijk een verplicht veld.

- KlicB2BAanvraag-1.2 kan vanaf medio juli 2021 al gebruikt worden; maar:
  - Het verzoek om  de velden `telefoon van ExtraContact` en `naam van ExtraContact` pas te gebruiken vanaf 3 januari 2022, aangezien deze pas per 3 januari 2022 wordt uitgeleverd in de XML (vanaf de start van de overgangsperiode IMKL-upgrade). Deze komt wel in de Ontvangstbevestiging te staan en die inconsistentie kan leiden tot verwarring. 
  - Let op: er is maar één extra emailadres, die op 2 manier doorgegeven kan worden. Als beide gevuld worden, wordt er maar één gebruikt: het nieuwe veld.

Merk op dat vanaf IMKL 2.1 het technisch toegestaan is om adressen op te geven zonder postcode. Echter voor het aanvrager-adres en opdrachtgever-adres, behouden we deze verplichting wel.  \
Het opgeven van een Locatieadres (DAS) zonder postcode en het opvragen van huisaansluitschetsen (HAS) van adressen zonder postcode; kan alleen indien er een BAGid van dergelijk adresseerbare object wordt opgegeven. Dit komt voor bij o.a. transformatorhuisjes.



### Wijzigingen KlicB2BAanvraag-1.2

- `ExtraEmail` van Aanvrager: \
Gemarkeerd als 'OBSOLETE'. Dit is vervangen door `Email` in het nieuwe attribuut `ExtraContact`

- `ExtraContact` van Aanvrager \
Nieuw optioneel attribuut. Extra contactgegevens van de aanvrager waarmee de graafsector in contact kan treden voor nadere informatie over de aanvraag.

- `Fax` van Contactegegevens \
Gemarkeerd als 'OBSOLETE'.

- `ExtraContactType`
Nieuw type toegevoegd. Extra contactgegevens van de aanvrager waarmee de graafsector in contact kan treden voor nadere informatie over de aanvraag.

- `Locatieomschrijving` van ProductSpecificate
Nieuw optioneel attribuut. Nadere omschrijving van de locatie waar de werkzaamheden plaatsvinden.

- `Huisnummer` van Adres
Optioneel gemaakt.

- `Postcode` van Adres
Optioneel gemaakt.

### Wijzigingen KlicB2BAanvraag-1.1

In de nieuwe versie van het interface _KlicB2BAanvraag_ zijn de volgende optionele attributen van een gebiedsinformatie-aanvraag toegevoegd:

- `KvkNummer` van aanvrager;  \
het KvK-nummer (Kamer van Koophandel) van de aanvrager (klant) van de aanvraag.  \
Het KvK-Nummer van de klant kan geregistreerd worden in het relatiebeheer-systeem van het Kadaster (CRM). Zolang deze niet is geverifieerd en eventueel is geactualiseerd, moet er een mogelijkheid komen om deze per aanvraag op te geven.

- `KvkNummer` van opdrachtgever;  \
het KvK-nummer (Kamer van Koophandel) van de opdrachtgever voor de aanvraag.  \
Dit KvK-nummer van organisaties is niet bekend bij het Kadaster. Er moet een mogelijkheid komen om deze bij een graafmelding op te geven.

- `VoorbereidingMedegebruikFysiekeInfrastructuur`;  \
een specifiek orientatieverzoek ter voorbereiding op een verzoek tot medegebruik van fysieke infrastructuur. Deze mogelijkheid moet worden geboden in het kader van EU-stimulering breedband (nieuwe WIBON-regelgeving).

- `VoorbereidingCoordinatieCivieleWerken`;  \
een specifiek orientatieverzoek ter voorbereiding op een verzoek tot coördinatie van civiele werken. Deze mogelijkheid moet worden geboden in het kader van EU-stimulering breedband (nieuwe WIBON-regelgeving).

- `IdentificatieBAG` van een dichtstbijzijnd adres (DAS);  \
de identificatie van het adresseerbare object van een dichtstbijzijnd adres.  \
Deze `IdentificatieBAG` is in BAG vastgelegd als identificatie van een adresseerbaar object (_Verblijfsobject_, _Ligplaats_ of _Standplaats_).  \
De identificatie is leidend voor de verwerking van een Klic-aanvraag.  \
Als er van een dichtstbijzijnd adres een `IdentificatieBAG` wordt meegegeven, hoeven geen overige adresgegevens te worden meegegeven. Het eventueel meegegeven adres moet het hoofdadres zijn van het adresseerbare object en overeenstemmen met de adresgegevens zoals deze in BAG zijn vastgelegd.
Is er geen `IdentificatieBAG` meegegeven, dan moeten minimaal een `huisnummer` en `postcode` worden meegegeven voor de bepaling van een geldig dichtsbijzijnd adres.

- `IdentificatieBAG` van een huisaansluitschets-adres (HAS);  \
de identificatie van het adresseerbare object van een adres waarvoor een huisaansluitschets wordt gevraagd.  \
Ook deze `IdentificatieBAG` is in BAG vastgelegd als identificatie van een adresseerbaar object (_Verblijfsobject_, _Ligplaats_ of _Standplaats_).  \
De identificatie is leidend voor de verwerking van een Klic-aanvraag.  \
Als er van een huisaansluitschets-adres een `IdentificatieBAG` wordt meegegeven, hoeven geen overige adresgegevens te worden meegegeven. Het eventueel meegegeven adres moet het hoofdadres zijn van het adresseerbare object en overeenstemmen met de adresgegevens zoals deze in BAG zijn vastgelegd.
Is er geen `IdentificatieBAG` meegegeven, dan moeten minimaal een `huisnummer` en `postcode` worden meegegeven voor de bepaling van een geldig dichtsbijzijnd adres.

- `Informatiepolygoon`;  \
de geometrie van het gebied (een polygoon) waarover informatie gevraagd wordt.  \
Voor een graafmelding en een calamiteitenmelding moet er een mogelijkheid worden geboden om rond het graafgebied een ruimer gebied aan te geven waarover informatie wordt gewenst.  \
Een opgegeven informatiegebied moet een graafgebied volledig omvatten: niets van de graafpolygoon mag buiten de informatiepolygoon liggen.

---------------------------------------------------------
## KlicB2BBetrokkenBeheerders

### Wijzigingen KlicB2BBetrokkenBeheerders-1.1

In de nieuwe versie van het interface _KlicB2BBetrokkenBeheerders_ is het volgende optionele attribuut toegevoegd aan het overzicht van netbeheerders met een belang in het aangegeven gebied:

- `ThemaAlleenInInformatiepolygoon` toegevoegd bij `ContactPerThema`;  \
met deze indicator wordt aangegeven of het thema van het belang van een netbeheerder alleen in de informatiepolygoon zit, of ook in de graafpolygoon.

---------------------------------------------------------
## Implementatie

Er is voor gekozen om één wijziging alle attributen die in de nabije toekomst gebruikt moeten kunnen worden, op te nemen in een nieuwe versie van het interface (versie 1.1).  \
Alle nieuwe attributen zijn (nu nog) optioneel, zodat een gebruikersapplicatie nog niet hoeft te worden aangepast als de nieuwe versie wordt uitgerold. Deze versie is daarmee backwards compatible.

In eerste instantie controleren we alleen of het gebruik van de nieuwe attributen valide is, en geen verstoring geeft op de verwerking in KLIC.

Vervolgens zal de functionaliteit voor het gebruik van de nieuwe attributen gefaseerd worden uitgerold.  \
Deze fasering ziet er voorlopig als volgt uit:

1. Het gebruik van `IdentificatieBAG` voor een dichtstbijzijnd adres (DAS) en een huisaansluitschets-adres (HAS).
2. Het gebruik van opties voor het aanvragen van een orientatieverzoek in het kader van de EU-stimulering breedband (`VoorbereidingMedegebruikFysiekeInfrastructuur` en `VoorbereidingCoordinatieCivieleWerken`).
3. Het vastleggen en gebruiken van een `KvkNummer` bij een aanvrager en opdrachtgever.
4. Het vastleggen en gebruiken van `Informatiepolygoon` bij een graafmelding of calamiteitenmelding.  \
Bij een calamiteitenmelding wordt dan ook de indicator `ThemaAlleenInInformatiepolygoon` teruggegeven.

Voor een overzicht van de geplande en gerealiseerde implementaties van deze functionaliteit, zie:
* [KLIC - Geplande werkzaamheden](../KLIC%20-%20Geplande%20werkzaamheden.md) 
* [KLIC - Release notes](../KLIC%20-%20Release%20notes.md) 

