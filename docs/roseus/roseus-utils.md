### exact-time-message-filter-callback-helper
- :super **propertied-object**
- :slots deligate topic 



#### :init
&nbsp;&nbsp;&nbsp;*topic-info* *&key* *deligate-object* 

- topic-info := (topic message-type) <br>
     deligate-object := exact-time-message-filter <br>


#### :callback
&nbsp;&nbsp;&nbsp;*msg* 

- just call :add-message method of deligate object <br>



### exact-time-message-filter
- :super **propertied-object**
- :slots callback-helpers buffers buffer-length 

Exact time message filter synchronizes incoming channels by the timestamps contained in their headers, and outputs them in the form of a single callback that takes the same number of channels. This filter requres messages to have exactly the same timestamp in order to match. Your callback is only called if a message has been received on all specified channels with the same exact timestamp. <br>
 <br>
topic := ((topic message-type) (topic message-type) ...) <br>
 <br>

        ;; sample
        (defclass image-caminfo-synchronizer
          :super exact-time-message-filter)
        
        (defmethod image-caminfo-synchronizer
          (:callback (image caminfo)
        	(print (list image caminfo))
        	(print (send-all (list image caminfo) :header :stamp))
        	))
        (ros::roseus "hoge")
        (ros::roseus-add-msgs "sensor_msgs")
        ;; test
        (setq hoge (instance image-caminfo-synchronizer :init
        		 (list (list "/multisense/left/image_rect_color" sensor_msgs::Image)
        		   (list "/multisense/left/camera_info" sensor_msgs::CameraInfo))))
        (ros::spin)


#### :init
&nbsp;&nbsp;&nbsp;*topics* *&key* *((:buffer-length abuffer-length) 50)* 

- Create exact-time-message-filter instance <br>


#### :add-message
&nbsp;&nbsp;&nbsp;*topic-name* *message* 

- add message to buffer and call callback if possible <br>


#### :call-callback-if-possible
&nbsp;&nbsp;&nbsp;*stamp* 

- call synchronized callback if messages can be found <br>
with the same timestamp <br>



### make-camera-from-ros-camera-info ###
&nbsp;&nbsp;&nbsp;*msg* 

- Make camera model from ros sensor_msgs::CameraInfo message <br>


### make-msg-from-3dpointcloud ###
&nbsp;&nbsp;&nbsp;*points-list* *&key* *color-list* <br>&nbsp;&nbsp;&nbsp;*(frame /sensor_frame)* 

- Make sensor_msgs::PointCloud from list of 3d point <br>


### vector->rgba ###
&nbsp;&nbsp;&nbsp;*cv* *&optional* *(alpha 1.0)* 

- Convert vector to std_msgs::ColorRGBA <br>


### cylinder->marker-msg ###
&nbsp;&nbsp;&nbsp;*cyl* *header* *&key* *((:color col) (float-vector 1.0 0 0))* <br>&nbsp;&nbsp;&nbsp;*((:alpha a) 1.0)* <br>&nbsp;&nbsp;&nbsp;*((:id idx) 0)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* 

- Convert cylinder object to visualization_msgs::Marker <br>


### cube->marker-msg ###
&nbsp;&nbsp;&nbsp;*cb* *header* *&key* *((:color col) (float-vector 1.0 0 0))* <br>&nbsp;&nbsp;&nbsp;*((:alpha a) 1.0)* <br>&nbsp;&nbsp;&nbsp;*((:id idx) 0)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* 

- Convert cube object to visualization_msgs::Marker <br>


### sphere->marker-msg ###
&nbsp;&nbsp;&nbsp;*sp* *header* *&key* *((:color col) (float-vector 1.0 0 0))* <br>&nbsp;&nbsp;&nbsp;*((:alpha a) 1.0)* <br>&nbsp;&nbsp;&nbsp;*((:id idx) 0)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* 

- Convert shpere object to visualization_msgs::Marker <br>


### line->marker-msg ###
&nbsp;&nbsp;&nbsp;*li* *header* *&key* *((:color col) (float-vector 1 0 0))* <br>&nbsp;&nbsp;&nbsp;*((:alpha a) 1.0)* <br>&nbsp;&nbsp;&nbsp;*((:id idx) 0)* <br>&nbsp;&nbsp;&nbsp;*((:scale sc) 10.0)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* 

- Convert line object to visualization_msgs::Marker <br>


### line-list->marker-msg ###
&nbsp;&nbsp;&nbsp;*li* *header* *&key* *((:color col) (float-vector 1 0 0))* <br>&nbsp;&nbsp;&nbsp;*((:alpha a) 1.0)* <br>&nbsp;&nbsp;&nbsp;*((:id idx) 0)* <br>&nbsp;&nbsp;&nbsp;*((:scale sc) 10.0)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* 

- Convert list of line object to visualization_msgs::Marker <br>


### faces->marker-msg ###
&nbsp;&nbsp;&nbsp;*faces* *header* *&key* *((:color col) (float-vector 1 0 0))* <br>&nbsp;&nbsp;&nbsp;*((:id idx) 0)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* 

- Convert face objects to visualization_msgs::Marker <br>


### object->marker-msg ###
&nbsp;&nbsp;&nbsp;*obj* *header* *&key* *coords* <br>&nbsp;&nbsp;&nbsp;*((:color col) (float-vector 1 1 1))* <br>&nbsp;&nbsp;&nbsp;*((:alpha a) 1.0)* <br>&nbsp;&nbsp;&nbsp;*((:id idx) 0)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* <br>&nbsp;&nbsp;&nbsp;*rainbow* 

- Convert euslisp body object to visualization_msgs::Marker <br>


### wireframe->marker-msg ###
&nbsp;&nbsp;&nbsp;*wf* *header* *&rest* *args* 

- Convert euslisp object to visualization_msgs::Marker as wireframe <br>


### text->marker-msg ###
&nbsp;&nbsp;&nbsp;*str* *c* *header* *&key* *((:color col) (float-vector 1 1 1))* <br>&nbsp;&nbsp;&nbsp;*((:alpha a) 1.0)* <br>&nbsp;&nbsp;&nbsp;*((:id idx) 0)* <br>&nbsp;&nbsp;&nbsp;*((:scale sc) 100.0)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* 

- Convert text to visualization_msgs::Marker <br>


### coords->marker-msg ###
&nbsp;&nbsp;&nbsp;*coords* *header* *&key* *(size 100)* <br>&nbsp;&nbsp;&nbsp;*(width 10)* <br>&nbsp;&nbsp;&nbsp;*(id 0)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* 

- Convert coordinates to visualization_msgs::Marker <br>


### mesh->marker-msg ###
&nbsp;&nbsp;&nbsp;*cds* *mesh_resource* *header* *&key* *((:color col) (float-vector 1 1 1))* <br>&nbsp;&nbsp;&nbsp;*((:scale sc) 1000)* <br>&nbsp;&nbsp;&nbsp;*((:id idx) 0)* <br>&nbsp;&nbsp;&nbsp;*((:mesh_use_embedded_materials use_embedded) t)* <br>&nbsp;&nbsp;&nbsp;*(alpha 1.0)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* 

- Convert mesh resources to visualization_msgs::Marker <br>


### pointcloud->marker-msg ###
&nbsp;&nbsp;&nbsp;*pc* *header* *&key* *((:color col) (float-vector 1.0 0 0))* <br>&nbsp;&nbsp;&nbsp;*((:alpha a) 1.0)* <br>&nbsp;&nbsp;&nbsp;*(use-color)* <br>&nbsp;&nbsp;&nbsp;*(point-width 0.01)* <br>&nbsp;&nbsp;&nbsp;*(point-height 0.01)* <br>&nbsp;&nbsp;&nbsp;*((:id idx) 0)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* 

- Convert pointcloud data to visualization_msgs::Marker <br>


### eusobj->marker-msg ###
&nbsp;&nbsp;&nbsp;*eusgeom* *header* *&rest* *args* *&key* *(wireframe)* <br>&nbsp;&nbsp;&nbsp;*&allow-other-keys* 

- Convert any euslisp object to visualization_msgs::Marker <br>


### arrow->marker-msg ###
&nbsp;&nbsp;&nbsp;*arg* *header* *&key* *((:color col) (float-vector 1.0 0 0))* <br>&nbsp;&nbsp;&nbsp;*((:alpha a) 1.0)* <br>&nbsp;&nbsp;&nbsp;*((:id idx) 0)* <br>&nbsp;&nbsp;&nbsp;*((:scale sc) nil)* <br>&nbsp;&nbsp;&nbsp;*ns* <br>&nbsp;&nbsp;&nbsp;*lifetime* 

- Convert arrow object to visualization_msgs::Marker <br>


### marker-msg->shape ###
&nbsp;&nbsp;&nbsp;*msg* *&rest* *args* 

- Convert visualization_msgs::Marker to euslisp object <br>


### marker-msg->shape/cube ###
&nbsp;&nbsp;&nbsp;*msg* 

- Convert visualization_msgs::Marker to euslisp cube object <br>


### marker-msg->shape/cylinder ###
&nbsp;&nbsp;&nbsp;*msg* 

- Convert visualization_msgs::Marker to euslisp cylinder object <br>


### marker-msg->shape/sphere ###
&nbsp;&nbsp;&nbsp;*msg* 

- Convert visualization_msgs::Marker to euslisp sphere object <br>


### marker-msg->shape/cube_list ###
&nbsp;&nbsp;&nbsp;*msg* *&key* *((:pointcloud pt))* 

- Convert visualization_msgs::Marker to eusliss list of cube objects <br>


### make-ros-msg-from-eus-pointcloud ###
&nbsp;&nbsp;&nbsp;*pcloud* *&key* *(with-color :rgb)* <br>&nbsp;&nbsp;&nbsp;*(with-normal t)* <br>&nbsp;&nbsp;&nbsp;*(frame /sensor_frame)* 

- Convert from pointcloud in eus to sensor_msgs::PointCloud2 in ros <br>


### make-eus-pointcloud-from-ros-msg ###
&nbsp;&nbsp;&nbsp;*msg* *&key* *(pcloud)* <br>&nbsp;&nbsp;&nbsp;*(remove-nan)* 

- Convert from sensor_msgs::PointCloud2 in ros to pointcloud in eus <br>


### dump-pointcloud-to-pcd-file ###
&nbsp;&nbsp;&nbsp;*fname* *pcloud* *&key* *(rgb :rgb)* <br>&nbsp;&nbsp;&nbsp;*(binary)* <br>&nbsp;&nbsp;&nbsp;*(scale 0.001)* 

- Write pointcloud data to pcd file <br>


### read-pcd-file ###
&nbsp;&nbsp;&nbsp;*fname* *&key* *(scale 1000.0)* 

- Write read pcd file and construct pointcloud object <br>


### call-empty-service ###
&nbsp;&nbsp;&nbsp;*srvname* *&key* *(wait nil)* 

- Call empty service <br>


### one-shot-subscribe ###
&nbsp;&nbsp;&nbsp;*topic-name* *mclass* *&key* *(timeout)* <br>&nbsp;&nbsp;&nbsp;*(after-stamp)* <br>&nbsp;&nbsp;&nbsp;*(unsubscribe t)* 

- Subscribe message, just for once <br>


### one-shot-publish ###
&nbsp;&nbsp;&nbsp;*topic-name* *msg* *&key* *(wait 500)* <br>&nbsp;&nbsp;&nbsp;*(after-stamp)* <br>&nbsp;&nbsp;&nbsp;*(unadvertise t)* 

- Publish message just for once <br>


### ros::set-dynamic-reconfigure-param ###
&nbsp;&nbsp;&nbsp;*srv* *name* *type* *value* 

- set dynamic_reconfugre parameter. <br>


print-ros-msg *msg* *&optional* *(rp (find-package ROS))* *(ro ros::object)* *(nest 0)* *(padding   )* 

make-camera-from-ros-camera-info-aux *pwidth* *pheight* *p* *frame-coords* *&rest* *args* 

make-eus-pointcloud-from-ros-msg1 *sensor_msgs_pointcloud* 

read-header-symbol *str* *sym* 

