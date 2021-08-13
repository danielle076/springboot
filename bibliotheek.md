## Het idee

Er is een systeem nodig voor een bibliotheek waarin de leden worden bijgehouden. Ook de beschikbare boeken staan in het systeem. Een lid kan een boek lenen voor 3 weken. Het systeem houdt bij welke boeken aanwezig zijn in de bibliotheek.

## Eerste opzet klassendiagram

1. Beschrijf de applicatie. Probeer min of meer volledig te zijn zonder al te zeer op de details in te gaan
2. Markeer alle zelfstandige naamwoorden met een kleur
3. Markeer alle werkwoorden met een andere kleur
4. De zelfstandige naamwoorden zijn kandidaten om in de applicatie classes te worden
5. De werkwoorden zijn kandidaat methoden

### Voorbeeld tekst
![img32.png](images/img32.png)

De rode woorden zijn zelfstandige naamwoorden.<br/>
De groene woorden zijn de werkwoorden.

![img31.png](images/img31.png)

### Idee

De zelfstandige namen voor het idee zijn als volgt.

![img.png](images/img33.png)

- Systeem 
- Bibliotheek
- Lid
- Boek
- Leenperiode (=weken)

Systeem is het alles overkoepelende, dat is juist wat je gaat beschrijven, dus deze gebruiken we niet in de klassendiagram UML.

## UML: klassendiagram

Wanneer klassen iets met elkaar te maken hebben dan zet je een verbinding ertussen.

- Een bibliotheek heeft een X aantal exemplaren (aggregatie - open diamant)
- Een bibliotheek heeft een X aantal leden (aggregatie - open diamant)
- Examplaar heeft een relatie met een boek (associatie - lijn)
    - een boek kan meerdere exemplaren hebben
    - ieder exemplaar gaat over 1 boek
- Lid kan een exemplaar lenen (associatie - lijn)
- Leenperiode: het exemplaar is geleend voor een periode door een lid. Leenperiode is een soort koppeling tussen exemplaar en lid (associatie - lijn)

![img34.png](images/img34.png)

## Database: postgresql

We gaan een nieuwe database maken in pgAdmin genaamd bieb. 

Ga naar schemas > tables en rechts klik om een nieuwe tabel `books` te maken.

![img.png](images/img35.png)

In de tab `columns` gaan we de kolommen zetten en slaan we hem op.

![img_1.png](images/img36.png)

We hebben nu een tabel waar we records in kunnen zetten.

![img_2.png](images/img37.png)

## Hibernate

Hoe verbind je van je Java Applicatie naar je Database? Springboot biedt een koppeling tussen de database en Java.

In Java heb je het over klasses, objecten etc. In een database hebben we het over tabellen. Een verbinding tussen een klasse en een tabel is niet altijd even 1 op 1. In een klasse kun je bijvoorbeeld werken met lijsten, met een reeks, in een database kan dit niet. In een database werk je altijd met een primary key en foreign keys en dit hoef je niet altijd in je klasses te doen.

Aan de Java kant heb je ORM: Object Relational Mapping. Een ORM heb je nodig om de verbinding te maken.

![img_3.png](images/img38.png)

In Springboot heet de ORM Hibernate. Hibernate is de populairste Object Relational Mapping (ORM) voor Java. 

## Springboot

We gaan de klasses van het klassendiagram in Springboot maken.

We beginnen met een Spring Initializr https://start.spring.io/ project.

![img_4.png](images/img39.png)

![img_5.png](images/img40.png)

Open het bestand in IntelliJ.

![img_6.png](images/img41.png)

De `pom.xml` specificeert welke pakketten je nodig hebt.