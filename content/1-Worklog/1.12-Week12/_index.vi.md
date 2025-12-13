---
title: "Week 12 Worklog"
date: "2025-11-24T09:00:00+07:00"
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Week 12 Objectives
- Validate frontend behavior under failure and recovery scenarios.
- Perform end-to-end testing from the user interface perspective.
- Finalize documentation and close the project.

---

### Tasks

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **HA Validation:**<br>- Refresh UI during backend instance termination.<br>- Observe request recovery via Load Balancer. | 24/11/2025 | 24/11/2025 | |
| 2 | **API Failure Handling:**<br>- Simulate backend downtime.<br>- Validate error states and retry behavior. | 25/11/2025 | 25/11/2025 | |
| 3 | **Transaction Feedback:**<br>- Test failed checkout scenarios.<br>- Verify UI rollback and user notifications. | 26/11/2025 | 26/11/2025 | |
| 4 | **UI Testing & Review:**<br>- Manual regression testing of core pages.<br>- Verify responsiveness and edge cases. | 27/11/2025 | 27/11/2025 | |
| 5 | **Project Closure:**<br>- Remove environment-specific configs.<br>- Prepare final frontend build for submission. | 28/11/2025 | 28/11/2025 | |

---

### Extra Knowledge: Frontend Resilience During Backend Failures

While testing system resilience, I learned that frontend applications must gracefully handle backend instability. Even when backend services restart or fail over, the UI should:

- Display meaningful error messages instead of breaking.
- Prevent duplicate submissions during retry windows.
- Recover automatically once APIs become available again.

This improves user trust and aligns frontend behavior with high-availability backend architecture.

---

### Frontend Implementation: Global Error Boundary

To prevent unexpected UI crashes, I implemented a global error boundary.

**File:** `ErrorBoundary.tsx`

```script
import React from "react";

interface State {
  hasError: boolean;
}

export class ErrorBoundary extends React.Component<
  { children: React.ReactNode },
  State
> {
  state: State = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error: Error) {
    console.error("UI Error:", error);
  }

  render() {
    if (this.state.hasError) {
      return <h2>Something went wrong. Please refresh the page.</h2>;
    }

    return this.props.children;
  }
}
```

## Achievements
* Verified frontend stability during backend instance restarts and failovers.
* Ensured checkout flow responds correctly to transaction failures.
* Implemented global UI error handling to prevent application crashes.
* Completed final testing and documentation from the frontend perspective.
* Successfully finalized and closed the frontend portion of the project.
