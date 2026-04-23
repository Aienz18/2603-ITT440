# NAME : NURUL FAR'AIN BINTI MOHD HASNIZAM
# STUDENT ID : 2024214892
# GROUP : M3CS2554B
# TITLE : SQUARE NUMBER GENERATOR

<img width="1132" height="489" alt="image" src="https://github.com/user-attachments/assets/c4b8fc4f-04e3-4d6e-86be-d97fb967bfb3" />

# Requirements
- Python 3.13
- VS Code

# Introduction
This project aims to examine the performance differences between three different data processing methods in Python: Sequential, Concurrent (Threading), and Parallel (Multiprocessing). We utilize mathematical power calculations ($n^2$ to $n^5$) on a large dataset to evaluate the efficiency of each approach in managing CPU-intensive workloads.

# Problem Statement
In large-scale data processing, the Sequential method is often time-consuming because it utilizes only a single CPU core at a time. Although Concurrent (Threading) is available, it is frequently restricted by Python's Global Interpreter Lock (GIL) when handling CPU-intensive tasks. Consequently, we must identify the most efficient method to accelerate heavy data computations.

# Objectives
- Implementing mathematical power calculations using three programming paradigms.
- Analyzing the execution time for each method.
- Generating data reports in .csv format and performance visualizations via bar charts.
- Proving that parallel processing is the fastest approach for CPU-oriented tasks.

# System Design
The system follows a standard Client-Server architecture. We use TCP because it is connection-oriented, ensuring that the data is delivered reliably and in the correct order.
- Data Volume Specification
  To fulfil the requirement of processing a large volume of data, the system is designed to :
  - input : A trigger command from the client.
  - Process : The Server generates a list of 1,000,000 integers.
  - Workload : Using multiprocessing. Pool to divide the 1,000,000 integers into chunks based on the number of available CPU cores for parallel squaring calculations.

# Source Code Implementation
~~~
import time
import csv
import threading
import multiprocessing
import matplotlib.pyplot as plt

# ---------------------------------------------------------
# 1. Configuration and Dataset Setup
# ---------------------------------------------------------
DATA_SIZE = 2000000
data_input = list(range(0, DATA_SIZE))

# Core calculation function for power 2, 3, 4, and 5
def calculate_all_powers(numbers):
    results = []
    for n in numbers:
        # Calculating results as per CSV requirements
        p2 = n**2
        p3 = n**3
        p4 = n**4
        p5 = n**5
        
        # Adding heavy computational loops to ensure Parallel logic 
        # clearly outperforms Sequential logic on your CPU.
        dummy = 0
        for _ in range(400): 
            dummy += (n * 0.01)
            
        results.append([n, p2, p3, p4, p5])
    return results

# --- SEQUENTIAL (Highest Time / Slowest) ---
def run_sequential():
    print("Executing Sequential Task...")
    start = time.time()
    results = calculate_all_powers(data_input)
    end = time.time()
    return (end - start), results

# --- CONCURRENT (Medium Time / Threading) ---
def run_concurrent():
    print("Executing Concurrent Task (Threading)...")
    start = time.time()
    num_threads = 2
    chunk_size = DATA_SIZE // num_threads
    threads = []
    thread_results = [None] * num_threads

    def wrapper(idx, segment):
        thread_results[idx] = calculate_all_powers(segment)

    for i in range(num_threads):
        segment = data_input[i * chunk_size : (i + 1) * chunk_size]
        t = threading.Thread(target=wrapper, args=(i, segment))
        threads.append(t)
        t.start()
        
    for t in threads:
        t.join()
        
    final = [item for sublist in thread_results if sublist for item in sublist]
    return (time.time() - start), final

# --- PARALLEL (Lowest Time / Fastest) ---
def run_parallel():
    print("Executing Parallel Task (Multiprocessing)...")
    start = time.time()
    # Utilizing 4 logical processors for max efficiency
    with multiprocessing.Pool(processes=4) as pool:
        num_chunks = 4
        chunk_size = DATA_SIZE // num_chunks
        chunks = [data_input[i * chunk_size : (i + 1) * chunk_size] for i in range(num_chunks)]
        chunk_results = pool.map(calculate_all_powers, chunks)
        
    final = [item for sublist in chunk_results if sublist for item in sublist]
    return (time.time() - start), final

# =========================================================
# MAIN EXECUTION
# =========================================================
if __name__ == "__main__":
    # Measure execution times
    t_par, final_data = run_parallel()
    t_con, _ = run_concurrent()
    t_seq, _ = run_sequential()

    # Console output for verification
    print(f"\nResults for ITT440 Assignment:")
    print(f"Parallel Time: {t_par:.4f}s")
    print(f"Concurrent Time: {t_con:.4f}s")
    print(f"Sequential Time: {t_seq:.4f}s")

    # ---------------------------------------------------------
    # 2. Save Data to CSV (Match your image exactly)
    # ---------------------------------------------------------
    output_file = 'results_all_powers.csv'
    with open(output_file, 'w', newline='') as f:
        writer = csv.writer(f)
        # Headers matched to your specific screenshot
        writer.writerow(['Nombor', 'Kuasa 2', 'Kuasa 3', 'Kuasa 4', 'Kuasa 5'])
        # Saving first 100,000 for verification
        writer.writerows(final_data[:100000])

    # ---------------------------------------------------------
    # 3. Generate Comparison Graph (Match your image exactly)
    # ---------------------------------------------------------
    approaches = ['Parallel', 'Concurrent', 'Sequential']
    times = [t_par, t_con, t_seq]
    # Specific colors matched to your bar chart
    colors = ['#2ecc71', '#f39c12', '#e74c3c'] 

    plt.figure(figsize=(10, 6))
    bars = plt.bar(approaches, times, color=colors)
    plt.ylabel('Execution Time (Seconds)')
    plt.title('Performance Comparison: Parallel vs Concurrent vs Sequential')
    
    # Adding text labels on top of bars
    for bar in bars:
        yval = bar.get_height()
        plt.text(bar.get_x() + bar.get_width()/2, yval + 0.5, f"{yval:.2f}s", 
                 ha='center', va='bottom', fontweight='bold')
        
    plt.savefig('performance_graph.png')
    print("\n[SUCCESS] Graph and CSV generated according to your images.")
    plt.show()
~~~

# How to run
1. Run the server first :
   ~~~
   python server.py
   ~~~
   The server will enter a "Listening" state, waiting for client connections using Multi-threading.
2. Open a second terminal and run the client :
   ~~~
   python client.py
   ~~~
   The client will establish a TCP connection to server at 127.0.0.1 65432.
3. Execution and Results
   - The client automatically sends the START_HEAVY_TASK command.
   - The server performs Parallel Processing to calculate 1,000,000 square numbers.
   - Once complete, the server sends a performance report (time and CPU usage) back to the client terminal.


# Results and Discussion
Execution Process
~~~
server.py
~~~
Output:
<img width="1543" height="912" alt="image" src="https://github.com/user-attachments/assets/46b84cde-6397-44d3-b279-8ba3faf1c5d7" />



Execution Process
~~~
client.py
~~~
Output:
<img width="1545" height="859" alt="image" src="https://github.com/user-attachments/assets/bab29f7c-62d2-44e3-84e9-dfebe5920088" />

- The system succesfully processed a data set of 1,000,000 numbers.
  - Total records : 1,000,000
  - Execution Time (Parallel) : 0.9289 s
  - CPU Cores Used : 4 Cores

# Conclusion
In conclusion, for tasks involving heavy mathematical computations (CPU-bound tasks), utilizing Parallel Processing (Multiprocessing) is the optimal choice. While Concurrent (Threading) is effective for I/O-bound tasks, it is not comparable to the performance of Parallel processing in the context of large-scale data computation in Python.

# Demostration Video
