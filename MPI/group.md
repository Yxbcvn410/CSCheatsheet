# Групповые операции в MPI

Чтобы выполнить барьерную синхронизацию, есть функция [MPI_Barrier](https://www.mpich.org/static/docs/latest/www3/MPI_Barrier.html). 
На вход она принимает множество процессов, возвращает код выполнения:

```c++
int MPI_Barrier(MPI_Comm comm)
```
Впрочем, эта функция используется редко - все групповые операции синхронизированны и так.

Если мы хотим загрузить одни и те же данные на много потоков, желательно воспользоваться функцией [MPI_Bcast](https://www.mpich.org/static/docs/latest/www3/MPI_Bcast.html).
Это оптимальнее, чем много раз вызывать `MPI_Send` - асимптотически время выполнения этой операции O(log N).

```c++
int MPI_Bcast(void *buffer, int count, MPI_Datatype datatype, int root, MPI_Comm comm)
```

**Важно**. Такого рода сообщения не имеют тега. Также в этой функции необходимо задать номер потока-отправителя.

Если нужно собрать воедино данные из кучи разных массивов, есть функция [MPI_Gather](https://www.mpich.org/static/docs/latest/www3/MPI_Gather.html).

```c++
int MPI_Gather(const void *sendbuf, int sendcount, MPI_Datatype sendtype,
               void *recvbuf, int recvcount, MPI_Datatype recvtype, int root, MPI_Comm comm)
```

**Важно**. `sendcount` и `recvcount` должны быть одинаковыми и равны друг другу для всех потоков. Если у каждого потока свой размер данных, необходимо использовать функцию [MPI_Gatherv](https://www.mpich.org/static/docs/latest/www3/MPI_Gatherv.html).


```c++
int MPI_Gatherv(const void *sendbuf, int sendcount, MPI_Datatype sendtype,
                void *recvbuf, const int *recvcounts, const int *displs,
                MPI_Datatype recvtype, int root, MPI_Comm comm)
```

Если у нас много потоков и мы хотим быстро, скажем, просуммировать числа, есть функция [MPI_Reduce](https://www.mpich.org/static/docs/latest/www3/MPI_Reduce.html).

```c++
int MPI_Reduce(const void *sendbuf, void *recvbuf, int count, MPI_Datatype datatype,
               MPI_Op op, int root, MPI_Comm comm)
```

Кратко о параметрах:
- `sendbuf`, `count` - задаётся область входных данных
- `recvbuf` - валидно только для принимающего потока, в этот адрес запишется результат
- `op` - собственно, редукционная операция. Есть много предопределённых, например, для суммирование - `MPI_SUM`

----

[На главную](../Readme.md)