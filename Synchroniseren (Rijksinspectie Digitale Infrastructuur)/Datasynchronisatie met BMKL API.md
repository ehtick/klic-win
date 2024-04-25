﻿# Data synchronisatie met de KLIC BMKL API

## Inleiding

De BMKL API is zo ontworpen dat verschillende doelgroepen deze resources op een identieke wijze kunnen benaderen.
De resources worden op dezelfde manier benaderd door netbeheerders, service providers en AT.
In dit document wordt toegelicht hoe deze API gebruikt kan worden door AT voor het synchroniseren met een eigen database.

## Inhoudsopgave

- [Data synchronisatie met de KLIC BMKL API](#data-synchronisatie-met-de-klic-bmkl-api)
    - [Inleiding](#inleiding)
    - [Inhoudsopgave](#inhoudsopgave)
    - [Objecten](#objecten)
    - [Url-structuur](#url-structuur)
        - [Endpoints](#endpoints)
        - [Authenticatie](#authenticatie)
    - [Pagineren](#pagineren)
- [Use casemodel datasynchronisatie met BMKL API](#use-casemodel-data-synchronisatie-met-bmkl-api)
- [Voorbeeldberichten per endpoint](#voorbeeldberichten-per-endpoint)
    - [gebiedsinformatieAanvragen synchroniseren](#gebiedsinformatieaanvragen-synchroniseren)
    - [beheerdersinformatieAanvragen synchroniseren](#beheerdersinformatieaanvragen-synchroniseren)
    - [beheerdersinformatieLeveringen synchroniseren](#beheerdersinformatieleveringen-synchroniseren)
    - [gebiedsinformatieLeveringen synchroniseren](#gebiedsinformatieleveringen-synchroniseren)

## Objecten

De volgende objecten spelen een rol in de BMKL API.
- __gebiedsinformatieAanvraag__  \
Dit is de aanvraag zoals deze door de grondroerder wordt gedaan.
- __beheerdersinformatieAanvraag__  \
Dit zijn de beheerdersinformatieAanvragen die gestuurd worden naar de netbeheerders die een belang hebben in het selectiegebied van de gebiedsinformatieAanvraag.
- __aanlevering (beheerdersinformatie)__  \
De beheerdersinformatie die aangeleverd wordt door de beheerder op basis van de beheerdersinformatieAanvraag. Dit kunnen meerdere afgekeurde leveringen zijn en + maximaal 1 goedgekeurde levering. (Alleen beschikbaar voor decentrale netbeheerders)
- __beheerdersinformatieLevering__  \
Dit is de gevalideerde decentrale aanlevering of de centrale aanlevering aangevuld met de thema's die daarin geleverd zijn. Hiervan is er maximaal 1 per beheerdersinformatieAanvraag en deze is pas beschikbaar ná een succesvolle aanlevering. Indien gevraagd op het __/zip__ endpoint levert dit een levering met de uitsnede van de gebiedsinformatie-levering voor de betreffende netbeheerder.
- __gebiedsinformatieLeveringen__  \
De gebiedsinformatieLevering in json, of als ziplevering. Dit kunnen één of meer (deel)leveringen zijn. Dit object is pas beschikbaar na een daadwerkelijke levering.
- __terugmeldingen__  \
Eén of meer terugmeldingen op een gebiedsinformatieLevering.
- __beheerdersTerugmeldingen__  \
De terugmeldingen die op basis van een terugmelding doorgestuurd worden naar de betrokken netbeheerders.

Voor AT zijn de objecten _gebiedsinformatieAanvraag_, _beheerdersinformatieAanvraag_, _beheerdersinformatieLevering_ en _gebiedsinformatieLevering_ van belang.

## Url-structuur
De objecten van deze API zijn ondergebracht in onderstaande url-structuur:

|                                      |                                          |                                                            |
|--------------------------------------|------------------------------------------|------------------------------------------------------------|
| /[__gebiedsinformatieAanvragen__](#gebiedsinformatieaanvragen-synchroniseren)/{id} |  /[__beheerdersinformatieAanvragen__](#beheerdersinformatieaanvragen-synchroniseren)/{id} | /__aanleveringen__/[id]                                    |
|                                      |                                          | /[__beheerdersinformatieLevering__](#beheerdersinformatieleveringen-synchroniseren)/__zip__                     |
|                                      | /[__gebiedsinformatieLeveringen__](#gebiedsinformatieleveringen-synchroniseren)/[id]    | /__terugmeldingen__/[id]/__beheerdersTerugmeldingen__/[id] |

### Endpoints
In het [overzicht met endpoints KLIC API's](../API%20management/Overzicht%20endpoints%20KLIC%20APIs.md) wordt een overzicht gegeven van de basispaden voor de endpoints die door KLIC API's worden gebruikt.

De endpoints die in onderstaande voorbeelden worden gebruikt, zijn relatief ten opzichte van deze basispaden.  \
In de voorbeelden wordt uitgegaan van de API's op de productieomgeving KLIC.

### Authenticatie
De KLIC REST API's zijn beveiligd middels de OAuth 2.0 specificatie. Zie daarvoor
 [Authenticatie via OAuth](../API%20management/Authenticatie_via_oauth.md).

## Pagineren
Voor de endpoints die een lijst van objecten opleveren, pagineren we de output. Waar we een collectie geven, pagineren we door in de response een link naar volgende pagina te geven.  \
Zie ook de toepassing van [standaarden en richtlijnen](../API%20management/Standaardisering%20bij%20KLIC%20APIs.md) in KLIC API's.

Voor synchronisatie wordt pagineren op basis van een mutatiedatum gebruikt. In de URL dient de '+' van de timezone aanduiding in de timestamp weergeven te worden als '%2B'. 
``` json
{
   "_links":{
      "self":{
         "href":"https://service10.kadaster.nl/klic/bmkl/v2/gebiedsinformatieAanvragen?datumType=mutatieDatum&datumVanaf=2018-07-01T19:00:00%2B02&datumTot=2018-07-01T20:00:00%2B02"
      },
      "next":{
         "href":"https://service10.kadaster.nl/klic/bmkl/v2/gebiedsinformatieAanvragen?datumType=mutatieDatum&datumVanaf=2018-07-01T19:05:22.323%2B02&datumTot=2018-07-01T20:00:00%2B02"
      }
   },
   "collection":[
      {
         //gebiedsinformatieAanvraag1...
      },
      {
         //gebiedsinformatieAanvraag2...
      }
   ]
}
```

# Use casemodel datasynchronisatie met BMKL API
Voor het synchroniseren van de Klic-resources:

- GebiedsinformatieAanvraag
- BeheerdersinformatieAanvraag
- BeheerdersinformatieLevering
- GebiedsinformatieLevering

zijn use cases gemodelleerd in een use casemodel. Hieronder wordt daarvan een schematisch overzicht gegeven.

![usecasemodel](images/UC470-Synchroniseren-procesgegevens-door-toezichthouder.jpg "UC470 Synchroniseren procesgegevens door toezichthouder.jpg")
_Figuur 1 UCM Koppelvlak voor datasynchronisatie van procesgegevens_

De use cases voor dit koppelvlak zijn geimplementeerd als BMKL API's. Onderstaand overzicht geeft van de use cases de API-structuur.

![usecasemodel](images/UC470-Synchroniseren-procesgegevens-door-toezichthouder-WebAPI.jpg "UC470 Synchroniseren procesgegevens door toezichthouder (WebAPI).jpg")
_Figuur 2 UCM B2B-koppeling datasynchronisatie met BMKL API's (BMKL2.0, API-structuur)_

# Voorbeeldberichten per endpoint
## gebiedsinformatieAanvragen synchroniseren
Aanvragers van gebiedsinformatie (grondroerders) en AT kunnen een lijst van gebiedsinformatieAanvragen ophalen.
Aanvragers krijgen dan alleen eigen gebiedsinformatieAanvragen. AT krijgt alle gebiedsinformatieAanvragen.  
Benodigde scope: klic.gebiedsinformatieaanvraag of klic.gebiedsinformatieaanvraag.readonly of klic.toezicht  

GET /gebiedsinformatieAanvragen?datumType=mutatieDatum&datumVanaf=2018-07-01T19:00:00%2B02&datumTot=2018-07-01T20:00:00%2B02  
``` http
HTTP/1.1 200 OK
Content-Type: application/json
```

``` json
{
   "_links":{
      "self":{
         "href":"https://service10.kadaster.nl/klic/bmkl/v2/gebiedsinformatieAanvragen?datumType=mutatieDatum&datumVanaf=2018-07-01T19:00:00%2B02&datumTot=2018-07-01T20:00:00%2B02"
      },
      "next":{
         "href":"https://service10.kadaster.nl/klic/bmkl/v2/gebiedsinformatieAanvragen?datumType=mutatieDatum&datumVanaf=2018-07-01T19:15:22.323%2B02&datumTot=2018-07-01T20:00:00%2B02"
      }
   },
   "gebiedsinformatieAanvragen":[
      {
         "giAanvraagId":"2109992D-90F6-4BC7-815E-E72A02D46220",
         "ordernummer":"2015000471",
         "klicMeldnummer":"00G000227",
         "aanvrager":{
            "contact":{
                "naam":"Aanvrager01",
                "telefoon":"0881324567",
                "email":"klic.testers@kadaster.nl"
            },
            "organisatie":{
                "naam":"Netbeheerder Actualiseren01",
                "kvkNummer": "12345678",
                "bezoekAdres":{
                    "openbareRuimteNaam":"Laan van Westenenk",
                    "huisnummer":"701",
                    "postcode":"7334DP",
                    "woonplaatsNaam":"APELDOORN"
                },
            },
         },
         "opdrachtgever":{
            "contact":{
                "naam":"Kadaster",
                "telefoon":"(088) 183 20 00",
                "email":"noreply@kadaster.nl"
            },
            "organisatie":{
                "naam":"Kadaster",
                "kvkNummer": "98765432",
                "bezoekAdres":{
                    "openbareRuimteNaam":"Hofstraat",
                    "huisnummer":"110",
                    "postcode":"7311KZ",
                    "woonplaatsNaam":"APELDOORN"
                },
            },
        },
         "aanvraagSoort":"http://definities.geostandaarden.nl/imkl2015/id/waarde/AanvraagSoortValue/graafmelding",
         "aanvraagDatum":"2018-07-01T19:05:22+02",
         "mutatieDatum":"2018-07-01T19:05:22.323+02",
         "giAanvraagStatus":"https://api.kadaster.nl/klic/v2/waarde/giAanvraagStatussen/giOpen",
         "soortWerkzaamheden":[
            "http://definities.geostandaarden.nl/imkl2015/id/waarde/SoortWerkzaamhedenValue/funderingswerk",
            "http://definities.geostandaarden.nl/imkl2015/id/waarde/SoortWerkzaamhedenValue/woningbouw"
         ],
         "locatieWerkzaamheden":{
               "openbareRuimteNaam":"Laan van Westenenk",
               "huisnummer":"701",
               "postcode":"7334DP",
               "woonplaatsNaam":"Apeldoorn",
               "BAGidAdresseerbaarObject": "0200010000130331"
         },
         "startDatum":"2018-08-01",
         "eindDatum":"2018-08-08",
         "graafpolygoon":{
            "type":"Polygon",
            "crs":{
               "type":"name",
               "properties":{
                  "name":"EPSG:28992"
               }
            },
            "coordinates":[ [ [ 121070, 486903 ], [ 121095, 486849 ], [ 121220, 486871 ], [ 121480, 486905 ], [ 121507, 487100 ], [ 121564, 487215 ], [ 121539, 487226 ], [ 121460, 487288 ], [ 121337, 487331 ], [ 121220, 487338 ], [ 121156, 487308 ], [ 121070, 486903 ] ] ]
         },
         "informatiepolygoon":{
            "type":"Polygon",
            "crs":{
               "type":"name",
               "properties":{
                  "name":"EPSG:28992"
               }
            },
            "coordinates":[ [ [ 121070, 486903 ], [ 121095, 486849 ], [ 121220, 486871 ], [ 121480, 486905 ], [ 121507, 487100 ], [ 121564, 487215 ], [ 121539, 487226 ], [ 121460, 487288 ], [ 121337, 487331 ], [ 121220, 487338 ], [ 121156, 487308 ], [ 121070, 486903 ] ] ]
         },
         "huisaansluitingAdressen":[{
               "openbareRuimteNaam":"Laan van Westenenk",
               "postcode":"7334DP",
               "huisnummer":"701",
               "woonplaatsNaam":"Apeldoorn",
               "BAGidAdresseerbaarObject": "0200010000130331"
            }, {
               "openbareRuimteNaam":"Evert van 't Landstraat",
               "postcode":"7334DR",
               "huisnummer":"15",
               "woonplaatsNaam":"Apeldoorn",
               "BAGidAdresseerbaarObject": "0200010003923183"
            }]
      }
   ]
}
```

## beheerdersinformatieAanvragen synchroniseren
Netbeheerders, serviceproviders of AT kunnen een lijst van beheerdersinformatieAanvragen ophalen.
Netbeheerders en service providers krijgen dan alleen eigen beheerdersinformatieAanvragen. AT krijgt alle beheerdersinformatieAanvragen.  

GET /gebiedsinformatieAanvragen/-/beheerdersinformatieAanvragen?datumType=mutatieDatum&datumVanaf=2018-07-01T19:00:00%2B02&datumTot=2018-07-01T20:00:00%2B02  
Scope: klic.gebiedsinformatieaanvraag.readonly of klic.toezicht    

``` http
HTTP/1.1 200 OK
Content-Type: application/json
```

``` json
{
    "_links": {
        "self": {
            "href": "https://service10.kadaster.nl/klic/bmkl/v2/gebiedsinformatieAanvragen/-/beheerdersinformatieAanvragen?datumType=mutatieDatum&datumVanaf=2018-07-01T19:00:00%2B02&datumTot=2018-07-01T20:00:00%2B02"
        },
        "next": {
            "href": "https://service10.kadaster.nl/klic/bmkl/v2/gebiedsinformatieAanvragen/-/beheerdersinformatieAanvragen?datumType=mutatieDatum&datumVanaf=2018-07-01T19:55:22.323%2B02&datumTot=2018-07-01T20:00:00%2B02"
        }
    },
    "beheerdersinformatieAanvragen": [{
        "biAanvraagId": "8A1C2D58-1823-4E50-B826-7E820AD080A6",
        "giAanvraagId": "7A908F06-484E-42D7-AD86-D1E558A1F5B9",
        "bronhoudercode": "nbact2",
        "biNotificatieStatus": "https://api.kadaster.nl/klic/v2/waarde/biNotificatieStatussen/biBevestigingOntvangen",
        "biProductieStatus": "https://api.kadaster.nl/klic/v2/waarde/biProductieStatussen/biGereedVoorSamenstellenProduct",
        "datumGenotificeerd": "2018-06-30T19:00:00+02",
        "datumBevestigingOntvangen": "2018-06-30T19:02:01+02",
        "mutatieDatum":"2018-07-01T19:04:29.248+02"
    }, {
        "biAanvraagId": "8G33E1D5-1833-4E82-B826-7E820AD012F6",
        "giAanvraagId": "7A908F06-484E-42D7-AD86-D1E558A1F5B9",
        "bronhoudercode": "nbact1",
        "biNotificatieStatus": "https://api.kadaster.nl/klic/v2/waarde/biNotificatieStatussen/biBevestigingOntvangen",
        "biProductieStatus": "https://api.kadaster.nl/klic/v2/waarde/biProductieStatussen/biWachtOpAntwoord",
        "datumGenotificeerd": "2018-06-30T19:00:00+02",
        "datumBevestigingOntvangen": "2018-07-01T19:16:00+02",
        "mutatieDatum":"2018-07-01T19:16:00.369+02"
    }, {
        "biAanvraagId": "3D3988D5-1813-4E82-B386-7E820AD012S2",
        "giAanvraagId": "2639FD07-6A9F-4455-84D0-87EBFB13466D",
        "bronhoudercode": "nbact1",
        "biNotificatieStatus": "https://api.kadaster.nl/klic/v2/waarde/biNotificatieStatussen/biBevestigingOntvangen",
        "biProductieStatus": "https://api.kadaster.nl/klic/v2/waarde/biProductieStatussen/biWachtOpAntwoord",
        "datumGenotificeerd": "2018-07-01T19:15:00+02",
        "datumBevestigingOntvangen": "2018-07-01T19:22:00+02",
        "mutatieDatum":"2018-07-01T19:22:00.246+02"
    }, {
        "biAanvraagId": "42G178D6-1815-4X27-B386-7E820AD012F2",
        "giAanvraagId": "2109992D-90F6-4BC7-815E-E72A02D46220",
        "bronhoudercode": "nbact1",
        "biNotificatieStatus": "https://api.kadaster.nl/klic/v2/waarde/biNotificatieStatussen/biOpen",
        "biProductieStatus": "https://api.kadaster.nl/klic/v2/waarde/biProductieStatussen/biWachtOpAntwoord",
        "datumGenotificeerd": "2018-07-01T19:55:00+02",
        "mutatieDatum":"2018-07-01T19:55:00.248+02"
    }]
}
```

## beheerdersinformatieLeveringen synchroniseren
Netbeheerders, serviceproviders of AT kunnen een lijst van beheerdersinformatieLeveringen ophalen.
Netbeheerders en service providers krijgen dan alleen eigen leveringen. AT krijgt alle leveringen.  

GET /gebiedsinformatieAanvragen/-/beheerdersinformatieAanvragen/-/beheerdersinformatieLevering?datumType=mutatieDatum&datumVanaf=2018-07-01T19:00:00%2B02&datumTot=2018-07-01T20:00:00%2B02  
Scope: klic.gebiedsinformatieaanvraag.readonly of klic.toezicht    

``` http
HTTP/1.1 200 OK
Content-Type: application/json
```

``` json
{
    "_links": {
        "self": {
            "href": "https://service10.kadaster.nl/klic/bmkl/v2/gebiedsinformatieAanvragen/-/beheerdersinformatieAanvragen/-/beheerdersinformatieLevering?datumType=mutatieDatum&datumVanaf=2018-07-01T19:00:00%2B02&datumTot=2018-07-01T20:00:00%2B02"
        },
        "next": {
            "href": "https://service10.kadaster.nl/klic/bmkl/v2/gebiedsinformatieAanvragen/-/beheerdersinformatieAanvragen/-/beheerdersinformatieLevering?datumType=mutatieDatum&datumVanaf=2018-07-01T19:05:22.323%2B02&datumTot=2018-07-01T20:00:00%2B02"
        }
    },
    "beheerdersinformatieLeveringen": [{
        "biAanvraagId": "8A1C2D58-1823-4E50-B826-7E820AD080A6",
        "datumBeheerdersinformatieOntvangen": "2018-07-01T19:03:49+02",
        "mutatieDatum": "2018-07-01T19:03:49.248+02",
        "betrokkenBijAanvraag": "true",
        "eisVoorzorgsMaatregel": "true",
        "geleverdeThemas" : [
            {
                "thema": "http://definities.geostandaarden.nl/imkl2015/id/waarde/Thema/laagspanning",
                "eisVoorzorgsMaatregel": "false"
            },
            {
                "thema": "http://definities.geostandaarden.nl/imkl2015/id/waarde/Thema/hoogspanning",
                "eisVoorzorgsMaatregel": "true"
            },
        ]
    }, {
        "biAanvraagId": "B002B509-FE7C-45B3-83C0-CDBA2584697F",
        "datumBeheerdersinformatieOntvangen": "2018-07-01T19:04:12+02",
        "mutatieDatum": "2018-07-01T19:04:12.369+02",
        "betrokkenBijAanvraag": "false"
    }]
}
```

## gebiedsinformatieLeveringen synchroniseren
GET /gebiedsinformatieAanvragen/-/gebiedsinformatieLeveringen?datumType=mutatieDatum&datumVanaf=2017-11-02T09:00:00%2B01&datumTot=2017-11-02T10:00:00%2B01  
Scope: klic.gebiedsinformatieaanvraag.readonly of klic.toezicht    

``` http
HTTP/1.1 200 OK
Content-Type: application/json
```

``` json
{
    "_links": {
        "self": {
            "href": "https://service10.kadaster.nl/klic/bmkl/v2/gebiedsinformatieAanvragen/-/gebiedsinformatieLeveringen?datumType=mutatieDatum&datumVanaf=2017-11-02T09:00:00%2B01&datumTot=2017-11-02T10:00:00%2B01"
        },
        "next": {
            "href": "https://service10.kadaster.nl/klic/bmkl/v2/gebiedsinformatieAanvragen/-/gebiedsinformatieLeveringen?datumType=mutatieDatum&datumVanaf=2017-11-02T09:35:22.323%2B01&datumTot=2017-11-02T10:00:00%2B01"
        }
    },
    "gebiedsinformatieLeveringen": [{
        "giAanvraagId": "F7CB3ACE-6E73-42F9-9865-EFA57D00AEC1",
        "giLeveringId": "BE0B80CA-1301-4216-80A9-DE691356E273",
        "leveringsvolgnummer": "2",
        "datumLeveringSamengesteld": "2017-11-02T09:16:01+01",
        "mutatieDatum":"2017-11-02T09:16:01.808+01",
        "indicatieLeveringCompleet": "true",
        "giLeveringUrl": "https://service10.kadaster.nl/gds2/download/public/ce417a87-92cd-4c57-a70e-8a0c405b2701"
    }, {
        "giAanvraagId": "2109992D-90F6-4BC7-815E-E72A02D46220",
        "giLeveringId": "10789865-82A0-4FA5-8E07-500EA0E2487B",
        "leveringsvolgnummer": "1",
        "datumLeveringSamengesteld": "2017-11-02T09:25:01+01",
        "mutatieDatum":"2017-11-02T09:25:01.808+01",
        "indicatieLeveringCompleet": "false",
        "giLeveringUrl": "https://service10.kadaster.nl/gds2/download/public/cfbdaaf6-c84b-4a05-9d71-657cd43ecf21"
    }]
}
```


