---
published: true
title: Liquibase - Brief Intro and best practices
layout: post
---
Liquibase is an open source library to track database changes.

What does that mean?
We all have worked in projects where the typical database change management process was to write DDL, DML scripts in sql files which were source controlled and database changes were executed by self or by some other dedicated team. But there wasn’t really a nice way to define sequence of these changes, it was followed as best practice in the project. We always had to write sql for rollbacks as well and migration to other database was a painful task.

Liquibase tries to address all these issues in an elegant way. Liquibase scripts are typically written in xml format (may be because xml is more readable), though other formats like json and yaml are also supported. Database changes are defined in change log files as small change sets which are uniquely identified by change set name and author. Sequence is defined in master change log file. Rollbacks are handled in most of the cases by Liquibase itself. And since these scripts are database agnostic, migrations to other databases are relatively smooth. As of now, Liquibase supports most of the popular database including MySQL, Oracle, DB2 and PostgreSQL.

Maven Integration
Including Liquibase to Maven projects is pretty easy. Just follow below 2 steps:

Step 1: Add below dependency to include liquibase jar

~~~ xml
<dependency>
    <groupId>org.liquibase</groupId>
    <artifactId>liquibase-core</artifactId>
    <version>3.3.2</version>
</dependency>
~~~

Step 2: To control liquibase via Maven plugin

~~~ xml
<build>
    <plugins>
        <plugin>
            <groupId>org.liquibase</groupId>
            <artifactId>liquibase-maven-plugin</artifactId>
            <version>3.3.2</version>
            <configuration>
                <changeLogFile>
                ${basedir}/src/main/resources/liquibase/master.xml
                </changeLogFile>
                <driver>org.apache.derby.jdbc.ClientDriver</driver>
                <url>jdbc:derby://localhost:1527/message</url>
                <username>app</username>
                <password>app</password>
            </configuration>
        </plugin>
    </plugins>
</build>
~~~

changeLogFile element is to define the location of the master change log file which has ordered references of other change log files.

Most frequently used goals:

To execute your database changes:
    mvn liquibase:update

To rollback changes with count parameter:
    mvn liquibase:rollback -Dliquibase.rollbackCount=n (rolls back last n change sets)

Project structure example:

![alt text](/img/project-structure.png "Project Structure")

master.xml

~~~ xml
<databaseChangeLog>
    <include file="changelog-create-employee-table.xml" 
        relativeToChangelogFile="true"/>
    <include file="changelog-add-employee-data.xml" 
        relativeToChangelogFile="true"/>
</databaseChangeLog>
~~~

Change set example (for adding employee table):

~~~ xml
<databaseChangeLog>
    <changeSet id="create_employee_table" author="sinhav">
        <createTable tableName="Employee">
            <column name="EmployeeId" type="BIGINT">
                <constraints nullable="false"/>
            </column>
            <column name="FirstName" type="VARCHAR(50)">
                <constraints nullable="true"/>
            </column>
            <column name="LastName" type="varchar(50)">
                <constraints nullable="true"/>
            </column>
            <column name="Phone" type="varchar(50)">
                <constraints nullable="true"/>
            </column>
            <column name="JoiningDate" type="Date">
                <constraints nullable="true"/>
            </column>
        </createTable>
    </changeSet>
    <changeSet id="add_pk_for_employee" author="sinhav">
    <addPrimaryKey columnNames="EmployeeId"
                  constraintName="pk_employee"
                  tableName="Employee" />
    </changeSet>
</databaseChangeLog>
~~~

Executing liquibase for the first time will create 2 additional tables in Database: Databasechangelog and Databasechangeloglock

First is to track all executed change sets and second to provide locking mechanism to ensure only one instance of liquibase is running at a time.

Best Practices:

- Organizing change logs:
As shown in the project structure above, select a directory to store all your change log files and define their sequence in master change log file. Liquibase suggests to organize these files by major version but working in a multi developer environment, I would recommend to skip version from file names to avoid conflicts arising to developers choosing same versions. Its better to choose a logical and explanatory name for these change log files which upon first look would give some idea about the type of database changes it contains. E.g.
db.changelog-add-queued-messages-table.xml
db.changelog-add-retry-column-to-queued-messages-table.xml

- Change set id and author name:
Combination of change set id and author name uniquely identifies a change set. Change set id should be logical and explanatory name and author name should easily identify the developer who created the change set.

- Pre-condition check for DDL statements:
If you are inserting any data which relies on data of some other tables then take care to check it as a pre-condition to ensure your change set is executed successfully across all environments regardless of data discrepancy across environments. E.g.

~~~ xml
<changeSet id="insert-subcustomer" author="sinhav">
    <preConditions onFail="MARK_RAN" 
    onFailMessage="There is no customers data">
        <sqlCheck expectedResult="1" >
            select count(1)
            from Customers
            where CustomerNumber = ‘1234567'
        </sqlCheck>
    </preConditions>
    <sql>
        insert into SubCustomers(MasterCustomerId, SubCustomerNumber,
        SupplierName, ApiId) values(
        (select id from Customers where CustomerNumber = ‘1234567'),
        ‘11223344', ’VS Suppliers', ‘TPR871')
    </sql>
    <rollback>
        <delete tableName="SubCustomers">
            <where>SubCustomerNumber='11223344'</where>
        </delete>
    </rollback>
</changeSet>
~~~

- Rollback Mechanism:
Liquibase offers rollback for DDL statements but for all DML statement rollback has to be handled explicitly by developer. For example, the above change set of creating employee table will create one record in databasechangelog table for this change set. For rolling back this change, you can execute command mvn liquibase:rollback -Dliquibase.rollbackCount=1, this will delete table and will also remove change set record from databasechangelog table.

If you attempt to rollback a change set which has some DML statement then it will result in liquibase.exception.RollbackImpossibleException so make sure that you handle this rollback for DML statement explicitly in the change sets. Its always a good practice to run liquibase and test that both migration and rollback of your change set works as expected and without any exceptions. You can also have empty rollback blocks in case you don’t want to take any action on rollback.

Example of change set having DML statements along with rollback:

~~~ xml
<changeSet id="add_employee_data" author="sinhav">
    <insert tableName="Employee">
        <column name="EmployeeId" type="BIGINT" valueNumeric="10001"/>
        <column name="FirstName">Vishal</column>
        <column name="LastName">Sinha</column>
        <column name="Phone">+47 32324324</column>
        <column name="JoiningDate" valueDate="2004-06-09"/>
    </insert>
    <insert tableName="Employee">
        <column name="EmployeeId" type="BIGINT" valueNumeric="10002"/>
        <column name="FirstName">Nishant</column>
        <column name="LastName">Varshney</column>
        <column name="Phone">+91 9834249399</column>
        <column name="JoiningDate" valueDate="2004-05-11"/>
    </insert>
    <insert tableName="Employee">
        <column name="EmployeeId" type="BIGINT" valueNumeric="10003"/>
        <column name="FirstName">Rajat</column>
        <column name="LastName">Sharma</column>
        <column name="Phone">+91 8342342345</column>
        <column name="JoiningDate" valueDate="2004-07-07"/>
    </insert>
    <rollback>
        <sql>
            delete from Employee
            where EmployeeId in (10001, 10002, 10003)
        </sql>
    </rollback>
</changeSet>
~~~

For complete reference:

<a href="http://www.liquibase.org/" target="_blank">http://www.liquibase.org/</a>