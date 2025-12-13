---
title: "Week 10 Worklog"
date: "2025-11-10T09:00:00+07:00"
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives
- Implement core business flows on the frontend.
- Build order creation and checkout UI.
- Integrate frontend logic with backend order APIs.

---

### Tasks

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **State Management:**<br>- Design global state for cart and checkout.<br>- Define cart data models. | 10/11/2025 | 10/11/2025 | |
| 2 | **Cart Features:**<br>- Add items to cart.<br>- Update quantity and remove items. | 11/11/2025 | 11/11/2025 | |
| 3 | **Checkout Page:**<br>- Build checkout UI.<br>- Display order summary and pricing. | 12/11/2025 | 12/11/2025 | |
| 4 | **Order API Integration:**<br>- Call create-order API.<br>- Handle success and failure responses. | 13/11/2025 | 13/11/2025 | |
| 5 | **Verification:**<br>- Test full purchase flow from product list to order confirmation. | 14/11/2025 | 14/11/2025 | |

---

### Extra Knowledge: Managing Client-Side State Consistency

During checkout implementation, I learned that inconsistent client-side state can lead to incorrect orders. To mitigate this:

- Cart state is treated as a single source of truth.
- Order data sent to backend is derived directly from cart state.
- UI disables the checkout button while the order request is processing to prevent duplicate submissions.

This ensures the frontend does not introduce race conditions at the user interaction level.

---

### Frontend Implementation: Cart Context

To manage cart state across multiple pages, I implemented a React Context.

**File:** `CartContext.tsx`

```tsx
import { createContext, useContext, useState } from "react";

interface CartItem {
  variantId: number;
  quantity: number;
  price: number;
}

interface CartContextType {
  items: CartItem[];
  addItem: (item: CartItem) => void;
  removeItem: (variantId: number) => void;
  clearCart: () => void;
}

const CartContext = createContext<CartContextType | null>(null);

export const CartProvider = ({ children }: { children: React.ReactNode }) => {
  const [items, setItems] = useState<CartItem[]>([]);

  const addItem = (item: CartItem) => {
    setItems((prev) => [...prev, item]);
  };

  const removeItem = (variantId: number) => {
    setItems((prev) => prev.filter((i) => i.variantId !== variantId));
  };

  const clearCart = () => setItems([]);

  return (
    <CartContext.Provider value={{ items, addItem, removeItem, clearCart }}>
      {children}
    </CartContext.Provider>
  );
};

export const useCart = () => {
  const context = useContext(CartContext);
  if (!context) {
    throw new Error("useCart must be used within CartProvider");
  }
  return context;
};
```

## Achievements
* Implemented complete cart and checkout flow on the frontend.
* Ensured consistent order data sent from client to backend.
* Integrated order creation API with proper loading and error handling.
* Completed end-to-end purchase flow testing from UI perspective.