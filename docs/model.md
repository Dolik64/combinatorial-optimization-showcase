# The integer linear model

[Project](../README.md) · [Walkthrough](demo.md) · [Verification](verification.md)

## Inputs and decisions

- Two 24-element demand vectors: one for weekdays, one for weekends.
- A nonnegative integer hourly shortage limit, D.
- A repeating 168-hour week, built from five weekday profiles followed by two weekend profiles.
- A fixed eight-hour shift duration.

Let q_h be demand at hour h. The variable x_h is the nonnegative integer number of shifts beginning at hour h. The nonnegative integer variable z_h bounds the absolute coverage error at that hour. The original implementation therefore creates **336 integer variables**.

## Coverage wraps around the week

Shifts beginning in the current hour and the preceding seven hours are active:

$$c_h = \sum_{k=0}^{7} x_{(h-k)\bmod 168}.$$

At Monday 00:00, this includes starts from Sunday 17:00 through 23:00 and Monday 00:00. A shift starting Monday 00:00 covers hours 0 through 7 and stops before hour 8.

## Objective and linearisation

The objective minimises total absolute deviation, measured in agent-hours:

$$\min \sum_{h=0}^{167} z_h.$$

For each hour, two inequalities represent the absolute value:

$$q_h-c_h \le z_h, \qquad c_h-q_h \le z_h.$$

Because z_h is minimised and has no other coupling, at an optimum it equals the absolute error. The original code also explicitly adds z_h >= 0, which is redundant with the variable's lower bound.

The service constraint caps shortage:

$$q_h-c_h \le D.$$

The source adds four constraints per hour, including the redundant nonnegativity constraint: **672 linear constraints** in total.

## Interpretation

D caps shortage in each individual hour. It does not cap excess coverage. Shortage and excess have equal unit weight in the objective, and coverage above demand can be necessary because a shift lasts eight consecutive hours.

The model chooses aggregate shift starts. It does not assign named employees, impose breaks or rest periods, limit weekly hours per person, or minimise wage costs. A shift count reported for a selected solution is descriptive: the solver does not apply a secondary objective to minimise that count.
