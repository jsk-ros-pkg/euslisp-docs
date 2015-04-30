### rtm-ros-robot-interface
- :super **robot-interface**
- :slots nil

##### **:force-vector** &optional (limb) 
- Returns :force-vector [N] list for all limbs obtained by :state.
    If a limb argument is specified, returns a vector for the limb.

##### **:moment-vector** &optional (limb) 
- Returns :moment-vector [Nm] list for all limbs obtained by :state.
    If a limb argument is specified, returns a vector for the limb.

##### **:off-force-vector** &optional (limb) 
- Returns offset-removed :force-vector [N] list for all limbs obtained by :state.
    This value corresponds to RemoveForceSensorLinkOffset RTC.
    If a limb argument is specified, returns a vector for the limb.

##### **:off-moment-vector** &optional (limb) 
- Returns offset-removed :moment-vector [Nm] list for all limbs obtained by :state.
    This value corresponds to RemoveForceSensorLinkOffset RTC.
    If a limb argument is specified, returns a vector for the limb.

##### **:reference-force-vector** &optional (limb) 
- Returns reference force-vector [N] list for all limbs obtained by :state.
    This value corresponds to StateHolder and SequencePlayer RTC.
    If a limb argument is specified, returns a vector for the limb.

##### **:reference-moment-vector** &optional (limb) 
- Returns reference moment-vector [Nm] list for all limbs obtained by :state.
    This value corresponds to StateHolder and SequencePlayer RTC.
    If a limb argument is specified, returns a vector for the limb.

##### **:absolute-force-vector** &optional (limb) 
- Returns offset-removed :force-vector [N] list for all limbs in world frame obtained by :state.
    This value corresponds to RemoveForceSensorLinkOffset RTC.
    If a limb argument is specified, returns a vector for the limb.

##### **:absolute-moment-vector** &optional (limb) 
- Returns offset-removed :moment-vector [Nm] list for all limbs in world frame obtained by :state.
    This value corresponds to RemoveForceSensorLinkOffset RTC.
    If a limb argument is specified, returns a vector for the limb.

##### **:zmp-vector** &optional (wrt :local) 
- Returns zmp vector [mm].
    If wrt is :local, returns zmp in the base-link frame. If wrt is :world, returns zmp in the world frame.

##### **:temperature-vector** nil
- Returns temperature vector.

##### **:motor-extra-data** nil
- Returns motor extra data. Please see iob definition for each system.

##### **:imucoords** nil
- Returns robot's coords based on imu measurement.

##### **:accel-vector** nil
- Returns acceleration [m/s2] of the acceleration sensor.

##### **:gyro-vector** nil
- Returns angular velocity [rad/s] of the gyro sensor.

##### **:state** &rest args 
- Obtains sensor and robot command topics using spin-once.

##### **:set-interpolation-mode** interpolation-mode 
- Set interpolation mode for SequencePlayer.

##### **:set-base-coords** base-coords tm 
- Set base coordinates in the world frame.
    base-coords is Euslisp coords and tm is [ms].

##### **:set-base-pos** base-pos tm 
- Set base pos in the world frame.
    base-pos is [mm] and tm is [ms].

##### **:set-base-rpy** base-rpy tm 
- Set base rpy in the world frame.
    base-rpy is [rad] and tm is [ms].

##### **:set-ref-forces-moments** force-list moment-list tm 
- Set reference wrenches. wrench-list is list of wrench ([N],[Nm]) for all end-effectors. tm is interpolation time [ms].

##### **:set-ref-forces** force-list tm &key (update-robot-state t) 
- Set reference forces. force-list is list of force ([N]) for all end-effectors. tm is interpolation time [ms].

##### **:set-ref-moments** moment-list tm &key (update-robot-state t) 
- Set reference moments. moment-list is list of moment ([Nm]) for all end-effectors. tm is interpolation time [ms].

##### **:set-ref-force** force tm &optional (limb :arms) &key (update-robot-state t) 
- Set reference force [N]. tm is interpolation time [ms].
    limb should be limb symbol name such as :rarm, :larm, :rleg, :lleg, :arms, or :legs.

##### **:set-ref-moment** moment tm &optional (limb :arms) &key (update-robot-state t) 
- Set reference moment [Nm]. tm is interpolation time [ms].
    limb should be limb symbol name such as :rarm, :larm, :rleg, :lleg, :arms, or :legs.

##### **:save-log** fname &key (set-robot-date-string t) 
- Save log files as [fname].[component_name]_[dataport_name].
    This method corresponds to DataLogger save().
    If set-robot-date-string is t, filename includes date string and robot name. By default, set-robot-date-string is t.

##### **:start-log** nil
- Start logging.
    This method corresponds to DataLogger clear().

##### **:set-log-maxlength** &optional (maxlength 4000) 
- Set max log length.
    This method corresponds to DataLogger maxLength().

##### **:start-impedance** limb &rest args 
- Start impedance controller mode.
    limb should be limb symbol name such as :rarm, :larm, :rleg, :lleg, :arms, or :legs.

##### **:stop-impedance** limb 
- Stop impedance controller mode.
    limb should be limb symbol name such as :rarm, :larm, :rleg, :lleg, :arms, or :legs.

##### **:set-impedance-controller-param** limb &rest args 
- Set impedance controller parameter like (send *ri* :set-impedance-controller-param :rarm :K-p 400).
    limb should be limb symbol name such as :rarm, :larm, :rleg, :lleg, :arms, or :legs.

##### **:get-impedance-controller-param** limb 
- Get impedance controller parameter.
    limb should be limb symbol name such as :rarm, :larm, :rleg, :lleg, :arms, or :legs.

##### **:get-impedance-controller-controller-mode** name 
- Get ImpedanceController ControllerMode as Euslisp symbol.

##### **:load-forcemoment-offset-params** filename 
- Load RMFO offset parameters from parameter file.
    This method corresponds to RemoveForceSensorLinkOffset loadForceMomentOffsetParams().

##### **:dump-forcemoment-offset-params** filename &key (set-robot-date-string t) 
- Save all RMFO offset parameters.
    This method corresponds to RemoveForceSensorLinkOffset dumpForceMomentOffsetParams().
    If set-robot-date-string is t, filename includes date string and robot name. By default, set-robot-date-string is t.

##### **:reset-force-moment-offset-arms** nil
- Remove force and moment offset for :rarm and :larm

##### **:reset-force-moment-offset** limbs 
- Remove force and moment offsets. limbs should be list of limb symbol name.

##### **:get-foot-step-params** nil
- Get AutoBalancer foot step params.

##### **:get-foot-step-param** param-name 
- Get AutoBalancer foot step param by given name.
    param-name is key word for parameters defined in IDL.
    param-name should be :rleg-coords :lleg-coords :support-leg-coords :swing-leg-coords :swing-leg-src-coords :swing-leg-dst-coords :dst-foot-midcoords :support-leg :support-leg-with-both.

##### **:set-foot-steps-roll-pitch** angle &key (axis :x) 
- Set foot steps with roll or pitch orientation.
    angle is roll or pitch angle [deg].
    axis is :x (roll) or :y (pitch).

##### **:calc-go-velocity-param-from-velocity-center-offset** ang velocity-center-offset 
- Calculate go-velocity velocities from rotation center and rotation angle.
    ang is rotation angle [rad]. velocity-center-offset is velocity center offset [mm] from foot mid coords.

##### **:get-gait-generator-orbit-type** nil
- Get GaitGenerator Orbit Type as Euslisp symbol.

##### **:get-auto-balancer-controller-mode** nil
- Get AutoBalancer ControllerMode as Euslisp symbol.

##### **:set-st-param** &rest args &key st-algorithm &allow-other-keys 
- Set Stabilizer parameters.
    To learn more about arguments, please see (pprint (find-method *ri* :raw-set-st-param)).

##### **:set-st-param-for-non-feedback-lip-mode** nil
- Set Stabilizer parameters to make robot Linear Inverted Pendulum mode without state feedback.
    By using this mode, robot feets adapt to ground surface.

##### **:set-default-st-param** nil
- Set Stabilzier parameter by default parameters.

##### **:set-st-param-by-ratio** state-feedback-gain-ratio damping-control-gain-ratio attitude-control-gain-ratio 
- Set Stabilzier parameter by ratio from default parameters.
    state-feedback-gain-ratio is ratio for state feedback gain.
    damping-control-gain-ratio is ratio for damping control gain.
    attitude-control-gain-ratio is ratio for attitude control gain.
    When ratio = 1, parameters are same as default parameters.
    For state-feedback-gain-ratio and attitude-control-gain-ratio,
    when ratio < 1 Stabilzier works less feedback control and less oscillation and when ratio > 1 more feedback and more oscillation.
    For dampnig-control-gain-ratio,
    when ratio > 1 feet behavior becomes hard and safe and when ratio < 1 soft and dangerous.

##### **:get-st-controller-mode** nil
- Get Stabilizer ControllerMode as Euslisp symbol.

##### **:get-st-algorithm** nil
- Get Stabilizer Algorithm as Euslisp symbol.

##### **:start-st** nil
- Start Stabilizer Mode.

##### **:stop-st** nil
- Stop Stabilizer Mode.

##### **:get-kalman-filter-algorithm** nil
- Get KalmanFilter Algorithm as Euslisp symbol.

:init &rest args 

:rtmros-motor-states-callback msg 

:rtmros-zmp-callback msg 

:rtmros-imu-callback msg 

:rtmros-force-sensor-callback fsensor-name msg 

:tmp-force-moment-vector-for-limb f/m fsensor-name &optional (topic-name-prefix nil) 

:tmp-force-moment-vector f/m &optional (limb) (topic-name-prefix nil) 

:define-all-rosbridge-srv-methods &key (debug-view nil) (ros-pkg-name hrpsys_ros_bridge) 

:get-rosbridge-fnames-from-type type-name &optional (ros-pkg-name hrpsys_ros_bridge) 

:get-rosbridge-idl-fnames &optional (ros-pkg-name hrpsys_ros_bridge) 

:get-rosbridge-srv-fnames &optional (ros-pkg-name hrpsys_ros_bridge) 

:get-rosbridge-method-def-macro rtc-name srv-name &optional (ros-pkg-name hrpsys_ros_bridge) 

:get-idl-enum-values value enum-type-string 

:calc-zmp-from-state &key (wrt :world) 

:get-robot-date-string nil

:set-base-pose &optional base-coords (tm 0.1) 

:wait-interpolation-of-group groupname 

:add-joint-group groupname jnames 

:remove-joint-group groupname 

:set-joint-angles-of-group groupname av tm 

:load-pattern basename &optional (tm 0.0) 

:wait-interpolation-seq nil

:sync-controller controller &optional (interpolation-time 5000) (blockp t) 

:set-tolerance &key (tolerance 0.1) (link-pair-name all) 

:start-collision-detection nil

:stop-collision-detection nil

:get-collision-status nil

:set-servo-gain-percentage name percentage 

:remove-force-sensor-offset nil

:set-servo-error-limit name limit 

:calibrate-inertia-sensor nil

:raw-get-impedance-controller-param name 

:raw-set-impedance-controller-param name &rest args &key m-p d-p k-p m-r d-r k-r force-gain moment-gain sr-gain avoid-gain reference-gain manipulability-limit controller-mode ik-optional-weight-vector 

:raw-start-impedance limb 

:force-sensor-method limb method-func method-name &rest args 

:raw-get-forcemoment-offset-param name 

:raw-set-forcemoment-offset-param name &rest args &key force-offset moment-offset link-offset-centroid link-offset-mass 

:set-forcemoment-offset-param limb &rest args 

:get-forcemoment-offset-param limb 

:load-forcemoment-offset-param fname &key (set-offset t) 

:_reset-force-moment-offset limbs f/m &key (itr 10) 

:get-gait-generator-param nil

:raw-set-gait-generator-param &rest args &key default-step-time default-step-height default-double-support-ratio stride-parameter default-orbit-type swing-trajectory-delay-time-offset stair-trajectory-way-point-offset gravitational-acceleration toe-pos-offset-x heel-pos-offset-x toe-zmp-offset-x heel-zmp-offset-x toe-angle heel-angle toe-heel-phase-ratio use-toe-joint 

:start-auto-balancer &key (limbs '(:rleg :lleg)) 

:stop-auto-balancer nil

:go-pos-no-wait xx yy th 

:go-pos xx yy th 

:raw-get-foot-step-param nil

:set-foot-steps-no-wait fs 

:set-foot-steps fs 

:set-foot-steps-with-param-no-wait fs sh 

:set-foot-steps-with-param fs sh 

:go-velocity vx vy vth 

:go-stop nil

:wait-foot-steps nil

:set-gait-generator-param &rest args &key default-orbit-type &allow-other-keys 

:print-gait-generator-orbit-type nil

:get-auto-balancer-param nil

:set-auto-balancer-param &key default-zmp-offsets move-base-gain graspless-manip-arm graspless-manip-mode graspless-manip-p-gain graspless-manip-reference-trans-pos graspless-manip-reference-trans-rot 

:abc-footstep->eus-footstep f 

:eus-footstep->abc-footstep f 

:cmd-vel-cb msg &key (vel-x-ratio 1.0) (vel-y-ratio 1.0) (vel-th-ratio 1.0) 

:cmd-vel-mode nil

:start-cmd-vel-mode nil

:stop-cmd-vel-mode nil

:set-soft-error-limit name limit 

:get-st-param nil

:raw-set-st-param &rest args &key k-tpcc-p k-tpcc-x k-brot-p k-brot-tc k-run-b d-run-b tdfke tdftc k-run-x k-run-y d-run-x d-run-y eefm-k1 eefm-k2 eefm-k3 eefm-zmp-delay-time-const eefm-ref-zmp-aux eefm-rot-damping-gain eefm-rot-time-const eefm-pos-damping-gain eefm-pos-time-const-support eefm-pos-time-const-swing eefm-pos-transition-time eefm-pos-margin-time eefm-leg-inside-margin eefm-leg-front-margin eefm-leg-rear-margin eefm-body-attitude-control-gain eefm-body-attitude-control-time-const eefm-cogvel-cutoff-freq eefm-wrench-alpha-blending eefm-gravitational-acceleration st-algorithm controller-mode 

:get-kalman-filter-param nil

:set-kalman-filter-param &rest args &key q-angle q-rate r-angle kf-algorithm 


##### **print-end-effector-parameter-conf-from-robot** rb 
- Print end effector setting for hrpsys conf file.

##### **dump-seq-pattern-file** rs-list output-basename &key (initial-sync-time 3.0) 
- Dump pattern file for SequencePlayer.
     rs-list : list of (list :time time0 :angle-vector av :root-coords rc ...).
               Fields other than :time and :angle-vector are optional.
     output-basename : output file (output-basename.pos, ...).
     root-coords : worldcoords for root link.
     zmp : world zmp[mm].
     wrench-list : world (list force-list moment-list) at end effector.

##### **load-from-seq-pattern-file** input-basename 
- Load from seq pattern file and generate robot state list.

##### **calculate-eefm-st-state-feedback-gain** default-cog-height &key (alpha -13.0) (beta -4.0) ((:time-constant tp) 0.04) ((:gravitational-acceleration ga) (* 0.001 (elt *g-vec* 2))) ((:print-mode pm) :euslisp) 
- Calculate EEFMe st state feedback gain (k1, k2, k3) and print them.
   default-cog-height is default COG height[mm].
   alpha and beta are poles. -13.0 and -4.0 by default.
   time-constant is time constant of ZMP tracking delay [s]. 0.04[s] by default.
   gravitational-acceleration is gravitational acceleration [m/s^2].
   When print-mode is :euslisp, print k1, k2, and k3 in Euslisp manner.
   When print-mode is :python, print k1, k2, and k3 in python hrpsys_config manner.

##### **calculate-eefm-st-state-feedback-default-gain-from-robot** robot &key (alpha -13.0) (beta -4.0) ((:time-constant tp) 0.04) ((:gravitational-acceleration ga) (* 0.001 (elt *g-vec* 2))) ((:print-mode pm) :euslisp) (exec-reset-pose-p t) 
- Calculate EEFMe st state feedback gain (k1, k2, k3) from robot model and print them.
   robot is robot model and calculate gains from reset-pose by default.

##### **calculate-toe-heel-pos-offsets** robot &key ((:print-mode pm) :euslisp) 
- Calculate toe and heel position offset in ee frame for toe heel contact used in GaitGenerator in AutoBalancer.
   robot is robot model.
   When print-mode is :euslisp, print parameters in Euslisp style.
   When print-mode is :python, print parameters in Python hrpsys_config style.

##### **calculate-toe-heel-zmp-offsets** robot &key ((:print-mode pm) :euslisp) 
- Calculate toe and heel zmp offset in ee frame for toe heel contact used in GaitGenerator in AutoBalancer.
   robot is robot model.
   When print-mode is :euslisp, print parameters in Euslisp style.
   When print-mode is :python, print parameters in Python hrpsys_config style.

##### **def-set-get-param-method** param-class set-param-method-name get-param-method-name set-param-idl-name get-param-idl-name &key (optional-args) (debug nil) 
- nil

##### **calculate-toe-heel-offsets** robot &key ((:print-mode pm) :euslisp) (pos-or-zmp :pos) 
- nil

##### **dump-project-file-by-cloning-euslisp-models** robot robot-file-path &key (object-models) (object-models-file-path) (nosim) (timestep 0.005) (dt 0.005) (output-fname (format nil /tmp/~A (send robot :name))) 
- nil

##### **gen-projectgenerator-joint-properties-string** robot 
- nil

##### **gen-projectgenerator-model-root-coords-string** obj 
- nil

