# 1. Big Picture

* **What is this concept?**
  We are building a Spring Boot REST API, starting with a simple "Hello World" application. It involves setting up a server that listens for incoming web requests from a client (like a browser) and returns a text response.
* **Why does it exist?**
  Writing a web application from scratch in Java requires manually setting up folder structures, configuring servers, and downloading external libraries. Spring Boot exists to automatically configure these elements, allowing developers to focus purely on business logic. 
* **What problem does it solve?**
  It solves the problem of "boilerplate code" and manual server configuration. Instead of manually downloading an Apache Tomcat server and routing HTTP requests, Spring Boot provides an embedded server and simple annotations to handle networking.
* **Where does it fit in the Java → Spring Boot → Backend roadmap?**
  This is the very first step in transitioning from a Core Java developer to a Backend Engineer. You are moving from running code in a local console (using `public static void main`) to running code on a live server that anyone can access over a network.

# 2. First-Principles Explanation

To understand backend development, you must understand how computers talk to each other. 

**1. Finding the Computer (IP Address & DNS)**
When a client (browser) wants to talk to a server, it needs to know where the server lives. Every device connected to a network has a unique **IP Address**. Since humans cannot remember numbers easily, we use Domain Names (like `coderarmy.in`). A **DNS (Domain Name System)** resolves this domain name into an actual IP address.
When you are building and testing an application on your own computer, your computer acts as both the client and the server. To talk to yourself, you use the fixed IP address `127.0.0.1`, which is mapped to the word **localhost**.

**2. Finding the Application (Port Numbers)**
Once the data reaches your computer via the IP address, the Operating System needs to know which application should receive the data (e.g., Chrome, WhatsApp, Spotify, or your Spring Boot app). This is solved by **Port Numbers**. An IP address brings you to the machine, and the port number brings you to the specific application. 
Standard web traffic uses default ports: HTTP uses Port `80`, and HTTPS uses Port `443`. Spring Boot applications default to running on Port `8080`. 

**3. Bridging the Web and Java (Spring Initializr & Dependencies)**
To write code that listens on these ports, we need an underlying template. **Spring Initializr** (`start.spring.io`) generates this template, setting up the folder structure, Java versions, and project management tools (like Maven). 
Your code will also need **Dependencies** (external libraries written by others) to perform specific tasks, like connecting to a database (MySQL Connector) or listening to web requests (Spring Web).

# 3. Mental Model

**The Apartment Building Analogy**
* **The Server Computer** is a massive apartment building.
* **The IP Address** is the street address of the building. The postman (DNS) looks up the street address to deliver the package.
* **The Port Number** is the specific apartment number inside the building. Apartment `443` (HTTPS) gets standard mail, but your specific Spring Boot application lives in Apartment `8080`.
* **The Reverse Proxy** is the lobby receptionist. If a package arrives at standard Apartment `443`, the receptionist redirects it to your custom application in Apartment `8080`.
* **The Controller (`@RestController`)** is the specific person inside the apartment answering the door, reading the request, and handing back a "Hello World" letter.

# 4. Internal Working

When you hit `localhost:8080/hello` in your browser, here is the architecture of what happens behind the scenes:

```text
[Browser (Client)] 
       | 1. HTTP GET Request to localhost (127.0.0.1)
       v
[Operating System] 
       | 2. Routes traffic to Port 8080
       v
[Apache Tomcat (Embedded Server)] <--- Spun up automatically by Spring Boot!
       | 3. Receives HTTP request, looks for mapped paths
       v
[Spring Web Framework] 
       | 4. Matches "/hello" path to the specific Controller
       v
[@RestController (HelloController)]
       | 5. Executes Java method "hello()"
       v
[Returns "Hello World" String back up the chain]
```

**Key Internal Mechanisms:**
* **Embedded Tomcat:** By including the "Spring Web" dependency, Spring Boot automatically embeds an Apache Tomcat server (a Servlet Container) inside your application. When you run your Java `main` method, Tomcat starts up on port `8080`.
* **Reverse Proxy Mapping:** In real-world production, servers receive traffic on port `443` (HTTPS). A Reverse Proxy sits in front and maps incoming requests on port `443` internally to port `8080` where your Spring Boot app is actually listening.

# 5. Code Explanation

Here is the exact code required to make a REST API endpoint.

**1. The Controller Class (`HelloController.java`)**
```java
@RestController
public class HelloController {
    
    @GetMapping("/hello")
    public String hello() {
        return "Hello World";
    }
}
```
* **`@RestController`**: 
  * **What it does**: Tells the Spring Boot framework that this specific Java class is a gateway for API endpoints.
  * **Why it exists**: Without this, Spring would treat the class as standard Java code and Tomcat wouldn't know to route web traffic to it.
  * **What happens if removed**: The application will run, but going to `/hello` in the browser will result in a "White Label Error Page" (404 Not Found) because the endpoint is no longer registered.

* **`@GetMapping("/hello")`**:
  * **What it does**: Maps an incoming HTTP GET request with the URL path `/hello` to the `hello()` method below it.
  * **Why it exists**: It connects the networking world (URLs) to the Java world (methods).
  * **What happens if removed**: The browser won't be able to trigger the `hello()` method.

**2. Changing Server Configurations (`application.properties`)**
```properties
server.port=9090
```
* **What it does**: Changes the default Tomcat server port from `8080` to `9090`.
* **Why it exists**: If another application on your computer is already using port `8080`, your Spring Boot app will crash. This allows you to easily switch ports.

# 6. Backend Connection

* **Spring Boot:** This entirely relies on Spring Boot's "auto-configuration" philosophy. It automatically binds Tomcat to your Java code without you writing XML files or manual server configurations. 
* **REST APIs:** You have created your first API endpoint (`/hello`). In real applications, instead of returning just strings ("Hello World" or "Bye"), you will return structured JSON data representing users, products, or orders.
* **Databases:** We discussed **Dependencies**. To connect this API to a database later, you won't write custom socket connections. You will simply add a "MySQL Connector" library via Spring Initializr, and Spring Boot will auto-configure the database connection.
* **Why Learn Core Architecture?** If you just memorize `@RestController`, you won't know how to debug when things break. Understanding that the `@RestController` is communicating with an embedded Tomcat server listening on a specific Port allows you to effectively troubleshoot networking and mapping issues.

# 7. Common Mistakes

* **Mistake:** Confusing an IP Address with a Port. 
  * *Correction:* IP is the machine (`localhost`); Port is the specific app (`8080`).
* **Mistake:** Trying to manually install and configure an Apache Tomcat server.
  * *Correction:* Spring Web already contains Tomcat as an embedded container. Running the Java `main` method starts the server automatically.
* **Mistake:** Using "Snapshot" versions of Spring Boot from Spring Initializr.
  * *Correction:* Snapshots are "work in progress" versions and may contain bugs. Always choose a stable version (e.g., `4.0.6`) without labels like "SNAPSHOT" or "RC" (Release Candidate).
* **Misconception:** Assuming Java executes standard `main` methods strictly line-by-line in Spring. 
  * *Correction:* While execution starts at `main`, Spring Boot's `SpringApplication.run()` actually scans your project, finds `@RestController` annotations, and builds an internal routing map dynamically behind the scenes.

# 8. Interview Questions

**Q1: What is the difference between an IP Address and a Port Number?**
*Answer:* An IP address uniquely identifies a device on a network, while a Port Number uniquely identifies a specific application running on that device. 

**Q2: What is `localhost`?**
*Answer:* `localhost` is a hostname that translates to the loopback IP address `127.0.0.1`. It is used to establish a network connection to your own machine.

**Q3: How does a Spring Boot application handle web requests without manually installing a server?**
*Answer:* When you include the `spring-boot-starter-web` dependency, Spring Boot automatically provides an embedded Apache Tomcat server. When the application runs, Tomcat starts and listens on port 8080 by default.

**Q4: What is the purpose of `@RestController`?**
*Answer:* It marks the class as a web controller capable of handling incoming HTTP requests and binds the return values of its methods directly to the web response body.

**Q5: What is a reverse proxy?**
*Answer:* A server setup that intercepts external requests (e.g., standard HTTPS requests on port 443) and forwards or maps them to internal application servers running on different ports (e.g., port 8080).

# 9. Active Recall

1. What tool allows us to easily generate the folder structure and dependencies for a new Spring Boot project?
2. What IP address does the word `localhost` represent?
3. If an API request comes to a computer, how does the OS know which background application should process it?
4. What is a "Dependency" in the context of Java?
5. Why should you avoid "Snapshot" versions of Spring Boot?
6. What is the modern standard packaging format for Spring Boot applications: JAR or WAR?
7. What embedded server comes by default when you add the "Spring Web" dependency?
8. What annotation must be placed at the top of a class to tell Spring it contains API endpoints?
9. What annotation maps a specific URL path to a Java method?
10. In which file can you write `server.port=9090` to change the application's port?

# 10. 5-Minute Revision

* **Key Ideas:**
  * Web requests require an **IP address** to find a machine and a **Port Number** to find an application.
  * **Spring Initializr** (`start.spring.io`) is used to bootstrap projects, define metadata, and inject external libraries (Dependencies).
  * Spring Boot takes away manual configuration by providing an **embedded Tomcat server**.
  * Routing HTTP requests to Java code is done declaratively via annotations.
* **Important Terms:**
  * **DNS:** Resolves domain names to IPs.
  * **Reverse Proxy:** Maps external standard ports (443) to internal application ports (8080).
  * **Snapshot:** An unstable, work-in-progress framework version.
  * **RC (Release Candidate):** Almost final, but not perfectly stable version.
* **Rules:**
  * Always use stable Spring Boot releases.
  * Modern apps should be packaged as JAR files, not WAR files.
* **Common Mistakes:** Forgetting that `@RestController` is required to expose API endpoints to the web.
* **Interview Points:** Understand the difference between standard server deployment (installing Tomcat manually) vs. Spring Boot's Embedded Server model.

### End With

**3 Key Takeaways:**
1. Spring Boot severely reduces boilerplate; a fully functioning web server can be written in a single Java class with a few annotations.
2. Networking fundamentals (IPs, Ports, Localhost) are crucial. Your code does not live in a vacuum; it lives on a port (like `8080`) inside a host machine.
3. `@RestController` and `@GetMapping` act as the bridge between incoming HTTP web requests and your Java business logic.

**What this prepares me to learn next:**
Now that you understand how to expose basic text to the web via endpoints, you are prepared to learn **Spring Core (Inversion of Control and Dependency Injection)**, how Spring Boot manages objects behind the scenes, and how to return structured JSON objects instead of plain text strings. You will also look deeper into project management tools like **Maven**.