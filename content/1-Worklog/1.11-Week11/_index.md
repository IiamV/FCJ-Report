---
title: "Week 11 Worklog"
date: "2025-11-17T09:00:00+07:00"
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives
- Implement authentication and authorization on the frontend.
- Secure API communication using JWT.
- Optimize frontend delivery and access via CDN.

---

### Tasks

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **Authentication Flow:**<br>- Design login and logout UI.<br>- Handle credential submission. | 17/11/2025 | 17/11/2025 | |
| 2 | **JWT Handling:**<br>- Store access token securely.<br>- Attach token to API requests. | 18/11/2025 | 18/11/2025 | |
| 3 | **Protected Routes:**<br>- Implement role-based route protection.<br>- Redirect unauthorized users. | 19/11/2025 | 19/11/2025 | |
| 4 | **CDN Integration:**<br>- Prepare frontend build for CloudFront delivery.<br>- Validate asset loading via CDN. | 20/11/2025 | 20/11/2025 | |
| 5 | **Verification:**<br>- Test login, logout, token expiry, and protected pages. | 21/11/2025 | 21/11/2025 | |

---

### Extra Knowledge: Stateless Authentication on the Frontend

While implementing JWT-based authentication, I learned that frontend applications must remain stateless. The React application does not manage sessions; instead:

- The backend issues a signed JWT after successful login.
- The frontend stores the token and sends it with each request.
- Any backend instance can validate the token independently.

This approach aligns with scalable architectures behind load balancers and avoids dependency on sticky sessions.

---

### Frontend Implementation: Auth Context

To centralize authentication logic, I implemented an authentication context.

**File:** `AuthContext.tsx`

```tsx
import { createContext, useContext, useState } from "react";
import apiClient from "../api/apiClient";

interface AuthContextType {
  token: string | null;
  login: (username: string, password: string) => Promise<void>;
  logout: () => void;
}

const AuthContext = createContext<AuthContextType | null>(null);

export const AuthProvider = ({ children }: { children: React.ReactNode }) => {
  const [token, setToken] = useState<string | null>(
    localStorage.getItem("access_token")
  );

  const login = async (username: string, password: string) => {
    const response = await apiClient.post("/auth/login", {
      username,
      password,
    });

    const accessToken = response.data.token;
    setToken(accessToken);
    localStorage.setItem("access_token", accessToken);
  };

  const logout = () => {
    setToken(null);
    localStorage.removeItem("access_token");
  };

  return (
    <AuthContext.Provider value={{ token, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error("useAuth must be used within AuthProvider");
  }
  return context;
};
```

## Achievements
* Implemented secure login and logout functionality in the frontend.
* Protected routes based on authentication and user roles.
* Integrated JWT into API requests without relying on session state.
* Verified frontend compatibility with CDN-based delivery.