Mountain View College ALUMNI MANAGEMENT SYSTEM

Features & Functionalities Master Documentation

1. SYSTEM OVERVIEW

The Alumni Management System is a unified, enterprise-grade web application engineered for higher education institutions to manage the post-graduation lifecycle. The platform bridges graduating students, verified alumni, and administrative officers into a single secure ecosystem.
Core Objectives:
Automated Identity Verification: Replaces physical campus visits and paper document validation with email OTP verification, Cloudinary proof document vaults, and administrative audit consoles.
Digital Yearbook & Multimedia Preservation: Replaces static paper yearbooks with digital batch catalogs, interactive 5-slide "Yearbook Wrapped" stories, and social Memory Walls.
Event Logistics & Cryptographic Check-Ins: Streamlines alumni reunions, homecomings, and webinars via 1-click RSVPs, mobile cryptographic QR passes, and live web camera check-in scanners.
Centralized Support Ticketing: Deploys a searchable FAQ knowledge base and a multi-threaded support ticket engine (TCK-XXXXX).
Data Governance & Privacy Protection: Protects graduate information through NextAuth authentication, edge route middleware (middleware.ts), and server-enforced data masking (isPhotoPublic, isContactPublic).

 Access Control Matrix (RBAC)

| Persona / Role | Role Code | Permitted Route Groups | Default Landing Path | Unauthenticated Redirection |
|---|---|---|---|---|
| **Unauthenticated Guest** | `GUEST` | Home (`/`), FAQ Help Base (`/help`), Login (`/login/student`, `/login/officer`) | `/` | Redirected to `/` when accessing protected routes |
| **Public Guest User** | `PUBLIC_USER` | Published Yearbooks (`/yearbooks`), Public FAQ Search | `/yearbooks` | Redirected to `/yearbooks` |
| **Verified Student / Alumni** | `STUDENT` | Dashboard (`/dashboard`), Profile (`/profile`), Interactive Yearbook (`/yearbook`), Events (`/events`), News & Announcements (`/news`), Help Center (`/help`) | `/dashboard` | Redirected to `/` |
| **Alumni Officer** | `ALUMNI_OFFICER` | Officer Dashboard (`/officer/dashboard`), Verification Queue (`/officer/alumni-verification`), Master Directories (`/officer/alumni`, `/officer/graduates`, `/officer/faculty`), Event Manager & Live Scanner (`/officer/events`), Yearbook Manager (`/officer/yearbook`), Help Desk (`/officer/help-desk`), FAQ CMS (`/officer/faq-management`), Announcements (`/officer/announcements`) | `/officer/dashboard` | Redirected to `/dashboard` if accessing student routes |

3. SYSTEM MODULES OVERVIEW
   
Module 1: Alumni Verification & Self-Registration: Email OTP validation, 3-step application wizard, Cloudinary document upload vault, tracking lookup, and officer verification audit queue.

Module 2: Authentication & Profile Governance: NextAuth credentials & Google OAuth SSO, onboarding wizard, and privacy visibility toggles.

Module 3: Digital Yearbook & Interactive Wrapped Engine: Batch yearbook catalog (Grid and Flipbook views), 5-slide "Yearbook Wrapped" story, personal quote/superlative customization, and officer publishing console.

Module 4: Event Management, RSVP & QR Attendance: Audience-restricted event publishing, proposal audit pipeline with document vault (EventDocument), 1-click RSVP generating cryptographic mobile QR passes, and live web camera QR scanners.

Module 5: Support Help Desk & Knowledge Base: Searchable FAQ knowledge base (FaqItem), ticket submission (TCK-XXXXX), multi-threaded conversation messaging, and officer help desk queue.

Module 6: System Announcements & Real-Time Notifications: Hero banner CMS (Announcement), banner color customizer, and WebSocket real-time alerts (Notification).

5. DETAILED FEATURES AND FUNCTIONALITIES
MODULE 1: ALUMNI VERIFICATION & SELF-REGISTRATION MODULE
Feature 1.1: Email One-Time Password (OTP) Pre-Verification
Description: Validates applicant email inbox ownership prior to unlocking the registration wizard.
Functionality: Dispatches a 6-digit numeric OTP to the applicant's email address and verifies the code entry to unlock Step 2.
User/Actor: Public Guest / Unverified Graduate
System Behavior: The system generates a unique six-digit verification code and stores it securely with a validity period of 10 minutes. The verification code is sent to the user's registered email address. Upon submission, the system validates the provided code and checks whether it remains within its validity period. If the verification code is valid and has not expired, the user's email address is marked as verified. 
Inputs and Outputs:
Inputs: Email Address, 6-digit OTP Code.
Outputs: Verification Status, Unlocked Step 2 Form, Error Toast (if invalid/expired).
Rules or Restrictions: OTP expires strictly in 10 minutes. Maximum 3 failed attempts before requiring resend.
Feature 1.2: Multi-Step Registration Request Submission & Document Upload Vault
Description: Collects personal and academic data while uploading proof documents (Diploma, TOR, ID) to Cloudinary.
Functionality: Guides applicants through a 3-step wizard (Personal -> Academic -> Proof Documents). Uploads PDF/JPG files to Cloudinary and submits request.
User/Actor: Verified Applicant / Unverified Graduate
System Behavior: The system processes and stores the documents submitted during registration. A registration request is created with an initial Pending status. The applicant can monitor the status and progress of their registration using their registered email address. 
Inputs and Outputs:
Inputs: Full Name, Contact Number, Gender, Course, Department, Graduation Year, Student ID (optional), Proof Document Files (PDF/JPG/PNG max 5MB).
Outputs: The registration application is submitted with a Pending status, and a confirmation is sent to the applicant’s registered email address. The applicant can monitor the application status using the same email address. 
Rules or Restrictions: File size limit 5MB per document. Mandatory fields required. Initial status set to PENDING.
Feature 1.3: Registration Request Status Tracking & Revision Resubmission
Description: Enables applicants to monitor request progress and upload revised documents if requested by officers.
Functionality: Allows applicants to check their registration status using their registered email address. The system displays the current application status, officer notes or feedback, and provides an option to upload revised documents when revisions are requested. 
User/Actor: Applicant / Unverified Graduate
System Behavior: The system retrieves the applicant’s registration record using their registered email address. If the application status is Revision Requested, the system enables the document upload function. Once the applicant submits the revised documents, the application is updated and its status is reset to Pending for further review. 
Inputs and Outputs:

Inputs: Registered Email, Revised Document Files.
Outputs: Status View, Officer Notes, Resubmission Confirmation.
Rules or Restrictions: Resubmitting automatically resets application status back to PENDING.
Feature 1.4: Administrative Registration Audit Queue & Decision Console
Description: Provides officers with a review console to inspect applications and proof documents side-by-side.
Functionality: Displays pending applications on /officer/alumni-verification. Officers review details and select Approve, Request Revision (with notes), or Reject (with reason).
User/Actor: Alumni Officer (ALUMNI_OFFICER)
System Behavior:
Approval: Upon approval, the system creates the applicant's alumni account, links the corresponding student and alumni records, updates the application status to Approved, and sends a welcome email containing the account credentials.
Revision: If revisions are required, the system updates the application status to Revision Requested and provides the applicant with the reviewing officer's notes or required corrections.
Rejection: If the application does not meet the requirements, the system updates the status to Rejected and provides the applicant with the reason for rejection.
Inputs and Outputs:
Inputs: Decision Action, Revision Notes / Rejection Reason, Assigned Student ID.
Outputs: Created or updated alumni account and records, updated registration status, and automated welcome email with account credentials. 
Rules or Restrictions: Restricted to ALUMNI_OFFICER. Approval requires linking a studentId.
Related Features: User Model, Student/Alumni Models, Nodemailer Service.


MODULE 2: AUTHENTICATION & PROFILE GOVERNANCE MODULE

Feature 2.1: Role-Based Authentication 
Description: Provides secure authentication methods based on the user's assigned role. Public Users can authenticate through Google Sign-In, while Alumni and Alumni Officers authenticate using their assigned unique username and password. 
Functionality: Provides separate sign-in gateways for Alumni and Alumni Officers, allowing users to securely authenticate and access system features based on their assigned roles and permissions. 
User/Actor: Public User, Alumni and Alumni Officer
System Behavior: 
-Public User: May sign in using Google Sign-In.
-Alumni: Sign in using their assigned unique username and password.
-Alumni Officer: Sign in using their assigned unique username and password.
Upon successful authentication, the system establishes a secure session and provides access to features authorized for the user's role.
Inputs and Outputs:
Inputs: Username/Email and Password for Alumni and Alumni Officers; Google account authentication for Public Users. 
Outputs: Secure authenticated session and role-based access to the appropriate system interface. 
Rules or Restrictions: Access to system features is restricted based on the user's assigned role. Alumni and Public Users cannot access administrative functions, which are exclusively available to authorized Alumni Officers. 

Feature 2.2: First-Time Alumni Onboarding 

Description: Guides newly verified graduates through the required onboarding process upon their first login. The onboarding allows users to provide a personal quote, which is displayed in the Interactive Yearbook. 
Functionality: Allows newly verified graduates to submit their personal quote during their first-time onboarding. 
User/Actor: Newly Verified Graduate / Alumni 
System Behavior: The system saves the submitted personal quote and associates it with the user's alumni record. Once the onboarding process is completed, the quote becomes available for display in the Interactive Yearbook. 
Inputs and Outputs:
Inputs: Personal Quote 
Outputs: Saved personal quote and completed onboarding status. 
Rules or Restrictions: Onboarding is required upon the user's first login.


MODULE 3: DIGITAL YEARBOOK & INTERACTIVE WRAPPED ENGINE

Feature 3.1: Batch Yearbook Catalog & Flipbook / Grid Viewer
Description: Displays graduation yearbooks categorized by batch year in responsive grid cards or interactive flipbook mode.
Functionality: Allows users to select a graduation batch, browse the corresponding graduate roster, filter graduates by department, and switch between Grid View and Flipbook View. 
User/Actor: Public Guest, Alumni, Alumni Officer 
System Behavior: The system displays published yearbook records for the selected graduation batch and applies the appropriate privacy settings to individual graduate information and photographs. 
Inputs and Outputs:
Inputs: Graduation Batch Selection, View Mode Selection (Grid/Flipbook), and Department Filter. 
Outputs: Digital Yearbook Gallery, Graduate Records, and Interactive Yearbook Pages. 
Rules or Restrictions: Only yearbooks that have been approved for publication are visible to Public Users and Alumni. Graduate information and photographs are displayed according to the individual's configured privacy settings. 
Feature 3.2: Multi-Slide Interactive "Yearbook Wrapped" Story Experience
Description: Plays an animated, interactive slide presentation that tells the story of a graduating batch through milestones, statistics, memories, photos, achievements, and historical highlights. 
Functionality: The system presents a customizable sequence of slides based on the available yearbook content. Administrators can add, remove, rearrange, or customize slides to create a unique "Yearbook Wrapped" experience for each batch. 
User/Actor: Public Guest, Alumni
System Behavior: Queries the configured WrappedSlide records according to their assigned position/order and dynamically displays the slides included in the batch's Wrapped Story. from approved yearbook records.
Inputs and Outputs:
Inputs: Play Wrapped Action, Slide Controls, Configured Wrapped Story Content. 
Outputs: Animated Story Slides, Batch Analytics Summary.
Rules or Restrictions: Officers can add, remove, edit, and rearrange slides and customize their content based on the batch's needs. The number, order, and type of slides are not strictly fixed. 

Feature 3.3: Graduate Personal Entry Customization

Description: Allows graduates to personalize their yearbook profile by adding or updating their quotes.
Functionality: Form on /yearbook/profile capturing personal quote.
User/Actor: Alumni 
System Behavior:Saves and updates the graduate’s yearbook profile. When significant changes are made, the updated information is returned to the officer for review and approval. 
Inputs and Outputs:
Inputs: Quote
Outputs: Saved YearbookEntry Record.
Rules or Restrictions: Quotes limited to 280 characters. Edits require officer approval before display.
Feature 3.4: Administrative Yearbook Moderation & Master Publishing Console
Description: Provides officers with tools to create yearbooks, configure Wrapped slides, and toggle publication.
Functionality: Provides a management page where officers can add and manage yearbook content, customize the Wrapped Story slides, and publish the completed yearbook for the batch. 
User/Actor: Alumni Officer.
System Behavior: Updates the yearbook and Wrapped Story information. Once the officer publishes the yearbook, it becomes available for graduates to view. 
Inputs and Outputs:
Inputs: Batch Year, Cover Graphic, Wrapped Content, Publish Toggle.
Outputs: Saved Yearbook Records, Published Live Batch.
Rules or Restrictions: Restricted to ALUMNI_OFFICER. Batch year must be unique.


MODULE 4: EVENT MANAGEMENT, RSVP & QR ATTENDANCE MODULE

Feature 4.1: Audience-Targeted Event Publishing & Proposal Submission
Description: Enables student leads and officers to submit event proposals targeting specific audience cohorts (ALL, DEPARTMENT, SECTION, GRAD_YEAR).
Functionality: Form on /officer/events collecting title, description, category, purpose, date, time, venue, target audience, organizer info, and cover image.
User/Actor: Alumni/Student Lead , Alumni Officer
System Behavior: Creates a new event record with a Pending status and records the initial submission in the event history for monitoring and review. 
Inputs and Outputs:
Inputs: Title, Description, Date, Time, Venue, Target Audience, Organizer Info, Cover File.
Outputs: Newly Created Event Record with a Pending status and a corresponding entry in the event history. 
Rules or Restrictions: The event date must be scheduled for a future date. The event may be designated for all alumni, a specific section, department, or graduating year.
Feature 4.2: Event Proposal Multi-Level Audit Review & Document Vault (EventDocument)
Description: Audit console for officers to review event proposals, inspect budget spreadsheets/letters, and execute approvals.
Functionality: Allows officers to review pending event submissions through the event management page, including the attached supporting documents such as the budget plan, event proposal, and letter of request. Officers can approve the event, request revisions, or reject the submission. 
User/Actor: Alumni Officer
System Behavior: Updates the event’s review status and records the date of review. Each review decision is also recorded in the event history for proper documentation and monitoring.
Inputs and Outputs:
Inputs: Review decision, reviewer’s notes or reason for requesting revisions or rejecting the event, and attached event documents.
Outputs:Updated event record, documented review history, and publication of approved events. 
Rules or Restrictions: Access is limited to authorized alumni officers. All changes to an event’s status are recorded in the event history for proper documentation and accountability. 

Feature 4.3: 1-Click RSVP & Cryptographic QR Pass Generation

Description: Registers alumni for upcoming events with a single click, issuing a cryptographic mobile QR code pass.
Functionality: When an eligible graduate selects “RSVP Now” on the Events page, the system records their attendance and provides a unique QR code as their event pass. 
User/Actor: Alumni
System Behavior: Ensures that each graduate can register for an event only once. The system updates the event’s attendee count and, if the graduate has already registered, displays their existing QR code instead of creating a new one. 
Inputs and Outputs:
Inputs: RSVP request from the graduate. 
Outputs: Confirmed event registration, unique QR code, and mobile event pass. 
Rules or Restrictions: 
Feature 4.4: Live Web Camera QR Attendance Scanner & Duplicate Check-in Prevention
Module Name: Event Management, RSVP & QR Attendance Module
Feature Name: Live Web Camera QR Attendance Scanner & Duplicate Check-in Prevention
Description: Allows officers to scan mobile QR passes at venue entrances using a web browser camera, logging attendance timestamps and preventing duplicate check-ins.
Functionality: Allows officers to use a web camera scanner on the event management page to scan attendees’ QR codes and verify their event passes. 
User/Actor: Alumni Officer
System Behavior: Verifies the attendee’s QR code and records their check-in during the first successful scan. A successful check-in displays a confirmation message. If the same QR code is scanned again, the system identifies the existing attendance record and displays a warning that the attendee has already checked in, including the time of the initial check-in.
Inputs and Outputs:
Inputs: Scanned QR code through the web camera. 
Outputs:Recorded event attendance and a notification confirming a successful or duplicate check-in. 
Rules or Restrictions: Each graduate can be checked in only once per event. The system prevents duplicate attendance records for the same graduate and event.


MODULE 5: SUPPORT HELP DESK & KNOWLEDGE BASE MODULE
Feature 5.1: Searchable FAQ Knowledge Base (FaqItem)
Description: Self-service knowledge center where visitors search indexed frequently asked questions by category.
Functionality: Search FAQs on /help categorized into Account, Events, General, Technical, Yearbook, and Other, featuring accordion answer expanders.
User/Actor: Public Guest, Alumni, Alumni Officer
System Behavior: Displays published frequently asked questions in the designated order. Users can search for specific information by entering keywords, and the system matches the search with the available questions and answers. 
Inputs and Outputs:
Inputs: Search Keyword Query, Category Selection.
Outputs: Filtered FAQ Accordion List.
Rules or Restrictions: Only FAQs that have been approved for publication are visible to users. 

Feature 5.2: Support Ticket Submission & Reference Tracking (TCK-XXXXX)

Description: Enables users to submit support tickets when FAQs do not resolve their issue, generating a unique tracking reference code (TCK-XXXXX).
Functionality: Form collects User Type, Category, Name, Email, Phone, and Description. Generates reference code (e.g. TCK-10293).

User/Actor: Authenticated Alumni
System Behavior: Verifies that the user is logged in or prompts them to sign in. Upon submission, the system creates a support request with an Open status and generates a unique tracking reference code.

Inputs and Outputs:

Inputs: User Type, Category,Name, Email, Phone Number, and Description. 
Outputs: Submitted Support Request and a unique tracking reference code (TCK-XXXXX). 
Rules or Restrictions: Requires authenticated account session to prevent ticket spam. ticketNumber must be globally unique.
Feature 5.3: Multi-Threaded Ticket Messaging & Officer Help Desk Console
Description: Facilitates conversation threads between users and officers, managing ticket status lifecycle (OPEN, IN_PROGRESS, RESOLVED, CLOSED).
Functionality: Allows users to search for their support requests using the tracking number and email address, view responses, and send additional messages. Officers can manage support requests through the Help Desk by assigning them to an officer, responding to users, and updating the status of each request. 
User/Actor: Alumni, Alumni Officer 
System Behavior: Records each response as part of the support request history, updates the request status when necessary, and sends an email notification to the user when an officer provides a response. 
Inputs and Outputs:
Inputs: Ticket Number & Email, Message Text, Assigned Officer ID, Status Change.
Outputs: Recorded Support Response, Updated Ticket Status, and Email Notification. 
Rules or Restrictions: Only assigned officers can alter ticket status. Messages rendered chronologically.


MODULE 6: SYSTEM ANNOUNCEMENTS & REAL-TIME NOTIFICATIONS

Feature 6.1: Announcement 
Description: Allows officers to publish styled announcements with custom color themes, and cover graphics.
Functionality: Provides a form on /officer/announcements where officers can create and manage announcements by entering the title and content, uploading a cover image, and customizing the banner and date badge colors. 
User/Actor: Alumni Officer
System Behavior:Creates or updates announcement records. Active announcements designated as featured are displayed prominently at the top of the student dashboard.
Inputs: Title, Body, Cover Graphic, Banner Color, Date Badge Color.
Outputs: Saved Announcement Record and Published Featured Banner on the student dashboard.
Rules or Restrictions: Restricted to ALUMNI_OFFICER 
Feature 6.2: Automated Real-Time System Notification Dispatch (Notification)
Description: Delivers instant in-app alerts informing users of registration status updates, ticket replies, event updates, and announcements.
Functionality: Provides a notification bell with an unread notification counter. When a user selects a notification, it is marked as read and the user is redirected to the appropriate page.
User/Actor: Alumni, Alumni Officer
System Behavior: Creates and delivers notifications to the intended users based on the type of notification, such as announcements, events, memories, or system updates. Notifications are sent to the appropriate users in real time. 
Inputs and Outputs:
Inputs: Trigger Event (e.g. Ticket Replied, Registration Approved), Notification Click.
Outputs: Created notification record, real-time notification alert, and updated unread notification count.
Rules or Restrictions: Notifications are visible only to the specific user they are intended for. 




5. BUSINESS RULES AND SYSTEM STATE MACHINES
5.1 Registration Request Audit State Machine
   
5.2 Event Proposal Approval State Machine
  
5.3 Support Ticket Lifecycle

   
5.4 Relational Database Entity Reference (Prisma Schema)

| Database Entity | Primary Key / Index | Core Fields & Data Types | Relationships |
|---|---|---|---|
| **User** | `id` (cuid) | `username`, `password`, `email`, `role` (enum) | Alumni, AlumniOfficer, Event, SupportTicket |
| **Alumni** | `id` (cuid) | `studentId`, `firstName`, `lastName`, `department`, `course`, `graduationYear` | User, YearbookEntry, EventRSVP, EventAttendance |
| **AlumniRegistrationRequest** | `id` (cuid) | `fullName`, `email`, `course`, `department`, supporting documents, `status` | User (`processedBy`) |
| **EmailVerification** | `id` (cuid) | `email`, `otp`, `expiresAt`, `verified` | Independent Pre-verification |
| **Yearbook** | `id` (cuid) | `batch` (unique), `title`, `coverPhotoUrl`, `isPublished` | YearbookEntry, WrappedSlide |
| **WrappedSlide** | `id` (cuid) | `yearbookId`, `position` (1–4), `title`, `description`, `imageUrl` | Yearbook |
| **YearbookEntry** | `id` (cuid) | `yearbookId`, `alumniId`, `quote`, `motto`, `isPhotoPublic`, `isApproved` | Yearbook, Alumni |
| **Event** | `id` (cuid) | `title`, `date`, `venue`, `audienceType`, `status`, `rsvpCount` | EventAuditLog, EventDocument, EventRSVP, EventAttendance |
| **EventDocument** | `id` (cuid) | `eventId`, `fileName`, `fileUrl`, `documentType` | Event, User |
| **EventRSVP** | `id` (cuid) | `eventId`, `alumniId`, `qrToken` (unique) | Event, Alumni |
| **EventAttendance** | `id` (cuid) | `eventId`, `alumniId`, `checkedInAt`, `scannedByOfficerId` | Event, Alumni, AlumniOfficer |
| **SupportTicket** | `id` (cuid) | `ticketNumber` (unique), `email`, `userType`, `category`, `status` | User, TicketResponse |
| **TicketResponse** | `id` (cuid) | `ticketId`, `message`, `isOfficer`, `authorId` | SupportTicket, User |
| **Announcement** | `id` (cuid) | `title`, `body`, `bannerColor`, `isActive` | Independent Dashboard |
| **Notification** | `id` (cuid) | `userId`, `type`, `title`, `message`, `redirectPath`, `read` | User |



6. SYSTEM VALIDATIONS, EDGE CASES & NOTIFICATIONS
   
Unverified Route Protection:
Access to protected pages such as the dashboard, profile, and officer pages is restricted to authenticated and authorized users. Users who are not logged in or do not have the required access are redirected to the appropriate page.

Duplicate RSVP Safeguard:
Each graduate may register for an event only once. If a graduate attempts to register again, the system prevents a duplicate registration and displays their existing event pass.

QR Code Replay and Double-Scanning Prevention:
Each event pass can be used for attendance verification only once. If the same pass is scanned again, the system informs the officer that attendance has already been recorded and displays the original check-in details.

OTP Expiration Window:
Registration verification codes are valid for 10 minutes. Expired codes are rejected, and the user is prompted to request a new verification code.

Data Privacy Protection:

Users who choose to keep their contact information private will have their email address, contact number, and address hidden from public directories.
Users who choose to keep their photo private will have their profile photo hidden and replaced with a neutral placeholder in public yearbook displays.

First-Time Setup

Prerequisites
Node.js 18+ (with npm)
MySQL 8.0+ (via Laragon, XAMPP, or standalone)
Step-by-step
1. Clone and install dependencies
cd alumni-management-system
npm install

2. Create the database
Open Laragon → Start MySQL → click Root Terminal and run:

CREATE DATABASE IF NOT EXISTS alumni_system;
Or use phpMyAdmin / any MySQL client.

3. Configure environment
Create .env file and edit — ensure DATABASE_URL points to your MySQL instance:

DATABASE_URL="mysql://root:@localhost:3306/alumni_system"
AUTH_SECRET="your-super-secret-key-change-in-production"
AUTH_URL="http://localhost:3000"

# OAuth providers (required for general user login)
AUTH_GOOGLE_ID="your-google-client-id"
AUTH_GOOGLE_SECRET="your-google-client-secret"
AUTH_FACEBOOK_ID="your-facebook-app-id"
AUTH_FACEBOOK_SECRET="your-facebook-app-secret"

# Registrar API (required for Officer → Graduates sync)
REGISTRAR_API_URL="https://college.mymvc.app/api/graduate"

CLOUDINARY_CLOUD_NAME="your-cloudinary-cloud-name"
CLOUDINARY_API_KEY="your-cloudinary-api-key"
CLOUDINARY_API_SECRET="your-cloudinary-api-secret"

SMTP_HOST="smtp.gmail.com"
SMTP_PORT="587"
SMTP_USER="mvc.alumni.portal@gmail.com"
SMTP_PASS="eqdv tyyr blhf inre"  
SMTP_FROM="mvc.alumni.portal@gmail.com"

For Laragon, root with empty password is the default. If you use a password, include it: mysql://root:yourpass@localhost:3306/alumni_system.

OAuth setup: Create credentials in Google Cloud Console and Facebook Developers. Set authorized redirect URIs to http://localhost:3000/api/auth/callback/google and http://localhost:3000/api/auth/callback/facebook.

4. Push schema and seed data
npx prisma generate
npx prisma db push
npm install preact
npm install framer-motion@11
Remove-Item -Recurse -Force .next
npx prisma db seed
This creates all tables, an officer account, 3 demo student/alumni accounts (for alumni-portal login testing), Help Center FAQs, and 3 progression tiers (Bronze, Silver, Gold) with 10 sample quests. Yearbooks and real graduate lists are not seeded — create yearbooks from the officer dashboard and sync graduates from the registrar.

5. Start the dev server
npm run dev
Open http://localhost:3000 in your browser.

