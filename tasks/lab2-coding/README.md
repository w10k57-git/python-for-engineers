# Laboratory 2: Beam Analysis - Programming Tasks

## Introduction

Welcome to your first programming laboratory! In this lab, you will write Python functions to solve common beam analysis problems. These are real engineering calculations that you'll use throughout your career. Those functions are very useful from the point of view of building the engineering toolbox for Large Language Models. 

---

## Task Group 1: Beam Analysis

In this group of tasks, you will analyze beams under different loading conditions, calculating reaction forces, maximum bending moments, and maximum shear forces.

### Data Class Definition

For Tasks 1.2, 1.3, and 1.4, you will use the following dataclass to return results. Add this at the top of your Python file:

```python
from dataclasses import dataclass

@dataclass
class BeamResults:
    """Results from beam analysis"""
    R1: float      # Reaction force at left support [N]
    R2: float      # Reaction force at right support [N]
    M_max: float   # Maximum bending moment [N·mm]
    V_max: float   # Maximum shear force [N]
```

---

### Task 1.1: Three-Point Bending - Symmetric Load (Simple Calculation)

A simply supported beam with span `L` has a single point load `F` applied at the center of the beam (symmetric loading). Write a function `max_moment_3p_symmetric(F, L)` that calculates the maximum bending moment in the beam.

**Parameters:**
- `F` - applied force [N]
- `L` - span length (distance between supports) [mm]

**Returns:**
- Maximum bending moment [N·mm] (just a single number)

**Hints:**
- Since the load is centered, the reaction forces are equal: R1 = R2 = F/2
- The maximum moment occurs at the center where the load is applied
- Use the formula for bending moment at the center of a simply supported beam

**Example:**
```python
# For F = 1000 N, L = 1000 mm
M_max = max_moment_3p_symmetric(1000, 1000)
# Expected result: M_max = 250000 N·mm
print(f"Maximum moment: {M_max} N·mm")
```

---

### Task 1.2: Three-Point Bending - Symmetric Load (Full Analysis)

Same setup as Task 1.1: a simply supported beam with span `L` and a single point load `F` at the center. Write a function `analyze_3p_symmetric(F, L)` that performs a complete beam analysis and returns a `BeamResults` object containing all reaction forces and maximum values.

**Parameters:**
- `F` - applied force [N]
- `L` - span length (distance between supports) [mm]

**Returns:**
- `BeamResults` object containing:
  - `R1` - reaction at left support [N]
  - `R2` - reaction at right support [N]
  - `M_max` - maximum bending moment [N·mm]
  - `V_max` - maximum shear force [N]

**Hints:**
- Calculate the reaction forces using equilibrium equations
- The maximum shear force is equal to the larger reaction force
- For symmetric loading, R1 = R2, so V_max = F/2

**Example:**
```python
# For F = 1000 N, L = 1000 mm
result = analyze_3p_symmetric(1000, 1000)
print(f"R1 = {result.R1} N")
print(f"R2 = {result.R2} N")
print(f"M_max = {result.M_max} N·mm")
print(f"V_max = {result.V_max} N")
# Expected: R1 = 500 N, R2 = 500 N, M_max = 250000 N·mm, V_max = 500 N
```

---

### Task 1.3: Three-Point Bending - General Case (3P General)

A simply supported beam with span `L` has a single point load `F`. The load can be positioned anywhere along the beam using an `offset` parameter that shifts the load from the center position. Write a function `analyze_3p(F, L, offset=0)` that performs a complete beam analysis and returns a `BeamResults` object.

**Parameters:**
- `F` - applied force [N]
- `L` - span length (distance between supports) [mm]
- `offset` - distance from center of beam to the load [mm] (default: 0)
  - `offset = 0`: load at center (symmetric)
  - `offset > 0`: load shifted toward right support
  - `offset < 0`: load shifted toward left support

**Returns:**
- `BeamResults` object containing:
  - `R1` - reaction at left support [N]
  - `R2` - reaction at right support [N]
  - `M_max` - maximum bending moment [N·mm]
  - `V_max` - maximum shear force [N]

**Important - Input Validation:**
Your function must validate the inputs before performing calculations:
- Check that `F > 0` (force must be positive)
- Check that `L > 0` (span must be positive)
- Calculate the load position: `a = L/2 + offset`
- Check that `0 < a < L` (load must be between the supports)
- If any validation fails, raise a `ValueError` with a descriptive error message

**Hints:**
- First validate all inputs
- Calculate load position: `a = L/2 + offset`
- Calculate the reaction forces R1 and R2 using equilibrium equations (sum of forces = 0, sum of moments = 0)
- The maximum moment occurs at the point of load application
- The maximum shear force is the larger of R1 and R2

**Example:**
```python
# Symmetric case (offset = 0)
result = analyze_3p(1000, 1000)
print(f"R1 = {result.R1} N, R2 = {result.R2} N")
# Expected: R1 = 500 N, R2 = 500 N

# Asymmetric case (offset = -100 mm, load shifted left from center)
result = analyze_3p(1000, 1000, offset=-100)
print(f"R1 = {result.R1} N")
print(f"R2 = {result.R2} N")
print(f"M_max = {result.M_max} N·mm")
print(f"V_max = {result.V_max} N")
# Expected: R1 = 600 N, R2 = 400 N, M_max = 240000 N·mm, V_max = 600 N

# Invalid input should raise an error
try:
    result = analyze_3p(1000, 1000, offset=600)  # Load outside beam!
except ValueError as e:
    print(f"Error: {e}")
```

---

### Task 1.4: Four-Point Bending - General Case (4P General)

A simply supported beam with span `L` has two equal point loads `F`. The loads are separated by a distance `spacing` and can be shifted along the beam using an `offset` parameter. By default (offset = 0), the loads are positioned symmetrically about the beam's center. Write a function `analyze_4p(F, L, spacing, offset=0)` that performs a complete beam analysis and returns a `BeamResults` object.

**Parameters:**
- `F` - applied force (each load) [N]
- `L` - span length (distance between supports) [mm]
- `spacing` - distance between the two loads [mm]
- `offset` - distance from center of beam to center of load pair [mm] (default: 0)
  - `offset = 0`: loads symmetric about beam center
  - `offset > 0`: load pair shifted toward right support
  - `offset < 0`: load pair shifted toward left support

**Returns:**
- `BeamResults` object containing:
  - `R1` - reaction at left support [N]
  - `R2` - reaction at right support [N]
  - `M_max` - maximum bending moment [N·mm]
  - `V_max` - maximum shear force [N]

**Important - Input Validation:**
Your function must validate the inputs before performing calculations:
- Check that `F > 0` (force must be positive)
- Check that `L > 0` (span must be positive)
- Check that `spacing > 0` (loads must be separated)
- Calculate load positions:
  - `a = L/2 - spacing/2 + offset` (first load)
  - `b = L/2 + spacing/2 + offset` (second load)
- Check that `0 < a < b < L` (both loads must be between supports)
- If any validation fails, raise a `ValueError` with a descriptive error message

**Hints:**
- First validate all inputs
- Calculate both load positions from the spacing and offset
- In 4-point bending with equal loads positioned symmetrically, the reactions are equal: R1 = R2 = F
- The maximum moment occurs in the region between the two loads (constant in that region)
- The maximum shear force equals the reaction forces

**Example:**
```python
# Symmetric case (offset = 0, spacing = 600 mm)
result = analyze_4p(1000, 1200, 600)
print(f"R1 = {result.R1} N, R2 = {result.R2} N")
print(f"M_max = {result.M_max} N·mm")
# Expected: R1 = 1000 N, R2 = 1000 N, M_max = 300000 N·mm

# Asymmetric case (offset = 100 mm, loads shifted right)
result = analyze_4p(1000, 1200, 600, offset=100)
print(f"R1 = {result.R1} N")
print(f"R2 = {result.R2} N")
print(f"M_max = {result.M_max} N·mm")
print(f"V_max = {result.V_max} N")

# Invalid input should raise an error
try:
    result = analyze_4p(1000, 1200, 1000)  # Spacing too large!
except ValueError as e:
    print(f"Error: {e}")
```

---

## Testing Your Functions

Create a Python file (e.g., `beam_analysis.py`) and implement all four functions. Test them with the provided examples to verify your calculations.

You can test your functions like this:

```python
# Test Task 1.1 - Simple calculation
M_max = max_moment_3p_symmetric(1000, 1000)
print(f"Task 1.1 - Max Moment: {M_max} N·mm\n")

# Test Task 1.2 - Full analysis (symmetric)
result = analyze_3p_symmetric(1000, 1000)
print(f"Task 1.2:")
print(f"  R1 = {result.R1} N, R2 = {result.R2} N")
print(f"  M_max = {result.M_max} N·mm, V_max = {result.V_max} N\n")

# Test Task 1.3 - 3P with offset
result = analyze_3p(1000, 1000)  # Default offset=0
print(f"Task 1.3 (symmetric):")
print(f"  R1 = {result.R1} N, R2 = {result.R2} N")
print(f"  M_max = {result.M_max} N·mm, V_max = {result.V_max} N")

result = analyze_3p(1000, 1000, offset=-100)  # Offset to the left
print(f"Task 1.3 (asymmetric):")
print(f"  R1 = {result.R1} N, R2 = {result.R2} N")
print(f"  M_max = {result.M_max} N·mm, V_max = {result.V_max} N\n")

# Test Task 1.4 - 4P with spacing and offset
result = analyze_4p(1000, 1200, 600)  # Default offset=0
print(f"Task 1.4 (symmetric):")
print(f"  R1 = {result.R1} N, R2 = {result.R2} N")
print(f"  M_max = {result.M_max} N·mm, V_max = {result.V_max} N")
```

---
**Good luck!** 🔧


TA1: Calculate reaction forces in each of the supports.
TA2: Calculate the maximum bending moment in the beam.
TA3: Calculate the maximum deflection of the beam.
TA4: Calculate the maximum bending stress.
TA5: Calculate the maximum equivalent Huber-Mises-Hencky stress.
TA6: Design a cross-section of the beam to satisfy the strength requirement regarding bending stress.
Ensure optimum selection of the cross-section.
TG1: Calculate the torque that must be applied to the screw to overcome the axial force of 3 kN
present in the power screw mechanism. The thread is Tr16x2, d2 = 14.7mm, and the friction coefficient
between the nut and the screw is 0.1.
TG2: A gearbox receives an input at 1500 rev/min clockwise and delivers an output at 300 rev/min
counterclockwise. The input power is 20 kW, and the mechanical efficiency of the gearbox is 70%.
Calculate the output torque of the system.