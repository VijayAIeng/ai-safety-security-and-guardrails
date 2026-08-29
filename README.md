# AI Safety, Security and Guardrails

This repository is my hands-on exploration of AI safety, AI security, and guardrails, starting from the fundamentals and gradually moving toward modern LLM, RAG, and agentic AI systems.

The goal is to understand what can go wrong when AI systems are used in real applications and how we can design systems that are safer, more secure, more reliable, and easier to control.

I want to understand these problems from the engineering level.

Not only:

```text
What is prompt injection?
What is a jailbreak?
What are guardrails?
```

But also:

```text
Why does the problem happen?
Where does the problem enter the system?
What is the actual attack surface?
Why does a particular defense fail?
How can the application reduce the risk?
How do we evaluate the defense?
How do we monitor it in production?
```

The repository gradually moves from:

```text
AI Safety Fundamentals
        ↓
AI Security Fundamentals
        ↓
LLM Security
        ↓
Prompt Injection
        ↓
Jailbreaks
        ↓
Data Leakage
        ↓
RAG Security
        ↓
Agent Security
        ↓
Tool Security
        ↓
Guardrails
        ↓
Evaluation
        ↓
Monitoring
        ↓
Production AI Security
```

---

# Why Do We Need AI Safety and Security?

Traditional software generally follows deterministic application logic.

A simplified system looks like:

```text
Input
  ↓
Application Logic
  ↓
Output
```

The developer defines what the system should do.

With an LLM application, the architecture becomes:

```text
User Input
    ↓
Prompt
    ↓
LLM
    ↓
Generated Output
```

The model is generating an output rather than following a fixed sequence of predefined rules.

This creates a different engineering problem.

The model can generate:

```text
Incorrect Information
Unsafe Content
Sensitive Information
Unexpected Instructions
Manipulated Responses
Unauthorized Actions
```

The problem becomes much larger when the LLM is connected to external systems.

For example:

```text
User
  ↓
LLM
  ↓
RAG
  ↓
Database
  ↓
Tools
  ↓
External APIs
```

Now the model is no longer just generating text.

It may be able to:

```text
Read data
Search documents
Call APIs
Execute tools
Modify records
Send messages
Perform business operations
```

This creates a much larger security surface.

---

# The Core Problem

A modern AI application can look like:

```text
                         AI APPLICATION

User
  ↓
Frontend
  ↓
Backend
  ↓
Prompt
  ↓
LLM
  ├──────────────→ RAG
  │                 ↓
  │              Documents
  │
  ├──────────────→ Tools
  │                 ↓
  │              APIs
  │
  └──────────────→ Database
```

Every connection introduces another possible failure.

For example:

```text
User Input
    ↓
Prompt Injection

Retrieved Document
    ↓
Indirect Prompt Injection

LLM
    ↓
Jailbreak / Unsafe Output

RAG
    ↓
Unauthorized Data Retrieval

Tool
    ↓
Tool Abuse

Database
    ↓
Data Leakage

Application
    ↓
Authorization Failure
```

Therefore, securing only the model is not enough.

The entire application needs to be designed with security and safety controls.

---

# The Security Boundary

One of the most important ideas in this repository is:

```text
The LLM should not be treated as a security boundary.
```

For example, we should not depend entirely on:

```text
System Prompt

"Never reveal private information."
```

to protect sensitive information.

A stronger architecture is:

```text
Authentication
      ↓
Authorization
      ↓
Data Access Control
      ↓
Retrieval Filtering
      ↓
LLM
      ↓
Output Validation
      ↓
Tool Permission Checks
      ↓
Monitoring
```

Security controls should exist outside the model as well.

---

# AI Safety vs AI Security

These concepts are closely related but have different focuses.

## AI Safety

AI safety focuses on ensuring that an AI system behaves within acceptable boundaries.

Examples:

```text
Unsafe outputs
Unreliable behavior
Harmful recommendations
Hallucinations
Unexpected behavior
Loss of control
Misuse
```

## AI Security

AI security focuses on protecting the AI system and its resources from malicious or unauthorized behavior.

Examples:

```text
Prompt Injection
Jailbreaking
Data Leakage
Unauthorized Access
Tool Abuse
Credential Exposure
Model Manipulation
Application Attacks
```

A production AI system needs both.

```text
                 AI SYSTEM

              +------------+
              | AI Safety  |
              +------------+
                    +
              +------------+
              |AI Security |
              +------------+
                    +
              +------------+
              |  Privacy   |
              +------------+
                    +
              +------------+
              |Reliability |
              +------------+
```

---

# What Are Guardrails?

Guardrails are controls placed around an AI system to constrain, validate, monitor, or block unsafe or unauthorized behavior.

They are not simply a system prompt.

A production guardrail architecture can look like:

```text
User Input
     ↓
Input Guardrails
     ↓
LLM
     ↓
Output Guardrails
     ↓
Tool Permission Checks
     ↓
Action Guardrails
     ↓
Final Response
     ↓
Monitoring
```

The idea is:

```text
Input
  ↓
Validate
  ↓
Process
  ↓
Validate
  ↓
Act
  ↓
Validate
  ↓
Monitor
```

Guardrails therefore become a layer of control around the model.

---

# Why Are Guardrails Needed?

Consider an AI agent with access to a database.

Without controls:

```text
User
  ↓
Agent
  ↓
Database Tool
  ↓
Sensitive Data
```

The model may decide to call a tool when it should not.

A safer design is:

```text
User
  ↓
Agent
  ↓
Tool Permission Check
  ↓
Authorization
  ↓
Input Validation
  ↓
Database Tool
  ↓
Output Validation
  ↓
Response
```

The model can decide what it wants to do, but the application controls whether that action is actually allowed.

This distinction is extremely important:

```text
Model Decision
      ≠
Authorized Action
```

---

# Complete Guardrail Architecture

The repository will explore guardrails at multiple layers.

```text
                         USER
                           |
                           v
                  +----------------+
                  | Input Security |
                  +----------------+
                           |
                           v
                    Prompt Builder
                           |
                           v
                         LLM
                           |
              +------------+------------+
              |                         |
              v                         v
            RAG                       Tools
              |                         |
              v                         v
       Retrieved Data            Permission Check
              |                         |
              v                         v
       Context Validation        Action Validation
              |                         |
              +------------+------------+
                           |
                           v
                   Output Validation
                           |
                           v
                    Final Response
                           |
                           v
                      Monitoring
```

---

# Input Guardrails

Input guardrails operate before the request reaches the model.

They can help identify:

```text
Malicious Input
Prompt Injection
Abuse
Sensitive Input
Unsupported Requests
Unexpected Input
```

Conceptually:

```text
User Input
    ↓
Input Validation
    ↓
Security Checks
    ↓
Allowed?
   / \
 Yes  No
  ↓    ↓
 LLM  Block
```

I will explore both rule-based and model-based approaches.

---

# Prompt Injection

Prompt injection is one of the most important security problems in LLM applications.

The basic problem is:

```text
Application Instructions
        +
User-Controlled Input
        ↓
        LLM
        ↓
Unexpected Behavior
```

The attacker attempts to influence the model's instruction hierarchy or behavior through input.

The important question is not only:

```text
Can the prompt injection work?
```

but:

```text
Why can the model interpret attacker-controlled text as an instruction?
```

This repository will explore the architectural reasons behind this behavior.

---

# Direct Prompt Injection

The attacker directly controls the input.

```text
Attacker
   ↓
Malicious Input
   ↓
Application
   ↓
LLM
   ↓
Manipulated Output
```

The attack surface is the user-controlled prompt.

I will explore:

```text
Instruction Conflicts
Context Manipulation
Role Manipulation
Instruction Override Attempts
Prompt Boundary Problems
```

and evaluate different defense strategies.

---

# Indirect Prompt Injection

Indirect prompt injection becomes particularly important in RAG systems.

The attacker may not directly attack the model.

Instead, malicious instructions can be inserted into content that the application later retrieves.

```text
Attacker
    ↓
Web Page / Document / Data
    ↓
Retriever
    ↓
LLM Context
    ↓
Model Processes Content
    ↓
Unexpected Behavior
```

This creates an important distinction:

```text
Trusted Instructions
        ≠
Untrusted Retrieved Content
```

A production system must treat external content as untrusted data.

---

# RAG Security

RAG introduces another security boundary.

A typical RAG system is:

```text
User
  ↓
Query
  ↓
Retriever
  ↓
Documents
  ↓
Context
  ↓
LLM
```

The security problem is that documents may contain:

```text
Sensitive Information
Malicious Instructions
Incorrect Information
Unauthorized Content
Poisoned Data
```

Therefore, RAG security needs to consider:

```text
Document Access
Document Trust
Metadata Filtering
Authorization
Retrieval Security
Context Isolation
Prompt Injection
Data Leakage
```

The goal is to ensure:

```text
User
  ↓
Authorized Query
  ↓
Authorized Documents
  ↓
Safe Context
  ↓
LLM
```

rather than:

```text
User
  ↓
Retrieve Everything
  ↓
LLM
```

---

# Data Leakage

Data leakage occurs when sensitive information becomes accessible to an unauthorized party.

Potential sources include:

```text
Training Data
RAG Documents
Database Queries
Tool Responses
System Instructions
Application Logs
Model Outputs
```

A secure system should consider:

```text
Who can access the data?
Why can they access it?
Where is the data retrieved?
Where is it sent?
Can the model expose it?
Is the output validated?
Is access logged?
```

---

# PII Protection

AI applications may process personally identifiable information.

Examples can include:

```text
Names
Email Addresses
Phone Numbers
Addresses
Identification Information
Customer Records
```

The system may need to detect and protect sensitive information before it reaches the model or before it is returned to the user.

A conceptual pipeline:

```text
Input
  ↓
PII Detection
  ↓
Redaction / Masking
  ↓
LLM
  ↓
Output PII Detection
  ↓
Final Response
```

I will explore the tradeoff between protection and model usefulness.

---

# Jailbreaking

Jailbreaking refers to attempts to bypass a model's intended safety behavior.

The general flow is:

```text
User
  ↓
Manipulated Request
  ↓
LLM
  ↓
Attempt to Circumvent Safety
  ↓
Unsafe Output
```

Jailbreaks are important because they demonstrate that safety behavior cannot always be assumed to be perfectly reliable.

I will explore:

```text
Why jailbreaks work
How model behavior changes under different instructions
How different defenses behave
How to evaluate defense robustness
```

The purpose is defensive research and evaluation.

---

# Output Guardrails

Even if the input looks safe, the generated response still needs to be evaluated.

```text
User Input
    ↓
LLM
    ↓
Generated Output
    ↓
Output Guardrail
    ↓
Allowed?
   / \
 Yes  No
  ↓    ↓
User  Block / Modify / Escalate
```

Output guardrails can check:

```text
Unsafe Content
Sensitive Information
Policy Violations
Incorrect Formats
Unexpected Tool Instructions
Potential Data Leakage
```

---

# Tool Security

Tools dramatically increase the capabilities of an AI system.

Without tools:

```text
LLM
 ↓
Text
```

With tools:

```text
LLM
 ├── Search
 ├── Database
 ├── API
 ├── File System
 ├── Code Execution
 └── External Services
```

The risk therefore increases.

A model generating text is one thing.

A model generating a tool call is different.

```text
LLM
 ↓
DeleteDatabase()
```

should never automatically mean:

```text
Action Executed
```

Instead:

```text
LLM
 ↓
Proposed Tool Call
 ↓
Validation
 ↓
Authorization
 ↓
Policy Check
 ↓
Human Approval if Required
 ↓
Execute
```

---

# Agent Security

Agentic systems introduce another level of risk because the model can perform multiple actions.

A simple agent loop:

```text
Goal
 ↓
LLM
 ↓
Tool
 ↓
Observation
 ↓
LLM
 ↓
Tool
 ↓
Observation
 ↓
...
 ↓
Final Answer
```

The system may perform many actions before producing the final answer.

This creates risks such as:

```text
Unauthorized Tool Usage
Excessive Tool Calls
Infinite Loops
Data Exfiltration
Privilege Escalation
Unintended Actions
Prompt Injection
```

Agent security therefore requires controlling not only the final answer but also the intermediate actions.

---

# Agent Guardrails

A production agent should have boundaries around its actions.

```text
User Goal
    ↓
Agent
    ↓
Plan
    ↓
Tool Request
    ↓
Permission Check
    ↓
Policy Check
    ↓
Execute
    ↓
Observation
    ↓
Agent
```

Important controls include:

```text
Tool Allowlist
Permission Checks
Input Validation
Output Validation
Rate Limits
Maximum Tool Calls
Action Limits
Human Approval
Audit Logs
```

---

# Least Privilege

A fundamental security principle is:

```text
Give the AI only the permissions it actually needs.
```

Bad architecture:

```text
AI Agent
    ↓
Administrator Access
    ↓
Everything
```

Better architecture:

```text
AI Agent
    ↓
Restricted Tool
    ↓
Specific Permission
    ↓
Specific Resource
```

For example:

```text
Agent
  ↓
ReadCustomerProfile
```

is safer than:

```text
Agent
  ↓
Full Database Access
```

This repository will explore least-privilege design for AI agents and tools.

---

# Authentication vs Authorization

These should not be confused.

Authentication asks:

```text
Who is the user?
```

Authorization asks:

```text
What is this user allowed to access?
```

An AI system should not rely on the LLM to make these decisions.

The application should enforce them.

```text
User
  ↓
Authentication
  ↓
Authorization
  ↓
Allowed Resources
  ↓
LLM / RAG / Tools
```

---

# Guardrails Are Not Perfect

An important part of this repository is understanding the limitations of guardrails.

A guardrail can fail because:

```text
The detector misses the attack
The model behaves unexpectedly
The policy is incomplete
The input is adversarial
The output changes across contexts
The attacker adapts
The tool has excessive permissions
The application trusts model output
```

Therefore:

```text
One Guardrail
      ↓
Not Enough
```

A stronger architecture uses defense in depth.

```text
Input Controls
      +
Model Controls
      +
Output Controls
      +
Tool Controls
      +
Authorization
      +
Monitoring
      +
Human Oversight
```

---

# Defense in Depth

The overall security philosophy of this repository is:

```text
Do not trust a single layer.
```

A production AI system can have:

```text
                 USER
                   ↓
            Input Validation
                   ↓
          Prompt Injection Check
                   ↓
             Authentication
                   ↓
             Authorization
                   ↓
                 LLM
                   ↓
             RAG Controls
                   ↓
            Tool Permission
                   ↓
            Action Validation
                   ↓
            Output Validation
                   ↓
               Logging
                   ↓
             Monitoring
                   ↓
          Security Response
```

If one layer fails, another layer should reduce the impact.

---

# AI Safety Evaluation

Building a defense is only half of the work.

We also need to measure whether it works.

A simple evaluation loop is:

```text
Attack / Test Case
       ↓
AI System
       ↓
Observed Behavior
       ↓
Safety Evaluation
       ↓
Security Evaluation
       ↓
Metrics
       ↓
Improve Defense
       ↓
Retest
```

I will explore evaluation for:

```text
Prompt Injection
Jailbreak Resistance
Data Leakage
PII Protection
Output Safety
Tool Security
Agent Security
RAG Security
```

---

# Security Metrics

The repository will investigate metrics such as:

```text
Attack Success Rate
Detection Rate
False Positive Rate
False Negative Rate
Blocked Request Rate
Unsafe Output Rate
Data Leakage Rate
Tool Authorization Failure Rate
Latency Overhead
Cost Overhead
```

The goal is to understand the tradeoff between:

```text
Security
   ↕
Usability
   ↕
Latency
   ↕
Cost
```

A guardrail that blocks everything is not necessarily a good production guardrail.

---

# Red Team and Blue Team

I will approach the repository from two perspectives.

## Red Team

Try to identify weaknesses.

```text
Attack
  ↓
Exploit
  ↓
Measure
  ↓
Understand Failure
```

## Blue Team

Build defenses.

```text
Weakness
  ↓
Defense
  ↓
Evaluation
  ↓
Monitoring
  ↓
Improvement
```

The objective is not to build attacks for misuse.

The objective is to understand attack behavior so that defensive systems can be designed and tested properly.

---

# Security Testing Lifecycle

The complete process becomes:

```text
Threat Model
     ↓
Identify Attack Surface
     ↓
Create Safe Test Cases
     ↓
Run Evaluation
     ↓
Measure Results
     ↓
Identify Weakness
     ↓
Build Defense
     ↓
Retest
     ↓
Deploy
     ↓
Monitor
     ↓
Improve
```

This creates a continuous security process rather than a one-time implementation.

---

# Threat Modeling AI Systems

Before building defenses, I want to understand the system itself.

For every AI application:

```text
What are the assets?
        ↓
Who are the users?
        ↓
What can users control?
        ↓
What can the model access?
        ↓
What tools exist?
        ↓
What data is sensitive?
        ↓
What actions can be performed?
        ↓
What happens if the model is manipulated?
```

This allows the security design to begin with the actual architecture.

---

# Example Threat Model

Consider an enterprise RAG application:

```text
Employee
   ↓
Chat Application
   ↓
LLM
   ↓
Retriever
   ↓
Company Documents
```

Potential threats:

```text
Unauthorized Document Access
Prompt Injection
Indirect Prompt Injection
Data Leakage
Sensitive Information Exposure
Malicious Documents
```

The security architecture might become:

```text
Employee
   ↓
Authentication
   ↓
Authorization
   ↓
Query Validation
   ↓
Retriever
   ↓
Permission Filtering
   ↓
Document Validation
   ↓
LLM
   ↓
Output Validation
   ↓
Audit Logging
```

---

# AI Safety in Production

A production AI system should not be treated as:

```text
Model + Prompt
```

It should be treated as a complete system:

```text
                  AI SYSTEM

            +------------------+
            |   Application    |
            +------------------+
                     |
          +----------+----------+
          |                     |
          v                     v
      Security               Safety
          |                     |
          +----------+----------+
                     |
                    LLM
                     |
          +----------+----------+
          |          |          |
          v          v          v
         RAG       Tools      APIs
          |          |          |
          +----------+----------+
                     |
                  Guardrails
                     |
                  Monitoring
```

This is the level of system thinking I want to develop through this repository.

---

# Production Guardrail Architecture

A more complete production architecture can look like:

```text
                               USER
                                 |
                                 v
                         Authentication
                                 |
                                 v
                         Authorization
                                 |
                                 v
                       Input Guardrails
                                 |
                                 v
                         Prompt Builder
                                 |
                                 v
                               LLM
                                 |
                 +---------------+---------------+
                 |                               |
                 v                               v
                RAG                            Tools
                 |                               |
          Document Access                 Tool Allowlist
                 |                               |
          Retrieval Filter                Permission Check
                 |                               |
          Context Validation               Input Validation
                 |                               |
                 +---------------+---------------+
                                 |
                                 v
                        Output Guardrails
                                 |
                                 v
                       Security Validation
                                 |
                                 v
                         Final Response
                                 |
                                 v
                           Monitoring
                                 |
                                 v
                         Audit / Logging
```

---

# What I Want to Explore

This repository is organized around understanding the complete problem.

```text
Why do LLM security problems exist?

Why can prompt injection work?

Why are system prompts not sufficient security boundaries?

What makes indirect prompt injection dangerous?

Why do jailbreaks work?

How can sensitive data leak through an AI application?

How should RAG systems handle untrusted documents?

How should authorization work with RAG?

How should tools be protected?

How should AI agents be restricted?

What does least privilege mean for an AI agent?

What are input guardrails?

What are output guardrails?

What are tool and action guardrails?

How should guardrails be evaluated?

What happens when a guardrail fails?

How do multiple guardrails work together?

How should AI security be monitored?

How should security incidents be investigated?

What does a production AI security architecture look like?
```

---

# Repository Structure

```text
ai-safety-security-and-guardrails/
│
├── README.md
├── LICENSE
├── pyproject.toml
├── requirements.txt
│
├── 01_ai_safety/
│   ├── fundamentals/
│   ├── reliability/
│   ├── robustness/
│   ├── misuse/
│   └── human_oversight/
│
├── 02_ai_security/
│   ├── fundamentals/
│   ├── threat_modeling/
│   ├── attack_surface/
│   ├── authentication/
│   ├── authorization/
│   └── least_privilege/
│
├── 03_llm_security/
│   ├── prompt_security/
│   ├── instruction_hierarchy/
│   ├── system_prompt_security/
│   ├── context_security/
│   └── model_behavior/
│
├── 04_prompt_injection/
│   ├── direct/
│   ├── indirect/
│   ├── detection/
│   ├── defenses/
│   └── evaluation/
│
├── 05_jailbreaks/
│   ├── fundamentals/
│   ├── safety_evaluation/
│   ├── detection/
│   └── defenses/
│
├── 06_data_security/
│   ├── data_leakage/
│   ├── pii/
│   ├── secrets/
│   ├── access_control/
│   └── output_protection/
│
├── 07_rag_security/
│   ├── document_security/
│   ├── retrieval_security/
│   ├── metadata_filtering/
│   ├── access_control/
│   ├── indirect_injection/
│   └── context_security/
│
├── 08_agent_security/
│   ├── agent_threats/
│   ├── tool_security/
│   ├── permission_control/
│   ├── action_validation/
│   ├── loops/
│   └── human_approval/
│
├── 09_guardrails/
│   ├── input_guardrails/
│   ├── output_guardrails/
│   ├── content_guardrails/
│   ├── pii_guardrails/
│   ├── injection_guardrails/
│   ├── tool_guardrails/
│   ├── agent_guardrails/
│   └── policy_guardrails/
│
├── 10_evaluation/
│   ├── safety/
│   ├── security/
│   ├── red_team/
│   ├── blue_team/
│   ├── attack_success/
│   ├── false_positives/
│   └── benchmarks/
│
├── 11_monitoring/
│   ├── logging/
│   ├── audit_trails/
│   ├── security_events/
│   ├── anomaly_detection/
│   └── incident_response/
│
├── 12_production/
│   ├── architecture/
│   ├── defense_in_depth/
│   ├── deployment/
│   ├── access_control/
│   ├── monitoring/
│   └── incident_response/
│
├── src/
│   └── ai_security/
│       ├── guardrails/
│       ├── detection/
│       ├── policies/
│       ├── evaluation/
│       ├── monitoring/
│       └── security/
│
├── configs/
├── notebooks/
├── scripts/
├── tests/
├── experiments/
└── artifacts/
```

---

# Technology

The repository will primarily use:

```text
Python
PyTorch
FastAPI
LLM APIs / Open Models
RAG Systems
Vector Databases
Docker
```

For security and guardrail experimentation, I will explore techniques involving:

```text
Input Validation
Output Validation
Policy Enforcement
PII Detection
Prompt Injection Detection
Content Classification
Authorization
Tool Permissioning
Rate Limiting
Logging
Monitoring
Evaluation
```

The exact tools may change as the implementations evolve.

The focus is on understanding the underlying engineering concepts rather than becoming dependent on one framework.

---

# Learning Path

The repository will progress from fundamentals to production.

```text
AI Safety Fundamentals
        ↓
AI Security Fundamentals
        ↓
Threat Modeling
        ↓
LLM Security
        ↓
Prompt Injection
        ↓
Jailbreaks
        ↓
Data Leakage
        ↓
PII Protection
        ↓
RAG Security
        ↓
Agent Security
        ↓
Tool Security
        ↓
Guardrails
        ↓
Security Evaluation
        ↓
Red Team / Blue Team
        ↓
Monitoring
        ↓
Incident Response
        ↓
Production AI Security
```

---

# From Attack Surface to Production Defense

The complete approach I want to develop is:

```text
Understand the System
        ↓
Identify Assets
        ↓
Identify Trust Boundaries
        ↓
Identify Attack Surface
        ↓
Understand Threats
        ↓
Build Safe Evaluations
        ↓
Measure Weaknesses
        ↓
Build Guardrails
        ↓
Enforce Authorization
        ↓
Validate Actions
        ↓
Validate Outputs
        ↓
Monitor Production
        ↓
Respond to Incidents
        ↓
Continuously Improve
```

---

# Final Goal

The final goal of this repository is to understand AI safety and security as a complete engineering discipline.

I want to move beyond the idea that:

```text
LLM
 +
System Prompt
 =
Secure AI
```

A real production system requires multiple layers:

```text
Authentication
        +
Authorization
        +
Input Validation
        +
Prompt Injection Defense
        +
RAG Security
        +
Tool Security
        +
Agent Controls
        +
Output Validation
        +
Guardrails
        +
Monitoring
        +
Auditability
        +
Human Oversight
```

The objective is to understand how these layers work together and where each layer can fail.

The repository therefore moves from:

```text
AI Safety
```

to:

```text
AI Security
```

to:

```text
LLM Security
```

to:

```text
RAG and Agent Security
```

to:

```text
Guardrails
```

and finally:

```text
Production AI Safety and Security
```

This is my hands-on exploration of how to build AI systems that are not only capable, but also controlled, secure, observable, and designed with failure in mind.
