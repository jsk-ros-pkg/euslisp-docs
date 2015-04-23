### ros::actionlib-comm-state
- :super **propertied-object**
- :slots ros::state ros::action-goal ros::latest-goal-status ros::latest-result 

:init &key ((:action-goal ros::ac)) ((:action-result ros::ar)) (name) 

:action-goal nil

:latest-goal-status nil

:latest-result nil

:find-status-by-goal-id ros::msg 

:state &optional ros::s 

:update-status ros::msg 

:update-result ros::msg 


### ros::simple-action-client
- :super **ros::object**
- :slots ros::name-space ros::action-spec ros::simple-state ros::comm-state ros::status-stamp ros::action-goal-class ros::action-result-class ros::action-feedback-class ros::action-done-cb ros::action-active-cb ros::action-feedback-cb ros::goal_id ros::groupname ros::seq-count 

:goal-status-cb ros::msg 

:action-result-cb ros::msg 

:action-feedback-cb ros::msg 

:make-goal-instance &rest args 

:init ros::ns ros::spec &key ((:groupname ros::gp)) 

:wait-for-server &optional (ros::timeout nil) 

:send-goal ros::goal &key ros::done-cb ros::active-cb ros::feedback-cb 

:send-goal-and-wait ros::goal &key (ros::timeout 0) 

:wait-for-result &key (ros::timeout 0) (ros::return-if-server-down) (ros::maximum-status-interval 5) 

:get-result nil

:get-state nil

:get-goal-status-text nil

:cancel-all-goals nil

:cancel-goal nil

:spin-once nil


### ros::simple-action-server
- :super **ros::object**
- :slots ros::name-space ros::action-spec ros::status ros::action-goal-class ros::action-result-class ros::action-feedback-class ros::execute-cb ros::accept-cb ros::preempt-cb ros::goal ros::goal-id ros::pending-goal ros::seq-id ros::feed-seq-id ros::groupname 

:execute-cb nil

:goal-callback ros::msg 

:cancel-callback ros::msg 

:publish-result ros::msg &optional (ros::text ) 

:publish-feedback ros::msg 

:set-succeeded ros::msg &optional (ros::text ) 

:set-aborted ros::msg &optional (ros::text ) 

:set-preempted &optional (ros::msg (send self :result)) (ros::text ) 

:goal nil

:result &rest args 

:feedback &rest args 

:init ros::ns ros::spec &key ((:execute-cb ros::exec-f)) ((:preempt-cb ros::preempt-f)) ((:groupname ros::gp)) ((:accept-cb ros::accept-f)) 

:worker nil

:is-preempt-requested nil

:is-active nil

:name-space nil

:next-seq-id nil

:next-feed-seq-id nil

:spin-once nil


##### **ros::simple-goal-states-to-string** ros::i 
- nil

##### **ros::goal-status-to-string** ros::i 
- nil

