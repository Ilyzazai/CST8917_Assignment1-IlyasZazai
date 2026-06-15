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


