### controller-action-client
- :super **ros::simple-action-client**
- :slots time-to-finish 

:init &rest args 

:time-to-finish nil

:action-feedback-cb msg 


### robot-interface
- :super **propertied-object**
- :slots robot objects robot-state joint-action-enable warningp controller-type controller-actions controller-timeout namespace controller-table visualization-topic joint-states-topic pub-joint-states-topic viewer groupname 

##### **:angle-vector** av &optional (tm nil) (ctype controller-type) (start-time 0) &key (scale 1) (min-time 1.0) 
- tm (time to goal in msec)
   if designated tm is faster than fastest speed, use fastest speed
   if not specified, it will use 1/scale of the fastest speed .
   if :fastest is specefied use fastest speed calcurated from max speed

##### **:eus-mannequin-mode** tmp-robot limb &key ((:time tm) 1000) ((:viewer vwr) *viewer*) 
- Euslisp version mannequin mode.
    In every loop, :angle-vector is continuously updated.
    If you want to stop this mode, press Enter.
    tmp-robot is argument for robot instance and angle-vector of tmp-robot is updated in this method.
    limb is mannequin mode limb such as :rarm, :larm, :rleg, and :lleg.
    tm is angle-vector update time [ms]. 1000 by default.

:init &rest args &key ((:robot r)) ((:objects objs)) (type :default-controller) (use-tf2) ((:groupname nh) robot_multi_queue) ((:namespace ns)) ((:joint-states-topic jst) joint_states) ((:publish-joint-states-topic pjst) nil) ((:controller-timeout ct) 3) ((:visuzlization-marker-topic vmt) robot_interface_marker_array) &allow-other-keys 

:add-controller ctype &key (joint-enable-check) (create-actions) 

:publish-joint-state &optional (joint-list (send robot :joint-list)) 

:angle-vector-safe av tm &key (simulation) (yes) (wait t) (norm-threshold 120) (angle-threshold 80) (velocity-threshold 240) 

:angle-vector-duration start end &optional (scale 1) (min-time 1.0) (ctype controller-type) 

:angle-vector-sequence avs &optional (tms (list 3000)) (ctype controller-type) (start-time 0.1) &key (scale 1) (min-time 0.0) 

:wait-interpolation &optional (ctype) (timeout 0) 

:interpolatingp &optional (ctype) 

:wait-interpolation-smooth time-to-finish &optional (ctype) 

:interpolating-smoothp time-to-finish &optional (ctype) 

:stop-motion &key (stop-time 0) 

:cancel-angle-vector &key ((:controller-actions ca) controller-actions) ((:controller-type ct) controller-type) (wait) 

:worldcoords nil

:torque-vector nil

:potentio-vector nil

:reference-vector nil

:actual-vector nil

:error-vector nil

:state-vector type &key ((:controller-actions ca) controller-actions) ((:controller-type ct) controller-type) 

:send-ros-controller action joint-names starttime trajpoints 

:set-robot-state1 key msg 

:ros-state-callback msg 

:wait-until-update-all-joints tgt-tm 

:update-robot-state &key (wait-until-update nil) 

:state &rest args 

:default-controller nil

:sub-angle-vector v0 v1 

:robot &rest args 

:viewer &rest args 

:objects &optional objs 

:draw-objects nil

:joint-action-enable &optional (e :dummy) 

:simulation-modep nil

:warningp &optional (w :dummy) 

:spin-once nil

:send-trajectory joint-trajectory-msg &key ((:controller-actions ca) controller-actions) ((:controller-type ct) controller-type) (starttime 1) &allow-other-keys 

:send-trajectory-each action joint-names traj &optional (starttime 0.2) 

:ros-wait tm &key (spin) (spin-self) (finish-check) &allow-other-keys 

:joint-trajectory-to-angle-vector-list move-arm joint-trajectory &key ((:diff-sum diff-sum) 0) ((:diff-thre diff-thre) 50) (show-trajectory t) (send-trajectory t) ((:speed-scale speed-scale) 1.0) &allow-other-keys 

:show-goal-hand-coords coords move-arm 

:find-descendants-dae-links l 

:show-mesh-traj-with-color link-body-list link-coords-list &key ((:lifetime lf) 20) (ns robot_traj) ((:color col) #f(0.5 0.5 0.5)) 

:nod &key (angle 40) (time 3000) 


### ros-interface
- :super **robot-interface**
- :slots nil

:init &rest args 


##### **make-robot-interface-from-name** name &rest args 
- make a robot model from string: (make-robot-model "pr2")

##### **init-robot-from-name** robot-name &rest args 
- call ${robot}-init function

##### **joint-list->joint_state** jlist &key (position) (effort 0) (velocity 0) 
- nil

##### **apply-joint_state** jointstate robot 
- nil

##### **apply-trajectory_point** names trajpoint robot 
- nil

##### **apply-joint_trajectory** joint-trajectory robot &optional (offset 200.0) 
- nil

