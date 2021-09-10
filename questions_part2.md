## Project Person (vervolg questions)

### Spring initializr

Spring initializr genereert een basis project in Java met Spring Boot.

Ga naar de website <a href="https://start.spring.io" target="_blank">spring.io</a>. De volgende gegevens vul je in voor
het eerste Spring Boot project.

- Project: vink aan `Maven Project`
- Language: vink aan `Java`
- Spring Boot: vink aan `2.5.4` (laatste versie)
- Project Metadata: vul informatie in over jouw project
    - Group: `nl.danielle` (identifier van de ontwikkelaar)
    - Artifact: `demo_springboot` (hoe heet je project)
    - Name: `demo_springboot` (hoe heet je project)
    - Description: `Demo project for Spring Boot`
    - Package name: maakt het systeem zelf aan
    - Packaging: vink aan `Jar`
    - Java: vink aan `11`

Klik op "add dependencies" en voeg `Spring Web` toe.

Met "generate" wordt er een bestand in jouw download map gezet. Unzip het bestand en open het in Intellij.

Klik vervolgens rechtsonder in IntelliJ op `Load`.

![img13.png](images/img13.png)

Ga naar File > Project Structure en zet `Project SDK` op 11. Klik op apply.

### Spring Boot errors

![img.png](images/img74.png)

Klik op "Trust project..." in de gele balk.

Wanneer de error niet weg is klik je op File > Invalidate Caches > Invalidate and Restart.

### Model

De controller werkt in dit geval nog steeds op een lijst van personen, maar eigenlijk is het een lijst van Strings.

    private static List<String> personen = new ArrayList<>();

Als je persoon als klasse definieert, dan heeft zo'n persoon meerdere attributen zoals een naam, voornaam, woonplaats.

We maken een nieuwe package genaamd `model`. Model gaat java classes hebben die overeenkomen met de entity,
bijvoorbeeld `Persoon.java`.

![img117.png](images/img117.png)

```java
package nl.danielle.demo_springboot.model;

public class Persoon {

    public long id; // primary key

    public String voornaam;
    public String achternaam;
    public String woonplaats;

    public long getId() {
        return id;
    }

    public void setId(long id) {
        this.id = id;
    }

    public String getVoornaam() {
        return voornaam;
    }

    public void setVoornaam(String voornaam) {
        this.voornaam = voornaam;
    }

    public String getAchternaam() {
        return achternaam;
    }

    public void setAchternaam(String achternaam) {
        this.achternaam = achternaam;
    }

    public String getWoonplaats() {
        return woonplaats;
    }

    public void setWoonplaats(String woonplaats) {
        this.woonplaats = woonplaats;
    }
}

```

In `PersoonController.java` pas je de code `List<String>` aan in `List<Persoon>`. Ook pas je in deze file alles
van `String` aan naar `Persoon`.

```java
package nl.danielle.demo_springboot.controller;

import nl.danielle.demo_springboot.exception.RecordNotFoundException;
import nl.danielle.demo_springboot.model.Persoon;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.List;

@RestController
public class PersonController {
    private static List<Persoon> personen = new ArrayList<>();

    @GetMapping(value = "/personen")
    public ResponseEntity getPersonen() {
        return ResponseEntity.ok(personen);
    }

    @GetMapping(value = "/personen/{nr}")
    public ResponseEntity getPerson(@PathVariable int nr) {
        try {
            return ResponseEntity.ok(personen.get(nr));
        } catch (Exception ex) {
            throw new RecordNotFoundException();
        }
    }

    @PostMapping(value = "/personen")
    public ResponseEntity addPerson(@RequestBody Persoon persoon) {
        personen.add(persoon);
        return ResponseEntity.ok("Toegevoegd");
    }

    @DeleteMapping(value = "/personen/{nr}")
    public ResponseEntity deletePerson(@PathVariable int nr) {
        try {
            personen.remove(nr);
            return ResponseEntity.ok("Verwijderd");
        } catch (Exception ex) {
            throw new RecordNotFoundException();
        }
    }

    @PutMapping(value = "/personen/{nr}")
    public ResponseEntity updatePerson(@PathVariable int nr, @RequestBody Persoon persoon) {
        try {
            personen.set(nr, persoon);
            return ResponseEntity.ok("Updated");
        } catch (Exception ex) {
            throw new RecordNotFoundException();
        }
    }
}
```

Run de applicatie.

Ga naar Postman met url `http://localhost:8080/personen/` en `POST`. Om een nieuw persoon toe te voegen moet je
de `body` op `raw` en `JSON` zetten.

![img118.png](images/img118.png)

In `GET` met url `http://localhost:8080/personen/` krijg je het volgende terug.

![img119.png](images/img119.png)

Je ziet dat de `id` leeg is, die werkt pas wanneer we de gegevens in de database gaan zetten.

### Database

We gaan pom.xml uitbreiden met Spring initializr (https://start.spring.io/). Voeg de dependencies `Spring Data JPA`
en `PostgreSQL Driver` toe. Klik onderaan op `explore` en kopieer de code.

    <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
      <groupId>org.postgresql</groupId>
      <artifactId>postgresql</artifactId>
      <scope>runtime</scope>
    </dependency>

Zet deze in pom.xml.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.4</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>nl.danielle</groupId>
    <artifactId>demo_springboot</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo_springboot</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>11</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>${project.parent.version}</version>
            </plugin>
        </plugins>
    </build>

</project>
```

We gaan naar pgAdmin (postgreSQL) en maken een nieuwe database genaamd `person`.

Vervolgens gaan we in het project naar het bestand resources > `application.properties`. De volgende code zet je hierin.

    # datasource PostgreSQL
    spring.datasource.platform=postgres
    spring.datasource.url=jdbc:postgresql://localhost:5432/person
    spring.datasource.username=postgres
    spring.datasource.password=postgres123
    spring.datasource.driver-class-name=org.postgresql.Driver
    # jpa
    spring.jpa.database=postgresql
    spring.jpa.generate-ddl=true
    spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
    spring.jpa.properties.hibernate.dialect= org.hibernate.dialect.PostgreSQLDialect

- `url` is de database naam in pgAdmin
- `username` is jouw username in pgAdmin
- `password` is jouw wachtwoord in pgAdmin

