\begin{refdesc}
\classdesc{ros::tf-cascaded-coords}{cascaded-coords}{ros::frame-id ros::child-frame-id ros::stamp }{}
{\footnotesize 
\methoddesc{:init}{\&rest ros::initargs \&key ((:frame-id ros::fid) ) ((:child-frame-id ros::cid) ) ((:stamp ros::st) \#i(0 0)) \&allow-other-keys }{}
\vspace*{-5mm}
\methoddesc{:frame-id}{\&optional id }{}
\vspace*{-5mm}
\methoddesc{:child-frame-id}{\&optional id }{}
\vspace*{-5mm}
\methoddesc{:stamp}{\&optional ros::st }{}
\vspace*{-5mm}
\methoddesc{:prin1}{\&optional (ros::strm 1) }{}
\vspace*{-5mm}
}

\classdesc{ros::transformer}{ros::object}{ros::cobject }{}
{\footnotesize 
\methoddesc{:init}{\&optional (ros::interpolating t) (ros::cache-time 10.0) }{}
\vspace*{-5mm}
\methoddesc{:all-frames-as-string}{nil}{}
\vspace*{-5mm}
\methoddesc{:set-transform}{coords \&optional (ros::auth ) }{}
\vspace*{-5mm}
\methoddesc{:wait-for-transform}{ros::target-frame ros::source-frame ros::time ros::timeout \&optional (ros::duration 0.01) }{}
\vspace*{-5mm}
\methoddesc{:wait-for-transform-full}{ros::target-frame ros::target-time ros::source-frame ros::source-time ros::fixed-frame ros::timeout \&optional (ros::duration 0.01) }{}
\vspace*{-5mm}
\methoddesc{:can-transform}{ros::target-frame ros::source-frame ros::time }{}
\vspace*{-5mm}
\methoddesc{:can-transform-full}{ros::target-frame ros::target-time ros::source-frame ros::source-time ros::fixed-frame }{}
\vspace*{-5mm}
\methoddesc{:chain}{ros::target-frame ros::target-time ros::source-frame ros::source-time ros::fixed-frame }{}
\vspace*{-5mm}
\methoddesc{:clear}{nil}{}
\vspace*{-5mm}
\methoddesc{:frame-exists}{ros::frame-id }{}
\vspace*{-5mm}
\methoddesc{:get-frame-strings}{nil}{}
\vspace*{-5mm}
\methoddesc{:get-latest-common-time}{ros::source-frame ros::target-frame }{}
\vspace*{-5mm}
\methoddesc{:lookup-transform}{ros::target-frame ros::source-frame ros::time }{}
\vspace*{-5mm}
\methoddesc{:lookup-transform-full}{ros::target-frame ros::target-time ros::source-frame ros::source-time ros::fixed-frame }{}
\vspace*{-5mm}
\methoddesc{:lookup-velocity}{ros::reference-frame ros::moving-frame ros::time ros::duration }{}
\vspace*{-5mm}
\methoddesc{:get-parent}{ros::frame\_id ros::time }{}
\vspace*{-5mm}
\methoddesc{:set-extrapolation-limit}{distance }{}
\vspace*{-5mm}
}

\classdesc{ros::transform-listener}{ros::transformer}{nil}{}
{\footnotesize 
\methoddesc{:init}{\&optional (ros::cache-time 10.0) (ros::spin-thread t) }{}
\vspace*{-5mm}
\methoddesc{:dispose}{nil}{}
\vspace*{-5mm}
\methoddesc{:transform-pose}{ros::target-frame ros::pose-stamped }{}
\vspace*{-5mm}
}

\classdesc{ros::transform-broadcaster}{ros::object}{ros::cobject }{}
{\footnotesize 
\methoddesc{:init}{nil}{}
\vspace*{-5mm}
\methoddesc{:send-transform}{coords ros::p-frame ros::c-frame \&optional ros::tm }{}
\vspace*{-5mm}
}

\classdesc{ros::buffer-client}{ros::object}{ros::cobject }{}
{\footnotesize 
\methoddesc{:init}{\&key (ros::namespace tf2\_buffer\_server) (ros::check-frequency 10.0) (ros::timeout-padding 2.0) }{}
\vspace*{-5mm}
\methoddesc{:wait-for-server}{\&optional (ros::timeout 5.0) }{}
\vspace*{-5mm}
\methoddesc{:wait-for-transform}{ros::target-frame ros::source-frame ros::rostime ros::timeout \&optional (ros::duration 0.05) }{}
\vspace*{-5mm}
\methoddesc{:can-transform}{ros::target-frame ros::source-frame ros::rostime \&optional (ros::timeout 0.0) }{}
\vspace*{-5mm}
\methoddesc{:lookup-transform}{ros::target-frame ros::source-frame ros::rostime \&optional (ros::timeout 0.0) }{}
\vspace*{-5mm}
}

{\footnotesize
\fundesc{ros::pos-\textgreater tf-point}{pos }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::pos-\textgreater tf-translation}{pos }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::rot-\textgreater tf-quaternion}{rot }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::coords-\textgreater tf-pose}{coords }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::coords-\textgreater tf-pose-stamped}{coords id }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::coords-\textgreater tf-transform}{coords }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::coords-\textgreater tf-transform-stamped}{coords id \&optional (ros::child\_id ) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::tf-point-\textgreater pos}{ros::point }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::tf-quaternion-\textgreater rot}{ros::quaternion }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::tf-pose-\textgreater coords}{ros::pose }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::tf-pose-stamped-\textgreater coords}{ros::pose-stamped }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::tf-transform-\textgreater coords}{ros::trans }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::tf-transform-stamped-\textgreater coords}{ros::trans }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::tf-twist-\textgreater coords}{ros::twist }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::create-identity-quaternion}{nil}{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::create-quaternion-from-rpy}{ros::roll ros::pitch ros::yaw }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::create-quaternion-msg-from-rpy}{ros::roll ros::pitch ros::yaw }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::identity-quaternion}{nil}{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::identity-pose}{nil}{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::identity-pose-stamped}{\&optional (ros::header (instance std\_msgs::header :init)) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::identity-transform}{nil}{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::identity-transform-stamped}{\&optional (ros::header (instance std\_msgs::header :init)) }{nil}
\vspace*{-5mm}
}
\end{refdesc}
