### FinWise: Interview Cheat Sheet

---

#### 1. **Project Title & Problem Statement**

*   **Project Title:** FinWise
*   **Problem Statement:** FinWise is a personal finance management application for families. It helps users plan and save for major life goals, such as their children's education and marriage. The app provides a clear view of their financial health and offers predictions to help them stay on track.

#### 2. **Tech Stack**

*   **Frontend:** React.js, Bootstrap, Axios
*   **Backend:** Java, Spring Boot (v3.2.5), Spring Data JPA, Spring Security
*   **ML/AI:** Python, Flask, Scikit-learn, Pandas
*   **Database:** MySQL
*   **Tools & APIs:** Maven, npm, Git, REST APIs

#### 3. **High-Level Architecture**

The project follows a microservices-oriented architecture.

`Frontend (React.js)` → `Backend API (Spring Boot)` → `Database (MySQL)`
`↑`
`ML Service (Flask)`

*   The **Frontend** is a single-page application that users interact with.
*   The **Backend API** handles all business logic, data storage, and user authentication.
*   The **ML Service** provides predictions for financial goals based on user data.

#### 4. **Database Design**

*   **users:** `user_id` (PK), `name`, `email`, `password`
*   **child:** `child_id` (PK), `user_id` (FK), `name`, `age`
*   **family_profile:** `profile_id` (PK), `user_id` (FK), `number_of_members`
*   **education_plan:** `plan_id` (PK), `child_id` (FK), `target_amount`, `current_amount`, `time_to_college`
*   **marriage_plan:** `plan_id` (PK), `child_id` (FK), `target_amount`, `current_amount`, `years_to_marriage`
*   **investment_plan:** `plan_id` (PK), `user_id` (FK), `investment_type`, `amount`, `expected_return`
*   **financial_transaction:** `transaction_id` (PK), `user_id` (FK), `amount`, `type` (income/expense), `date`

*(PK = Primary Key, FK = Foreign Key)*

#### 5. **Core Features**

*   **User Authentication:** Secure user registration and login system using Spring Security.
*   **Family & Child Management:** Users can create a family profile and add details for each child, which links to their financial goals.
*   **Education & Marriage Planning:** Create dedicated savings plans for a child's future education and marriage costs. The system tracks progress and shows shortfalls.
*   **Investment Tracking:** Allows users to add and monitor various investment plans, contributing to their overall financial picture.
*   **Financial Goal Prediction:** An ML model predicts the future value of savings and advises on the monthly contributions needed to reach goals.
*   **Dashboard & Visualization:** A central dashboard displays key metrics, recent activities, and progress towards all financial goals.

#### 6. **Code Internals**

*   **Data Flow (Example: Creating an Education Plan):**
    1.  **Input:** User fills a form in the `EducationPage.js` (React component).
    2.  **Processing:** An API call is made to `EducationPlanController.java`. The controller passes the data to `EducationPlanService.java`, which contains the business logic. The service creates an `EducationPlan` entity and saves it to the database via `EducationPlanRepository.java`.
    3.  **Output:** The new plan is returned to the frontend and displayed on the user's dashboard.
*   **Key Algorithm (Prediction Logic):**
    *   The `PredictionController.java` in the backend calls the Flask API (`app.py`).
    *   The Flask service uses a pre-trained machine learning model (`priority_model.pkl`) and a scaler (`scaler.pkl`) to predict the required savings based on inputs like target amount, income, and time horizon.

#### 7. **Your Role & Contributions**

*   You acted as a **full-stack developer** with a focus on the **backend and ML integration**.
*   **Detailed Contribution 1 (Backend API Development):** You likely designed and implemented the core REST APIs using Spring Boot. This includes creating controllers, services, and repositories for features like Education Planning, Marriage Planning, and User Management. You defined the data transfer objects (DTOs) and mapped them to the JPA entities.
*   **Detailed Contribution 2 (Database Schema Design):** You designed the MySQL database schema. You created the tables, defined the relationships using primary and foreign keys, and wrote the initial DDL (Data Definition Language) scripts.

#### 8. **Challenges & Solutions**

*   **Challenge 1: Integrating Separate Services:** Connecting the React frontend, Spring Boot backend, and Python Flask API was complex, especially handling Cross-Origin Resource Sharing (CORS) and ensuring smooth data flow.
    *   **Solution:** Implemented CORS configuration in both the Spring Boot and Flask applications. We established a clear API contract (JSON format) for requests and responses between services.
*   **Challenge 2: User Authentication & State Management:** Managing user login state across the frontend and securing backend endpoints.
    *   **Solution:** Implemented a session-based authentication mechanism in Spring Security. On the frontend, user details and login status were managed in the React context after a successful login, and the session was cleared on logout.

#### 9. **Impact / Learning Outcomes**

*   **End Result:** The project is a functional prototype of a comprehensive financial planning tool that can genuinely help families manage their long-term savings goals.
*   **Learning Outcomes:** You gained hands-on experience in full-stack development, microservices architecture, and MLOps. You learned to build and connect applications using React, Spring Boot, and Flask, and you deepened your understanding of database design and REST API security.
*   **Real-World Usefulness:** This application serves as a strong foundation for a commercial FinTech product. It can be extended with more features like budget tracking, loan management, and automated investment advice.

---

### 🔹 Git-based Contributions

#### 1. **Overall Role**

Your commit history shows you were the **primary contributor and integrator** for the project. You worked across the entire stack—backend, frontend, and the ML service—with a significant focus on building the backend infrastructure and integrating the different components.

#### 2. **Main Contributions**

*   **Backend Foundation:** You initialized the Spring Boot application and set up all the core backend modules, including entities, repositories, services, and controllers. (Commit `a2d125b`, `1bf6680`)
*   **Authentication & Security:** You implemented the initial user authentication and security configuration. You later refactored it from OAuth2 to a custom username/password system. (Commit `9df9373`, `7a2310b`)
*   **Core Feature Modules:** You wrote the code for the Education and Marriage planning modules, both on the frontend and backend. (Commit `2615779`)
*   **ML Integration:** You created the Flask API and integrated the ML prediction model with the Spring Boot backend. (Commit `f48d0d0`, `620b5f6`)
*   **Frontend Development:** You made substantial contributions to the React frontend, creating pages for Education, Marriage, and Investment, and setting up the navigation and dashboard.

#### 3. **Notable Commits**

*   **Commit `1bf6680` (Added Services and Controllers):** This was a massive backend contribution where you created almost all the service and controller classes, effectively building the entire business logic layer of the application in one go.
*   **Commit `55a7071` (Adjusted the Flow):** This commit shows significant refactoring work. You restructured the frontend by breaking down components (`FamilyDetails.jsx`), created a dynamic navigation bar, and adjusted the backend flow, demonstrating your ability to improve and organize a growing codebase.
*   **Commit `620b5f6` (Major Changes):** This highlights your work on integrating the ML model. You created new services, updated the Flask API, and made significant changes to the frontend pages to consume and display the prediction results.

#### 4. **Challenges I Solved (as per commits)**

*   **Refactoring Authentication:** The commit `7a2310b` ("removed google authentication") shows you made a major architectural decision to replace a previous authentication method with a custom solution, likely to gain more control over the user model. This involved heavy changes in Spring Security and creating new components for Login/Registration in React.
*   **Fixing and Refactoring Investment Feature:** Commits by a teammate introduced an `Investment` feature. You later refactored this into `InvestmentPlan` (Commit `3ab5a7e`), cleaning up the code, removing redundant classes, and making the implementation more robust and consistent with the rest of the application.
