2. Add the following dependencies in the `pom.xml` file:
   ``xml
   <dependencies>
       <!-- Hibernate Core -->
       <dependency>
           <groupId>org.hibernate</groupId>
           <artifactId>hibernate-core</artifactId>
           <version>5.6.14.Final</version>
       </dependency>
       <!-- MySQL Connector -->
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
           <version>8.0.34</version>
       </dependency>

       <!-- JPA API -->
   <dependency>
           <groupId>javax.persistence</groupId>
           <artifactId>javax.persistence-api</artifactId>
           <version>2.2</version>
       </dependency>

       <!-- Logging for Hibernate -->
   <dependency>
           <groupId>org.jboss.logging</groupId>
           <artifactId>jboss-logging</artifactId>
           <version>3.4.3.Final</version>
       </dependency>
   </dependencies>
   ```

---

 2. **Database Setup**
Create a database named labexam and a table for departments:
``sql
CREATE DATABASE labexam;

USE labexam;

CREATE TABLE Department (
    department_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    location VARCHAR(100),
    hod_name VARCHAR(100)
);
``

---
##3. **Hibernate Configuration (`hibernate.cfg.xml`)**
Place this file in the src/main/resources directory:
``xml
<!DOCTYPE hibernate-configuration PUBLIC 
    "-//Hibernate/Hibernate Configuration DTD 3.0//EN" 
    "http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">

<hibernate-configuration>
    <session-factory>
        <!-- JDBC connection settings -->
        <property name="hibernate.connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/labexam</property>
        <property name="hibernate.connection.username">root</property>
        <property name="hibernate.connection.password">your_password</property>

        <!-- Hibernate settings -->
  <property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
        <property name="hibernate.show_sql">true</property>
        <property name="hibernate.hbm2ddl.auto">update</property>
        <!-- Entity Mapping -->
  <mapping class="com.klef.jfsd.exam.Department" />
    </session-factory>
</hibernate-configuration>
``
### 4. **Department Entity Class**
Create the Department entity class under com.klef.jfsd.exam package:
`java
package com.klef.jfsd.exam;

import javax.persistence.*;

@Entity
@Table(name = "Department")
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "department_id")
    private int departmentId;

  @Column(name = "name")
  private String name;

  @Column(name = "location")
    private String location;

   @Column(name = "hod_name")
    private String hodName;

   // Getters and setters
    public int getDepartmentId() {
        return departmentId;
    }

  public void setDepartmentId(int departmentId) {
        this.departmentId = departmentId;
    }

  public String getName() {
        return name;
    }

  public void setName(String name) {
        this.name = name;
    }

  public String getLocation() {
        return location;
    }

  public void setLocation(String location) {
        this.location = location;
    }

public String getHodName() {
        return hodName;
    }

   public void setHodName(String hodName) {
        this.hodName = hodName;
    }
}
``

-

### 5. **ClientDemo Class**
Create the `ClientDemo` class under `com.klef.jfsd.exam` package to perform the operations:java
package com.klef.jfsd.exam;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class ClientDemo {
    public static void main(String[] args) {
        // Load configuration
        Configuration config = new Configuration().configure();
        SessionFactory sessionFactory = config.buildSessionFactory();

   // Insert a new record
        insertDepartment(sessionFactory, "Computer Science", "Hyderabad", "Dr. Sharma");

  // Delete a record by department ID
        deleteDepartmentById(sessionFactory, 1);

   sessionFactory.close();
    }

  public static void insertDepartment(SessionFactory sessionFactory, String name, String location, String hodName) {
        Session session = sessionFactory.openSession();
        Transaction tx = session.beginTransaction();

   // Create a new Department object
        Department dept = new Department();
        dept.setName(name);
        dept.setLocation(location);
        dept.setHodName(hodName);

  // Save the object
        session.save(dept);
        tx.commit();
        session.close();
        System.out.println("Record inserted successfully!");
    }

  public static void deleteDepartmentById(SessionFactory sessionFactory, int departmentId) {
        Session session = sessionFactory.openSession();
        Transaction tx = session.beginTransaction();

   // HQL delete operation using positional parameter
        String hql = "DELETE FROM Department WHERE departmentId = :id";
        int deleted = session.createQuery(hql)
                             .setParameter("id", departmentId)
                             .executeUpdate();

  tx.commit();
        session.close();
        System.out.println(deleted + " record(s) deleted successfully!");
    }
}
