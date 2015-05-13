### ros::tf-cascaded-coords
- :super **cascaded-coords**
- :slots ros::frame-id ros::child-frame-id ros::stamp 



:init *&rest* *ros::initargs* *&key* *((:frame-id ros::fid) )* *((:child-frame-id ros::cid) )* *((:stamp ros::st) #i(0 0))* *&allow-other-keys* 

:frame-id *&optional* *id* 

:child-frame-id *&optional* *id* 

:stamp *&optional* *ros::st* 

:prin1 *&optional* *(ros::strm 1)* 


### ros::transformer
- :super **ros::object**
- :slots ros::cobject 



#### :init
&nbsp;&nbsp;&nbsp;*&optional* *(ros::interpolating t)* *(ros::cache-time 10.0)* 

- The Transformer object is the core of tf. It maintains a time-varying graph of transforms, and permits asynchronous graph modification and queries <br>
 <br>
$ (setq tr (instance ros::transformer :init)) <br>
#<ros::transformer #X545e290> <br>
$ (send tr :set-transform (instance ros::tf-cascaded-coords :init :pos #f(1 2 3) :frame-id "this_frame" :child-frame-id "child")) <br>
$ (send tr :get-frame-strings) <br>
("this_frame" "child") <br>
$ (send tr :lookup-transform "this_frame" "child" (ros::time)) <br>
#<cascaded-coords #X5438430 this_frame  1000.0 2000.0 3000.0 / 3.142 0.0 3.142> <br>
$ (send tr :lookup-transform "child"  "this_frame" (ros::time)) <br>
#<cascaded-coords #X54388e0 child  1000.0 -2000.0 3000.0 / 3.142 0.0 3.142> <br>


#### :all-frames-as-string


- Returns a string representing all frames <br>


#### :set-transform
&nbsp;&nbsp;&nbsp;*coords* *&optional* *(ros::auth )* 

- Adds a new transform to the Transformer graph from euslisp coordinates object <br>


#### :wait-for-transform
&nbsp;&nbsp;&nbsp;*ros::target-frame* *ros::source-frame* *ros::time* *ros::timeout* *&optional* *(ros::duration 0.01)* 

- Waits for the given transformation to become available. If the timeout occurs before the transformation becomes available <br>


#### :wait-for-transform-full
&nbsp;&nbsp;&nbsp;*ros::target-frame* *ros::target-time* *ros::source-frame* *ros::source-time* *ros::fixed-frame* *ros::timeout* *&optional* *(ros::duration 0.01)* 

- Extended version of :wait-for-transform <br>


#### :can-transform
&nbsp;&nbsp;&nbsp;*ros::target-frame* *ros::source-frame* *ros::time* 

- Returns True if the Transformer can determine the transform from source_frame to target_frame at time <br>


#### :can-transform-full
&nbsp;&nbsp;&nbsp;*ros::target-frame* *ros::target-time* *ros::source-frame* *ros::source-time* *ros::fixed-frame* 

- Extended version of can-transform <br>


#### :chain
&nbsp;&nbsp;&nbsp;*ros::target-frame* *ros::target-time* *ros::source-frame* *ros::source-time* *ros::fixed-frame* 

- returns the chain of frames connecting source_frame to target_frame. <br>


#### :clear


- Clear all transformations. <br>


#### :frame-exists
&nbsp;&nbsp;&nbsp;*ros::frame-id* 

- returns True if frame frame_id exists in the Transformer. <br>


#### :get-frame-strings


- returns all frame names in the Transformer as a list <br>


#### :get-latest-common-time
&nbsp;&nbsp;&nbsp;*ros::source-frame* *ros::target-frame* 

- Determines the most recent time for which Transformer can compute the transform between the two given frames. Raises tf.Exception if transformation is not possible. <br>


#### :lookup-transform
&nbsp;&nbsp;&nbsp;*ros::target-frame* *ros::source-frame* *ros::time* 

- Returns the transform from source_frame to target_frame at time. <br>


#### :lookup-transform-full
&nbsp;&nbsp;&nbsp;*ros::target-frame* *ros::target-time* *ros::source-frame* *ros::source-time* *ros::fixed-frame* 

- Extended version of :lookup-transform <br>


#### :get-parent
&nbsp;&nbsp;&nbsp;*ros::frame_id* *ros::time* 

- Fill the parent of a frame. <br>


#### :set-extrapolation-limit
&nbsp;&nbsp;&nbsp;*distance* 

- Set the distance which tf is allow to extrapolate. <br>



### ros::transform-listener
- :super **ros::transformer**
- :slots nil



#### :init
&nbsp;&nbsp;&nbsp;*&optional* *(ros::cache-time 10.0)* *(ros::spin-thread t)* 

- This class inherits from Transformer and automatically subscribes to ROS transform messages. <br>


#### :transform-pose
&nbsp;&nbsp;&nbsp;*ros::target-frame* *ros::pose-stamped* 

- Transform a geometry_msgs::PoseStamped into the target frame <br>


:dispose 


### ros::transform-broadcaster
- :super **ros::object**
- :slots ros::cobject 



#### :init


- Construnct transform broadcaster <br>


#### :send-transform
&nbsp;&nbsp;&nbsp;*coords* *ros::p-frame* *ros::c-frame* *&optional* *ros::tm* 

- Send a StampedTransform from euslisp coordinates, parent frame, frame_id, and time <br>



### ros::buffer-client
- :super **ros::object**
- :slots ros::cobject 



#### :init
&nbsp;&nbsp;&nbsp;*&key* *(ros::namespace tf2_buffer_server)* <br>&nbsp;&nbsp;&nbsp;*(ros::check-frequency 10.0)* <br>&nbsp;&nbsp;&nbsp;*(ros::timeout-padding 2.0)* 

- Construct tf2 buffer object <br>


#### :wait-for-server
&nbsp;&nbsp;&nbsp;*&optional* *(ros::timeout 5.0)* 

- Wait for tf2 buffer server <br>


#### :wait-for-transform
&nbsp;&nbsp;&nbsp;*ros::target-frame* *ros::source-frame* *ros::rostime* *ros::timeout* *&optional* *(ros::duration 0.05)* 

- Waits for the given transformation to become available. If the timeout occurs before the transformation becomes available <br>


#### :can-transform
&nbsp;&nbsp;&nbsp;*ros::target-frame* *ros::source-frame* *ros::rostime* *&optional* *(ros::timeout 0.0)* 

- Returns t if the Transformer can determine the transform from source_frame to target_frame at time. <br>


#### :lookup-transform
&nbsp;&nbsp;&nbsp;*ros::target-frame* *ros::source-frame* *ros::rostime* *&optional* *(ros::timeout 0.0)* 

- Returns the transform from source_frame to target_frame at time. <br>



### ros::pos->tf-point ###
&nbsp;&nbsp;&nbsp;*pos* 

- Convert euslisp pos to geoometry_msgs::Point, this also converts [mm] to [m] <br>


### ros::pos->tf-translation ###
&nbsp;&nbsp;&nbsp;*pos* 

- Convert euslisp pos to geoometry_msgs::Vector3, this also converts [mm] to [m] <br>


### ros::rot->tf-quaternion ###
&nbsp;&nbsp;&nbsp;*rot* 

- Convert euslisp rot to geoometry_msgs::Quaternion <br>


### ros::coords->tf-pose ###
&nbsp;&nbsp;&nbsp;*coords* 

- Convert euslisp coordinates to geoometry_msgs::Pose <br>


### ros::coords->tf-pose-stamped ###
&nbsp;&nbsp;&nbsp;*coords* *id* 

- Convert euslisp coordinates to geoometry_msgs::PoseStapmed <br>


### ros::coords->tf-transform ###
&nbsp;&nbsp;&nbsp;*coords* 

- Convert euslisp coordinates to geoometry_msgs::Transform <br>


### ros::coords->tf-transform-stamped ###
&nbsp;&nbsp;&nbsp;*coords* *id* *&optional* *(ros::child_id )* 

- Convert euslisp coordinates to geoometry_msgs::TransformStamped <br>


### ros::tf-point->pos ###
&nbsp;&nbsp;&nbsp;*ros::point* 

- Convert geoometry_msgs::Point to euslisp point vector, this aloso converts [m] to [mm] <br>


### ros::tf-quaternion->rot ###
&nbsp;&nbsp;&nbsp;*ros::quaternion* 

- Convert geoometry_msgs::quaternion to euslisp rotation matrix <br>


### ros::tf-pose->coords ###
&nbsp;&nbsp;&nbsp;*ros::pose* 

- Convert geoometry_msgs::Pose to euslisp coordinates <br>


### ros::tf-pose-stamped->coords ###
&nbsp;&nbsp;&nbsp;*ros::pose-stamped* 

- Convert geoometry_msgs::PoseStamped to euslisp coordinates <br>


### ros::tf-transform->coords ###
&nbsp;&nbsp;&nbsp;*ros::trans* 

- Convert geoometry_msgs::Transform to euslisp coordinates <br>


### ros::tf-transform-stamped->coords ###
&nbsp;&nbsp;&nbsp;*ros::trans* 

- Convert geoometry_msgs::TransformStamped to euslisp coordinates <br>


### ros::tf-twist->coords ###
&nbsp;&nbsp;&nbsp;*ros::twist* 

- Convert geoometry_msgs::Twist to euslisp coordinates <br>


### ros::create-quaternion-msg-from-rpy ###
&nbsp;&nbsp;&nbsp;*ros::roll* *ros::pitch* *ros::yaw* 

- Create geoometry_msgs::Quaternion from roll, pitch and yaw <br>


### ros::identity-quaternion ###


- Create identity geoemtry_msgs::Quaternion <br>


### ros::identity-pose ###


- Create identity geometry_msgs::Pose <br>


### ros::identity-pose-stamped ###
&nbsp;&nbsp;&nbsp;*&optional* *(ros::header (instance std_msgs::header :init))* 

- Create identity geometry_msgs::PoseStamped <br>


### ros::identity-transform ###


- Create identity geometry_msgs::Transform <br>


### ros::identity-transform-stamped ###
&nbsp;&nbsp;&nbsp;*&optional* *(ros::header (instance std_msgs::header :init))* 

- Create identity geometry_msgs::TransformStamped <br>


ros::create-identity-quaternion 

ros::create-quaternion-from-rpy *ros::roll* *ros::pitch* *ros::yaw* 

