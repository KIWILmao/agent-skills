# Screens Template

List every screen in the product grouped by user flow. Be exhaustive — cover every state, 
including empty states, error states, and loading states where meaningful.

---

# screens.md — [Project Name]

---

## Flow 1: Auth

### Screen: Login
- **Route:** `/login`
- **Auth State:** `public`
- **Purpose:** Allow existing users to sign in to their account
- **Key Actions:**
 - Submit email + password form
 - Click "Forgot password" → goes to /forgot-password
 - Click "Sign up" → goes to /register
 - OAuth login (Google, GitHub) if applicable
- **Data Displayed:** None (form only)

### Screen: Register
- **Route:** `/register`
- **Auth State:** `public`
- **Purpose:** Allow new users to create an account
- **Key Actions:**
 - Submit registration form (name, email, password)
 - Click "Already have an account" → /login
- **Data Displayed:** None (form only)

### Screen: Forgot Password
- **Route:** `/forgot-password`
- **Auth State:** `public`
- **Purpose:** Let users request a password reset email
- **Key Actions:**
 - Submit email
 - Receive success confirmation message
- **Data Displayed:** None

### Screen: Reset Password
- **Route:** `/reset-password?token=xxx`
- **Auth State:** `public` (token-validated)
- **Purpose:** Let users set a new password via reset link
- **Key Actions:**
 - Submit new password + confirm
 - Redirect to /login on success
- **Data Displayed:** None

---

## Flow 2: Onboarding

### Screen: Onboarding Step 1
- **Route:** `/onboarding/step-1`
- **Auth State:** `authenticated`
- **Purpose:** Collect initial user preferences or profile info
- **Key Actions:**
 - Fill in profile fields
 - Click Next
- **Data Displayed:** Pre-filled user name/email if available

[Continue for each onboarding step...]

---

## Flow 3: Core App

### Screen: Dashboard
- **Route:** `/dashboard`
- **Auth State:** `authenticated`
- **Purpose:** Main landing page after login — overview of key metrics/activity
- **Key Actions:**
 - Navigate to sub-sections via sidebar/nav
 - Quick-action buttons
- **Data Displayed:** [List what data shows — e.g. recent items, stats, notifications]

[Continue for all core screens...]

---

## Flow 4: Settings

### Screen: Profile Settings
- **Route:** `/settings/profile`
- **Auth State:** `authenticated`
- **Purpose:** Let user update their name, avatar, email
- **Key Actions:**
 - Edit and save profile fields
 - Upload avatar
- **Data Displayed:** Current profile data

---

## Flow 5: Admin (if applicable)

### Screen: Admin Dashboard
- **Route:** `/admin`
- **Auth State:** `role: admin`
- **Purpose:** Overview of system health, users, and activity
- **Key Actions:**
 - View user list
 - Access admin sub-sections
- **Data Displayed:** User count, system stats, recent activity log

---

## Notes for Claude
- Cover ALL screens — not just happy path. Include: empty states, 404, error pages, loading screens if they are distinct UX states.
- If a screen has significantly different states (e.g. empty vs populated), document both.
- For modals and drawers that are significant interactions, include them as sub-entries under the parent screen.
