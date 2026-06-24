# THE OG CAREER CONNECT: BUT NOW SECURED 🕵🏼‍♀️✨
# Introducing Project Kirata 🛡⚔
Inspired by Lord Shiva's Avatar, Kirata, a tribal hunstsmen to test Arjuna's warrior skills in the epic Mahabharata. This project involves finding vulnerabilties in Career Connect and resolving them to safegaurd our website from cyber-attacks.

## 1.Problem Statement
The application tracking system lacked a defensive layer against automated API abuse and Denials of Service (DoS). A malicious actor or bot could flood the application endpoints (/apply, /status, /get) with unlimited rapid requests, potentially crashing the server or polluting the database with spam records.

## Tools Used
Node.js & Express.js (Backend Framework)
MongoDB (Backendend Framework)
JavaScript (Backend Framework)
express-rate-limit (Security Middleware Package)
SonarQube (Vulnerability Finder)
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



## 3. Problem Statement

The platform lacked an automated mechanism to detect, isolate, and contain malicious data or fraudulent listings targeting vulnerable users. Without a system to track user-driven threat indicators in real-time, the portal was vulnerable to hosting phishing infrastructure, deceptive credential-harvesting schemes, and advance-fee recruitment scams. Administrative intervention was completely manual, meaning a malicious listing could remain active indefinitely, continuously scaling its blast radius before a human operator could review and remove it.

## Tools Used

 **Node.js & Express Router:** For engineering stateful arrays and logic sequences to track and increment reporting metrics tied to unique database entries.
* **VS Code:** Used to draft, isolate, and test the conditional conditional threshold statements.

## Lessons learnt

* Shifting from manual oversight to an automated, crowdsourced detection pipeline allows the application to function as an agile, application-layer intrusion detection system (IDS) that traps threats early. * Designing a two-tiered threshold protocol (Triage/Quarantine at 4-5 reports, and Visibility Revocation at a higher threshold) proves that system automation can neutralize threats instantly without permanently destroying raw database integrity before a formal review.
* Injecting complex database object manipulation and asset hiding states directly through basic local routing templates can cause rendering engine conflicts, highlighting the need to offload heavy state-tracking to an isolated caching layer in the future.


## 4. Problem Statement

The application's authentication interface left plain-text passwords fully visible on screen during the registration and login flows. This lack of user interface masking introduced a critical vulnerability to local physical observation, commonly known as a shoulder-surfing attack. In public, semi-public, or shared university environments like computer labs, any nearby actor or local surveillance mechanism could visually harvest a user's raw credentials, leading to subsequent unauthorized account takeovers.

## Tools Used

 **HTML5 :** For modifying frontend form controls and specifying secure input attributes.
 **React.js :** Used to target authentication components and manage secure field states.
 **VS Code:** Used for isolating structural interface code and validating input field configuration parameters.

 ## Code Snippet 
javascript
const hashedPassword = await bcrypt.hash(password, 10);

await User.create({
    fullname,
    email,
    phoneNumber,
    password: hashedPassword,
    role,
    profile:{
        profilePhoto:cloudResponse.secure_url,
    }
});

## Lessons Learnt

* Defensive engineering must extend beyond backend database encryption; protecting data while it is in transit or actively rendered on a user's screen is just as vital to maintaining overall system integrity.
* Utilizing proper HTML input types (such as changing raw text parameters to hidden password fields) provides a lightweight, native browser defense that mitigates local surveillance risks without requiring heavy processing overhead.
* Securing the portal requires a multi-layered approach where front-end visual shielding (credential masking) acts as the outermost perimeter defense, perfectly complementing downstream backend authentication mechanisms.


## 5. Problem Statement

The platform's authentication gateway relied entirely on single-factor passwords, leaving user profiles highly vulnerable to automated credential-guessing, credential-stuffing, and dictionary attacks. Without request-throttling or secondary verification checkpoints, an attacker who compromised or guessed a user's primary password via automated script iterations could instantly execute an unauthorized account takeover, completely bypassing the perimeter.

## Tools Used
**Node.js & Express.js:** Used to design custom verification routing and structural session validation logic.
**VS Code:** For script isolation, code auditing, and programming cryptographic token handling.
**bcrypt** Employed to test and maintain the security of underlying identity tokens.

## Lessons learnt
* Integrating complex state counters and multi-factor validation routines directly into the primary UI rendering loop creates an architectural bottleneck that risks template parsing conflicts on localhost.
* Access controls, password visual shields, and multi-factor validation checkpoints must be structurally mapped out during initial system design rather than added as unstable post-production overrides.
* Maintaining non-compiled features as isolated, verified Proof-of-Concept (PoC) code blocks allows engineering teams to preserve flawless backend logic syntax while troubleshooting environment-specific runtime constraints. The following backend verification pattern establishes the cryptographic structural logic required to authenticate tokens and secure active login sessions before issuing authorization flags:












