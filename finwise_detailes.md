### **1. Project Overview**

*   **Project Name:** FinWise
*   **Summary:** FinWise is a personal finance management application designed for families. It helps users plan and save for significant life events, specifically their children's education and marriage. The application provides a clear overview of their financial health and uses machine learning to offer predictions, helping them stay on track with their financial goals.
*   **Real-World Problem:** Many families struggle with long-term financial planning due to a lack of accessible tools. FinWise addresses this by providing a centralized platform to manage savings, track progress, and receive data-driven advice, making it easier to prepare for future expenses.
*   **End-Users:** The primary end-users are parents or heads of households who are responsible for managing family finances and planning for their children's futures.

### **2. Tech Stack & Tools**

*   **Backend:**
    *   **Language:** Java (Version 21)
    *   **Framework:** Spring Boot (Version 3.4.5)
    *   **Libraries:** Spring Data JPA (for database interaction), Spring Security (for authentication), Lombok (to reduce boilerplate code), ModelMapper (for object mapping).
    *   **Database:** MySQL
    *   **Build Tool:** Maven
*   **Frontend:**
    *   **Language:** JavaScript
    *   **Framework:** React.js
    *   **Libraries:** Axios (for API requests), React Router (for navigation), Tailwind CSS (for styling), `@testing-library/jest-dom` (for testing).
*   **Machine Learning Service:**
    *   **Language:** Python
    *   **Framework:** Flask
    *   **Libraries:** Scikit-learn (for the ML model), Pandas & NumPy (for data manipulation), Gunicorn (as a production web server).
*   **General Tools:**
    *   **Version Control:** Git
    *   **API Style:** REST

### **3. File-by-File Explanation (Backend)**

The backend is a standard Spring Boot application, structured into several packages, each with a distinct responsibility.

#### **`com.finwise` (Root Package)**

*   **`FinWiseApplication.java`**
    *   **Purpose:** This is the main entry point of the Spring Boot application.
    *   **Logic:** The `@SpringBootApplication` annotation enables auto-configuration, component scanning, and other Spring Boot features. The `main` method starts the embedded Tomcat server, launching the entire backend application. The `@EnableScheduling` annotation enables the execution of scheduled tasks, like the `MonthlySavingsUpdater`.

#### **`com.finwise.config` (Configuration)**

This package contains the security and other bean configurations for the application.

*   **`SecurityConfig.java`**
    *   **Purpose:** This is the central class for configuring Spring Security.
    *   **Logic:**
        *   It defines a `SecurityFilterChain` bean that configures the application's security rules.
        *   It specifies public URLs (like `/api/auth/register`, `/api/auth/login`) that can be accessed without authentication.
        *   All other API endpoints under `/api/**` require authentication.
        *   It disables CSRF (Cross-Site Request Forgery) protection, which is common for REST APIs consumed by a separate frontend.
        *   It configures CORS (Cross-Origin Resource Sharing) to allow requests from the frontend application running on `http://localhost:3000`.
        *   It sets up a `DaoAuthenticationProvider` to use our custom `UserDetailsService` and a `PasswordEncoder` for authenticating users.
        *   It defines a `PasswordEncoder` bean that uses `BCryptPasswordEncoder` to securely hash and verify user passwords.
*   **`CustomUserDetailsService.java`**
    *   **Purpose:** This service class is responsible for loading user-specific data for Spring Security.
    *   **Logic:** It implements the `UserDetailsService` interface. The `loadUserByUsername` method is called by Spring Security during authentication. It fetches a `User` from the `UserRepository` by their email and wraps it in a `CustomUserDetails` object.
*   **`CustomUserDetails.java`**
    *   **Purpose:** This class is a wrapper around the `User` entity to provide user details to Spring Security in a format it understands.
    *   **Logic:** It implements the `UserDetails` interface. It provides methods to get the user's username (using email), password, and authorities (roles).
*   **`CustomAuthenticationSuccessHandler.java`**
    *   **Purpose:** This handler is executed after a user successfully authenticates.
    *   **Logic:** It implements the `AuthenticationSuccessHandler` interface. The `onAuthenticationSuccess` method redirects the user to the frontend's callback URL (`http://localhost:3000/auth/callback`) after a successful login.
*   **`MapperConfig.java`**
    *   **Purpose:** This configuration class defines beans for `ModelMapper` and `RestTemplate`.
    *   **Logic:**
        *   It creates a `ModelMapper` bean, which is used throughout the application to map data between DTOs (Data Transfer Objects) and JPA Entities.
        *   It creates a `RestTemplate` bean, which is used to make HTTP requests to other services, such as the Python ML API.

#### **`com.finwise.controller` (API Endpoints)**

This package contains the REST controllers that define the API endpoints of the application.

*   **`UserController.java`**: Handles user registration, login, logout, and fetching the current user's details.
*   **`FamilyProfileController.java`**: Manages the creation, retrieval, and updating of family profiles.
*   **`ChildController.java`**: Provides CRUD (Create, Read, Update, Delete) operations for children associated with a family profile.
*   **`EducationPlanController.java`**: Manages education savings plans for children.
*   **`MarriagePlanController.java`**: Manages marriage savings plans.
*   **`InvestmentPlanController.java`**: Handles investment plans.
*   **`FinancialTransactionController.java`**: Manages financial transactions like deposits and withdrawals.
*   **`PredictionController.java`**: Triggers the ML model to generate predictions for financial goals.
*   **`InvestmentOptionController.java`**: Fetches investment options from an external API or provides default options.
*   **Other Controllers**: `EconomicIndicatorController`, `EducationCostReferenceController`, `MarriageCostReferenceController`, `NotificationController`, `ReportController`, `SavingsPlanController` provide CRUD operations for their respective entities.

#### **`com.finwise.dto` (Data Transfer Objects)**

This package contains DTOs, which are plain Java objects used to transfer data between the client (frontend) and the server (backend). They help to decouple the API layer from the database layer.

*   **`UserDTO.java`**: Carries user registration and profile data.
*   **`FamilyProfileDTO.java`**: Represents a family's financial profile.
*   **`ChildDTO.java`**: Carries data for creating and updating a child's information.
*   **`EducationPlanDTO.java`, `MarriagePlanDTO.java`, `InvestmentPlanDTO.java`**: Represent the different types of financial plans.
*   **`FinancialTransactionDTO.java`**: Represents a single financial transaction.
*   **Other DTOs**: Each DTO corresponds to an entity and is used for data transfer in the API.

#### **`com.finwise.entity` (JPA Entities)**

This package contains the JPA entity classes that are mapped to the database tables.

*   **`User.java`**: Mapped to the `users` table. Contains user details and relationships to other entities.
*   **`FamilyProfile.java`**: Mapped to the `family_profiles` table. Has a one-to-one relationship with `User` and one-to-many relationships with `Child`, `MarriagePlan`, etc.
*   **`Child.java`**: Mapped to the `children` table. Has a many-to-one relationship with `FamilyProfile`.
*   **`EducationPlan.java`, `MarriagePlan.java`, `InvestmentPlan.java`**: Mapped to their respective tables. These entities contain the core data for the financial plans.
*   **Other Entities**: Each entity class corresponds to a table in the database schema defined in `ddl.sql`.

#### **`com.finwise.repository` (Data Access Layer)**

This package contains the Spring Data JPA repositories. These interfaces provide methods for performing CRUD operations on the database without writing boilerplate code.

*   **`UserRepository.java`**: Extends `JpaRepository` for the `User` entity. Includes custom methods like `findByEmail` and `existsByEmail`.
*   **`FamilyProfileRepository.java`**: For the `FamilyProfile` entity.
*   **`EducationPlanRepository.java`, `MarriagePlanRepository.java`, etc.**: Each repository corresponds to an entity and provides database access methods for that entity.

#### **`com.finwise.service` (Business Logic)**

This package contains the service classes, which hold the core business logic of the application.

*   **`UserService.java`**: Handles the logic for user registration, ensuring that new users are saved correctly with an encrypted password.
*   **`FamilyProfileService.java`**: Manages the business logic for family profiles.
*   **`EducationPlanService.java`, `MarriagePlanService.java`, etc.**: These services contain the business logic for creating, updating, and managing the various financial plans. They interact with the repositories to persist data.
*   **`PredictionService.java`**: This service is responsible for calling the external Python/Flask ML API to get predictions. It constructs the request, sends it via `RestTemplate`, and processes the response.
*   **`InvestmentOptionsService.java`**: This service calls an external API (`magicloops.dev`) to get investment options based on the user's risk profile. It includes a fallback to a default list of options if the API call fails.
*   **`MonthlySavingsUpdater.java`**: This service contains a scheduled task (`@Scheduled`) that runs on the first day of every month to automatically add the `monthlyContribution` to the `currentSavings` of all education and marriage plans.

#### **`com.finwise.exception` (Custom Exceptions)**

*   **`ResourceNotFoundException.java`**: A custom exception thrown when a requested resource (like a user or a plan) is not found in the database.
*   **`UserAlreadyExistsException.java`**: A custom exception thrown during registration if a user with the given email or username already exists.

#### **`com.finwise.util` (Utility Classes)**

*   **`Util.java`**: A utility class with helper methods to get the currently authenticated user from the `SecurityContextHolder`. This is used in controllers and services to get the user who is making the request.

### **3. File-by-File Explanation (Frontend)**

The frontend is a single-page application (SPA) built with React. It uses `react-router-dom` for navigation and `axios` for making API calls to the backend. The styling is done using Tailwind CSS.

#### **`src/` (Root Frontend Source)**

*   **`index.js`**
    *   **Purpose:** This is the entry point for the React application.
    *   **Logic:** It renders the main `App` component into the `root` DOM element in `public/index.html`. It also wraps the `App` component in `React.StrictMode` to highlight potential problems in the application.
*   **`App.js`**
    *   **Purpose:** This is the main component that defines the application's routing.
    *   **Logic:** It uses `react-router-dom` to set up the different routes of the application. Each route is mapped to a specific component. For example, the `/dashboard` route renders the `Dashboard` component, and the `/login` route renders the `Login` component.
*   **`App.css` & `index.css`**
    *   **Purpose:** These files are for styling. `App.css` includes the Tailwind CSS directives, and `index.css` contains some base styling for the application.

#### **`src/components/` (React Components)**

This directory contains the reusable UI components of the application.

*   **`landing.jsx`**
    *   **Purpose:** This is the landing page of the application, which is the first thing a new user sees.
    *   **Logic:** It's a static page that describes the features of FinWise and has buttons for logging in and signing up. It serves as the marketing and entry page for the application.
*   **`Login.jsx` & `Registration.jsx`**
    *   **Purpose:** These components provide the user authentication forms.
    *   **Logic:**
        *   They manage the form state (email, password, etc.).
        *   On form submission, they make API calls to the backend's `/api/auth/login` and `/api/auth/register` endpoints using `axios`.
        *   They handle loading states and display any errors returned from the backend.
        *   Upon successful login or registration, they redirect the user to the `AuthCallback` component.
*   **`AuthCallback.js`**
    *   **Purpose:** This component handles the callback after a successful login or registration.
    *   **Logic:**
        *   It runs in a `useEffect` hook after the component mounts.
        *   It makes a request to the backend's `/api/auth/user` endpoint to get the authenticated user's data.
        *   It stores the user's information (ID, email, name, etc.) in the browser's `localStorage`.
        *   It checks the `isNewUser` flag from the user data. If the user is new, it redirects them to the `/family-details` page to complete their profile. Otherwise, it redirects them to the `/dashboard`.
*   **`Dashboard.jsx`**
    *   **Purpose:** This is the main dashboard that users see after logging in.
    *   **Logic:**
        *   It fetches the user's financial data from the backend, including their family profile, savings, and expenses.
        *   It displays a summary of the user's financial health in a series of cards.
        *   It includes placeholders for charts and financial goals.
*   **`DynamicNavbar.jsx`**
    *   **Purpose:** This is a responsive navigation bar that is displayed on most pages after the user logs in.
    *   **Logic:**
        *   It fetches the user's profile and family information to display the user's name and other details.
        *   It includes navigation links to the different pages of the application (Dashboard, Education, Marriage, etc.).
        *   It has a dropdown menu for user-specific actions like viewing the profile, editing family details, and logging out.
        *   It also displays notifications for the user.
*   **`FamilyProfile/FamilyDetails.jsx`**
    *   **Purpose:** This component provides a form for new users to enter their family's financial details.
    *   **Logic:**
        *   It collects information like family size, monthly income, expenses, and location.
        *   On submission, it sends a `POST` request to the `/api/familyProfile` endpoint to create the family profile.
        *   After successful submission, it redirects the user to the dashboard via the `AuthCallback` component.
*   **`Education/EducationPage.js`, `Marriage/MarriagePage.js`, `Investment/InvestmentPage.js`**
    *   **Purpose:** These are the main pages for managing the different types of financial plans.
    *   **Logic:**
        *   Each page fetches the relevant plans for the user's family from the backend.
        *   They display the plans in a list of cards, showing the progress and other details for each plan.
        *   They include buttons to add, edit, and delete plans, which open a modal form.
        *   The forms in the modals make API calls to the backend to perform the CRUD operations.
        *   They also include a feature to trigger the ML prediction service to get updated recommendations.
*   **`Investment/InvestmentOptionsPage.js`**
    *   **Purpose:** This page displays a list of recommended investment options for the user.
    *   **Logic:**
        *   It calls the `/api/investment-options/{familyProfileId}` endpoint on the backend.
        *   The backend, in turn, calls an external API to get personalized investment options based on the user's risk tolerance.
        *   It displays the options in a grid of cards, with links to the investment provider's website.
*   **`util/userService.js`**
    *   **Purpose:** This is a utility file that provides helper functions for managing user data stored in `localStorage`.
    *   **Logic:** It has functions to get the user's data, check if the user is authenticated, get specific user properties, and clear the user data on logout. This centralizes the logic for interacting with `localStorage`.

### **3. File-by-File Explanation (ML API)**

The ML API is a Python-based service built with Flask. Its primary role is to provide intelligent recommendations for allocating savings across different financial goals.

*   **`requirements.txt`**
    *   **Purpose:** This file lists all the Python dependencies required for the ML API to run.
    *   **Libraries:**
        *   **`Flask`**: A micro web framework for building the REST API.
        *   **`scikit-learn`**: Used for the machine learning model (`RandomForestRegressor`) and data preprocessing (`StandardScaler`).
        *   **`pandas`** & **`numpy`**: Used for data manipulation and numerical operations, especially for generating synthetic data.
        *   **`joblib`**: Used for saving and loading the trained machine learning model (`.pkl` files).
        *   **`gunicorn`**: A production-ready web server to run the Flask application.

*   **`app.py`**
    *   **Purpose:** This is the main file for the Flask application. It contains the logic for the machine learning model, the allocation engine, and the API endpoints.
    *   **Key Classes:**
        *   **`PlanAllocationModel`**:
            *   **Purpose:** This class encapsulates the machine learning model.
            *   **Logic:**
                *   It uses a `RandomForestRegressor` from `scikit-learn` to predict the "priority" of a financial plan.
                *   It includes a `generate_synthetic_data` method to create training data since no real-world data is available. This is a common technique for bootstrapping a model.
                *   The `train_model` method trains the model on the synthetic data and saves the trained model and a `StandardScaler` object to the `models/` directory using `joblib`.
                *   The `load_model` method loads the pre-trained model from the `.pkl` files.
                *   The `predict_priority` method takes the features of a financial plan and returns a priority score.
        *   **`SmartAllocationEngine`**:
            *   **Purpose:** This class contains the logic for allocating savings.
            *   **Logic:**
                *   It takes the user's total monthly savings and a list of their financial plans (education and marriage).
                *   For each plan, it uses the `PlanAllocationModel` to predict its priority.
                *   It then allocates the total savings among the plans based on their relative priorities. Plans with a higher priority get a larger share of the savings.
    *   **API Endpoints:**
        *   **`/allocate` (POST)**: This is the main endpoint. The Spring Boot backend calls this endpoint with the user's financial data (monthly savings and plans). The endpoint uses the `SmartAllocationEngine` to calculate the optimal allocation and returns it as a JSON response.
        *   **`/retrain` (POST)**: This endpoint can be used to trigger a retraining of the machine learning model.
        *   **`/health` (GET)**: A simple health check endpoint to verify that the service is running.
    *   **Startup Logic:** When the Flask application starts, it first tries to load a pre-trained model from the `models/` directory. If no model is found, it trains a new one using the synthetic data.

*   **`models/priority_model.pkl` & `models/scaler.pkl`**
    *   **Purpose:** These are the saved machine learning model and scaler objects.
    *   **Logic:**
        *   `priority_model.pkl` contains the trained `RandomForestRegressor` model.
        *   `scaler.pkl` contains the `StandardScaler` object that was fitted to the training data. It's important to use the same scaler to preprocess new data before making predictions to ensure the data is in the same format as the training data.

### **4. System Architecture (High-Level)**

The project uses a microservices-oriented architecture:

`Frontend (React.js)` → `Backend API (Spring Boot)` → `Database (MySQL)`
`↑`
`ML Service (Flask)`

*   **Data Flow:**
    1.  The user interacts with the **Frontend** (React application).
    2.  The Frontend sends REST API requests to the **Backend API** (Spring Boot application).
    3.  The Backend handles business logic, authenticates users, and performs CRUD operations on the **Database** (MySQL).
    4.  For predictive features, the Backend calls the **ML Service** (Flask API), which returns predictions based on the user's financial data.

### **5. Database Design**

The project uses a MySQL database with a well-structured schema to store user and financial data. The `ddl.sql` script defines the following tables:

*   **`users`**: Stores user credentials and basic information.
    *   Fields: `id` (PK), `username`, `email`, `password`, `first_name`, `last_name`, etc.
*   **`family_profiles`**: Contains details about a user's family and financial situation.
    *   Fields: `id` (PK), `user_id` (FK to `users`), `family_size`, `monthly_income`, `monthly_expenses`, `risk_tolerance`.
*   **`children`**: Stores information about a user's children.
    *   Fields: `id` (PK), `family_profile_id` (FK to `family_profiles`), `name`, `date_of_birth`.
*   **`education_plans`**: Tracks savings plans for a child's education.
    *   Fields: `id` (PK), `child_id` (FK to `children`), `plan_name`, `estimated_total_cost`, `current_savings`, `monthly_contribution`.
*   **`marriage_plans`**: Tracks savings plans for marriage expenses.
    *   Fields: `id` (PK), `family_profile_id` (FK to `family_profiles`), `plan_name`, `estimated_total_cost`, `current_savings`.
*   **`savings_plans`**: A general table for other savings goals.
    *   Fields: `id` (PK), `family_profile_id` (FK to `family_profiles`), `goal_amount`, `current_amount`.
*   **`investment_options`**: A reference table for different types of investments.
    *   Fields: `id` (PK), `name`, `type`, `risk_level`, `expected_annual_return`.
*   **`investments`**: Links savings plans to specific investment options.
    *   Fields: `id` (PK), `savings_plan_id` (FK to `savings_plans`), `investment_option_id` (FK to `investment_options`), `amount`.
*   **`financial_transactions`**: Logs all financial activities (deposits, withdrawals).
    *   Fields: `id` (PK), `user_id` (FK to `users`), `transaction_type`, `amount`, `related_plan_id`.
*   **Other Tables**: `marriage_expense_categories`, `notifications`, `education_cost_references`, `marriage_cost_references`, `economic_indicators`, `reports`.

### **6. Core Features**

Here is a breakdown of the core features of the FinWise application and the files that implement them.

*   **User Authentication**
    *   **Description:** Secure user registration and login system.
    *   **Files:**
        *   **Backend:** `SecurityConfig.java`, `UserController.java`, `UserService.java`, `CustomUserDetailsService.java`, `UserRepository.java`.
        *   **Frontend:** `Login.jsx`, `Registration.jsx`, `AuthCallback.js`, `userService.js`.
    *   **Flow:**
        1.  The user fills out the registration or login form in the frontend (`Registration.jsx` or `Login.jsx`).
        2.  An API call is made to the backend's `/api/auth/register` or `/api/auth/login` endpoint.
        3.  `UserController` handles the request and calls `UserService` to register the user or `AuthenticationManager` to authenticate them.
        4.  Spring Security validates the credentials against the `users` table using `CustomUserDetailsService`.
        5.  Upon success, the user is redirected to `AuthCallback.js`, which stores the user's data in `localStorage` and navigates to the appropriate page.

*   **Family & Child Management**
    *   **Description:** Users can create a family profile and add details for each child.
    *   **Files:**
        *   **Backend:** `FamilyProfileController.java`, `FamilyProfileService.java`, `FamilyProfileRepository.java`, `ChildController.java`, `ChildService.java`, `ChildRepository.java`.
        *   **Frontend:** `FamilyDetails.jsx`, `EducationPage.js`.
    *   **Flow:**
        1.  A new user is redirected to `FamilyDetails.jsx` to create their family profile.
        2.  The form submission calls the `/api/familyProfile` endpoint to save the data.
        3.  On the `EducationPage.js`, the user can add, edit, or delete children, which makes API calls to the `/api/children` endpoints.

*   **Education & Marriage Planning**
    *   **Description:** Create and manage dedicated savings plans for a child's education and marriage.
    *   **Files:**
        *   **Backend:** `EducationPlanController.java`, `EducationPlanService.java`, `MarriagePlanController.java`, `MarriagePlanService.java`.
        *   **Frontend:** `EducationPage.js`, `MarriagePage.js`.
    *   **Flow:**
        1.  On the `EducationPage.js` or `MarriagePage.js`, the user can create a new plan.
        2.  This opens a modal form that collects the plan details.
        3.  The form submission calls the `/api/education-plans` or `/api/marriage-plans` endpoints to save the plan to the database.
        4.  The pages display the progress of each plan, with options to edit or delete them.

*   **Financial Goal Prediction & Allocation**
    *   **Description:** An ML model predicts the priority of each financial goal and allocates the user's monthly savings accordingly.
    *   **Files:**
        *   **Backend:** `PredictionController.java`, `PredictionService.java`.
        *   **ML API:** `app.py`.
    *   **Flow:**
        1.  The `PredictionService` in the backend is triggered when a user's financial situation changes (e.g., a new plan is added).
        2.  `PredictionService` gathers all the user's financial plans and monthly savings and sends them to the `/allocate` endpoint of the ML API.
        3.  The Flask API in `app.py` uses the `SmartAllocationEngine` to calculate the optimal allocation for each plan.
        4.  The allocations are returned to the `PredictionService`, which then updates the `monthlyContribution` for each plan in the database.

*   **Dashboard & Visualization**
    *   **Description:** A central dashboard displays key financial metrics, recent activities, and progress towards goals.
    *   **Files:**
        *   **Backend:** `DashboardMetricsDTO.java` (though not fully implemented, the structure is there).
        *   **Frontend:** `Dashboard.jsx`.
    *   **Flow:**
        1.  The `Dashboard.jsx` component fetches data from various backend endpoints (`/api/familyProfile`, etc.).
        2.  It displays the data in a series of cards and charts, giving the user a quick overview of their financial health.

### **7. API Endpoints**

The backend exposes a comprehensive set of RESTful APIs, as documented in `api-endpoints.md`. Here are some of the key endpoints:

*   **User Management (`/api`)**
    *   `POST /api/user`: Register a new user.
    *   `GET /api/user`: Get details of the currently authenticated user.
    *   `POST /api/auth/logout`: Log out the user.
*   **Family Profile (`/api/familyProfile`)**
    *   `POST /api/familyProfile`: Create a family profile.
    *   `GET /api/familyProfile`: Get the family profile for the current user.
*   **Education Plans (`/api/education-plans`)**
    *   `POST /api/education-plans/{familyProfileId}`: Create a new education plan.
    *   `GET /api/education-plans/child/{childId}`: Get all education plans for a specific child.
*   **Marriage Plans (`/api/marriage-plans`)**
    *   `POST /api/marriage-plans/{familyProfileId}`: Create a new marriage plan.
    *   `GET /api/marriage-plans/family/{familyProfileId}`: Get all marriage plans for a family.
*   **Financial Transactions (`/api/financial-transactions`)**
    *   `POST /api/financial-transactions/{familyProfileId}`: Add a new financial transaction.
    *   `GET /api/financial-transactions/family/{familyProfileId}/recent`: Get recent transactions.

### **8. Frontend**

*   **Major Pages/Components:**
    *   **`landing.jsx`**: The marketing and entry page for unauthenticated users.
    *   **`Login.jsx` / `Registration.jsx`**: User authentication forms.
    *   **`Dashboard.jsx`**: The main overview page for authenticated users.
    *   **`FamilyDetails.jsx`**: The form for new users to set up their family profile.
    *   **`EducationPage.js` / `MarriagePage.js` / `InvestmentPage.js`**: Pages for managing the different types of financial plans.
    *   **`InvestmentOptionsPage.js`**: Displays recommended investment options.
    *   **`DynamicNavbar.jsx`**: The main navigation component.
*   **Communication with Backend:**
    *   The frontend uses the `axios` library to make REST API calls to the Spring Boot backend.
    *   The `withCredentials: true` option is used in `axios` requests to send session cookies, which is necessary for the session-based authentication to work.
    *   User data and authentication status are stored in `localStorage` and managed through the `userService.js` utility.

### **9. Tests**

*   **Backend:**
    *   **`FinWiseApplicationTests.java`**: This is a basic test class created by Spring Boot. It contains a `contextLoads()` test that checks if the Spring application context can start successfully. This is a simple integration test to ensure that the application's configuration is correct.
*   **Frontend:**
    *   **`App.test.js`**: This is a basic test file created by Create React App. It contains a simple test to check if the "learn react" link is rendered.
*   **Summary of Coverage:** The project has a very basic level of testing. There are no comprehensive unit or integration tests for the controllers, services, or components. This is an area that could be significantly improved.

### **11. Challenges & Solutions**

*   **Challenge 1: Integrating Three Separate Services (Frontend, Backend, ML API)**
    *   **Problem:** Connecting the React frontend, Spring Boot backend, and Python Flask API was complex, especially handling Cross-Origin Resource Sharing (CORS) and ensuring smooth data flow between them.
    *   **Solution:**
        *   **CORS:** CORS was configured in both the Spring Boot (`SecurityConfig.java`) and Flask (`app.py`, although not explicitly shown, Flask allows CORS) applications to allow requests from the frontend's origin (`http://localhost:3000`).
        *   **API Contract:** A clear API contract (JSON format) was established for requests and responses between the services, as documented in `api-endpoints.md`.
*   **Challenge 2: User Authentication and State Management**
    *   **Problem:** Managing the user's login state across the frontend and securing the backend endpoints was a key challenge.
    *   **Solution:**
        *   **Backend:** A session-based authentication mechanism was implemented using Spring Security. When a user logs in, the backend creates a session for them.
        *   **Frontend:** After a successful login, the user's details and authentication status are stored in `localStorage`. The `axios` library is configured to send credentials (session cookies) with every request, so the backend can identify the user.
*   **Challenge 3: Lack of Real-World Data for ML Model**
    *   **Problem:** The ML model for predicting plan priorities needed data to be trained, but no real-world user data was available.
    *   **Solution:** A `generate_synthetic_data` function was created in `app.py`. This function generates a dataset of realistic-looking financial plans and their "optimal" priorities based on a set of business rules (e.g., plans with a shorter time horizon are more urgent). This synthetic data was then used to train the `RandomForestRegressor` model, allowing the ML feature to be developed and tested without real user data.

### **12. Impact & Learning Outcomes**

*   **What the project demonstrates:**
    *   This project demonstrates the ability to build a full-stack application with a microservices-oriented architecture.
    *   It showcases skills in three different technology stacks: Java/Spring Boot for the backend, JavaScript/React for the frontend, and Python/Flask/scikit-learn for the ML service.
    *   It shows an understanding of database design, REST API development, and user authentication.
*   **Skills and technologies practiced:**
    *   **Backend:** Java, Spring Boot, Spring Data JPA, Spring Security, REST API design, Maven.
    *   **Frontend:** React, React Router, Axios, Tailwind CSS, state management.
    *   **ML/AI:** Python, Flask, scikit-learn, Pandas, model training and deployment.
    *   **Database:** MySQL, schema design, DDL scripting.
    *   **Architecture:** Microservices, three-tier architecture.
*   **How this project can be applied in real-world scenarios:**
    *   This project serves as a strong foundation for a commercial FinTech product.
    *   It can be extended with more features like budget tracking, loan management, automated investment advice, and integration with financial institutions to automatically import transaction data.
    *   The architecture allows for each component to be scaled independently, which is a common practice in modern web applications.
