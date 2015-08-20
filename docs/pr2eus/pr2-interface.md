### pr2-interface
- :super **robot-interface**
- :slots r-gripper-action l-gripper-action move-base-action move-base-trajectory-action finger-pressure-origin move-base-goal-msg move-base-goal-coords move-base-goal-map-to-frame go-pos-unsafe-goal-msg current-goal-coords 



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


:init *&rest* *args* *&key* *(type :default-controller)* *(move-base-action-name move_base)* *&allow-other-keys* 

:pr2-odom-callback *msg* 

:state *&rest* *args* 

:publish-joint-state 

:wait-interpolation *&rest* *args* 

:larm-controller 

:rarm-controller 

:head-controller 

:torso-controller 

:default-controller 

:midbody-controller 

:fullbody-controller 

:controller-angle-vector *av* *tm* *type* 

:larm-angle-vector *av* *tm* 

:rarm-angle-vector *av* *tm* 

:head-angle-vector *av* *tm* 

:move-gripper *arm* *pos* *&key* *(effort 25)* *(wait t)* 

:start-grasp *&optional* *(arm :arms)* *&key* *((:gain g) 0.01)* *((:objects objs) objects)* 

:stop-grasp *&optional* *(arm :arms)* *&key* *(wait nil)* 

:pr2-fingertip-callback *arm* *msg* 

:reset-fingertip 

:finger-pressure *arm* *&key* *(zero nil)* 

:go-stop *&optional* *(force-stop t)* 

:make-plan *st-cds* *goal-cds* *&key* *(start-frame-id /world)* *(goal-frame-id /world)* 

:move-to *coords* *&rest* *args* *&key* *(no-wait nil)* *&allow-other-keys* 

:move-to-send *coords* *&key* *(frame-id /world)* *(wait-for-server-timeout 5)* *(count 0)* *&allow-other-keys* 

:move-to-wait *&rest* *args* *&key* *(retry 10)* *(frame-id /world)* *&allow-other-keys* 

:go-pos *x* *y* *&optional* *(d 0)* 

:go-pos-no-wait *x* *y* *&optional* *(d 0)* 

:go-wait 

:go-velocity *x* *y* *d* *&optional* *(msec 1000)* *&key* *(stop t)* *(wait)* 

:go-pos-unsafe *&rest* *args* 

:go-pos-unsafe-no-wait *x* *y* *&optional* *(d 0)* 

:go-pos-unsafe-wait 

:wait-torso *&optional* *(timeout 0)* 

:robot-interface-simulation-callback 

:check-continuous-joint-move-over-180 *diff-av* 

:angle-vector *av* *&optional* *(tm 3000)* *&rest* *args* 

:angle-vector-sequence *avs* *&optional* *(tms (list 3000))* *&rest* *args* 

:angle-vector-with-constraint *av1* *&optional* *(tm 3000)* *(arm :arms)* *&key* *(rotation-axis t)* *(translation-axis t)* *&rest* *args* 


speak-jp *jp-str* 

speak-en *en-str* *&key* *(google nil)* *(wait nil)* 

pr2-init *&optional* *(create-viewer)* 

get-tuckarm *free-arm* *dir* *arm* 

check-tuckarm-pose *&key* *(thre 20)* *&rest* *args* 

pr2-tuckarm-pose *&rest* *args* 

pr2-reset-pose 

clear-costmap 

change-inflation-range *&optional* *(range 0.55)* 

use-tilt-laser-obstacle-cloud *enable* 

