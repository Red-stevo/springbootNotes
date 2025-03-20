# MyBatis Configuration in Spring Boot

This guide provides step-by-step instructions for setting up **MyBatis** in a Spring Boot project using XML-based SQL mapping.

## 1. Add Dependencies

### **Maven**
```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.19</version>
</dependency>

```

### **Gradle**
```gradle
// https://mvnrepository.com/artifact/org.mybatis/mybatis
implementation group: 'org.mybatis', name: 'mybatis', version: '3.5.19'

```

---

## 2. Configure MyBatis in `application.yml`
```yaml
mybatis:
  config-location: classpath:mybatis-config.xml
  mapper-locations: classpath:mappers/*.xml
```

For `application.properties`:
```properties
mybatis.config-location=classpath:mybatis-config.xml
mybatis.mapper-locations=classpath:mappers/*.xml
```

---

## 3. Create `mybatis-config.xml`
Create this file inside `src/main/resources/`:
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
</configuration>
```

---

## 4. Create the Entity Class (`Customer.java`)
```java

@Data //lombok
@Entity
@Table("customer")
@Builder()
public class Customer {
    
    @Id
    private Long id;
    private String firstName;
    private String lastName;
    private String email;
}
```

---

## 5. Create the Mapper Interface (`CustomerMapper.java`)
```java

@Mapper
public interface CustomerMapper {
    @Select("SELECT * FROM customers WHERE id = #{id}")
    Customer getCustomerById(@Param("id") Long id);

    List<Customer> getAllCustomers(); // Mapped in XML
    
    void insertCustomer(Customer customer); // Mapped in XML
}
```

---

## 6. Create the Mapper XML File (`CustomerMapper.xml`)
Save this file in `src/main/resources/mappers/`:
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.repository.CustomerMapper">

    <!-- Select Query -->
    <select id="getAllCustomers" resultType="com.example.model.Customer">
        SELECT id, first_name AS firstName, last_name AS lastName, email FROM customers;
    </select>

    <!-- Insert Query -->
    <insert id="insertCustomer" parameterType="com.example.model.Customer">
        INSERT INTO customers (first_name, last_name, email) 
        VALUES (#{firstName}, #{lastName}, #{email});
    </insert>

</mapper>
```

---

## 7. Implement the Service Layer (`CustomerService.java`)
```java
@Service
public class CustomerService {
    @Autowired
    private CustomerMapper customerMapper;

    public List<Customer> getAllCustomers() {
        return customerMapper.getAllCustomers();
    }

    public Customer getCustomerById(Long id) {
        return customerMapper.getCustomerById(id);
    }

    public void addCustomer(Customer customer) {
        customerMapper.insertCustomer(customer);
    }
}
```

---

## 8. Create a Controller (`CustomerController.java`)
```java
@RestController
@RequestMapping("/customers")
public class CustomerController {
    @Autowired
    private CustomerService customerService;

    @GetMapping
    public List<Customer> getAllCustomers() {
        return customerService.getAllCustomers();
    }

    @GetMapping("/{id}")
    public Customer getCustomerById(@PathVariable Long id) {
        return customerService.getCustomerById(id);
    }

    @PostMapping
    public ResponseEntity<String> addCustomer(@RequestBody Customer customer) {
        customerService.addCustomer(customer);
        return ResponseEntity.ok("Customer added successfully");
    }
}
```

---

## Summary of Steps
| Step | Description |
|------|------------|
| **1** | Add MyBatis dependency (`pom.xml` or `build.gradle`) |
| **2** | Configure MyBatis in `application.yml` or `application.properties` |
| **3** | Create `mybatis-config.xml` for settings |
| **4** | Define the entity class (`Customer.java`) |
| **5** | Create a Mapper interface (`CustomerMapper.java`) |
| **6** | Define SQL queries in XML (`CustomerMapper.xml`) |
| **7** | Implement the service layer (`CustomerService.java`) |
| **8** | Create the REST API controller (`CustomerController.java`) |

---

## Running the Application
To start the Spring Boot application, run:
```sh
mvn spring-boot:run
```
or with Gradle:
```sh
gradle bootRun
```

Your application will now be accessible at:
```
http://localhost:8080/customers
```

---

## Notes
- MyBatis allows SQL queries to be managed externally in XML files.
- The **CamelCase mapping** (`mapUnderscoreToCamelCase=true`) enables automatic conversion of `first_name` → `firstName`.
- The mapper interface links SQL queries to Java methods.
- The controller exposes REST endpoints for CRUD operations.

---

## Conclusion
This guide shows how to configure **MyBatis** with XML-based SQL mapping in **Spring Boot**. You can now easily manage database queries externally while keeping your codebase clean.🚀🚀
