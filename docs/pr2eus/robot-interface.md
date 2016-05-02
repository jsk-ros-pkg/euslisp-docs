### controller-action-client
- :super **ros::simple-action-client**
- :slots time-to-finish ri angle-vector-sequence timer-sequence current-time current-angle-vector previous-angle-vector scale-angle-vector 



:init *r* *&rest* *args* 

:time-to-finish 

:action-feedback-cb *msg* 

:push-angle-vector-simulation *av* *tm* *&optional* *prev-av* 

:pop-angle-vector-simulation 

:interpolatingp 


### robot-interface
- :super **propertied-object**
- :slots robot objects robot-state joint-action-enable warningp controller-type controller-actions controller-timeout namespace controller-table visualization-topic simulation-default-look-all-p joint-states-topic pub-joint-states-topic viewer groupname 

robot-interface is object for interacting real robot thorugh JointTrajectoryAction servers and JointState topics, this function uses old nervous-style interface for histrical reason. If JointTrajectoryAcion serve is not found, this instance runs as simulation mode, see "Kinematics Simulator" view as simulatied robot, <br>
 <br>

        (setq *ri* (instance robot-interface :init))
        (send *ri* :angle-vector (send *robot* :joint-angle) 2000)
        (send *ri* :wait-interpolation)
        (send *ri* :state :potentio-vector)


#### :init
&nbsp;&nbsp;&nbsp;*&rest* *args* *&key* *((:robot r))* <br>&nbsp;&nbsp;&nbsp;*((:objects objs))* <br>&nbsp;&nbsp;&nbsp;*(type :default-controller)* <br>&nbsp;&nbsp;&nbsp;*(use-tf2)* <br>&nbsp;&nbsp;&nbsp;*((:groupname nh) robot_multi_queue)* <br>&nbsp;&nbsp;&nbsp;*((:namespace ns))* <br>&nbsp;&nbsp;&nbsp;*((:joint-states-topic jst) joint_states)* <br>&nbsp;&nbsp;&nbsp;*((:publish-joint-states-topic pjst) nil)* <br>&nbsp;&nbsp;&nbsp;*((:controller-timeout ct) 3)* <br>&nbsp;&nbsp;&nbsp;*((:visuzlization-marker-topic vmt) robot_interface_marker_array)* <br>&nbsp;&nbsp;&nbsp;*((:simulation-look-all sim-look-all) t)* <br>&nbsp;&nbsp;&nbsp;*&allow-other-keys* 

- Create robot interface <br>
- robot : class name of robot <br>
- objects : objects to be displayed in simulation (only for simulation) <br>
- type : method name for defineing controller type <br>
- use-tf2 : use tf2 <br>
- groupname : ros nodehandle group name <br>
- namespace : ros nodehandle name space <br>
- publish-joint-state-topic :  name for publishing joint state topuc (only for simulation) <br>
- conter-timeout : timeout seconds for controller lookup <br>
- visualizatoin-marker-topic : topic name for visualize <br>


#### :angle-vector-safe
&nbsp;&nbsp;&nbsp;*av* *tm* *&key* *(simulation)* <br>&nbsp;&nbsp;&nbsp;*(yes)* <br>&nbsp;&nbsp;&nbsp;*(wait t)* <br>&nbsp;&nbsp;&nbsp;*(norm-threshold 120)* <br>&nbsp;&nbsp;&nbsp;*(angle-threshold 80)* <br>&nbsp;&nbsp;&nbsp;*(velocity-threshold 240)* 

- send joint angle to robot with user-level confirmation. This method requires key input <br>


#### :angle-vector
&nbsp;&nbsp;&nbsp;*av* *&optional* *(tm nil)* *(ctype controller-type)* *(start-time 0)* *&key* *(scale 1)* <br>&nbsp;&nbsp;&nbsp;*(min-time 1.0)* 

- Send joind angle to robot, this method retuns immediately, so use :wait-interpolation to block until the motion stops. <br>
- av : joint angle vector [rad] <br>
- tm : (time to goal in [msec]) <br>
   if designated tm is faster than fastest speed, use fastest speed <br>
   if not specified, it will use 1/scale of the fastest speed . <br>
   if :fastest is specefied use fastest speed calcurated from max speed <br>
- ctype : controller method name <br>
- start-time : time to start moving <br>
- scale : if tm is not specified, it will use 1/scale of the fastest speed <br>
- min-time : minimum time to for time to goal <br>


#### :angle-vector-sequence
&nbsp;&nbsp;&nbsp;*avs* *&optional* *(tms (list 3000))* *(ctype controller-type)* *(start-time 0.1)* *&key* *(scale 1)* <br>&nbsp;&nbsp;&nbsp;*(min-time 0.0)* 

- Send joind angle to robot, this method retuns immediately, so use :wait-interpolation to block until the motion stops. <br>


#### :wait-interpolation
&nbsp;&nbsp;&nbsp;*&optional* *(ctype)* *(timeout 0)* 

- Wait until last sent motion is finished <br>
- ctype : controller to be wait <br>
- timeout : max time of for waiting <br>
- return values is a list of interpolatingp for all controllers, so (null (some #'identity (send *ri* :wait-interpolation))) -> t if all interpolation has stopped <br>


#### :interpolatingp
&nbsp;&nbsp;&nbsp;*&optional* *(ctype)* 

- Check if the last sent motion is executing <br>
return t if interpolating <br>


#### :wait-interpolation-smooth
&nbsp;&nbsp;&nbsp;*time-to-finish* *&optional* *(ctype)* 

- Return time-to-finish [msec] before the sent command is finished. Example code are: <br>

        (dolist (av (list av1 av2 av3 av4))
            (send *ri* :angle-vector av)
            (send *ri* :wait-interpolation-smooth 300))
Return value is a list of interpolatingp for all controllers, so (null (some #'identity (send *ri* :wait-interpolation))) -> t if all interpolation has stopped <br>


#### :interpolating-smoothp
&nbsp;&nbsp;&nbsp;*time-to-finish* *&optional* *(ctype)* 

- Check inf the last sent motion will stops for next time-to-finish [msec] <br>


#### :stop-motion
&nbsp;&nbsp;&nbsp;*&key* *(stop-time 0)* 

- Stop current executing motion <br>


#### :cancel-angle-vector
&nbsp;&nbsp;&nbsp;*&key* *((:controller-actions ca) controller-actions)* <br>&nbsp;&nbsp;&nbsp;*((:controller-type ct) controller-type)* <br>&nbsp;&nbsp;&nbsp;*(wait)* 

- Cancerl current joint trajetory action <br>


#### :worldcoords


- Returns world coords of robot, This method uses caced data <br>


#### :torque-vector


- Return torque vector of robot, This method uses caced data <br>


#### :potentio-vector


- Retuns current robot angle vector, This method uses caced data <br>


#### :reference-vector


- Return reference joint angle vector, This method uread current state. <br>


#### :actual-vector


- Returns current robot angle vector, This method read curren tstate. <br>


#### :error-vector


- Returns error vector of the robot, This method read current state. <br>


#### :state
&nbsp;&nbsp;&nbsp;*&rest* *args* 

- Read robot state and update internal robot instance <br>
- :wait-until-update nil wait until joint_state is updated <br>


#### :find-object
&nbsp;&nbsp;&nbsp;*name-or-obj* 

- Returns objects with given name or object of the same name. <br>


#### :simulation-modep


- Check if simulation mode <br>


#### :warningp
&nbsp;&nbsp;&nbsp;*&optional* *(w :dummy)* 

- When warning mode, it wait for user's key input before sending angle-vector to the robot <br>


#### :go-pos
&nbsp;&nbsp;&nbsp;*x* *y* *&optional* *(d 0)* 

- move robot toward x, y, degree and wait to reach that goal. return t if reached or nil if fail <br>
    the robot moves relative to current position. <br>
    using [m] and [degree] is historical reason from original hrpsys code <br>


#### :go-pos-no-wait
&nbsp;&nbsp;&nbsp;*x* *y* *&optional* *(d 0)* 

- no-wait version of :go-pos. this function is always assumed to return t <br>


#### :go-wait


- wait until :go-pos-no-wait reached to the goal, return t if reached or nil if fail <br>


#### :go-velocity
&nbsp;&nbsp;&nbsp;*x* *y* *d* *&optional* *(msec 1000)* *&key* *(stop t)* <br>&nbsp;&nbsp;&nbsp;*(wait)* 

- move robot at given speed for given msec. <br>
    if stop is t, robot stops after msec, use wait t for blocking call <br>
    return nil if aborted while waiting by enabling :wait, otherwise return t <br>


#### :go-stop


- stop go-velocity. return t if robot successfly stops, otherwise return nil <br>


#### :gripper
&nbsp;&nbsp;&nbsp;*&rest* *args* 

- get information about gripper <br>


#### :nod
&nbsp;&nbsp;&nbsp;*&key* *(angle 40)* <br>&nbsp;&nbsp;&nbsp;*(time 3000)* 

- Nod robot head <br>


#### :eus-mannequin-mode
&nbsp;&nbsp;&nbsp;*tmp-robot* *limb* *&key* *((:time tm) 1000)* <br>&nbsp;&nbsp;&nbsp;*((:viewer vwr) *viewer*)* 

- Euslisp version mannequin mode. <br>
    In every loop, :angle-vector is continuously updated. <br>
    If you want to stop this mode, press Enter. <br>
- tmp-robot : argument for robot instance and angle-vector of tmp-robot is updated in this method. <br>
- limb : mannequin mode limb such as :rarm, :larm, :rleg, and :lleg. <br>
- tm : angle-vector update time [ms]. 1000 by default. <br>


:add-controller *ctype* *&key* *(joint-enable-check)* *(create-actions)* 

:robot-interface-simulation-callback 

:publish-joint-state *&optional* *(joint-list (send robot :joint-list))* 

:angle-vector-duration *start* *end* *&optional* *(scale 1)* *(min-time 1.0)* *(ctype controller-type)* 

:angle-vector-simulation *av* *tm* *ctype* 

:state-vector *type* *&key* *((:controller-actions ca) controller-actions)* *((:controller-type ct) controller-type)* 

:send-ros-controller *action* *joint-names* *starttime* *trajpoints* 

:set-robot-state1 *key* *msg* 

:ros-state-callback *msg* 

:wait-until-update-all-joints *tgt-tm* 

:update-robot-state *&key* *(wait-until-update nil)* 

:default-controller 

:sub-angle-vector *v0* *v1* 

:robot *&rest* *args* 

:viewer *&rest* *args* 

:objects *&optional* *objs* 

:set-simulation-default-look-at *look-at-p* 

:draw-objects *&key* *look-all* 

:joint-action-enable *&optional* *(e :dummy)* 

:spin-once 

:send-trajectory *joint-trajectory-msg* *&key* *((:controller-actions ca) controller-actions)* *((:controller-type ct) controller-type)* *(starttime 1)* *&allow-other-keys* 

:send-trajectory-each *action* *joint-names* *traj* *&optional* *(starttime 0.2)* 

:ros-wait *tm* *&key* *(spin)* *(spin-self)* *(finish-check)* *&allow-other-keys* 

:joint-trajectory-to-angle-vector-list *move-arm* *joint-trajectory* *&key* *((:diff-sum diff-sum) 0)* *((:diff-thre diff-thre) 50)* *(show-trajectory t)* *(send-trajectory t)* *((:speed-scale speed-scale) 1.0)* *&allow-other-keys* 

:show-goal-hand-coords *coords* *move-arm* 

:find-descendants-dae-links *l* 

:show-mesh-traj-with-color *link-body-list* *link-coords-list* *&key* *((:lifetime lf) 20)* *(ns robot_traj)* *((:color col) #f(0.5 0.5 0.5))* 


### robot-move-base-interface
- :super **robot-interface**
- :slots move-base-action move-base-trajectory-action move-base-goal-msg move-base-goal-coords move-base-goal-map-to-frame base-frame-id odom-topic go-pos-unsafe-goal-msg current-goal-coords 



#### :move-trajectory-sequence
&nbsp;&nbsp;&nbsp;*trajectory-points* *time-list* *&key* *(stop t)* <br>&nbsp;&nbsp;&nbsp;*(start-time)* <br>&nbsp;&nbsp;&nbsp;*(send-action nil)* 

- trajectory-points [ list of #f(x y d) ] time-list [list of time span] <br>
stop [ stop after msec moveing ] <br>
start-time [ robot will move at start-time ] <br>
send-action [ send message to action server, it means robot will move ] <br>


#### :move-trajectory
&nbsp;&nbsp;&nbsp;*x* *y* *d* *&optional* *(msec 1000)* *&key* *(stop t)* <br>&nbsp;&nbsp;&nbsp;*(start-time)* <br>&nbsp;&nbsp;&nbsp;*(send-action nil)* 

-  x [m/sec] y [m/sec] d [rad/sec] msec [milli second] <br>
stop [ stop after msec moveing ] <br>
start-time [ robot will move at start-time ] <br>
send-action [ send message to action server, it means robot will move ] <br>


:init *&rest* *args* *&key* *(move-base-action-name move_base)* *((:base-frame-id base-frame-id-name) /base_footprint)* *((:odom-topic odom-topic-name) /base_odometry/odom)* *&allow-other-keys* 

:odom-callback *msg* 

:go-stop *&optional* *(force-stop t)* 

:make-plan *st-cds* *goal-cds* *&key* *(start-frame-id /world)* *(goal-frame-id /world)* 

:move-to *coords* *&rest* *args* *&key* *(no-wait nil)* *&allow-other-keys* 

:move-to-send *coords* *&key* *(frame-id /world)* *(wait-for-server-timeout 5)* *(count 0)* *&allow-other-keys* 

:move-to-wait *&rest* *args* *&key* *(retry 10)* *(frame-id /world)* *&allow-other-keys* 

:go-waitp 

:go-pos *x* *y* *&optional* *(d 0)* 

:go-pos-no-wait *x* *y* *&optional* *(d 0)* 

:go-wait 

:go-velocity *x* *y* *d* *&optional* *(msec 1000)* *&key* *(stop t)* *(wait)* 

:go-pos-unsafe *&rest* *args* 

:go-pos-unsafe-no-wait *x* *y* *&optional* *(d 0)* 

:go-pos-unsafe-wait 

:state *&rest* *args* 


### ros-interface
- :super **robot-interface**
- :slots nil



:init *&rest* *args* 


### make-robot-interface-from-name ###
&nbsp;&nbsp;&nbsp;*name* *&rest* *args* 

- make a robot model from string: (make-robot-model "pr2") <br>


### init-robot-from-name ###
&nbsp;&nbsp;&nbsp;*robot-name* *&rest* *args* 

- call ${robot}-init function <br>


### clear-costmap ###
&nbsp;&nbsp;&nbsp;*&key* *(node-name /move_base_node)* 

- reset local costmap and clear unknown grid around robot <br>


### change-inflation-range ###
&nbsp;&nbsp;&nbsp;*&optional* *(range 0.2)* *&key* *(node-name /move_base_node)* <br>&nbsp;&nbsp;&nbsp;*(costmap-name local_costmap)* <br>&nbsp;&nbsp;&nbsp;*(inflation-name inflation)* 

- change inflation range of local costmap <br>


### robot-init ###
&nbsp;&nbsp;&nbsp;*&optional* *(robot-name (ros::get-param /robot/type))* 

- Initialize robot-model and robot-interface instances respectively, such as *pr2* and *ri*. <br>
   Behavior: <br>
     1. Without /robot/type and argument, or no interface file is found, robot instance and interface instance are not created and errors are invoked. <br>
     2. If argument are specified and robot interface file is found, robot instance and interface with given name are created. <br>
     3. If no argument is specified, /robot/type ROSPARAM is set, and robot interface file is found, robot instance and interface with given name are created. <br>
   Typical usage: <br>
     (robot-init) ;; With setting /robot/type ROSPARAM anywhere. <br>
     (robot-init (or (ros::get-param "/robot/type") "pr2")) ;; If /robot/type ROSPARAM is specified, use it. Otherwise, use "pr2" by default. <br>
   Configuring user-defined robot: <br>
     This function searches robot interface file from rospack plugins. <br>
     If user want to use their own robots from robot-init function, <br>
     please write export tag in [user_defined_rospackage]/package.xml as pr2eus/package.xml. <br>
    <br>


joint-list->joint_state *jlist* *&key* *(position)* *(effort 0)* *(velocity 0)* 

apply-joint_state *jointstate* *robot* 

apply-trajectory_point *names* *trajpoint* *robot* 

apply-joint_trajectory *joint-trajectory* *robot* *&optional* *(offset 200.0)* 

