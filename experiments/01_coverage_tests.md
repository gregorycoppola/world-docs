# Coverage Tests

## Overview

The coverage test suite validates the inference engine across a range of reasoning patterns. Each test case provides:
- A knowledge base (facts and rules)
- A query
- An expected answer (yes/no/unknown)

The tests are organized by reasoning pattern, with 2-3 tests per category.

## Test Categories

### and_gates (3 tests)

Tests conjunctive reasoning — conclusions that require multiple premises.

**01_allies**: Two countries are allies if they both trust each other.

    trust(a, b) & trust(b, a) -> allies(a, b)

**02_one_sided**: One-sided trust doesn't create alliance.

**03_dating**: Dating requires mutual attraction.

    likes(x, y) & likes(y, x) -> dating(x, y)

### and_plus_depth (2 tests)

Tests conjunction combined with inference chains.

**01_ambitious**: Ambitious requires smart AND hardworking, which have their own preconditions.

**02_powerful**: Power requires wealth AND influence, each derived from other facts.

### causation (2 tests)

Tests causal chains.

**01_broke_window**: Ball hit window, window broke.

**02_domino**: Chain of dominoes falling.

### conditionals (2 tests)

Tests if-then reasoning.

**01_if_rains**: If it rains, ground is wet.

**02_missed_bus**: If missed bus, then late.

### contrapositive (1 test)

Tests backward reasoning through negation.

**01_not_mortal**: Zeus is not mortal, all men are mortal, therefore Zeus is not a man.

    man(x) -> mortal(x)
    not mortal(zeus)
    ? man(zeus)  # expected: no

### counting (1 test)

Tests reasoning about quantities.

**01_two_children**: Mary has two children.

### degree (1 test)

Tests gradable predicates.

**01_devoted**: Very loyal implies devoted.

### depth_chains (3 tests)

Tests transitive inference chains.

**01_socrates**: man -> mortal -> soul_exists (3 levels)

**02_poodle**: poodle -> dog -> mammal -> animal (4 levels)

**03_food_chain**: grass -> herbivore -> carnivore -> apex (4 levels)

### disjunction (2 tests)

Tests reasoning with "or".

**01_lonely_or_exciting**: Attraction from loneliness OR excitement.

**02_either_parent**: Has parent if has mother OR father.

### hypothetical (2 tests)

Tests counterfactual reasoning.

**01_if_rich**: If John were rich, he would buy a yacht.

**02_would_win**: If trained hard, would win.

### identity (2 tests)

Tests reasoning about identity/equality.

**01_clark_kent**: Clark Kent is Superman.

**02_capital**: Paris is the capital of France.

### modality (2 tests)

Tests modal reasoning (possibility, necessity).

**01_careful**: Being careful prevents accidents.

**02_umbrella**: Umbrella prevents getting wet.

### multiple_rules (2 tests)

Tests combining evidence from multiple rules.

**01_popular**: Popular if funny OR kind OR talented.

**02_guilty**: Guilty if motive AND opportunity AND evidence.

### negation (1 test)

Tests direct negation handling.

**01_not_mortal**: Gods are not mortal.

### possessives (2 tests)

Tests possessive relationships.

**01_marys_dog**: Mary's dog is named Fido.

**02_kings_army**: The king's army is powerful.

### quantifier_scope (2 tests)

Tests quantifier interactions.

**01_everyone_someone**: Everyone loves someone.

**02_no_one_likes**: No one likes the tax collector.

### relative_clauses (2 tests)

Tests embedded clauses.

**01_who_loves**: The man who loves Mary is John.

**02_that_barks**: The dog that barks is loud.

### roles (2 tests)

Tests semantic role handling.

**01_giving**: John gave Mary a book (agent, recipient, theme).

**02_sending**: John sent a letter to Mary (agent, theme, goal).

### sets (2 tests)

Tests reasoning about collections.

**01_both**: Both John and Mary are students.

**02_team**: The team has five members.

### spatial (2 tests)

Tests spatial reasoning.

**01_in_box**: The ball is in the box.

**02_north_of**: Paris is north of Madrid.

### symmetric (2 tests)

Tests symmetric relations.

**01_married**: If A married B, then B married A.

**02_neighbors**: If A is neighbor of B, then B is neighbor of A.

### time (2 tests)

Tests temporal reasoning.

**01_trust_now**: Countries that usually cooperate now trust each other.

**02_allies_now**: Current allies based on current trust.

### transitivity (2 tests)

Tests transitive relations.

**01_taller**: A taller than B, B taller than C, therefore A taller than C.

**02_ancestor**: Parent of parent is ancestor.

## Test File Structure

Each test has these files:

    category/test_name.document    # English prose (optional)
    category/test_name.lexicon     # Predicate declarations
    category/test_name.facts       # Logical facts and rules
    category/test_name.query       # The query to answer
    category/test_name.expected    # yes / no / unknown
    category/test_name.question    # Natural language question
    category/test_name.description # What reasoning pattern this tests

## Running Tests

Verify a single test:

    lever pipeline verify coverage/contrapositive/01_not_mortal

Verify all tests:

    lever pipeline verify-all coverage/

Verbose output:

    lever pipeline verify coverage/contrapositive/01_not_mortal -v