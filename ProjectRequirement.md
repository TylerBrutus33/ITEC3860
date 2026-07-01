# Project Requirements Document: Daily Facts & Trivia REST API

## 1. Introduction

### Brief Project Description
The Daily Facts & Trivia API is a community-driven web service built using Java Spring Boot and a MySQL relational database. The platform allows users to retrieve, submit, edit, and delete trivia items across various structural categories like history, science, and pop culture. Additionally, the system incorporates automated background workers that handle automated scheduled broadcasts of high-interest trivia records directly to external social communication platforms like X.com.

### Problem Statement
In the modern digital landscape, modern applications heavily rely on engaging, bite-sized dynamic data content to boost user retention and platform interactions. However, static data dumps or unvetted text files lead to repetitive, stale informational streams that require constant manual development overhead to rotate or update. There is a lack of accessible, self-cleaning, and community-moderated REST APIs that automate trivia scheduling workflows while protecting target databases from corrupt or unverified entries.

### Identify Stakeholders
- **API Consumers / Front-End Developers**: Software developers looking to query structured, randomized trivia elements to populate their respective web or mobile user interfaces.
- **System Administrators / Content Moderators**: Core team members responsible for maintaining operational uptime, reviewing user-submitted trivia, and deleting or modifying erroneous records.
- **End Users**: General digital audience members consuming automated scheduled informational posts on social media channels.

---

## 2. Requirements Gathering

### Explanation of Identification Process
Requirements for the platform were gathered using a blended agile framework combining Stakeholder Prototyping and Technical Constraint Mapping. The team held collaborative brainstorming sessions to establish core functional system operations matching baseline project constraints.

To simulate real-world data constraints, the operational behaviors were mapped against structured HTTP method structures (GET, POST, PUT, DELETE) linked directly to a relational data schema, rather than temporary file persistence streams. Automated task parameters were derived by modeling targeted webhook limits on modern communication networks to establish programmatic execution cycles.

---

## 3. Use Cases

### Use Case 1: Submit and Approve a New Community Trivia Fact

**Goal**: Allow an external contributor to submit a trivia record, and enable an administrator to approve it for rotation.

**Scope**: REST API System & Backend Relational Database

**Actors**: 
- Content Submitter (User)
- Content Moderator (Admin)

**Trigger**: A user submits an HTTP POST request containing a new trivia entry.

**Preconditions**: 
- The database connection is active
- The incoming data satisfies JSON structural constraints

**Success Conditions**: 
- The trivia item is successfully saved to the database with a pending status
- Updated by an admin and marked ready for public rotation

**Failure Conditions**: 
- The request formatting throws a validation exception, or database storage restrictions are violated, resulting in an error response

**Main Scenario**:
1. The Content Submitter sends an HTTP POST request to `/addFact` with `fact_text` and `category` payloads
2. The system validates the structural integrity of the payload
3. The system assigns a unique record identifier, sets `is_approved` to `false`, and writes the item to the MySQL database
4. The Content Moderator submits an HTTP PUT request to `/updateFact/{id}` updating the flag `is_approved` to `true`
5. The system stores the updated record and returns a 200 OK status confirming verification

**Extensions / Variations**:
- **3a. Malformed Payload**: System identifies missing fields, rejects the entry, and returns an explicit 400 Bad Request

**Related Info**: Intersects directly with active schema validation layers

**Open Issues**: Defining automated regex filters to pre-screen submittals for profanity prior to manual administrative review

---

### Use Case 2: Automated Social Media Trivia Broadcast

**Goal**: Periodically fetch a highly-rated trivia record from the data storage engine and broadcast it to an external platform automatically.

**Scope**: Automated Scheduled Task Subsystem

**Actors**: 
- Automated CRON Engine (System Task)
- X.com API Endpoint

**Trigger**: The system execution clock hits a designated temporal interval threshold.

**Preconditions**: 
- Active network connection
- Valid third-party API authentication credentials loaded in environment files

**Success Conditions**: 
- An eligible, approved record is isolated, dispatched to the third-party network
- Its internal metadata tracking counter is incremented

**Failure Conditions**: 
- Network communication failure or database retrieval fault, triggering an administrative exception notice

**Main Scenario**:
1. The task scheduler fires an execution pulse at a designated interval
2. The system queries the database to grab a random trivia entry where `is_approved` equals `true`
3. The system builds an authorized communication package containing the fact content
4. The system sends the payload out via a secure connection to the X.com API
5. Upon verifying transmission success, the system executes an internal database write to increase that specific record's post count

**Extensions / Variations**:
- **2a. Empty Database Return**: If no approved facts match the query parameters, the worker defaults to a hardcoded system fact to prevent task crashing

**Related Info**: Tied strictly to the system's runtime parameters

**Open Issues**: Rate limiting parameters associated with third-party service provider access tiers

---

## 4. Validation

### Requirement Validation Strategy
To ensure all identified requirements match physical project criteria prior to final cloud deployment, the team will utilize a multi-tiered validation workflow:

- **API Verification Matrix**: Every endpoint layout will undergo black-box functional validation using Postman. Test suites will manually verify that all four essential HTTP methods (GET, POST, PUT, DELETE) alter database states exactly as expected without exceptions.

- **Mock Schedule Inspections**: To ensure automated background tasks fire reliably without waiting hours between intervals, cron cycle frequencies will be temporarily scaled down to 10-second intervals during testing environments. This confirms that automated data selections and external connection attempts execute smoothly.

- **Deployment Smoke Tests**: Following local validation, the compiled system package will be migrated directly to Google Cloud or DigitalOcean hosting environments. Post-deployment smoke testing will verify database connectivity over public endpoints, ensuring full operational requirements are achieved.
