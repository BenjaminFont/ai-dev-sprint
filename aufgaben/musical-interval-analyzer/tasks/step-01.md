# Step 01: White Keys, Positive Intervals

## Goal

Calculate the interval between two white piano keys. The result is always positive.
Assume C is the lowest key.

## Rules

- Input: two note names (white keys only), e.g. "C", "E"
- Output: the interval as a number (double), e.g. 2.0
- Only white keys: C, D, E, F, G, A, B
- Result is always positive (first note is always lower or equal)
- Adjacent white keys are NOT always 0.5 apart -- remember black keys exist between some white keys

## Examples

- C, C -> 0
- C, D -> 1.0
- C, E -> 2.0
- C, F -> 2.5
- D, G -> 2.5
- C, B -> 5.5

## When done

Update `CURRENT_STEP.md` to point to step 02.
