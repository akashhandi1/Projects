
# Interview Cheat Sheet: Accredit Project

---

### 1. Project Title & Problem Statement

- **Project Title:** Accredit
- **Problem:** Educational institutes often use manual or outdated systems to manage student data, fees, and schedules. This project, Accredit, solves this by providing a modern, centralized web application to automate these academic and administrative tasks simply and efficiently.

---

### 2. Tech Stack

- **Language:** Java (JDK 21)
- **Backend:** Spring Boot 3.2.5 (using Spring MVC, Spring Data JPA, Spring Security)
- **Database:** MySQL
- **Frontend:** JSP (JavaServer Pages), JSTL, HTML, CSS
- **Build Tool:** Apache Maven
- **Server:** Apache Tomcat
- **Libraries:** Apache POI (for Excel), OpenCSV (for CSV), iTextPDF (for PDF)

---

### 3. High-Level Architecture

- The project uses a **Monolithic MVC (Model-View-Controller)** architecture.
- **Model:** These are the data classes (JPA Entities) that map to database tables.
- **View:** These are the JSP pages that the user sees and interacts with.
- **Controller:** These handle user requests, call the business logic, and return the correct view.

**Simple Diagram:**

`User's Browser (JSP)  -->  Spring Controller  -->  Service Layer (Business Logic)  -->  JPA Repository  -->  MySQL Database`

---

### 4. Database Design

- **`users`**: Stores login info for all users.
  - `user_id` (PK), `user_email`, `user_password`
- **`roles`**: Stores user roles (ADMIN, FACULTY, STUDENT).
  - `role_id` (PK), `role_name`
- **`user_roles_mapping`**: Connects users to roles.
  - `user_id` (FK), `role_id` (FK)
- **`student_details`**: Holds student-specific information.
  - `student_id` (PK), `user_id_fk` (FK), `reg_number`
- **`subject`**: Contains all the subjects offered.
  - `subject_id` (PK), `subject_code`, `subject_name`
- **`academic_year`**: Represents academic years.
  - `academic_year_id` (PK), `year`
- **`faculty_subject_mapping`**: Assigns subjects to faculty.
  - `user_id` (FK), `subject_id` (FK), `academic_year_id` (FK)
- **`fee_structure` & `fees_paid`**: Manages fee details and tracks payments.
  - `fee_id` (PK), `student_id` (FK), `amount_paid`

---

### 5. Core Features

1.  **Secure, Role-Based Login:** Users log in and see a dashboard customized for their role (Admin, Faculty, or Student).
2.  **Student and Subject Management:** Admins can add/edit student records, manage academic years, and assign subjects to students.
3.  **Fee Processing:** Admins set up fee structures. The system simulates a payment gateway for students to pay fees.
4.  **Timetable Generation:** Automatically generates course timetables for faculty, handling different subjects and avoiding conflicts.
5.  **Exam Results Management:** Admins can upload exam results, and students can view their results on their dashboard.
6.  **Data Export:** Admins can export data like fee reports or student lists into PDF, Excel, or CSV files.

---

### 6. Code Internals

- **Key Packages:**
    - `config`: Holds the `SecurityConfig` class for all security rules.
    - `controller`: Contains classes like `StudentController` and `TimetableController` that handle user actions.
    - `service`: Includes classes like `TimetableGenerationService` which contains the core logic for features.
    - `repository`: Interfaces like `StudentDetailsRepository` that handle database queries.
    - `util`: Contains helper classes like `PDFUtil.java` and `ExcelUtil.java` for file generation.
- **Data Flow (Example: Fee Payment):**
    1.  **Input:** A student clicks the "Pay Fee" button on a JSP view.
    2.  **Processing:** The `FeePaidController` receives the request. It calls the `FeePaidService` which creates a new payment record and updates the status.
    3.  **Output:** The service returns a success/failure message, and the controller shows a `payment-confirmation.jsp` page to the user.

---

### 7. Your Role & Contributions

- I worked as a **full-stack developer** on this project, with a strong focus on the backend.
- **Backend Development:** I designed and wrote most of the backend, including the controllers, services, and repositories for all the core modules.
- **Feature Implementation:** I was responsible for building several key features from scratch, including:
    - The **Fee Management module**, where I implemented the logic for fee structures and simulated a payment gateway.
    - The **Timetable Generation service**, which involved creating the algorithm to schedule classes without conflicts.
    - The **Exam Results module** and the utility classes for exporting data as PDF, CSV, and Excel files.
- **Database & Security:** I wrote the initial DDL scripts for the database and set up the foundation for the Spring Security configuration.

---

### 8. Challenges & Solutions

1.  **Challenge: Secure Role-Based Access.**
    - **Problem:** We needed to ensure that a student could not access faculty or admin pages.
    - **Solution:** I implemented a `CustomAuthenticationSuccessHandler`. After a user logs in, this handler checks their role and redirects them to the correct dashboard (`/admin`, `/faculty`, or `/student`). This prevents unauthorized access right after login.

2.  **Challenge: Timetable Generation Logic.**
    - **Problem:** Generating a timetable is complex. We had to handle multiple subjects, and faculty availability, and avoid scheduling conflicts.
    - **Solution:** I designed a service (`TimetableGenerationService`) that uses a logical approach to build the schedule. It iterates through time slots, checks for faculty and subject availability, and places subjects in valid slots, effectively preventing clashes.

---

### 9. Impact / Learning Outcomes

- **Impact:** The project is a complete, functional prototype for a college management system that can reduce manual work and improve data accuracy for an educational institution.
- **Learning:**
    - I gained deep expertise in **Spring Boot**, especially Spring Security and Spring Data JPA.
    - I learned how to design and build a full-stack application from the ground up, including database schema design and RESTful API development.
    - I improved my problem-solving skills, particularly in implementing complex business logic like the timetable generator and security handlers.

---

## 🔹 Git-based Contributions (Analysis of My Commits)

- **Overall Role:** My commits show I was the **primary backend developer**. I built the core application logic, database structure, and implemented most of the key features.

- **Main Contributions:**
    - **Foundation:** Established the initial project setup, including dependencies, database scripts, and security configuration.
    - **Core Modules:** Built the `Fee Structure`, `Exam Result`, and `Timetable Generation` modules.
    - **Backend Logic:** Wrote the majority of the `Controllers`, `Services`, and `Repositories`.
    - **Utilities:** Created the file export utilities (PDF, Excel, CSV).

- **Notable Commits:**
    1.  **`ed640ae`**: `Added Payment Gateway Simulation and Util Classes...`
        - This was a major contribution where I added both the fee payment simulation and the file export features, which are significant user-facing functionalities.
    2.  **`30fa875`**: `Updated Timetable Generation Module for Multiple Subjects`
        - This commit shows my work on a complex algorithmic feature, improving it to handle more complex scenarios.
    3.  **`b6eca38`**: `Added Custom Authentication Success Handler`
        - This commit is important because it shows how I solved a key security challenge by implementing a custom, role-based redirection after login.

- **Challenges I Solved (According to Commits):**
    - **`f674da1`**: I fixed UI button alignment, resolved a form submission bug, and patched role-based access issues, showing my ability to debug across the full stack.
    - **`8e55071`**: I updated the timetable logic to handle more complex cases (practical and tutorial sessions), demonstrating my ability to enhance existing complex features.
