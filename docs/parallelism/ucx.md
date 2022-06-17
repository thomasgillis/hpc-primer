# UCX
{:.no_toc}

## Content
{:.no_toc}

* TOC
{:toc}

Here are some of the information I have found useful about UCX.
Be mindful this might not be the most accurate and up-to-date information

--------------------------------------------------------------------------------

# UCT vs UCP

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



-----------------------------------------
[home](../index.md)