### pr2-interface
- :super **robot-move-base-interface**
- :slots r-gripper-action l-gripper-action finger-pressure-origin 



#### :gripper
&nbsp;&nbsp;&nbsp;*&rest* *args* 

- get information of gripper <br>
Arguments: <br>
 - arm (:larm :rarm :arms) <br>
 - type (:position :velocity :pressure) <br>
Example: (send self :gripper :rarm :position) => 0.00 <br>


:init *&rest* *args* *&key* *(type :default-controller)* *&allow-other-keys* 

:pr2-odom-callback *msg* 

:state *&rest* *args* 

:publish-joint-state 

:wait-interpolation *&optional* *(ctype)* *(timeout 0)* 

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

:pr2-gripper-state-callback *arm* *msg* 

:pr2-fingertip-callback *arm* *msg* 

:reset-fingertip 

:finger-pressure *arm* *&key* *(zero nil)* 

:wait-torso *&optional* *(timeout 0)* 

:robot-interface-simulation-callback 

:check-continuous-joint-move-over-180 *diff-av* 

:angle-vector *av* *&optional* *(tm 3000)* *&rest* *args* 

:angle-vector-sequence *avs* *&optional* *(tms (list 3000))* *&rest* *args* 

:angle-vector-with-constraint *av1* *&optional* *(tm 3000)* *(arm :arms)* *&key* *(rotation-axis t)* *(translation-axis t)* *&rest* *args* 


send-speak-msg *msg* *&key* *(topic-name robotsound)* *(timeout 0)* *(wait *speak-wait*)* 

speak-google *str* *&key* *(lang :ja)* *(wait *speak-wait*)* *(topic-name robotsound)* *(timeout 20)* 

speak-jp *str* *&key* *google* *(wait *speak-wait*)* *(topic-name robotsound_jp)* *(timeout 20)* 

speak-en *str* *&key* *google* *(wait *speak-wait*)* *(topic-name robotsound)* *(timeout 20)* 

pr2-init *&optional* *(create-viewer)* 

get-tuckarm *free-arm* *dir* *arm* 

check-tuckarm-pose *&key* *(thre 20)* *&rest* *args* 

pr2-tuckarm-pose *&rest* *args* 

pr2-reset-pose 

clear-costmap 

change-inflation-range *&optional* *(range 0.2)* 

use-tilt-laser-obstacle-cloud *enable* 

