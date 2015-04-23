### exact-time-message-filter-callback-helper
- :super **propertied-object**
- :slots deligate topic 

##### **:init** topic-info &key deligate-object 
- topic-info := (topic message-type)
     deligate-object := exact-time-message-filter

##### **:callback** msg 
- just call :add-message method of deligate object


### exact-time-message-filter
- :super **propertied-object**
- :slots callback-helpers buffers buffer-length 

##### **:init** topics &key ((:buffer-length abuffer-length) 50) 
- topic := ((topic message-type) (topic message-type) ...)

##### **:add-message** topic-name message 
- add message to buffer and call callback if possible

##### **:call-callback-if-possible** stamp 
- call synchronized callback if messages can be found
with the same timestamp


##### **make-ros-msg-from-eus-pointcloud** pcloud &key (with-color :rgb) (with-normal t) (frame /sensor_frame) 
- convert from pointcloud in eus to sensor_msgs::PointCloud2 in ros

##### **make-eus-pointcloud-from-ros-msg** msg &key (pcloud) (remove-nan) 
- convert from sensor_msgs::PointCloud2 in ros to pointcloud in eus

##### **ros::set-dynamic-reconfigure-param** srv name type value 
- set dynamic_reconfugre parameter.

##### **print-ros-msg** msg &optional (rp (find-package ROS)) (ro ros::object) (nest 0) (padding   ) 
- nil

##### **make-camera-from-ros-camera-info-aux** pwidth pheight p frame-coords &rest args 
- nil

##### **make-camera-from-ros-camera-info** msg 
- nil

##### **make-msg-from-3dpointcloud** points-list &key color-list (frame /sensor_frame) 
- nil

##### **make-eus-pointcloud-from-ros-msg1** sensor_msgs_pointcloud 
- nil

##### **vector->rgba** cv &optional (alpha 1.0) 
- nil

##### **cylinder->marker-msg** cyl header &key ((:color col) (float-vector 1.0 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ns lifetime 
- nil

##### **cube->marker-msg** cb header &key ((:color col) (float-vector 1.0 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ns lifetime 
- nil

##### **sphere->marker-msg** sp header &key ((:color col) (float-vector 1.0 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ns lifetime 
- nil

##### **line->marker-msg** li header &key ((:color col) (float-vector 1 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ((:scale sc) 10.0) ns lifetime 
- nil

##### **line-list->marker-msg** li header &key ((:color col) (float-vector 1 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ((:scale sc) 10.0) ns lifetime 
- nil

##### **faces->marker-msg** faces header &key ((:color col) (float-vector 1 0 0)) ((:id idx) 0) ns lifetime 
- nil

##### **object->marker-msg** obj header &key coords ((:color col) (float-vector 1 1 1)) ((:alpha a) 1.0) ((:id idx) 0) ns lifetime 
- nil

##### **wireframe->marker-msg** wf header &rest args 
- nil

##### **text->marker-msg** str c header &key ((:color col) (float-vector 1 1 1)) ((:alpha a) 1.0) ((:id idx) 0) ((:scale sc) 100.0) ns lifetime 
- nil

##### **coords->marker-msg** coords header &key (size 100) (width 10) (id 0) ns lifetime 
- nil

##### **mesh->marker-msg** cds mesh_resource header &key ((:color col) (float-vector 1 1 1)) ((:scale sc) 1000) ((:id idx) 0) ((:mesh_use_embedded_materials use_embedded) t) (alpha 1.0) ns lifetime 
- nil

##### **pointcloud->marker-msg** pc header &key ((:color col) (float-vector 1.0 0 0)) ((:alpha a) 1.0) (use-color) (point-width 0.01) (point-height 0.01) ((:id idx) 0) ns lifetime 
- nil

##### **eusobj->marker-msg** eusgeom header &rest args &key (wireframe) &allow-other-keys 
- nil

##### **arrow->marker-msg** arg header &key ((:color col) (float-vector 1.0 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ((:scale sc) nil) ns lifetime 
- nil

##### **marker-msg->shape** msg &rest args 
- nil

##### **marker-msg->shape/cube** msg 
- nil

##### **marker-msg->shape/cylinder** msg 
- nil

##### **marker-msg->shape/sphere** msg 
- nil

##### **marker-msg->shape/cube_list** msg &key ((:pointcloud pt)) 
- nil

##### **dump-pointcloud-to-pcd-file** fname pcloud &key (rgb :rgb) (binary) (scale 0.001) 
- nil

##### **read-header-symbol** str sym 
- nil

##### **read-pcd-file** fname &key (scale 1000.0) 
- nil

##### **call-empty-service** srvname &key (wait nil) 
- nil

##### **one-shot-subscribe** topic-name mclass &key (timeout) (after-stamp) (unsubscribe t) 
- nil

##### **one-shot-publish** topic-name msg &key (wait 500) (after-stamp) (unadvertise t) 
- nil

