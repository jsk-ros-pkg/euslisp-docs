### ros::tf-cascaded-coords
- :super **cascaded-coords**
- :slots ros::frame-id ros::child-frame-id ros::stamp 

:init &rest ros::initargs &key ((:frame-id ros::fid) ) ((:child-frame-id ros::cid) ) ((:stamp ros::st) #i(0 0)) &allow-other-keys 

:frame-id &optional id 

:child-frame-id &optional id 

:stamp &optional ros::st 

:prin1 &optional (ros::strm 1) 


### ros::transformer
- :super **ros::object**
- :slots ros::cobject 

:init &optional (ros::interpolating t) (ros::cache-time 10.0) 

:all-frames-as-string nil

:set-transform coords &optional (ros::auth ) 

:wait-for-transform ros::target-frame ros::source-frame ros::time ros::timeout &optional (ros::duration 0.01) 

:wait-for-transform-full ros::target-frame ros::target-time ros::source-frame ros::source-time ros::fixed-frame ros::timeout &optional (ros::duration 0.01) 

:can-transform ros::target-frame ros::source-frame ros::time 

:can-transform-full ros::target-frame ros::target-time ros::source-frame ros::source-time ros::fixed-frame 

:chain ros::target-frame ros::target-time ros::source-frame ros::source-time ros::fixed-frame 

:clear nil

:frame-exists ros::frame-id 

:get-frame-strings nil

:get-latest-common-time ros::source-frame ros::target-frame 

:lookup-transform ros::target-frame ros::source-frame ros::time 

:lookup-transform-full ros::target-frame ros::target-time ros::source-frame ros::source-time ros::fixed-frame 

:lookup-velocity ros::reference-frame ros::moving-frame ros::time ros::duration 

:get-parent ros::frame_id ros::time 

:set-extrapolation-limit distance 


### ros::transform-listener
- :super **ros::transformer**
- :slots nil

:init &optional (ros::cache-time 10.0) (ros::spin-thread t) 

:dispose nil

:transform-pose ros::target-frame ros::pose-stamped 


### ros::transform-broadcaster
- :super **ros::object**
- :slots ros::cobject 

:init nil

:send-transform coords ros::p-frame ros::c-frame &optional ros::tm 


### ros::buffer-client
- :super **ros::object**
- :slots ros::cobject 

:init &key (ros::namespace tf2_buffer_server) (ros::check-frequency 10.0) (ros::timeout-padding 2.0) 

:wait-for-server &optional (ros::timeout 5.0) 

:wait-for-transform ros::target-frame ros::source-frame ros::rostime ros::timeout &optional (ros::duration 0.05) 

:can-transform ros::target-frame ros::source-frame ros::rostime &optional (ros::timeout 0.0) 

:lookup-transform ros::target-frame ros::source-frame ros::rostime &optional (ros::timeout 0.0) 


##### **ros::pos->tf-point** pos 
- nil

##### **ros::pos->tf-translation** pos 
- nil

##### **ros::rot->tf-quaternion** rot 
- nil

##### **ros::coords->tf-pose** coords 
- nil

##### **ros::coords->tf-pose-stamped** coords id 
- nil

##### **ros::coords->tf-transform** coords 
- nil

##### **ros::coords->tf-transform-stamped** coords id &optional (ros::child_id ) 
- nil

##### **ros::tf-point->pos** ros::point 
- nil

##### **ros::tf-quaternion->rot** ros::quaternion 
- nil

##### **ros::tf-pose->coords** ros::pose 
- nil

##### **ros::tf-pose-stamped->coords** ros::pose-stamped 
- nil

##### **ros::tf-transform->coords** ros::trans 
- nil

##### **ros::tf-transform-stamped->coords** ros::trans 
- nil

##### **ros::tf-twist->coords** ros::twist 
- nil

##### **ros::create-identity-quaternion** nil
- nil

##### **ros::create-quaternion-from-rpy** ros::roll ros::pitch ros::yaw 
- nil

##### **ros::create-quaternion-msg-from-rpy** ros::roll ros::pitch ros::yaw 
- nil

##### **ros::identity-quaternion** nil
- nil

##### **ros::identity-pose** nil
- nil

##### **ros::identity-pose-stamped** &optional (ros::header (instance std_msgs::header :init)) 
- nil

##### **ros::identity-transform** nil
- nil

##### **ros::identity-transform-stamped** &optional (ros::header (instance std_msgs::header :init)) 
- nil

