\begin{refdesc}
\classdesc{ros::object}{propertied-object}{nil}{}
{\footnotesize 
\methoddesc{:init}{nil}{}
\vspace*{-5mm}
\methoddesc{:md5sum-}{nil}{}
\vspace*{-5mm}
\methoddesc{:datatype-}{nil}{}
\vspace*{-5mm}
}

\classdesc{ros::time}{ros::object}{ros::sec-nsec }{}
{\footnotesize 
\methoddesc{:init}{\&key ((:sec ros::\_sec) 0) ((:nsec ros::\_nsec) 0) }{}
\vspace*{-5mm}
\methoddesc{:sec}{\&optional ros::s }{}
\vspace*{-5mm}
\methoddesc{:nsec}{\&optional ros::s }{}
\vspace*{-5mm}
\methoddesc{:sec-nsec}{nil}{}
\vspace*{-5mm}
\methoddesc{:now}{nil}{}
\vspace*{-5mm}
\methoddesc{:to-sec}{nil}{}
\vspace*{-5mm}
\methoddesc{:to-nsec}{nil}{}
\vspace*{-5mm}
\methoddesc{:from-sec}{ros::sec }{}
\vspace*{-5mm}
\methoddesc{:from-nsec}{ros::nsec }{}
\vspace*{-5mm}
\methoddesc{:prin1}{\&optional (ros::strm t) \&rest ros::msgs }{}
\vspace*{-5mm}
}

\funcdesc{ros::ros-message-destructuring-bind-parse-arg}{ros::msg ros::params }{this function returns a list like ((symbol value) (symbol value) ...).
always the rank of list is 2}
\funcdesc{ros::\_ros-message-destructuring-bind-parse-arg}{ros::msg ros::param }{this function returns a list like ((symbol value) ((symbol value) ...))}
{\footnotesize
\fundesc{ros::roseus-sigint-handler}{ros::sig ros::code }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::roseus-error}{ros::code ros::msg1 ros::form \&optional (ros::msg2) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::roseus}{name \&key (ros::anonymous t) (ros::logger ros.roseus) (ros::level ros::\textasteriskcentered rosinfo\textasteriskcentered ) (args lisp::\textasteriskcentered eustop-argument\textasteriskcentered ) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::time}{\&optional (ros::sec 0) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::time-now}{nil}{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::time+}{ros::a ros::b }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::time-}{ros::a ros::b }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::time=}{ros::a ros::b }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::time\textgreater }{ros::a ros::b }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::time\textgreater =}{ros::a ros::b }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::time\textless }{ros::a ros::b }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::time\textless =}{ros::a ros::b }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::ros-home-dir}{nil}{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::roseus-add-files}{ros::pkg type }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::roseus-add-msgs}{ros::pkg }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::roseus-add-srvs}{ros::pkg }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::resolve-ros-path}{fname }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{load}{fname \&rest args }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::append-name-space}{\&rest args }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::ros-message-destructuring-bind-flatten-param}{ros::params }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::ros-slot-ref}{ros::inst slot }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::find-load-msg-path}{ros::pkg }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::load-ros-manifest}{ros::pkg }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{ros::load-ros-package}{ros::pkg }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{compiler::compile-file-if-src-newer-so}{compiler::srcfile \&key (compiler::out-prefix) (compiler::output-dir ./) (compiler::clear-files) \&rest args }{nil}
\vspace*{-5mm}
}
\end{refdesc}
