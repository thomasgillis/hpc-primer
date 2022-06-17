# MPI
{:.no_toc}

## Content
{:.no_toc}

* TOC
{:toc}

We here present some of the aspect of the MPI-3.1 standard.
The newest currently v4.0, however only a few features of the new standard are available currently.

--------------------------------------------------------------------------------

# Asynchronous communications

Asynchronous communications allows the user to start the communication and then come back later to check for completness.
Doing so is convenient to 'hide' the communications behind computations or other tasks.

## Requests

`MPI_Request` is used to track the status of async communications
- the request is null if the handle has a value of `MPI_REQUEST_NULL`
- the request is inactive if it is not associated to an ongoing communication

### request progress

A call to `MPI_Wait` returns when the operation identified to the request is completed.
If the request is a persistent one it becomes inactive, otherwise the request is deallocated and set to `MPI_REQUEST_NULL`.

A call to `MPI_Test` returns `flag=true` if the operation associated to the request is completed.
If the request is a persistent one it becomes inactive, otherwise the request is deallocated and the handle set to `MPI_REQUEST_NULL`.

A persistent request must be free'd using `MPI_Request_free` as it is not automatically deallocated by the `MPI_Wait`/`MPI_Test`.
It is also permitted to free an ongoing request, however then the status of the request cannot be checked anymore.

## Non-blocking calls

To every point-to-point MPI call, there is a corresponding non-blocking version.
The non-blocking version is prefixed with an `I`: `Alltoall` becomes `IAlltoall`, `Send` becomes `ISend`, `Recv` becomes `IRecv` etc.

The function takes a request into account, which then becomes active at the return of the call (not the completion of the communication).
The status of the request and the completion of communication can be queried or enforced using `MPI_TestX` functions or `MPI_Wait`.

## Persistent calls

When the communication pattern is fixed, there is an advantage in using persistent calls to avoid the overhead while creating the communication.
The persistent calls are `SendInit` and `RecvInit` where a request is initialized with the needed information.
The communication itself can then be started using `MPI_Start` functions and the commpletion is checked as for the non-blocking calls with the `Test` or `Wait` functions.