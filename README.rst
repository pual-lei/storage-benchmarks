Introduction
============

The storage-benchmarks repository is used for the performance comparison of
different erasure coding libraries, currently Kodo, OpenFEC and
the Intel Storage Acceleration library (ISA).

If you have any questions or suggestions about the benchmarks, please contact
us at our developer mailing list (hosted at Google Groups):

* http://groups.google.com/group/steinwurf-dev

Licenses
--------

1. Kodo
.......
To obtain a valid Kodo license **you must fill out the license request** form_.

Kodo is available under a research and educational friendly license, see the
details in the LICENSE.rst file.

.. _form: http://steinwurf.com/license/

2. ISA
......
See "About_bsd.txt" in the "isa-l_open_src_2.13" folder.

3. OpenFEC
..........
See "Licence_CeCILL_V2-en.txt" in the "openfec-1.4.2" folder.

The libraries are benchmarked in standalone applications that implement a
common benchmarking interface to create a framework for fair comparison.
These standalone applications are licensed under the same terms as the
original libraries (Kodo license, BSD or CeCILL).

Requirements
============

1. A recent C++11 compiler
2. yasm (for compiling the Assembly sources in ISA)

Installation
=============

Clone the repository::

    git clone https://github.com/steinwurf/storage-benchmarks.git

How to build it
===============

The benchmarks can be built with waf::

  cd storage-benchmarks
  python waf configure
  python waf build

How to run the benchmarks
=========================

The benchmark applications are built in the ``./build/linux/benchmark`` folder,
and they can be started with the following commands::

  build/linux/benchmark/kodo_storage/kodo_storage
  build/linux/benchmark/isa_throughput/isa_throughput
  build/linux/benchmark/openfec_throughput/openfec_throughput

By default, these applications will execute some basic benchmarks with the
same default parameters for all libraries.

The benchmarked scenario is the same in all cases:

1. A random data block is generated which consists of a given number of
   original symbols (specified by the ``symbols`` parameter).
2. Encoding: This data block is used to generate some encoded symbols
   (the encoding throughput is measured during this step)
3. Several original symbols are erased from the data block (this is
   specified by the ``loss_rate`` parameter)
4. Decoding: The erased original symbols are reconstructed using the
   encoded symbols (the decoding throughput is measured during this step)

The benchmark results contain the following metrics:

- goodput (encoding): the total number of encoded bytes divided by
  processing time (measured in MegaBytes/second)
- extra_symbols (decoding): some codecs might need more encoded symbols than
  the number of erased symbols to reconstruct the original symbols, the
  extra_symbols show this difference (NB: this is measured in number of
  packets, not in MB/s as shown in the output)
- goodput (decoding): the total number of reconstructed bytes divided by
  processing time (measured in MegaBytes/second)

Additional parameters can be given to these binaries to customize
the benchmark runs:

``--runs=N``: the number of repetitions for a given benchmark

``--symbols=N``: the number of symbols in a block/generation

``--symbol_size=N``: the size of each symbol in bytes (this should be a
multiple of 64)

``--loss_rate=0.x``: the ratio of erased original symbols

``--type=encoder/decoder``: enables only the encoder or decoder benchmark type

``--python_file=filename``: saves the results as a Python dictionary to the given file

``--csv_file=filename``: saves the results as a CSV table to the given file

``--json_file=filename``: saves the results as a JSON document to the given file

Extra option for kodo_storage:

``--density=0.x``: the code density used for the sparse RLNC benchmark

For example, kodo_storage can be invoked with these parameters::

  build/linux/benchmark/kodo_storage/kodo_storage --symbols=100 --symbol_size=1000000 --loss_rate=0.2 --python_file=myfile.py --csv_file=myfile.csv

We also have a helper script to run the benchmark applications in sequence.
You can start it without parameters to use the default settings::

  sh run-all-benchmarks.sh

Or you can specify some parameters that will be used for every benchmark application::

  sh run-all-benchmarks.sh --runs=10 --symbols=16 --symbol_size=32000
  sh run-all-benchmarks.sh --runs=10 --symbols=64 --symbol_size=32000
  sh run-all-benchmarks.sh --runs=10 --symbols=16 --symbol_size=1000000
  sh run-all-benchmarks.sh --runs=10 --symbols=64 --symbol_size=1000000
