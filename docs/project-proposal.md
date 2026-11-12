# Project proposal

## 1. The problem and goal

(Explain the problem you want to solve and why it matters. Describe what you
want to improve, how you will measure improvement, and which limits or
requirements must be respected. A short example can help someone outside your
field understand the problem.)

**The problem and goal in my project:**

Controllers for dynamic systems are usually designed and tuned in simulation.
The simulations used today do not represent real conditions well, so a
controller that performs very well in simulation often behaves differently on
the physical system. The gap comes from effects the simulation omits or
simplifies, such as friction, environmental noise, temperature and nonlinear
dynamics.

The goal is an agent that builds a simulation environment representative of
reality. It does so using a physical platform, for example a wheeled robot. The
agent proposes experiments, writes the command code for the robot, learns from
the measured results, and corrects the simulation. It may also propose
additional sensors or data that would help. After every correction, the agent
repeats the experiment in simulation and checks whether the simulated data are
close to the measured data. It iterates until it reaches the closest match it
can find. If the match does not reach the required error, it can request
further experiments.

Improvement is measured by the closeness of simulated data to real measured
data. The requirement is that the error between the measured vector from an
experiment and the simulated vector of the same quantity is below 1%.

The following limits must be respected. The robot runs on a battery, which
limits the number of consecutive experiments. Human approval is required before
any experiment is run in the laboratory. Laboratory time is limited, for
example to one continuous hour.

As an extension, a controller is designed on the simulation twice: once
before the improvement and once after. Both are tested on the real system to
check whether the more realistic simulation yields a better controller.

## 2. The simulator

(Describe a program or model that could simulate your system and calculate what
happens under different conditions. Explain what inputs it takes and what
results it produces. State whether it can run on your computer and accept inputs
in a text file, such as JSON or YAML. If you have not chosen a simulator yet,
name an option you want to explore and explain what you need to check.)

**The simulator in my project:**

The simulation is a dynamic model of a wheeled robot, written in Python, whose
parameters and structure the agent can modify. The agent also uses Python to
write the commands sent to the robot. The physical side is a networked
laboratory equipped with optical cameras and robots carrying an inertial
measurement unit (IMU), with the option of adding other sensors. The robot is
controlled by an Arduino board programmed in C.

The Python code runs on my own computer and reads its inputs from text files:
experiments are described in YAML files, and JSON files carry data between
Python and the robot. The real experiments return measurements from the cameras
and the IMU, which the agent compares with the simulation output.

The software stack is not final. The options I want to explore are MuJoCo as
the physics engine for the simulation, PyTorch for learning the corrections to
the model, BoTorch for choosing new experiments, and PySerial for the
connection to the robot. What I need to check is whether MuJoCo can represent
the effects I need, such as ground friction and nonlinear dynamics, and
whether its results can be compared directly with the camera and IMU
measurements.

## 3. The agent's role

(Explain what the language-model-based agent will decide or change, and how it
will use the simulator's results to choose its next action. If known, list the
variables it may change and their allowed ranges. Explain what you expect the
language model to contribute: could a simple program that tries different
options and picks the best result achieve the same goal? Include one question
you still need to answer.)

**The agent in my project:**

The agent plans the laboratory experiments and sends the required commands to
the robot, in order to collect broad information for building the environment.
It does not only plan the experiment. It also reasons about how to improve the
next version of the simulation. It builds the simulation iteratively by trial
and error: it compares simulated and measured data, decides what to change in
the simulation, and runs it again to bring the simulated conditions closer to
reality.

The agent may change environmental noise, friction, temperature effects, and
the system dynamics, which need not be the linear model used today. The
allowed range for temperature is 10 to 30 degrees Celsius, a realistic range.
Ranges for the other variables are not yet defined.

A simple program that tries many options and keeps the best result is not
enough. Here many factors and large amounts of information must be considered
at the same time, over many iterations. Judgement is needed to decide which
direction to take the simulation at each iteration. The language model
provides this judgement: it chooses which experiment to run, decides how the
next simulation should be improved, which effect to add to the model, and when
to ask for more data. Every experiment still needs
human approval before it is run.

Open question: in what order should the agent address the effects, and what
should it start with? This priority still has to be defined.

## Explain your project to someone outside your field

(Now describe your whole project again, as if you were explaining it to someone
from another field who has no background in your topic. For example, if you
study chemical engineering, imagine explaining it to a physicist; if you study
aeronautical engineering, imagine explaining it to someone in medicine; if you
study data science, imagine explaining it to a materials engineer. In one or two
short paragraphs, connect all three parts: the problem you want to solve, how
the simulator helps you test ideas, and what the agent will decide or change.
Use everyday language, explain any technical terms you need, and avoid
unexplained abbreviations. Do not simply copy your answers above.)

**My project in simple words:**

Imagine a small robot driving on grass. A computer knows the robot's equations
of motion and its basic data, so it can simulate how fast the robot will go
when driving in a straight line. Now drive the real robot and measure its speed.
The two numbers will most likely differ, and that is the problem. Suppose you
want the robot to reach 1 km/h. You build a controller, a system that brings
the actual state to the desired one, using the simulation. On the real grass it
will not work as planned, because the simulation does not know the real
ground.

I want to build a simulation that predicts the robot's speed on grass without
being told it in advance, by learning from experiments. The agent, a program
driven by a language model, decides which experiments the robot performs and
what to change in the simulation. Simple textbook equations leave out effects
such as friction with the ground and with the air, so the agent must also
update the model itself. It does this gradually, comparing the simulation to
the real measurements after every change. Deciding what it should address
first is still an open point.
