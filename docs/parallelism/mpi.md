# Parallelism
{:.no_toc}

## Content
{:.no_toc}

* TOC
{:toc}



--------------------------------------------------------------------------------

# MPI

We here present some of the aspect of the MPI-3.1 standard.
The newest currently v4.0, however only a few features of the new standard are available currently.

## Asynchronous communications

Asynchronous communications allows the user to start the communication and then come back later to check for completness.
Doing so is convenient to 'hide' the communications behind computations or other tasks.

### Requests

`MPI_Request` is used to track the status of async communications
- the request is null if the handle has a value of `MPI_REQUEST_NULL`
- the request is inactive if it is not associated to an ongoing communication

#### request progress

A call to `MPI_Wait` returns when the operation identified to the request is completed.
If the request is a persistent one it becomes inactive, otherwise the request is deallocated and set to `MPI_REQUEST_NULL`.

A call to `MPI_Test` returns `flag=true` if the operation associated to the request is completed.
If the request is a persistent one it becomes inactive, otherwise the request is deallocated and the handle set to `MPI_REQUEST_NULL`.

A persistent request must be free'd using `MPI_Request_free` as it is not automatically deallocated by the `MPI_Wait`/`MPI_Test`.
It is also permitted to free an ongoing request, however then the status of the request cannot be checked anymore.

### Non-blocking calls

To every point-to-point MPI call, there is a corresponding non-blocking version.
The non-blocking version is prefixed with an `I`: `Alltoall` becomes `IAlltoall`, `Send` becomes `ISend`, `Recv` becomes `IRecv` etc.

The function takes a request into account, which then becomes active at the return of the call (not the completion of the communication).
The status of the request and the completion of communication can be queried or enforced using `MPI_TestX` functions or `MPI_Wait`.

### Persistent calls

When the communication pattern is fixed, there is an advantage in using persistent calls to avoid the overhead while creating the communication.
The persistent calls are `SendInit` and `RecvInit` where a request is initialized with the needed information.
The communication itself can then be started using `MPI_Start` functions and the commpletion is checked as for the non-blocking calls with the `Test` or `Wait` functions.



--------------------------------------------------------------------------------

# UCX

Here are some of the information I have found useful about UCX.
Be mindful this might not be the most accurate and up-to-date information

## UCT vs UCP

UCX has two levels of design: _UC-P_ (P = protocols) and _UC-T_ (T = transport).


The UCT level itself relies on various transport low-level API, based on the hardware.
It can also have different communication mode such as the `RC` (reliable communication) and `UD` (unreliable datagram).
The RC is a reliable mode of communication where a send matches a receive.
However it requires $N^2$ memory to connect $N$ endpoints together (every pair has a send and receive queue).
On the other side, UD can only transfer one MTU at a time and implements only the two sided communications.
Therefore the latency is higher than RC and the bandwidth is smaller. However, it has a very low memory consumption.
On a broad picture, **RC is costly, yet fast** and **UD is cheap, yet slow**.
In an attempt to get the best of both worlds, Mellanox introduced `DC` (Dynamical connected), which reproduces RC but dynamically adapt the memory to the need of communication.

To get an overview of all the UCT available one can use `ucx_info -d`. 
Similarly the command `ucx_info -c` will return the variables associated.

The UCP part of ucx will dynamically choose and handle the best UCT for the communication to perform.
The choice of UCT is based on parameter values and is [described on the website](https://openucx.readthedocs.io/en/master/faq.html#which-transports-does-ucx-use).
Shortly put, for small scale `RC` will be prefered, while `DC` (or `UD` if not available) will be used for larger scales.

On top of this main directions, some specific implementations of each communication strategy can be selected, cfr the [list on the website](https://openucx.readthedocs.io/en/master/faq.html#list-of-main-transports-and-aliases).



## Resources
- [UCX & IB](https://pavanbalaji.github.io/pubs/2017/ccgrid/ccgrid17.ucx-analysis.pdf)
- [OpenUCX FAQ](https://openucx.org/documentation/)


--------------------------------------------------------------------------------

# Performance analysis

## Strong scaling - aka Amdahl's Law

In this case we fix the global workload and increase the computational resources.

### Speedup
if $\alpha$ percent of the program can be done in parallel, then the speedup from one resources $N_o$ to another one $N$ is given by (where $r = N/N_o$)

$$
\dfrac{1}{ \left( 1-\alpha\right) + \dfrac{\alpha}{r}}
$$

As N increases, $r \rightarrow \infty$ and the speedup is bounded by $1/\left(1-\alpha\right)$.

The speedup is then valuable to determine $\alpha$, the percentage of the program actually done in parallel.

### Efficiency
still considering the same definitions, the efficiency of the program is given by 

$$
\dfrac{N_o}{ \left[ \left( 1-\alpha\right) + \dfrac{\alpha}{r} \right] \cdot N}
\qquad \Leftrightarrow \qquad
\dfrac{1}{ \left[ \left( 1-\alpha\right) + \dfrac{\alpha}{r} \right] \cdot r}
\qquad \Leftrightarrow \qquad
\dfrac{1}{ \left( 1-\alpha\right) \cdot r + \alpha}
$$

As $r \rightarrow \infty$, the efficiency is bounded by $1/\infty = 0$, which makes it less attractive as analysis.

<!-- 
Although the bound is not very interesting the inverse efficiency is interesting to when $r$ is still moderate.
The inverse efficiency is also obtained as
$$
r - \left(r-1 \right) \cdot \alpha
$$ -->


## Weak scaling

Here we take the opposite approach as we aim to increase the computational resources proportionally to the size of the problem.

Similar to the Amdahl's law, the first approach here is to measure the speedup:
$$
\dfrac{1}{ r \cdot \left[ \left( 1-\alpha\right) + \dfrac{\alpha}{r} \right]}
$$
where the $r$ comes from the increase in size of the problem. This expression is exactly the same as the strong efficiency. Therefore not much can be extracted form this approach.


Another analysis is to decompose the computational time of the program into operations that scale with the size of the problem and in fixed cost operations (which depends on the number of involved ranks in the case of MPI).
Now the speedup becomes
$$
\dfrac{\dfrac{\beta S_o + C_o}{N_o} }{ \dfrac{\beta S_o r + C}{N}}
\qquad \Leftrightarrow \qquad
\dfrac{\left(\beta S_o  + C_o\right) \cdot r}{\beta S_o r + C}
$$

The goal of the weak scalability is focused on the communication part of the program.
As the communication size per rank stays constant, we are interested in the evolution of the latency in the communications.





--------------------------------------------------------------------------------
[home](../index.md)