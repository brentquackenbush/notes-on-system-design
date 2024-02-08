# Task Scheduler 

## Introduction to Task Schedulers
A task scheduler is a system used for managing the execution of computer programs or scripts at pre-defined times intervals. It's a critical component in both `operating systems` and `application software`, allowing for the automation of tasks, optimization of system resources, and management of background processes. In the context of business and computing, task schedulers play a pivotal role in ensuring that essential operations, such as data backups, batch processing, and system maintenance, occur without manual intervention and at optimal times.

In modern computing environments, task schedulers are not limited to traditional time-based execution. They also consider factors like resource availability, priority of tasks, and system load, making them more dynamic and adaptable to complex computing needs.

### Case Studies and Examples of Task Schedulers

#### Google's Borg
- **Description**: [Borg](https://research.google/pubs/large-scale-cluster-management-at-google-with-borg/) is Google's cluster manager and task scheduler, responsible for job scheduling and resource management across thousands of machines. It's designed for high availability and efficient resource utilization.
- **Impact**: Borg helps in running multiple applications across Google's massive infrastructure, ensuring optimal resource usage and task prioritization.

#### Apache Hadoop YARN
- **Description**: [Yet Another Resource Negotiator](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/YARN.html) (YARN) is a key component of the Apache Hadoop ecosystem. It manages and schedules resources and tasks in Hadoop, allowing for efficient processing of large data sets.
- **Impact**: YARN has been instrumental in enabling scalable and flexible data processing for big data applications, significantly impacting data analysis and storage solutions.

#### [Amazon EC2 Auto Scaling](https://aws.amazon.com/ec2/)
- **Description**: EC2 Auto Scaling in Amazon Web Services (AWS) ensures the correct number of EC2 instances are running to handle the load. It schedules scaling actions based on specified conditions.
- **Impact**: This scheduler is crucial for maintaining application performance and availability, ensuring cost-effectiveness by scaling resources as needed.

## Types of Task Scheduling

### Single OS-Based Node Scheduling
- **Focus**: Managing system resources and prioritizing tasks within an operating system.
- **Examples**: Unix-like systems' [`cron`](https://ostechnix.com/a-beginners-guide-to-cron-jobs/) for time-based job scheduling, Windows Task Scheduler for automating tasks.

### Cloud Computing Scheduling (Kubernetes)
- **Focus**: Orchestrating containerized applications, managing their lifecycle, ensuring resource utilization.
- **Example**: Kubernetes scheduler assigns containers to nodes, considering resource availability and constraints.

### Large Distributed Systems
- **Approach**: Managing tasks across numerous machines, often in different geographic locations.
- **Challenges**: High availability, network latencies, load balancing.
- **Example Technologies**: Apache Mesos, Kubernetes, Borg.

### Functional Requirements
- **Submit tasks**: The system should allow the users to submit their tasks for execution.
- **Allocate resources**: The system should be able to allocate the required resources to each task.
- **Remove tasks**: The system should allow the users to cancel the submitted tasks.
- **Monitor task execution**: The task execution should be adequately monitored and rescheduled if the task fails to execute.
- **Efficient resource utilization**: The resources (CPU and memory) must be used efficiently in terms of time, cost, and fairness. Efficiency means that we do not waste resources. For example, if we allocate a heavy resource to a light task that can easily be executed on a cheap resource, it means that we have not efficiently utilized our resources. Fairness is all tenants’ ability to get the resources with equally likely probability in a certain cost class.
- **Release resources**: After successfully executing a task, the system should take back the resources assigned to the task.
- **Show task status**: The system should show the users the current status of the task.
