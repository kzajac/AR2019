<!-- class: center, middle, inverse -->

## Chapel

### Uruchomienie na Zeusie

```shell
export PATH=/usr/lib64/openmpi/bin:$PATH
export LD_LIBRARY_PATH=/usr/lib64/openmpi/lib
export PYTHONPATH=/usr/lib64/python2.7/site-packages/openmpi:/usr/lib64/python2.7/site-packages/openmpi
```
* Uruchamianie: 

---



## Hello world! 

* Dokumentacja i standard: [mcs.anl.gov/research/projects/mpi](http://www.mcs.anl.gov/research/projects/mpi/)
* Tutorial [mpitutorial.com](http://mpitutorial.com/) i przykłady [github.com/wesleykendall/mpitutorial](https://github.com/wesleykendall/mpitutorial)

```cpp
#include <stdio.h>
#include <mpi.h>

int main (int argc, char * argv[])
{
  int rank, size;

  MPI_Init (&argc, &argv);  /* starts MPI */
  MPI_Comm_rank (MPI_COMM_WORLD, &rank);  /* get current process id */
  MPI_Comm_size (MPI_COMM_WORLD, &size);  /* get number of processes */
  printf( "Hello world from process %d of %d\n", rank, size );
  MPI_Finalize();
  return 0;
}
```
### Kompilacja
```shell
$ mpicc -o hello_world_c hello_world.c
```



---
## Komunikacja w C

```cpp
#include <mpi.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char** argv) {
  MPI_Init(NULL, NULL);
  int world_rank;
  MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);
  int world_size;
  MPI_Comm_size(MPI_COMM_WORLD, &world_size);

  // We are assuming at least 2 processes for this task
  if (world_size < 2) {
    fprintf(stderr, "World size must be greater than 1 for %s\n", argv[0]);
    MPI_Abort(MPI_COMM_WORLD, 1);
  }
  int number;
  if (world_rank == 0) {
    // If we are rank 0, set the number to -1 and send it to process 1
    number = -1;
    MPI_Send(&number, 1, MPI_INT, 1, 0, MPI_COMM_WORLD);
  } else if (world_rank == 1) {
    MPI_Recv(&number, 1, MPI_INT, 0, 0, MPI_COMM_WORLD, MPI_STATUS_IGNORE);
    printf("Process 1 received number %d from process 0\n", number);
  }
  MPI_Finalize();
}
```

---
## Hello world! w C++ i Boost

* Dokumentacja: [boost.org/doc/libs/1_55_0/doc/html/mpi.html](http://www.boost.org/doc/libs/1_55_0/doc/html/mpi.html)
* Supportowana jest tylko standardowa i asynchroniczna komunikacja [boost.org/doc/libs/1_55_0/doc/html/mpi/tutorial.html#mpi.point_to_point](http://www.boost.org/doc/libs/1_55_0/doc/html/mpi/tutorial.html#mpi.point_to_point)

```cpp
#include <boost/mpi/environment.hpp>
#include <boost/mpi/communicator.hpp>
#include <iostream>
namespace mpi = boost::mpi;

int main()
{
  mpi::environment env;
  mpi::communicator world;
  std::cout << "I am process " << world.rank() << " of " << world.size()
            << "." << std::endl;
  return 0;
}

```

### Kompilacja

```shell
$ mpic++ -o hello_world_cpp -lboost_mpi hello_world.cpp
```

---
## Komunikacja w C++ i Boost

```cpp
#include <boost/mpi.hpp>
#include <iostream>
#include <string>
#include <boost/serialization/string.hpp>
namespace mpi = boost::mpi;

int main()
{
  mpi::environment env;
  mpi::communicator world;

  if (world.rank() == 0) {
    world.send(1, 0, std::string("Hello world!"));
  } else if(world.rank() == 1) {
    std::string msg;
    world.recv(0, 0, msg);
    std::cout << msg << std::endl;
  }

  return 0;
}
```
---
## Master–slave w MPI

Dwa podejścia:

 * wszystko w 1 pliku (master dla rank == 0, slave dla pozostałych) – jak w poprzednich przykładach;
 * uruchamianie innego pliku binarnego (`MPI_Comm_spawn`),
   * [http://www.mcs.anl.gov/research/projects/mpi/mpi-standard/mpi-report-2.0/node98.htm](http://www.mcs.anl.gov/research/projects/mpi/mpi-standard/mpi-report-2.0/node98.htm).


---
# Uruchamianie MPI na klastrze Zeus

System modułów – oprogramowanie ładowane do `$PATH` na żądanie

```shell
[plgkfigiela@zeus ~]$ module avail

---------- /software/local/Modules/versions ----------
3.2.10     3.2.10.el6 3.2.9

--- /software/local/Modules/3.2.10.el6/modulefiles ---
dot         module-info null
module-git  modules

------ /software/local/Modules/modulefiles/el6 -------
apps/arora/0.11.0
apps/autodock/4.2.3
apps/bamtools/2.3.0
.....

[plgkfigiela@zeus ~]$ module load el6/apps/ruby/2.0.0-p247
 'el6/apps/ruby/2.0.0-p247' load complete.
```

---
# Uruchamianie MPI na klastrze Zeus

  * Obsługa systemu kolejkowego (PBS): [https://docs.plgrid.pl/pages/viewpage.action?pageId=4260614](https://docs.plgrid.pl/pages/viewpage.action?pageId=4260614)
  * Tutoriale dla innych klastrów (przykłady uruchomienia dla MPI):
     * [http://kdm.wcss.wroc.pl/wiki/MPIEXEC](http://kdm.wcss.wroc.pl/wiki/MPIEXEC),
     * [http://www.icm.edu.pl/kdm/System_kolejkowy](http://www.icm.edu.pl/kdm/System_kolejkowy).
  * U nas: `qsub -I -q plgrid -l nodes=2:ppn=12`:
    * `nodes=2` – liczba węzłów,
    * `ppn=12` – liczba rdzeni na każdym węźle.
    * Po zakolejkowaniu zadania otrzymamy interaktywną konsolę.
    * Dalej normalnie – `mpiexec`, liczby procesorów oraz machinefile nie należy podawać.
  * **Python:** ważne, by używać dystrybucji Pythona ładowanej z modułu (uwaga na `#!/usr/bin/python`, lepiej użyć `#!/usr/bin/env python`)

---
# Uruchamianie MPI na klastrze Zeus

Moduły `libs/boost/1.41.0` oraz `libs/boost/1.52.0` mają skompilowane wsparcie dla MPI.

```shell
[plgkfigiela@zeus ~]$ qsub -I -q plgrid-testing -l nodes=2:ppn=12
...

[plgkfigiela@zeus ~]$ module load libs/boost/1.52.0
 'tools/python/2.7.2-ucs4' load complete.
 'tools/openmpi/1.6.3-gnu-4.1.2-ib' load complete.
 'libs/boost/1.52.0' load complete.

[plgkfigiela@zeus tpr_lab]$ pip install --user mpi4py
....

[plgkfigiela@zeus tpr_lab]$ mpicc -o hello_c hello_world.c
[plgkfigiela@zeus tpr_lab]$ mpic++ -o hello_c++ -L $BOOST_ROOT/lib -lboost_mpi hello_world.cpp
```
