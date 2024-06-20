# Process Scheduling Algorithms

This project demonstrates the implementation of various CPU scheduling algorithms, which are used to manage process execution in a system. The algorithms include First-Come, First-Served (FCFS), Shortest Job First (SJF), Longest Job First (LJF), Shortest Remaining Time First (SRTF), and Round Robin (RR). The program reads process data from an input file and outputs the scheduling results, including Gantt charts and average waiting/turnaround times, to an output file. It also predicts the most suitable scheduling algorithm based on process characteristics.

## Table of Contents
1. [Introduction](#introduction)
2. [Algorithms](#algorithms)
    - [First-Come, First-Served (FCFS)](#first-come-first-served-fcfs)
    - [Shortest Job First (SJF)](#shortest-job-first-sjf)
    - [Longest Job First (LJF)](#longest-job-first-ljf)
    - [Shortest Remaining Time First (SRTF)](#shortest-remaining-time-first-srtf)
    - [Round Robin (RR)](#round-robin-rr)
3. [Execution](#execution)
4. [Prediction Mechanism](#prediction-mechanism)
5. [Output](#output)
6. [File Structure](#file-structure)
7. [References](#references)

## Introduction
CPU scheduling is essential in multiprogramming operating systems. This project simulates different CPU scheduling algorithms to understand their behavior and effectiveness in managing process execution.

The implemented algorithms include:
- FCFS
- SJF
- LJF
- SRTF
- Round Robin

## Algorithms

### First-Come, First-Served (FCFS)
FCFS scheduling executes processes in the order they arrive. If two processes arrive simultaneously, the one listed first gets executed first. This algorithm is simple and non-preemptive, meaning a process runs to completion once it starts.

**Implementation:**
1. Sort processes by arrival time.
2. Execute each process in order of arrival.
3. Calculate waiting time and turnaround time for each process.

### Shortest Job First (SJF)
SJF selects the process with the shortest burst time for execution next. If two processes have the same burst time, the one arriving first gets executed first. This algorithm aims to minimize average waiting time.

**Implementation:**
1. Sort processes by arrival time.
2. Use a ready queue to select the process with the shortest burst time.
3. Update the ready queue as new processes arrive during execution.

### Longest Job First (LJF)
LJF selects the process with the longest burst time for execution next. This algorithm is less common as it can cause long waiting times for shorter processes.

**Implementation:**
1. Sort processes by arrival time.
2. Use a ready queue to select the process with the longest burst time.
3. Update the ready queue as new processes arrive during execution.

### Shortest Remaining Time First (SRTF)
SRTF is a preemptive version of SJF, selecting the process with the shortest remaining time for execution. If a new process arrives with a shorter burst time than the current process's remaining time, the current process is preempted.

**Implementation:**
1. Sort processes by arrival time.
2. Use a ready queue to select the process with the shortest remaining time.
3. Manage preemptions and update the ready queue as new processes arrive.

### Round Robin (RR)
RR assigns a fixed time quantum to each process in the ready queue. Each process executes for a maximum of one time quantum at a time, ensuring fair CPU time distribution among processes.

**Implementation:**
1. Sort processes by arrival time.
2. Use a queue to manage processes, executing each for a time quantum.
3. Reinsert processes with remaining burst time back into the queue.

## Execution
The program reads process data from an input file (`input.txt`) and writes results to an output file (`output.txt`). The input file contains process information in the format:

```
ProcessID ArrivalTime BurstTime
```

The main steps include:
1. Reading process data from `input.txt`.
2. Predicting the most suitable scheduling algorithm based on process characteristics.
3. Executing each scheduling algorithm.
4. Outputting Gantt charts and average waiting/turnaround times to `output.txt`.

## Prediction Mechanism
The program predicts the most suitable scheduling algorithm based on statistical analysis of process burst times and arrival times:

1. Calculate the average and standard deviation of burst times and arrival times.
2. Use these metrics to determine the variability of process characteristics.
3. Predict the algorithm:
   - If both standard deviations are low, SJF is predicted.
   - If burst time variability is low, SRTF is predicted.
   - If most processes have burst times longer than the average, LJF is predicted.
   - Otherwise, Round Robin is predicted.

## Output
The output file (`output.txt`) contains:
1. The predicted scheduling algorithm.
2. Gantt charts for each algorithm.
3. Average waiting time and turnaround time for each algorithm.

## File Structure
- `input.txt`: Contains the process data.
- `output.txt`: Contains the scheduling results and Gantt charts.

## References
For further reading on CPU scheduling algorithms and their implementations, consider the following resources:
- Operating System Concepts by Silberschatz, Galvin, and Gagne.
- Modern Operating Systems by Andrew S. Tanenbaum.

---

By following this structured approach, you can effectively manage and analyze different CPU scheduling algorithms to understand their behavior and performance in various scenarios.
