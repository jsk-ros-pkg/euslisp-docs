### rotational-joint
- :super **joint**
- :slots parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target axis 



:ros-joint-angle *&optional* *v* *&rest* *args* 


### linear-joint
- :super **joint**
- :slots parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target axis 



:ros-joint-angle *&optional* *v* *&rest* *args* 


### controller-action-client
- :super **ros::simple-action-client**
- :slots time-to-finish last-feedback-msg-stamp ri angle-vector-sequence timer-sequence current-time current-angle-vector previous-angle-vector scale-angle-vector 



:init *r* *&rest* *args* 

:last-feedback-msg-stamp 

:time-to-finish 

:action-feedback-cb *msg* 

:push-angle-vector-simulation *av* *tm* *&optional* *prev-av* 

:pop-angle-vector-simulation 

:interpolatingp 


:interpolatingp 

:pop-angle-vector-simulation 

:push-angle-vector-simulation *av* *tm* *&optional* *prev-av* 

:action-feedback-cb *msg* 

:time-to-finish 

:last-feedback-msg-stamp 

:init *r* *&rest* *args* 


### robot-interface
- :super **propertied-object**
- :slots robot objects robot-state joint-action-enable warningp controller-type controller-actions controller-timeout namespace controller-table visualization-topic simulation-default-look-all-p joint-states-topic pub-joint-states-topic viewer groupname 

robot-interface is object for interacting real robot through JointTrajectoryAction servers and JointState topics, this function uses old nervous-style interface for historical reason. If JointTrajectoryAcion server is not found, this instance runs as simulation mode, see "Kinematics Simulator" view as simulated robot, <br>
 <br>

        (setq *ri* (instance robot-interface :init))
        (send *ri* :angle-vector (send *robot* :joint-angle) 2000)
        (send *ri* :wait-interpolation)
        (send *ri* :state :potentio-vector)


#### :init
&nbsp;&nbsp;&nbsp;*&rest* *args* *&key* *((:robot r))* <br>&nbsp;&nbsp;&nbsp;*((:objects objs))* <br>&nbsp;&nbsp;&nbsp;*(type :default-controller)* <br>&nbsp;&nbsp;&nbsp;*(use-tf2)* <br>&nbsp;&nbsp;&nbsp;*((:groupname nh) robot_multi_queue)* <br>&nbsp;&nbsp;&nbsp;*((:namespace ns))* <br>&nbsp;&nbsp;&nbsp;*((:joint-states-topic jst) joint_states)* <br>&nbsp;&nbsp;&nbsp;*((:joint-states-queue-size jsq) 1)* <br>&nbsp;&nbsp;&nbsp;*((:publish-joint-states-topic pjst) nil)* <br>&nbsp;&nbsp;&nbsp;*((:controller-timeout ct) 3)* <br>&nbsp;&nbsp;&nbsp;*((:visualization-marker-topic vmt) robot_interface_marker_array)* <br>&nbsp;&nbsp;&nbsp;*((:simulation-look-all sim-look-all) t)* <br>&nbsp;&nbsp;&nbsp;*&allow-other-keys* 

- Create robot interface <br>
- robot : class name of robot <br>
- objects : objects to be displayed in simulation (only for simulation) <br>
- type : method name for defining controller type <br>
- use-tf2 : use tf2 <br>
- groupname : ros nodehandle group name <br>
- namespace : ros nodehandle name space <br>
- joint-states-topic (jst) : name for subscribing joint state topic <br>
- joint-states-queue-size : queue size of joint state topic. if you have two different node publishing joint_state, better to use 2. default is 1 <br>
- publish-joint-state-topic :  name for publishing joint state topic (only for simulation) <br>
- controller-timeout : timeout seconds for controller look-up <br>
- visualization-marker-topic : topic name for visualize <br>


#### :angle-vector-safe
&nbsp;&nbsp;&nbsp;*av* *tm* *&key* *(simulation)* <br>&nbsp;&nbsp;&nbsp;*(yes)* <br>&nbsp;&nbsp;&nbsp;*(wait t)* <br>&nbsp;&nbsp;&nbsp;*(norm-threshold 120)* <br>&nbsp;&nbsp;&nbsp;*(angle-threshold 80)* <br>&nbsp;&nbsp;&nbsp;*(velocity-threshold 240)* 

- send joint angle to robot with user-level confirmation. This method requires key input <br>


#### :angle-vector
&nbsp;&nbsp;&nbsp;*av* *&optional* *(tm nil)* *(ctype controller-type)* *(start-time 0)* *&key* *(scale 1)* <br>&nbsp;&nbsp;&nbsp;*(min-time 1.0)* <br>&nbsp;&nbsp;&nbsp;*(end-coords-interpolation nil)* <br>&nbsp;&nbsp;&nbsp;*(end-coords-interpolation-steps 10)* 

- Send joint angle to robot, this method returns immediately, so use :wait-interpolation to block until the motion stops. <br>
- av : joint angle vector [deg] <br>
- tm : (time to goal in [msec]) <br>
   if designated tm is faster than fastest speed, use fastest speed <br>
   if not specified, it will use 1/scale of the fastest speed . <br>
   if :fast is specified use fastest speed calculated from max speed <br>
- ctype : controller method name <br>
- start-time : time to start moving <br>
- scale : if tm is not specified, it will use 1/scale of the fastest speed <br>
- min-time : minimum time for time to goal <br>
- end-coords-interpolation : set t if you want to move robot in cartesian space interpolation <br>
- end-coords-interpolation-steps : number of divisions when interpolating end-coords <br>


#### :angle-vector-sequence
&nbsp;&nbsp;&nbsp;*avs* *&optional* *(tms (list 3000))* *(ctype controller-type)* *(start-time 0.1)* *&key* *(scale 1)* <br>&nbsp;&nbsp;&nbsp;*(min-time 0.0)* <br>&nbsp;&nbsp;&nbsp;*(end-coords-interpolation nil)* <br>&nbsp;&nbsp;&nbsp;*(end-coords-interpolation-steps 10)* 

- Send sequence of joint angle to robot, this method returns immediately, so use :wait-interpolation to block until the motion stops. <br>
- avs: sequence of joint angles(float-vector) [deg],  (list av0 av1 ... avn) <br>
- tms: sequence of duration(float) from previous angle-vector to next goal [msec],  (list tm0 tm1 ... tmn) <br>
   if tms is atom, then use (list (make-list (length avs) :initial-element tms))) for tms <br>
   if designated each tmn is faster than fastest speed, use fastest speed <br>
   if tmn is nil, then it will use 1/scale of the fastest speed . <br>
   if :fast is specified, use fastest speed calculated from max speed <br>
- ctype : controller method name <br>
- start-time : time to start moving <br>
- scale : if tms is not specified, it will use 1/scale of the fastest speed <br>
- min-time : minimum time for time to goal <br>
- end-coords-interpolation : set t if you want to move robot in cartesian space interpolation <br>
- end-coords-interpolation-steps : number of divisions when interpolating end-coords <br>
    <br>


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

- Check if the last sent motion will stop for next time-to-finish [msec] <br>


#### :stop-motion
&nbsp;&nbsp;&nbsp;*&key* *(stop-time 0)* <br>&nbsp;&nbsp;&nbsp;*(wait t)* 

- Smoothly stops the motion being executed by sending the current joint state to the joint trajectory action goal in stop-time [ms]. Please note that if stop-time is smaller than the :angle-vector min-time (defaulting to 1000 [ms]) the actual interpolation uses min-time instead. Refer to :cancel-angle-vector for a faster stop <br>


#### :cancel-angle-vector
&nbsp;&nbsp;&nbsp;*&key* *((:controller-actions ca))* <br>&nbsp;&nbsp;&nbsp;*((:controller-type ct))* <br>&nbsp;&nbsp;&nbsp;*(wait)* 

- Cancels the current joint trajectory action, causing an abrupt stop <br>


#### :worldcoords


- Returns world coords of robot. This method uses caced data <br>


#### :torque-vector


- Return torque vector of robot. This method uses caced data <br>


#### :potentio-vector


- Returns current robot angle vector. This method uses caced data <br>


#### :reference-vector


- Returns reference joint angle vector. This method reads current state. <br>


#### :actual-vector


- Returns current robot angle vector. This method reads current state. <br>


#### :error-vector


- Returns error vector of the robot. This method reads current state. <br>


#### :stamp


- Returns the stamp which was read at latest callback <br>


#### :state
&nbsp;&nbsp;&nbsp;*&rest* *args* 

- Read robot state and update internal robot instance <br>
- :wait-until-update nil wait until joint_state is updated <br>


#### :find-object
&nbsp;&nbsp;&nbsp;*name-or-obj* 

- Returns objects with given name or object of the same name. <br>


#### :simulation-modep


- Check if in simulation mode <br>


#### :warningp
&nbsp;&nbsp;&nbsp;*&optional* *(w :dummy)* 

- When in warning mode, it wait for user's key input before sending angle-vector to the robot <br>


#### :go-pos
&nbsp;&nbsp;&nbsp;*x* *y* *&optional* *(d 0)* 

- move robot toward x, y, degree and wait to reach that goal. Return t if reached or nil if fail <br>
    the robot moves relative to current position. <br>
    using [m] and [degree] is historical reason from original hrpsys code <br>


#### :go-pos-no-wait
&nbsp;&nbsp;&nbsp;*x* *y* *&optional* *(d 0)* 

- no-wait version of :go-pos. This function is always assumed to return t <br>


#### :go-wait


- wait until :go-pos-no-wait reached the goal. Return t if reached or nil if fail <br>


#### :go-velocity
&nbsp;&nbsp;&nbsp;*x* *y* *d* *&optional* *(msec 1000)* *&key* *(stop t)* <br>&nbsp;&nbsp;&nbsp;*(wait)* 

- move robot at given speed for given msec. <br>
    if stop is t, robot stops after msec, use wait t for blocking call <br>
    return nil if aborted while waiting by enabling :wait, otherwise return t <br>


#### :go-stop


- stop go-velocity. Return t if robot successfully stops, otherwise return nil <br>


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

:get-robot-state *key* 

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

:send-trajectory *joint-trajectory-msg* *&key* *((:controller-type ct) controller-type)* *(starttime 1)* *&allow-other-keys* 

:send-trajectory-each *action* *joint-names* *traj* *&optional* *(starttime 0.2)* 

:ros-wait *tm* *&key* *(spin)* *(spin-self)* *(finish-check)* *&allow-other-keys* 

:joint-trajectory-to-angle-vector-list *move-arm* *joint-trajectory* *&key* *((:diff-sum diff-sum) 0)* *((:diff-thre diff-thre) 50)* *(show-trajectory t)* *(send-trajectory t)* *((:speed-scale speed-scale) 1.0)* *&allow-other-keys* 

:show-goal-hand-coords *coords* *move-arm* 

:find-descendants-dae-links *l* 

:show-mesh-traj-with-color *link-body-list* *link-coords-list* *&key* *((:lifetime lf) 20)* *(ns robot_traj)* *((:color col) #f(0.5 0.5 0.5))* 

:play-sound *sound* *&key* *arg2* *(topic-name robotsound)* *wait* 

:speak *text* *&key* *(lang )* *(topic-name robotsound)* *wait* 

:speak-en *text* *&key* *(topic-name robotsound)* *wait* 

:speak-jp *text* *&key* *(topic-name robotsound_jp)* *wait* 


#### :gripper
&nbsp;&nbsp;&nbsp;*&rest* *args* 

- get information about gripper <br>


#### :go-stop


- stop go-velocity. Return t if robot successfully stops, otherwise return nil <br>


#### :go-velocity
&nbsp;&nbsp;&nbsp;*x* *y* *d* *&optional* *(msec 1000)* *&key* *(stop t)* <br>&nbsp;&nbsp;&nbsp;*(wait)* 

- move robot at given speed for given msec. <br>
    if stop is t, robot stops after msec, use wait t for blocking call <br>
    return nil if aborted while waiting by enabling :wait, otherwise return t <br>


#### :go-wait


- wait until :go-pos-no-wait reached the goal. Return t if reached or nil if fail <br>


#### :go-pos-no-wait
&nbsp;&nbsp;&nbsp;*x* *y* *&optional* *(d 0)* 

- no-wait version of :go-pos. This function is always assumed to return t <br>


#### :go-pos
&nbsp;&nbsp;&nbsp;*x* *y* *&optional* *(d 0)* 

- move robot toward x, y, degree and wait to reach that goal. Return t if reached or nil if fail <br>
    the robot moves relative to current position. <br>
    using [m] and [degree] is historical reason from original hrpsys code <br>


#### :warningp
&nbsp;&nbsp;&nbsp;*&optional* *(w :dummy)* 

- When in warning mode, it wait for user's key input before sending angle-vector to the robot <br>


#### :simulation-modep


- Check if in simulation mode <br>


#### :find-object
&nbsp;&nbsp;&nbsp;*name-or-obj* 

- Returns objects with given name or object of the same name. <br>


#### :state
&nbsp;&nbsp;&nbsp;*&rest* *args* 

- Read robot state and update internal robot instance <br>
- :wait-until-update nil wait until joint_state is updated <br>


#### :stamp


- Returns the stamp which was read at latest callback <br>


#### :error-vector


- Returns error vector of the robot. This method reads current state. <br>


#### :actual-vector


- Returns current robot angle vector. This method reads current state. <br>


#### :reference-vector


- Returns reference joint angle vector. This method reads current state. <br>


#### :potentio-vector


- Returns current robot angle vector. This method uses caced data <br>


#### :torque-vector


- Return torque vector of robot. This method uses caced data <br>


#### :worldcoords


- Returns world coords of robot. This method uses caced data <br>


#### :cancel-angle-vector
&nbsp;&nbsp;&nbsp;*&key* *((:controller-actions ca))* <br>&nbsp;&nbsp;&nbsp;*((:controller-type ct))* <br>&nbsp;&nbsp;&nbsp;*(wait)* 

- Cancels the current joint trajectory action, causing an abrupt stop <br>


#### :stop-motion
&nbsp;&nbsp;&nbsp;*&key* *(stop-time 0)* <br>&nbsp;&nbsp;&nbsp;*(wait t)* 

- Smoothly stops the motion being executed by sending the current joint state to the joint trajectory action goal in stop-time [ms]. Please note that if stop-time is smaller than the :angle-vector min-time (defaulting to 1000 [ms]) the actual interpolation uses min-time instead. Refer to :cancel-angle-vector for a faster stop <br>


#### :interpolating-smoothp
&nbsp;&nbsp;&nbsp;*time-to-finish* *&optional* *(ctype)* 

- Check if the last sent motion will stop for next time-to-finish [msec] <br>


#### :wait-interpolation-smooth
&nbsp;&nbsp;&nbsp;*time-to-finish* *&optional* *(ctype)* 

- Return time-to-finish [msec] before the sent command is finished. Example code are: <br>

        (dolist (av (list av1 av2 av3 av4))
            (send *ri* :angle-vector av)
            (send *ri* :wait-interpolation-smooth 300))
Return value is a list of interpolatingp for all controllers, so (null (some #'identity (send *ri* :wait-interpolation))) -> t if all interpolation has stopped <br>


#### :interpolatingp
&nbsp;&nbsp;&nbsp;*&optional* *(ctype)* 

- Check if the last sent motion is executing <br>
return t if interpolating <br>


#### :wait-interpolation
&nbsp;&nbsp;&nbsp;*&optional* *(ctype)* *(timeout 0)* 

- Wait until last sent motion is finished <br>
- ctype : controller to be wait <br>
- timeout : max time of for waiting <br>
- return values is a list of interpolatingp for all controllers, so (null (some #'identity (send *ri* :wait-interpolation))) -> t if all interpolation has stopped <br>


#### :angle-vector-sequence
&nbsp;&nbsp;&nbsp;*avs* *&optional* *(tms (list 3000))* *(ctype controller-type)* *(start-time 0.1)* *&key* *(scale 1)* <br>&nbsp;&nbsp;&nbsp;*(min-time 0.0)* <br>&nbsp;&nbsp;&nbsp;*(end-coords-interpolation nil)* <br>&nbsp;&nbsp;&nbsp;*(end-coords-interpolation-steps 10)* 

- Send sequence of joint angle to robot, this method returns immediately, so use :wait-interpolation to block until the motion stops. <br>
- avs: sequence of joint angles(float-vector) [deg],  (list av0 av1 ... avn) <br>
- tms: sequence of duration(float) from previous angle-vector to next goal [msec],  (list tm0 tm1 ... tmn) <br>
   if tms is atom, then use (list (make-list (length avs) :initial-element tms))) for tms <br>
   if designated each tmn is faster than fastest speed, use fastest speed <br>
   if tmn is nil, then it will use 1/scale of the fastest speed . <br>
   if :fast is specified, use fastest speed calculated from max speed <br>
- ctype : controller method name <br>
- start-time : time to start moving <br>
- scale : if tms is not specified, it will use 1/scale of the fastest speed <br>
- min-time : minimum time for time to goal <br>
- end-coords-interpolation : set t if you want to move robot in cartesian space interpolation <br>
- end-coords-interpolation-steps : number of divisions when interpolating end-coords <br>
    <br>


#### :angle-vector
&nbsp;&nbsp;&nbsp;*av* *&optional* *(tm nil)* *(ctype controller-type)* *(start-time 0)* *&key* *(scale 1)* <br>&nbsp;&nbsp;&nbsp;*(min-time 1.0)* <br>&nbsp;&nbsp;&nbsp;*(end-coords-interpolation nil)* <br>&nbsp;&nbsp;&nbsp;*(end-coords-interpolation-steps 10)* 

- Send joint angle to robot, this method returns immediately, so use :wait-interpolation to block until the motion stops. <br>
- av : joint angle vector [deg] <br>
- tm : (time to goal in [msec]) <br>
   if designated tm is faster than fastest speed, use fastest speed <br>
   if not specified, it will use 1/scale of the fastest speed . <br>
   if :fast is specified use fastest speed calculated from max speed <br>
- ctype : controller method name <br>
- start-time : time to start moving <br>
- scale : if tm is not specified, it will use 1/scale of the fastest speed <br>
- min-time : minimum time for time to goal <br>
- end-coords-interpolation : set t if you want to move robot in cartesian space interpolation <br>
- end-coords-interpolation-steps : number of divisions when interpolating end-coords <br>


#### :angle-vector-safe
&nbsp;&nbsp;&nbsp;*av* *tm* *&key* *(simulation)* <br>&nbsp;&nbsp;&nbsp;*(yes)* <br>&nbsp;&nbsp;&nbsp;*(wait t)* <br>&nbsp;&nbsp;&nbsp;*(norm-threshold 120)* <br>&nbsp;&nbsp;&nbsp;*(angle-threshold 80)* <br>&nbsp;&nbsp;&nbsp;*(velocity-threshold 240)* 

- send joint angle to robot with user-level confirmation. This method requires key input <br>


#### :init
&nbsp;&nbsp;&nbsp;*&rest* *args* *&key* *((:robot r))* <br>&nbsp;&nbsp;&nbsp;*((:objects objs))* <br>&nbsp;&nbsp;&nbsp;*(type :default-controller)* <br>&nbsp;&nbsp;&nbsp;*(use-tf2)* <br>&nbsp;&nbsp;&nbsp;*((:groupname nh) robot_multi_queue)* <br>&nbsp;&nbsp;&nbsp;*((:namespace ns))* <br>&nbsp;&nbsp;&nbsp;*((:joint-states-topic jst) joint_states)* <br>&nbsp;&nbsp;&nbsp;*((:joint-states-queue-size jsq) 1)* <br>&nbsp;&nbsp;&nbsp;*((:publish-joint-states-topic pjst) nil)* <br>&nbsp;&nbsp;&nbsp;*((:controller-timeout ct) 3)* <br>&nbsp;&nbsp;&nbsp;*((:visualization-marker-topic vmt) robot_interface_marker_array)* <br>&nbsp;&nbsp;&nbsp;*((:simulation-look-all sim-look-all) t)* <br>&nbsp;&nbsp;&nbsp;*&allow-other-keys* 

- Create robot interface <br>
- robot : class name of robot <br>
- objects : objects to be displayed in simulation (only for simulation) <br>
- type : method name for defining controller type <br>
- use-tf2 : use tf2 <br>
- groupname : ros nodehandle group name <br>
- namespace : ros nodehandle name space <br>
- joint-states-topic (jst) : name for subscribing joint state topic <br>
- joint-states-queue-size : queue size of joint state topic. if you have two different node publishing joint_state, better to use 2. default is 1 <br>
- publish-joint-state-topic :  name for publishing joint state topic (only for simulation) <br>
- controller-timeout : timeout seconds for controller look-up <br>
- visualization-marker-topic : topic name for visualize <br>


:ros-wait *tm* *&key* *(spin)* *(spin-self)* *(finish-check)* *&allow-other-keys* 

:send-trajectory-each *action* *joint-names* *traj* *&optional* *(starttime 0.2)* 

:send-trajectory *joint-trajectory-msg* *&key* *((:controller-type ct) controller-type)* *(starttime 1)* *&allow-other-keys* 

:spin-once 

:joint-action-enable *&optional* *(e :dummy)* 

:draw-objects *&key* *look-all* 

:set-simulation-default-look-at *look-at-p* 

:objects *&optional* *objs* 

:viewer *&rest* *args* 

:robot *&rest* *args* 

:sub-angle-vector *v0* *v1* 

:default-controller 

:update-robot-state *&key* *(wait-until-update nil)* 

:wait-until-update-all-joints *tgt-tm* 

:ros-state-callback *msg* 

:get-robot-state *key* 

:set-robot-state1 *key* *msg* 

:send-ros-controller *action* *joint-names* *starttime* *trajpoints* 

:state-vector *type* *&key* *((:controller-actions ca) controller-actions)* *((:controller-type ct) controller-type)* 

:angle-vector-simulation *av* *tm* *ctype* 

:angle-vector-duration *start* *end* *&optional* *(scale 1)* *(min-time 1.0)* *(ctype controller-type)* 

:publish-joint-state *&optional* *(joint-list (send robot :joint-list))* 

:robot-interface-simulation-callback 

:add-controller *ctype* *&key* *(joint-enable-check)* *(create-actions)* 


#### :eus-mannequin-mode
&nbsp;&nbsp;&nbsp;*tmp-robot* *limb* *&key* *((:time tm) 1000)* <br>&nbsp;&nbsp;&nbsp;*((:viewer vwr) *viewer*)* 

- Euslisp version mannequin mode. <br>
    In every loop, :angle-vector is continuously updated. <br>
    If you want to stop this mode, press Enter. <br>
- tmp-robot : argument for robot instance and angle-vector of tmp-robot is updated in this method. <br>
- limb : mannequin mode limb such as :rarm, :larm, :rleg, and :lleg. <br>
- tm : angle-vector update time [ms]. 1000 by default. <br>


#### :nod
&nbsp;&nbsp;&nbsp;*&key* *(angle 40)* <br>&nbsp;&nbsp;&nbsp;*(time 3000)* 

- Nod robot head <br>


:show-mesh-traj-with-color *link-body-list* *link-coords-list* *&key* *((:lifetime lf) 20)* *(ns robot_traj)* *((:color col) #f(0.5 0.5 0.5))* 

:find-descendants-dae-links *l* 

:show-goal-hand-coords *coords* *move-arm* 

:joint-trajectory-to-angle-vector-list *move-arm* *joint-trajectory* *&key* *((:diff-sum diff-sum) 0)* *((:diff-thre diff-thre) 50)* *(show-trajectory t)* *(send-trajectory t)* *((:speed-scale speed-scale) 1.0)* *&allow-other-keys* 


:speak-jp *text* *&key* *(topic-name robotsound_jp)* *wait* 

:speak-en *text* *&key* *(topic-name robotsound)* *wait* 

:speak *text* *&key* *(lang )* *(topic-name robotsound)* *wait* 

:play-sound *sound* *&key* *arg2* *(topic-name robotsound)* *wait* 


### robot-move-base-interface
- :super **robot-interface**
- :slots move-base-action move-base-trajectory-action move-base-goal-msg move-base-goal-coords move-base-goal-map-to-frame base-frame-id cmd-vel-topic odom-topic move-base-trajectory-joint-names go-pos-unsafe-goal-msg current-goal-coords 



#### :move-trajectory-sequence
&nbsp;&nbsp;&nbsp;*trajectory-points* *time-list* *&key* *(stop t)* <br>&nbsp;&nbsp;&nbsp;*(start-time)* <br>&nbsp;&nbsp;&nbsp;*(send-action nil)* 

- Move base following the trajectory points at each time points <br>
    trajectory-points [ list of #f(x y d) ([m] for x, y; [rad] for d) ] <br>
    time-list [list of time span [msec] ] <br>
    stop [ stop after msec moveing ] <br>
    start-time [ robot will move at start-time [sec or ros::Time] ] <br>
    send-action [ send message to action server, it means robot will move ] <br>


#### :move-trajectory
&nbsp;&nbsp;&nbsp;*x* *y* *d* *&optional* *(msec 1000)* *&key* *(stop t)* <br>&nbsp;&nbsp;&nbsp;*(start-time)* <br>&nbsp;&nbsp;&nbsp;*(send-action nil)* 

- x [m] y [m] d [rad] msec [milli second (default: 1000)] <br>
    stop [ stop after the base reached the goal if T (default: T)] <br>
    start-time [ robot starts to move from `start-time` (default: now) ] <br>
    send-action [ send the goal to action server if enabled, otherwise just returns goal without sending (default: nil) ] <br>


#### :clear-costmap


- Send signal to clear costmap for obstacle avoidance to move_base and return t if succeeded. <br>


#### :change-inflation-range
&nbsp;&nbsp;&nbsp;*&optional* *(range 0.2)* *&key* *(node-name /move_base_node)* <br>&nbsp;&nbsp;&nbsp;*(costmap-name local_costmap)* <br>&nbsp;&nbsp;&nbsp;*(inflation-name inflation)* 

- Changes inflation range of local costmap for obstacle avoidance. <br>


:init *&rest* *args* *&key* *(move-base-action-name move_base)* *((:base-frame-id base-frame-id-name) base_footprint)* *(base-controller-action-name /base_controller/follow_joint_trajectory)* *(base-controller-joint-names (list base_link_x base_link_y base_link_pan))* *((:cmd-vel-topi cmd-vel-topic-name) /base_controller/command)* *((:odom-topic odom-topic-name) /base_odometry/odom)* *&allow-other-keys* 

:odom-callback *msg* 

:go-stop *&optional* *(force-stop t)* 

:make-plan *st-cds* *goal-cds* *&key* *(start-frame-id world)* *(goal-frame-id world)* 

:move-to *coords* *&rest* *args* *&key* *(no-wait nil)* *&allow-other-keys* 

:move-to-send *coords* *&key* *(frame-id world)* *(wait-for-server-timeout 5)* *(count 0)* *&allow-other-keys* 

:move-to-wait *&rest* *args* *&key* *(retry 10)* *(frame-id world)* *(correction t)* *&allow-other-keys* 

:go-waitp 

:go-pos *x* *y* *&optional* *(d 0)* 

:go-pos-no-wait *x* *y* *&optional* *(d 0)* 

:go-wait 

:send-cmd-vel-raw *x* *y* *d* 

:go-velocity *x* *y* *d* *&optional* *(msec 1000)* *&key* *(stop t)* *(wait)* 

:go-pos-unsafe *&rest* *args* 

:go-pos-unsafe-no-wait *x* *y* *&optional* *(d 0)* 

:go-pos-unsafe-wait 

:state *&rest* *args* 

:robot-interface-simulation-callback 


#### :change-inflation-range
&nbsp;&nbsp;&nbsp;*&optional* *(range 0.2)* *&key* *(node-name /move_base_node)* <br>&nbsp;&nbsp;&nbsp;*(costmap-name local_costmap)* <br>&nbsp;&nbsp;&nbsp;*(inflation-name inflation)* 

- Changes inflation range of local costmap for obstacle avoidance. <br>


#### :clear-costmap


- Send signal to clear costmap for obstacle avoidance to move_base and return t if succeeded. <br>


#### :move-trajectory
&nbsp;&nbsp;&nbsp;*x* *y* *d* *&optional* *(msec 1000)* *&key* *(stop t)* <br>&nbsp;&nbsp;&nbsp;*(start-time)* <br>&nbsp;&nbsp;&nbsp;*(send-action nil)* 

- x [m] y [m] d [rad] msec [milli second (default: 1000)] <br>
    stop [ stop after the base reached the goal if T (default: T)] <br>
    start-time [ robot starts to move from `start-time` (default: now) ] <br>
    send-action [ send the goal to action server if enabled, otherwise just returns goal without sending (default: nil) ] <br>


#### :move-trajectory-sequence
&nbsp;&nbsp;&nbsp;*trajectory-points* *time-list* *&key* *(stop t)* <br>&nbsp;&nbsp;&nbsp;*(start-time)* <br>&nbsp;&nbsp;&nbsp;*(send-action nil)* 

- Move base following the trajectory points at each time points <br>
    trajectory-points [ list of #f(x y d) ([m] for x, y; [rad] for d) ] <br>
    time-list [list of time span [msec] ] <br>
    stop [ stop after msec moveing ] <br>
    start-time [ robot will move at start-time [sec or ros::Time] ] <br>
    send-action [ send message to action server, it means robot will move ] <br>


:robot-interface-simulation-callback 

:state *&rest* *args* 

:go-pos-unsafe-wait 

:go-pos-unsafe-no-wait *x* *y* *&optional* *(d 0)* 

:go-pos-unsafe *&rest* *args* 

:go-velocity *x* *y* *d* *&optional* *(msec 1000)* *&key* *(stop t)* *(wait)* 

:send-cmd-vel-raw *x* *y* *d* 

:go-wait 

:go-pos-no-wait *x* *y* *&optional* *(d 0)* 

:go-pos *x* *y* *&optional* *(d 0)* 

:go-waitp 

:move-to-wait *&rest* *args* *&key* *(retry 10)* *(frame-id world)* *(correction t)* *&allow-other-keys* 

:move-to-send *coords* *&key* *(frame-id world)* *(wait-for-server-timeout 5)* *(count 0)* *&allow-other-keys* 

:move-to *coords* *&rest* *args* *&key* *(no-wait nil)* *&allow-other-keys* 

:make-plan *st-cds* *goal-cds* *&key* *(start-frame-id world)* *(goal-frame-id world)* 

:go-stop *&optional* *(force-stop t)* 

:odom-callback *msg* 

:init *&rest* *args* *&key* *(move-base-action-name move_base)* *((:base-frame-id base-frame-id-name) base_footprint)* *(base-controller-action-name /base_controller/follow_joint_trajectory)* *(base-controller-joint-names (list base_link_x base_link_y base_link_pan))* *((:cmd-vel-topi cmd-vel-topic-name) /base_controller/command)* *((:odom-topic odom-topic-name) /base_odometry/odom)* *&allow-other-keys* 


### ros-interface
- :super **robot-interface**
- :slots nil



:init *&rest* *args* 


:init *&rest* *args* 


### make-robot-interface-from-name ###
&nbsp;&nbsp;&nbsp;*name* *&rest* *args* 

- make a robot model from string: (make-robot-model "pr2") <br>


### init-robot-from-name ###
&nbsp;&nbsp;&nbsp;*robot-name* *&rest* *args* 

- call ${robot}-init function <br>


### clear-costmap ###
&nbsp;&nbsp;&nbsp;*&key* *(node-name /move_base_node)* 

- reset local costmap, clear unknown grid around robot and return t if succeeded <br>


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


shortest-angle *d0* *d1* 

joint-list->joint_state *jlist* *&key* *(position)* *(effort 0)* *(velocity 0)* 

apply-joint_state *jointstate* *robot* 

apply-trajectory_point *names* *trajpoint* *robot* 

apply-joint_trajectory *joint-trajectory* *robot* *&optional* *(offset 200.0)* 

