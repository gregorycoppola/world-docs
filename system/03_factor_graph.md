# Factor Graph Construction

## Overview

The factor graph is the runtime representation for inference. It's built from:
1. A query (what we want to know)
2. A knowledge base (facts and rules)
3. An entity inventory (what entities exist)

The graph has a specific bipartite structure that enables efficient belief propagation.

## Graph Structure