# STQL \(StackState Query Language\)

The built-in StackState Query Language \(STQL\) can be used to run advanced queries in the StackState Topology perspective and Analytics environments.

* **Topology:** Use STQL to build [advanced topology filters](/use/perspectives/topology-perspective/) that zoom in on a specific area of your topology or highlight components and their root cause.
* **Analytics:** Combine STQL with [scripting](/develop/scripting/) to create powerful queries that access the entire 4T data model.

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

**Examples**

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
| `components` | all | \(componentFilter\) | The component\(s\) for which the neighbors will be returned, see [Component filters](/reference/stql-reference#component-filters). |
| `levels` | 1 | all, \[1:14\] | The number of levels to include in the output. Use **all** to display all available levels \(maximum 15\) |
| `direction` | both | up, down, both | **up -** only components that depend on the named component\(s\) will be added  **down -** only dependencies of the named component\(s\) will be added  **both -** components that depend on and dependencies of the named component\(s\) will be added. |

### Examples

```text
# Select all components in the application layer that have
# a healthstate of either "CRITICAL" or "DEVIATING".
# Also include components with names "appA" or "appB" and their neighbors.

layer = "application"
  AND (healthstate = "CRITICAL" OR healthstate = "DEVIATING")
  OR withNeighborsOf(components = (name in ("appA","appB")))
```

## Function: withCauseOf `DEPRECATED`

This functionality is deprecated. It has been replaced by the **Root Cause Analysis** section in the visulaizer. The construct will be parsed, but will not produce any additional components.
