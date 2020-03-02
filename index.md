

# Contraint Optimization with Google OR-Tools

Google Optimization Tools (a.k.a., OR-Tools) is an open-source, fast and portable software suite for solving combinatorial optimization problems. Google OR tools are essentially one of the most powerful tools introduced in the world of problem-solving.

The suite contains:

   1.   A Linear Programming solver (GLOP)
   2.   A Constraint Programming Solver (CP-SAT)
   3.   Wrappers around commercial and other open-source solvers, including mixed-integer solvers
   4.   Bin Packing and Knapsack Algorithms
   5.   Algorithms for the Traveling Salesman Problem and Vehicle Routing Problem
   6.   Graph algorithms (shortest paths, min-cost flow, max flow, linear sum assignment).

## Installation


The fastest way to get OR-Tools is to install the Python binary version. If you already have Python (version 3.5+ on Linux, or 3.6+ on Mac OS or Windows), and the Python package manager PIP, you can install OR-Tools as follows: 

```bash
   python -m pip install --upgrade --user ortools
```
For more please check out <a href='https://developers.google.com/optimization/install' target="_blank">Source Installation Instruction</a>

Now, let's begin 👍

<b>Optimization</b>, essentially, means finding the best solution to a problem out of a large set of possible solutions. We all have finite resources and time & we want to make most of them. Either it is the task of scheduling the order at which you will answer the emails or switching to a new route back home to minimize traffic woes, we surely know how important optimization is to data science. One of the main concerns of a data scientist is creating a model that fits the problem like finding optimal heuristics, minimum function losses, etc. Therefore it is crucial to understand optimization frameworks.

Here in this article, we will focus on understanding how to use OR-tools for  <b>Constraint Optimization</b>.


## Constraint Optimization
<b>Constraint optimization</b> or constraint programming (CP), is the name given to identifying feasible solutions out of a very large set of candidates, where the problem can be modeled in terms of arbitrary constraints. 

CP is based on feasibility (finding a feasible solution) rather than optimization (finding an optimal solution) and focuses on the constraints and variables rather than the objective function. In fact, a CP problem may not even have an objective function — the goal may simply be to narrow down a very large set of possible solutions to a more manageable subset by adding constraints to the problem.

The most popular problem in constraint optimization is  <b>Employee Scheduling</b> and also happens to be our use case.

<center><img src='https://46nuwl3acn7q25gnmb32k5qh-wpengine.netdna-ssl.com/wp-content/uploads/2018/01/memebetter.com-20180122190930.jpg' height=500></img></center>


Like Morpheus in Matrix was in pursuit of the <b>One</b> (Neo) his whole life, we at BOX8 too are in continuous pursuit to deliver orders quickly and efficiently to our customers. To do so we have an army of delivery boys, 
but now the question is how to distribute them in all our outlets? 
also, how do we distribute them throughout the day? 

To understand the complexity of the whole problem let's list what we would like to achieve:

1. More Delivery Boys during Peak Hours (if possible more skilled ones).
2. Sufficient Breaks between continuous shifts.
3. More shifts to skilled Delivery Boys 
4. Identifying which outlets need more Delivery Boys and which ones could do with less.
5. The minimum number of Delivery Boys to be always present in the outlet.

The above mentioned are just a few constraints we would like to put during roster generation, but doing so manually for the hundreds of Delivery Boys in over 140+ outlets is pretty much impossible.
<center><img src='https://drive.google.com/uc?export=view&id=1sa5BJXk57UMSDoCYztPIZfDDACLYwx1M' height='50%'></center>
This happens to be a perfect use case of OR-tools Constraint Optimization Solver <b>CP-SAT</b>.

The CP-SAT solver is technologically superior to the original CP solver and should be preferred in almost all situations. The exceptions are small problems for which solutions can be found quickly using either solver. In those cases you may find that the original CP solver outperforms CP-SAT.

Check out the <a href='https://developers.google.com/optimization/scheduling/employee_scheduling' target="_blank">Nurse Scheduling Problem</a> to get a rudimentary understanding of employee scheduling and CP-SAT.

Now you have some idea of the (N, D, S) problem. Now let's try to create our own constraint using OR-Tools

```python
from ortools.sat.python import cp_model
#define variables here like all_employees, all_days etc.

model = cp_model.CpModel()
for n in all_employees:
    for d in all_days:
        for s in all_shifts:
            shifts[(n, d,
                    s)] = self.model.NewBoolVar('shift_n%id%is%i' % (n, d, s))

def shift_constraint():
        #more than one employee on a particular day and a particular shift
        for d in all_days:
            for s in all_shifts:
                    model.Add(sum(shifts[(n, d, s)] for n in all_employees) > 1)

solver = model.CpSolver()
#process solver
```
Before we design a constraint we need an OR-Tools variable to operate on.
In the first block of the code we are defining an OR-Tools Boolvar shifts[(n,d,s)] where:

1. n -> employee index(0...N)
2. d -> days index(0...D)
3. s -> shift index(0...S)

For example: Let's say we want employee 1 on day 1 to do shift 2 => shift[(0,0,1)] = 1

In the second block, we are designing a basic shift constraint which states that for each shift in a particular day there should be more than 1 employee(Delivery Boy). So the mathematical equation for that will be:

<center><img src='https://drive.google.com/uc?export=view&id=1B_pTvo3cXg0Xd3MrF0wnrXXYDtf3FyLC' width='60%'></center>

This mathematical equation, when written programmatically in python, is something like - 
```python
    sum(shifts[(n, d, s)] for n in all_employees) > 1
```
Congrats you successfully created your first OR-Tools Constraint ! 👏

Using OR-Tools we were able to programmatically codify all the above mentioned constraints to create a <b>Roster Automation Service</b> .  This not only helps us generate rosters for upcoming weeks but also can be used as a simulation tool to evalute historical rosters that were created manually .