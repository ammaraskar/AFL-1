.. _motivation:

=====================
Motivation behind AFL
=====================


Challenges of guided fuzzing
============================

Fuzzing is one of the most powerful and proven strategies for identifying
security issues in real-world software; it is responsible for the vast
majority of remote code execution and privilege escalation bugs found to date
in security-critical software.

Unfortunately, fuzzing is also relatively shallow; blind, random mutations
make it very unlikely to reach certain code paths in the tested code, leaving
some vulnerabilities firmly outside the reach of this technique.

There have been numerous attempts to solve this problem. One of the early
approaches - pioneered by Tavis Ormandy - is corpus distillation. The method
relies on coverage signals to select a subset of interesting seeds from a
massive, high-quality corpus of candidate files, and then fuzz them by
traditional means. The approach works exceptionally well, but requires such
a corpus to be readily available. In addition, block coverage measurements
provide only a very simplistic understanding of program state, and are less
useful for guiding the fuzzing effort in the long haul.

Other, more sophisticated research has focused on techniques such as program
flow analysis ("concolic execution"), symbolic execution, or static analysis.
All these methods are extremely promising in experimental settings, but tend
to suffer from reliability and performance problems in practical uses - and
currently do not offer a viable alternative to "dumb" fuzzing techniques.

.. _afl-approach:

The AFL approach
================

American Fuzzy Lop is a brute-force fuzzer coupled with an exceedingly simple
but rock-solid instrumentation-guided genetic algorithm. It uses a modified
form of edge coverage to effortlessly pick up subtle, local-scale changes to
program control flow.

Simplifying a bit, the overall algorithm can be summed up as:

  1) Load user-supplied initial test cases into the queue,

  2) Take next input file from the queue,

  3) Attempt to trim the test case to the smallest size that doesn't alter
     the measured behavior of the program,

  4) Repeatedly mutate the file using a balanced and well-researched variety
     of traditional fuzzing strategies,

  5) If any of the generated mutations resulted in a new state transition
     recorded by the instrumentation, add mutated output as a new entry in the
     queue.

  6) Go to 2.

The discovered test cases are also periodically culled to eliminate ones that
have been obsoleted by newer, higher-coverage finds; and undergo several other
instrumentation-driven effort minimization steps.

As a side result of the fuzzing process, the tool creates a small,
self-contained corpus of interesting test cases. These are extremely useful
for seeding other, labor- or resource-intensive testing regimes - for example,
for stress-testing browsers, office applications, graphics suites, or
closed-source tools.

The fuzzer is thoroughly tested to deliver out-of-the-box performance far
superior to blind fuzzing or coverage-only tools.