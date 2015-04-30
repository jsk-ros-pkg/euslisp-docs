### pr2-interface
- :super **robot-interface**
- :slots r-gripper-action l-gripper-action move-base-action move-base-trajectory-action finger-pressure-origin 

##### **:move-trajectory-sequence** trajectory-points time-list &key (stop t) (start-time) (send-action nil) 
- trajectory-points [ list of #f(x y d) ] time-list [list of time span]
stop [ stop after msec moveing ]
start-time [ robot will move at start-time ]
send-action [ send message to action server, it means robot will move ]

##### **:move-trajectory** x y d &optional (msec 1000) &key (stop t) (start-time) (send-action nil) 
-  x [m/sec] y [m/sec] d [rad/sec] msec [milli second]
stop [ stop after msec moveing ]
start-time [ robot will move at start-time ]
send-action [ send message to action server, it means robot will move ]

:init &rest args &key (type :default-controller) (move-base-action-name move_base) &allow-other-keys 

:pr2-odom-callback msg 

:state &rest args 

:publish-joint-state nil

:wait-interpolation &rest args 

:larm-controller nil

:rarm-controller nil

:head-controller nil

:torso-controller nil

:default-controller nil

:midbody-controller nil

:fullbody-controller nil

:controller-angle-vector av tm type 

:larm-angle-vector av tm 

:rarm-angle-vector av tm 

:head-angle-vector av tm 

:move-gripper arm pos &key (effort 25) (wait t) 

:start-grasp &optional (arm :arms) &key ((:gain g) 0.01) ((:objects objs) objects) 

:stop-grasp &optional (arm :arms) &key (wait nil) 

:pr2-fingertip-callback arm msg 

:reset-fingertip nil

:finger-pressure arm &key (zero nil) 

:go-stop &optional (force-stop t) 

:make-plan st-cds goal-cds &key (start-frame-id /world) (goal-frame-id /world) 

:move-to coords &key (retry 10) (frame-id /world) (wait-for-server-timeout 5) 

:go-pos x y &optional (d 0) 

:go-velocity x y d &optional (msec 1000) &key (stop t) (wait) 

:go-pos-unsafe x y &optional (d 0) 

:wait-torso &optional (timeout 0) 

:check-continuous-joint-move-over-180 diff-av 

:angle-vector av &optional (tm 3000) &rest args 

:angle-vector-sequence avs &optional (tms (list 3000)) &rest args 

:angle-vector-with-constraint av1 &optional (tm 3000) (arm :arms) &key (rotation-axis t) (translation-axis t) &rest args 


##### **speak-jp** jp-str 
- nil

##### **speak-en** en-str &key (google nil) (wait nil) 
- nil

##### **pr2-init** &optional (create-viewer) 
- nil

##### **get-tuckarm** free-arm dir arm 
- nil

##### **check-tuckarm-pose** &key (thre 20) &rest args 
- nil

##### **pr2-tuckarm-pose** &rest args 
- nil

##### **pr2-reset-pose** nil
- nil

##### **clear-costmap** nil
- nil

##### **change-inflation-range** &optional (range 0.55) 
- nil

##### **use-tilt-laser-obstacle-cloud** enable 
- nil

