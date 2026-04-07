## Overview

This trial project is the practical stage of our hiring process for the Senior Analytics Consultant (AI-First) role. It is designed to reflect real work: a bounded delivery task where you have access to your full toolkit, including whatever AI tools you use in your normal workflow, and are expected to use them.

## What are trial projects?

Trial projects are short (usually between 5-8 hours), fully-paid projects which are designed to be similar to the actual work that we do at Rittman Analytics. 

What we ask you to deliver in a trial project is used for evaluative purposes only, and not used for our own products, consulting or marketing.  We do however pay £40/hour (£200 for a five hour trial) as compensation for your time, payable within 30 days to your Paypal email account or UK bank account.

Please note that usually have multiple people do trial projects, so please don't be too disappointed if we don't proceed past the trial.

## Guidance Notes

We are not looking for the most complete submission. We are looking for evidence that you can deliver high-quality, validated analytics work using AI tools responsibly, and that you understand the difference between AI-generated output and AI-assisted output that you own and can defend.

The trial has three parts. You do not need to finish every section of every part to progress. Prioritise depth over breadth if you are running short on time, and be explicit about what you chose not to do and why.

- Please spend no more than a total of 5 hours on this trial. 
- If you don't finish within those 5 hours, just send what you have delivered. 
- Submit the repository link by replying to the email from Teamtailor that asked you to complete this project
- If you have questions along the way, don't hesitate to ask. 
- Please provide your paypal email or UK bank details to us via TeamTailor so that you can get paid after the trial. 

---

## What to Submit

A GitHub repository containing:

- All code, models, and configuration files produced during the trial
- A `SUBMISSION.md` file at the root of the repository covering your AI workflow log, key decisions, and reflection (see Part 3 for the required structure)
- Any documentation or client-facing outputs produced as part of the tasks

---

## The Dataset

The trial uses a synthetic e-commerce dataset hosted in BigQuery, made available via BigQuery sharing (formerly Analytics Hub). It contains five tables:

| Table | Description |
|-------|-------------|
| `raw_customers` | Customer profile records including acquisition channel and region |
| `raw_products` | Product catalogue with category, cost, and active status |
| `raw_orders` | Order header records with status, timestamps, and customer references |
| `raw_order_items` | Line item detail with product, quantity, and unit price at time of order |
| `raw_events` | Web event stream with session, page, and event type fields |

### Accessing the dataset

**Step 1: Subscribe via BigQuery sharing**

Open the listing link below. You will need a Google account with access to a GCP project — a free tier project is sufficient.

[Subscribe to the trial dataset on BigQuery sharing](https://console.cloud.google.com/bigquery/analytics-hub/exchanges/projects/15755059154/locations/eu/dataExchanges/rittman_analytics_181c88de202/listings/trial_project_ecommerce_dataset_19d6a074c53?project=ra-development)

On the listing page, click **Subscribe**, select your own GCP project from the dropdown, leave the linked dataset name as `trial_ecommerce`, and click **Save**. The dataset will appear in your BigQuery project within a few seconds as a read-only linked dataset.

**Step 2: Authenticate dbt**

Add the following to your `profiles.yml`, replacing `your-project-id` with your GCP project ID:

```yaml
trial_project:
  target: dev
  outputs:
    dev:
      type: bigquery
      method: oauth
      project: your-project-id
      dataset: trial_dev
      location: EU
      threads: 4
      timeout_seconds: 300
```

Then run:

```bash
gcloud auth application-default login
```

**Step 3: Set your source project**

Set the following environment variable so dbt resolves the source database correctly:

```bash
export DBT_BIGQUERY_PROJECT=your-project-id
```

**Step 4: Verify**

```bash
dbt debug
dbt compile
```

If both pass, you are ready to start. If you hit any access or connection issues, email your hiring contact immediately — we would rather pause the clock than have you spend time on infrastructure.

---

## Part 1: Analytics Engineering Build (approximately 2.5 hours)

### The brief

The client is a mid-market e-commerce business that has just migrated their transactional data into BigQuery. They have no existing analytics layer. Their Head of Commercial has asked for a metrics foundation that will let them answer three questions reliably:

1. What is our revenue and order volume trend over the past 90 days, by channel and region?
2. Which product categories are driving the most and least margin?
3. How is customer retention performing: what share of revenue comes from repeat customers each month?

### What to build

Using dbt (or Dataform if you prefer), build a staging and metrics layer that addresses these questions. Specifically:

- At least three staging models that clean and type-cast the raw source tables
- At least two intermediate or mart models that produce the metrics above in a form suitable for BI consumption
- dbt tests covering not-null, uniqueness, and at least one custom data quality assertion you identify as important
- A `schema.yml` with descriptions for every model and at least the key columns in each

### AI tool expectations

Use AI coding assistants throughout. We expect you to use Claude Code, Copilot, Cursor, or equivalent to generate model scaffolding, write tests, produce documentation, and speed up any part of this task. There is no constraint on which tools you use or how much of the work they generate.

What we will assess is your review discipline: the tests you chose to add, the outputs you chose to override or rewrite, and the things you caught that the AI missed. Document this in your `SUBMISSION.md` (see Part 3).

---

## Part 2: Responsible AI Scenario (approximately 1.5 hours)

### The brief

Following your analytics build, the client's Head of Data has come to you with a new request. They want to add a natural language interface to their metrics layer so that commercial analysts can ask questions in plain English and get answers grounded in the dbt models you have just built. They have seen demos of RAG-based analytics assistants and want to know if this is the right approach for them.

They have two specific concerns:

1. Some of the data includes personally identifiable customer information. They are not sure what they are allowed to send to an LLM provider.
2. They want to be able to trust the answers the system gives. They have had bad experiences with hallucinated numbers in AI-generated reports.

### What to produce

A short advisory document (800 to 1200 words, written for a technical but non-specialist audience) covering:

**Architecture recommendation**: Should they use RAG over their dbt documentation and metric definitions, fine-tuning, structured query generation, or some combination? State your recommendation and your reasoning. Identify one approach you considered and ruled out, and explain why.

**Data handling and governance**: What data can and cannot be sent to an LLM provider in this context? What redaction or anonymisation should be applied before any customer data enters a prompt? What client approval process would you recommend before going live?

**Evaluation approach**: How would you measure whether the system is giving correct and reliable answers? Describe a minimal evaluation set you would build, what you would measure (factuality, relevance, refusal rate for out-of-scope questions), and how you would monitor it after deployment.

**Client-facing limitations summary**: Write two short paragraphs — as if addressed directly to the Head of Commercial, not the Head of Data — explaining what the system will and will not do, and what a user should do if they are unsure whether an answer is correct.

You may use AI tools to draft and structure this document. Apply the same review discipline as Part 1: own the content, verify the claims, and note in your `SUBMISSION.md` where AI contributed and what you changed.

---

## Part 3: AI Workflow Reflection (approximately 1 hour, woven throughout)

### SUBMISSION.md structure

Your `SUBMISSION.md` should contain four sections.

**AI tools used**: List the tools you used (Claude Code, Copilot, Cursor, ChatGPT, etc.) and briefly describe what you used each one for.

**What AI generated vs what you changed**: For each part of the submission, note at least two specific instances where you reviewed, overrode, extended, or rejected AI-generated output, and explain why. Be specific: "the model generated a generic not-null test for customer_id but I added a referential integrity test against raw_customers because the join was non-obvious and I wanted to catch upstream deletion issues" is the level of specificity we are looking for.

**What you would do differently with more time**: One short paragraph covering the analytics engineering build and one covering the responsible AI document. This is not a formality — we read it carefully.

**One thing that surprised you**: Either something the AI did better than you expected, or something it got wrong that you would not have caught without reviewing carefully.

---

## How We Evaluate Submissions

We assess submissions across four dimensions. Each is weighted equally.

**Analytics engineering craft**
Are the models well-structured, correctly typed, and appropriately tested? Is the SQL readable and defensible? Are the data quality tests meaningful, or are they generic scaffolding that was generated and not reviewed? Does the mart layer actually answer the three business questions?

**AI tool usage and review discipline**
Did the candidate use AI tools effectively to move faster? Did they review, validate, and own the output, or did they ship generated content without inspection? The `SUBMISSION.md` is the primary evidence here. We are looking for a consistent "generate, review, verify" pattern — not volume of AI usage.

**Responsible AI reasoning**
Is the architecture recommendation grounded and pragmatic? Are the data handling recommendations accurate and specific to this context, not generic? Is the evaluation approach workable, not theoretical? Are the client-facing limitations honest and clearly written?

**Communication quality**
Is the advisory document written for its intended audience? Are the dbt model descriptions written for someone who did not build the models? Is the `SUBMISSION.md` clear and specific, or vague and defensive?

---

## Practical Notes

You have access to your own Claude Code, Copilot, Cursor, or other AI tool subscriptions. We do not provide tool access as part of the trial but we will reimburse reasonable costs incurred during the 5-hour window on request.

The BigQuery dataset is synthetic and contains no real personal data. You do not need to apply data handling restrictions to the trial dataset itself, but your Part 2 advisory document should treat it as if it were real.

You are not expected to deploy anything. All outputs are evaluated from the repository.

If you hit a blocker — access issues, an unclear brief, a data quality problem in the source tables — email your hiring contact immediately. We would rather pause the clock than have you spend an hour working around something we can fix in five minutes.

---

## What We Are Not Looking For

We are not looking for a complete, production-ready data platform built in five hours. We are looking for evidence of how you work: how you prioritise under time pressure, how you use AI tools without losing ownership of the output, and whether you can reason clearly about the governance and reliability of AI components.

A submission that covers two-thirds of Part 1 thoroughly, with excellent tests and a clear `SUBMISSION.md`, will score higher than a submission that covers everything at surface level with no evidence of review discipline.
