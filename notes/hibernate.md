# Intro

- ORM (Object-relational mapping)
- maps the object model's data to the schema of the database itself with the help of annotations
- database-independent and the same code can work for many databases with minor changes
- manages the exceptions itself by marking them as unchecked
- Hibernate uses HQL (Hibernate Query Language) which is similar to SQL but understands object-oriented concepts like inheritance, association


## JDBC (Java Database Connectivity)

- API that helps applications to communicate with databases
- It allows Java programs to connect to a database, run queries, retrieve and manipulate data


## JPA (Java Persistence API)

- specification that defines how ORM tools should function
- Hibernate is the implementation of JPA


## Components

1\. Configuration

- class which is present in org.hibernate.cfg package.
- It activates the Hibernate framework.
- It reads both configuration file and mapping files

```
Configuration cfg = new Configuration();
cfg.configure(); // Reads and validates hibernate.cfg.xml
```

2\. SessionFactory

- creates Session objects and maintains a second-level cache.
- It is heavyweight and thread-safe, so usually one per application

```
SessionFactory factory = cfg.buildSessionFactory();
```

3\. Session

- The Session interface interacts with the database and provides CRUD operations.
- It maintains a first-level cache (session-level).

```
Session session = factory.openSession();
```

4\. Transaction

- handles atomic database operations, ensuring data integrity through commit or rollback

```
Transaction tx = session.beginTransaction();
tx.commit(); // or tx.rollback();
```

5\. Query

- The Query interface executes HQL, Criteria API or native SQL queries to fetch or manipulate data.

```
Query<Student> query = session.createQuery("from Student");
List<Student> students = query.list();
```

6\. Persistent Classes (Entities)

- Java classes annotated with @Entity represent database tables.
- Each object maps to a row.

```
@Entity
@Table(name="student")
class Student {
    @Id
    private Long id;
    private String name;
}
```

7\. JDBC Layer

- Handles low-level database communication.
- Hibernate internally uses JDBC to execute SQL and retrieve results.


## Hibernate Lifecycle

1\. Transient State

- When an entity object is created using the new operator, it starts in the transient state.
- It is not associated with any Hibernate session and not stored in the database.
- Any modification to this object does not affect the database since Hibernate does not track it yet.

2\. Persistent State

- When a transient object becomes associated with a Hibernate session, it moves into the persistent state.
- Hibernate starts tracking changes to the object. Any modification to fields automatically updates the corresponding database record during transaction commit.
- Occurs when you call any of these methods:
  - session.save(e)
  - session.persist(e)
  - session.saveOrUpdate(e)
  - session.update(e)
  - session.merge(e)
  - session.lock(e)

3\. Detached State

- When a persistent object’s session is closed or cleared, it enters the detached state.
- The object still exists in memory but is no longer managed by Hibernate.
- Any modification made to it will not be reflected in the database unless reattached.
- Occurs when you call any of these methods:
  - session.close()
  - session.clear()
  - session.detach(e)
  - session.evict(e)
- To reattach a detached object:
  - session.update(e)
  - session.merge(e)
  - session.refresh(e)
  - session.load(e)

4\. Removed State

- The removed state represents a deleted entity.
- When a persistent object is deleted using session.delete(), it is removed from both the session and the database.
- Any further changes to the object will not affect the database.


## Caching

1\. First-Level Cache

- session-level cache
- It stores entities that are currently being used by a specific Hibernate session.
- When an entity is loaded or updated for the first time in a session, it is stored in this cache.
- Any subsequent request to fetch the same entity within the same session is served from the cache, avoiding a database query
- Enabled by default

2\. Second-Level Cache

- second-level cache, which is a shared cache across multiple sessions
- It stores data frequently used across different sessions, reducing database queries


## Interceptors

hook into Hibernate’s lifecycle events and perform custom logic before or after Hibernate interacts with the database

Types

1. onSave()
2. onFlushDirty()
3. onLoad()
4. onDelete()
5. preFlush()
6. postFlush()
7. findDirty()


## Example

```
// File: src/main/java/com/example/demo/DemoApplication.java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}


// File: src/main/resources/application.properties
# Spring datasource (example H2 for quick run; change to PostgreSQL/MySQL for production)
spring.datasource.url=jdbc:h2:mem:demo;DB_CLOSE_DELAY=-1;MODE=PostgreSQL
spring.datasource.username=sa
spring.datasource.password=
spring.datasource.driverClassName=org.h2.Driver

# JPA / Hibernate
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.format_sql=true

# Use UTC times in DB (optional)
spring.jpa.properties.hibernate.jdbc.time_zone=UTC

# Server port
server.port=8080


// File: src/main/java/com/example/demo/entity/Embeddables/Address.java
package com.example.demo.entity.embeddables;

import jakarta.persistence.Embeddable;
import jakarta.validation.constraints.Size;

/**
 * Simple embeddable Address value object.
 */
@Embeddable
public class Address {
    @Size(max = 200)
    private String street;

    @Size(max = 100)
    private String city;

    @Size(max = 100)
    private String state;

    @Size(max = 20)
    private String postalCode;

    public Address() {}

    public Address(String street, String city, String state, String postalCode) {
        this.street = street;
        this.city = city;
        this.state = state;
        this.postalCode = postalCode;
    }

    // getters & setters
    public String getStreet() { return street; }
    public void setStreet(String street) { this.street = street; }
    public String getCity() { return city; }
    public void setCity(String city) { this.city = city; }
    public String getState() { return state; }
    public void setState(String state) { this.state = state; }
    public String getPostalCode() { return postalCode; }
    public void setPostalCode(String postalCode) { this.postalCode = postalCode; }
}


// File: src/main/java/com/example/demo/entity/User.java
package com.example.demo.entity;

import com.example.demo.entity.embeddables.Address;
import jakarta.persistence.*;
import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import java.time.OffsetDateTime;
import java.util.ArrayList;
import java.util.List;

/**
 * User entity - annotated JPA entity. Includes:
 * - @Entity, @Table, @Id, @GeneratedValue
 * - @Version for optimistic locking
 * - @Embedded Address example
 * - OneToMany relationship with UserSession
 */
@Entity
@Table(name = "users",
       uniqueConstraints = {@UniqueConstraint(columnNames = {"email"})})
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Version
    private Long version; // optimistic locking

    @NotBlank
    @Column(nullable = false, length = 150)
    private String name;

    @Email
    @NotBlank
    @Column(nullable = false, length = 200)
    private String email;

    @Embedded
    private Address address;

    @Column(name = "created_at", nullable = false, updatable = false)
    private OffsetDateTime createdAt;

    @Column(name = "updated_at")
    private OffsetDateTime updatedAt;

    // Bi-directional OneToMany to user sessions
    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<UserSession> sessions = new ArrayList<>();

    public User() {}

    @PrePersist
    public void prePersist() {
        createdAt = OffsetDateTime.now();
    }

    @PreUpdate
    public void preUpdate() {
        updatedAt = OffsetDateTime.now();
    }

    // getters & setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public Long getVersion() { return version; }
    public void setVersion(Long version) { this.version = version; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public Address getAddress() { return address; }
    public void setAddress(Address address) { this.address = address; }
    public OffsetDateTime getCreatedAt() { return createdAt; }
    public void setCreatedAt(OffsetDateTime createdAt) { this.createdAt = createdAt; }
    public OffsetDateTime getUpdatedAt() { return updatedAt; }
    public void setUpdatedAt(OffsetDateTime updatedAt) { this.updatedAt = updatedAt; }
    public List<UserSession> getSessions() { return sessions; }
    public void setSessions(List<UserSession> sessions) { this.sessions = sessions; }

    public void addSession(UserSession session) {
        sessions.add(session);
        session.setUser(this);
    }

    public void removeSession(UserSession session) {
        sessions.remove(session);
        session.setUser(null);
    }
}


// File: src/main/java/com/example/demo/entity/UserSession.java
package com.example.demo.entity;

import jakarta.persistence.*;
import jakarta.validation.constraints.NotBlank;
import java.time.OffsetDateTime;

/**
 * UserSession entity - stores session tokens, FK to User.
 */
@Entity
@Table(name = "user_sessions", indexes = {
        @Index(columnList = "token"),
        @Index(columnList = "user_id")
})
public class UserSession {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    // session token (in real app use secure token generation and expiry)
    @NotBlank
    @Column(nullable = false, unique = true, length = 512)
    private String token;

    @Column(name = "created_at", nullable = false, updatable = false)
    private OffsetDateTime createdAt;

    @Column(name = "expires_at")
    private OffsetDateTime expiresAt;

    // Relationship to User: many sessions to one user
    @ManyToOne(fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "user_id", nullable = false, foreignKey = @ForeignKey(name = "fk_user_session_user"))
    private User user;

    public UserSession() {}

    public UserSession(String token, OffsetDateTime expiresAt) {
        this.token = token;
        this.expiresAt = expiresAt;
    }

    @PrePersist
    public void prePersist() {
        createdAt = OffsetDateTime.now();
    }

    // getters & setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getToken() { return token; }
    public void setToken(String token) { this.token = token; }
    public OffsetDateTime getCreatedAt() { return createdAt; }
    public void setCreatedAt(OffsetDateTime createdAt) { this.createdAt = createdAt; }
    public OffsetDateTime getExpiresAt() { return expiresAt; }
    public void setExpiresAt(OffsetDateTime expiresAt) { this.expiresAt = expiresAt; }
    public User getUser() { return user; }
    public void setUser(User user) { this.user = user; }
}


// File: src/main/java/com/example/demo/repository/UserRepository.java
package com.example.demo.repository;

import com.example.demo.entity.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.Optional;

/**
 * Basic JPA repository for User.
 * Spring Data provides common methods (save, findById, delete, paging).
 */
@Repository
public interface UserRepository extends JpaRepository<User, Long>, UserRepositoryCustom {
    Optional<User> findByEmail(String email);
}


// File: src/main/java/com/example/demo/repository/UserRepositoryCustom.java
package com.example.demo.repository;

import com.example.demo.entity.User;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;

/**
 * Custom repository contract for dynamic search using Criteria API
 */
public interface UserRepositoryCustom {
    Page<User> search(String nameOrEmail, String city, Pageable pageable);
}


// File: src/main/java/com/example/demo/repository/impl/UserRepositoryCustomImpl.java
package com.example.demo.repository.impl;

import com.example.demo.entity.User;
import com.example.demo.entity.embeddables.Address;
import com.example.demo.repository.UserRepositoryCustom;
import jakarta.persistence.EntityManager;
import jakarta.persistence.TypedQuery;
import jakarta.persistence.criteria.*;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageImpl;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Repository;

import java.util.ArrayList;
import java.util.List;

/**
 * Implementation of custom repository using JPA Criteria API (type-safe, dynamic).
 */
@Repository
public class UserRepositoryCustomImpl implements UserRepositoryCustom {

    private final EntityManager em;

    public UserRepositoryCustomImpl(EntityManager em) {
        this.em = em;
    }

    @Override
    public Page<User> search(String nameOrEmail, String city, Pageable pageable) {
        CriteriaBuilder cb = em.getCriteriaBuilder();

        // Build main query
        CriteriaQuery<User> cq = cb.createQuery(User.class);
        Root<User> root = cq.from(User.class);

        List<Predicate> predicates = new ArrayList<>();

        if (nameOrEmail != null && !nameOrEmail.isBlank()) {
            String like = "%" + nameOrEmail.toLowerCase() + "%";
            predicates.add(cb.or(
                    cb.like(cb.lower(root.get("name")), like),
                    cb.like(cb.lower(root.get("email")), like)
            ));
        }

        if (city != null && !city.isBlank()) {
            // Access embedded address field
            Path<String> cityPath = root.get("address").get("city");
            predicates.add(cb.equal(cb.lower(cityPath), city.toLowerCase()));
        }

        cq.where(predicates.toArray(new Predicate[0]));
        cq.orderBy(cb.desc(root.get("createdAt")));

        TypedQuery<User> query = em.createQuery(cq);
        // pagination
        query.setFirstResult((int) pageable.getOffset());
        query.setMaxResults(pageable.getPageSize());
        List<User> content = query.getResultList();

        // count query
        CriteriaQuery<Long> countQuery = cb.createQuery(Long.class);
        Root<User> countRoot = countQuery.from(User.class);
        countQuery.select(cb.count(countRoot));
        List<Predicate> countPredicates = new ArrayList<>();
        if (nameOrEmail != null && !nameOrEmail.isBlank()) {
            String like = "%" + nameOrEmail.toLowerCase() + "%";
            countPredicates.add(cb.or(
                    cb.like(cb.lower(countRoot.get("name")), like),
                    cb.like(cb.lower(countRoot.get("email")), like)
            ));
        }
        if (city != null && !city.isBlank()) {
            countPredicates.add(cb.equal(cb.lower(countRoot.get("address").get("city")), city.toLowerCase()));
        }
        countQuery.where(countPredicates.toArray(new Predicate[0]));
        Long total = em.createQuery(countQuery).getSingleResult();

        return new PageImpl<>(content, pageable, total);
    }
}


// File: src/main/java/com/example/demo/repository/UserSessionRepository.java
package com.example.demo.repository;

import com.example.demo.entity.UserSession;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.Optional;

@Repository
public interface UserSessionRepository extends JpaRepository<UserSession, Long> {
    Optional<UserSession> findByToken(String token);
}


// File: src/main/java/com/example/demo/dto/UserDto.java
package com.example.demo.dto;

import com.example.demo.dto.AddressDto;

import java.time.OffsetDateTime;
import java.util.List;

/**
 * Response DTO for User - keeps entity encapsulated.
 */
public class UserDto {
    private Long id;
    private String name;
    private String email;
    private AddressDto address;
    private OffsetDateTime createdAt;
    private OffsetDateTime updatedAt;
    private Long version;
    private List<SessionDto> sessions;

    public UserDto() {}

    // getters & setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public AddressDto getAddress() { return address; }
    public void setAddress(AddressDto address) { this.address = address; }
    public OffsetDateTime getCreatedAt() { return createdAt; }
    public void setCreatedAt(OffsetDateTime createdAt) { this.createdAt = createdAt; }
    public OffsetDateTime getUpdatedAt() { return updatedAt; }
    public void setUpdatedAt(OffsetDateTime updatedAt) { this.updatedAt = updatedAt; }
    public Long getVersion() { return version; }
    public void setVersion(Long version) { this.version = version; }
    public List<SessionDto> getSessions() { return sessions; }
    public void setSessions(List<SessionDto> sessions) { this.sessions = sessions; }
}


// File: src/main/java/com/example/demo/dto/AddressDto.java
package com.example.demo.dto;

public class AddressDto {
    private String street;
    private String city;
    private String state;
    private String postalCode;

    public AddressDto() {}

    // getters & setters
    public String getStreet() { return street; }
    public void setStreet(String street) { this.street = street; }
    public String getCity() { return city; }
    public void setCity(String city) { this.city = city; }
    public String getState() { return state; }
    public void setState(String state) { this.state = state; }
    public String getPostalCode() { return postalCode; }
    public void setPostalCode(String postalCode) { this.postalCode = postalCode; }
}


// File: src/main/java/com/example/demo/dto/SessionDto.java
package com.example.demo.dto;

import java.time.OffsetDateTime;

public class SessionDto {
    private Long id;
    private String token;
    private OffsetDateTime createdAt;
    private OffsetDateTime expiresAt;

    public SessionDto() {}

    // getters & setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getToken() { return token; }
    public void setToken(String token) { this.token = token; }
    public OffsetDateTime getCreatedAt() { return createdAt; }
    public void setCreatedAt(OffsetDateTime createdAt) { this.createdAt = createdAt; }
    public OffsetDateTime getExpiresAt() { return expiresAt; }
    public void setExpiresAt(OffsetDateTime expiresAt) { this.expiresAt = expiresAt; }
}


// File: src/main/java/com/example/demo/dto/UserCreateRequest.java
package com.example.demo.dto;

import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Size;

/**
 * Create DTO for User - input validation on fields
 */
public class UserCreateRequest {

    @NotBlank
    @Size(max = 150)
    private String name;

    @NotBlank
    @Email
    @Size(max = 200)
    private String email;

    private AddressDto address;

    // getters & setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public AddressDto getAddress() { return address; }
    public void setAddress(AddressDto address) { this.address = address; }
}


// File: src/main/java/com/example/demo/dto/UserUpdateRequest.java
package com.example.demo.dto;

import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Size;

/**
 * Update DTO - includes version for optimistic locking
 */
public class UserUpdateRequest {

    @NotNull
    private Long id;

    @NotBlank
    @Size(max = 150)
    private String name;

    @NotBlank
    @Email
    @Size(max = 200)
    private String email;

    private AddressDto address;

    @NotNull
    private Long version; // client must send version to avoid lost updates

    // getters & setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public AddressDto getAddress() { return address; }
    public void setAddress(AddressDto address) { this.address = address; }
    public Long getVersion() { return version; }
    public void setVersion(Long version) { this.version = version; }
}


// File: src/main/java/com/example/demo/exception/ResourceNotFoundException.java
package com.example.demo.exception;

public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) { super(message); }
}


// File: src/main/java/com/example/demo/exception/ConflictException.java
package com.example.demo.exception;

public class ConflictException extends RuntimeException {
    public ConflictException(String message) { super(message); }
}


// File: src/main/java/com/example/demo/controller/GlobalExceptionHandler.java
package com.example.demo.controller;

import com.example.demo.exception.ConflictException;
import com.example.demo.exception.ResourceNotFoundException;
import jakarta.persistence.OptimisticLockException;
import jakarta.validation.ConstraintViolationException;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.time.OffsetDateTime;
import java.util.HashMap;
import java.util.Map;

/**
 * Global controller advice to return consistent error responses.
 */
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<?> handleNotFound(ResourceNotFoundException ex) {
        return error(HttpStatus.NOT_FOUND, ex.getMessage());
    }

    @ExceptionHandler(ConflictException.class)
    public ResponseEntity<?> handleConflict(ConflictException ex) {
        return error(HttpStatus.CONFLICT, ex.getMessage());
    }

    @ExceptionHandler(OptimisticLockException.class)
    public ResponseEntity<?> handleOptimisticLock(OptimisticLockException ex) {
        return error(HttpStatus.CONFLICT, "Concurrent update detected. " + ex.getMessage());
    }

    @ExceptionHandler(ConstraintViolationException.class)
    public ResponseEntity<?> handleValidation(ConstraintViolationException ex) {
        return error(HttpStatus.BAD_REQUEST, "Validation failed: " + ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<?> handleGeneric(Exception ex) {
        return error(HttpStatus.INTERNAL_SERVER_ERROR, "Unexpected error: " + ex.getMessage());
    }

    private ResponseEntity<?> error(HttpStatus status, String message) {
        Map<String, Object> body = new HashMap<>();
        body.put("timestamp", OffsetDateTime.now());
        body.put("status", status.value());
        body.put("error", status.getReasonPhrase());
        body.put("message", message);
        return ResponseEntity.status(status).body(body);
    }
}


// File: src/main/java/com/example/demo/service/UserService.java
package com.example.demo.service;

import com.example.demo.dto.*;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;

/**
 * Service interface for user operations
 */
public interface UserService {
    UserDto create(UserCreateRequest req);
    UserDto getById(Long id);
    UserDto update(UserUpdateRequest req);
    void delete(Long id);
    Page<UserDto> list(String q, String city, Pageable pageable);
    SessionDto createSession(Long userId); // simple session creation
}


// File: src/main/java/com/example/demo/service/impl/UserServiceImpl.java
package com.example.demo.service.impl;

import com.example.demo.dto.*;
import com.example.demo.entity.User;
import com.example.demo.entity.UserSession;
import com.example.demo.entity.embeddables.Address;
import com.example.demo.exception.ConflictException;
import com.example.demo.exception.ResourceNotFoundException;
import com.example.demo.repository.UserRepository;
import com.example.demo.repository.UserSessionRepository;
import com.example.demo.service.UserService;
import jakarta.persistence.EntityManager;
import jakarta.persistence.OptimisticLockException;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.time.OffsetDateTime;
import java.util.*;
import java.util.stream.Collectors;

/**
 * Service implementation:
 * - Maps DTO -> Entity and Entity -> DTO
 * - Handles optimistic locking (via @Version on entity)
 * - Uses repository for persistence
 */
@Service
public class UserServiceImpl implements UserService {

    private final UserRepository userRepository;
    private final UserSessionRepository sessionRepository;
    private final EntityManager em;

    public UserServiceImpl(UserRepository userRepository, UserSessionRepository sessionRepository, EntityManager em) {
        this.userRepository = userRepository;
        this.sessionRepository = sessionRepository;
        this.em = em;
    }

    @Override
    @Transactional
    public UserDto create(UserCreateRequest req) {
        // check unique email
        userRepository.findByEmail(req.getEmail()).ifPresent(u -> {
            throw new ConflictException("Email already in use");
        });

        User user = new User();
        user.setName(req.getName());
        user.setEmail(req.getEmail());
        if (req.getAddress() != null) {
            user.setAddress(mapToAddress(req.getAddress()));
        }
        User saved = userRepository.save(user);
        return mapToDto(saved);
    }

    @Override
    @Transactional(readOnly = true)
    public UserDto getById(Long id) {
        return userRepository.findById(id)
                .map(this::mapToDto)
                .orElseThrow(() -> new ResourceNotFoundException("User not found: " + id));
    }

    @Override
    @Transactional
    public UserDto update(UserUpdateRequest req) {
        User u = userRepository.findById(req.getId())
                .orElseThrow(() -> new ResourceNotFoundException("User not found: " + req.getId()));

        // Optimistic locking check: ensure version matches
        if (!Objects.equals(u.getVersion(), req.getVersion())) {
            throw new OptimisticLockException("Version mismatch");
        }

        u.setName(req.getName());
        u.setEmail(req.getEmail());
        if (req.getAddress() != null) {
            u.setAddress(mapToAddress(req.getAddress()));
        } else {
            u.setAddress(null);
        }

        // saving entity will increment version via @Version
        User saved = userRepository.save(u);
        // ensure changes are flushed so caller can see updated version
        userRepository.flush();
        return mapToDto(saved);
    }

    @Override
    @Transactional
    public void delete(Long id) {
        User u = userRepository.findById(id)
                .orElseThrow(() -> new ResourceNotFoundException("User not found: " + id));
        userRepository.delete(u);
    }

    @Override
    @Transactional(readOnly = true)
    public Page<UserDto> list(String q, String city, Pageable pageable) {
        Page<User> page = userRepository.search(q, city, pageable);
        return page.map(this::mapToDto);
    }

    @Override
    @Transactional
    public SessionDto createSession(Long userId) {
        User u = userRepository.findById(userId)
                .orElseThrow(() -> new ResourceNotFoundException("User not found: " + userId));

        // generate a simple token (for demo only!)
        String token = UUID.randomUUID().toString() + "-" + System.currentTimeMillis();
        OffsetDateTime expiresAt = OffsetDateTime.now().plusDays(7);

        UserSession session = new UserSession(token, expiresAt);
        session.setUser(u);
        UserSession saved = sessionRepository.save(session);

        // return session dto
        SessionDto dto = new SessionDto();
        dto.setId(saved.getId());
        dto.setToken(saved.getToken());
        dto.setCreatedAt(saved.getCreatedAt());
        dto.setExpiresAt(saved.getExpiresAt());
        return dto;
    }

    // ---------- Mapping helpers ----------
    private UserDto mapToDto(User u) {
        UserDto dto = new UserDto();
        dto.setId(u.getId());
        dto.setName(u.getName());
        dto.setEmail(u.getEmail());
        dto.setCreatedAt(u.getCreatedAt());
        dto.setUpdatedAt(u.getUpdatedAt());
        dto.setVersion(u.getVersion());
        if (u.getAddress() != null) {
            dto.setAddress(mapToDto(u.getAddress()));
        }
        // sessions (lazy): convert if present
        if (u.getSessions() != null) {
            dto.setSessions(u.getSessions().stream().map(this::mapToDto).collect(Collectors.toList()));
        }
        return dto;
    }

    private AddressDto mapToDto(Address a) {
        AddressDto d = new AddressDto();
        d.setStreet(a.getStreet());
        d.setCity(a.getCity());
        d.setState(a.getState());
        d.setPostalCode(a.getPostalCode());
        return d;
    }

    private SessionDto mapToDto(UserSession s) {
        SessionDto d = new SessionDto();
        d.setId(s.getId());
        d.setToken(s.getToken());
        d.setCreatedAt(s.getCreatedAt());
        d.setExpiresAt(s.getExpiresAt());
        return d;
    }

    private Address mapToAddress(AddressDto d) {
        return new Address(d.getStreet(), d.getCity(), d.getState(), d.getPostalCode());
    }
}


// File: src/main/java/com/example/demo/controller/UserController.java
package com.example.demo.controller;

import com.example.demo.dto.*;
import com.example.demo.service.UserService;
import jakarta.validation.Valid;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

/**
 * REST controller exposing CRUD and list/search endpoints.
 *
 * Endpoints:
 * POST   /api/users         -> create
 * GET    /api/users/{id}    -> get by id
 * PUT    /api/users/{id}    -> update
 * DELETE /api/users/{id}    -> delete
 * GET    /api/users         -> list (paging + filters)
 * POST   /api/users/{id}/sessions -> create a session for user
 */
@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService svc;

    public UserController(UserService svc) {
        this.svc = svc;
    }

    @PostMapping
    public ResponseEntity<UserDto> create(@Valid @RequestBody UserCreateRequest req) {
        UserDto created = svc.create(req);
        return ResponseEntity.status(201).body(created);
    }

    @GetMapping("/{id}")
    public ResponseEntity<UserDto> get(@PathVariable Long id) {
        return ResponseEntity.ok(svc.getById(id));
    }

    @PutMapping("/{id}")
    public ResponseEntity<UserDto> update(@PathVariable Long id, @Valid @RequestBody UserUpdateRequest req) {
        // ensure path id matches payload id (defensive)
        if (!id.equals(req.getId())) {
            throw new IllegalArgumentException("Path id and payload id do not match");
        }
        UserDto updated = svc.update(req);
        return ResponseEntity.ok(updated);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<?> delete(@PathVariable Long id) {
        svc.delete(id);
        return ResponseEntity.noContent().build();
    }

    @GetMapping
    public ResponseEntity<Page<UserDto>> list(
            @RequestParam(value = "q", required = false) String q,
            @RequestParam(value = "city", required = false) String city,
            @RequestParam(value = "page", defaultValue = "0") int page,
            @RequestParam(value = "size", defaultValue = "20") int size
    ) {
        PageRequest pr = PageRequest.of(Math.max(0, page), Math.max(1, size));
        Page<UserDto> result = svc.list(q, city, pr);
        return ResponseEntity.ok(result);
    }

    @PostMapping("/{id}/sessions")
    public ResponseEntity<SessionDto> createSession(@PathVariable Long id) {
        SessionDto dto = svc.createSession(id);
        return ResponseEntity.status(201).body(dto);
    }
}
```
