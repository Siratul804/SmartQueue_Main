# Templates Architecture Documentation

## Overview
The `templates` directory contains all the HTML files responsible for the frontend presentation of the SmartQueue application. We use Django's Template Language (DTL) to dynamically render data passed from our views, ensuring a separation of logic (Python/Views) and presentation (HTML/CSS).

## Core Design Principles

1.  **Template Inheritance (DRY Principle):**
    We heavily utilize template inheritance to avoid repeating code. The master layout is defined in `base.html`, and all other pages "extend" this master file. This means the navigation bar, footer, CSS links, and JS scripts are written only once.
2.  **Modular Folder Structure:**
    Instead of having dozens of HTML files in one folder, templates are organized into subdirectories corresponding to their respective Django apps (`accounts/`, `organizations/`, `queue/`).
3.  **Role-Aware UI:**
    Many templates use `{% if %}` statements checking the `request.user.profile.role` to conditionally hide or show buttons depending on whether the user is a Public User or an Organizer.

## Directory Breakdown

### 1. Root Level Templates
*   **`base.html`**: The most important file in this directory. It contains the standard HTML boilerplate (`<head>`, `<body>`), global CSS frameworks (like Bootstrap/Tailwind), the main navigation bar, and the `{% block content %}{% endblock %}` placeholder where child templates inject their specific code.
*   **`home.html`**: The landing page of the application.
*   **`403.html` & `404.html`**: Custom error pages to provide a better user experience when a user hits a broken link or tries to access a page they don't have permission for.

### 2. `accounts/` & `registration/` Directories
*   **Purpose**: Handles all authentication and profile-related UI.
*   **Key Files**:
    *   `registration/login.html` & `signup.html`: The forms where users authenticate. They use Django's built-in form rendering combined with custom CSS classes for styling.
    *   `accounts/profile.html`: The dashboard where a user sees their personal info, their active/past queue tickets, and (if they are an Organizer) a link to manage their services.

### 3. `organizations/` Directory
*   **Purpose**: Handles the display of the business/service side of the application.
*   **Key Files**:
    *   `organization_list.html`: The public-facing catalog where users can browse available organizations to join their queues.
    *   `organization_detail.html`: The specific page for an organization showing its description and available services.
    *   **Organizer Dashboards**: Specialized templates used exclusively by organizers to edit their organization details, manage staff, and view analytics.

### 4. `queue/` Directory
*   **Purpose**: Handles the core "SmartQueue" functionality (joining a queue, seeing live status, and organizer management of the queue).
*   **Key Files**:
    *   `token_status.html`: A dynamic page that shows a user their current position in line, estimated wait time, and a progress bar. It uses JavaScript polling to refresh the status in real-time.
    *   `join_queue.html`: The form where users input their details to generate a new Token for a specific service.
    *   **Queue Management**: Templates used by organizers to "call the next person", "complete a service", or handle "emergency requests".

## How to Explain This to Your Faculty
1.  **Emphasize "Template Inheritance"**: Explain how `base.html` acts as the skeleton. If you need to change the logo in the navbar, you only change it in `base.html`, and it instantly updates across the entire site. This shows you understand scalability and maintainability.
2.  **Explain the "Context"**: Mention that the HTML files are not static. They are "rendered" by Django views which pass a "Context Dictionary" (like the user's name, or a list of services). The templates use DTL tags like `{{ variable_name }}` to display this dynamic data.
3.  **Highlight the Real-Time Aspect**: If you are explaining the queue system, point out that templates like `token_status.html` combine Django's server-side rendering with client-side JavaScript to create a modern, live-updating feel without the user needing to constantly hit "refresh".
