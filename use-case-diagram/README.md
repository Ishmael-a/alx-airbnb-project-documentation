# 🎭 Airbnb Clone — Backend Use Case Diagram

## 🎯 Objective
Visualize how users interact with the backend system of the Airbnb Clone, focusing on the main functionalities such as user registration, property management, bookings, and payments.

---

## 🧩 Key Actors

| Actor | Description |
|--------|--------------|
| **Guest** | A user who browses listings, books properties, and leaves reviews. |
| **Host** | A user who lists properties, manages availability, and receives payments. |
| **Admin** | Manages users, listings, bookings, and handles reports or disputes. |
| **Payment Gateway** | External service (e.g., Stripe, PayPal) for processing payments. |
| **Email Service** | External service (e.g., SendGrid, Mailgun) for sending notifications. |

---

## ⚙️ Use Cases by Actor

### 🧍 Guest
- Register / Login  
- Browse and Search Properties  
- Filter Listings (by price, location, amenities, etc.)  
- View Property Details  
- Make a Booking  
- Cancel Booking  
- Make Payment  
- Leave Review / Rating  
- Receive Notifications  

### 🏠 Host
- Register / Login  
- Add Property Listing  
- Edit / Delete Property Listing  
- View Bookings for Own Listings  
- Manage Availability and Pricing  
- Respond to Reviews  
- Receive Payments  
- Receive Notifications  

### 👨‍💼 Admin
- Manage Users (Guests/Hosts)  
- Manage Property Listings  
- Monitor Bookings and Payments  
- Handle Complaints and Reports  
- Access Analytics Dashboard  

### 🏦 Payment Gateway
- Process Guest Payments  
- Trigger Host Payouts  

### ✉️ Email Service
- Send Booking Confirmation Emails  
- Send Payment Receipts  
- Send Cancellation Notices  

---

## 🗺️ System Overview (Text Description)
1. **Guest** interacts with the system to register, log in, search listings, and make bookings.  
2. **Host** adds and manages property listings and receives payments for successful bookings.  
3. **Admin** oversees all system activities and ensures smooth operations.  
4. **Payment Gateway** securely processes all transactions between guests and hosts.  
5. **Email Service** manages system notifications for all users.

---
