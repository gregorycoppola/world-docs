# Future Work: Scaling

## Current Limitations

The current implementation handles:
- ~100 entities
- ~50 rules
- ~1000 grounded propositions

For real-world applications, we need:
- Millions of entities
- Thousands of rules
- Efficient inference over large graphs

## Scaling Challenges

### Grounding Explosion

A rule with k variables over n entities produces O(n^k) groundings:

    [x:e, y:e]: loves(x, y) -> happy(x)
    
    For 1000 entities: 1,000,000 groundings

### Graph Size

The factor graph grows with:
- Number of propositions (entities × predicates)
- Number of groups (groundings × rules)
- Number of factors (proportional to groups)

### Inference Cost

Each BP iteration visits all factors:

    O(iterations × factors × 2^max_premises)

## Scaling Strategies

### Lazy Grounding

Only ground rules when needed:

1. Start with query
2. Expand relevant propositions
3. Ground only rules that touch active propositions
4. Stop when no new evidence can flow

**Benefit**: Most rules never grounded for typical queries.

### Lifted Inference

Exploit symmetry — identical entities can be grouped:

    If we don't know anything specific about john vs. mary,
    treat "person" as a single lifted variable.

**Methods**:
- Lifted BP
- First-order variable elimination
- Counting formulas

**Challenge**: Breaks down when entities have different evidence.

### Approximate Inference

Trade accuracy for speed:

**Sampling**: Monte Carlo estimates instead of exact BP.

**Truncation**: Limit graph expansion depth.

**Pruning**: Remove low-probability branches.

### Sparse Representations

Most propositions are false or unknown:

    P(loves(john, mary)) = 0.001 for most pairs

Store only non-default values:

    Sparse: {(john, mary): 0.8, (mary, john): 0.6}
    vs
    Dense: 1,000,000 entries mostly 0.001

### Batch Processing

Process multiple queries together:

    Queries: mortal(socrates)?, mortal(plato)?, mortal(aristotle)?
    
    Shared: Ground man->mortal once
    Combine: Single BP pass answers all three

### Caching

Cache intermediate results:

    If we computed P(mortal(socrates)) before,
    reuse unless evidence changed.

**Invalidation**: Track dependencies, invalidate on updates.

## Database Integration

For large KBs, integrate with a database:

### Storage

    Entities table: id, type, properties
    Facts table: predicate, arg1, arg2, ...
    Rules table: premises, conclusion, weight

### Query Planning

    1. Parse query
    2. Identify relevant rules (SQL join)
    3. Fetch relevant facts (SQL select)
    4. Build factor graph in memory
    5. Run BP
    6. Return result

### Indexing

Index by:
- Predicate name
- Argument values
- Rule conclusions

Enables fast lookup of relevant rules/facts.

## Distributed Inference

For very large graphs, distribute across machines:

### Graph Partitioning

Split graph into subgraphs:
- Minimize edges between partitions
- Each machine handles one partition
- Synchronize at boundaries

### Message Passing Protocol

    1. Local BP within partition
    2. Exchange boundary messages
    3. Repeat until global convergence

### Challenges

- Communication overhead
- Load balancing
- Fault tolerance

## Benchmarks Needed

To guide optimization, we need benchmarks:

### Small (Current)

- 10-100 entities
- 10-50 rules
- Milliseconds per query

### Medium (Target)

- 1,000-10,000 entities
- 100-500 rules
- Seconds per query

### Large (Aspirational)

- 1,000,000+ entities
- 1,000+ rules
- Subsecond for simple queries
- Minutes for complex queries

## Practical Path Forward

### Phase 1: Lazy Grounding (Implemented)

Already doing query-driven expansion.

### Phase 2: Sparse Representations

Store only non-trivial beliefs.

### Phase 3: Database Backend

Move KB to SQLite/Postgres.

### Phase 4: Caching Layer

Cache computed beliefs.

### Phase 5: Lifted Inference (Research)

Exploit symmetry where possible.