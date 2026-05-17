# Accounts App Documentation

## Overview
The `accounts` app is responsible for handling all user authentication, authorization, and profile management within the SmartQueue application. It extends Django's default authentication system to support role-based access control (RBAC), distinguishing between standard public users and service organizers.

## Main Data Models

### `Profile` Model (`models.py`)
This is the core model of the app. It extends Django's built-in `User` model using a One-to-One relationship.
*   **Key Fields:**
    *   `user`: Links directly to the Django authentication User.
    *   `phone` & `address`: Basic contact information.
    *   `role`: The most critical field for business logic. It determines what the user can do in the application. It uses choices: `ROLE_USER` ('Public User') or `ROLE_ORGANIZER` ('Organizer').

## Main Views & Logic (`views.py`)

### 1. `SignUpView` (Class-Based View - `CreateView`)
*   **Purpose**: Handles new user registration.
*   **Flow**: 
    1. The user fills out the `SignUpForm`.
    2. If valid, a new Django `User` and linked `Profile` are created.
    3. `form_valid()` intercepts the success phase to *automatically log the user in* immediately after creation.
    4. The user is then redirected to the `login_redirect` view.

### 2. `RedirectAfterLoginView` (Class-Based View - `View`)
*   **Purpose**: Acts as a traffic controller immediately after a user successfully logs in.
*   **Flow**:
    1. Checks the `role` of the authenticated user.
    2. **If Organizer/Staff**: It checks how many organizations they manage. If they manage exactly one, it takes them directly to that organization's dashboard (`organizer_dashboard`). If they manage multiple (or zero), it takes them to a list of their organizations (`organizer_org_list`).
    3. **If Public User**: Redirects them to the public catalog of organizations (`organization_list`) so they can find services and join queues.

### 3. `ProfileView` (Class-Based View - `TemplateView`)
*   **Purpose**: Renders the user's personal dashboard/profile page.
*   **Flow**:
    1. Fetches the user's `Profile`.
    2. Queries the `Token` model (from the `queue` app) to retrieve the history of queues the user has joined.
    3. If the user is an organizer, it additionally queries and passes the `managed_organizations` to the template.

## Routing (`urls.py`)
*   `/signup/`: Points to `SignUpView`.
*   `/login/`: Uses Django's built-in `LoginView` but overrides the form with a custom `LoginForm` and template.
*   `/logout/`: Uses Django's built-in `LogoutView`.
*   `/profile/`: Points to `ProfileView`.
*   `/login-redirect/`: Points to `RedirectAfterLoginView`.

## Templates Architecture

The templates for this app are located in the global `apps/templates` directory (specifically `registration/` and `accounts/`).

1.  **`registration/login.html` & `registration/signup.html`**: 
    *   These render the authentication forms. They are designed to look clean and modern, collecting the necessary fields for the `SignUpForm` and `LoginForm`.
2.  **`accounts/profile.html`**:
    *   This is a dynamic dashboard.
    *   **Standard Users**: See their personal details and a list of their active/past tokens (queue tickets).
    *   **Organizers**: See an additional section displaying the organizations they manage, with quick links to edit or view their specific organizational dashboards.

## How to Explain This to Your Faculty
1.  **Start with the Data**: Explain that Django's built-in User wasn't enough, so you used a `Profile` model linked via a OneToOneField to store custom fields like "role".
2.  **Explain the RBAC (Role-Based Access Control)**: Emphasize the `RedirectAfterLoginView`. This is a great piece of logic to highlight because it proves your application is "smart" enough to provide a tailored user experience depending on who just logged in.
3.  **Explain the DRY (Don't Repeat Yourself) Principle**: Note how you used Django's Class-Based Views (`CreateView`, `TemplateView`) and built-in Auth Views (`LoginView`, `LogoutView`) rather than writing all the authentication boilerplate from scratch. This demonstrates a strong grasp of standard enterprise framework practices.
