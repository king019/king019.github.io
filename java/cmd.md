[Java Platform, Standard Edition Tools Reference](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/index.html)



# jps
# jstat

class: Displays statistics about the behavior of the class loader.

compiler: Displays statistics about the behavior of the Java HotSpot VM Just-in-Time compiler.

gc: Displays statistics about the behavior of the garbage collected heap.

gccapacity: Displays statistics about the capacities of the generations and their corresponding spaces.

gccause: Displays a summary about garbage collection statistics (same as -gcutil), with the cause of the last and current (when applicable) garbage collection events.

gcnew: Displays statistics of the behavior of the new generation.

gcnewcapacity: Displays statistics about the sizes of the new generations and its corresponding spaces.

gcold: Displays statistics about the behavior of the old generation and metaspace statistics.

gcoldcapacity: Displays statistics about the sizes of the old generation.

gcmetacapacity: Displays statistics about the sizes of the metaspace.

gcutil: Displays a summary about garbage collection statistics.

printcompilation: Displays Java HotSpot VM compilation method statistics.

# jstatd
# jinfo
# jhat
# jmap
Options

<no option>

When no option is used, the jmap command prints shared object mappings. For each shared object loaded in the target JVM, the start address, size of the mapping, and the full path of the shared object file are printed. This behavior is similar to the Oracle Solaris pmap utility.

-dump:[live,] format=b, file=filename

Dumps the Java heap in hprof binary format to filename. The live suboption is optional, but when specified, only the active objects in the heap are dumped. To browse the heap dump, you can use the jhat(1) command to read the generated file.

-finalizerinfo

Prints information about objects that are awaiting finalization.

-heap

Prints a heap summary of the garbage collection used, the head configuration, and generation-wise heap usage. In addition, the number and size of interned Strings are printed.

-histo[:live]

Prints a histogram of the heap. For each Java class, the number of objects, memory size in bytes, and the fully qualified class names are printed. The JVM internal class names are printed with an asterisk (*) prefix. If the live suboption is specified, then only active objects are counted.

-clstats

Prints class loader wise statistics of Java heap. For each class loader, its name, how active it is, address, parent class loader, and the number and size of classes it has loaded are printed.

-F

Force. Use this option with the jmap -dump or jmap -histo option when the pid does not respond. The live suboption is not supported in this mode.

-h

Prints a help message.

-help

Prints a help message.

-Jflag

Passes flag to the Java Virtual Machine where the jmap command is running.


jstack


-F

Force a stack dump when jstack [-l] pid does not respond.

-l

Long listing. Prints additional information about locks such as a list of owned java.util.concurrent ownable synchronizers. See the AbstractOwnableSynchronizer class description at

http://docs.oracle.com/javase/8/docs/api/java/util/concurrent/locks/AbstractOwnableSynchronizer.html

-m

Prints a mixed mode stack trace that has both Java and native C/C++ frames.

-h

Prints a help message.

-help

Prints a help message.






