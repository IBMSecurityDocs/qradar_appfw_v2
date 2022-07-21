# App memory use optimization

Tune the IBM® QRadar® application framework to optimize app memory usage.

Use any of the following methods to help prevent your app from using an excessive amount of memory.

- Avoid allocating large amounts of memory by chunking (or staggering) the work into small memory footprints.
- Call for garbage collection when the app is finished with code that uses large amounts of memory.

## Calling for garbage collection

The Python interpreter might not know when to free the memory. You can speed up garbage collection by placing the
following code after sections where large amounts of memory are no longer needed:

```python
import gc
gc.collect()
```

> Note: Python does not ensure that any memory that your code uses is returned to the OS. Garbage collection ensures
> that the memory used by an object is free to be used by another object in the future.

## Tools

Some tools that can help you identify memory problems:

### Memory profiler

A Python module for monitoring memory consumption of a process. For more information, see [the PyPi page for the memory
profiler](https://pypi.python.org/pypi/memory_profiler).

### Linux utilities

The command-line utility top can be used to monitor all Python processes running on the machine:

```bash
top -p $(pgrep -f -d',' python3)
```

You can also use the following command to get the total memory (in MB) used by all Python interpreters on your system:

```bash
ps -eo rss,command | grep python3 | grep -v grep | awk '{SUM+=$1}END{print SUM}'
```

### Resource module

You can log the amount of memory your process uses by adding the following code to your module:

```python
import resource
print('Memory usage: %s (kb)' % resource.getrusage (resource.RUSAGE_SELF).ru_maxrss)
```
