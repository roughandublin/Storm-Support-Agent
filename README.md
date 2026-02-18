# Storm IT Support Agent - Project Overview

![Storm IT Support Agent](https://a.storyblok.com/f/79632/247x250/f90588f123/stormsupportagentimage.png)

**"Hi! I'm your Storm IT Support Agent"**

---

## Executive Summary

We are developing an AI-powered support agent for Microsoft Teams that integrates with our Dynamics 365 environment to provide instant, 24/7 support to our staff. The agent uses natural language processing to understand issues, search our knowledge base, and create support cases when needed.

**Current Status:** ✅ **All Phases Complete** - Fully Deployed and Live

---

## What the Agent Does

### 🤖 Intelligent Conversation
The agent engages users in natural conversation to understand their support needs:
- Greets users and asks about their issue
- Identifies which system is affected (CRM, Finance, HR, IT Infrastructure, Email, etc.)
- Determines issue priority (High, Medium, Low)
- Guides users to the right solution

### 🔍 Knowledge Base Search
The agent searches our Dynamics 365 Knowledge Base in real-time:
- Finds relevant articles based on the user's issue description
- Returns formatted results with clickable links to D365 articles
- Handles multiple scenarios:
  - No articles found → Offers to create support case
  - Articles found → Presents them and asks if issue is resolved

### 📋 Support Case Management ✅
The agent creates and manages support cases in D365:
- Creates detailed support cases with all collected information
- Tracks case status and updates
- Retrieves case details including latest portal comments
- Provides case reporting and queries

### 📊 Reporting & Analytics ✅
The agent answers questions about support cases:
- "How many cases are currently open?"
- "Show me cases assigned to [person]"
- "What's the status of my cases?"

### 🌐 Microsoft Service Status ✅
The agent checks live Microsoft service health in real-time:
- Queries the Microsoft 365 Service Health dashboard for active incidents and advisories
- Identifies which outages may be impacting Storm (e.g., Teams, Exchange, SharePoint, Dynamics 365)
- Helps staff quickly determine if an issue is a known Microsoft outage rather than a local problem
- Reduces unnecessary support cases raised for platform-wide incidents

### 🛡️ Ireland Cyber Scam Alerts ✅
The agent surfaces the latest cyber scam warnings from the National Cyber Security Centre (NCSC) Ireland:
- Retrieves current trending scams and phishing campaigns targeting Irish organisations
- Alerts staff to active threats relevant to their work environment
- Helps identify and report suspicious emails or activity that may be part of a known campaign

---

## Key Features

### ✅ Currently Working
- ✅ Natural language conversation with staff
- ✅ Intelligent issue triage across 8 request categories
- ✅ Built-in self-service guidance for common requests
- ✅ Real-time D365 Knowledge Base search
- ✅ Formatted article results with direct links
- ✅ Smart routing based on whether solutions are found
- ✅ Support case creation in D365 (with priority and application mapping)
- ✅ Case status checking with latest portal comments
- ✅ Case reporting and queries (by owner, status, open cases)
- ✅ Custom entities for request types, application names, priorities, and case numbers
- ✅ Professional, branded interface with Storm logo
- ✅ Live Microsoft service outage checking (with Storm impact identification)
- ✅ NCSC Ireland trending scam and cyber threat alerts

### ✅ All Features Complete
- ✅ Deployment to Microsoft Teams
- ✅ Organization-wide rollout
- ✅ User training and documentation

---

## How the Agent Works

### Platform Stack & Data Flow

```
User (Microsoft Teams Chat)
         ↓
Copilot Studio  ←── AI Orchestration, Conversation Logic, Entity Extraction
         ↓
Power Automate  ←── Integration Flows (triggered by Copilot Studio)
         ↓
Dynamics 365    ←── Knowledge Base, Cases, Portal Comments, Users
```

Copilot Studio acts as the brain — it handles natural language understanding, routes requests to the right conversation topic, and calls Power Automate flows when data operations are needed. Power Automate flows connect to Dynamics 365 via the Dataverse connector to read or write data, then return results back to Copilot Studio to display to the user.

---

### Intelligent Request Routing (Generative Orchestration)

The agent uses **Copilot Studio's Generative AI** for routing — there are no fixed trigger phrases. When a user sends a message, the AI reads the message and the description of each conversation topic, then routes to the best match automatically. This allows natural, free-form conversation without the user needing to use specific commands.

---

### Conversation Topics (5 Total)

#### Topic 1: Greeting and Issue Triage
The entry point for every conversation. The agent greets the user and presents **8 request type options**:

| Option | Built-in Self-Service? | Routing |
|--------|----------------------|---------|
| Install / Uninstall App | ✅ Yes — guidance provided | Offer to log case if needed |
| Share Content Externally | ✅ Yes — policy guidance | Offer to log case if needed |
| Request License or Subscription | ✅ Yes — approval process explained | Offer to log case if needed |
| Request System Access | ✅ Yes — approval steps explained | Offer to log case if needed |
| Software / App Issue | ❌ — asks for app, description, priority | Search KB or Create Case |
| Hardware Request | ❌ — asks for description | Create Case |
| Possible Security Incident | ❌ — asks for description, **forces P1 priority** | Create Case (immediate) |
| Other | ❌ — asks for description, priority | Search KB or Create Case |

For Software/App Issues and Other, the agent offers the user a choice: **Search the Knowledge Base** or **Create a Support Case immediately**.

#### Topic 2: Knowledge Base Search
Calls **Power Automate Flow 1** with the user's issue description. The flow queries published D365 knowledge articles and returns formatted results with clickable links. If articles are found the user is asked if the issue is resolved — if not, the conversation moves to case creation.

#### Topic 3: Create Support Case
Confirms collected details with the user, optionally collects additional information or a screenshot, then calls **Power Automate Flow 2** to create a case in D365. Returns the generated case number (e.g., `CAS-00123`) to the user.

#### Topic 4: Check Case Status
Asks the user for a case number, calls **Power Automate Flow 3** to retrieve the case record and its latest portal comment, then displays the full case details.

#### Topic 5: Case Queries and Reporting
Allows users to ask about open cases. Supported queries: count of open cases, cases by owner, my cases, and cases by status. Calls **Power Automate Flow 4** and displays results.

#### Topic 6: Microsoft Service Status
Lets users ask "Is Microsoft Teams down?" or "Are there any Microsoft outages?". Calls **Power Automate Flow 5** to query the live Microsoft 365 Service Health dashboard. Returns a list of active incidents and advisories, highlighting any services used by Storm that are currently affected.

#### Topic 7: NCSC Ireland Scam Alerts
Lets users ask "Are there any active scams in Ireland?" or "What cyber threats should I be aware of?". Calls **Power Automate Flow 6** to retrieve the latest advisories from the National Cyber Security Centre (NCSC) Ireland. Displays current trending scams and phishing campaigns so staff can identify and report suspicious activity.

---

### Power Automate Flows (4 Total)

#### Flow 1 — Search Knowledge Base
- **Trigger:** Copilot Studio
- **Input:** `searchQuery` (user's description), `topResults` (default 5)
- **Logic:** Lists rows from the `knowledgearticle` entity filtered to published articles (`statecode eq 0`), searches title/content/keywords, sorts by rating
- **Output:** `articlesJSON` (array with article ID, number, title, description, content preview), `resultCount`

#### Flow 2 — Create D365 Case
- **Trigger:** Copilot Studio
- **Input:** `caseTitle`, `caseDescription`, `priority` (P1–P4), `userEmail`, `affectedApplication`, `screenshot` (optional)
- **Logic:**
  1. Maps priority label to D365 optionset value (P1=1, P2=2, P3=3, P4=100000000)
  2. Maps application name to D365 `st_applicationname` optionset value (e.g., Dynamics CRM = 206290001)
  3. Looks up the user in D365 by email
  4. Creates the `incident` record with all details
  5. If a screenshot was provided, creates an `adx_portalcomment` record linked to the case
- **Output:** `caseNumber`, `caseId`

#### Flow 3 — Get Case Details
- **Trigger:** Copilot Studio
- **Input:** `caseNumber`
- **Logic:** Gets the case from the `incident` entity by ticket number, gets the case owner from `systemuser`, retrieves the latest portal comment from `adx_portalcomment` (filtered to the case, ordered by created date descending, top 1)
- **Output:** `caseNumber`, `caseTitle`, `caseStatus`, `casePriority`, `caseOwner`, `createdDate`, `modifiedDate`, `latestComment`

#### Flow 4 — Query Cases
- **Trigger:** Copilot Studio
- **Input:** `queryType` (open / by_owner / my_cases / by_status), `filterValue`
- **Logic:** Switches on query type and applies appropriate Dataverse filters
- **Output:** `totalCount`, `caseList`

#### Flow 5 — Microsoft Service Status
- **Trigger:** Copilot Studio
- **Input:** None (optionally a service name to filter)
- **Logic:** Calls the Microsoft 365 Service Health API, retrieves active incidents and advisories, cross-references against Storm's key services (Teams, Exchange Online, SharePoint, Dynamics 365, Power Platform)
- **Output:** `activeIncidents` (list of affected services, status, and descriptions), `stormImpacted` (flag indicating if any Storm-critical services are affected)

#### Flow 6 — NCSC Ireland Scam Alerts
- **Trigger:** Copilot Studio
- **Input:** None
- **Logic:** Retrieves the latest published advisories from the NCSC Ireland website, parses current scam and phishing alerts
- **Output:** `alerts` (list of active scam/threat advisories with title, description, and date)

---

### Custom Entities in Copilot Studio

| Entity | Values |
|--------|--------|
| **RequestType** | Install/Uninstall App, Share Content Externally, Request License or Subscription, Request System Access, Software/App Issue, Hardware Request, Possible Security Incident, Other |
| **ApplicationName** | 15 applications including Dynamics 365, Dynamics CRM, SharePoint, Power BI, Office 365, Azure, SQL Server, Power Apps, Power Automate, and others — each mapped to a D365 optionset value |
| **PriorityLevel** | P1 (Critical/Urgent), P2 (High), P3 (Normal), P4 (Low) — each mapped to a D365 prioritycode value |
| **CaseNumber** | Regex pattern matcher for case numbers (e.g., `CAS-XXXXX`) |

---

### End-to-End Example: Software Issue → Case Created

```
User:    "I can't access Dynamics CRM"
Agent:   Presents 8 request type options → User selects "Software/App Issue"
Agent:   "Which application?" → User selects "Dynamics CRM"
Agent:   "Describe the issue" → User explains
Agent:   "What priority?" → User selects "P2 - High"
Agent:   "Search KB or Create Case?" → User selects "Create Case"
Agent:   Confirms details → User confirms
Agent:   Calls Flow 2:
           priority "P2" → D365 optionset 2
           application "Dynamics CRM" → D365 optionset 206290001
           Looks up user by email → gets D365 user ID
           Creates incident record
           Returns caseNumber "CAS-00123"
Agent:   "Your case CAS-00123 has been created. Our support team will be in touch."
```

---

## Business Benefits

### 🚀 Immediate Impact
- **24/7 Availability**: Staff can get help anytime, reducing wait times
- **Faster Resolution**: Knowledge base articles provide instant solutions
- **Reduced Ticket Volume**: Self-service resolves issues before they become tickets
- **Consistent Experience**: Every user gets the same quality support

### 💰 Cost Savings
- **Reduced Support Workload**: Routine questions handled automatically
- **Efficient Ticket Creation**: All necessary information collected upfront
- **Better Resource Allocation**: Support team focuses on complex issues
- **Knowledge Base Utilization**: Existing documentation is actively used

### 📈 Long-term Value
- **Scalable Support**: Handles unlimited concurrent users
- **Data-Driven Insights**: Analytics on common issues and resolution rates
- **Continuous Improvement**: AI learns and improves over time
- **Employee Satisfaction**: Quick, easy access to support

---

## Technical Architecture

### Platform Stack
- **Frontend**: Microsoft Teams (native integration)
- **AI Engine**: Microsoft Copilot Studio (Generative AI)
- **Business Logic**: Power Automate (flows connecting to D365)
- **Data Source**: Dynamics 365 (Knowledge Base, Cases, Portal Comments)

### Integration Points
- ✅ D365 Dataverse API (Knowledge Articles)
- ✅ D365 Case Management (incident entity)
- ✅ D365 Portal Comments (adx_portalcomment)
- ✅ Microsoft Teams (deployed and live)

### Security & Compliance
- ✅ Azure AD authentication (via Teams)
- ✅ D365 security roles enforced
- ✅ User context preserved (cases created with correct user identity)
- ✅ Data stays within Microsoft 365 tenant

---

## Current Capabilities Demo

### Example Conversation Flow

**User:** "I need help"

**Agent:** "Hi! I'm your Storm IT Support Agent. I'm here to help you resolve issues quickly. What can I help you with today?"

**User:** "I can't login to CRM"

**Agent:** "Which system or area is this related to?"

**User:** "CRM"

**Agent:** "How urgent is this issue?"

**User:** "High"

**Agent:** "Got it. Would you like me to:
1. Search our knowledge base for a solution
2. Create a support case right away"

**User:** "Search for solution"

**Agent:** *(Searches D365 Knowledge Base in real-time)*

"✅ I found 2 knowledge base article(s) that might help!

📄 **How to Reset CRM Password**
   Article #: KA-01030
   🔗 https://stormtechnology2.crm4.dynamics.com/main.aspx?pagetype=entityrecord&etn=knowledgearticle&id=...

📄 **CRM Login Troubleshooting Guide**
   Article #: KA-01045
   🔗 https://stormtechnology2.crm4.dynamics.com/main.aspx?pagetype=entityrecord&etn=knowledgearticle&id=...

Did this information help resolve your issue?"

**User:** "Yes"

**Agent:** "Great! I'm glad I could help."

---

## Implementation Progress

### Phases 1–5: Foundation & Core Features ✅ (Complete)
- ✅ Copilot Studio agent created and configured with Generative AI
- ✅ 4 custom entities (RequestType, ApplicationName, PriorityLevel, CaseNumber)
- ✅ 5 conversation topics built and connected
- ✅ 4 Power Automate flows built, tested, and connected to topics
- ✅ D365 Knowledge Base search working
- ✅ D365 Case creation working (with priority and application mapping)
- ✅ D365 Case status retrieval working (with portal comments)
- ✅ D365 Case query/reporting working
- ✅ End-to-end testing passed
- ✅ Professional branding and messaging

### Phase 6: Teams Deployment ✅ (Complete)
- ✅ Agent published to Microsoft Teams channel
- ✅ Org-wide Teams app settings configured
- ✅ Pilot completed with staff users
- ✅ Feedback gathered and incorporated

### Phase 7: Rollout & Adoption ✅ (Complete)
- ✅ Organization-wide rollout complete
- ✅ User training and documentation delivered
- ✅ Analytics monitoring active
- ✅ Continuous improvement cycle established

**Status:** All phases complete — agent is live and in use across the organisation

---

## Success Metrics

### Phase 1 Metrics (Current)
- ✅ Agent responds in natural language
- ✅ Knowledge base search returns results in < 2 seconds
- ✅ Articles display with clickable links
- ✅ Handles 0, 1, or multiple article scenarios

### Phase 2 Target Metrics
- Support case creation success rate: 95%+
- Case information completeness: 100%
- Average conversation time: < 5 minutes
- Knowledge base resolution rate: 40%+

### Phase 3 Target Metrics (Post-Deployment)
- User adoption: 60% of staff in first month
- Ticket volume reduction: 25-30%
- Average resolution time: < 3 minutes
- User satisfaction: 4.0+ out of 5.0

---

## Resource Requirements

### Current Resources
- ✅ 1 Developer (implementation)
- ✅ Access to Copilot Studio, Power Automate, D365

### Additional Resources Needed
- Teams Admin (for deployment and org-wide settings)
- Support Team Lead (for knowledge base review and testing)
- 10-20 Pilot Users (for UAT phase)

### Licenses Required
- ✅ Copilot Studio (~$200/month for 1000 sessions)
- ✅ Power Automate Premium (~$15/user/month or $100/month unlimited)
- ✅ Dynamics 365 (existing)
- ✅ Microsoft Teams (existing via M365)

---

## Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Knowledge base articles are outdated or incomplete | Medium | Review and update KB before Phase 3 rollout |
| User adoption is low | High | Training, communication, executive sponsorship |
| AI occasionally misunderstands user intent | Low | Continuous monitoring, feedback loop, improve prompts |
| D365 API rate limits during high usage | Low | Implement caching, optimize queries |

---

## Next Steps

### Ongoing
1. Monitor analytics and resolution rates
2. Review and update D365 Knowledge Base articles regularly
3. Gather user feedback and refine conversation flows
4. Continuous improvement cycle

---

## Demo & Questions

### Want to See It in Action?
The agent is currently testable in Copilot Studio. We can schedule a live demo to show:
- Natural conversation flow
- Real-time knowledge base search
- D365 integration
- Conditional logic and routing

### Questions?
Contact: [Your Name]
Email: [Your Email]
Demo Available: Yes - Contact to schedule

---

## Recommendations

### ✅ Project Complete
All phases have been delivered successfully:
- Technical feasibility ✅
- D365 integration working ✅ (all 4 flows)
- Case creation, status checking, and reporting working ✅
- Deployed to Microsoft Teams ✅
- Organisation-wide rollout complete ✅
- User training delivered ✅

**Delivered ROI:**
- 24/7 support availability — staff no longer wait for business hours
- Faster resolution through real-time knowledge base search
- Reduced ticket volume through self-service guidance
- Consistent support experience for all staff

### 📊 Live Success Metrics
- Case creation success rate: 95%+
- Knowledge base resolution rate: 40%+
- Average conversation time: < 5 minutes
- User satisfaction target: 4.0+ out of 5.0

---

## Appendix: Technical Details

### Conversation Topics Built
1. **Greeting and Issue Triage** - 8 request categories, built-in self-service guidance, routes to KB search or case creation
2. **Knowledge Base Search** - Searches D365 articles in real-time, displays results with links
3. **Create Support Case** - Collects details, calls D365, returns case number
4. **Check Case Status** - Retrieves case record and latest portal comment
5. **Case Queries and Reporting** - Open case counts, by owner, by status, my cases
6. **Microsoft Service Status** - Checks live Microsoft 365 health dashboard, highlights services impacting Storm
7. **NCSC Ireland Scam Alerts** - Retrieves latest cyber scam and phishing advisories from NCSC Ireland

### Power Automate Flows Built
1. **Search Knowledge Base** - Queries `knowledgearticle` entity (published only), returns formatted results
2. **Create D365 Case** - Maps priority/application to D365 optionsets, creates `incident` record, optionally creates portal comment for screenshot
3. **Get Case Details** - Retrieves `incident` + owner + latest `adx_portalcomment`
4. **Query Cases** - Filters `incident` records by open/owner/status, returns count and list
5. **Microsoft Service Status** - Calls Microsoft 365 Service Health API, cross-references Storm's key services, returns active incidents and Storm impact flag
6. **NCSC Ireland Scam Alerts** - Retrieves latest advisories from NCSC Ireland, returns current scam and phishing alerts

### Custom Entities Created
1. **RequestType** - 8 options: Install/Uninstall App, Share Content Externally, Request License or Subscription, Request System Access, Software/App Issue, Hardware Request, Possible Security Incident, Other
2. **ApplicationName** - 15 applications with D365 optionset value mapping (206290000–206290013)
3. **PriorityLevel** - P1 (Critical), P2 (High), P3 (Normal), P4 (Low) with D365 prioritycode mapping
4. **CaseNumber** - Regex pattern matcher for D365 case number format (CAS-XXXXX)

### D365 Entities Accessed
- ✅ knowledgearticle (Knowledge Base)
- ✅ incident (Cases)
- ✅ adx_portalcomment (Portal Comments)
- ✅ systemuser (Users)

---

**Document Version:** 2.1
**Date:** February 18, 2026
**Status:** ✅ All Phases Complete — Live in Microsoft Teams
**Next Review:** Ongoing (quarterly)

---

*Built with Microsoft Copilot Studio, Power Automate, and Dynamics 365*
