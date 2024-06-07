# Design Doc - Disk Scheduling

### B113040003 鍾承恩 B113040004 蔡英助

### Table of Contents





#### Preprocess and Global

<img src="img/design_doc_4/library_and_global_vars_functions.png" style="width:75%;" >



#### main()

<img src="img/design_doc_4/main.png" style="width:75%;" >



#### fcfs()
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

<img src="img/design_doc_4/sstf.png" style="width:75%;" >



#### scan()
> `A.k.a. elevator algorithm`
>
> 1. head 先往 cylinder 為 4999 的方向移動，再往 cylinder 為 0 的方向移動；
> 
>    我們因為先向 4999 的那方移動，故我們的head中途會到達 4999。
>
> 2. minimize the seek time
>
> 3. strengths
>
>        a. High throughput
>
>        b. Low variance of response time
>
>        c. Starvation is avoided
> 
>    weaknesses
>
>        a. Long waiting time for requests for locations just visited by disk arm
<img src="img/design_doc_4/scan.png" style="width:75%;" >



#### cscan()
> `C-SCAN(circular SCAN)`
> 
> 1. head 先往 cylinder 為 4999 的方向移動，接著直接移到 0，再繼續往 cylinder 為 4999 的方向移動；
>
>    head 中途會到達 4999、0。
>
> 2. a
>
> 3. strengths
>
>        a. Provides more uniform wait time compared to SCAN
> 
>    weaknesses
>
>        a. Long waiting time for requests for locations just visited by disk arm
>
>        b. More seek movements are caused in C-SCAN compared to SCAN Algorithm
<img src="img/design_doc_4/cscan.png" style="width:75%;" >



#### look()
> 1. head 先往 cylinder 為 4999 的方向移動，再往 cylinder 為 4999 的方向移動；
>
>    需要注意不一樣的是 head 只會到達 cylinder request 的位置，不一定會到達 4999。
>
> 2. a
>
> 3. strengths
>
>        a. Higher throughput
>
>        b. Low variance response timeuniform waiting time and response time
>
>        c. Starvation is avoided
> 
>    weaknesses
>
>        a. Overhead of finding the end requests is present
<img src="img/design_doc_4/look.png" style="width:75%;" >



#### clook()
> `C-LOOK(circular LOOK)`
> 1. head 先往 cylinder 為 4999 的方向移動，接著直接移到最小的 cylinder request，再繼續往 cylinder 為 4999 的方向移動；
>
>    需要注意不一樣的是 head 只會到達 cylinder request 的位置，不一定會到達 4999。
>
> 2. a
>
> 3. strengths
>
>        a. Starvation is avoided
>
>        b. Low variance is provided in waiting time and response time
> 
>    weaknesses
>
>        a. Overhead of finding the end requests is present
<img src="img/design_doc_4/clook.png" style="width:75%;" >

#### optimal()

<img src="img/design_doc_4/optimal.png" style="width:75%;" >



#### cmp()

<img src="img/design_doc_4/cmp-7749039.png" style="width:75%;" >



