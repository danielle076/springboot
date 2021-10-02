## Relaties

Je hebt verschillende annotaties voor relaties tussen klassen.

- `@OneToOne`
- `@OneToMany`
- `@ManyToOne`
- `@ManyToMany`

###  @OneToOne

Een-op-een relatie is een toestand waarbij gegevens in een tabel alleen een relatie hebben met een gegeven in een andere tabel.

![img137.png](images/img137.png)

Een werknemer heeft maar één rekening en vice versa.

_Employee.java_

```java
@Entity
@Table(name = "employee")
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String name;
    private String email;

// constructors, getters, setters
}
```

_Account.java_

```java
@Entity
@Table(name = "account")
public class Account {

    @Id
    private int id;

    private String password;

    @OneToOne
    @JoinColumn(name = "id", referencedColumnName = "id")
    private Employee employee;
// constructors, getters, setters
}
```

### @OneToMany en @ManyToOne

Een veel-op-één-relatie is er wanneer één entiteit (typisch een kolom of een reeks kolommen) waarden bevat die verwijzen naar een andere entiteit (een kolom of reeks kolommen) die unieke waarden heeft.

Een één-op-veel relatie komt voor in database ontwerp wanneer een entiteit een meervoudige relatie heeft met een andere entiteit.

![img138.png](images/img138.png)

Eén positie kan veel werknemers hebben, maar elke werknemer kan maar voor één positie werken.

_Employee.java_

```java
@Entity
@Table(name = "employee")
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String name;
    private String email;
    @ManyToOne(optional = false)
    @JoinColumn(name = "position_id", nullable = false,
        referencedColumnName = "p_id")
    private Position position;
// constructors, getters, setters
}
```

_Position.java_

```java
@Entity
@Table(name = "position")
public class Position {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int p_id;
    private String p_name;
    @OneToMany(mappedBy = "position", cascade = CascadeType.ALL)
    private List<Employee> employee;
// constructors, getters, setters
}
```

### @ManyToMany

Een veel-op-veel relatie is een relatie die in beide richtingen een meervoudige waarde heeft.

![img139.png](images/img139.png)

Een account kan meer dan één rol hebben, en een rol kan meer dan één account hebben.

_Account.java_

```java
@Entity
@Table(name = "account")
public class Account {
    @Id
    private int id;

    private String password;
    @ManyToMany(cascade = CascadeType.ALL, fetch = FetchType.EAGER)
    @JoinTable(name = "account_role",
        joinColumns = @JoinColumn(name = "id", referencedColumnName        = "id"),
        inverseJoinColumns = @JoinColumn(name = "role_id", referencedColumnName = "role_id"))
    private List<Role> role;
// constructors, getters, setters
}
```

_Role.java_

```java
@Entity
@Table(name = "role")
public class Role {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int role_id;

    private String role_name;
    @ManyToMany(mappedBy = "role")
    private List<Account> account;
// constructors, getters, setters
}
```