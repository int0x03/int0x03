---
title: "Eric Tian"
permalink: /reflections/resume/
---

[中文]({{ '/reflections/zh-cn/resume/' | relative_url }})

**Eric Tian**

Site Reliability Engineer (SRE) | Software Engineer
https://www.tianxiaohui.com/

## Professional Summary

Software engineer with 19 years of experience in software development and
production operations, including 10 years in SRE. Extensive experience in
incident response, diagnosis, remediation, and post-incident review for
high-concurrency, high-traffic production environments serving users worldwide.
Experienced with cloud infrastructure based on OpenStack and Kubernetes and
proficient in developing production-grade software and operations automation
tools in Python. Skilled at improving system reliability and diagnostic
efficiency through observability, automation, machine learning, and large
language models (LLMs), with additional experience in Java performance tuning
and building and operating distributed systems.

## Core Competencies

- **Troubleshooting:** Analyze application metrics and operational data to map
  the symptoms, impact, and causal relationships of system failures, then
  rapidly identify and resolve issues in high-concurrency, high-traffic
  production environments
- **Reliability management:** Service Level Agreements (SLAs), Service Level
  Indicators (SLIs), Service Level Objectives (SLOs), capacity planning, and
  continuous monitoring and evaluation of service availability, latency, and
  performance targets
- **Incident management:** Incident response and review, root cause analysis
  (RCA), and metrics including time to detect (TTD), time to restore (TTR), and
  mean time to restore (MTTR)
- **Java performance tuning:** Garbage collection (GC) tuning, thread and stack
  analysis, and memory leak analysis
- **Observability:** Metrics collection, log aggregation, distributed tracing,
  Prometheus, Grafana, Loki, and OpenTelemetry
- **Intelligent diagnostics:** Machine learning- and LLM-based alert, log, and
  metric analysis, plus iterative development of agents and skills
- **Cloud and containers:** Linux, OpenStack, Kubernetes, and version-controlled,
  automated management of containerized environments
- **Distributed systems:** Building and operating highly available,
  high-concurrency, high-traffic systems, with experience in microservices,
  cloud-native systems, and Development and Operations (DevOps) practices
- **Development and delivery:** Production-grade Python software and operations
  automation, with additional experience in Java, Shell, and continuous
  integration and continuous delivery (CI/CD)
- **Global traffic:** Content Delivery Networks (CDNs), multi-tier load
  balancing, and cross-region traffic management

## Selected Projects

### Sherlock.ai

- Built an automated diagnostic framework combining artificial intelligence
  (AI) and LLMs. It integrates agents, skills, and Retrieval-Augmented
  Generation (RAG) to transform manual troubleshooting procedures into
  automated agent workflows and continuously improve diagnostic capabilities.
- Used the company's internal agent runtime to organize issue context, invoke
  skills and RAG for internal static and dynamic knowledge, and retrieve
  metrics, logs, traces, topology, and configuration through Application
  Programming Interfaces (APIs) and the Model Context Protocol (MCP), ultimately
  generating root cause assessments and remediation recommendations.
- Captured complete analysis and diagnostic processes for reuse in subsequent
  investigations, validating skill effectiveness and supporting continuous
  improvement.
- Organized, categorized, and iteratively updated skill and RAG documentation.
- Regularly replayed and scored representative incidents to evaluate diagnostic
  performance and drive ongoing improvements to skills and RAG documentation.

### Lepenseur

- Built an LLM-based automated question-answering system before the agent
  runtime had fully matured, responding to user questions about monitoring,
  alerts, logs, traces, and topology.
- Collected one year of real-world question-and-answer data and built a RAG
  knowledge base after cleaning, filtering, and categorizing it. Selected
  representative questions for evaluation by the relevant on-call engineers.
- Identified the tools required by agents from the RAG content, then categorized
  and consolidated them into an agent skill library.
- Enabled agents to engage automatically when users asked questions, explore
  possible causes concurrently, synthesize conclusions, and respond while
  preserving the complete analysis trace for review and optimization.

### SLO Optimization System

- Estimated reasonable SLO ranges for specific Uniform Resource Locators (URLs)
  from existing latency and traffic metrics and recommended configuration
  changes to users.
- Analyzed tracing and log data for URLs that failed to meet their SLOs within a
  defined time window, identifying potential issues and recommending
  optimizations.
- Combined logs and source code for critical URLs and performed static code
  analysis to identify potential performance issues and recommend improvements.

### Groot

- Automatically analyzed application and infrastructure metric trends
  associated with alerts across business units, inferred possible causes, and
  used machine learning models to rank their likelihood and recommend the most
  probable causes.
- Extracted knowledge from historical production incidents, including alert
  details, related applications, key metrics and logs, topology relationships
  between business domains and applications, and historical configuration
  changes, then used it as training data for machine learning models.
- Applied statistical anomaly detection and similarity algorithms to compare
  alert signals with application and infrastructure metrics, then filtered and
  ranked the results.

## Professional Experience

### eBay — Senior Site Reliability Engineer

**September 2016–Present**

- Lead front-line troubleshooting, diagnosis, analysis, remediation, and
  post-incident reviews for eBay's global sites. Partner with development teams
  to track and resolve application and infrastructure issues in production and
  participate in on-call response and incident management.
- Launched and operate automated SLA, SLI, and SLO applications that manage more
  than 4,000 production SLIs and support continuous monitoring and evaluation of
  service availability, latency, and performance targets.
- Established and track TTD, TTR, and MTTR metrics, using RCA and post-incident
  reviews to continuously improve incident response and recovery processes.
- Participate in production capacity planning and contributed to a Kubernetes
  container autoscaling system driven by production metrics to address changing
  traffic and resource demands.
- Use cloud infrastructure based on OpenStack and Kubernetes to support the
  operation and evolution of production services and automation systems.
- Develop production-grade diagnostic and operations automation tools in Python
  to improve troubleshooting efficiency.
- Built machine learning- and large-scale automation-based systems for anomaly
  monitoring, alert analysis, automated diagnosis, and root cause
  recommendations.
- Built an LLM-based automated diagnostic framework that uses agents and skills
  to transform manual troubleshooting procedures into automated analysis
  workflows and continuously improve diagnostic capabilities.

### eBay — Software Engineer, Release Engineering

**October 2010–August 2016**

- Developed, maintained, and supported build and release tools.
- Built and continuously improved CI/CD, product registration, build, release,
  deployment, and monitoring processes and their supporting tools and platforms
  as the platform architecture, cloud infrastructure, and DevOps practices
  evolved.
- Developed build, release, and operations automation tools using Java, Python,
  and Shell.

### eBao Software Co., Ltd. — Software Engineer

**April 2008–September 2010**

- Designed and developed core property insurance systems for a major European
  insurer and integrated third-party APIs.
- Contributed to policy administration, claims management, premium calculation,
  government vehicle registration, and address validation modules.

### Augmentum Software Co., Ltd. — Software Engineer

**July 2007–April 2008**

- Designed and developed an Office Automation (OA) system.
- Contributed to core modules including workflow, process, and rules engines.

## Education

**Bachelor's Degree in Information Management and Information Systems**, Henan
University

2003–2007
