# Algorithm Laboratory

An interactive data structures and algorithms visualizer that runs entirely in a single HTML file. Every animation is driven by a real execution trace, not a scripted playback, so what you see on screen is exactly what the code did.

## Overview

Algorithm Laboratory is built for people who want to understand how an algorithm behaves, not just watch coloured bars move. The visualizer instruments a genuine JavaScript implementation of each algorithm, records every comparison, swap, write and function call as a discrete step, and then lets you move through that recording in either direction.

Because the execution trace is captured up front, you can step backwards, jump to any point on the timeline, or skip straight to the next interesting moment. The highlighted source line, the variable table, the call stack and the metrics counters all stay synchronised with the step you are looking at.

The entire application is one file with no build step, no package installation and no backend. Open it and it works.

## Features

**Real execution, not animation.** Each algorithm runs against a tracer object that records state at every meaningful operation. The visualizer replays that recording, so the step count, the comparison tally and the final output are all genuine.

**Bidirectional stepping.** Move forward or backward one operation at a time, scrub the timeline, restart, or jump to the end. Stepping backward restores the exact earlier state rather than recomputing it.

**Synchronised code view.** The source panel highlights the line currently executing and scrolls to keep it visible. A pseudocode view is available alongside the JavaScript for every algorithm.

**Three explanation depths.** Beginner mode gives plain language narration, Detailed mode adds the reasoning behind each decision, and Code Trace mode shows the raw operation log for readers who want the mechanics.

**Live inspector.** Four panels track the run as it happens: State shows loop ranges and variables, Stack shows call frames and the recursion tree, Metrics shows live counters and complexity tables, and Info shows the algorithm reference including common mistakes.

**Comparison Lab.** Run several algorithms against the same dataset and compare their operation counts side by side.

**Quiz mode.** Generated questions test whether you can predict what the algorithm does next.

**Custom and preset data.** Enter your own values or generate datasets shaped to expose specific behaviour, including nearly sorted, reverse sorted, duplicate heavy, few unique and already sorted.

**Themes and persistence.** Dark, light and system themes, with your preferences stored locally and applied before the first paint so there is no flash of the wrong theme.

**Responsive layout.** The interface adapts from 320 pixel phones through to large desktop displays. On narrow screens a four tab switcher gives access to the algorithm list, the visualization, the code and the inspector.

**Accessible.** Keyboard driven throughout, with live region announcements and proper tab semantics on the inspector panels.

## Algorithms

Twenty two implementations across four categories.

### Sorting

| Algorithm | Best | Average | Worst | Space |
|---|---|---|---|---|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) |
| Shell Sort | O(n log n) | O(n^1.25) | O(n²) | O(1) |
| Counting Sort | O(n + k) | O(n + k) | O(n + k) | O(n + k) |
| Radix Sort | O(d·(n + b)) | O(d·(n + b)) | O(d·(n + b)) | O(n + b) |

### Searching

| Algorithm | Best | Average | Worst | Space |
|---|---|---|---|---|
| Linear Search | O(1) | O(n) | O(n) | O(1) |
| Binary Search | O(1) | O(log n) | O(log n) | O(1) |
| Jump Search | O(1) | O(√n) | O(√n) | O(1) |
| Interpolation Search | O(1) | O(log log n) | O(n) | O(1) |

### Data structures

| Structure | Best | Average | Worst | Space |
|---|---|---|---|---|
| Stack (LIFO) | O(1) | O(1) | O(1) | O(n) |
| Queue (FIFO) | O(1) | O(1) | O(1) | O(n) |
| Singly Linked List | O(1) | O(n) | O(n) | O(n) |
| Doubly Linked List | O(1) | O(n) | O(n) | O(n) |
| Circular Linked List | O(1) | O(n) | O(n) | O(n) |
| Hash Table | O(1) | O(1) | O(n) | O(n) |
| Array Operations | O(1) | O(n) | O(n) | O(n) |

### Trees

| Structure | Best | Average | Worst | Space |
|---|---|---|---|---|
| Binary Search Tree | O(log n) | O(log n) | O(n) | O(n) |
| Heap / Priority Queue | O(1) | O(log n) | O(log n) | O(n) |


## Architecture

The application is organised around four cooperating pieces.

**Tracer.** Each algorithm receives a tracer instead of writing to the screen. The tracer records a step whenever something meaningful happens, capturing the array contents, the active variables, the highlight state, the call stack and a human readable explanation. It also maintains the running metrics.

**ExecutionEngine.** Once an algorithm finishes, the engine holds the complete list of steps and owns the cursor into that list. Playback, stepping, seeking and speed control all operate on the cursor, which is why stepping backward is exact rather than approximate. The engine notifies the interface on every cursor change.

**AlgorithmRegistry.** Algorithms register themselves as self contained definitions containing metadata, complexity figures, the displayed source, the pseudocode and the instrumented implementation. Adding a new algorithm means adding one definition, with no changes to the engine or the interface.

**UIController.** The controller renders the current step into the visualization, the code panel, the inspector and the metrics bar. Rendering is a pure function of the current step, so any cursor position produces a correct display without replaying history.

### Code and visualization synchronisation

Every recorded step carries the source line number that produced it. When the cursor moves, the controller highlights that line, scrolls it into view if the user is not actively scrolling, and updates the variable table from the same step object. Because both views read from one record, they cannot drift apart.