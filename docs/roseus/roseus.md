### ros::object
- :super **propertied-object**
- :slots nil

:init nil

:md5sum- nil

:datatype- nil


### ros::time
- :super **ros::object**
- :slots ros::sec-nsec 

:init &key ((:sec ros::_sec) 0) ((:nsec ros::_nsec) 0) 

:sec &optional ros::s 

:nsec &optional ros::s 

:sec-nsec nil

:now nil

:to-sec nil

:to-nsec nil

:from-sec ros::sec 

:from-nsec ros::nsec 

:prin1 &optional (ros::strm t) &rest ros::msgs 


##### **ros::ros-message-destructuring-bind-parse-arg** ros::msg ros::params 
- this function returns a list like ((symbol value) (symbol value) ...).
always the rank of list is 2

##### **ros::_ros-message-destructuring-bind-parse-arg** ros::msg ros::param 
- this function returns a list like ((symbol value) ((symbol value) ...))

##### **ros::roseus-sigint-handler** ros::sig ros::code 
- nil

##### **ros::roseus-error** ros::code ros::msg1 ros::form &optional (ros::msg2) 
- nil

##### **ros::roseus** name &key (ros::anonymous t) (ros::logger ros.roseus) (ros::level ros::*rosinfo*) (args lisp::*eustop-argument*) 
- nil

##### **ros::time** &optional (ros::sec 0) 
- nil

##### **ros::time-now** nil
- nil

##### **ros::time+** ros::a ros::b 
- nil

##### **ros::time-** ros::a ros::b 
- nil

##### **ros::time=** ros::a ros::b 
- nil

##### **ros::time>** ros::a ros::b 
- nil

##### **ros::time>=** ros::a ros::b 
- nil

##### **ros::time<** ros::a ros::b 
- nil

##### **ros::time<=** ros::a ros::b 
- nil

##### **ros::ros-home-dir** nil
- nil

##### **ros::roseus-add-files** ros::pkg type 
- nil

##### **ros::roseus-add-msgs** ros::pkg 
- nil

##### **ros::roseus-add-srvs** ros::pkg 
- nil

##### **ros::resolve-ros-path** fname 
- nil

##### **load** fname &rest args 
- nil

##### **ros::append-name-space** &rest args 
- nil

##### **ros::ros-message-destructuring-bind-flatten-param** ros::params 
- nil

##### **ros::ros-slot-ref** ros::inst slot 
- nil

##### **ros::find-load-msg-path** ros::pkg 
- nil

##### **ros::load-ros-manifest** ros::pkg 
- nil

##### **ros::load-ros-package** ros::pkg 
- nil

##### **compiler::compile-file-if-src-newer-so** compiler::srcfile &key (compiler::out-prefix) (compiler::output-dir ./) (compiler::clear-files) &rest args 
- nil

