# CST8917 Assignment 1: Serverless Computing - Critical Analysis

**Name:** Ilyas Zazai  
**Student Number:**  41173490    
**Course:** CST8917 - Serverless Applications  
**Assignment:** Assignment 1 - Serverless Computing Critical Analysis  
**Date:** June 14, 2026  

---

## Part 1: Paper Summary

The paper Serverless Computing: One Step Forward, Two Steps Back by Hellerstein et al. (2019) talks about the good and bad sides of early serverless computing. The main idea of the paper is that serverless computing is useful but the first version of Function-as-a-Service platforms also created many problems for developers. The “one step forward” means that serverless made cloud development easier because developers do not need to manage servers, scaling or infrastructure manually. Functions can run when they are needed and users only pay for the time the function is running. This is very useful for small tasks, event-based applications, and simple cloud workloads. However the “two steps back” means that serverless also removed some important abilities that developers need for bigger and more complex systems (Hellerstein et al., 2019).

One important limitation is execution time. In first-generation serverless platforms the functions are short-lived. The paper explains that AWS Lambda functions had a maximum execution time of 15 minutes. This is a problem for long-running jobs because the work needs to be divided into smaller parts. Developers also need to save progress somewhere else because the function cannot keep running forever. Also even if a function has a warm start, there is no guarantee that the next function call will run on the same machine. Because of this, developers cannot depend on local memory or local state (Hellerstein et al., 2019) .

Another limitation is communication between functions. In normal distributed systems, services can communicate with each other directly over the network. But in serverless FaaS, functions are not directly addressable. This means that one function cannot easily call another function like a normal service. Instead functions usually communicate using queues, storage, databases, or other cloud services. The paper says this can create an I/O bottleneck because data must go through slower storage or network services instead of direct communication (Hellerstein et al., 2019) .

The paper also explains the “data shipping” problem. In many systems, it is better to move the code closer to the data. But in early serverless then data often has to move to the function. This can be expensive and slow when there is a lot of data. For example, machine learning, analytics, and big data workloads might need to move large amounts of data from storage into functions. This can increase latency, cost, and network usage (Hellerstein et al., 2019) 
Another issue is limited hardware access. Early serverless platforms mostly gave developers CPU and memory, but not GPUs or special hardware. This is a problem for workloads like machine learning, video processing, and scientific computing. These workloads often need GPUs or other accelerators to run efficiently (Hellerstein et al., 2019).

The paper also says that serverless is not very strong for distributed computing and stateful workloads. Functions are stateless, short-lived, and not good at direct communication. This makes it difficult to build applications that need coordination, shared state, or long-running distributed processes (Hellerstein et al., 2019) .

For future cloud programming, the authors suggest some improvements. First, cloud platforms should support better placement of code and data, so the system can move computation closer to the data when needed. Second, serverless should support different types of hardware, such as GPUs and accelerators. Third, the authors suggest long-running and addressable cloud agents that can keep identity over time but still scale like serverless. They also mention better programming models, better security, and better service-level objectives for cloud applications (Hellerstein et al., 2019).

---

## Part 2: Azure Durable Functions Deep Dive

### 1. Orchestration Model

Azure Durable Functions adds a workflow model on top of normal Azure Functions. In a Durable Functions application there are mainly three main parts which are client functions, orchestrator functions, and activity functions. The client function starts the workflow. The orchestrator function controls the steps of the workflow. The activity functions doing the actual work, such as calling an API, processing data, or saving something to storage (Microsoft Learn, n.d.-a) . This is different from basic FaaS because a normal function usually starts, does one job, and finishes. Durable Functions can control many steps in order or in parallel. This helps with one criticism from the paper because developers do not need to manually connect many small functions using only queues and storage. However, Durable Functions is still based on serverless functions, so it can improves the workflow problem but does not completely remove all FaaS limitations.

### 2. State Management

Durable Functions manages state by using event sourcing, checkpointing, and replay. It means the system saves the history of the workflow. When the orchestrator waits for an activity function, timer or external event Durable Functions saves the progress. If the function host restarts, the orchestrator can replay the history and rebuild the workflow state (Microsoft Learn, n.d.-b) . This helps with the paper’s criticism that serverless functions are stateless. With Durable functions, the workflow can continue even if the function stops or the server changes. But this doesn’t mean the state is kept in memoryfor  forever. The state is stored in a backend storage system, such as Azure Storage or another supported provider. So Durable Functions gives state to the workflow, but it still depends on storage and the orchestrator must be written carefully (Microsoft Learn, n.d.-b).

### 3. Execution Timeouts

Durable Functions helps with long-running workflows. A normal Azure Function has timeout limits depending on the hosting plan. For example, the Consumption plan has a default timeout of 5 minutes and a maximum of 10 minutes. also, HTTP-triggered functions have a 230-second response limit because of the Azure Load Balancer timeout (Microsoft Learn, n.d.-d) . Durable Functions works differently because the orchestrator doesn’t need to run the whole time. It saves progress, stops when it is waiting, and starts again later. Because of this, an orchestration can run for a very long time, such as days, months, or longer (Microsoft Learn, n.d.-b). This helps with the paper’s concern about short execution time. But activity functions are still normal functions, so they still have timeout limits. Durable Functions solves the long workflow problem, but not unlimited execution for every single function (Microsoft Learn, n.d.-e).

### 4. Communication Between Functions

In Durable Functions the orchestrator communicates with activity functions through the Durable Functions runtime. The orchestrator schedules activity functions waits for their results, and stores the workflow history. The runtime handles events, activity results, timers and external inputs (Microsoft Learn, n.d.-c). This is easier than manually using many queues, database records, or storage messages to connect functions. It makes communication between workflow steps more organized and reliable. it partially addresses the paper’s criticism about functions needing storage intermediaries. However, Durable Functions still uses a backend storage system to manage state and communication. It doesn’t give functions direct network addressability like normal long-running services. So it is better for workflow coordination, but it is not the same as fast direct communication between distributed services.

### 5. Parallel Execution: Fan-Out/Fan-In

Durable Functions supports the fan-out/fan-in pattern. In this pattern, the orchestrator starts many activity functions at the same time. Each activity does part of the work. After that, the orchestrator waits for all activities to finish and then combines the results. it is useful for batch processing, image processing, file processing, or any task where the work can be divided into smaller independent parts. This helps with the paper’s concern that serverless is weak for distributed computing. Durable Functions makes parallel work easier because the developer doesn’t need to manually track every worker and every result. However, this works best when tasks are independent. It is not perfect for systems that need very fast communication between workers. Microsoft also explains that fan-in happens inside one orchestrator instance, so performance can still have some limits (Microsoft Learn, n.d.-e).

---

## Part 3: Critical Evaluation

Azure Durable Functions is a real improvement for serverless computing, but I don’t think its fully solves all the problems from the paper. According to my opinion Durable Functions is a good practical solution for many workflow problems, but it mostly works around serverless limitations instead of completely fixing them.

One problem that is still not fully solved is communication between functions. The paper says that early FaaS functions are not directly addressable, so they usually need to communicate through storage, queues, or other services. Durable Functions makes this much easier because the orchestrator can call activities, wait for results, retry failed steps, and keep the workflow history. From the developer side, this is much better than manually building the full connection between many functions. However, Durable Functions still uses a storage backend and event history behind the scenes (Microsoft Learn, n.d.-d) . it means it is reliable but it is not the same as direct and fast communication between services. For applications that need very fast communication, such as distributed databases or high-performance computing Durable Functions may not be the best choice.

Another problem that is still not fully solved is the data shipping problem. The paper explains that serverless often moves data to the function instead of moving the function closer to the data (Microsoft Learn, n.d.-a) . Durable Functions does not really change this. activity functions may still need to read data from Blob Storage, Cosmos DB, APIs, queues or other services. The orchestrator can decide the order of the work, but it does not automatically place the function beside the data. This means large data workloads can still have latency, network cost and performance issues. Durable Functions is good for controlling workflow steps, but it is not a full data-processing engine.

Hardware access is also still a limitation. The paper says that first-generation serverless didn’t provide good access to GPUs or specialized hardware (Microsoft Learn, n.d.-a) . Durable Functions itself does not solve this problem. It is mainly for workflow orchestration, not GPU scheduling or hardware management. If the application needs GPUs for machine learning or video processing, developers usually need other Azure services, such as Azure Machine Learning, Azure Batch, AKS, or container-based services. So Durable Functions helps with orchestration, but not with every type of compute requirement.

At the same time, Durable Functions clearly improves some weaknesses of normal serverless functions. It supports stateful workflows, checkpointing, replay, retries, durable timers, external events, and long-running orchestration instances (Microsoft Learn, n.d.-b) . These features make serverless more useful for real business applications. for example it can be used for order processing, approval workflows, batch jobs, monitoring, and cloud service integration. In these cases Durable Functions gives developers a clean way to build workflows without managing servers.

My final verdict is that Azure Durable Functions is an important step forward, but it’s not the full solution that the paper’s authors wanted. The authors wanted future cloud platforms to support better code and data placement, specialized hardware, long-running addressable agents, and stronger distributed programming models (Microsoft Learn, n.d.-a) . Durable Functions improves serverless by adding orchestration and workflow state, but it doesn’t fully solve low-latency communication, data locality or hardware access, So I think Durable Functions is a strong improvement for serverless workflows, but it is still a workaround for some deeper problems in FaaS.


---

## References

Hellerstein, J. M., Faleiro, J., Gonzalez, J. E., Schleier-Smith, J., Sreekanti, V., Tumanov, A., & Wu, C. (2019). Serverless Computing: One Step Forward, Two Steps Back. CIDR 2019.
https://www.cidrdb.org/cidr2019/papers/p119-hellerstein-cidr19.pdf

Hellerstein et al. (2019). Serverless Computing: One Step Forward, Two Steps Back - arXiv version.
https://arxiv.org/abs/1812.03651

Microsoft Learn. (n.d.-a) .  Durable Functions overview.
https://learn.microsoft.com/en-us/azure/durable-task/durable-functions/durable-functions-overview

Microsoft Learn. (n.d.-b). Durable orchestrations overview.
https://learn.microsoft.com/en-us/azure/durable-task/common/durable-task-orchestrations

Microsoft Learn. (n.d.-c). Bindings for Durable Functions in Azure Functions.
https://learn.microsoft.com/en-us/azure/durable-task/durable-functions/durable-functions-bindings

Microsoft Learn. (n.d.-d). Azure Functions scale and hosting.
https://learn.microsoft.com/en-us/azure/azure-functions/functions-scale

Microsoft Learn. (n.d.-e). Performance and scale in Durable Functions.
https://learn.microsoft.com/en-us/azure/durable-task/durable-functions/durable-functions-perf-and-scale

Microsoft Learn. (n.d.-f). Fan-out/fan-in pattern in Durable Functions.
https://learn.microsoft.com/en-us/azure/durable-task/common/durable-task-fan-in-fan-out

---

## AI Disclosure Statement

I used AI to help me understand the paper, organize the assignment, explain Azure Durable Functions, improve grammar and convert my docs to MD file.
