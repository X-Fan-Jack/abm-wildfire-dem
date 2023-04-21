# abm-wildfire-dem

A wildfire spread model based on DEM and ABM.      
This is the assignment for the UCL CASA module Agent-based Modeling(CASA0011)


# 1. Research Question

How does the elevation difference between neighboring patches affect the burning progress in a fire propagation model?

# 2. ODD Description

## 2.1 Purpose and patterns

The system is a simulation system based on agent-based cellular automaton, which is built upon an improved forest fire spread model proposed(Feifei and Xinlu, 2012). Its purpose is to explore the impact of changes in altitude on the spread pattern of forest fires under real topographic conditions.

The system is written in Python and implemented function using libraries such as Mesa and visualized using web pages.

## 2.2 Entities, state variables, and scales

The model includes the following entities: An agents set representing the burning area, a model representing the entire fire range.

The variables that entities have are as follows:

Table 1 Variables of agents

| **Variable name** | **Variable type** | **Meaning** |
| --- | --- | --- |
| unique\_id | int | The unique id of each agent. |
| pos | tuple | The position (x, y) of the agent. |
| grid | object | The grid where agent stay.An instance of mesa.space.SingleGrid() |
| elevation | double | The elevation of the agent. Define by the DEM. |
| state\_t0 | double | The agent state in T |
| state\_t1 | double | The agent state in T+1 |
| affected | bool | The agent is affected by its neighbor or not.Default in False. |
| isBoundary | bool | The agent is at boundary or not. Default in False. |
| extinguished | bool | The agent is extinguished or not. Default in False. |

Table 2 Variables of agents

| **Variable name** | **Variable type** | **Meaning** |
| --- | --- | --- |
| fire\_x | double | The x coordinate of the fire start. |
| fire\_y | double | The y coordinate of the fire start. |
| height | int | The height of the grid. Define by the size of DEM. |
| width | int | The width of the grid. Define by the size of DEM. |
| grid | object | The grid where agent stay.An instance of mesa.space.SingleGrid() |
| ini\_id | int | The identify id for each new agent. |
| schedule | object | Objects for handling the time component of a model.An instance of mesa.time.SimultaneousActivation() |

The spatial scale of the system is based on the size of the DEM space entered by the user. It can be cropped as needed, and thus a small portion of the area can be obtained for study.

The time scale of the system uses continuous time. It is controlled by mesa.time.SimultaneousActivation(), which allows us to perform a two-step operation, in one time period.

## 2.3 Process overview and scheduling

_**Process:**_ The model was developed to simulate the entire life cycle of the flame spread process.

The life cycle of each area consists of four processes: 1. unburned: this is the initial state, the area is not burning. 2. burning: receive the influence of the surrounding area, causing itself to begin to burn. 3. complete combustion: the area burned to a complete state and began to make the surrounding area burn. 4. extinguished state: due to its own combustible material limitations cannot burn for a long time, and the surrounding area have been reached the complete combustion or extinguished state, the area turned to the extinguished state.

At each time step, the agent updates its own state in two steps: In the first step, it first calculates its state at the moment T+1 based on the state at the moment T. In the second part, the state at the moment T is updated to the value at the moment T+1. The purpose of this is to prevent that in one update, the neighboring agents may use the value at the moment T+1 to participate in the calculation at the moment T+1, which in turn leads to an update error.

_**Schedule:**_ The simulation runs based on a single step time span parameter (_dt_) entered by the user.

## 2.4 Design concepts

The model attempts to explore the spread of wildfires as influenced by elevation. The development of wildfires is influenced by a variety of factors, such as wind, topography, type of combustible material, air humidity, and other factors. Many studies have tried to explore the influence of different factors on wildfire spread from different perspectives. This system aims to explore the effect of elevation change on the spread of wildfires. By simulating and predicting the process of fire propagation, the system can provide information on fire spread situations for emergency management agencies and firefighting personnel, helping them make better emergency decisions, allocate resources rationally, and improve disaster response effectiveness.

## 2.5 Initialization

At the beginning of the model initialization, the model creates a time queue and then creates a MESA grid of corresponding size based on the DEM data and creates and places an agent in each MESA grid. The coordinates of each agent correspond to the coordinates in the grid and the elevation is the same as the relative position in the DEM, and the burning state of each agent is also initialized to unburned and unaffected. The model adds the agent to the time queue after the agent creation is complete. Finally, the model updates the burning state of the agent at the correct location to the fully burned state based on the user input of the location of the fire point. At this point, the model is initialized.

## 2.6 Input data

Users need to input the following variables in the Variable initialization section of Python notebook to initialize the model.

Table 3 Input data

| **Variable name** | **Variable type** | **Meaning** |
| --- | --- | --- |
| dem\_file | String | Local file path of DEM in research area. |
| T | double | Average temperature (centigrade) |
| h | double | Relative humidity (percentage) |
| fire\_start\_pos | tuple | The start position of fire. |

# 3. Brief Methodology

The model is based on Moore-type neighborhoods and defines the state of the cell at moment as:

$$State_{i,j}^t=\frac{Burned\ area\ of\ cell\left(i,j\right)}{Total\ area\ of\ cell\left(i,j\right)}$$

Based on the study of Zhang et al. the rate of flame spread was defined as:

$$R=R_0K_SK_WK_\varphi=R_0K_SK_W\cdot e^{3.533\cdot\left(tan\varphi\right)^{1.2}}$$

Where is the natural spread rate of wildfire without considering the effect of wind speed and terrain; is the combustible material factor; is the wind speed adjustment factor; is the terrain slope adjustment factor. Since this model only considers terrain factors, , are defined as constants.

Thus, the state of the cell at time will be defined as:

$$State_{i,j}^{t+1}=f\left(State_{i,j}^t,R_{i-1,j-1}^t,R_{i-1,j}^t,R_{i-1,j+1}^t,R_{i,j-1}^t,R_{i,j+1}^t,R_{i+1,j-1}^t,R_{i+1,j=1}^t,R_{i+1,j+1}^t\right)$$

All agents will have their status updated based on the above algorithm.

# Bibliography

Feifei Z. and Xinlu X. (2012). 'An Improved Forest Fire Spread Model and Its Realization'. _GEOMATICS_＆_SPATIAL Information Technology_.
