# JPA Annotations

- Static and transient fields are not persisted.
```xml
@Entity
@Table(name="TBL_EMPLOYEE")
public class Employee {
  @Id
  @Column(name="EMPLOYEE_ID", nullable = false, unique = true)
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private int id;
}
```

#### @Entity

- Annotate the class to be persisted.

#### @Table

- Default table name will be same as class name.
- If we want a different table name, we can use @Table(name="TABLE_NAME")

#### @Id, @IdClass, @EmbeddedId

- It could be used to annotate accessor method too.
- ID might be a reserved word in some dbs,

#### @Column(name="TABLE_COLUMN_NAME")

- When the column name is different than the field name.

#### @GeneratedValue(strategy = GeneratorType.AUTO)

- seragated and normal key

- AUTO : default. portable. HB chooses the appropriate ID based on the DB.

- IDENTITY: responsibility of the database to provide a unique identifier.
  - MySQL: AUTO_INCREMENT, PostgreSQL: SERIAL, ORACLE: NoSupport

- SEQUENCE: some db provides mechanism of sequenced numbers.
  - ORACLE: alternative to IDENTITY

- TABLE: sometimes primary keys have been created from a unique column in
  another table.

#### @Transient

- use it to tell HB to ignore that field

#### @Temporal (TemporalType.DATE)

- DATE:
- TIME:
- TIMESTAMP:

#### @Lob

- Large object
- When varchar(255) is not enough

#### @Embeddable
- It is a value object/type. There wont be a table for this class.
#### @Embedded

#### @ElementCollection, @JoinTable, @JoinColumn

- one-to-many relations. 
- HB creates the other tables and foreign key etc.

```java
@ElementCollection // default table name = userdetails_addresses
@JoinTable (name = "USER_ADDRESS", 
            joinColumns = @JoinColumn(name="USER_ID")) // default: userdetails_userid
private Set<Address> addresses = new HashSet<>();
```
#### Composite Identifiers

- There are 3 ways:

1. @Embeddable

```java
// It should implement Serializable and have no-arg constructor
// It must have hashCode and equals methods, HB distinguish uniqueness
@Embeddable
public class CoursePK implements Serializable {
  private String tutor;
  private String title;
  public CoursePK() {}
}
```

2. @EmbeddedId

3. @IdClass
- It is not a standard practice and is best avoided.

```java
public class CoursePK3 implements Serializable {
  private String tutor = null;
  private String title = null;
  public CoursePK3() {}
  //...
}
```

```java
@IdClass(value = CoursePK3.class)
@Entity
@Table(name = "COURSE_ANNOTATION_V3")
public class Course3 {
  // We must duplicate the identifiers
  // defined in our primary class here too
  @Id
  private String title = null;
  @Id
  private String tutor = null;
  //...
}
```

## @Version

- Enable versioning for 'first commit wins'
- To handle isolation level problem: last commit wins

- Hibernate changes the version value automatically
- it is a simple counter without any useful semantic value beyond concurrency control

- wraps and starts from 0 if value reach the max number of Long

- version column is used in WHERE clause during updating, throws
  OptimisticLockException if cannot find that version(means it changed somewhere else)

- Use db trigger and procedures in a shared-db if some arenot using hibernate
- check to find version value, if not there, means not hibernate and update it

- Hibernate can use lastupdated timestamp to do the same thing

```java
@Version
protected Long version; // just a getter, not setter
```
