# THE OG CAREER CONNECT: BUT NOW SECURED 🕵🏼‍♀️✨


## 1.Problem Statement
The application tracking system lacked a defensive layer against automated API abuse and Denials of Service (DoS). A malicious actor or bot could flood the application endpoints (/apply, /status, /get) with unlimited rapid requests, potentially crashing the server or polluting the database with spam records.

## Tools Used
Node.js & Express.js (Backend Framework)

express-rate-limit (Security Middleware Package)

VS Code (Code Editor)

## Solution
Implemented Rate Limiting at the routing layer. This serves as an automated gatekeeper that counts incoming requests per IP address and blocks execution if a threshold is crossed, protecting the controller logic from handling malicious traffic.

## Code Snippet
JavaScript

// 1. Imported the defensive package
import rateLimit from "express-rate-limit";

// 2. Created the protective rule
const applicationLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes window
  max: 20, // Limit each IP to 20 actions per window
  message: { message: "Too many application requests. Please try again later." }
});

// 3. Injected the rule as a checkpoint before execution
router.route("/apply/:id").get(isAuthenticated, applicationLimiter, applyJob); 
router.route("/get").get(isAuthenticated, applicationLimiter, getAppliedJobs);
router.route("/:id/applicants").get(isAuthenticated, applicationLimiter, getApplicants); 
router.route("/status/:id/update").post(isAuthenticated, applicationLimiter, updatestatus);

Meaning of the Code:

windowMs and max: Restricts any single user/IP to a maximum of 20 requests every 15 minutes.

applicationLimiter placement: Injected directly before the controller actions (applyJob, updatestatus) to trap and turn away unauthorized flood traffic at the door without using up server processing power.

## Lessons Learnt
Scanners have Blindspots: Automated tools like SonarQube look for syntax errors, but they miss logical architecture flaws like missing rate limits or open API spam risks.

Defense in Depth: Security should be placed at the entry layer (Routes) rather than the database layer (Models) to save server computing resources during an automated incident.

Non-Breaking Enhancements: Security architecture can be seamlessly integrated into existing functional code without modifying the core business logic or disrupting the user experience.



## 2. Problem Statement
The application had an authentication check (isAuthenticated) but completely lacked an authorization check. While it verified who a user was, it never verified what permissions they had. This meant any logged-in user (like a Candidate) could bypass the user interface and send automated requests directly to backend endpoints to delete jobs or view private data reserved for Recruiters.

 ## Tools Used
VS Code: For locating, isolation, and refactoring backend logic.

SonarQube for IDE: To audit code structure, analyze bugs, and ensure standard clean code practices.

npm audit: To detect and patch vulnerable open-source dependencies in the project's ecosystem.

## Code Snippet
A. In middleware/isAuthenticated.js
The Change:

JavaScript

// Old Line:
req.id = decode.userId;

// New Lines:
req.id = decode.userId;
req.role = decode.role; // <-- Added
Meaning: Extracts both the user ID and their account type ('Candidate' or 'Recruiter') from the login token, saving them to the incoming request object so the backend knows their account tier.

The Addition:

JavaScript

// Added to the bottom of the file:
export const authorizeRole = (requiredRole) => {
    return (req, res, next) => {
        if (!req.role || req.role !== requiredRole) {
            return res.status(403).json({
                message: `Access Denied: Requires a ${requiredRole} account.`,
                success: false
            });
        }
        next();
    };
};
Meaning: A brand-new security guard function. It intercepts requests, checks the req.role we saved above, and blocks the request with a 403 Access Denied error if the user's role does not match what the page requires.

B. In the API Routes (e.g., routes/jobRoutes.js)
The Change:

JavaScript

// Old Route (Vulnerable):
router.delete("/delete/:id", isAuthenticated, deleteJob);

// New Route (Secure):
router.delete("/delete/:id", isAuthenticated, authorizeRole("Recruiter"), deleteJob);
Meaning: Upgraded the endpoint into a two-step checkpoint. It now forces the system to first verify the user is logged in (isAuthenticated), and then verifies they are explicitly a 'Recruiter' (authorizeRole) before running the data deletion logic.

## Lessons Learnt
Authentication is not Authorization: Verifying a user's identity is completely separate from verifying what actions they are allowed to perform.

Scanners Miss Logic Flaws: Automated security scanners look for syntax errors, but manual code analysis is required to catch design weaknesses like missing permission gates.

















