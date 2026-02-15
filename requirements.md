# Requirements Document: SanchetVani (संचेतवाणी)

## Introduction

SanchetVani (संचेतवाणी – "Alert Voice") is designed to be a voice-first, AI-powered public health alert and intelligence platform for rural India. The system is intended to proactively deliver mandatory life-saving alerts and provide on-demand health scheme eligibility intelligence via automated voice calls and SMS, designed to work on ANY phone including basic feature phones.

## Glossary

- **Alert_System**: The core SanchetVani platform that will manage alert generation, processing, and delivery
- **Risk_Detection_Agent**: AI agent that will monitor incoming data streams for health risks
- **Risk_Scoring_Agent**: AI agent that will evaluate severity and geographic relevance of detected risks
- **Policy_Agent**: AI agent that will determine whether alerts require human approval or can be auto-delivered
- **Language_Agent**: AI agent that will generate localized messages in Indian languages
- **Eligibility_Agent**: AI agent that will process on-demand health scheme eligibility queries
- **Orchestration_Agent**: AI agent that will coordinate alert delivery via voice and SMS channels
- **Mandatory_Alert**: Life-saving alerts that will be auto-delivered without user consent (weather, WASH, disease outbreaks)
- **Optional_Alert**: Consent-based alerts that users will be able to opt into (scheme eligibility, campaigns, advisories)
- **Voice_Channel**: Amazon Connect-based voice call delivery system
- **SMS_Channel**: Amazon SNS-based text message delivery system
- **Feature_Phone**: Basic mobile phones without internet connectivity or smartphone capabilities
- **WASH**: Water, Sanitation, and Hygiene related health risks
- **Scheme_Eligibility**: Government health scheme qualification assessment based on user profile

## Requirements

### Requirement 1: Mandatory Alert Processing and Delivery

**User Story:** As a rural citizen, I want to receive critical health alerts automatically on my phone, so that I can take immediate protective action during health emergencies.

#### Acceptance Criteria

1. WHEN extreme weather conditions are detected (heatwave, coldwave, cyclone, flood), THE Alert_System is designed to use Amazon EventBridge to trigger AWS Lambda functions that will generate mandatory health protection alerts within 15 minutes
2. WHEN WASH contamination risks are identified, THE Alert_System is designed to leverage Amazon Bedrock AgentCore to create water safety alerts for affected geographic areas within 30 minutes
3. WHEN high-confidence disease outbreak patterns are detected, THE Alert_System is designed to use Amazon Bedrock LLMs to issue population-level health warnings within 1 hour
4. THE Alert_System is designed to use Amazon Connect to deliver mandatory alerts via voice calls to all registered phone numbers in affected areas without requiring user consent
5. WHEN delivering mandatory alerts, THE Alert_System is designed to attempt Amazon Connect voice calls first and fall back to Amazon SNS SMS delivery if voice delivery fails

### Requirement 2: Multi-Agent Risk Detection and Assessment

**User Story:** As a health department official, I want AI agents to continuously monitor health risk signals, so that threats are detected early and appropriate responses are triggered.

#### Acceptance Criteria

1. THE Risk_Detection_Agent is designed to use Amazon Bedrock AgentCore to continuously monitor weather data, environmental sensors, and health surveillance feeds through AWS Lambda functions
2. WHEN potential health risks are identified, THE Risk_Scoring_Agent is designed to use Amazon Bedrock LLMs to evaluate severity on a scale of 1-10 and determine geographic impact radius
3. THE Policy_Agent is designed to use Amazon Bedrock reasoning capabilities to classify risks as auto-alert (score ≥ 8) or human-approval-required (score 5-7) within 5 minutes
4. WHEN risk scores are below 5, THE Policy_Agent is designed to log the event to Amazon CloudWatch but not trigger alert generation
5. THE Orchestration_Agent is designed to use AWS Step Functions and Amazon EventBridge to coordinate between all agents and aim to provide proper alert workflow execution

### Requirement 3: Voice-First Communication System

**User Story:** As a rural citizen with a basic feature phone, I want to receive health information through voice calls in my local language, so that I can understand and act on important health guidance.

#### Acceptance Criteria

1. THE Voice_Channel is designed to use Amazon Connect to support automated voice calls to any phone number including feature phones
2. WHEN generating voice messages, THE Language_Agent is designed to use Amazon Bedrock LLMs to create content in Hindi, English, and major regional languages (Tamil, Telugu, Bengali, Marathi, Gujarati)
3. THE Voice_Channel is designed to use Amazon Polly text-to-speech with natural-sounding voices and appropriate regional accents and pronunciation
4. WHEN Amazon Connect voice calls fail or are not answered, THE Alert_System is designed to automatically send Amazon SNS SMS backup messages
5. THE Voice_Channel is designed to use Amazon Connect Contact Flows to provide interactive voice response (IVR) options for users to request additional information

### Requirement 4: On-Demand Health Scheme Eligibility Intelligence

**User Story:** As a rural citizen, I want to call a number and ask about my eligibility for government health schemes, so that I can access benefits I'm entitled to receive.

#### Acceptance Criteria

1. WHEN a user calls the eligibility helpline, THE Eligibility_Agent is designed to use Amazon Connect and Amazon Transcribe to collect basic demographic and economic information through voice interaction
2. THE Eligibility_Agent is designed to use Amazon Bedrock AgentCore to process user information against government health scheme databases stored in Amazon DynamoDB and eligibility criteria
3. WHEN eligibility assessment is complete, THE Eligibility_Agent is designed to use Amazon Bedrock LLMs and Amazon Polly to provide personalized scheme recommendations with application guidance
4. THE Alert_System is designed to only process eligibility requests with explicit user consent and data usage agreement, storing consent records in Amazon DynamoDB
5. THE Eligibility_Agent is designed to use Amazon Polly to explain eligibility decisions in simple language and provide next steps for scheme enrollment

### Requirement 5: Geographic Targeting and Personalization

**User Story:** As a district health officer, I want alerts to be sent only to relevant geographic areas and populations, so that resources are used efficiently and people receive pertinent information.

#### Acceptance Criteria

1. THE Alert_System is designed to maintain user location data in Amazon DynamoDB at district, block, and village levels for precise targeting
2. WHEN generating alerts, THE Alert_System is designed to use AWS Lambda functions with Amazon Location Service to determine affected geographic boundaries based on risk assessment data
3. THE Alert_System is designed to use Amazon DynamoDB queries to send alerts only to phone numbers registered in affected areas
4. WHEN users have demographic profiles stored in Amazon DynamoDB, THE Alert_System is designed to use Amazon Bedrock LLMs to customize alert content for vulnerable populations (elderly, pregnant women, children)
5. THE Alert_System is designed to support manual geographic override by authorized health officials through AWS Lambda functions for emergency situations

### Requirement 6: Consent Management and Privacy Protection

**User Story:** As a rural citizen, I want control over what optional health information I receive, so that I only get relevant communications while my privacy is protected.

#### Acceptance Criteria

1. THE Alert_System is designed to require explicit opt-in consent for all optional alert categories (schemes, campaigns, advisories)
2. WHEN users register, THE Alert_System is designed to clearly explain mandatory vs optional alert types and obtain appropriate consents
3. THE Alert_System is designed to provide voice-based options for users to modify their consent preferences at any time
4. THE Alert_System is designed to never share personal information with third parties without explicit user consent
5. WHEN processing eligibility requests, THE Alert_System is designed to store personal data only for the duration necessary to provide the service

### Requirement 7: Multi-Channel Delivery and Reliability

**User Story:** As a rural citizen with unreliable network connectivity, I want to receive health alerts through multiple channels, so that critical information reaches me even during network issues.

#### Acceptance Criteria

1. THE Alert_System is designed to use Amazon Connect as the primary channel to attempt voice delivery for all alerts
2. WHEN Amazon Connect voice calls fail after 3 attempts, THE Alert_System is designed to use Amazon SNS to send SMS messages as backup
3. THE Alert_System is designed to use AWS Lambda functions with Amazon SQS to retry failed deliveries with exponential backoff for up to 24 hours for mandatory alerts
4. THE Alert_System is designed to use Amazon CloudWatch to track delivery success rates and automatically adjust delivery strategies based on network conditions
5. WHEN both Amazon Connect voice and Amazon SNS SMS fail, THE Alert_System is designed to log failed deliveries to Amazon DynamoDB for manual follow-up by health workers

### Requirement 8: Real-Time Data Integration and Processing

**User Story:** As a system administrator, I want the platform to integrate with multiple data sources in real-time, so that health risks are detected as quickly as possible.

#### Acceptance Criteria

1. THE Alert_System is designed to use AWS Lambda functions to integrate with India Meteorological Department (IMD) weather APIs for real-time weather data
2. THE Alert_System is designed to use Amazon EventBridge to connect to state health department surveillance systems for disease outbreak monitoring
3. WHEN new data is received, THE Alert_System is designed to use Amazon Kinesis Data Streams and AWS Lambda to process it within 2 minutes using event-driven architecture
4. THE Alert_System is designed to use AWS Lambda functions with Amazon DynamoDB to maintain data quality checks and reject invalid or suspicious data inputs
5. THE Alert_System is designed to support manual data input by authorized health officials through AWS Lambda functions for emergency situations

### Requirement 9: Scalability and Performance

**User Story:** As a platform operator, I want the system to handle millions of users across India, so that the service remains reliable during large-scale health emergencies.

#### Acceptance Criteria

1. THE Alert_System is designed to use Amazon Connect with auto-scaling to support concurrent voice calls to at least 100,000 users within 30 minutes
2. THE Alert_System is designed to use AWS Lambda with Amazon Kinesis to process incoming data streams with less than 5 seconds latency under normal load
3. WHEN system load exceeds 80% capacity, THE Alert_System is designed to use AWS Auto Scaling to automatically scale Amazon EC2 instances and AWS Lambda compute resources
4. THE Alert_System aims to maintain 99.9% uptime for critical alert delivery functions using AWS managed services and multi-AZ deployment
5. THE Alert_System is designed to use AWS Application Load Balancer and Amazon CloudFront to handle traffic spikes of 10x normal load without service degradation

### Requirement 10: Monitoring, Analytics, and Compliance

**User Story:** As a health department administrator, I want comprehensive monitoring and reporting capabilities, so that I can measure platform effectiveness and ensure regulatory compliance.

#### Acceptance Criteria

1. THE Alert_System is designed to use Amazon CloudWatch to track delivery success rates, response times, and user engagement metrics in real-time
2. THE Alert_System is designed to use Amazon QuickSight to generate daily, weekly, and monthly reports on alert effectiveness and population reach
3. THE Alert_System is designed to use AWS CloudTrail and Amazon DynamoDB to maintain audit logs of all alert decisions, approvals, and delivery attempts for regulatory compliance
4. THE Alert_System is designed to use Amazon QuickSight dashboards to provide geographic coverage, demographic reach, and health outcome correlations
5. THE Alert_System is designed to use Amazon CloudWatch Alarms to alert administrators when delivery success rates fall below 85% or system performance degrades
