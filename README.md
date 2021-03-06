
| Code initiated on 20 November 2020 | Restructured API on 27 September 2021 | Quickstart at bottom | Contact: ThomasCarstens |
| -- | -- | -- | -- |

As part of the testbed for service drones, we offer a library of **well-known swarm behaviors**. This is the first collection of ‘swarm patterns’ known to-date on this swarm framework. The library is built with ROS 1 upon the Crazyswarm open-source project. 

1. Encapsulating multi-step tasks.

    *Multi-step tasks encapsulate the full swarm stack, from single-robot commands to dynamic management.*

2. Encapsulating swarm instructions.

    *Dynamic swarm tasks are encapsulated within the multi-step tasks.*

3. Encapsulating individual task execution.

    *Robot-specific instructions are encapsulated within the swarm instructions.*




# multi-step applications
Multi-step applications bring together the tasks defined in lower levels.

| TO-AND-FRO LOOP | SWARM DEMO | DRONES & MIXED REALITY DEMO |
|-- | -- | -- |
|![Application: to and fro](/full_applications/to_and_fro.png "To and Fro") | ![Application: octogon](/full_applications/swarm_demo.png "Octogon") | ![Application: octogon](/full_applications/trajectory_diagram.png "Octogon") |
| smlib.oneDrone_ToAndFro (id: _int32 drone reference_) | smlib.execOctogonAndTrajOnCollision (ids: _int32[] drone references_) | |
| # move to position 1<br># move to position 2<br># and loop<br># land if abort | # position drone 1<br># position drone 2<br># concurrent figures of eight<br># 3 drone octogon<br># generalized landing<br># if Unity3D flag appears:<br># if so, concurrent helis | |

> More Examples: https://github.com/ThomasCarstens/Service_Drones_Thesis/blob/main/sm_structs.py

<!-- 
## Usage
Each state machine can be executed on a separate thread.
    sm0 = smlib.execOctogonAndTrajOnCollision (ids = [1, 2, 4])
    smlib.start_sm_on_thread(sm0)

## Development
A state machine like the one above would be composed of different States. For instance:

    smach.StateMachine.add('FIG8_EXECUTE', fig8_sm, transitions={'succeeded' : 'land_all', 
                                                                'aborted' : 'land_all', 
                                                            'preempted' : 'land_all'}) 
    #the smach library is a procedural task-based framework for python
    # in this case, fig8_sm is a swarm instruction. This is covered in the next section.

> SMACH DOCS TO READ -->

# swarm instructions

| MOVE DRONE TO POINT | SETUP TOPIC MONITOR | LAND GROUP | CONCURRENT SHAPES | FLY OCTOGON WITH GROUP | SETUP CONCURRENCE WITH MONITOR | 
|-- | -- | -- | -- | -- | -- | 
|![Application: to and fro](/action_server/go_home.png "To and Fro") | ![Application: to and fro](/action_server/traj_selector.png "To and Fro") | | | | |
| smlib.move_drone(drone_id, traj_waypoint) | smlib.monitor_general(monitor_topic, monitor_type, truth_function) | smlib.land_group(selected_drones, traj_waypoint) | smlib.concurrent_trajs(selected_drones, traj_id) | octogon_all_drones(selected_drones, waypoint_array, order_array) | monitored_trajs(internal_sm, internal_name, monitor_sm, monitor_name) | 
| MOVE DRONE [id] to waypoint [index] |   | Land all the drones to their respective points | CONCURRENT Fo8s CONTAINER # Using all the ids currently running. | | |



> More Examples: https://github.com/ThomasCarstens/Service_Drones_Thesis/blob/main/sm_structs.py
<!-- ## Usage
Our API packages swarm instructions, ie. commands with their own internal task management.

    fig8_sm = concurrent_trajs(selected_drones = [1, 2, 4], traj_id = 8)
    # where concurrent_trajs(...) is defined below.

## Development
Note how concurrent_trajs(...) returns a particular State of States. 

    def concurrent_trajs(self, selected_drones, traj_id):
        """FIG8_EXECUTE""" 
        figure = Concurrence(['succeeded', 'aborted', 'preempted'], 'succeeded')
        with figure:
            for drone_id in selected_drones:
                Concurrence.add('FIG8_EXECUTE_drone'+str(id),
                SimpleActionState('fig8_drone'+str(id),
                                doTrajAction, goal = doTrajGoal(shape = traj_id, id = drone_id)))
        return figure
 -->

# individual task execution


| PREDEFINED TRAJECTORY | LAND (AND CONFIRM IF ALIVE) | FLY TO WAYPOINT | FOLLOW-ME | RANDOM WALK |
|-- | -- | -- | -- | -- |
| trajectory_action | land_   | detect_perimeter | cf3_follow_cf2 | random_walk |
| DRONE [goal.id] executes TRAJ [goal.shape] - query arrival at 200Hz | MOVE DRONE [goal.id] down   | MOVE DRONE [goal.id] to Point [goal.point] - query arrival at 200Hz | MOVE DRONE [goal.id] to Point [goal.point] (currently cf2)  | MOVE DRONE [goal.id] to Point [goal.point] (currently random) |

> More Examples: https://github.com/ThomasCarstens/cfScripts/blob/master/ros_action_server.py

<!-- ## Usage
Within a particular swarm instruction, come calls to perform tasks. The python actionlib library references an instruction (here 'fig8_drone1', 'fig8_drone2'... and it is conveniently wrapped into a SimpleActionState to interface with the smach library.

    SimpleActionState('fig8_drone'+str(id),
                      doTrajAction, 
                      goal = doTrajGoal(shape = traj_id, id = drone_id)))

## Development
SimpleActionState communicates the task to the ROS Action Server, which performs the robot instructions. This allows for a truly asynchronous management of tasks, thus the execution of tasks remains separate.

    for drone in self.allcfs.crazyflies:                              
        if drone.id == goal.id: #id is a goal argument
            drone.takeoff(targetHeight=0.6, duration=3.0)
            drone.goTo(self.waypoint, yaw=0, duration=3.0)

These robot instructions are encapsulated within a server callback.
The server informs the execution with **feedback** and **result** data.
 -->


# getting started

/!\ The Crazyswarm repository needs to be set up.
> https://crazyswarm.readthedocs.io/en/latest/
This is necessary to create the python bindings to interact with the drones from the Groundstation.

    cd crazyswarm/ros_ws/src/crazyswarm/launch
    #launch an independent core for ROS
    roscore
    python crazyswarm/ros_ws/src/crazyswarm/scripts/chooser.py
    #set up the swarm server and dependables
    roslaunch crazyswarm hover_swarm.launch
    #launch the action server and dependables
    roslaunch 3_drones.launch
    #now you can launch your user code
    python individual_hover.py# getting started

