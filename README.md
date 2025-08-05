# Revu Product Specification

## 1. Executive Summary

Revu is an AI-powered performance analysis platform for engineering teams. It aggregates data from developer tools like GitHub, Jira, and Slack to generate holistic reports that measure productivity, impact, and collaboration. By providing a unified view of an engineer's contributions, Revu helps create a more objective and data-driven approach to performance reviews.

---

## 2. Core User Journey

The primary user workflow consists of three main steps:

1.  **Connect Data Sources:** The user connects their GitHub, Jira, and other accounts. The system prioritizes a seamless, secure authentication experience.
2.  **Analyze Contributions:** The user initiates an analysis. The backend fetches, processes, and analyzes the user's activity, providing real-time feedback on its progress.
3.  **Generate AI Report:** Once the analysis is complete, the user can generate a narrative-style performance report. The report is crafted by an AI model (Claude via Google Genkit) and summarizes key achievements, collaboration patterns, and areas for growth.

---

## 3. Feature Breakdown

### 3.1. GitHub Analysis

This feature analyzes a user's GitHub contributions based on their username and optional organization.

#### **UI & Form Behavior:**

*   The GitHub analysis tab is the default view on page load.
*   The date range is pre-filled to the last six months.
*   The "Analyze" button is disabled while an analysis is in progress to prevent duplicate submissions.

#### **Input Validation:**

*   A GitHub username is required. The form cannot be submitted without it.
*   Validation errors are displayed clearly and immediately.

#### **Analysis Flow & Results:**

*   Upon submission, a loading spinner and a progress bar are displayed. The progress bar must reflect the actual job status, polled every 2 seconds.
*   The number of commits is streamed in real-time during the analysis.
*   The final results include:
    *   **Data Freshness:** A timestamp indicating when the data was last updated.
    *   **Coding Activity:** Key metrics like commit volume, lines of code added/deleted, and pull requests.
    *   **Collaboration:** A summary of repositories contributed to, issues opened, and languages used.
    *   **Profile Information:** The user's GitHub avatar, username, and bio.
*   All UI elements must maintain a consistent layout, even if the corresponding data is zero or null.

### 3.2. AI Report Generation

This feature generates a qualitative, narrative-style report based on the analyzed data.

#### **Access & Trigger:**

*   The "Generate Report" button is disabled until at least one data source has been successfully analyzed.
*   The button's label changes to "Generating..." during report creation.

#### **Behavior:**

*   The system **always** uses the most recent successful analysis for each data source.
*   If the user switches from "User A" to "User B," the report will be generated using User B's data.
*   The report is generated in paragraph format, avoiding lists or tables for a more natural, readable flow.

#### **Report Structure:**

1.  **Executive Summary:** A high-level overview of the user's performance.
2.  **Quantified Metrics:** A summary of key metrics (e.g., commits, tickets resolved).
3.  **Qualitative Insights:** An analysis of teamwork, technical impact, and potential growth areas.

---

## 4. Other Data Sources

While GitHub is the primary data source, Revu also supports manual data entry for other tools.

| Source     | Input Method    | Data Points                                         |
| ---------- | --------------- | --------------------------------------------------- |
| Slack      | Manual textarea | Message frequency, channel activity, response times |
| Jira       | Username        | Ticket resolution, sprints, task complexity         |
| Calendar   | Manual textarea | Meeting frequency, mentoring, time allocation       |
| Confluence | Manual textarea | Docs created/updated, knowledge base impact         |

---

## 5. Non-Functional Requirements

### 5.1. Caching

*   Identical form submissions (i.e., same username, org, and date range) must return cached results instantly.
*   Cached results bypass the need for new API calls and backend jobs.
*   The cache is invalidated if any input parameters change.

### 5.2. Error Handling

The system follows a fail-fast approach to deliver a predictable user experience.

| Error Type                | Behavior                                                                 |
| ------------------------- | ------------------------------------------------------------------------ |
| **User Not Found**        | Fail immediately with a clear error message. Do not create a background job. |
| **API Rate Limit**        | Catch the error early and provide guidance on when to retry.             |
| **Authentication Failure**| Provide immediate feedback and direct the user to support.               |
| **Backend Unavailable**   | Display a status badge in the footer and disable relevant features.      |
| **Report Generation Fails**| Show a clear toast notification and return the "Generate Report" button to its enabled state. |

---

## 6. Security & Privacy

*   No sensitive user data is stored in the frontend.
*   All communication with third-party services is authenticated using securely managed tokens.
*   The application will provide clear user consent forms for data usage.

---

## 7. Future Roadmap

*   **Expanded Integrations:** Add support for GitLab, Microsoft Teams, and Linear.
*   **Code Quality Metrics:** Integrate with SonarQube or CodeClimate to analyze code quality.
*   **Team-Level Benchmarking:** Introduce AI-powered recommendations and team-level comparisons.