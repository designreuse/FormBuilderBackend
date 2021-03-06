RESTFUL-WS 
==========



Collection of RESTful web service implementation for core DASH functions. The source code is developed as Eclipse Maven project using facets "Dynamic Web Project" and "Java". The code has been tested for a default JRE Java 1.7 The following assumptions have been made:

1. Java backend provides a RESTful web service.  
2. AngularJS consumes the web service.

We used a [tutorial](http://www.codingpedia.org/ama/tutorial-rest-api-design-and-implementation-in-java-with-jersey-and-spring/) as guideline for the overall implementation.

Description of the Java backend
================================

We are using [Jersey](https://jersey.java.net/) as framework for providing REST. Logging is done with Logback as described [here](http://www.codingpedia.org/ama/how-to-log-in-spring-with-slf4j-and-logback/).


Instructions for authentication against the security filter
===========================================================

1. Set up the webSecurityConfig.xml to connect to the database you keep the security tables in.  By default it connects to the DB `test` with the login credentials "root" and no password.

2. Import the test_acl.sql file included in the mysql directory.  This includes the neccissary tables for user management and acl.  The only user that comes with the table is "tyler" who is given "ROLE_ADMIN".  You can use this user to authenticate against the server to gain access to UserService.createUsers() function which should allow for batch creation of users.  All new users created via post are given role "ROLE_USER".

Using ACL for new object services
=================================

For examples of using PreAuthorize, PostAuthorize, PreFilter, and PostFilter..
See http://krams915.blogspot.com/2011/01/spring-security-3-full-acl-tutorial_1042.html

Key things to remember:
1. Authorization annotations should be included at the service level interface.

2. Any methods that will delete a resource should accept an instance of the POJO as a parameter and apply a PreAuthorization annotation. Do not allow deletion of an object with just an id, it wont work right.  The have to give us a json of the object they want to delete. Example:

    I want to delete an object of class Foo, called bar.
    My service level method should look like
    
    ```
    @PreAuthorize("hasPermission(#bar, 'DELETE')")
    void deleteObject(Foo bar);
    ```

3. All  POJOs that will be access controlled need to implement interface IAclObject.  Make sure that it is the POJO that implements it, and not the entity for that POJO.

4. The service implementation of all resources (other than user) should contain an instance of GenericAclContoller.  Be sure to provide the template the POJO class you are handling with that service.

