# OS Laboratory Activity 3
## Multilevel Queue and Real-Time CPU Scheduling

### Student Information
- Name: Karle Kevin P. Taer
- Course and Section: BSCA 3rd Year Block 186
- Instructor: Maria Fe Bahinting
- Date Submitted: 11/14/2026

### I. Introduction
This laboratory activity implements and simulates Multilevel Queue (MLQ), Rate Monotonic Scheduling (RMS), and Earliest Deadline First (EDF) CPU scheduling in Python. MLQ organizes processes into queues with different scheduling policies, while RMS and EDF schedule periodic real-time tasks by period and deadline. The Gantt charts, CPU utilization, waiting time, turnaround time, and deadline results were analyzed, and GitHub was used to store and document the work.

### II. Objectives
1. Explain the operation of Multilevel Queue, RMS, and EDF scheduling algorithms.
2. Implement MLQ scheduling using Round Robin and FCFS.
3. Implement preemptive RMS and EDF scheduling simulations using Python.
4. Generate and interpret Gantt charts showing process and task execution.
5. Calculate CPU utilization, waiting time, turnaround time, and deadline compliance.
6. Create and manage a GitHub repository containing Python source code and laboratory documentation.

### III. Scheduling Algorithms
1. **Multilevel Queue Scheduling** - Processes are placed in separate queues by category. Queue 1 (interactive) uses Round Robin with a 2 ms quantum and has higher priority. Queue 2 (batch) uses FCFS and has lower priority. Queue 1 has strict priority over Queue 2.
2. **Rate Monotonic Scheduling** - A fixed-priority preemptive algorithm for periodic tasks. The shorter the period, the higher the priority.
3. **Earliest Deadline First** - A dynamic-priority preemptive algorithm. The ready task with the earliest absolute deadline runs first (absolute deadline = release time + relative deadline).

### IV. Program Implementation
- **mlq.py** - Uses two queues (`deque`). The simulation advances in 1 ms steps. At each step, newly arrived processes join their queue. If Queue 1 has a process, it runs with a 2 ms quantum (Round Robin). Only when Queue 1 is empty does Queue 2 run in FCFS order, so a new Queue 1 arrival interrupts Queue 2. The program prints the Gantt chart, completion time (CT), turnaround time (TAT = CT - AT), waiting time (WT = TAT - BT), and the averages.
- **rms.py** - Each periodic task is expanded into separate jobs with a release time, absolute deadline, and remaining execution time. At every 1 ms step, among the ready jobs the program runs the one whose task has the shortest period (fixed priority). It prints the priority order, CPU utilization, the RMS utilization bound test, the Gantt chart, and MET/MISSED/PENDING status for each job.
- **edf.py** - Uses the same job generation, but at every 1 ms step it runs the ready job with the earliest absolute deadline (ties are broken by earlier release time, then task ID). It prints CPU utilization, the EDF utilization test, the Gantt chart, and the status of each job.

All three programs use a 1 ms time step and ignore context-switching overhead.

### V. Test Data
**MLQ (time quantum for Queue 1 = 2 ms)**

| Process | Arrival Time (ms) | Burst Time (ms) | Queue |
|---|---|---|---|
| P1 | 0 | 5 | 1 |
| P2 | 1 | 4 | 1 |
| P3 | 0 | 6 | 2 |
| P4 | 2 | 3 | 2 |
| P5 | 3 | 2 | 1 |

**RMS and EDF (simulation time = 20 ms; all tasks released at 0 ms)**

| Task | Execution Time (C) | Period (T) | Relative Deadline (D) |
|---|---|---|---|
| T1 | 1 ms | 4 ms | 4 ms |
| T2 | 1 ms | 5 ms | 5 ms |
| T3 | 2 ms | 10 ms | 10 ms |

### VI. Results and Discussion
**A. Multilevel Queue**

Gantt chart:

```text
| P1 [0-2] | P2 [2-4] | P1 [4-6] | P5 [6-8] | P2 [8-10] | P1 [10-11] | P3 [11-17] | P4 [17-20] |
```

| Process | AT | BT | CT | TAT | WT |
|---|---|---|---|---|---|
| P1 | 0 | 5 | 11 | 11 | 6 |
| P2 | 1 | 4 | 10 | 9 | 5 |
| P3 | 0 | 6 | 17 | 17 | 11 |
| P4 | 2 | 3 | 20 | 18 | 15 |
| P5 | 3 | 2 | 8 | 5 | 3 |

Average turnaround time = 12.0 ms. Average waiting time = 8.0 ms. All Queue 1 processes (P1, P2, P5) finished before any Queue 2 process (P3, P4) got the CPU. This shows that Queue 1 has strict priority over Queue 2. P3 arrived at 0 ms but waited until 11 ms.

**B. Rate Monotonic Scheduling**

Priority order: T1 > T2 > T3.

CPU utilization: U = 1/4 + 1/5 + 2/10 = 0.65 = 65%. The RMS sufficient bound for 3 tasks is 3(2^(1/3) - 1) = 77.98%. Since 65% is below the bound, the task set passes the test.

Gantt chart:

```text
| T1 [0-1] | T2 [1-2] | T3 [2-4] | T1 [4-5] | T2 [5-6] | IDLE [6-8] | T1 [8-9] | IDLE [9-10] | T2 [10-11] | T3 [11-12] | T1 [12-13] | T3 [13-14] | IDLE [14-15] | T2 [15-16] | T1 [16-17] | IDLE [17-20] |
```

All 11 task instances finished on or before their deadlines (Deadline Misses: 0). The CPU was busy for 13 ms out of 20 ms (65%). At 12 ms, T1 was released and preempted T3, which resumed at 13 ms.

**C. Earliest Deadline First**

CPU utilization: 65%. The classical EDF test passes because utilization does not exceed 100%.

Gantt chart:

```text
| T1 [0-1] | T2 [1-2] | T3 [2-4] | T1 [4-5] | T2 [5-6] | IDLE [6-8] | T1 [8-9] | IDLE [9-10] | T2 [10-11] | T3 [11-12] | T1 [12-13] | T3 [13-14] | IDLE [14-15] | T2 [15-16] | T1 [16-17] | IDLE [17-20] |
```

All 11 task instances met their absolute deadlines (Deadline Misses: 0). For this task set, EDF produced the same schedule as RMS, even though EDF chooses by earliest absolute deadline instead of by period.

Screenshots of the three program runs are in the `screenshots` folder, and the saved outputs are in the `outputs` folder.

### VII. Algorithm Comparison

| Criteria | MLQ | RMS | EDF |
|---|---|---|---|
| Scheduling basis | Queue priority | Task period | Absolute deadline |
| Priority assignment | Fixed queue priority | Fixed task priority | Dynamic task priority |
| Scheduling type | Depends on queue policy | Preemptive | Preemptive |
| Main application | Process classification | Periodic real-time tasks | Deadline-based real-time tasks |
| Deadline monitoring | Not inherent | Yes | Yes |

MLQ is a general-purpose way of separating process types and does not guarantee deadlines. RMS is simple and predictable but is only guaranteed by the utilization bound up to about 78% for three tasks. EDF can use the CPU up to 100% under the classical assumptions, but its priorities change at runtime.

### VIII. Conclusion
Through this activity I learned how Multilevel Queue, Rate Monotonic, and Earliest Deadline First scheduling work and how they differ. I wrote Python simulations, read Gantt charts, computed waiting time, turnaround time, CPU utilization, and deadline compliance, and used GitHub to organize, commit, and document my work.

### IX. Guide Questions and Answers

**1. What is the main difference between Multilevel Queue and Multilevel Feedback Queue scheduling?**
In Multilevel Queue, a process is assigned to one queue permanently (for example, by process type) and never moves. In Multilevel Feedback Queue, processes can move between queues based on their behavior, for example a process that uses too much CPU time is moved to a lower-priority queue, and a process that waits too long can be moved up.

**2. Why does MLQ use different scheduling algorithms for different process queues?**
Different kinds of processes have different needs. Interactive processes need fast response, so they use Round Robin with a small time quantum. Batch processes need to finish their work with little switching overhead, so FCFS suits them. Using one algorithm for both would favor one type and hurt the other.

**3. How does RMS determine which task receives the CPU first?**
RMS gives the CPU to the ready task with the shortest period, because the shortest period has the highest priority. In this lab, T1 (4 ms) goes first, then T2 (5 ms), then T3 (10 ms).

**4. Why is RMS considered a fixed-priority scheduling algorithm?**
The priority is based on the task's period, and the period never changes. So each task keeps the same priority during the whole run. T1 is always higher than T2 and T3.

**5. How does EDF determine which task should execute next?**
EDF looks at all ready tasks and runs the one with the earliest absolute deadline (release time + relative deadline). If two tasks tie, the program uses the earlier release time and then the task ID.

**6. Why is EDF considered a dynamic-priority scheduling algorithm?**
Because the priority is not fixed to a task. It is recalculated from the absolute deadlines of the jobs that are ready at that moment. Each new release of a task gets a new absolute deadline, so which task is highest priority changes over time.

**7. What happens when a newly released task has an earlier deadline than the currently running task under EDF?**
The new task preempts the running task and takes the CPU. The interrupted task goes back to the ready state with its remaining execution time and continues later. Example from the output: at 12 ms, T1 (deadline 16) is released and preempts T3 (deadline 20). T3 resumes at 13 ms.

**8. Why is CPU utilization important in real-time scheduling?**
It shows how much of the CPU the task set needs (the sum of C/T). It is used to check whether the tasks can be scheduled before running them. If utilization is above 100%, the tasks cannot all meet their deadlines. In this lab, the utilization is 65%, which leaves spare capacity.

**9. Can a task set with CPU utilization below 100% still miss a deadline? Explain the conditions under which this may happen.**
Yes. Under RMS, a task set whose utilization is above the sufficient bound (about 77.98% for three tasks) may miss a deadline, although it is not guaranteed to. Under EDF, utilization of 100% or less only guarantees success under the classical assumptions: one processor, independent and preemptible tasks, deadlines equal to periods, and no overhead. If these assumptions are broken, for example by context-switching overhead, tasks that block each other, deadlines shorter than periods, or non-preemptible sections, a deadline can be missed even below 100%.

**10. Which algorithm would you recommend for periodic real-time tasks, RMS or EDF? Justify your answer.**
I would recommend EDF. It can use the CPU up to 100% under the classical assumptions, while RMS is only guaranteed up to about 77.98% for three tasks, so EDF gives more room for additional tasks. In this lab both met all deadlines with the same schedule, but EDF has more headroom. RMS is still a good choice when simplicity and predictable fixed priorities matter, because it is easier to implement and behaves more predictably when the system is overloaded.
