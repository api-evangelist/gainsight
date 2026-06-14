# Gainsight GraphQL Schema

**Provider:** Gainsight  
**Platform:** Customer Success (CS) and Product Experience (PX)  
**Schema File:** gainsight-schema.graphql  
**Reference APIs:** Gainsight NXT REST APIs, Gainsight Bulk API, Gainsight PX API  
**Docs:** https://support.gainsight.com/SFDC_Edition/API_and_Developer_Docs/

## Overview

Gainsight is a customer success platform that enables companies to retain and grow their customer base through health scoring, playbooks, engagement tracking, and renewal management. This conceptual GraphQL schema models the core Gainsight domain objects drawn from the Gainsight CS NXT APIs, Bulk API, PX API, and Renewal Center API.

The schema covers the full lifecycle of customer success operations: account and contact management, health score calculation, risk identification, playbook orchestration, task management, cockpit alerts (CTAs), surveys, NPS, renewal tracking, product usage analytics, and digital journey engagement.

## Named Types

### Account & Company

| Type | Description |
|------|-------------|
| `Account` | Core customer account record with health, CSM, risks, renewals, and usage. |
| `AccountDetails` | Extended metadata for an account: address, CRM ID, GSID, contract dates. |
| `AccountHealth` | Aggregated health state with overall score, trend, and contributing factors. |
| `AccountScore` | A named scorecard value contributing to account health. |

### Health Scoring

| Type | Description |
|------|-------------|
| `HealthScore` | Overall health score record with color code, label, and factor breakdown. |
| `HealthScoreFactor` | An individual dimension (adoption, engagement, support, etc.) in the health score. |

### Customer

| Type | Description |
|------|-------------|
| `CustomerDetails` | Lifetime value, open CTA count, NPS score, and engagement attributes per account. |

### Contacts

| Type | Description |
|------|-------------|
| `Contact` | A person at a customer account, with role, title, and activity history. |
| `ContactDetails` | Extended contact attributes including CRM ID, timezone, opt-out flags. |
| `ContactActivity` | An individual interaction (call, email, meeting) associated with a contact. |
| `ContactRole` | Enum of contact roles: champion, economic buyer, sponsor, end user, etc. |

### CSM

| Type | Description |
|------|-------------|
| `CSM` | A Customer Success Manager user with assigned accounts and open tasks. |
| `CSMDetails` | Capacity, ARR managed, average health score, and open CTA count for a CSM. |
| `CSMTeam` | A grouping of CSMs by region or segment with a designated manager. |

### Risk

| Type | Description |
|------|-------------|
| `Risk` | A risk record associated with a customer account. |
| `RiskDetails` | Mitigation plan, impact assessment, likelihood, and escalation metadata. |
| `RiskType` | Classification type for a risk with default severity. |
| `RiskSeverity` | Enum: LOW, MEDIUM, HIGH, CRITICAL. |
| `RiskCategory` | Enum: ADOPTION, ENGAGEMENT, FINANCIAL, PRODUCT, RELATIONSHIP, SUPPORT. |

### Timeline

| Type | Description |
|------|-------------|
| `Timeline` | A timeline grouping activities for a customer account. |
| `TimelineActivity` | An individual activity entry (call, meeting, note) on the account timeline. |
| `ActivityType` | Enum of activity types: CALL, EMAIL, MEETING, NOTE, QBR, etc. |

### Notes

| Type | Description |
|------|-------------|
| `Note` | A freeform note attached to an account, contact, or timeline activity. |
| `MeetingNote` | Structured notes from a customer meeting: agenda, summary, action items. |

### Playbooks & Tasks

| Type | Description |
|------|-------------|
| `Playbook` | A Gainsight Playbook defining a structured customer success workflow. |
| `PlaybookDetails` | Metadata including objective, trigger conditions, and applicable segments. |
| `PlaybookStep` | A step within a Playbook grouping related tasks. |
| `Task` | An actionable item for a CSM tied to an account, CTA, or playbook step. |
| `TaskDetails` | Additional task attributes: type, effort, recurrence, completion notes. |
| `TaskStatus` | Enum: OPEN, IN_PROGRESS, COMPLETED, SKIPPED, OVERDUE. |

### Cockpit & Alerts

| Type | Description |
|------|-------------|
| `Cockpit` | Prioritized CSM workspace surfacing open CTAs, tasks, and alerts. |
| `Alert` | A Call To Action (CTA) or system-generated alert for an account. |
| `AlertDetails` | Trigger conditions, current and threshold values, priority, and closure reason. |
| `AlertType` | Enum: HEALTH_SCORE_CHANGE, RENEWAL_UPCOMING, USAGE_DROP, SURVEY_RESPONSE, etc. |
| `AlertSeverity` | Enum: INFO, WARNING, CRITICAL. |

### Surveys

| Type | Description |
|------|-------------|
| `Survey` | A survey sent to customer contacts (CSAT, NPS, custom). |
| `SurveyDetails` | Configuration: type, audience, send method, response rate, average score. |
| `SurveyResponse` | A single respondent's answers to a survey. |
| `Question` | A survey question with type, order, and answer options. |
| `QuestionType` | Enum: TEXT, MULTIPLE_CHOICE, RATING, NPS, YES_NO, DROPDOWN, DATE, NUMBER. |
| `Answer` | An answer to a specific question within a survey response. |

### NPS

| Type | Description |
|------|-------------|
| `NPS` | An NPS score record for a specific contact. |
| `NPSScore` | Detailed NPS score with promoter/detractor reason and label. |
| `NPSSurvey` | An NPS survey instance with aggregate promoters, passives, detractors, and score. |

### Renewals

| Type | Description |
|------|-------------|
| `Renewal` | A renewal record tracking a contract renewal opportunity. |
| `RenewalDetails` | Previous ARR, renewal ARR, expansion ARR, churn risk, and signing contact. |
| `RenewalStatus` | Enum: OPEN, AT_RISK, COMMITTED, CLOSED_WON, CLOSED_LOST, CHURNED. |

### Opportunities

| Type | Description |
|------|-------------|
| `Opportunity` | An opportunity for upsell, expansion, or renewal in the Renewal Center. |
| `OpportunityDetails` | Booking type, forecast category, loss/win reason, product, and pricing. |

### Features & Usage

| Type | Description |
|------|-------------|
| `Feature` | A product feature tracked for adoption and engagement. |
| `FeatureUsage` | Usage statistics for a feature by account over a time period. |
| `FeatureAdoption` | Aggregated adoption percentage, active users, and trend for a feature. |
| `UserActivity` | A product usage event captured from a user session (PX API). |
| `UsageData` | Aggregated usage summary for an account: sessions, active users, top features. |

### Metrics

| Type | Description |
|------|-------------|
| `Metric` | A named metric recorded for a customer account. |
| `MetricDetails` | Category, source, formula, target, threshold, and frequency for a metric. |
| `MetricTrend` | Directional trend data for a metric with historical data points. |

### Licenses

| Type | Description |
|------|-------------|
| `License` | A software license assigned to a customer account. |
| `LicenseDetails` | SKU, product edition, contract ID, pricing, and renewal metadata. |

### API & Auth

| Type | Description |
|------|-------------|
| `APIKey` | An API key for authenticating Gainsight REST API requests. |
| `Token` | An OAuth 2.0 access token for Gainsight API authentication. |

### Webhooks

| Type | Description |
|------|-------------|
| `Webhook` | A registered webhook endpoint for Gainsight event notifications. |
| `WebhookEvent` | An event payload delivered to a webhook with delivery status. |

### Journeys & Segments

| Type | Description |
|------|-------------|
| `Journey` | A digital journey orchestrating in-app engagement for a user cohort. |
| `JourneyDetails` | Trigger/goal events, steps, enrollment and completion metrics. |
| `Segment` | A named segment of accounts or users used for targeting. |
| `SegmentDetails` | Dynamic or static criteria, account IDs, and tags for a segment. |

## Total Named Types

This schema defines **66 named types** (object types, enums, and scalars), covering the full range of Gainsight CS and PX platform concepts.

## Queries

The schema exposes queries for all major entity types:

- `account(id)`, `accounts(filter, limit, offset)`, `accountHealth(accountId)`, `accountScore(accountId)`
- `contact(id)`, `contacts(accountId)`
- `csm(id)`, `csms(region)`, `csmTeam(id)`, `csmTeams`
- `risk(id)`, `risks(accountId, severity, category)`
- `timeline(id)`, `timelines(accountId)`, `timelineActivity(id)`
- `playbook(id)`, `playbooks`, `task(id)`, `tasks(accountId, status, assigneeId)`
- `cockpit(csmId)`, `alert(id)`, `alerts(accountId, type, severity)`
- `survey(id)`, `surveys`, `surveyResponse(id)`, `surveyResponses(surveyId)`
- `nps(accountId)`, `npsSurvey(id)`, `npsSurveys`
- `healthScore(accountId)`, `healthScoreFactors(healthScoreId)`
- `renewal(id)`, `renewals(accountId, status)`
- `opportunity(id)`, `opportunities(accountId, stage)`
- `feature(id)`, `features`, `featureUsage`, `usageData(accountId)`, `userActivities(accountId)`
- `metric(id)`, `metrics(accountId)`, `license(id)`, `licenses(accountId)`
- `webhook(id)`, `webhooks`, `journey(id)`, `journeys`, `segment(id)`, `segments`

## Mutations

Mutations support the full CRUD lifecycle for all major types, including upsert patterns matching the Gainsight REST API conventions (bulk upsert for Company, Person, and Custom Object APIs) and status transitions for Alerts, Renewals, and Tasks.

## Authentication

Gainsight APIs use:
- **REST API Key** — generated in the Gainsight admin under API & Integrations
- **OAuth 2.0** — access token flow for third-party integrations
- Reference: https://support.gainsight.com/gainsight_nxt/API_and_Developer_Docs/Generate_REST_API/Generate_REST_API_Key
