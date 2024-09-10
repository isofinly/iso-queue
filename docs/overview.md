# Flow Manager Interactions

The Flow Manager interacts with several other components of the distributed job system to manage job dependencies, execute workflows, and ensure proper sequencing of tasks. Here's a breakdown of its key interactions:

## 1. Interaction with Job Scheduler

- **Workflow Initiation**: When a new flow is submitted, the Flow Manager communicates with the Job Scheduler to create and schedule the initial jobs in the workflow.
- **Dependency Management**: The Flow Manager informs the Job Scheduler about job dependencies, ensuring that dependent jobs are not scheduled until their prerequisites are met.
- **Job Status Updates**: The Job Scheduler notifies the Flow Manager when jobs complete, fail, or reach specific milestones, allowing the Flow Manager to update the flow state and trigger dependent jobs.

## 2. Interaction with Queue Manager

- **Queue Selection**: The Flow Manager works with the Queue Manager to determine appropriate queues for jobs within a flow, considering factors like job priority and queue-specific concurrency limits.
- **Queue State Monitoring**: The Flow Manager monitors queue states through the Queue Manager to make informed decisions about when to release dependent jobs into the queues.

## 3. Interaction with Worker Supervisor

- **Job Execution Feedback**: Workers report job execution status back to the Flow Manager (often via the Job Scheduler), allowing it to update the flow state and make decisions about dependent jobs.
- **Flow-Specific Instructions**: The Flow Manager may provide flow-specific instructions or context to workers executing jobs that are part of a flow.

## 4. Interaction with Rate Limiter

- **Flow-Level Rate Limiting**: For flows that require rate limiting, the Flow Manager consults with the Rate Limiter to ensure that job releases within a flow adhere to specified rate limits.
- **Global Flow Coordination**: In scenarios where multiple flows might impact shared resources, the Flow Manager works with the Rate Limiter to coordinate across flows and prevent system overload.

## 5. Cross-Node Coordination

- **Distributed State Management**: The Flow Manager maintains flow states in a distributed manner, often using tools like `:pg` (process groups) or a distributed database like         to share flow information across nodes.
- **Node Failure Handling**: In case of node failures, the Flow Manager coordinates with its counterparts on other nodes to reassign and recover in-progress flows.

## 6. Interaction with External Systems

- **Webhook Notifications**: For critical flow events (e.g., flow completion, failure), the Flow Manager may trigger notifications to external systems.
- **External Dependency Integration**: In cases where flows depend on external systems, the Flow Manager may interact with these systems to check conditions or trigger actions.

## Key Functionalities Facilitated by These Interactions

1. **Bulk Flow Operations**: When adding flows in bulk, the Flow Manager coordinates with the Job Scheduler and Queue Manager to efficiently process and schedule multiple flows.

2. **Flow Tree Management**: The Flow Manager maintains the structure of complex flow trees, updating the state as jobs complete and triggering child jobs when parent jobs finish.

3. **Failure Handling**: When a job in a flow fails, the Flow Manager determines the impact on parent and child jobs, potentially triggering failure cascades or recovery processes.

4. **Dynamic Flow Modification**: The Flow Manager allows for runtime modifications to flows, such as removing dependencies or ignoring certain failed jobs, by updating its internal state and communicating changes to the Job Scheduler and Queue Manager.

5. **Performance Optimization**: By understanding the full structure of flows, the Flow Manager can work with other components to optimize job scheduling and resource allocation across the distributed system.

These interactions allow the Flow Manager to orchestrate complex workflows across the distributed system, ensuring that jobs are executed in the correct order, dependencies are respected, and the system remains efficient and responsive even under complex workflow scenarios.