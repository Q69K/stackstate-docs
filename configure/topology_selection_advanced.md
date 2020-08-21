---
title: Advanced topology selection
kind: Documentation
aliases:
  - /usage/topology_selection_advanced/
listorder: 2
---

# Advanced topology queries with STQL

The full topology available in StackState most likely includes much more detail than you will require at any given time. As well as a providing a number of basic filters, StackState allows you to create your own advanced topology filters to locate  and zoom in on the specific part of the topology you are interested in.

Advanced topology selection gives you full control over the topology displayed. You can access the Advanced Filter screen by clicking _Advanced_ on the basic filter panel. Filters entered in the advanced filter bar should be written using the in-built StackState Query Language (STQL).

![Advanced filter example](../.gitbook/assets/query_advanced_with_neighbours.png)

# STQL \(StackState Query Language\)

## Overview

The built-in StackState Query Language \(STQL\) can be used to run advanced queries in the StackState Topology and Analytics environments.

* **Topology:** Use STQL to build [advanced topology filters](/) that zoom in on specific areas of your topology or highlight components and their root cause.
* **Analytics:** Combine STQL with [scripting](../develop/scripting/) to create powerful queries that access the entire 4T data model.

STQL queries consist of component filters and STQL functions. The query output is a component, or set of components, from the complete topology.

## Component filters

Component filters are used in two ways in STQL:

* Define the set of components to be included in the query output.
* Specify the set of components to be handled by an in-built STQL function.

The filters described below can be combined using boolean operators to achieve complex selections of components. Note that boolean operators will be executed in the standard order: NOT, OR, AND. You can change the order of operations by grouping sections of a query with parentheses \(...\).

### Filters

| Filter | Default | Allowed values | Description |
| :--- | :--- | :--- | :--- |
| `domain` | all | ... | ... |
| `environment` | all | ... | ... |
| `healthstate` | all | DEVIATING, CRITICAL | Components with the named healthstate |
| `label` | all | ... | Components with the named labels |
| `layer` | all | ... | Components in the named layer |
| `name` | all | ... | ... |
| `type` | all | ... | ... |

#### Examples

```text
# Select all components with name "serviceB"
name = "serviceB"

# Select all components in the "application" layer:
layer = "application"

# Select all components named either "appA" or "appB" that do not have a label "bck"
name in ("appA","appB") NOT label = "bck"
```

## Function: withNeighborsOf

The function **withNeighborsOf** extends STQL query output, adding connected components in the specified direction\(s\). The number of topology levels included can be adjusted up to a maximum of 15.

### Usage

```text
withNeighborsOf(components=(), levels=, direction-)
```

### Paramaeters

| Filter | Default | Allowed values | Description |
| :--- | :--- | :--- | :--- |
| `components` | all | (componentFilter) | The component\(s\) for which the neighbors will be returned, see [Component filters](test_ref.md). |
| `levels` | 1 | all, \[1:14\] | The number of levels to include in the output. Use **all** to display all available levels \(maximum 15\) |
| `direction` | both | up, down, both | **up -** only components that depend on the named component\(s\) will be added <br>**down -** only dependencies of the named component\(s\) will be added <br>**both -** components that depend on and dependencies of the named component\(s\) will be added. |

### Examples

```
# Select all components in the application layer that have
# a healthstate of either "CRITICAL" or "DEVIATING".
# Also include components with names "appA" or "appB" and their neighbors.

layer = "application"
  AND (healthstate = "CRITICAL" OR healthstate = "DEVIATING")
  OR withNeighborsOf(components = (name in ("appA","appB")))
```

## Function: withCauseOf `DEPRECATED`

This functionality is deprecated. It has been replaced by the **Root Cause Analysis** section in the visulaizer. The construct will be parsed, but will not produce any additional components.
