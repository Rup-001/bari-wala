# Software Requirements Specification (SRS) for Flatwise - Society Management System

## 1. Introduction

### 1.1 Purpose
This document provides a detailed description of the Flatwise Society Management System. Its purpose is to offer a comprehensive overview of the software's architecture, features, and requirements. It will serve as the guiding document for development, testing, and project management.

### 1.2 System Overview
Flatwise is a robust, web-based platform designed to automate and streamline the administrative and financial operations of residential societies. It provides a centralized system for managing society members, flats, billing, payments, and communications, thereby improving efficiency and transparency for both the society management and its residents.

---

## 2. Core Modules

The system is built upon a modular architecture. The core modules are:

1.  **User & Access Management:** Handles user profiles, roles, and permissions.
2.  **Society & Information Hub:** Manages society and flat-level data.
3.  **Member Billing & Collections:** Manages the generation of bills for residents and the collection of payments.
4.  **Vendor Payments & Expense Management:** A new module to manage payments made *by* the society to external vendors.

---

## 3. Detailed Feature Requirements

### 3.1 Module: User & Access Management

This module is the foundation for securing the system and ensuring users only access appropriate information.

*   **3.1.1 Role-Based Access Control (RBAC):**
    *   The system implements a strict RBAC mechanism. The foundation for this is already present in the database with `User` and `Role` tables.
    *   **Key Roles:**
        *   **Society Manager (Mngr):** Manages day-to-day operations. This role is assigned to a user for a fixed term (e.g., 1, 3, 6, or 12 months).
        *   **Association Secretary:** A role with a specific set of high-level permissions.
        *   **Flat Owner / Resident:** Basic users who can view their information, bills, and make payments.
        *   **System Administrator:** Super-user with access to all system settings.
    *   **Data Model Impact:** The assignment of a Manager role should be time-bound. This requires a mechanism to store the start and end dates of a manager's term, potentially in a separate `SocietyRoleAssignment` table.

*   **3.1.2 Board of Members:**
    *   The system must feature a "Board of Members" section.
    *   This will display a list of all users who hold a specific role (e.g., "Board Member," "Committee Member") within a society.
    *   **Implementation Note:** This can be achieved by filtering users based on their assigned role. The underlying database structure already supports this.

*   **3.1.3 Immutable Records:**
    *   Certain sensitive financial or historical records should be immutable. Even the highest-level authorities should not be able to modify them after they are finalized (e.g., a paid bill). This is a critical business rule to be enforced in the application logic.

### 3.2 Module: Society & Information Hub

This module manages the core data entities of the society.

*   **3.2.1 Society Profile:**
    *   Each society will have a profile containing its name, address, and total number of flats.
    *   The profile must also store the society's payment details:
        *   **Regular Bank Account:** Account number, bank name, branch, etc.
        *   **Mobile Banking Number:** For platforms like bKash, Nagad, etc.
    *   **Data Model Impact:** The `Society` model needs to be updated to include fields for `bank_account_details` and `mobile_banking_number`.

*   **3.2.2 Flat Information Management:**
    *   Every flat must be linked to a `User` who is designated as the owner.
    *   The system must store the phone number for either the owner or the current resident of the flat.
    *   **Implementation Note:** The database schema already links `Flat` to an `owner` (a `User`) and the `User` model contains a `phone` field, satisfying this requirement.

### 3.3 Module: Member Billing & Collections (Incoming Payments)

This module handles all financial transactions from residents to the society.

*   **3.3.1 Automated Bill Generation:** The system is capable of generating monthly bills for all flats based on predefined service charges.
*   **3.3.2 Bill Viewing:** All users can view their current and past bills. Authorized users (Manager, Secretary) can view all bills for the entire society.
*   **3.3.3 Payment Tracking:** The system tracks the status of each bill (e.g., `PENDING`, `PAID`, `CANCELLED`).

### 3.4 Module: Vendor Payments & Expense Management (Outgoing Payments)

This is a **new module** to be developed. It will handle all payments made by the society.

*   **3.4.1 Vendor Payment Management:**
    *   The system must allow for managing and recording payments made to external vendors (e.g., security companies, maintenance services).
*   **3.4.2 Payment Distribution:**
    *   A distribution mechanism will be implemented to properly allocate the costs of vendor payments against the society's budget or ledgers.
*   **3.4.3 Monthly Payment Backlog:**
    *   A report or view will be available to show a backlog of pending and overdue payments for previous months, ensuring vendors are paid on time.
*   **Data Model Impact:** This module will require new database tables:
    *   `Vendor`: To store information about external companies.
    *   `Payable` or `Expense`: To record an obligation to pay a vendor.
    *   `OutgoingPayment`: To log the actual payment transaction made to a vendor, including the source (bank, mobile banking).

---
