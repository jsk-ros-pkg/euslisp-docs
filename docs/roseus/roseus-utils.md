\begin{refdesc}
\classdesc{exact-time-message-filter-callback-helper}{propertied-object}{deligate topic }{}
\methoddesc{:init}{topic-info \&key deligate-object }{topic-info := (topic message-type)
     deligate-object := exact-time-message-filter}
\methoddesc{:callback}{msg }{just call :add-message method of deligate object}
{\footnotesize 
}

\classdesc{exact-time-message-filter}{propertied-object}{callback-helpers buffers buffer-length }{}
\methoddesc{:init}{topics \&key ((:buffer-length abuffer-length) 50) }{topic := ((topic message-type) (topic message-type) ...)}
\methoddesc{:add-message}{topic-name message }{add message to buffer and call callback if possible}
\methoddesc{:call-callback-if-possible}{stamp }{call synchronized callback if messages can be found
with the same timestamp}
{\footnotesize 
}

\funcdesc{make-ros-msg-from-eus-pointcloud}{pcloud \&key (with-color :rgb) (with-normal t) (frame /sensor\_frame) }{convert from pointcloud in eus to sensor\_msgs::PointCloud2 in ros}
\funcdesc{make-eus-pointcloud-from-ros-msg}{msg \&key (pcloud) (remove-nan) }{convert from sensor\_msgs::PointCloud2 in ros to pointcloud in eus}
\funcdesc{ros::set-dynamic-reconfigure-param}{srv name type value }{set dynamic\_reconfugre parameter.}
{\footnotesize
\fundesc{print-ros-msg}{msg \&optional (rp (find-package ROS)) (ro ros::object) (nest 0) (padding   ) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{make-camera-from-ros-camera-info-aux}{pwidth pheight p frame-coords \&rest args }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{make-camera-from-ros-camera-info}{msg }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{make-msg-from-3dpointcloud}{points-list \&key color-list (frame /sensor\_frame) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{make-eus-pointcloud-from-ros-msg1}{sensor\_msgs\_pointcloud }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{vector-\textgreater rgba}{cv \&optional (alpha 1.0) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{cylinder-\textgreater marker-msg}{cyl header \&key ((:color col) (float-vector 1.0 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{cube-\textgreater marker-msg}{cb header \&key ((:color col) (float-vector 1.0 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{sphere-\textgreater marker-msg}{sp header \&key ((:color col) (float-vector 1.0 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{line-\textgreater marker-msg}{li header \&key ((:color col) (float-vector 1 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ((:scale sc) 10.0) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{line-list-\textgreater marker-msg}{li header \&key ((:color col) (float-vector 1 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ((:scale sc) 10.0) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{faces-\textgreater marker-msg}{faces header \&key ((:color col) (float-vector 1 0 0)) ((:id idx) 0) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{object-\textgreater marker-msg}{obj header \&key coords ((:color col) (float-vector 1 1 1)) ((:alpha a) 1.0) ((:id idx) 0) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{wireframe-\textgreater marker-msg}{wf header \&rest args }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{text-\textgreater marker-msg}{str c header \&key ((:color col) (float-vector 1 1 1)) ((:alpha a) 1.0) ((:id idx) 0) ((:scale sc) 100.0) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{coords-\textgreater marker-msg}{coords header \&key (size 100) (width 10) (id 0) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{mesh-\textgreater marker-msg}{cds mesh\_resource header \&key ((:color col) (float-vector 1 1 1)) ((:scale sc) 1000) ((:id idx) 0) ((:mesh\_use\_embedded\_materials use\_embedded) t) (alpha 1.0) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{pointcloud-\textgreater marker-msg}{pc header \&key ((:color col) (float-vector 1.0 0 0)) ((:alpha a) 1.0) (use-color) (point-width 0.01) (point-height 0.01) ((:id idx) 0) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{eusobj-\textgreater marker-msg}{eusgeom header \&rest args \&key (wireframe) \&allow-other-keys }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{arrow-\textgreater marker-msg}{arg header \&key ((:color col) (float-vector 1.0 0 0)) ((:alpha a) 1.0) ((:id idx) 0) ((:scale sc) nil) ns lifetime }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{marker-msg-\textgreater shape}{msg \&rest args }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{marker-msg-\textgreater shape/cube}{msg }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{marker-msg-\textgreater shape/cylinder}{msg }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{marker-msg-\textgreater shape/sphere}{msg }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{marker-msg-\textgreater shape/cube\_list}{msg \&key ((:pointcloud pt)) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{dump-pointcloud-to-pcd-file}{fname pcloud \&key (rgb :rgb) (binary) (scale 0.001) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{read-header-symbol}{str sym }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{read-pcd-file}{fname \&key (scale 1000.0) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{call-empty-service}{srvname \&key (wait nil) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{one-shot-subscribe}{topic-name mclass \&key (timeout) (after-stamp) (unsubscribe t) }{nil}
\vspace*{-5mm}
}
{\footnotesize
\fundesc{one-shot-publish}{topic-name msg \&key (wait 500) (after-stamp) (unadvertise t) }{nil}
\vspace*{-5mm}
}
\end{refdesc}
