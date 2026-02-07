# Planning

## The Problem

Planning requires working backward from a goal to find actions that achieve it. LLMs generate forward — they produce sequences without verifying goal satisfaction.

### LLM Planning Failures

**Missing preconditions**:

    Goal: Make coffee
    LLM: "Pour water into coffee maker, add grounds, press start"
    Missing: Check if coffee maker is plugged in, has filter, etc.

**Unsatisfied goals**:

    Goal: Get from A to C
    Available: A→B, B→C, A→D
    LLM: Might suggest A→D (doesn't reach C)

**Suboptimal plans**:

    Goal: Minimize cost
    LLM: Generates plausible plan without cost optimization

### Why LLMs Fail at Planning

Planning requires:

1. **Goal representation**: What state do we want?
2. **Backward chaining**: What actions achieve the goal?
3. **Precondition checking**: Are action preconditions met?
4. **Search**: Find valid action sequence

LLMs do none of these explicitly.

## The QBBN Solution

The key insight: **planning is backward inference over temporal propositions**.

Consider a simple planning problem:

    Initial state: at(robot, room_a)
    Goal: at(robot, room_b)
    Actions: move(from, to) achieves at(robot, to) if at(robot, from)

### Encoding as Rules

    # Action effects
    always [x:loc, y:loc]: at(robot, x) & move(x, y) -> at_next(robot, y)
    
    # Frame axiom (things stay unless moved)
    always [x:loc]: at(robot, x) & not moved_from(x) -> at_next(robot, x)
    
    # Goal
    at_goal(robot, room_b)

### Backward Inference for Planning

    Query: What must be true for at(robot, room_b)?
    
    Backward from goal:
      at(robot, room_b) requires:
        - at(robot, room_a) & move(room_a, room_b)  OR
        - at(robot, room_b) already
    
    Given at(robot, room_a):
      Plan: move(room_a, room_b)

The λ messages propagate backward from the goal, identifying what must be true.

### Multi-Step Planning

    Initial: at(robot, a)
    Goal: at(robot, c)
    Actions: can move a→b, b→c
    
    Backward propagation:
      at(robot, c) requires move(b, c) & at(robot, b)
      at(robot, b) requires move(a, b) & at(robot, a)
      at(robot, a) is given
    
    Plan: move(a, b), move(b, c)

### Precondition Verification

QBBN automatically checks preconditions:

    Rule: move(x, y) requires door_open(x, y)
    
    If door_open(a, b) = False:
      P(move(a, b)) = 0
      Plan fails, need to open door first

## Comparison

| Aspect | LLM | QBBN |
|--------|-----|------|
| Direction | Forward generation | Backward inference |
| Goal checking | Implicit/none | Explicit |
| Preconditions | Often missed | Verified |
| Optimality | No guarantee | Can optimize |
| Explanation | None | Full derivation |

## Example: Travel Planning

    KB:
      at(me, nyc)
      goal: at(me, paris)
      
      flight(from, to) & at(x, from) -> at_after_flight(x, to)
      has_passport(x) & has_ticket(x, f) -> can_take_flight(x, f)
      
      has_passport(me)
      available_flight(nyc, paris, flight_123)
    
    Query: How to reach at(me, paris)?
    
    Backward:
      at(me, paris) requires flight(nyc, paris) & at(me, nyc)
      flight requires can_take_flight(me, flight_123)
      can_take_flight requires has_passport(me) & has_ticket(me, flight_123)
      has_passport(me) = True
      has_ticket(me, flight_123) = False
    
    Result: Need to acquire ticket for flight_123

## Temporal Reasoning

QBBN can handle temporal propositions:

    at(robot, room_a, t=0)
    move(room_a, room_b, t=0) -> at(robot, room_b, t=1)
    move(room_b, room_c, t=1) -> at(robot, room_c, t=2)

Time steps become part of the grounding, allowing explicit temporal inference.

## Limitations

QBBN planning is not a full planner:

1. **No search optimization**: Doesn't minimize plan length
2. **No numeric constraints**: Can't handle continuous resources
3. **Grounding explosion**: Many time steps = many propositions

For complex planning, use QBBN for verification, not generation.

## Key Insight

Planning is **goal-directed backward inference**. QBBN's λ messages naturally support this, identifying what must be true for the goal to hold.