### ros::object
- :super **propertied-object**
- :slots nil



:init 

:md5sum- 

:datatype- 


### ros::time
- :super **ros::object**
- :slots ros::sec-nsec 



#### :init
&nbsp;&nbsp;&nbsp;*&key* *((:sec ros::_sec) 0)* <br>&nbsp;&nbsp;&nbsp;*((:nsec ros::_nsec) 0)* 

- ros::time represents ROS time primitive type, which consits of two integers, seconds and nanoseconds <br>


#### :from-sec
&nbsp;&nbsp;&nbsp;*ros::sec* 

- create new ros::time instance from a float seconds representation <br>


:sec *&optional* *ros::s* 

:nsec *&optional* *ros::s* 

:sec-nsec 

:now 

:to-sec 

:to-nsec 

:from-nsec *ros::nsec* 

:prin1 *&optional* *(ros::strm t)* *&rest* *ros::msgs* 


### ros::rate ###


- frequency <br>
 <br>
Construct ros timer for periodic sleeps <br>


### ros::ros-error ###


- write mesage to error output <br>


### ros::get-host ###


- Get the hostname where the master runs. <br>


### ros::get-uri ###


- Get the full URI to the master  <br>


### ros::ok ###


- Check whether it's time to exit.  <br>


### ros::spin ###


- Enter simple event loop <br>


### ros::ros-debug ###


- write mesage to debug output <br>
 <br>

        (ros::ros-debug "this is error ~A" 0)


### ros::unsubscribe ###


- topicname <br>
 <br>
Unsubscribe topic <br>


### ros::get-name ###


- Returns current node name <br>


### ros::unadvertise ###


- Unadvertise topic <br>


### ros::get-namespace ###


- Returns current node name space <br>


### ros::spin-once ###


- &optional groupname  ;; spin only group <br>
 <br>
Process a single round of callbacks. <br>


### ros::ros-info ###


- write mesage to info output <br>


### ros::resolve-name ###


- Returns ros resolved name <br>


### ros::get-topic-publisher ###


- topicname <br>
 <br>
Retuns the name of topic if it already published <br>


### ros::create-nodehandle ###


- groupname &optional namespace  ;; <br>
 <br>
Create ros NodeHandle with given group name.  <br>
 <br>

        (ros::roseus "test")
        (ros::create-node-handle "mygroup")
        (ros::subscribe "/test" std_msgs::String #'(lambda (m) (print m)) :groupname "mygroup")
        (while (ros::ok)  (ros::spin-once "mygroup"))


### ros::subscribe ###


- topicname message_type callbackfunc args0 ... argsN &optional queuesize %key (:groupname groupname) <br>
 <br>
Subscribe to a topic, version for class member function with bare pointer. <br>
This method connects to the master to register interest in a given topic. The node will automatically be connected with publishers on this topic. On each message receipt, fp is invoked and passed a shared pointer to the message received. This message should not be changed in place, as it is shared with any other subscriptions to this topic. <br>
 <br>
This version of subscribe is a convenience function for using function, member function, lmabda function: <br>

        ;; callback function
        (defun string-cb (msg) (print (list 'cb (sys::thread-self) (send msg :data))))
        (ros::subscribe "chatter" std_msgs::string #'string-cb)
        ;; lambda function
        (ros::subscribe "chatter" std_msgs::string
                        #'(lambda (msg) (ros::ros-info
                                         (format nil "I heard ~A" (send msg :data)))))
        ;; method call
        (defclass string-cb-class
          :super propertied-object
          :slots ())
        (defmethod string-cb-class
          (:init () (ros::subscribe "chatter" std_msgs::string #'send self :string-cb))
          (:string-cb (msg) (print (list 'cb self (send msg :data)))))
        (setq m (instance string-cb-class :init))


### ros::advertise ###


- topic message_class &optional queuesize latch <br>
Advertise a topic. <br>
This call connects to the master to publicize that the node will be publishing messages on the given topic. This method returns a Publisher that allows you to publish a message on this topic. <br>

        (ros::advertise "chatter" std_msgs::string 1)


### ros::get-topic-subscriber ###


- topicname <br>
 <br>
Retuns the name of topic if it already subscribed <br>


### ros::get-topics ###


- Get the list of topics that are being published by all nodes. <br>


### ros::advertise-service ###


- servicename message_type callback function <br>
 <br>
Advertise a service <br>

        (ros::advertise-service "add_two_ints" roseus::AddTwoInts #'add-two-ints)


### ros::unadvertise-service ###


- Unadvertise service <br>


### ros::get-port ###


- Get the port where the master runs. <br>


### ros::get-num-publishers ###


- Returns the number of publishers this subscriber is connected to.  <br>


### ros::ros-fatal ###


- write mesage to fatal output <br>


### ros::service-exists ###


- servicename <br>
 <br>
Checks if a service is both advertised and available. <br>


### ros::wait-for-service ###


- servicename &optional timeout <br>
 <br>
Wait for a service to be advertised and available. Blocks until it is. <br>


### ros::sleep ###


- Sleeps for any leftover time in a cycle. Calculated from the last time sleep, reset, or the constructor was called. <br>


### ros::get-num-subscribers ###


- Retuns number of subscribers this publish is connected to <br>


### ros::rospack-find ###


- Returns ros package path <br>


### ros::get-param ###


- key <br>
 <br>
Get parameter <br>


### ros::ros-warn ###


- write mesage to warn output <br>


### ros::get-nodes ###


- Retreives the currently-known list of nodes from the master. <br>


### ros::has-param ###


- Check whether a parameter exists on the parameter server. <br>


### ros::service-call ###


- servicename message_type &optional persist <br>
 <br>
Invoke RPC service <br>

        (ros::roseus "add_two_ints_client")
        (ros::wait-for-service "add_two_ints")
        (setq req (instance roseus::AddTwoIntsRequest :init))
        (send req :a (random 10))
        (send req :b (random 20))
        (setq res (ros::service-call "add_two_ints" req t))
        (format t "~d + ~d = ~d~~%" (send req :a) (send req :b) (send res :sum))


### ros::set-param ###


- key value <br>
 <br>
Set parameter <br>


### ros::publish ###


- topic message <br>
 <br>
Publish a message on the topic <br>

        (ros::roseus "talker")
        (ros::advertise "chatter" std_msgs::string 1)
        (ros::rate 100)
        (while (ros::ok)
          (setq msg (instance std_msgs::string :init))
          (send msg :data (format nil "hello world ~a" (send (ros::time-now) :sec-nsec)))
          (ros::publish "chatter" msg)
          (ros::sleep))


### ros::get-param-cashed ###


- Get chached parameter <br>


### ros::roseus ###
&nbsp;&nbsp;&nbsp;*name* *&key* *(ros::anonymous t)* <br>&nbsp;&nbsp;&nbsp;*(ros::logger ros.roseus)* <br>&nbsp;&nbsp;&nbsp;*(ros::level ros::*rosinfo*)* <br>&nbsp;&nbsp;&nbsp;*(args lisp::*eustop-argument*)* 

- Register roseus client node with the master under the specified name <br>


### ros::time ###
&nbsp;&nbsp;&nbsp;*&optional* *(ros::sec 0)* 

- Create new ros::time instance <br>


### ros::time-now ###


- Create new ros::time instance representing current time <br>


### ros::time+ ###
&nbsp;&nbsp;&nbsp;*ros::a* *ros::b* 

- add ros::time <br>


### ros::time- ###
&nbsp;&nbsp;&nbsp;*ros::a* *ros::b* 

- substruct ros::time <br>


### ros::time= ###
&nbsp;&nbsp;&nbsp;*ros::a* *ros::b* 

- check if two ros::time is equal <br>


### ros::time> ###
&nbsp;&nbsp;&nbsp;*ros::a* *ros::b* 

- check if given ros::time is greater then the others <br>


### ros::time>= ###
&nbsp;&nbsp;&nbsp;*ros::a* *ros::b* 

- check if given ros::time is greater then or equal to the others <br>


### ros::time< ###
&nbsp;&nbsp;&nbsp;*ros::a* *ros::b* 

- check if given ros::time is less then the others <br>


### ros::time<= ###
&nbsp;&nbsp;&nbsp;*ros::a* *ros::b* 

- check if given ros::time is less the or equal to the others <br>


### ros::ros-message-destructuring-bind-parse-arg ###
&nbsp;&nbsp;&nbsp;*ros::msg* *ros::params* 

- this function returns a list like ((symbol value) (symbol value) ...). <br>
always the rank of list is 2 <br>


### ros::_ros-message-destructuring-bind-parse-arg ###
&nbsp;&nbsp;&nbsp;*ros::msg* *ros::param* 

- this function returns a list like ((symbol value) ((symbol value) ...)) <br>


### ros::load-ros-package ###
&nbsp;&nbsp;&nbsp;*ros::pkg* 

- load reqruied roseus files for given package <br>


ros::roseus-sigint-handler *ros::sig* *ros::code* 

ros::roseus-error *ros::code* *ros::msg1* *ros::form* *&optional* *(ros::msg2)* 

ros::ros-home-dir 

ros::roseus-add-files *ros::pkg* *type* 

ros::roseus-add-msgs *ros::pkg* 

ros::roseus-add-srvs *ros::pkg* 

ros::resolve-ros-path *fname* 

load *fname* *&rest* *args* 

ros::append-name-space *&rest* *args* 

ros::ros-message-destructuring-bind-flatten-param *ros::params* 

ros::ros-slot-ref *ros::inst* *slot* 

ros::find-load-msg-path *ros::pkg* 

ros::load-ros-manifest *ros::pkg* 

