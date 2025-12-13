---
title: "Week 9 Worklog"
date: "2025-11-03T09:00:00+07:00"
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives
- Set up the frontend foundation for the Game Card Platform.
- Initialize the React project and define application structure.
- Integrate frontend with backend APIs.

---

### Tasks

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **Project Initialization:**<br>- Create React project.<br>- Configure folder structure (components, pages, services). | 03/11/2025 | 03/11/2025 | |
| 2 | **Routing Setup:**<br>- Configure React Router.<br>- Define public and protected routes. | 04/11/2025 | 04/11/2025 | |
| 3 | **API Integration:**<br>- Configure Axios instance.<br>- Connect to backend product APIs. | 05/11/2025 | 05/11/2025 | |
| 4 | **UI Development:**<br>- Build product listing page.<br>- Implement reusable UI components. | 06/11/2025 | 06/11/2025 | |
| 5 | **Verification:**<br>- Validate API data rendering.<br>- Handle loading and error states. | 07/11/2025 | 07/11/2025 | |

---

### Extra Knowledge: Frontend–Backend Separation

During this week, I reinforced the importance of separating frontend and backend responsibilities. The React application communicates with the backend exclusively via REST APIs, ensuring:

- Loose coupling between UI and business logic
- Independent deployment of frontend and backend
- Easier scaling and maintenance

This architecture supports future migration to CDN-based hosting (e.g., S3 + CloudFront).

---

### Frontend Implementation: Axios Service Layer

To avoid duplicating API logic across components, I implemented a centralized Axios service.

**File:** `apiClient.ts`

```typescript
import axios from "axios";

const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 10000,
});

apiClient.interceptors.response.use(
  (response) => response,
  (error) => Promise.reject(error)
);

export default apiClient;
```

## Achievements

* Successfully initialized and structured the React frontend project.
* Implemented routing and page navigation using React Router.
* Integrated backend APIs using a centralized Axios configuration.
* Rendered dynamic data from the backend in frontend components.
* Established a scalable frontend architecture for future feature expansion.