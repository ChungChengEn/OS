# Design Doc - Disk Scheduling

### B113040003 鍾承恩 B113040004 蔡英助

### Table of Contents

1. [Preprocess and Global](#preprocess-and-global)

2. [int main()](#main)

3. [void fcfs()](#fcfs)
   * behavior description
   * objective/goal
   * strengths/weaknesses
   
4. [void sstf()](#sstf)
   * behavior description
   * objective/goal
   * strengths/weaknesses
   
5. [void scan()](#scan)
   * behavior description
   * objective/goal
   * strengths/weaknesses
   
6. [void cscan()](#cscan)
   * behavior description
   * objective/goal
   * strengths/weaknesses
   
7. [void look()](#look)
   * behavior description
   * objective/goal
   * strengths/weaknesses
   
8. [void clook()](#clook)
   * behavior description
   * objective/goal
   * strengths/weaknesses
   
9. [void optimal()](#optimal)
   * behavior description
   * objective/goal
   * strengths/weaknesses
   
10. [int cmp(const void\*, const void\*)](#cmp)

11. [Average Latency & Total Head Movement](#average-latency-&-total-head-movement)

    * average latency
    * total head movement

12. [Best Condition to Use Each Algorithm](#best-condition-to-use-each-algorithm)

    * fcfs
    * sstf
    * scan
    * cscan
    * look
    * clook

    

#### Preprocess and Global

---

> 1. 函括檔包含：
>    `stdio.h` , `stdlib.h` , `cstring` , `string` , `math.h` , `iomanip` , `iostream` 
>
> 2. global functions 包含：
>    `void fcfs()` , `void sstf()` , `void scan()` , `void cscan()` , `void look()` , `void clook()` , `void optimal()` ,
>     `int cmp(const void*, const void*)` 
>
> 3. global variables 包含：
>    `char* disk_n[7]` , `void (*disk_s[7])()` , 
>    `int movement, circular_movement` , `float time_spent` , `unsigned int request[1000]` 

<img src="img/design_doc_4/library_and_global_vars_functions.png" style="width:75%;" >

其中，`void (*disk_s[7])()` 用來指向各個函式的位址，舉例來說，如果執行：

```c++
disk_s[0]();
```

就等同於執行 `void fcfs()` 

另外，`char* disk_n[7]` 用來儲存每一個 disk scheduling function 的名字，之後要輸出結果時會用到

`int movement` 是用來記錄 head movement 

`int circular_movement` 是用來記錄 circular disk scheduling 中從端點返回另一端點的 movement

`float time_spent` 用來記錄 latency ( 1 ms / 100 cylinder) 

`unsigned int request[1000]` 用來記錄有需求的 cylinder

`int head` 用來記錄最初的 head 位置



#### main()

---

> `main()` 將`head` 初始值從 command line 取入，若無則為0
>
> 接著產生1000個亂數產生的數值作為 cylinder requests (保證倆倆皆不相同)，執行各演算法並輸出結果

<img src="img/design_doc_4/main.png" style="width:75%;" >



#### fcfs()

---

> `FCFS(First-Come-First-Serve)`
>
> 1. 最先到的 cylinder request 最先處理。
>
> 2. Fairly treating every cylinder requests, FIFO
>
> 3. strengths
>
>        a. Every request gets a fair chance
>        
>        b. No indefinite postponement
>
>    weaknesses
>
>        a. Does not try to optimize seek time
>        
>        b. May not provide the best possible service

<img src="img/design_doc_4/fcfs.png" style="width:75%;" >



#### sstf()

---

> `SSTF(Shortest-Seek-Time-First)`
>
> 1. `sstf()` 根據目前 `head` 所在位置，找到離此位置最近的 request (若有兩個 request距離 `head` 位置相等，選擇處理位置較大的)，並移動 `head` 位置到此處。之後此演算法不斷重複尋找距離`head`最小的request (排除掉已經完成的requests)這個過程
> 2. `sstf()` 演算法的目標是每次都去尋找距離當前最近的 cylinder，降低 seek time，以此來最佳化disk drive的整體表現
>
> 3. strengths
>
>    ```
>    a. It provides better throughput
>    
>    b. It has a less average response and waiting time
>    ```
>
>    weaknesses
>
>    ``` 
>    a. Starvation is possible for some requests as it favours easy-to-reach requests and ignores the far-away processes
>    
>    b. Switching direction slows things down
>    ```

<img src="img/design_doc_4/sstf.png" style="width:75%;" >



#### scan()

---

> `A.k.a. elevator algorithm`
>
> 1. head 先往 cylinder 為 4999 的方向移動，再往 cylinder 為 0 的方向移動；
>
>    我們因為先向 4999 的那方移動，故我們的head中途會到達 4999。
>
> 2. 去除不必要的`head` 來回移動，以此 minimize the seek time
>
> 3. strengths
>
>        a. High throughput
>        
>        b. Low variance of response time
>
>    weaknesses
>
>        a. Long waiting time for requests for locations just visited by disk arm

<img src="img/design_doc_4/scan.png" style="width:75%;" >



#### cscan()

---

> `C-SCAN(circular SCAN)`
>
> 1. head 先往 cylinder 為 4999 的方向移動，接著直接移到 0，再繼續往 cylinder 為 4999 的方向移動；
>
>    head 中途會到達 4999、0。
>
> 2. `head` 只往同一個方向移動直到，到端點之後直接跳到另一個端點，再繼續朝同一個方向移動。與SCAN一樣可以除掉`head` 不必要的來回跳動，另外有更好的避免飢餓的表現，minimize the wait time and also minimize the seek time
>
> 3. strengths
>
>        a. Provides more uniform wait time compared to SCAN
>        
>        b. Starvation is avoided
>
>    weaknesses
>
>        a. Long waiting time for requests for locations just visited by disk arm
>        
>        b. More seek movements are caused in C-SCAN compared to SCAN Algorithm

<img src="img/design_doc_4/cscan.png" style="width:75%;" >



#### look()

---

> 1. head 先往 cylinder 為 4999 的方向移動，再往 cylinder 為 4999 的方向移動；
>
>    需要注意不一樣的是 head 只會到達 cylinder request 的位置，不一定會到達 4999。
>
> 2. SCAN的一個變型 - instead of going till last track, it goes till last request and then direction is changed - 更進一步的minimize the seek time
>
> 3. strengths
>
>        a. Higher throughput
>        
>        b. Low variance response timeuniform waiting time and response time
>
>    weaknesses
>
>        a. Overhead of finding the end requests is present

<img src="img/design_doc_4/look.png" style="width:75%;" >



#### clook()

---

> `C-LOOK(circular LOOK)`
>
> 1. head 先往 cylinder 為 4999 的方向移動，接著直接移到最小的 cylinder request，再繼續往 cylinder 為 4999 的方向移動；
>
>    需要注意不一樣的是 head 只會到達 cylinder request 的位置，不一定會到達 4999。
>
> 2. LOOK 的變型，upon running till the last request, it goes directly to the first request and go by the same direction, which lower the waiting time and minimize the seek time in advance
>
> 3. strengths
>
>        a. Low variance is provided in waiting time and response time
>
>    weaknesses
>
>        a. Overhead of finding the end requests is present

<img src="img/design_doc_4/clook.png" style="width:75%;" >



#### optimal()

---

> 1. 先將所有 request 按照位置順序由小到大做排序，接著建立兩個整數變數 `l_ptr` 和 `r_ptr` 。`l_ptr` 指向距離`head`位置最近但小於`head`的 request，而`r_ptr`指向距離`head`最近但比`head`大或者相等的 request。每次選擇距離 `head` 最近的 request ，如果`l_ptr` 指向的 request 距離`head` 最近，則 `head` 移動到 `l_ptr` 的 request，`l_ptr = l_ptr-1` ；如果是`r_ptr` 指向的request 距離最近，則`head`移動到`r_ptr` 的request，`r_ptr = r_ptr+1` ；如果`l_ptr` 和 `r_ptr` 兩者的 request 與 `head` 等距離，則移動到 `r_ptr` 的 request (優先作右邊的) 。演算法重複尋找下一個最近的 request 直到所有 request 執行完畢
>
> 2. 透過先將 requests 做排序，再利用 shortest-seek-time-first 的演算法，達成最佳化 (minimize the seek time)
>
> 3. strengths
>
>        a. It provides better throughput
>        
>        b. It has a less average response and waiting time
>
>    weaknesses
>
>    ```
>    a. Starvation is possible for some requests as it favours easy-to-reach requests and ignores the far-away processes
>    
>    b. Switching direction slows things down
>    ```

<img src="img/design_doc_4/optimal.png" style="width:75%;" >





#### cmp()

---

> `int cmp(const void*, const void*)` 是函示庫 &lt;algorithm&gt; 內的 `qsort()` 會用到的函式，`qsort()` 的語法如下：
>
> `void qsort(void *__base, size_t __nel, size_t __width, int (*__compar)(const void *, const void *))` 

<img src="img/design_doc_4/cmp-7749039.png" style="width:75%;" >





#### Average Latency & Total Head Movement

---



| measure \\ algorithm | fcfs     | sstf  | scan  | cscan | look  | clook | optimal |
| -------------------- | -------- | ----- | ----- | ----- | ----- | ----- | ------- |
| average latency      | 16872.17 | 74.75 | 74.83 | 99.95 | 74.77 | 99.65 | 74.75   |
| total head movements | 1687217  | 7475  | 7483  | 9995  | 7477  | 9965  | 7475    |





#### Best Condition to Use Each Algorithm

---

* **fcfs**
  1. **Batch processing systems** where the order of requests is not critical.
  2. **Simple embedded systems** where predictability and simplicity are more important than performance.

* **sstf** 
  1. **Database management systems** where quick access to nearby data is crucial for performance.
  2. **Multimedia servers** where quick sequential access to data streams is needed.
* **scan**
  1. **Real-time systems** where predictable access patterns are required.
  2. **General-purpose time-sharing systems** where balanced performance is needed for varied workloads.
* **cscan**
  1. **Large-scale web servers** where uniform response times are critical.
  2. **Financial transaction systems** where consistent and predictable performance is needed.
* **look**
  1. **Interactive systems** where quick response times for end-users are important.
  2. **Distributed file systems** where the workload is dynamic and varied
* **clook**
  1. **E-commerce platforms** where consistent access times improve user experience.
  2. **Online transaction processing (OLTP) systems** where uniform and efficient processing is required.