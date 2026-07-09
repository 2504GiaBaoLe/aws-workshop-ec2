---
title: "Week 6 Worklog"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

## Week 6 Objectives

During this week, I focused on learning AWS Application Integration services. The main objective was to understand how different AWS services communicate with each other in an event-driven architecture. Instead of relying on direct communication between applications, AWS provides messaging and workflow services that make systems more scalable, reliable, and loosely coupled.

By the end of the week, I expected to:

- Understand the concept of asynchronous communication.
- Learn how Amazon SQS works as a message queue.
- Understand the publish/subscribe model using Amazon SNS.
- Explore how Amazon EventBridge routes events between AWS services.
- Learn how AWS Step Functions coordinate multiple tasks into a workflow.
- Build a simple event-driven application by combining these services.

---

## Tasks Carried Out

| Day | Task | Start Date | Completion Date | Reference |
| --- | ---- | ---------- | --------------- | --------- |
| 2 | Studied Application Integration concepts, asynchronous communication, and Event-driven Architecture. | 05/25/2026 | 05/25/2026 | https://cloudjourney.awsstudygroup.com/ |
| 3 | Learned Amazon SQS including Standard Queue, FIFO Queue, Visibility Timeout, and Dead Letter Queue. | 05/26/2026 | 05/27/2026 | https://cloudjourney.awsstudygroup.com/ |
| 4 | Studied Amazon SNS and Amazon EventBridge. Practiced creating SNS Topics, Email Subscriptions, and EventBridge Rules. | 05/27/2026 | 05/28/2026 | https://cloudjourney.awsstudygroup.com/ |
| 5 | Learned AWS Step Functions, including State Machine, Task State, Choice State, Retry, and Error Handling. | 05/28/2026 | 05/28/2026 | https://cloudjourney.awsstudygroup.com/ |
| 6 | Built a simple event-driven workflow by integrating SNS, SQS, Lambda, EventBridge, and Step Functions. | 05/29/2026 | 05/30/2026 | https://cloudjourney.awsstudygroup.com/ |

---

# Study Notes

## 1. Understanding Application Integration

Before this week, I mainly worked with AWS services individually. After studying Application Integration, I realized that cloud applications are usually composed of many independent services. Instead of calling each other directly, they communicate through events and messages.

This approach reduces dependencies between services and makes applications easier to scale and maintain.

One important concept I learned is **asynchronous communication**. Unlike synchronous communication where a client waits for an immediate response, asynchronous communication allows the sender to continue working after sending a message. The receiver processes the message whenever it is ready.

I found this concept especially useful for systems that need to process many requests without slowing down users.

---

## 2. Amazon SQS

Amazon Simple Queue Service (SQS) is a managed message queue service.

During my study, I learned the difference between Standard Queue and FIFO Queue.

- Standard Queue provides high throughput but messages may be delivered more than once.
- FIFO Queue guarantees message order and avoids duplicate processing.

I also learned about:

- Visibility Timeout
- Long Polling
- Dead Letter Queue (DLQ)

Initially, I thought messages disappeared immediately after being read. After studying Visibility Timeout, I understood that messages remain hidden temporarily until they are successfully processed. If processing fails, they can become visible again for another consumer.

This mechanism helps improve system reliability.

---

## 3. Amazon SNS

Amazon SNS follows the Publish/Subscribe model.

Instead of sending notifications directly to multiple applications, a publisher only needs to publish one message to an SNS Topic. Every subscriber automatically receives the notification.

During practice, I:

- Created an SNS Topic.
- Added an Email Subscription.
- Confirmed the subscription through email.
- Published several test messages.

Seeing the notification appear in my inbox helped me understand how SNS distributes information to multiple subscribers simultaneously.

---

## 4. Amazon EventBridge

EventBridge allows AWS services to react automatically whenever an event occurs.

I learned that EventBridge continuously monitors events and routes them to the appropriate target based on predefined rules.

During practice, I:

- Created EventBridge Rules.
- Defined Event Patterns.
- Selected target services.
- Tested event routing.

Compared with SNS, I found that EventBridge is more suitable when routing events based on conditions rather than simply broadcasting notifications.

---

## 5. AWS Step Functions

AWS Step Functions was one of the most interesting topics this week.

Instead of writing workflow logic inside application code, Step Functions allow developers to visually define workflows using State Machines.

I explored several state types:

- Task State
- Choice State
- Wait State
- Succeed State
- Fail State

I also learned how Retry and Catch help handle failures automatically.

At first, the workflow diagram looked complicated, but after creating several simple State Machines, I understood that each state represents a single step of the business process.

This makes workflows much easier to read and maintain.

---

## 6. Practice: Building an Event-driven Workflow

To reinforce what I learned, I created a simple event-driven workflow.

The workflow was organized as follows:

SNS → SQS → Lambda → Step Functions

I also explored how EventBridge could trigger workflows automatically when specific events occur.

Although the project was relatively simple, it helped me understand how each AWS service has its own responsibility:

- SNS distributes notifications.
- SQS stores messages safely.
- Lambda processes incoming messages.
- EventBridge detects and routes events.
- Step Functions coordinate multiple processing steps.

Seeing these services work together gave me a much clearer understanding of event-driven architecture.

---

# Personal Understanding

This week changed the way I think about cloud application design.

Previously, I believed services should communicate directly with each other. After learning Application Integration, I realized that using messaging services creates a more flexible and scalable architecture.

The biggest lesson for me was understanding that every service has a different responsibility:

- SNS focuses on notification.
- SQS focuses on reliable message delivery.
- EventBridge focuses on event routing.
- Step Functions focus on workflow orchestration.

Although these services seem similar at first, each one solves a different problem.

I also noticed that AWS encourages developers to build loosely coupled systems. Instead of creating one large application, it is better to divide responsibilities into independent services connected through events.

This learning will be useful when I study serverless architectures and microservices in the coming weeks.

---

# Challenges

Some concepts were initially difficult to distinguish, especially the differences between SNS, SQS, and EventBridge.

At first, I wondered why AWS provides several messaging services instead of only one. After reading documentation and practicing with simple examples, I understood that they serve different communication patterns.

Another challenge was understanding how Step Functions interact with other AWS services. After creating several State Machines and observing the execution flow, I became more comfortable reading workflow diagrams.

---

# Summary

By the end of Week 6, I gained a solid understanding of AWS Application Integration services and their roles in building event-driven systems.

Instead of studying each AWS service independently, I learned how they can be combined to create reliable, scalable, and loosely coupled applications. This week also strengthened my understanding of modern cloud architecture and prepared me for more advanced AWS services in future weeks.