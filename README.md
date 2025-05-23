# SkillVerse - Documentation

## Contents

1. [Overview](#overview)
2. [System Architecture](#system-architecture)
3. [Setup & Installation](#setup--installation)
4. [Authentication & User Management](#authentication--user-management)
5. [Core Features](#core-features)
6. [Technical Implementations](#technical-implementations)
7. [Security](#security)
8. [Deployment](#deployment)
9. [Known Isuues](#known-issues)

## Overview

SkillVerse is a comprehensive freelance marketplace platform that connects clients with freelancers across various service categories. It is a platform that facilitates job posting, job applications, milestone tracking, file sharing, and secure payment processing. The backend is built with Node.js, Express, and MongoDB, providing RESTful API endpoints for the frontend to interact with. The frontend is built with React and Bootstrap CSS, providing users with a responsive, easy-to-navigate, modern UI.

## System Architecture

The app is built using the MERN stack - MongoDB, Express, React, and Node.js. Specific technology stacks for the frontend and backend are given below.

**Frontend**

- React 18+ with functional components and hooks
- React Router for client-side navigation
- Axios for HTTP requests with credentials
- React Bootstrap for UI components
- Bootstrap CSS for modern styling

**Backend**

- Node.js with Express.js framework
- MongoDB with Mongoose ORM
- Passport.js with Google OAuth 2.0
- Multer for file management
- Express-session with MongoStore

**External Services**

- Google OAuth for user authentication
- Paystack for payment processing
- Azure Web Services for hosting

## Setup & Installation

### Prerequisites

- Node.js (v16.0.0 or higher)
- MongoDB (v5.0 or higher)
- npm or yarn
- Google OAuth credentials
- Paystack API keys

### Environment Variables

#### In the backend directory, create a `.env` file with the following variables:

```env
MONGO_URI:<your-mongo-uri>
PORT:<port>
GOOGLE_CLIENT_ID:<your-google-client-id>
GOOGLE_CLIENT_SECRET:<your-google-client-secret>
COOKIE_KEY:<your-cookie-key>
PAYSTACK_SECRET_KEY:<your-paystack-secret-key>
```

#### Setup For Local Development

1. **Clone the Repository**

   ```bash
   git clone https://github.com/SkillVerser/freelancer-management-platform.git
   cd freelancer-management-platform
   ```

2. **Install Dependencies**

   ```bash
   # Install backend dependencies
   cd backend
   npm install
   ```
   ```bash
   # Install frontend dependencies
   cd frontend
   npm install
   ```

3. **Start Development Servers**

   ```bash
   # Start backend server
   cd backend
   npm run dev
   ```
   ```bash
   # Start frontend server
   cd frontend
   npm start
   ```

4. **Visit Site**

   - Frontend is hosted at `localhost:3000` by default

## Authentication & User Management

### Google OAuth Implementation

The app makes use of Passport.js with the Google OAuth 2.0 strategy for user authentication. Users sign up and login via Google, and no sensitive information, such as emails or passwords, are stored in the database.

### Authentication Flow

1. User initiates Google OAuth login
2. Authentication callback processes user data
3. New users: redirect to role selection (client / freelancer)
4. Existing users: redirect to role-appropriate dashboard
5. Session persistence with MongoDB store and express-session

### User Roles & Permissions

**Clients**

- Post service requests different categories
- Review freelancer applications
- Create and track project milestones with payment integration
- Access project files and monitor progress
- View comprehensive job dashboard with progress indicators

**Freelancers**

- Browse and apply for available projects
- Manage profile, skills, and portfolio
- Upload deliverables and update project progress in real-time
- View accepted jobs dashboard with project details

**Admins**

- Monitor comprehensive platform statistics and analytics
- Manage user accounts and role change requests
- Oversee all job listings
- Process support tickets and user issues
- Delete user accounts and manage permissions

### Session Management

Sessions are managed using `express-session` with MongoDB persistence. This ensures scalability across multiple server instances.

```javascript
app.use(
  session({
    secret: process.env.COOKIE_KEY,
    resave: false,
    saveUninitialized: false,
    cookie: {
      maxAge: 24 * 60 * 60 * 1000,
      sameSite: process.env.NODE_ENV === "production" ? "none" : "lax",
      secure: process.env.NODE_ENV === "production",
    },
    store: MongoStore.create({
      mongoUrl: process.env.MONGO_URI,
      collectionName: "sessions",
    }),
  })
);
```

## Core Features

### Service Request System

The service request system forms the core of the marketplace, allowing clients to post jobs and freelancers to apply for them.

#### Service Categories

The platform supports five primary service categories:

1. **Software Development**: Custom application and web development
2. **Data Science**: Analytics, machine learning, and data processing
3. **Creating Logos**: Brand identity and logo design
4. **Graphic Designer**: Visual design and creative services
5. **Digital Marketing**: SEO, social media, and marketing campaigns

### Job Application System

#### Application Process (Freelancer)

1. **Browse Jobs**: View available job listings with filtering options
2. **Job Selection**: Choose a job to apply for with detailed requirements
3. **Application Form**: Submit cover letter and proposed fee
4. **Validation**: Form validation with error handling
5. **Confirmation**: Receive success/error feedback
6. **Tracking**: Monitor application status and responses

#### Application Review (Client)

- View all applications for posted jobs
- Review freelancer profiles and proposed fees
- Accept suitable applications with confirmation workflow
- Navigate to project management upon acceptance

### Milestone Management System

Milestones break large projects into manageable chunks and provide clear progress tracking for both parties.

#### Milestone Features

- **Dynamic Creation**: Add/remove milestones during project setup
- **Payment Integration**: Milestone-based payments
- **Date Management**: Due date tracking with formatted display
- **Status Management**: Track completion status with visual indicators

### Project Management Dashboard

The dashboard provides comprehensive project oversight with real-time updates and intuitive navigation, to both clients and freelancers.

#### Client Dashboard Features

- **Job Overview**: Grid display of active projects with status indicators
  - **Payment Tracking**: Outstanding payments and payment history
  - **Milestone Management**: Create and track project milestones
  - **File Access**: Access deliverables uploaded by freelancer
- **Application Management**: Review and manage freelancer applications
- **Progress Monitoring**: Real-time project status updates and completion percentages

#### Status Indicators

- **No Freelancer Assigned**: "View Applications" button
- **Freelancer Assigned**: "View Details" button for project management
- **Progress Visualization**: Completion percentages
- **Payment Status**: Amount owed calculations and payment buttons

#### Freelancer Dashboard Features

- **Active Projects**: Display of accepted jobs with client information
- **Project Details**: Access to job requirements and milestone information
  - **File Management**: Upload and manage project deliverables
  - **Progress Updates**: Mark milestones as complete and update status
  - **Payment Information**: View received and outstanding payments

## Technical Implementations

This section provides the technical implementations of various aspects of the app in the form of code snippets.

### Contents

- [Database Models](#database-models)
- [Core Features Implementation Details](#core-features-implementation-details)
- [API Endpoints](#api-endpoints)
  - [Admin Routes](#admin-routes)
  - [Application Routes](#application-routes)
  - [Authentication Routes](#authentication-routes)
  - [File Routes](#file-routes)
  - [Milestone Routes](#milestone-routes)
  - [Payment Routes](#payment-routes)
  - [Service Request Routes](#service-request-routes)
  - [User Routes](#user-routes)

### Database Models

The following are the core models of the system. There are others not shown, but they are are significantly less importance

```javascript
//Application Model
{
  jobId: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "ServiceRequest",
    required: true,
  },
  freelancerId: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User",
    required: true,
  },
  coverLetter: {
    type: String,
    required: false,
  },
  price: {
    type: Number,
    required: true,
  },
  status: {
    type: String,
    default: "Pending",
    enum: ["Pending", "Accepted", "Rejected"],
  },
  createdAt: {
    type: Date,
    default: Date.now,
  },
}

//File Model
{
    jobId: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "ServiceRequest",
      required: true,
    },
    filename: {
      type: String,
      required: true,
    },
    originalName: {
      type: String,
      required: true,
    },
    fileSize: {
      type: Number,
      required: true,
    },
    uploadedBy: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "User",
      required: true,
    },
    contentType: {
      type: String,
      required: true,
    },
    storagePath: {
      type: String,
      required: true,
    },
}

//Milestone Model
{
    jobId: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "ServiceRequest",
      required: true
    },
    description: {
      type: String,
      required: true
    },
    dueDate: {
      type: Date,
      required: true
    },
    status: {
      type: String,
      enum: ["Pending", "Completed"],
      default: "Pending"
    }
}

//ServiceRequest Model
{
    clientId: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "User",
      required: true,
    },
    serviceType: {
      type: String,
      required: true,
    },
    freelancerId: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "User",
      default: null,
    },
    status: {
      type: String,
      enum: ["Pending", "Accepted", "Completed", "In Progress"],
      default: "Pending",
    },
    progressActual: {
      type: Number,
      default: 0,
    },
    progressPaid: {
      type: Number,
      default: 0,
    },
    price: {
      type: Number,
      default: 0,
    },
    paymentsMade: {
      type: Number,
      default: 0,
    },
    paymentsPending: {
      type: Number,
      default: 0,
    },
}

//User Model
{
    googleID: {
      type: String,
      required: true,
      unique: true,
    },
    username: {
      type: String,
      required: true,
      unique: false,
    },
    role: {
      type: String,
      enum: ["client", "freelancer", "admin"],
    },
}
```

### Core Features Implementation Details

#### Service Request Workflow

1. **Service Request Creation**

   ```javascript
   const handleServiceSelection = async (category) => {
     const response = await axios.post(
       `${API_URL}/api/service-requests/create`,
       {
         clientId: user._id,
         serviceType: category,
         description: `Request for ${category}`,
       },
       { withCredentials: true }
     );
   };
   ```

2. **Application Submission**

   ```javascript
   const handleSubmitApplication = async (e) => {
     e.preventDefault();
     await axios.post(
       `${API_URL}/api/service-requests/applications`,
       {
         jobId,
         freelancerId: user._id,
         coverLetter,
         proposedFee: parseFloat(fee),
       },
       { withCredentials: true }
     );
   };
   ```

3. **Accept Application**

   ```javascript
   const handleAcceptConfirmed = async () => {
     try {
       await axios.post(
         `${API_URL}/api/applications/jobs/accept/${pendingApplicationId}`,
         {},
         { withCredentials: true }
       );
       setShowConfirmModal(false);
       setShowSuccessModal(true);
     } catch (error) {
       console.error("Error accepting application:", error);
       alert("Error accepting application");
     }
   };
   ```

4. **Milestone Completion Update**
   ```javascript
   const handleMilestoneComplete = async (milestoneId) => {
     const res = await fetch(
       `${API_URL}/api/milestones/complete/${milestoneId}`,
       {
         method: "PUT",
         credentials: "include",
       }
     );
   };
   ```

#### File Management

- File Upload Configuration

  ```javascript
  const storage = multer.diskStorage({
    destination: function (req, file, cb) {
      const uploadDir = path.join(__dirname, "../uploads");

      if (!fs.existsSync(uploadDir)) {
        fs.mkdirSync(uploadDir, { recursive: true });
      }

      cb(null, uploadDir);
    },
    filename: function (req, file, cb) {
      const uniqueName =
        Date.now() + "-" + file.originalname + path.extname(file.originalname);
      cb(null, uniqueName);
    },
  });

  const upload = multer({
    storage: storage,
    limits: { fileSize: 5 _ 1024 _ 1024 },
  });
  ```

#### Milestone-based Payment Calculation

```javascript
const { progressActual, progressPaid } = serviceRequest;
const progressDelta = progressActual - progressPaid;
const amountDue = (progressDelta / 100) * serviceRequest.price;
```

#### Project Dashboard

- Client

  ```javascript
  const fetchJobs = async () => {
    if (!userId || userId === null) {
      console.error("User ID is not available or null");
      return;
    }
    try {
      const res = await fetch(
        `${API_URL}/api/service-requests/client/jobs/${userId}`,
        {
          method: "GET",
          credentials: "include",
        }
      );

      if (res.ok) {
        const data = await res.json();
        setJobs(data);
        setError("");
        setSuccess("Jobs fetched successfully!");
        console.log(success);
      } else {
        setError("Failed to fetch jobs");
        console.log(error);
      }
    } catch (err) {
      setError("Error fetching jobs: " + err.message);
    }
  };
  ```

- Freelancer

  ```javascript
  const fetchAcceptedJobs = async (freelancerId) => {
    try {
      const res = await fetch(
        `${API_URL}/api/service-requests/freelancer/jobs/${freelancerId}`,
        {
          credentials: "include",
        }
      );

      if (res.ok) {
        const jobsData = await res.json();
        setAcceptedJobs(jobsData);
        console.log("Accepted jobs:", jobsData);
      } else {
        console.error("Failed to fetch accepted jobs");
      }
    } catch (err) {
      console.error("Error fetching accepted jobs:", err);
    } finally {
      setLoading(false);
    }
  };
  ```

### API Endpoints

#### Admin Routes

| Method | Endpoint           | Description                                 |
| ------ | ------------------ | ------------------------------------------- |
| GET    | `/api/admin/stats` | Get platform statistics for admin dashboard |

#### Application Routes

| Method | Endpoint                                       | Description                       |
| ------ | ---------------------------------------------- | --------------------------------- |
| GET    | `/api/applications/jobs/:jobId`                | Get all applications for a job    |
| POST   | `/api/applications/jobs/accept/:applicationId` | Accept a freelancer's application |

#### Authentication Routes

| Method | Endpoint                | Description                                     |
| ------ | ----------------------- | ----------------------------------------------- |
| GET    | `/auth/google`          | Initiates Google OAuth authentication           |
| GET    | `/auth/google/callback` | Google OAuth callback URL                       |
| GET    | `/auth/logout`          | Logs out the user                               |
| GET    | `/auth/me`              | Returns the currently authenticated user's data |

#### File Routes

| Method | Endpoint                      | Description              |
| ------ | ----------------------------- | ------------------------ |
| POST   | `/api/files/upload/:jobId`    | Upload a file for a job  |
| GET    | `/api/files/job/:jobId`       | Get all files for a job  |
| GET    | `/api/files/download/:fileId` | Download a specific file |
| DELETE | `/api/files/delete/:fileId`   | Delete a file            |

#### Milestone Routes

| Method | Endpoint                                | Description                   |
| ------ | --------------------------------------- | ----------------------------- |
| POST   | `/api/milestones/:jobId`                | Create milestones for a job   |
| GET    | `/api/milestones/job/:jobId`            | Get all milestones for a job  |
| PUT    | `/api/milestones/complete/:milestoneId` | Mark a milestone as completed |

#### Payment Routes

| Method | Endpoint                            | Description                                         |
| ------ | ----------------------------------- | --------------------------------------------------- |
| POST   | `/payments/create-checkout-session` | Create a Paystack checkout session                  |
| POST   | `/webhook/paystack`                 | Webhook endpoint for Paystack payment notifications |

#### Service Request Routes

| Method | Endpoint                                    | Description                                 |
| ------ | ------------------------------------------- | ------------------------------------------- |
| POST   | `/api/service-requests/create`              | Create a new service request                |
| GET    | `/api/service-requests/all`                 | Get all available service requests          |
| POST   | `/api/service-requests/applications`        | Submit an application for a service request |
| GET    | `/api/service-requests/client/jobs/:id`     | Get all jobs posted by a client             |
| GET    | `/api/service-requests/manage-jobs`         | Get all service requests (admin function)   |
| GET    | `/api/service-requests/freelancer/jobs/:id` | Get all jobs assigned to a freelancer       |
| GET    | `/api/service-requests/job/:id`             | Get details of a specific job               |

#### User Routes

| Method | Endpoint                          | Description                           |
| ------ | --------------------------------- | ------------------------------------- |
| POST   | `/users`                          | Create a new user                     |
| PUT    | `/users/:id`                      | Update a user                         |
| GET    | `/users`                          | Get all users (admin function)        |
| DELETE | `/users/:id`                      | Delete a user (admin function)        |
| POST   | `/users/create-user`              | Create or update user profile details |
| POST   | `/users/set-role`                 | Set a user's role after signup        |
| POST   | `/users/request-role-change`      | Request a role change                 |
| GET    | `/users/alltickets`               | Get all pending role change requests  |
| POST   | `/users/process-request`          | Process a role change request         |
| GET    | `/users/freelancer/:freelancerId` | Get freelancer details                |
| GET    | `/users/profile/:userId`          | Get a user's profile details          |

## Security

### Security Measures

- 3rd-Party authentication via Google OAuth for secure authentication
- Session encryption with `express-session` and MongoDB persistence
- Paystack webhook for signature validation for payments
- Secure and unique file naming and file upload restrictions
- Role-based access control
- CORS protection for cross-origin requests

## Deployment

### Azure Web Services

This app has been deployed via an Azure Static Web App, and be accessed from this URL: https://lively-coast-034460503.6.azurestaticapps.net/

## Known Issues

Currently, the file sharing system makes use of local storage on the server. In a real-world app, this presents persistence issues, as the contents in the server's local storage would be lost upon server restarts. Additionally, data in local storage is not shared across instances, which vastly limits the scalability of the app. \
Ideally, file sharing would be implemented using Azure Blob Storage. This has several benefits:

- Scalable: Can store much more data
- Accessible: Files are available to all instances of the application
- Resilient: Files aren't affected by server restarts or scaling events

However, since this app was built as a university project, and is not intended for real-world use, the local storage implementation, which is suitable for demonstration purporses, is sufficent.
