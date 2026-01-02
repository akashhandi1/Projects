### **1. Project Overview**

*   **Project Name:** Accredit
*   **Summary:** Accredit is a monolithic web application built with Java and Spring Boot that serves as a comprehensive **College Management System**. It’s designed to automate and streamline the academic and administrative operations of an educational institution. The system provides a centralized platform for managing student information, academic years, subjects, faculty assignments, fee structures, and exam results.
*   **Real-World Problem:** The project addresses the common challenge faced by educational institutions that rely on manual, paper-based, or fragmented software systems. Such methods are often inefficient, prone to errors, and lack a unified view of institutional data. Accredit solves this by providing a single, reliable, and easy-to-use digital platform.
*   **End-Users:** The application is designed for three main types of users, each with a specific role and a tailored user interface:
    *   **Admin:** Has full control over the system. They can manage student and faculty data, define academic structures (like subjects and academic years), handle fee management, and process exam results.
    *   **Faculty:** Can view their assigned subjects and generate their teaching timetables for a specific academic year.
    *   **Student:** Can view their enrolled subjects, check their exam results, and see their fee payment status.

### **2. Tech Stack & Tools**

*   **Programming Language:**
    *   **Java (JDK 21):** The core language used for the backend logic.
*   **Frameworks & Libraries:**
    *   **Spring Boot 3.2.5:** The primary backend framework. It simplifies the creation of stand-alone, production-grade Spring-based applications.
    *   **Spring MVC:** Used to build the web application following the Model-View-Controller design pattern.
    *   **Spring Data JPA:** Simplifies the data access layer by providing repository support for the Java Persistence API (JPA). It makes it easy to interact with the database.
    *   **Spring Security:** Used for handling authentication (login) and authorization (role-based access control), securing the application.
    *   **JSP (JavaServer Pages) & JSTL:** The view technology used to create dynamic web pages. JSPs allow embedding Java code in HTML, making the frontend interactive.
    *   **Apache Tomcat:** The application is packaged as a WAR file and is designed to be deployed on a servlet container like Tomcat.
    *   **MySQL Connector/J:** The JDBC driver used to connect the Java application to a MySQL database.
    *   **Apache POI, OpenCSV, iTextPDF:** Libraries used to implement data export functionality, allowing admins to download reports in Excel, CSV, and PDF formats.
*   **Database:**
    *   **MySQL:** The relational database used to store all the application data.
*   **Build Tool:**
    *   **Apache Maven:** Used for managing project dependencies and building the application.

### **3. File-by-File Explanation**

Here is a detailed breakdown of every important file in your project.

#### **Configuration Files (Root & `resources`)**

*   **`pom.xml`:** This is the heart of the Maven project configuration. It defines the project's dependencies (like Spring Boot, Spring Security, Data JPA, MySQL connector, etc.), plugins, and build settings. It specifies that the project uses Java 21 and is packaged as a WAR file.
*   **`application.properties`:** This file contains the main configuration for the Spring Boot application.
    *   `spring.datasource.url`, `username`, `password`: These properties configure the connection to the MySQL database.
    *   `spring.jpa.hibernate.ddl-auto=validate`: This is an important setting. It tells Hibernate to validate the database schema against the JPA entities at startup, but not to automatically create or update it. This is a safe choice for production to avoid accidental data loss.
    *   `spring.mvc.view.prefix` & `suffix`: These properties tell Spring MVC where to find the JSP view files and what file extension they have.
*   **`interview_cheat_sheet.md` & `project_cheat_sheet.md`:** These are your personal documentation files. They contain a high-level overview of the project, its features, tech stack, and your contributions. They are excellent for quick reference before an interview.
*   **`.gitignore`:** This file specifies which files and directories should be ignored by Git (e.g., `target/`, `.class` files, IDE settings).

#### **Backend: Java Source Code (`src/main/java`)**

The entire backend logic is located in the `com.plangurukul` package, which is further divided into sub-packages based on functionality.

##### **`com.plangurukul.AccreditApplication.java`**

*   **Purpose:** This is the main entry point of your Spring Boot application. The `@SpringBootApplication` annotation enables auto-configuration and component scanning. The `main` method starts the embedded Tomcat server and launches the application.

##### **`config` Package**

This package contains all the Spring Security and global application configuration.

*   **`SecurityConfig.java`:** This is the most important security file. It defines the security rules for your application.
    *   It uses `@EnableWebSecurity` to enable Spring Security's web security support.
    *   The `securityFilterChain` bean configures the HTTP security. It specifies which URLs are public (`/login`, `/css/**`, etc.) and which are protected based on roles (e.g., `/admin/**` requires `ROLE_ADMIN`).
    *   It configures the login form (`.formLogin()`) to use a custom login page at `/login` and a custom success handler.
    *   It also configures the logout functionality and the access-denied page.
*   **`CustomUserDetailsService.java`:** This class implements the `UserDetailsService` interface. Its `loadUserByUsername` method is called by Spring Security during authentication. It fetches the user from the database by email and returns a `UserDetails` object containing the user's credentials and roles.
*   **`CustomUserDetails.java`:** This is a custom implementation of the `UserDetails` interface. It holds the `User` entity and the user's authorities (roles).
*   **`CustomAuthenticationSuccessHandler.java`:** This class is responsible for the logic after a user successfully logs in. It checks the user's role and redirects them to the appropriate dashboard (`/admin/dashboard`, `/student/dashboard`, etc.). This is a key part of your role-based access control.
*   **`GlobalModelAttribute.java`:** This class uses `@ControllerAdvice` to add global attributes to the model for all controllers. It adds the currently logged-in `user` object to the model, so it's available in all JSP pages.

##### **`controller` Package**

This package contains the Spring MVC controllers that handle incoming HTTP requests from the user's browser.

*   **`LoginController.java`:** Handles requests for the login page and the different dashboards.
*   **`StudentController.java`:** Manages all admin-related actions for students, such as adding, viewing, editing, and exporting student details.
*   **`SubjectController.java`:** Handles the creation, viewing, and editing of subjects.
*   **`AcademicYearController.java`:** Manages academic years.
*   **`FeeStructureController.java` & `FeePaidController.java`:** These controllers manage the fee-related functionalities. `FeeStructureController` is for defining fee structures, and `FeePaidController` is for tracking fee payments.
*   **`ExamResultController.java`:** Handles the management of exam results by the admin.
*   **`FacultyAssignmentController.java`:** Allows the admin to assign subjects to faculty members.
*   **`TimetableController.java`:** This controller is for the faculty to view their generated timetable.
*   **`APIController.java`:** This is a `@RestController` that exposes a REST API endpoint. It's used by the frontend to fetch subjects for a given academic year dynamically using AJAX.
*   **`RoleSelectionController.java`:** Handles the logic for users who have multiple roles, allowing them to select which role they want to use for the current session.
*   **`EnrolledSubjectController.java`, `StudentExamResultController.java`, `StudentFeePaidController.java`:** These controllers are for the student-facing pages, allowing them to view their enrolled subjects, exam results, and fee details.

##### **`entity` Package**

This package contains the JPA entity classes, which are Java objects that map to tables in your MySQL database.

*   **`User.java`:** Maps to the `users` table. Stores login credentials and basic user information.
*   **`Roles.java`:** Maps to the `roles` table (ADMIN, FACULTY, STUDENT).
*   **`UserRolesMapping.java`:** Maps to the `user_roles_mapping` table, creating a many-to-many relationship between users and roles.
*   **`StudentDetails.java`:** Maps to the `student_details` table.
*   **`Subject.java`:** Maps to the `subject` table.
*   **`AcademicYear.java`:** Maps to the `academic_year` table.
*   **`FeeStructure.java` & `FeePaid.java`:** Map to the `fee_structure` and `fees_paid` tables respectively.
*   **`ExamResult.java`:** Maps to the `exam_result` table.
*   **`FacultySubjectMapping.java`:** Maps to the `faculty_subject_mapping` table.
*   **`FacultyTimetable.java`:** Maps to the `faculty_timetable` table and stores the generated timetable as a JSON string.

##### **`repository` Package**

This package contains the Spring Data JPA repositories. These are interfaces that extend `JpaRepository` and provide methods for CRUD (Create, Read, Update, Delete) operations on the entities.

*   Each entity has a corresponding repository (e.g., `UserRepository`, `StudentDetailsRepository`).
*   You have also defined custom query methods in some repositories (e.g., `findByEmail` in `UserRepository`).

##### **`service` Package**

This package contains the service classes, which hold the core business logic of your application.

*   **`TimetableGenerationService.java`:** This is one of the most complex services. It contains the algorithm for generating a conflict-free timetable for a faculty member based on their assigned subjects and contact hours.
*   **`FeePaidService.java`:** Contains logic to initialize fee payment records for students and update them.
*   **`ExamResultService.java`:** Manages the business logic for exam results, including initializing results for students.
*   Other services like `StudentDetailsService`, `SubjectService`, etc., act as a bridge between the controllers and the repositories, handling the main business logic for their respective domains.

##### **`util` Package**

This package contains utility classes that provide reusable functions.

*   **`CSVUtil.java`, `ExcelUtil.java`, `PDFUtil.java`:** These classes contain the logic for exporting data into CSV, Excel, and PDF formats, respectively. They are used by the controllers to provide data export functionality to the admin.
*   **`TimetableSlot.java`:** A helper class that defines the time slots and days for the timetable generation.

#### **Frontend: JSP Files (`src/main/webapp/WEB-INF/views`)**

This directory contains all the JSP files that make up the user interface of your application.

*   **`login.jsp`:** The login page for all users.
*   **`select-role.jsp`:** The page shown to users with multiple roles to let them choose one.
*   **`access-denied.jsp`:** The page shown to users when they try to access a resource they are not authorized to view.
*   **`sidebar.jsp`:** A reusable sidebar component that shows different navigation links based on the user's role.
*   **`admin/` directory:** Contains all the JSP pages for the admin dashboard, including pages for managing students, subjects, fees, etc.
*   **`faculty/` directory:** Contains the JSP pages for the faculty dashboard and timetable view.
*   **`student/` directory:** Contains the JSP pages for the student dashboard, where they can view their subjects, results, and fees.
*   **`includes/` directory:** Contains reusable JSP fragments like `links.jsp` (for CSS links) and `script.jsp` (for JavaScript files).

### **4. System Architecture (High-Level)**

The project follows a classic **Monolithic, Layered MVC (Model-View-Controller)** architecture.

**Text-Based Diagram:**

```
+----------------+      +---------------------+      +---------------------+      +--------------------+      +----------------+
| User's Browser |----->| Spring Dispatcher   |----->|     Controllers     |----->|      Services      |----->|   Repositories   |----->| MySQL Database |
|   (JSP View)   |      | (Servlet)           |      | (Handle HTTP Req)   |      | (Business Logic)   |      | (Spring Data JPA)|      | (Data Storage) |
+----------------+      +---------------------+      +---------------------+      +--------------------+      +----------------+
        ^                         |                            |                             |
        | (Rendered HTML)         | (ModelAndView)             | (Entities/DTOs)             | (JPA Entities)
        |                         v                            v                             v
        +-----------------------[ View Resolver ]<-------------+-----------------------------+
```

**Data Flow:**

1.  **Input:** A user interacts with a JSP page in their browser (e.g., clicks a "Save Student" button).
2.  **Request:** The browser sends an HTTP request to the Spring Boot application.
3.  **DispatcherServlet:** Spring's `DispatcherServlet` receives the request and, based on the URL, routes it to the appropriate method in a `@Controller` class (e.g., `StudentController`).
4.  **Controller:** The controller method processes the request, calls a `@Service` class (e.g., `StudentDetailsService`) to perform the business logic.
5.  **Service:** The service class contains the core logic. It might call one or more `@Repository` interfaces to interact with the database.
6.  **Repository:** The Spring Data JPA repository translates the service call into a database query (e.g., saving a `StudentDetails` entity).
7.  **Database:** The data is persisted in the MySQL database.
8.  **Response:** The service returns a result to the controller. The controller then returns a `ModelAndView` object, which tells Spring MVC which JSP page to render.
9.  **View:** The JSP page is rendered as HTML and sent back to the user's browser.

### **5. Database Design**

The database schema is well-structured and uses foreign keys to maintain relational integrity. The `ddl.sql` script defines the entire schema.

*   **Tables:**
    *   `users`: Stores user credentials. `user_id` is the primary key.
    *   `roles`: Stores the available roles. `role_id` is the primary key.
    *   `user_roles_mapping`: A join table for the many-to-many relationship between `users` and `roles`.
    *   `student_details`: Stores student-specific data. `student_id` is the primary key, and `user_id_fk` is a foreign key to the `users` table.
    *   `academic_year`: Stores academic years. `academic_year_id` is the primary key.
    *   `subject`: Stores course information. `subject_id` is the primary key.
    *   `academic_year_subject`: A join table for the many-to-many relationship between `academic_year` and `subject`.
    *   `faculty_subject_mapping`: Assigns subjects to faculty for a specific academic year.
    *   `fee_structure`: Defines the fee for an academic year.
    *   `fees_paid`: Tracks student fee payments.
    *   `exam_result`: Stores student exam results.
    *   `faculty_timetable`: Stores the generated timetables for faculty as JSON.
*   **Relationships:**
    *   **One-to-Many:** A `User` can have multiple `UserRolesMapping`.
    *   **Many-to-One:** A `StudentDetails` record is associated with one `User`.
    *   **Many-to-Many:** `users` and `roles` (via `user_roles_mapping`), and `academic_year` and `subject` (via `academic_year_subject`).
*   **ORM:** The project uses **Hibernate** as the JPA provider. The `@Entity` classes in the `entity` package are the Object-Relational Mapping (ORM) models.

### **6. Core Features**

1.  **Role-Based Authentication & Authorization:**
    *   **Files:** `SecurityConfig.java`, `CustomUserDetailsService.java`, `CustomAuthenticationSuccessHandler.java`, `login.jsp`.
    *   **Flow:** Users log in via the `/login` page. Spring Security validates their credentials against the `users` table. The `CustomAuthenticationSuccessHandler` then redirects them to their role-specific dashboard.
2.  **Student Management (Admin):**
    *   **Files:** `StudentController.java`, `StudentDetailsService.java`, `StudentDetailsRepository.java`, `admin/student/` JSPs.
    *   **Flow:** The admin can create, view, update, and delete student records. The `StudentController` handles the web requests and calls the `StudentDetailsService` to perform the database operations.
3.  **Fee Management:**
    *   **Files:** `FeeStructureController.java`, `FeePaidController.java`, `FeeStructureService.java`, `FeePaidService.java`, `admin/fee_structure/` and `admin/feepaid/` JSPs.
    *   **Flow:** The admin defines a fee structure for an academic year. The system automatically creates `FeePaid` records for all students in that year. The admin can then simulate making a payment for a student.
4.  **Timetable Generation (Faculty):**
    *   **Files:** `TimetableController.java`, `TimetableGenerationService.java`, `FacultyTimetableRepository.java`, `faculty/table-view.jsp`.
    *   **Flow:** A faculty member selects an academic year. The `TimetableController` calls the `TimetableGenerationService`. This service fetches the faculty's assigned subjects, and then uses a logical algorithm to place them into available time slots, avoiding conflicts. The generated timetable is saved as JSON in the `faculty_timetable` table and displayed to the faculty.
5.  **Data Export (Admin):**
    *   **Files:** `CSVUtil.java`, `ExcelUtil.java`, `PDFUtil.java`. These are called from various controllers like `StudentController`, `SubjectController`, etc.
    *   **Flow:** The admin clicks an "Export" button. The corresponding controller method fetches the data from the database, prepares it, and then calls one of the utility classes to generate the file and stream it back to the user for download.

### **7. API Endpoints**

The project has one main REST API endpoint.

*   **Endpoint:** `GET /admin/api/subjects-by-year/{academicYearId}`
    *   **Method:** `GET`
    *   **Path:** `/admin/api/subjects-by-year/{academicYearId}`
    *   **Input:** `academicYearId` (as a path variable).
    *   **Output:** A JSON array of `Subject` objects.
    *   **Code Handler:** `APIController.java`, in the `getSubjectsByYear` method.
    *   **Purpose:** This endpoint is used by the "Assign Subjects to Faculty" page. When the admin selects an academic year from a dropdown, an AJAX call is made to this endpoint to dynamically load the subjects for that year without reloading the entire page.

### **8. Frontend**

The frontend is built using **JSP (JavaServer Pages)** and styled with **Bootstrap**.

*   **Major Pages/Components:**
    *   **Login Page (`login.jsp`):** A clean and simple login form.
    *   **Dashboards (`admin/dashboard.jsp`, `faculty/dashboard.jsp`, `student/dashboard.jsp`):** Each role has a dedicated dashboard that shows relevant summary information (e.g., total students for the admin, enrolled courses for the student).
    *   **Data Tables:** The admin-facing pages (like student details, subject details) use the **DataTables** jQuery plugin to provide sortable, searchable, and paginated tables, which makes managing large amounts of data easy.
    *   **Forms:** The "add" and "edit" pages use standard HTML forms for data entry.
    *   **Sidebar (`sidebar.jsp`):** A reusable component that provides navigation. It dynamically shows different links based on the user's role, which is a great example of how you've used JSTL and Spring Security integration in the view layer.

### **9. Tests**

The project structure includes a `src/test/java` directory, which is the standard location for test files in a Maven project. However, based on the file listing, this directory is currently empty. This means the project **does not have any automated unit or integration tests**.

*   **Testing Framework:** Although no tests are present, the `pom.xml` includes a dependency for **JUnit**, which is the standard testing framework for Java. This indicates that the project is set up for testing, but no tests have been written yet.

### **10. Git Contributions**

To analyze your Git contributions, I would need to run `git log`. Since I cannot execute shell commands in this environment, I am providing a summary based on the `interview_cheat_sheet.md` file, which you should verify against your actual commit history.

*   **Summary of Contributions:** Based on your notes, you were the **primary full-stack developer** for this project.
*   **Key Contributions:**
    *   You designed the backend architecture, including the controllers, services, and repositories.
    *   You implemented several core features from scratch, most notably the **Fee Management module** and the **Timetable Generation service**.
    *   You created the data export utilities (PDF, Excel, CSV).
    *   You set up the database schema and the Spring Security configuration.
*   **Notable Commits (from your cheat sheet):**
    *   `ed640ae`: Added payment gateway simulation and util classes. This is a significant feature.
    *   `30fa875`: Updated the timetable generation module, showing your ability to work on complex algorithms.
    *   `b6eca38`: Added the custom authentication success handler, which is a key security feature.

### **11. Challenges & Solutions**

1.  **Challenge: Implementing Secure, Role-Based Access.**
    *   **Problem:** The application needed to strictly enforce that users could only access pages and data relevant to their role (e.g., a student should not be able to access admin functions).
    *   **Solution:** You solved this by deeply integrating **Spring Security**.
        *   You used `antMatchers` in `SecurityConfig.java` to restrict URL patterns by role.
        *   You created a `CustomAuthenticationSuccessHandler` to redirect users to the correct dashboard after login, which is a very clean and effective solution.
        *   In the frontend, you used JSTL `c:if` tags in `sidebar.jsp` to conditionally render navigation links based on the user's role, preventing them from even seeing links to pages they can't access.
2.  **Challenge: Complex Timetable Generation Logic.**
    *   **Problem:** Generating a valid timetable is a classic computer science problem. It requires scheduling subjects while respecting constraints like faculty availability, contact hours, and avoiding conflicts.
    *   **Solution:** You created a dedicated `TimetableGenerationService`. This service uses a logical, iterative approach. It shuffles the available time slots to ensure randomness and then iterates through them, placing subjects in valid slots while keeping track of already scheduled classes. This is a good, practical solution to a complex problem.
3.  **Challenge: Managing Data Integrity Across Multiple Tables.**
    *   **Problem:** Many operations in the application involve multiple database tables. For example, when adding a new student, you need to create a `User` record and a `StudentDetails` record. If one of these operations failed, you could end up with inconsistent data.
    *   **Solution:** While not explicitly shown in the code with `@Transactional` annotations on the service methods, the standard Spring Data JPA behavior when calling `save` within a single service method provides a level of atomicity. For a more robust solution in a real-world scenario, you would add `@Transactional` to your service methods (e.g., `addStudent` in `StudentDetailsService`) to ensure that all database operations within that method either succeed together or fail together.

### **12. Impact & Learning Outcomes**

*   **What the project demonstrates:** This project is an excellent demonstration of your ability to build a complete, full-stack web application using the Spring ecosystem. It showcases your skills in backend development, database design, security implementation, and frontend integration. The complexity of features like timetable generation and role-based access control shows that you can handle non-trivial business logic.
*   **Skills and Technologies Practiced:**
    *   **Backend:** Java, Spring Boot, Spring MVC, Spring Data JPA, Spring Security.
    *   **Database:** MySQL, relational database design, writing DDL scripts.
    *   **Frontend:** JSP, JSTL, HTML, CSS, Bootstrap, and some JavaScript for dynamic behavior.
    *   **Architecture:** Monolithic, Layered MVC architecture.
    *   **Problem Solving:** Implementing complex algorithms (timetable generation) and security workflows.
*   **Real-World Application:** This project is a very practical and relevant example of a real-world business application. The skills and concepts used here are directly applicable to a wide range of enterprise software development roles. You can confidently talk about this project as a complete system that solves a real business problem.
