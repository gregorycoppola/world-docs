# Application 3: Planning

## The Problem

Yann LeCun has argued that LLMs cannot plan because they lack the ability to simulate future states. Planning requires:

1. **State representation**: Knowing what state the world is in
2. **Action modeling**: Knowing how actions change states
3. **Goal specification**: Knowing what state we want to reach
4. **Search**: Finding a sequence of actions to reach the goal

LLMs generate text token by token. They don't model states, actions, or goals explicitly. When they appear to plan, they're pattern-matching from examples in training data.

## Planning as Backward Inference

The key insight: **planning is backward inference over temporal propositions**.

Consider a simple planning problem: