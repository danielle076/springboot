## Security met gebruikerstabel

We willen geen vaste tabel zoals `my_users` en `my_authorities`.

We maken een nieuw IntelliJ project in de Initializr.

Maak een nieuwe package aan genaamd `controller` en hierin de volgende bestanden: `AdminController.java`
, `AuthenticatedController.java`, `BaseController.java` en `CustomersController.java`.

_AdminController.java_

```java
package nl.danielle.demo_third_security.controller;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping(value = "/admin")
public class AdminController {

    @GetMapping(value = "")
    public ResponseEntity<Object> getMessage() {
        return new ResponseEntity<>("SECURED REST endpoint: /admin", HttpStatus.OK);
    }
}
```

_AuthenticatedController.java_

```java
package nl.danielle.demo_third_security.controller;

import org.springframework.http.ResponseEntity;
import org.springframework.security.core.Authentication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.security.Principal;

@RestController
@RequestMapping(value = "/authenticated")
public class AuthenticatedController {

    @GetMapping(value = "")
    public ResponseEntity<Object> authenticated(Authentication authentication, Principal principal) {
        return ResponseEntity.ok().body(principal);
    }
}
```

_BaseController.java_

```java
package nl.danielle.demo_third_security.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class BaseController {

    @GetMapping(value = "/")
    public String hello() {
        return "Hello World";
    }
}
```

_CustomersController.java_

```java
package nl.danielle.demo_third_security.controller;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping(value = "/customers")
public class CustomersController {

    @GetMapping(value = "")
    public ResponseEntity<Object> getMessage() {
        return new ResponseEntity<>("SECURED REST endpoint: /customers", HttpStatus.OK);
    }
}
```

Maak een nieuwe package aan genaamd `config` en hierin het volgende bestand: `SpringSecurityConfig.java`.

```java
package nl.danielle.demo_third_security.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;

import javax.sql.DataSource;

@Configuration
@EnableWebSecurity
public class SpringSecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private DataSource dataSource;

    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {

        auth.jdbcAuthentication().dataSource(dataSource);
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    // Secure the endpoints with HTTP Basic authentication
    @Override
    protected void configure(HttpSecurity http) throws Exception {

        http
                //HTTP Basic authentication
                .httpBasic()
                .and()
                .authorizeRequests()
                .antMatchers(HttpMethod.GET, "/customers/**").hasRole("USER")
                .antMatchers(HttpMethod.GET, "/admin/**").hasRole("ADMIN")
                .antMatchers(HttpMethod.GET, "/authenticated/**").authenticated()
                .anyRequest().permitAll()
                .and()
                .csrf().disable()
                .formLogin().disable();
    }
}
```

We hebben de query's (my_users en my_authorities) eruit gelaten en verwijzen nu naar de datasource met de
jdbc `auth.jdbcAuthentication().dataSource(dataSource);`

Omdat we nu een tabel maken die `'User` en `Authority` heten, gaat `jdbc` de query's volledig automatisch doen.

Maak een nieuwe package aan genaamd `model` en hierin de volgende bestanden: `Authority.java` en `User.java`.

_Authority.java_

```java
package nl.danielle.demo_third_security.model;

import javax.persistence.*;
import java.io.Serializable;

@Entity
@Table(name = "authorities")
public class Authority implements Serializable {

    @Id
    @Column(nullable = false)
    private String username;

    @Id
    @Column(nullable = false)
    private String authority;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getAuthority() {
        return authority;
    }

    public void setAuthority(String authority) {
        this.authority = authority;
    }
}
```

_User.java_

```java
package nl.danielle.demo_third_security.model;

import javax.persistence.*;

@Entity
@Table(name = "users")
public class User {

    @Id
    @Column(nullable = false, unique = true)
    private String username;

    @Column(nullable = false, length = 255)
    private String password;

    @Column(nullable = false)
    private boolean enabled;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public boolean isEnabled() {
        return enabled;
    }

    public void setEnabled(boolean enabled) {
        this.enabled = enabled;
    }
}

```

In de `resources` map heb je de volgende bestanden met bijbehorende code: `application.properties` en `data.sql`. Schema
hebben we niet meer nodig.

_application.properties_

    # datasource Postgres
    spring.jpa.database=postgresql
    spring.datasource.platform=postgres
    spring.datasource.url=jdbc:postgresql://localhost:5432/demo_third_security
    spring.datasource.username=postgres
    spring.datasource.password=postgres123
    spring.datasource.driver-class-name=org.postgresql.Driver
    spring.datasource.initialization-mode=always
    spring.jpa.generate-ddl=true
    spring.jpa.hibernate.ddl-auto=create-drop
    spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
    
    spring.jpa.properties.hibernate.dialect= org.hibernate.dialect.PostgreSQLDialect

_data.sql_

```sql
INSERT INTO users (username, password, enabled)
VALUES ('user', '$2a$10$wPHxwfsfTnOJAdgYcerBt.utdAvC24B/DWfuXfzKBSDHO0etB1ica', TRUE);
INSERT INTO users (username, password, enabled)
VALUES ('admin', '$2a$10$wPHxwfsfTnOJAdgYcerBt.utdAvC24B/DWfuXfzKBSDHO0etB1ica', TRUE);
INSERT INTO users (username, password, enabled)
VALUES ('peter', '$2a$10$wPHxwfsfTnOJAdgYcerBt.utdAvC24B/DWfuXfzKBSDHO0etB1ica', TRUE);

INSERT INTO authorities (username, authority)
VALUES ('user', 'ROLE_USER');
INSERT INTO authorities (username, authority)
VALUES ('admin', 'ROLE_USER');
INSERT INTO authorities (username, authority)
VALUES ('admin', 'ROLE_ADMIN');
INSERT INTO authorities (username, authority)
VALUES ('peter', 'ROLE_USER');
INSERT INTO authorities (username, authority)
VALUES ('peter', 'ROLE_ADMIN');
```

Het wachtwoord wat je ziet staan, is een encrypted wachtwoord. We hebben dit in code in de data.sql staan maar het is
een encryptie. Spring Boot kent allerlei encrypties.

In `SpringSecurityConfig.java` zie je de volgende code staan.

    @Bean
    public PasswordEncoder passwordEncoder() {
      return new BCryptPasswordEncoder();
    }

Dit is de encoder die gebruikt word om de wachtwoorden te encoden. De `BCryptPasswordEncoder` vereist een bepaalde
hoeveelheid rekenwerk.

Hoe weet je dat de code `$2a$10$wPHxwfsfTnOJAdgYcerBt.utdAvC24B/DWfuXfzKBSDHO0etB1ica` bij het wachtwoord `password`
hoort? Daar zijn websites voor: https://www.browserling.com/tools/bcrypt. Wanneer je het wachtwoord `password` intikt
krijg je `$2a$10$in7p/8vt5wmfqmo8xD5QHumpRPSuB2tEQ4oeWX5bL.cGi5GOqr7XW`.

Run de applicatie.

In postgreSQL zie je de tabellen `authorities` en `users` staan.

![img104.png](images/img104.png)

![img105.png](images/img105.png)

In Postman ga je naar url `http://localhost:8080/authenticated` en logt in als `peter`, `admin` of `user`.

![img106.png](images/img106.png)

De url's `http://localhost:8080/`, `http://localhost:8080/customers` en `http://localhost:8080/admin` kun je testen met
de rollen `peter`, `admin` of `user` met wachtwoord `password`. Dit zijn alle endpoints die je in de `controller`heb
gedefinieerd.
