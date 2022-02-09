# Component Developer Documentation

This repository is for developer documentation related to various VELOC / SCR components.
It also contains [documentation on policies](./POLICIES.md) that apply to all of the repositories.

## Status

All open issues for the components can be viewed on the [Components Project Board](https://github.com/orgs/ECP-VeloC/projects/1).

Repo | GitHub Actions
-----|---------------
[KVTree](https://github.com/ecp-veloc/kvtree)     | [![Build&Test](https://github.com/ECP-VeloC/KVTree/actions/workflows/build-and-test.yml/badge.svg)](https://github.com/ECP-VeloC/KVTree/actions/workflows/build-and-test.yml)
[AXL](https://github.com/ecp-veloc/axl)           | [![Build Only](https://github.com/ECP-VeloC/AXL/actions/workflows/build-and-test.yml/badge.svg)](https://github.com/ECP-VeloC/AXL/actions/workflows/build-and-test.yml)
[SPath](https://github.com/ecp-veloc/spath)       | [![Build&Test](https://github.com/ECP-VeloC/spath/actions/workflows/build-and-test.yml/badge.svg)](https://github.com/ECP-VeloC/spath/actions/workflows/build-and-test.yml)
[Shuffile](https://github.com/ecp-veloc/shuffile) | [![Build Only](https://github.com/ECP-VeloC/shuffile/actions/workflows/build-and-test.yml/badge.svg)](https://github.com/ECP-VeloC/shuffile/actions/workflows/build-and-test.yml)
[Redset](https://github.com/ecp-veloc/redset)     | [![Build Only](https://github.com/ECP-VeloC/redset/actions/workflows/build-and-test.yml/badge.svg)](https://github.com/ECP-VeloC/redset/actions/workflows/build-and-test.yml)
[Rankstr](https://github.com/ecp-veloc/rankstr)   | [![Build&Test](https://github.com/ECP-VeloC/rankstr/actions/workflows/build-and-test.yml/badge.svg)](https://github.com/ECP-VeloC/rankstr/actions/workflows/build-and-test.yml)
[ER](https://github.com/ecp-veloc/er)             | [![Build Only](https://github.com/ECP-VeloC/er/actions/workflows/build-and-test.yml/badge.svg)](https://github.com/ECP-VeloC/er/actions/workflows/build-and-test.yml)


## Components Diagram

![Components Diagram](./diagram/components.png)

# Component Descriptions

## Basic Data Structures and Algorithms

### [KVTree](https://github.com/ecp-veloc/kvtree): Recursive key-value structure

Documentation:
- [Overview](https://github.com/ecp-veloc/kvtree/blob/main/doc/rst/users/overview.rst)
- [API](https://github.com/ecp-veloc/kvtree/blob/main/doc/rst/users/api.rst)
- [Gather/Scatter](https://github.com/ecp-veloc/kvtree/blob/main/doc/rst/users/gatherscatter.rst)
- [File Format](https://github.com/ecp-veloc/kvtree/blob/main/doc/rst/users/fileformat.rst)
- [Debugging](https://github.com/ecp-veloc/kvtree/blob/main/doc/rst/users/debugging.rst)
- [Related SCR dev doc](https://scr-dev.readthedocs.io/en/latest/developers/hash.html)

Each KVTree object contains a list of key/value pairs.
Each key is a string, each value is another kvtree object.
This is a nested data structures, similar to a python dict or perl hash.
The library provides functions to serialize a kvtree object to / from a file.
It also optionally provides MPI send / recv functions to transfer an object from one process to another.

### [spath](https://github.com/ecp-veloc/spath): represent and manipulate file system paths

Documentation:
- [Overview](https://github.com/ecp-veloc/spath/blob/main/doc/rst/spath.rst)
- [Related SCR dev doc](https://scr-dev.readthedocs.io/en/latest/developers/path.html)

Create an spath object from a string.
The library includes functions to extract components (such as dirname, basename).
It can create an absolute path or compute a relative path from a source path to a destination path.
It can also simplify a path (i.e., convert `../foo//bar` to `foo/bar`).

### [rankstr](https://github.com/ecp-veloc/rankstr): splits processes into groups based on a set of process which have the same input string

Rankstr uses bitonic sort for a scalable method to identify process groups.
It is useful to create a communicator of ranks that all share the same storage device, then rank 0 in this communicator can create directory and inform others that dir has been created with barrier.
It is also used to split processes into groups based on failure group (failure group of NODE --> splits MPI_COMM_WORLD into subgroups based on hostname).

## File transfers between cache and parallel file system

### [AXL](https://github.com/ecp-veloc/axl): Asynchronous transfer library

Documentation:
- [Overview](https://github.com/ecp-veloc/axl/blob/main/doc/README.md)
- [Internal Data Structure](https://github.com/ecp-veloc/axl/blob/main/doc/file_lists.md)
- [Related SCR dev doc](https://scr-dev.readthedocs.io/en/latest/developers/file_transfer.html)

AXL is used to transfer a file from one path to another using synchronous and asynchronous methods.
This can only be done between storage tiers, AXL does not (yet) support movement within a storage tier (such as between 2 compute nodes).
Asynchronous methods include via pthreads, IBM BB API, Cray Datawarp.
AXL will create directories for destination files.

## Redundancy Encoding/Decoding and File Migration

### [Redset](https://github.com/ecp-veloc/redset): Encode/decode a set of files with a redundancy method

Documentation:
- [API overview](https://github.com/ECP-VeloC/redset/blob/main/doc/rst/redset.rst)
- [redset data structure](https://github.com/ECP-VeloC/redset/blob/main/doc/rst/implementation.rst)
- [redundancy schemes](https://github.com/ECP-VeloC/redset/blob/main/doc/rst/schemes.rst)
- [SCR dev docs: redundancy descriptors](https://scr-dev.readthedocs.io/en/latest/developers/redundancy_descriptors.html)
- [SCR dev docs: Encoding schemes](https://scr-dev.readthedocs.io/en/latest/developers/schemes.html)
- [SCR dev docs: XOR scheme](https://scr-dev.readthedocs.io/en/latest/developers/scheme_xor.html)

Redset will create the redundancy data needed for a set of files.
It can rebuild a file with provided redundancy information.

### [Shuffile](https://github.com/ecp-veloc/shuffile): Shuffle files between MPI ranks

Documentation:
- [SCR dev docs: Scatter filemaps](https://scr-dev.readthedocs.io/en/latest/developers/flow_api.html#scr-scatter-filemaps)

Files are registered with
Used during restart, shuffile will move a file to the 'owning' MPI rank.

### [ER](https://github.com/ecp-veloc/er): Encode + Rebuild

ER provides an abstraction around shuffile and redset.
During encode, ER calls redset to apply a redundancy scheme to a set of files,
and then it calls shuffile to record which rank owns which files.
During rebuild, ER first calls shuffile to move files back to their owning ranks,
and then redset is called to rebuild any missing files.
