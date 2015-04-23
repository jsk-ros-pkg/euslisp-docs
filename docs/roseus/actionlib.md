\begin{refdesc}
\classdesc{ros::actionlib-comm-state}{propertied-object}{ros::state ros::action-goal ros::latest-goal-status ros::latest-result }{}
{\footnotesize 
\methoddesc{:init}{\&key ((:action-goal ros::ac)) ((:action-result ros::ar)) (name) }{}
\vspace*{-5mm}
\methoddesc{:action-goal}{nil}{}
\vspace*{-5mm}
\methoddesc{:latest-goal-status}{nil}{}
\vspace*{-5mm}
\methoddesc{:latest-result}{nil}{}
\vspace*{-5mm}
\methoddesc{:find-status-by-goal-id}{ros::msg }{}
\vspace*{-5mm}
\methoddesc{:state}{\&optional ros::s }{}
\vspace*{-5mm}
\methoddesc{:update-status}{ros::msg }{}
\vspace*{-5mm}
\methoddesc{:update-result}{ros::msg }{}
\vspace*{-5mm}
}

\classdesc{ros::simple-action-client}{ros::object}{ros::name-space ros::action-spec ros::simple-state ros::comm-state ros::status-stamp ros::action-goal-class ros::action-result-class ros::action-feedback-class ros::action-done-cb ros::action-active-cb ros::action-feedback-cb ros::goal\_id ros::groupname ros::seq-count }{}
{\footnotesize 
\methoddesc{:goal-status-cb}{ros::msg }{}
\vspace*{-5mm}
\methoddesc{:action-result-cb}{ros::msg }{}
\vspace*{-5mm}
\methoddesc{:action-feedback-cb}{ros::msg }{}
\vspace*{-5mm}
\methoddesc{:make-goal-instance}{\&rest args }{}
\vspace*{-5mm}
\methoddesc{:init}{ros::ns ros::spec \&key ((:groupname ros::gp)) }{}
\vspace*{-5mm}
\methoddesc{:wait-for-server}{\&optional (ros::timeout nil) }{}
\vspace*{-5mm}
\methoddesc{:send-goal}{ros::goal \&key ros::done-cb ros::active-cb ros::feedback-cb }{}
\vspace*{-5mm}
\methoddesc{:send-goal-and-wait}{ros::goal \&key (ros::timeout 0) }{}
\vspace*{-5mm}
\methoddesc{:wait-for-result}{\&key (ros::timeout 0) (ros::return-if-server-down) (ros::maximum-status-interval 5) }{}
\vspace*{-5mm}
\methoddesc{:get-result}{nil}{}
\vspace*{-5mm}
\methoddesc{:get-state}{nil}{}
\vspace*{-5mm}
\methoddesc{:get-goal-status-text}{nil}{}
\vspace*{-5mm}
\methoddesc{:cancel-all-goals}{nil}{}
\vspace*{-5mm}
\methoddesc{:cancel-goal}{nil}{}
\vspace*{-5mm}
\methoddesc{:spin-once}{nil}{}
\vspace*{-5mm}
}

\classdesc{ros::simple-action-server}{ros::object}{ros::name-space ros::action-spec ros::status ros::action-goal-class ros::action-result-class ros::action-feedback-class ros::execute-cb ros::accept-cb ros::preempt-cb ros::goal ros::goal-id ros::pending-goal ros::seq-id ros::feed-seq-id ros::groupname }{}
{\footnotesize 
\methoddesc{:execute-cb}{nil}{}
\vspace*{-5mm}
\methoddesc{:goal-callback}{ros::msg }{}
\vspace*{-5mm}
\methoddesc{:cancel-callback}{ros::msg }{}
\vspace*{-5mm}
\methoddesc{:publish-result}{ros::msg \&optional (ros::text ) }{}
\vspace*{-5mm}
\methoddesc{:publish-feedback}{ros::msg }{}
\vspace*{-5mm}
\methoddesc{:set-succeeded}{ros::msg \&optional (ros::text ) }{}
\vspace*{-5mm}
\methoddesc{:set-aborted}{ros::msg \&optional (ros::text ) }{}
\vspace*{-5mm}
\methoddesc{:set-preempted}{\&optional (ros::msg (send self :result)) (ros::text ) }{}
\vspace*{-5mm}
\methoddesc{:goal}{nil}{}
\vspace*{-5mm}
\methoddesc{:result}{\&rest args }{}
\vspace*{-5mm}
\methoddesc{:feedback}{\&rest args }{}
\vspace*{-5mm}
\methoddesc{:init}{ros::ns ros::spec \&key ((:execute-cb ros::exec-f)) ((:preempt-cb ros::preempt-f)) ((:groupname ros::gp)) ((:accept-cb ros::accept-f)) }{}
\vspace*{-5mm}
\methoddesc{:worker}{nil}{}
\vspace*{-5mm}
\methoddesc{:is-preempt-requested}{nil}{}
\vspace*{-5mm}
\methoddesc{:is-active}{nil}{}
\vspace*{-5mm}
\methoddesc{:name-space}{nil}{}
\vspace*{-5mm}
\methoddesc{:next-seq-id}{nil}{}
\vspace*{-5mm}
\methoddesc{:next-feed-seq-id}{nil}{}
\vspace*{-5mm}
\methoddesc{:spin-once}{nil}{}
\vspace*{-5mm}
}

{\footnotesize
\fundesc{ros::simple-goal-states-to-string}{ros::i }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::goal-status-to-string}{ros::i }{nil}
\vspace*{-5mm}
}
\end{refdesc}
