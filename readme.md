# 1. How MakeMyTrip (MMT) Bookings Work?
![image](https://github.com/user-attachments/assets/832c2463-41ec-4b95-b487-b07098cd7fba)

---

## **1. Booking Process Flow**

Below is a structured explanation of how a flight/hotel booking works on MakeMyTrip.

### **Step 1: User Login & Selection**
* The user logs into the **MakeMyTrip** platform (via website or mobile app).
* The user selects a **flight** or **hotel** they want to book.

### **Step 2: Checkout Page**
* The user is redirected to the **checkout page**, where they review their selection and proceed with payment.

### **Step 3: Choosing Payment Method**
* The user selects **HDFC Net Banking** as the preferred payment method.

### **Step 4: Payment Gateway Redirection**
* A new **popup window** opens, redirecting the user to **HDFC Net Banking** (`netbanking.hdfc.com`) to complete the transaction.

### **Step 5: If User Closes MMT Frontend Checkout Page**
* Instead of waiting, the user **closes** the MMT **checkout page** while the payment is still in progress on the HDFC website.

### **Step 6: Payment Completion on HDFC Portal**
* The user **completes the payment** successfully on **HDFC Net Banking**.

### **Step 7: Bank Transfers Money to MMT Account**
* **HDFC Bank** transfers the **paid amount** to MakeMyTrip’s **bank account**.

### **Step 8: HDFC Calls MMT Webhook API**
* After the transaction is successful, **HDFC Bank** triggers a **webhook API call** to MMT’s **server**.
* The webhook is a **listener** on the server that waits for payment confirmations from banks.
* The **HDFC server** sends the following **payload (data)** to MMT’s webhook API:

```json
{
  "userId": 67,
  "amount": 19000,
  "flightDetails": {}
}
```

### **Step 9: MMT Webhook API Updates the Database**
* The **MMT Webhook API** processes this payment confirmation.
* It **updates the database** for the **specific userId (67)**.
* The booking details are **added to the database**, confirming the reservation.

---
**NOTE : Never process two withdrawl request parallely,  let the requests be reside in the queue and process each request sequentially at a time.**

## 2. Why Does MakeMyTrip (MMT) Use a Separate Webhook Server for Banks?

MMT uses a **separate webhook server** instead of merging it into the main backend for the following reasons:

## 1. Separation of Concerns  
- The main backend handles **user authentication, bookings, and searches**.  
- The webhook server **only** listens for **bank payment confirmations**.  
- Keeping them separate ensures **better structure and organization**.

## 2. High Availability & Reliability  
- Bank webhook calls **must always be processed** without failure.  
- If MMT’s main backend is **slow or down**, the webhook server still works.  
- Ensures **payments are never lost** even during heavy traffic.

## 3. Security & Compliance  
- Banks require **strict security rules** for webhook handling.  
- A separate server allows **better access control** for **only trusted banks**.  
- Different security policies ensure **safer transactions**.

## 4. Asynchronous Processing  
- Webhooks work **independently** and don’t need an instant response.  
- If a webhook request fails, it can **retry automatically**.  
- Prevents **delays** in the main backend.

## 5. Better Scalability  
- As bookings grow, **bank webhook traffic increases**.  
- MMT can **scale only the webhook server** instead of the entire system.  
- **Reduces costs** and **improves efficiency**.

## 6. Easier Debugging & Monitoring  
- A dedicated webhook server helps **quickly find payment issues**.  
- Logs and errors are separate from the main backend.  
- **Faster troubleshooting** for failed transactions.

---

### ✅ Summary  
MMT keeps a **separate webhook server** for banks to ensure **better performance, security, reliability, and scalability** while avoiding issues in the main backend. 🚀  

# 2. # LeetCode Problem Submission Architecture
 
![image](https://github.com/user-attachments/assets/2ab5cd6b-690b-4ae0-99dd-3b9f9a19ffa8)
## Overview
This document explains the flow of problem submission on LeetCode, from user submission to execution and result delivery.

## 1. User Submits Code
- A user writes and submits code via:
  - **Browser**
  - **Mobile App**
- The request contains:
  ```json
  {
    "userId": 43,
    "problemId": 91,
    "language": "java",
    "code": "..."
  }
  ```

## 2. Primary Backend (BE) Processing
- The request is sent to the **Primary Backend (BE)**, which handles:
  - `/problems` → Problem-related data
  - `/contest` → Contest-related data
  - `/profile` → User profile data
- The backend processes the request and forwards it to the **Queue**.

## 3. Queue System
- The **Queue** ensures smooth processing and distributes tasks efficiently to **Workers (w1, w2, etc.)**.
- The workers execute the submitted code on LeetCode’s servers.

## 4. Publishing Execution Results
- Once execution is complete, the result is published via a **Pub/Sub (Publish-Subscribe) system**.
- Example result:
  ```json
  {
    "userId": 43,
    "problemId": 91,
    "language": "java",
    "status": "TLE"
  }
  ```
- This result is sent to users subscribed to updates for `userId: 43`.

## 5. WebSocket (WS) Result Delivery
- The user's browser or mobile app **subscribes** to their execution results via WebSockets (`WS1`, `WS2`, `WS3`).
- WebSockets push the response back to the user’s interface in **real-time**.

## Final Output
- The user sees their submission result (e.g., **Accepted, TLE, Wrong Answer**) instantly.

## Benefits of This Architecture
✅ **Efficient handling of high traffic**  
✅ **Real-time updates for users**  
✅ **Optimized execution via queueing & worker distribution**
