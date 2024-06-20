# CPU Scheduling Algorithms

This repository contains implementations of various CPU scheduling algorithms. Each algorithm has been implemented in C++ and the performance metrics such as average waiting time and average turn-around time are calculated and displayed. This document explains each scheduling algorithm in detail and outlines how the predicted algorithm is determined based on the input processes.

## Table of Contents
1. [Algorithms Implemented](#algorithms-implemented)
2. [Details of Each Algorithm](#details-of-each-algorithm)
   - [First-Come, First-Served (FCFS)](#first-come-first-served-fcfs)
   - [Shortest Job First (SJF)](#shortest-job-first-sjf)
   - [Longest Job First (LJF)](#longest-job-first-ljf)
   - [Shortest Remaining Time First (SRTF)](#shortest-remaining-time-first-srtf)
   - [Round Robin (RR)](#round-robin-rr)
3. [Predicted Algorithm](#predicted-algorithm)
4. [Compilation and Execution](#compilation-and-execution)
5. [Input and Output Files](#input-and-output-files)

## Algorithms Implemented

The following CPU scheduling algorithms have been implemented:
1. First-Come, First-Served (FCFS)
2. Shortest Job First (SJF)
3. Longest Job First (LJF)
4. Shortest Remaining Time First (SRTF)
5. Round Robin (RR)

## Details of Each Algorithm

### First-Come, First-Served (FCFS)

The FCFS algorithm schedules processes in the order they arrive in the ready queue. It is a non-preemptive scheduling algorithm.

- **Process Selection**: The process with the earliest arrival time is selected.
- **Execution**: Each process runs to completion before the next process starts.
- **Output**: The Gantt chart and average waiting and turn-around times are calculated.
- **Explanation of Code**: Here, the code is implemented using a vector containing all the values defining a process and we sort the vector in increasing order of arrival times. Then, each and every processes comes one after the another and gets executed.
  ```
  vector<vector<float>> FCFS(vector<pair<int, pair<float, float>>> processes, ofstream& outputFile) {
      vector<pair<int, pair<float, float>>> original(processes.begin(), processes.end());
      sort(processes.begin(), processes.end(), IncArrTime);
      vector<vector<float>> run;
      float RunTime = 0;

      for (auto& p : processes) {
          float pID = p.first;
          float arrTime = p.second.first;
          float burstTime = p.second.second;

          if (RunTime < arrTime) {
              RunTime = arrTime;
          }

          float waitTime = max(0.0f, RunTime - arrTime);
          float TATime = RunTime + burstTime;

          run.push_back({pID, RunTime, original[pID - 1].second.second, original[pID - 1].second.first, TATime});
          RunTime = TATime;
      }

      outputFile << "FCFS :\n";
      GanttChart(run, outputFile);
      return run;
  }


### Shortest Job First (SJF)

The SJF algorithm selects the process with the smallest burst time from the ready queue. It is a non-preemptive scheduling algorithm.

- **Process Selection**: The process with the shortest burst time is selected next.
- **Execution**: The selected process runs to completion.
- **Output**: The Gantt chart and average waiting and turn-around times are calculated.
- **Code Explanation**: Here, a multiset is being used so that the sorting of processes, according to their burst times, comes handy and since this algorithm is non-preemptive, we are checking for new values at the completion of the process only and not in between.
```
vector< vector<float>> SJF(vector< pair<int, pair<float, float>>> processes, ofstream& outputFile){
    vector< pair<int, pair<float, float>>> original(processes.begin(), processes.end());
    sort(processes.begin(), processes.end(), IncArrTime);
    
    multiset <pair<float, pair<float, int>>> ReadyQueue;
    vector< vector<float>> run;

    int idx = 0;
    float RunTime = 0;
    int totalProcesses = processes.size();
    
    while(idx < totalProcesses || !ReadyQueue.empty()){

        while(idx < totalProcesses && processes[idx].second.first <= RunTime){
            ReadyQueue.insert({processes[idx].second.second, {processes[idx].second.first, processes[idx].first}});
            idx++;
        }

        if(ReadyQueue.empty()){
            RunTime = processes[idx].second.first;
            continue;
        }

        pair<float, pair<float, int>> RunningProcess = *ReadyQueue.begin();
        ReadyQueue.erase(ReadyQueue.begin());

        float pID = RunningProcess.second.second;
        float arrTime = RunningProcess.second.first;
        float burstTime = RunningProcess.first;

        float waitTime = max(0.0f, RunTime - arrTime);
        float TATime = RunTime + burstTime;

        run.push_back({pID, RunTime, original[pID-1].second.second, original[pID-1].second.first, TATime});
        RunTime += burstTime;
    }
    outputFile << "SJF :\n";
    GanttChart(run, outputFile);
    return run;
}
```

### Longest Job First (LJF)

The LJF algorithm selects the process with the longest burst time from the ready queue. It is a non-preemptive scheduling algorithm.

- **Process Selection**: The process with the longest burst time is selected next.
- **Execution**: The selected process runs to completion.
- **Output**: The Gantt chart and average waiting and turn-around times are calculated.
- **Code Explanation**: This implementation is also very similar to that of SJF. The only difference is that, a `struct` is introduced to deal with the decreasing order of burst times.
```
struct DecSet {
    bool operator()(const pair<float, pair<float, int>>& a, const pair<float, pair<float, int>>& b) const {
        return a.first > b.first;
    }
};

vector< vector<float>> LJF(vector< pair<int, pair<float, float>>> processes, ofstream& outputFile) {
    vector< pair<int, pair<float, float>>> original(processes.begin(), processes.end());
    sort(processes.begin(), processes.end(), IncArrTime);

    multiset< pair<float, pair<float, int>>, DecSet> ReadyQueue;
    vector< vector<float>> run;

    int idx = 0;
    float RunTime = 0;
    int totalProcesses = processes.size();

    while (idx < totalProcesses || !ReadyQueue.empty()) {
        while (idx < totalProcesses && processes[idx].second.first <= RunTime) {
            ReadyQueue.insert({processes[idx].second.second, {processes[idx].second.first, processes[idx].first}});
            idx++;
        }

        if (ReadyQueue.empty()) {
            if (idx < totalProcesses) {
                RunTime = processes[idx].second.first;
            }
            continue;
        }

        auto RunningProcess = *ReadyQueue.begin();
        ReadyQueue.erase(ReadyQueue.begin());

        float pID = RunningProcess.second.second;
        float arrTime = RunningProcess.second.first;
        float burstTime = RunningProcess.first;

        float waitTime = max(0.0f, RunTime - arrTime);
        float TATime = RunTime + burstTime;

        run.push_back({pID, RunTime, original[pID - 1].second.second, original[pID - 1].second.first, TATime});
        RunTime += burstTime;
    }

    outputFile << "LJF :\n";
    GanttChart(run, outputFile);
    return run;
}
```

### Shortest Remaining Time First (SRTF)

The SRTF algorithm is a preemptive version of SJF. It selects the process with the shortest remaining burst time.

- **Process Selection**: The process with the shortest remaining burst time is selected.
- **Execution**: A running process can be preempted if a new process with a shorter burst time arrives.
- **Output**: The Gantt chart and average waiting and turn-around times are calculated.
- **Code Explanation**: As it is already stated that this algorithm is just preemptive version of SJF. So, here at the end of every `second`, the code checks for new processes (if any) and updates the multiset in order to make the task with least burst time available to CPU.
```
vector< vector<float>> SRTF(vector< pair<int, pair<float, float>>> processes, ofstream& outputFile){
    vector< pair<int, pair<float, float>>> original(processes.begin(), processes.end());
    sort(processes.begin(), processes.end(), IncArrTime);
    
    multiset < pair<float, pair<float, int>>> ReadyQueue;
    vector< vector<float>> run, exec;

    int idx = 0, n = processes.size();
    float RunTime = 0;
    
    ReadyQueue.insert({processes[idx].second.second, {processes[idx].second.first, processes[idx].first}});
    RunTime = max(RunTime, processes[idx].second.first);
    idx++;

    while(!ReadyQueue.empty() || idx < n){
        while(idx < n && processes[idx].second.first <= RunTime){
            ReadyQueue.insert({processes[idx].second.second, {processes[idx].second.first, processes[idx].first}});
            idx++;
        }

        if (ReadyQueue.empty()) {
            RunTime = processes[idx].second.first;
            ReadyQueue.insert({processes[idx].second.second, {processes[idx].second.first, processes[idx].first}});
            idx++;
        }

        pair<float, pair<float, int>> RunningProcess = *ReadyQueue.begin();
        ReadyQueue.erase(ReadyQueue.begin());

        float pID = RunningProcess.second.second;
        float arrTime = RunningProcess.second.first;
        float burstTime = RunningProcess.first;
        float remTime = burstTime - 1;

        float waitTime = max(0.0f, RunTime - arrTime);
        float TATime = RunTime + 1;
        
        exec.push_back({pID, RunTime, 1.0f, waitTime, RunTime+1});

        RunTime++;

        while(idx < n && processes[idx].second.first <= RunTime){
            ReadyQueue.insert({processes[idx].second.second, {processes[idx].second.first, processes[idx].first}});
            idx++;
        }

        if(remTime == 0){
            run.push_back({pID, arrTime, original[pID-1].second.second, original[pID-1].second.first, TATime});
        } else {
            ReadyQueue.insert({remTime, {arrTime, pID}});
        }
    }
    outputFile << "SRTF :\n";
    GanttChart(exec, outputFile);
    return run;
}
```
### Round Robin (RR)

The RR algorithm assigns a fixed time quantum for each process and cycles through the ready queue.

- **Process Selection**: Processes are selected in a round-robin manner.
- **Execution**: Each process runs for a maximum of the time quantum before the next process is selected.
- **Output**: The Gantt chart and average waiting and turn-around times are calculated.
- **Code Explanation**: The code of round robin algorithm has a quantum value of `2s` and it checks for new processes coming before or at the time of preemption (or complemetion), whichever is applicable, then moves the current process `(if burst time remains > 0)` to the end of the ready queue.
```
vector< vector<float>> RoundRobin(vector< pair<int, pair<float, float>>> processes, ofstream& outputFile){
    vector< pair<int, pair<float, float>>> original(processes.begin(), processes.end());
    sort(processes.begin(), processes.end(), IncArrTime);
    
    queue < pair<float, pair<float, int>>> ReadyQueue;
    vector< vector<float>> run, exec;

    int idx = 0, n = processes.size();
    float RunTime = 0, quantum = 2;
    
    ReadyQueue.push({processes[idx].second.second, {processes[idx].second.first, processes[idx].first}});
    RunTime = max(RunTime, processes[idx].second.first);
    idx++;

    while(!ReadyQueue.empty() || idx < n){
        while(idx < n && processes[idx].second.first <= RunTime){
            ReadyQueue.push({processes[idx].second.second, {processes[idx].second.first, processes[idx].first}});
            idx++;
        }
        
        if (ReadyQueue.empty()) {
            RunTime = processes[idx].second.first;
            ReadyQueue.push({processes[idx].second.second, {processes[idx].second.first, processes[idx].first}});
            idx++;
        }
        
        pair<float, pair<float, int>> RunningProcess = ReadyQueue.front();
        ReadyQueue.pop();

        float pID = RunningProcess.second.second;
        float arrTime = RunningProcess.second.first;
        float burstTime = RunningProcess.first;

        float usedTime = min(quantum, burstTime);
        float remTime = burstTime - usedTime;

        float waitTime = max(0.0f, RunTime - arrTime);
        float TATime = RunTime + usedTime;
        
        exec.push_back({pID, RunTime, usedTime, waitTime, RunTime+usedTime});

        RunTime += usedTime;

        while(idx < n && processes[idx].second.first <= RunTime){
            ReadyQueue.push({processes[idx].second.second, {processes[idx].second.first, processes[idx].first}});
            idx++;
        }

        if(remTime == 0){
            run.push_back({pID, arrTime, original[pID-1].second.second, original[pID-1].second.first, TATime});
        } else {
            ReadyQueue.push({remTime, {arrTime, pID}});
        }
    }
    outputFile << "RoundRobin (quantum = 2) :\n";
    GanttChart(exec, outputFile);
    return run;
}
```
## Predicted Algorithm

The predicted scheduling algorithm is determined based on the standard deviation and average of the burst times and arrival times of the processes:

- If both the standard deviation of burst times (SDBT) and arrival times (SDAT) are less than their respective averages, the SJF algorithm is predicted.
- If SDBT is less than the average burst time, the SRTF algorithm is predicted.
- If more than half of the processes have burst times greater than the average burst time, the LJF algorithm is predicted.
- Otherwise, the Round Robin algorithm is predicted.

## Compilation and Execution

To compile and execute the program, follow these steps:

1. **Compilation**:
   ```bash
   g++ -o cpu_scheduling cpu_scheduling.cpp
   ```

2. **Execution**:
   ```bash
   ./cpu_scheduling
   ```

Make sure the input file `input.txt` is in the same directory as the executable.

## Input and Output Files

- **Input File (`input.txt`)**:
  - The input file should contain process information in the following format:
    ```
    ProcessID ArrivalTime BurstTime
    ```
  - Example:
    ```
    1 0 5
    2 2 3
    3 4 2
    ```

- **Output File (`output.txt`)**:
  - The output file will contain the results of the scheduling algorithms including Gantt charts and average waiting and turn-around times.

By following the instructions and understanding the provided details, one can effectively utilize and analyze various CPU scheduling algorithms. This repository serves as a comprehensive guide and implementation for these algorithms.
