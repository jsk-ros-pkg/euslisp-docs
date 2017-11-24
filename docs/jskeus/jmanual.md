---
Generator: LaTeX2HTML v2008
description: |
    EusLisp EusLisp version 9.00/ irteus version 1.00 リファレンスマニュアル
    -ロボットモデリングの拡張- ETL-TR-95-19 + JSK-TR-10-03 November 24, 2017
distribution: global
keywords: jmanual
resource-type: document
title: |
    EusLisp EusLisp version 9.00/ irteus version 1.00 リファレンスマニュアル
    -ロボットモデリングの拡張- ETL-TR-95-19 + JSK-TR-10-03 November 24, 2017
...

**EusLisp** **EusLisp version 9.00/ irteus version 1.00** **リファレンスマニュアル** -ロボットモデリングの拡張- ETL-TR-95-19 + JSK-TR-10-03 November 24, 2017
=============================================================================================================================================================

**irteus 1.00** *東京大学大学院　*
情報理工学系研究科　知能機械情報学専攻

------------------------------------------------------------------------

[]() irteus 拡張
================

[ロボットモデリング]()
======================

[ロボットのデータ構造とモデリング]()
------------------------------------

### [ロボットのデータ構造と順運動学]()

ロボットの構造はリンクと関節から構成されていると考えることが出来るが，
ロボットを関節とリンクに分割する方法として

-   (a)切り離されるリンクの側に関節を含める
-   (b)胴体，あるいは胴体に近いほうに関節を含める

が考えられる．コンピュータのデータ構造を考慮し，
(a)が利用されている．その理由は胴体以外のすべてのリンクにおいて，
必ず関節を一つ含んだ構造となり，すべてのリンクを同一のアルゴリズムで扱う
ことが出きるためである．

この様に分割されたリンクを計算機上で表現するためにはツリー構造を利用する
ことが出来る．一般的にはツリー構造を作るときに二分木にすることでデータ構
造を簡略化することが多い．

ロボットのリンクにおける同次変換行列の求め方としては，関節回転座標系上に
原点をもつ<span
class="MATH"></span>を設定し，親リンク座標系からみた回転軸ベクトルが
<span class="MATH">![\$ a\_j\$](jmanual-img3.png){width="23"
height="30"}</span>, <span class="MATH"></span>の原点が<span
class="MATH">![\$ b\_j\$](jmanual-img4.png){width="20"
height="30"}</span>であり，回転の関節角度を<span class="MATH">![\$
q\_j\$](jmanual-img5.png){width="18" height="30"}</span>とする．

このとき<span class="MATH"></span>の親リンク相対の同次変換行列は

<div class="mathdisplay">

![\$\\displaystyle {}\^iT\_j = \\left\[ \\begin{array}{cc}
e\^{\\hat{a}\_jq\_j} & b\_j \\\\ 0\~0\~0 & 1 \\end{array} \\right\]
\$](jmanual-img6.png){width="18" height="30"}

</div>

と書くことが出来る．

ここで， <span class="MATH">![\$
e\^{\\hat{a}\_jq\_j}\$](jmanual-img7.png){width="144"
height="56"}</span>は，一定速度の角速度ベクトルによって生ずる回
転行列を与える以下のRodriguesの式を用いている．これを回転軸<span
class="MATH">![\$ a\$](jmanual-img8.png){width="37"
height="20"}</span>周りに <span class="MATH">![\$
wt\[rad\]\$](jmanual-img9.png){width="13"
height="13"}</span>だけ回転する回転行列を与えるものとして利用している．

<div class="mathdisplay">

![\$\\displaystyle e\^{\\hat{\\omega}t} = E + \\hat{a} sin (wt) +
\\hat{a}\^2 (1 - cos(wt)) \$](jmanual-img10.png){width="55" height="32"}

</div>

親リンクの位置姿勢<span class="MATH">![\$ p\_i,
R\_i\$](jmanual-img11.png){width="267"
height="37"}</span>が既知だとすると，<span class="MATH">![\$
\\Sigma\_i\$](jmanual-img12.png){width="42"
height="30"}</span>の同次変換行列を

<div class="mathdisplay">

![\$\\displaystyle T\_i = \\left\[ \\begin{array}{cc} R\_i & p\_i \\\\
0\~0\~0 & 1 \\end{array} \\right\] \$](jmanual-img13.png){width="21"
height="30"}

</div>

と作ることができ，ここから

<div class="mathdisplay">

![\$\\displaystyle T\_j = T\_i \~ {}\^iT\_j
\$](jmanual-img14.png){width="137" height="54"}

</div>

として計算できる．これをロボットのルートリンクから初めてすべてのリンクに
順次適用することでロボットの全身の関節角度情報から姿勢情報を算出すること
ができ，これを順運動学と呼ぶ．

### [EusLispによる幾何情報のモデリング]()

Euslispの幾何モデリングでは，基本モデル(body)の生成，bodyの合成関数，複
合モデル(bodyset)の生成と3つの段階がある．

これまでに以下のような基本モデルの生成，合成が可能な事を見てきている．

    (setq c1 (make-cube 100 100 100))
    (send c1 :locate #f(0 0 50))
    (send c1 :rotate (deg2rad 30) :x)
    (send c1 :set-color :yellow)
    (objects (list c1))

    (setq c2 (make-cylinder 50 100))
    (send c2 :move-to
          (make-coords
           :pos #f(20 30 40)
           :rpy (float-vector 0 0 (deg2rad 90)))
          :world)
    (send c2 :set-color :green)
    (objects (list c1 c2))

    (setq c3 (body+ c1 c2))
    (setq c4 (body- c1 c2))
    (setq c5 (body* c1 c2))

bodysetはirteusで導入された複合モデルであり，bodyで扱えない複数の物体や
複数の色を扱うためのものである．

    (setq c1 (make-cube 100 100 100))
    (send c1 :set-color :red)
    (setq c2 (make-cylinder 30 100))
    (send c2 :set-color :green)
    (send c1 :assoc c2)           ;;; これを忘れいように
    (setq b1 (instance bodyset :init
                       (make-cascoords)
                       :bodies (list c1 c2)))
    (objects (list b1))

### [幾何情報の親子関係を利用したサンプルプログラム]()

    (setq c1 (make-cube 100 100 100))
    (setq c2 (make-cube 50 50 50))
    (send c1 :set-color :red)
    (send c2 :set-color :blue)
    (send c2 :locate #f(300 0 0))
    (send c1 :assoc c2)
    (objects (list c1 c2))
    (do-until-key
     (send c1 :rotate (deg2rad 5) :z)
     (send *irtviewer* :draw-objects)
     (x::window-main-one) ;; process window event
     )

### [bodyset-linkとjointを用いたロボット（多リンク系）のモデリング]()

irteusではロボットリンクを記述するクラスとしてbodyset-link(irtmodel.l)
というクラスが用意されている．これは機構情報と幾何情報をもち，一般的な木
構造でロボットの構造が表現されている．また，jointクラスを用いて関節情報
を扱っている．

    (defclass bodyset-link
      :super bodyset
      :slots (joint parent-link child-links analysis-level default-coords
                    weight acentroid inertia-tensor
                    angular-velocity angular-acceleration
                    spacial-velocity spacial-acceleration
                    momentum-velocity angular-momentum-velocity
                    momentum angular-momentum
                    force moment ext-force ext-moment))

ジョイント（関節）のモデリングはjointクラス(irtmodel.l)を用いる．jointクラスは基底ク
ラスであり，実際にはrotational-joint, linear-joint等を利用する．
jointの子クラスで作られた関節は，:joint-angleメソッドで関節角度を指定す
ることが出来る．

    (defclass joint
      :super propertied-object
      :slots (parent-link child-link joint-angle min-angle max-angle
        default-coords))
    (defmethod joint
      (:init (&key name
                   ((:child-link clink)) ((:parent-link plink))
                   (min -90) (max 90) &allow-other-keys)
             (send self :name name)
             (setq parent-link plink child-link clink
                   min-angle min max-angle max)
             self))

    (defclass rotational-joint
      :super joint
      :slots (axis))
    (defmethod rotational-joint
      (:init (&rest args &key ((:axis ax) :z) &allow-other-keys)
             (setq axis ax joint-angle 0.0)
             (send-super* :init args)
             self)
      (:joint-angle
       (&optional v)
       (when v
            (setq relang (- v joint-angle) joint-angle v)
            (send child-link :rotate (deg2rad relang) axis)))
         joint-angle))

ここでは，joint, parent-link, child-links, defualt-coordsを利用する．

簡単な１関節ロボットの例としてサーボモジュールを作ってみると

    (defun make-servo nil
      (let (b1 b2)
        (setq b1 (make-cube 35 20 46))
        (send b1 :locate #f(9.5 0 0))
        (setq b2 (make-cylinder 3 60))
        (send b2 :locate #f(0 0 -30))
        (setq b1 (body+ b1 b2))
        (send b1 :set-color :gray20)
        b1))

    (defun make-hinji nil
      (let ((b2 (make-cube 22 16 58))
            (b1 (make-cube 26 20 54)))
        (send b2 :locate #f(-4 0 0))
        (setq b2 (body- b2 b1))
        (send b1 :set-color :gray80)
        b2))

    (setq h1 (instance bodyset-link :init (make-cascoords) :bodies (list (make-hinji))))
    (setq s1 (instance bodyset-link :init (make-cascoords) :bodies (list (make-servo))))
    (setq j1 (instance rotational-joint :init :parent-link h1 :child-link s1 :axis :z))
    ;; instance cascaded coords
    (setq r (instance cascaded-link :init))
    (send r :assoc h1)
    (send h1 :assoc s1)
    (setq (r . links) (list h1 s1))
    (setq (r . joint-list) (list j1))
    (send r :init-ending)

となる．

ここでは，`h1`，`s1`という`bodyset-link`と，
`j1`という`rotational-joint`を作成し，ここから
`cascaded-link`という，連結リンクからなるモデルを生成している．
`cascaded-link`は`cascaded-coords`の子クラスであるため，
`r (cascaded-link)`，`h1`，`s1`の座標系の親子関係を
`:assoc`を利用して設定している．

`(r . links)`という記法は`r`というオブジェクトのスロット変数
（メンバ変数）である`links`にアクセスしている．ここでは，
`links`および`joint-list`に適切な値をセットし，
`(send r :init-ending)`として必要な初期設定を行っている．

これで`r`という2つのリンクと１つの関節情報
を含んだ1つのオブジェクトを生成できる．これで例えば
`(objects (list h1 s1))`ではなく，
`(objects (list r))`としてロボットをビューワに表示できる．
また，`(send r :locate #f(100 0 0))`などの利用も可能になっている．

`cascaded-link`クラスのメソッドの利用例としては以下ある．
`:joint-list`，`:links`といった関節リストやリンクリストへの
アクセサに加え，関節角度ベクトルへのアクセスを提供する
`:angle-vector`メソッドが重要である．これを引数なしで呼び出せば現
在の関節角度が得られ，これに関節角度ベクトルを引数に与えて呼び出せば，その引
数が示す関節角度ベクトルをロボットモデルに反映させることができる．

    $ (objects (list r))
    (#<servo-model #X628abb0  0.0 0.0 0.0 / 0.0 0.0 0.0>)
    ;; useful cascaded-link methods
    $ (send r :joint-list)
    (#<rotational-joint #X6062990 :joint101067152>)
    $ (send r :links)
    (#<bodyset-link #X62ccb10 :bodyset103598864  0.0 0.0 0.0 / 0.0 0.0 0.0>
     #<bodyset-link #X6305830 :bodyset103831600  0.0 0.0 0.0 / 0.524 0.0 0.0>)
    $ (send r :angle-vector)
    #f(0.0)
    $ (send r :angle-vector (float-vector 30))
    #f(30.0)

### [cascaded-linkを用いたロボット（多リンク系）のモデリング]()

一方で多リンク系のモデリング用のクラスとしてcascaded-linkというクラス
がある． これには，links,
joint-listというスロット変数があり，ここにbodyset-link,
ならびにjointのインスタンスのリストをバインドして利用する． 以下は，
`cascaded-link`の子クラスを定義しここでロボットモデリングに
関する初期化処理を行うという書き方の例である．

    (defclass cascaded-link
      :super cascaded-coords
      :slots (links joint-list bodies collision-avoidance-links))

    (defmethod cascaded-link
      (:init (&rest args &key name &allow-other-keys)
             (send-super-lexpr :init args)
             self)
      (:init-ending
       ()
       (setq bodies (flatten (send-all links :bodies)))
       (dolist (j joint-list)
         (send (send j :child-link) :add-joint j)
         (send (send j :child-link) :add-parent-link (send j :parent-link))
         (send (send j :parent-link) :add-child-links (send j :child-link)))
       (send self :update-descendants))
    )

    (defclass servo-model
      :super cascaded-link
      :slots (h1 s1 j1))
    (defmethod servo-model
      (:init ()
       (let ()
         (send-super :init)
         (setq h1 (instance bodyset-link :init (make-cascoords) :bodies (list (make-hinji))))
         (setq s1 (instance bodyset-link :init (make-cascoords) :bodies (list (make-servo))))

         (setq j1 (instance rotational-joint :init :parent-link h1 :child-link s1 :axis :z))

         ;; instance cascaded coords
         (setq links (list h1 s1))
         (setq joint-list (list j1))
         ;;
         (send self :assoc h1)
         (send h1 :assoc s1)
         ;;
         (send self :init-ending)
         self))
      ;;
      ;; (send r :j1 :joint-angle 30)
      (:j1 (&rest args) (forward-message-to j1 args))
      )

    (setq r (instance servo-model :init))

このようなクラスを定義して`(setq r (instance servo-model :init))`
としても同じようにロボットモデルのインスタンスを作成することができ，先
ほどのメソッドを利用できる．クラス定義するメリットは
`(:j1 (&rest args) (forward-message-to j1 args))`というメソッド定
義により，関節モデルのインスタンスへのアクセサを提供することができる．
これにより，特定の関節だけの値を知りたいとき，あるいは値をセットしたい
時には`(send r :j1 :joint-angle)`や `(send r :j1 :joint-angle 30)`
という指示が可能になっている．
このロボットを動かす場合は前例と同じように

    (objects (list r))
    (dotimes (i 300)
      (send r :angle-vector (float-vector (* 90 (sin (/ i 100.0)))))
      (send *irtviewer* :draw-objects))

などとするとよい．

    (setq i 0)
    (do-until-key
      (send r :angle-vector (float-vector (* 90 (sin (/ i 100.0)))))
      (send *irtviewer* :draw-objects)
      (incf i))

とすると，次にキーボードを押下するまでプログラムは動きつづける．

さらに，少し拡張して
これを用いて３リンク２ジョイントのロボットをモデリングした例が以下にな
る．:joint-angleというメソッドに\#f(0 0)というベクトルを引数に与えるこ
とで全ての関節角度列を指定することが出来る．

    (defclass hinji-servo-robot
      :super cascaded-link)
    (defmethod hinji-servo-robot
      (:init
       ()
       (let (h1 s1 h2 s2 l1 l2 l3)
         (send-super :init)
         (setq h1 (make-hinji))
         (setq s1 (make-servo))
         (setq h2 (make-hinji))
         (setq s2 (make-servo))
         (send h2 :locate #f(42 0 0))
         (send s1 :assoc h2)
         (setq l1 (instance bodyset-link :init (make-cascoords) :bodies (list h1)))
         (setq l2 (instance bodyset-link :init (make-cascoords) :bodies (list s1 h2)))
         (setq l3 (instance bodyset-link :init (make-cascoords) :bodies (list s2)))
         (send l3 :locate #f(42 0 0))

         (send self :assoc l1)
         (send l1 :assoc l2)
         (send l2 :assoc l3)

         (setq joint-list
               (list
                (instance rotational-joint
                          :init :parent-link l1 :child-link l2
                          :axis :z)
                (instance rotational-joint
                          :init :parent-link l2 :child-link l3
                          :axis :z)))
         (setq links (list l1 l2 l3))
         (send self :init-ending)
         )))
    (setq r (instance hinji-servo-robot :init))
    (objects (list r))

    (dotimes (i 10000)
      (send r :angle-vector (float-vector (* 90 (sin (/ i 500.0))) (* 90 (sin (/ i 1000.0)))))
      (send *irtviewer* :draw-objects))

### [EusLispにおける順運動学計算]()

順運動学計算を行うには，cascaded-corods, bodyset, bodyset-link 各クラ
スに定義された :worldcoords メソッドを用いる． :worldcoords
メソッドは，ルートリンクが見つかる（親リンクがなくなる） か，
スロット変数 changed が nil であるリンク
（一度順運動学計算を行ったことがある）が見つかるまで
さかのぼって親リンクの :worldcoords メソッドを呼び出すことで
順運動学計算を行う． その際，スロット変数 changed を nil で上書く．
したがって，二度目の :worldcoords メソッドの呼び出しでは，一度計算され
たリンクの順運動学計算は行われず，即座にリンクの位置姿勢情報を取り出す
ことができる．

また，bodyset-link クラスの :worldcoords メソッドは, level 引数を取る
ことができ，それが :coords である場合には，リンクのもつ bodies
スロット変数 の順運動学計算は行われない． bodies
にリンクの頂点を構成する faceset が含まれている場合には，これら
についての順運動学計算を省略することで大幅な高速化が期待できるだろう．
なお， level 引数の初期値には，リンクのもつ analysis-level スロット変数
が用いられるため，常に bodies の順運動学計算を行わない場合は，
リンクのインスタンス l について `(send l :analysis-level :coords)`
とすればよい．

    (defmethod bodyset-link
      (:worldcoords
       (&optional (level analysis-level))
       (case
        level
        (:coords (send-message self cascaded-coords :worldcoords))
        (t       (send-super :worldcoords)))
       ))

    (defmethod bodyset
      (:worldcoords
       ()
       (when changed
         (send-super :worldcoords)
         (dolist (b bodies) (send b :worldcoords)))
       worldcoords))

    (defmethod cascaded-coords
     (:worldcoords  ()      ;calculate rot and pos in the world
       (when changed
          (if parent
              (transform-coords (send parent :worldcoords) self
    worldcoords)
              (send worldcoords :replace-coords self))
          (send self :update)
          (setf changed nil))
       worldcoords))

[ロボットの動作生成]()
----------------------

### [逆運動学]()

逆運動学においては, エンドエフェクタの位置・姿勢 <span class="MATH">![\$
\^0\_n\$](jmanual-img15.png){width="83" height="36"}<span
class="MATH">**![\$ H\$](jmanual-img16.png){width="13"
height="34"}**</span></span>から マニピュレータの関節角度ベクトル <span
class="MATH">**<span class="MATH">**![\$
\\theta\$](jmanual-img17.png){width="19" height="14"}**</span>![\$
=(\\theta\_1, \\theta\_2, ...,
\\theta\_n)\^T\$](jmanual-img18.png){width="12" height="14"}**</span>
を求める.

ここで, エンドエフェクタの 位置・姿勢 <span class="MATH">**<span
class="MATH">**![\$ r\$](jmanual-img19.png){width="124"
height="35"}**</span>**</span> は関節角度ベクトルを用いて

<div class="mathdisplay">

[]()
  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------
  ![\$\\displaystyle \\mbox{\\boldmath {\$r\$}}\$](jmanual-img20.png){width="12" height="13"}![\$\\displaystyle =\$](jmanual-img21.png){width="13" height="30"}   ![\$\\displaystyle \\mbox{\\boldmath {\$f\$}}\$](jmanual-img22.png){width="17" height="30"}![\$\\displaystyle (\$](jmanual-img23.png){width="15" height="30"}![\$\\displaystyle \\mbox{\\boldmath {\$\\theta\$}}\$](jmanual-img24.png){width="11" height="32"}![\$\\displaystyle )\$](jmanual-img25.png){width="14" height="30"}           (<span class="arabic">1</span>)
  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------

</div>

\
とかける.Equation [18.2.1](#eq:forward-kinematics-functional) はEquation [18.2.1](#eq:inverse-kinematics-func) 
のように記述し,関節角度ベクトルを求める.

<div class="mathdisplay">

[]()
  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------
  ![\$\\displaystyle \\mbox{\\boldmath {\$\\theta\$}}\$](jmanual-img24.png){width="11" height="32"}![\$\\displaystyle =\$](jmanual-img21.png){width="13" height="30"}   ![\$\\displaystyle \\mbox{\\boldmath {\$f\$}}\$](jmanual-img22.png){width="17" height="30"}![\$\\displaystyle \^{-1}(\$](jmanual-img26.png){width="11" height="32"}![\$\\displaystyle \\mbox{\\boldmath {\$r\$}}\$](jmanual-img20.png){width="12" height="13"}![\$\\displaystyle )\$](jmanual-img25.png){width="14" height="30"}           (<span class="arabic">2</span>)
  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------

</div>

\

[]()における<span class="MATH">**![\$
f\^{-1}\$](jmanual-img27.png){width="28"
height="37"}**</span>は一般に非線形な関数となる.
そこで[]()を時刻tに関して微分することで, 線形な式

<div class="mathdisplay">

[]()
  ---------------------------------------------------------------------------------------------------- -------------------------------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
  ![\$\\displaystyle \\dot{\\mbox{\\boldmath {\$r\$}}}\$](jmanual-img28.png){width="31" height="34"}   ![\$\\displaystyle =\$](jmanual-img21.png){width="13" height="30"}   ![\$\\displaystyle \\frac{\\partial \\mbox{\\boldmath {\$f\$}}}{\\partial \\mbox{\\boldmath {\$\\theta\$}}} (\\mbox{\\boldmath {\$\\theta\$}})\\dot{\\mbox{\\boldmath {\$\\theta\$}}}\$](jmanual-img29.png){width="13" height="30"}                                                                                                                                       (<span class="arabic">3</span>)
                                                                                                       ![\$\\displaystyle =\$](jmanual-img21.png){width="13" height="30"}   ![\$\\displaystyle \\mbox{\\boldmath {\$J\$}}\$](jmanual-img30.png){width="60" height="52"}![\$\\displaystyle (\$](jmanual-img23.png){width="15" height="30"}![\$\\displaystyle \\mbox{\\boldmath {\$\\theta\$}}\$](jmanual-img24.png){width="11" height="32"}![\$\\displaystyle )\\dot{\\mbox{\\boldmath {\$\\theta\$}}}\$](jmanual-img31.png){width="16" height="30"}   (<span class="arabic">4</span>)
  ---------------------------------------------------------------------------------------------------- -------------------------------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------

</div>

\
を得る. ここで, <span class="MATH">**<span class="MATH">**![\$
J\$](jmanual-img32.png){width="20" height="38"}**</span>![\$
(\$](jmanual-img33.png){width="15" height="14"}<span class="MATH">**![\$
\\theta\$](jmanual-img17.png){width="19" height="14"}**</span>![\$
)\$](jmanual-img34.png){width="11" height="32"}**</span>は <span
class="MATH">**![\$ m \\times n\$](jmanual-img35.png){width="11"
height="32"}**</span>のヤコビ行列である. <span class="MATH">**![\$
m\$](jmanual-img36.png){width="47" height="30"}**</span>はベクトル <span
class="MATH">**<span class="MATH">**![\$
r\$](jmanual-img19.png){width="124"
height="35"}**</span>**</span>の次元, <span class="MATH">**![\$
n\$](jmanual-img37.png){width="18" height="13"}**</span>はベクトル <span
class="MATH">**<span class="MATH">**![\$
\\theta\$](jmanual-img17.png){width="19"
height="14"}**</span>**</span>の次元である. <span class="MATH">**<span
class="MATH">**![\$ \\dot{r}\$](jmanual-img38.png){width="14"
height="13"}**</span>**</span>は速度・角速度ベクトルである.

ヤコビ行列が正則であるとき逆行列 <span class="MATH">**<span
class="MATH">**![\$ J\$](jmanual-img32.png){width="20"
height="38"}**</span>![\$ (\$](jmanual-img33.png){width="15"
height="14"}<span class="MATH">**![\$
\\theta\$](jmanual-img17.png){width="19" height="14"}**</span>![\$
)\^{-1}\$](jmanual-img39.png){width="12" height="17"}**</span>を用いて
以下のようにしてこの線型方程式の解を得ることができる.

<div class="mathdisplay">

[]()
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------
  ![\$\\displaystyle \\dot{\\mbox{\\boldmath {\$\\theta\$}}} = \\mbox{\\boldmath {\$J\$}}(\\mbox{\\boldmath {\$\\theta\$}})\^{-1}\\dot{\\mbox{\\boldmath {\$r\$}}}\$](jmanual-img40.png){width="28" height="34"}           (<span class="arabic">5</span>)
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------

</div>

\

しかし, 一般にヤコビ行列は正則でないので, ヤコビ行列の疑似逆行列 <span
class="MATH">**<span class="MATH">**![\$
J\$](jmanual-img32.png){width="20" height="38"}**</span>![\$ \^{\\char93
}(\$](jmanual-img41.png){width="95" height="38"}<span
class="MATH">**![\$ \\theta\$](jmanual-img17.png){width="19"
height="14"}**</span>![\$ )\$](jmanual-img34.png){width="11"
height="32"}**</span>
が用いられる(Equation [6](#eq:psuedo-inverse-matrix) ).

<div class="mathdisplay">

[]()
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------
  ![\$\\displaystyle \\mbox{\\boldmath {\$A\$}}\$](jmanual-img42.png){width="22" height="35"}![\\begin{displaymath}\^{\\char93 } = \\left\\{ \\begin{array}{l l} & \\mbox{\\boldmath {\$... ...}\^T \\ ( m &gt; n = rank \\mbox{\\boldmath {\$A\$}}) \\end{array}\\right.\\end{displaymath}](jmanual-img43.png){width="14" height="30"}           (<span class="arabic">6</span>)
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------

</div>

\

Equation [4](#eq:inverse-kinematics-base) は， <span class="MATH">**![\$
m&gt;n\$](jmanual-img44.png){width="420"
height="77"}**</span>のときはEquation [7](#eq:inverse-kinematics-error-func) を，
<span class="MATH">**![\$ n&gt;=m\$](jmanual-img45.png){width="49"
height="30"}**</span>のときはEquation [9](#eq:inverse-kinematics-min-func) を，
最小化する最小二乗解を求める問題と捉え,解を得る.

<div class="mathdisplay">

[]()
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------
  ![\$\\displaystyle \\min\_{\\dot{\\mbox{\\boldmath {\$\\theta\$}}}} \\left(\\dot{\\mbox{\\boldma... ...ath {\$J\$}}(\\mbox{\\boldmath {\$\\theta\$}})\\dot{\\mbox{\\boldmath {\$\\theta\$}}}\\right)\$](jmanual-img46.png){width="62" height="30"}           (<span class="arabic">7</span>)
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------

</div>

\

<div class="mathdisplay">

[]()
  -------------------------------------------------------------------------------------------------------------------- --- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
  ![\$\\displaystyle \\min\_{\\dot{\\mbox{\\boldmath {\$\\theta\$}}}}\$](jmanual-img47.png){width="227" height="52"}       ![\$\\displaystyle \\dot{\\mbox{\\boldmath {\$\\theta\$}}}\^{T}\\dot{\\mbox{\\boldmath {\$\\theta\$}}}\$](jmanual-img48.png){width="31" height="44"}                                                       (<span class="arabic">8</span>)
  ![\$\\displaystyle s.t.\$](jmanual-img49.png){width="33" height="45"}                                                    ![\$\\displaystyle \\dot{\\mbox{\\boldmath {\$r\$}}} = \\mbox{\\boldmath {\$J\$}}(\\mbox{\\boldmath {\$\\theta\$}})\\dot{\\mbox{\\boldmath {\$\\theta\$}}}\$](jmanual-img50.png){width="27" height="30"}    
  -------------------------------------------------------------------------------------------------------------------- --- ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------

</div>

\

関節角速度は次のように求まる．

<div class="mathdisplay">

[]()
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------
  ![\$\\displaystyle \\dot{\\mbox{\\boldmath {\$\\theta\$}}} = \\mbox{\\boldmath {\$J\$}}\^{\\char... ...mbox{\\boldmath {\$J\$}}(\\mbox{\\boldmath {\$\\theta\$}})\\right)\\mbox{\\boldmath {\$z\$}}\$](jmanual-img51.png){width="78" height="38"}           (<span class="arabic">9</span>)
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ---------------------------------

</div>

\

しかしながら,
Equation [9](#eq:inverse-kinematics-lagrangian-formula) に従って解を
求めると, ヤコビ行列 <span class="MATH">**<span class="MATH">**![\$
J\$](jmanual-img32.png){width="20" height="38"}**</span>![\$
(\$](jmanual-img33.png){width="15" height="14"}<span class="MATH">**![\$
\\theta\$](jmanual-img17.png){width="19" height="14"}**</span>![\$
)\$](jmanual-img34.png){width="11"
height="32"}**</span>がフルランクでなくなる特異点に近づく と, <span
class="MATH">**![\$ \\left\\vert\\dot{\\mbox{\\boldmath
{\$\\theta\$}}}\\right\\vert\$](jmanual-img52.png){width="259"
height="45"}**</span>が大きくなり不安定な振舞いが生じる. そこで,
Nakamura et al.のSR-Inverse[^<span
class="arabic">1</span>^](jmanual-footnode.html#foot871)を用いること で,
この特異点を回避する.

本研究では ヤコビ行列の疑似逆行列 <span class="MATH">**<span
class="MATH">**![\$ J\$](jmanual-img32.png){width="20"
height="38"}**</span>![\$ \^{\\char93
}(\$](jmanual-img41.png){width="95" height="38"}<span
class="MATH">**![\$ \\theta\$](jmanual-img17.png){width="19"
height="14"}**</span>![\$ )\$](jmanual-img34.png){width="11"
height="32"}**</span>の代わりに,
Equation [10](#eq:SR-inverse-jacobian) に示す <span class="MATH">**<span
class="MATH">**![\$ J\$](jmanual-img32.png){width="20"
height="38"}**</span>![\$ \^{\*}(\$](jmanual-img53.png){width="25"
height="45"}<span class="MATH">**![\$
\\theta\$](jmanual-img17.png){width="19" height="14"}**</span>![\$
)\$](jmanual-img34.png){width="11" height="32"}**</span> を用いる.

<div class="mathdisplay">

[]()
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\$\\displaystyle \\mbox{\\boldmath {\$J\$}}\$](jmanual-img30.png){width="60" height="52"}![\$\\displaystyle \^{\*}(\$](jmanual-img54.png){width="18" height="32"}![\$\\displaystyle \\mbox{\\boldmath {\$\\theta\$}}\$](jmanual-img24.png){width="11" height="32"}![\$\\displaystyle ) =\$](jmanual-img55.png){width="18" height="32"}   ![\$\\displaystyle \\mbox{\\boldmath {\$J\$}}\$](jmanual-img30.png){width="60" height="52"}![\$\\displaystyle \^T\\left(\\mbox{\\boldmath {\$J\$}}\\mbox{\\boldmath {\$J\$}}\^T + \\epsilon \\mbox{\\boldmath {\$E\$}}\_m\\right)\^{-1}\$](jmanual-img56.png){width="28" height="32"}           (<span class="arabic">10</span>)
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\

これは, Equation [7](#eq:inverse-kinematics-error-func) の代わりに,
Equation [11](#eq:SR-inverse-error-func) を最小化する最適化問題を
解くことにより得られたものである.

<div class="mathdisplay">

[]()
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ --- --- ----------------------------------
  ![\$\\displaystyle \\min\_{\\dot{\\mbox{\\boldmath {\$\\theta\$}}}} \\{ \\dot{\\mbox{\\boldmath ... ... {\$J\$}}(\\mbox{\\boldmath {\$\\theta\$}})\\dot{\\mbox{\\boldmath {\$\\theta\$}}}\\right) \\}\$](jmanual-img57.png){width="138" height="51"}           (<span class="arabic">11</span>)
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ --- --- ----------------------------------

</div>

\

ヤコビ行列 <span class="MATH">**<span class="MATH">**![\$
J\$](jmanual-img32.png){width="20" height="38"}**</span>![\$
(\$](jmanual-img33.png){width="15" height="14"}<span class="MATH">**![\$
\\theta\$](jmanual-img17.png){width="19" height="14"}**</span>![\$
)\$](jmanual-img34.png){width="11"
height="32"}**</span>が特異点に近づいているかの指標には 可操作度 <span
class="MATH">**![\$ \\kappa(\$](jmanual-img58.png){width="298"
height="52"}<span class="MATH">**![\$
\\theta\$](jmanual-img17.png){width="19" height="14"}**</span>![\$
)\$](jmanual-img34.png){width="11" height="32"}**</span>[^<span
class="arabic">2</span>^](jmanual-footnode.html#foot909)が用いられる(Equation [12](#eq:manipulability) ).

<div class="mathdisplay">

[]()
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\$\\displaystyle \\kappa(\$](jmanual-img59.png){width="20" height="32"}![\$\\displaystyle \\mbox{\\boldmath {\$\\theta\$}}\$](jmanual-img24.png){width="11" height="32"}![\$\\displaystyle ) = \\sqrt{\\mbox{\\boldmath {\$J\$}}(\\mbox{\\boldmath {\$\\theta\$}}) \\mbox{\\boldmath {\$J\$}}\^{T}(\\mbox{\\boldmath {\$\\theta\$}})}\$](jmanual-img60.png){width="20" height="32"}           (<span class="arabic">12</span>)
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\

微分運動学方程式における タスク空間次元の選択行列[^<span
class="arabic">3</span>^](jmanual-footnode.html#foot920)は見通しの良い定式化のために省略するが,
以降で導出する全ての式において
適用可能であることをあらかじめことわっておく.

### [基礎ヤコビ行列]()

一次元対偶を関節に持つマニピュレータのヤコビアンは 基礎ヤコビ行列[^<span
class="arabic">4</span>^](jmanual-footnode.html#foot922)により
計算することが可能である. 基礎ヤコビ行列の第<span class="MATH">**![\$
j\$](jmanual-img61.png){width="125"
height="53"}**</span>関節に対応するヤコビアンの列ベクトル <span
class="MATH">**<span class="MATH">**![\$
J\$](jmanual-img32.png){width="20" height="38"}**</span>![\$
\_j\$](jmanual-img62.png){width="12" height="29"}**</span>は

<div class="mathdisplay">

[]()
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------
  <span class="MATH">![\$\\displaystyle \\mbox{\\boldmath {\$J\$}}\$](jmanual-img30.png){width="60" height="52"}![\$\\displaystyle \_j= \\begin{cases}\\left\[ \\begin{array}{ccc} \\mbox{\\boldmath {\$a\$}}... ...{\\boldmath {\$a\$}}\_j \\end{array} \\right\] & \\text{if rotational joint}\\end{cases}\$](jmanual-img63.png){width="11" height="30"}</span>   (<span class="arabic">13</span>)
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------

</div>

\

と表される. <span class="MATH">**<span class="MATH">**![\$
a\$](jmanual-img8.png){width="37" height="20"}**</span>![\$
\_j\$](jmanual-img62.png){width="12" height="29"}**</span>・ <span
class="MATH">**<span class="MATH">**![\$
p\$](jmanual-img64.png){width="334" height="113"}**</span>![\$
\_j\$](jmanual-img62.png){width="12"
height="29"}**</span>はそれぞれ第<span class="MATH">**![\$
j\$](jmanual-img61.png){width="125"
height="53"}**</span>関節の関節軸単位ベクトル・位置ベクトルであり, <span
class="MATH">**<span class="MATH">**![\$
p\$](jmanual-img64.png){width="334" height="113"}**</span>![\$
\_{end}\$](jmanual-img65.png){width="13"
height="30"}**</span>はヤコビアンで運動を制御するエンドエフェクタの位置ベクトルである.
上記では1自由度対偶の 回転関節・直動関節について導出したが，
その他の関節でもこれらの列ベクトルを
連結した行列としてヤコビアンを定義可能である．
非全方位台車の運動を表す2自由度関節は 前後退の直動関節と
旋回のための回転関節から構成できる． 全方位台車の運動を表す3自由度関節は
並進2自由度の直動関節と 旋回のための回転関節から構成できる．
球関節は姿勢を姿勢行列で， 姿勢変化を等価角軸変換によるものとすると，
3つの回転関節をつなぎ合わせたものとみなせる．

### [関節角度限界回避を含む逆運動学]()

ロボットマニピュレータの軌道生成において,
関節角度限界を考慮することはロボットによる実機実験の際に重要となる.本節では,文献[^<span
class="arabic">5</span>^](jmanual-footnode.html#foot950)[^<span
class="arabic">6</span>^](jmanual-footnode.html#foot1568)の式および文章を引用しつつ,
関節角度限界の回避を 含む逆運動学について説明する.

重み付きノルムを以下のように定義する.

<div class="mathdisplay">

[]()
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ --- --- ----------------------------------
  ![\$\\displaystyle \\left\\vert\\mbox{\\boldmath {\$\\dot{\\theta}\$}}\\right\\vert \_{\\mbox{\\b... ...ath {\$\\dot{\\theta}\$}}\^T\\mbox{\\boldmath {\$W\$}}\\mbox{\\boldmath {\$\\dot{\\theta}\$}}}\$](jmanual-img66.png){width="26" height="30"}           (<span class="arabic">14</span>)
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ --- --- ----------------------------------

</div>

\

ここで, <span class="MATH">**<span class="MATH">**![\$
W\$](jmanual-img67.png){width="131" height="60"}**</span>**</span>は
<span class="MATH">**<span class="MATH">**![\$
W\$](jmanual-img67.png){width="131" height="60"}**</span>![\$
\\in\$](jmanual-img68.png){width="22" height="14"}   <span
class="MATH">**![\$ R\$](jmanual-img69.png){width="15"
height="30"}**</span>![\$ \^{n \\times
n}\$](jmanual-img70.png){width="17" height="14"}**</span>であり,
対象で全ての要 素が正である重み係数行列である. この <span
class="MATH">**<span class="MATH">**![\$
W\$](jmanual-img67.png){width="131"
height="60"}**</span>**</span>を用いて, <span class="MATH">**<span
class="MATH">**![\$ J\$](jmanual-img32.png){width="20"
height="38"}**</span>![\$ \_{\\mbox{\\boldmath {\$W\$}}},
\\mbox{\\boldmath {\$\\dot{\\theta}\$}}\_{\\mbox{\\boldmath
{\$W\$}}}\$](jmanual-img71.png){width="31"
height="19"}**</span>を以下のよう に定義する.

<div class="mathdisplay">

  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\$\\displaystyle \\mbox{\\boldmath {\$J\$}}\$](jmanual-img30.png){width="60" height="52"}![\$\\displaystyle \_{\\mbox{\\boldmath {\$W\$}}} = \\mbox{\\boldmath {\$J\$}}\\mbox{\\boldmath... ...{\$W\$}}} = \\mbox{\\boldmath {\$W\$}}\^{\\frac{1}{2}}\\mbox{\\boldmath {\$\\dot{\\theta}\$}}\$](jmanual-img72.png){width="63" height="38"}           (<span class="arabic">15</span>)
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\

この <span class="MATH">**<span class="MATH">**![\$
J\$](jmanual-img32.png){width="20" height="38"}**</span>![\$
\_{\\mbox{\\boldmath {\$W\$}}}, \\mbox{\\boldmath
{\$\\dot{\\theta}\$}}\_{\\mbox{\\boldmath
{\$W\$}}}\$](jmanual-img71.png){width="31"
height="19"}**</span>を用いて, 以下の式を得 る.

<div class="mathdisplay">

  ------------------------------------------------------------------------------------------------------------------------------------------------------------------ -------------------------------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------
  ![\$\\displaystyle \\dot{\\mbox{\\boldmath {\$r\$}}}\$](jmanual-img28.png){width="31" height="34"}                                                                 ![\$\\displaystyle =\$](jmanual-img21.png){width="13" height="30"}   ![\$\\displaystyle \\mbox{\\boldmath {\$J\$}}\$](jmanual-img30.png){width="60" height="52"}![\$\\displaystyle \_{\\mbox{\\boldmath {\$W\$}}}\\mbox{\\boldmath {\$\\dot{\\theta}\$}}\_{\\mbox{\\boldmath {\$W\$}}}\$](jmanual-img73.png){width="196" height="41"}   (<span class="arabic">16</span>)
  ![\$\\displaystyle \\left\\vert\\dot{\\mbox{\\boldmath {\$\\theta\$}}}\\right\\vert \_{\\mbox{\\boldmath {\$W\$}}}\$](jmanual-img74.png){width="55" height="38"}   ![\$\\displaystyle =\$](jmanual-img21.png){width="13" height="30"}   ![\$\\displaystyle \\sqrt{\\mbox{\\boldmath {\$\\dot{\\theta}\$}}\_{\\mbox{\\boldmath {\$W\$}}}\^T\\mbox{\\boldmath {\$\\dot{\\theta}\$}}\_{\\mbox{\\boldmath {\$W\$}}}}\$](jmanual-img75.png){width="45" height="45"}                                             (<span class="arabic">17</span>)
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------ -------------------------------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ----------------------------------

</div>

\

これによって線型方程式の解は@xdefthefnmark[9](#LimitAvoidance:Fung:RA95)footnotemarkから
以下のように記述できる.

<div class="mathdisplay">

  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\$\\displaystyle \\mbox{\\boldmath {\$\\dot{\\theta}\$}}\$](jmanual-img76.png){width="81" height="54"}![\$\\displaystyle \_{\\mbox{\\boldmath {\$W\$}}} = \\mbox{\\boldmath {\$W\$}}\^{-1}\\mbox{\\bol... ...ath {\$W\$}}\^{-1}\\mbox{\\boldmath {\$J\$}}\^T\\right)\^{-1}\\dot{\\mbox{\\boldmath {\$r\$}}}\$](jmanual-img77.png){width="14" height="38"}           (<span class="arabic">18</span>)
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\

また、現在の関節角度<span class="MATH">**![\$
\\theta\$](jmanual-img17.png){width="19"
height="14"}**</span>が関節角度限界 <span class="MATH">**![\$
\\theta\_{i,\\max}, \\theta\_{i,
\\min}\$](jmanual-img78.png){width="226"
height="51"}**</span>に対してどの程度余裕があるかを評価する ための関数
<span class="MATH">**![\$ H(\$](jmanual-img79.png){width="89"
height="30"}<span class="MATH">**![\$
\\theta\$](jmanual-img17.png){width="19" height="14"}**</span>![\$
)\$](jmanual-img34.png){width="11"
height="32"}**</span>は以下のようになる[^<span
class="arabic">7</span>^](jmanual-footnode.html#foot1017)).

<div class="mathdisplay">

[]()
  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\$\\displaystyle H(\$](jmanual-img80.png){width="25" height="32"}![\$\\displaystyle \\mbox{\\boldmath {\$\\theta\$}}\$](jmanual-img24.png){width="11" height="32"}![\$\\displaystyle ) = \\sum\_{i = 1}\^n\\frac{1}{4} \\frac{(\\theta\_{i,\\max} - \\theta\_{i,\\min})\^2} {(\\theta\_{i,\\max} - \\theta\_i)(\\theta\_i - \\theta\_{i,\\min})}\$](jmanual-img81.png){width="25" height="32"}           (<span class="arabic">19</span>)
  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\

次にEquation [20](#eq:joint-weight-matrix) に示すような <span
class="MATH">**![\$ n \\times n\$](jmanual-img82.png){width="240"
height="62"}**</span>の重み係数行列 <span class="MATH">**<span
class="MATH">**![\$ W\$](jmanual-img67.png){width="131"
height="60"}**</span>**</span>を考える.

<div class="mathdisplay">

[]()
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\$\\displaystyle \\mbox{\\boldmath {\$W\$}}\$](jmanual-img83.png){width="43" height="30"}![\\begin{displaymath}= \\left\[ \\begin{array}{ccccc} w\_1 & 0 & 0 & \\cdots & 0 \\\\ 0 ... ...ts & \\cdots \\\\ 0 & 0 & 0 & \\cdots & w\_n \\\\ \\end{array}\\right\]\\end{displaymath}](jmanual-img84.png){width="19" height="30"}           (<span class="arabic">20</span>)
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\
ここで<span class="MATH">**![\$ w\_i\$](jmanual-img85.png){width="386"
height="103"}**</span>は

<div class="mathdisplay">

  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\$\\displaystyle w\_i = 1 + \\left\\vert\\frac{\\partial \\mbox{\\boldmath {\$H\$}}(\\mbox{\\boldmath {\$\\theta\$}})}{\\partial \\theta\_i}\\right\\vert\$](jmanual-img86.png){width="21" height="30"}           (<span class="arabic">21</span>)
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\
である.

さらにEquation [19](#eq:joint-performance-func) から次の式を得る.

<div class="mathdisplay">

  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\$\\displaystyle \\frac{\\partial H(\\mbox{\\boldmath {\$\\theta\$}})}{\\partial \\theta\_i}... ...heta\_{i,\\min})} {4(\\theta\_{i,\\max} - \\theta\_i)\^2(\\theta\_i - \\theta\_{i,\\min})\^2}\$](jmanual-img87.png){width="132" height="54"}           (<span class="arabic">22</span>)
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\

関節角度限界から遠ざかる向きに関節角度が動いている場合には重み係数行列を
変化させる必要はないので,<span class="MATH">**![\$
w\_i\$](jmanual-img85.png){width="386"
height="103"}**</span>を以下のように定義しなおす.

<div class="mathdisplay">

  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\\begin{displaymath}w\_i = \\left\\{ \\begin{array}{l l} 1 + \\left\\vert\\frac{\\partial... ...theta\$}})}{\\partial \\theta\_i}\\right\\vert &lt; 0 \\end{array}\\right.\\end{displaymath}](jmanual-img88.png){width="345" height="58"}           (<span class="arabic">23</span>)
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\
この<span class="MATH">**![\$ w\_i\$](jmanual-img85.png){width="386"
height="103"}**</span>および <span class="MATH">**<span
class="MATH">**![\$ W\$](jmanual-img67.png){width="131"
height="60"}**</span>**</span>を用いることで関節角度限界回避を含む逆運動学を解くこ
とができる.

### [衝突回避を含む逆運動学]()

ロボットの動作中での自己衝突や環境モデルとの衝突は
幾何形状モデルが存在すれば計算することが可能である. ここではSugiura et
al. により提案されている効率的な衝突回避計算 [^<span
class="arabic">8</span>^](jmanual-footnode.html#foot1070)[^<span
class="arabic">9</span>^](jmanual-footnode.html#foot1607)を応用した動作生成法を示す.
実際の実装はSugiura et al. の手法に加え,
タスク作業空間のNullSpaceの利用を係数により制御できるようにした点や
擬似逆行列ではなくSR-Inverseを用いて特異点に
ロバストにしている点などが追加されている.

### [衝突回避のための関節角速度計算法]()

逆運動学計算における目標タスクと衝突回避の統合は
リンク間最短距離を用いたblending係数により行われる.
これにより,衝突回避の必要のないときは目標タスクを厳密に満し
衝突回避の必要性があらわれたときに目標タスクを
あきらめて衝突回避の行われる関節角速度計算を行うことが可能になる.
最終的な関節角速度の関係式はEquation [24](#eq:collision-avoidance-all) で得られる.
以下では<span class="MATH">**![\$ ca\$](jmanual-img89.png){width="408"
height="74"}**</span>の添字は衝突回避計算のための成分を表し, <span
class="MATH">**![\$ task\$](jmanual-img90.png){width="20"
height="13"}**</span>の部分は衝突回避計算以外のタスク目標を表すことにする.

<div class="mathdisplay">

[]()
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------
  <span class="MATH">![\$\\displaystyle \\mbox{\\boldmath {\$\\dot{\\theta}\$}}\$](jmanual-img76.png){width="81" height="54"}![\$\\displaystyle = f(d)\$](jmanual-img91.png){width="35" height="14"}![\$\\displaystyle \\mbox{\\boldmath {\$\\dot{\\theta}\$}}\$](jmanual-img76.png){width="81" height="54"}![\$\\displaystyle \_{ca} + \\left(1-f(d)\\right)\$](jmanual-img92.png){width="51" height="32"}![\$\\displaystyle \\mbox{\\boldmath {\$\\dot{\\theta}\$}}\$](jmanual-img76.png){width="81" height="54"}![\$\\displaystyle \_{task}\$](jmanual-img93.png){width="107" height="32"}</span>   (<span class="arabic">24</span>)
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------

</div>

\

blending係数<span class="MATH">**![\$
f(d)\$](jmanual-img94.png){width="30" height="30"}**</span>は,
リンク間距離<span class="MATH">**![\$ d\$](jmanual-img95.png){width="35"
height="32"}**</span>と閾値<span class="MATH">**![\$
d\_a\$](jmanual-img96.png){width="13" height="14"}**</span>・<span
class="MATH">**![\$ d\_b\$](jmanual-img97.png){width="20"
height="30"}**</span>の関数として計算される
(Equation [25](#eq:collision-avoidance-blending-coefficient) ).

<div class="mathdisplay">

[]()
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\\begin{displaymath}f(d) = \\left\\{ \\begin{array}{l l} (d-d\_a)/(d\_b-d\_a) & if d&lt;d\_a\\\\ 0 & otherwise \\end{array}\\right.\\end{displaymath}](jmanual-img98.png){width="14" height="30"}           (<span class="arabic">25</span>)
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\

<span class="MATH">**![\$ d\_a\$](jmanual-img96.png){width="13"
height="14"}**</span>は衝突回避計算を行い始める値 (yellow
zone@xdefthefnmark[12](#WholebodyCollisionAvoidance:Sugiura:IROS07)footnotemark)であり,
<span class="MATH">**![\$ d\_b\$](jmanual-img97.png){width="20"
height="30"}**</span>は目標タスクを阻害しても衝突回避を行う閾値 (orange
zone@xdefthefnmark[12](#WholebodyCollisionAvoidance:Sugiura:IROS07)footnotemark)である.

衝突計算をする2リンク間の最短距離・最近傍点が計算できた場合の
衝突を回避するための動作戦略は
2リンク間に作用する仮想的な反力ポテンシャルから導出される.

2リンク間の最近傍点同士をつなぐベクトル <span class="MATH">**<span
class="MATH">**![\$ p\$](jmanual-img64.png){width="334"
height="113"}**</span>**</span>を用いた
2リンク間反力から導出される速度計算を
Equation [26](#eq:collision-avoidance-distance-force)  に記す.

<div class="mathdisplay">

[]()
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\$\\displaystyle \\mbox{\\boldmath {\$\\delta x\$}}\$](jmanual-img99.png){width="416" height="54"}![\\begin{displaymath}= \\left\\{ \\begin{array}{l l} 0 & if \\left\\vert\\mbox{\\boldmath... ...}\\right\\vert-1)\\mbox{\\boldmath {\$p\$}} & else \\end{array}\\right.\\end{displaymath}](jmanual-img100.png){width="18" height="30"}           (<span class="arabic">26</span>)
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\

これを用いた関節角速度計算はEquation [27](#eq:collision-avoidance-joint-speed) 
となる.

<div class="mathdisplay">

[]()
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------
  <span class="MATH">![\$\\displaystyle \\dot{\\mbox{\\boldmath {\$\\theta\$}}}\_{ca} = \\mbox{\\boldmath {\$J\$}}\_{... ...\_{task}) \\mbox{\\boldmath {\$J\$}}\_{ca}\^{T} k\_{null} \\mbox{\\boldmath {\$\\delta x\$}}\$](jmanual-img101.png){width="384" height="54"}</span>   (<span class="arabic">27</span>)
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------

</div>

\

<span class="MATH">**![\$ k\_{joint}\$](jmanual-img102.png){width="357"
height="38"}**</span>・<span class="MATH">**![\$
k\_{null}\$](jmanual-img103.png){width="43"
height="30"}**</span>はそれぞれ反力ポテンシャルを
目標タスクのNullSpaceに分配するかそうでないかを制御する係数である.

### [衝突回避計算例]()

以下ではロボットモデル・環境モデルを用いた衝突回避例を示す. 本研究では,
ロボットのリンク同士,またはリンクと物体の衝突判定には,衝突判定ライブラリ
PQP(A Proximity Query Package) [^<span
class="arabic">10</span>^](jmanual-footnode.html#foot1130)を用いた.

Fig.[19](#fig:collision-avoidance-example) では <span
class="MATH">**![\$ d\_a = 200\[mm\]\$](jmanual-img104.png){width="37"
height="30"}**</span>, <span class="MATH">**![\$ d\_b = 0.1 \* d\_a =
20\[mm\]\$](jmanual-img105.png){width="102" height="32"}**</span>, <span
class="MATH">**![\$ k\_{joint} = k\_{null} =
1.0\$](jmanual-img106.png){width="166" height="32"}**</span>と設定した.

この衝突判定計算では,衝突判定をリンクの設定を

1.  リンクのリスト<span class="MATH">**![\$
    n\_{ca}\$](jmanual-img107.png){width="138"
    height="30"}**</span>を登録
2.  登録されたリンクのリストから全リンクのペア <span class="MATH">**![\$
    \_{n\_{ca}}C\_2\$](jmanual-img108.png){width="27"
    height="30"}**</span>を計算
3.  隣接するリンクのペア,常に交わりを持つリンクのペアなどを除外

のように行うという工夫を行っている.

Fig.[19](#fig:collision-avoidance-example) 例では衝突判定をするリンクを
「前腕リンク」「上腕リンク」「体幹リンク」「ベースリンク」
の4つとして登録した. この場合, <span class="MATH">**![\$
\_4C\_2\$](jmanual-img109.png){width="44"
height="30"}**</span>通りのリンクのペア数から
隣接するリンクが除外され,全リンクペアは 「前腕リンク-体幹リンク」
「前腕リンク-ベースリンク」 「上腕リンク-ベースリンク」 の3通りとなる.

Fig.[19](#fig:collision-avoidance-example) の3本の線(赤1本,緑2本)が
衝突形状モデル間での最近傍点同士をつないだ 最短距離ベクトルである.
全リンクペアのうち赤い線が最も距離が近いペアであり,
このリンクペアより衝突回避のための 逆運動学計算を行っている.

<div>

[]()[]()
+--------------------------------------------------------------------------+
| <div>                                                                    |
|                                                                          |
| ![Image                                                                  |
| collision-avoidance-example](./collision-avoidance-example.jpg){width="4 |
| 27"                                                                      |
| height="428"}                                                            |
|                                                                          |
| </div>                                                                   |
+--------------------------------------------------------------------------+

: **Figure 19:** Example of Collision Avoidance

</div>

### [非ブロック対角ヤコビアンによる全身協調動作生成]()

ヒューマノイドは枝分かれのある複雑な構造を持ち,
複数のマニピュレータで協調して動作を行う必要がある
(Fig.[20](#fig:duplicate-link) )．

<div>

[]()[]()
+--------------------------------------------------------------------------+
| <div>                                                                    |
|                                                                          |
| ![Image normal](./normal.jpg){width="528" height="500"} ![Image          |
| linklist](./linklist.jpg){width="528" height="500"}                      |
|                                                                          |
| </div>                                                                   |
+--------------------------------------------------------------------------+

: **Figure 20:** Duplicate Link Sequence

</div>

複数マニピュレータの動作例として,

-   リンク間に重複がない場合 それぞれのマニピュレータについて
    Equation [5](#eq:inverse-kinematics) 式を用いて関節角速度を求める.
    もしくは,複数の式を連立した方程式(ヤコビアンはブロック対角行列となる) を用いて関節角速度を求めても良い.
-   リンク間に重複がある場合 リンク間に重複がある場合は,
    リンク間の重複を考慮したヤコビアンを考える必要がある.
    例えば,双腕動作を行う場合,左腕のマニピュレータのリンク系列と
    右腕のマニピュレータのリンク系列とで,体幹部リンク系列が重複し, その部位は左右で協調して関節角速度を求める必要がある.

次節ではリンク間に重複がある場合の 非ブロック対角なヤコビアンの計算法
および それを用いた関節角速度計算法を述べる
(前者の重複がない場合も以下の計算方法により後者の一部として計算可能で
ある).

### [リンク間重複があるヤコビアン計算と関節角度計算]()

微分運動学方程式を求める際の条件を以下に示す.

-   マニピュレータの本数　<span class="MATH">**![\$
    L\$](jmanual-img110.png){width="30" height="30"}**</span>本
-   全関節数　<span class="MATH">**![\$
    N\$](jmanual-img111.png){width="15" height="14"}**</span>個
-   マニピュレータの先端速度・角速度ベクトル　 <span class="MATH">**![\$
    \[\$](jmanual-img112.png){width="19" height="14"}<span
    class="MATH">**![\$ \\xi\$](jmanual-img113.png){width="9"
    height="32"}**</span>![\$
    \_0\^T,...,\$](jmanual-img114.png){width="12" height="30"}<span
    class="MATH">**![\$ \\xi\$](jmanual-img113.png){width="9"
    height="32"}**</span>![\$
    \_{L-1}\^T\]\^T\$](jmanual-img115.png){width="39"
    height="35"}**</span>
-   各関節角速度ベクトル　 <span class="MATH">**![\$
    \[\$](jmanual-img112.png){width="19" height="14"} <span
    class="MATH">**![\$
    \\dot{\\theta}\_0\$](jmanual-img116.png){width="45"
    height="35"}**</span>![\$ \^T,...,\$](jmanual-img117.png){width="19"
    height="38"} <span class="MATH">**![\$
    \\dot{\\theta}\_{L-1}\$](jmanual-img118.png){width="39"
    height="35"}**</span>![\$ \^T\]\^T\$](jmanual-img119.png){width="38"
    height="38"}**</span>
-   関節の添字和集合　 <span class="MATH">**![\$ S =
    \\{0,\\hdots,N-1\\}\$](jmanual-img120.png){width="29"
    height="35"}**</span> ただし,マニピュレータ<span class="MATH">**![\$
    i\$](jmanual-img121.png){width="138"
    height="32"}**</span>の添字集合<span class="MATH">**![\$
    S\_i\$](jmanual-img122.png){width="10"
    height="17"}**</span>を用いて<span class="MATH">**![\$
    S\$](jmanual-img123.png){width="20" height="30"}**</span>は <span
    class="MATH">**![\$ S = S\_0 \\cup \\hdots \\cup
    S\_{L-1}\$](jmanual-img124.png){width="15"
    height="14"}**</span>と表せる.
-   <span class="MATH">**![\$ S\$](jmanual-img123.png){width="20"
    height="30"}**</span>に基づく関節速度ベクトル　 <span
    class="MATH">**![\$ \[\\dot{\\theta}\_0, ...,
    \\dot{\\theta}\_{N-1}\]\^T\$](jmanual-img125.png){width="143"
    height="30"}**</span>

とする.

運動学関係式はEquation [28](#eq:multi-manipulator-jacobi-eq) のようになる.

<div class="mathdisplay">

[]()
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ --- --- ----------------------------------
  ![\\begin{displaymath}\\left\[ \\begin{array}{c} \\mbox{\\boldmath {\$\\xi\$}}\_0 \\\\ \\vdots... ...ot{\\theta}\_0\\\\ \\vdots\\\\ \\dot{\\theta}\_{N-1} \\end{array}\\right\]\\end{displaymath}](jmanual-img126.png){width="94" height="38"}           (<span class="arabic">28</span>)
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ --- --- ----------------------------------

</div>

\

小行列 <span class="MATH">**<span class="MATH">**![\$
J\$](jmanual-img32.png){width="20" height="38"}**</span>![\$
\_{i,j}\$](jmanual-img127.png){width="465"
height="87"}**</span>は以下のように求まる.

![\\begin{numcases}{\\mbox{\\boldmath {\$J\$}}\_{i,j}=}
\\mbox{\\boldmath {\$J\$}}\_j & if \$j... ...nt \$\\in\$\\ \$i\$-th link
array\\\\ \\mbox{\\boldmath {\$0\$}} & otherwise
\\end{numcases}](jmanual-img128.png){width="22" height="31"} ここで，
<span class="MATH">**<span class="MATH">**![\$
J\$](jmanual-img32.png){width="20" height="38"}**</span>![\$
\_j\$](jmanual-img62.png){width="12"
height="29"}**</span>はEquation [13](#eq:basic-jacobian-column-vector) のもの．

Equation [28](#eq:multi-manipulator-jacobi-eq) を単一のマニピュレータの
逆運動学解法と同様にSR-Inverseを用いて関節角速度を 求めることができる.

ここでの非ブロック対角ヤコビアンの計算法は, アーム・多指ハンドの動作生成
[^<span class="arabic">11</span>^](jmanual-footnode.html#foot1213)に
おいて登場する運動学関係式から求まるヤコビアンを
導出することが可能である.

### [ベースリンク仮想ジョイントを用いた全身逆運動学法]()

一般に関節数が<span class="MATH">**![\$
N\$](jmanual-img111.png){width="15"
height="14"}**</span>であるのロボットの運動を表現するためには
ベースリンクの位置姿勢と関節角自由度を合わせた<span class="MATH">**![\$
N+6\$](jmanual-img129.png){width="435"
height="39"}**</span>個の変数が必要であ る．
ベースリンクとなる位置姿勢の変数を用いたロボットの運動の定式化は
宇宙ロボット[^<span
class="arabic">12</span>^](jmanual-footnode.html#foot1215)だけでなく,
環境に固定されないヒューマノイドロボット [^<span
class="arabic">13</span>^](jmanual-footnode.html#foot1216)の場合にも重要である.

ここでは 腕・脚といったマニピュレータに
ベースリンクに3自由度の直動関節と
3自由度の回転関節が仮想的に付随したマニピュレータ構成を考える
(Fig.[21](#fig:base-link-virtual-joint) ). 上記の仮想的な6自由度関節を
本研究ではベースリンク仮想ジョイントと名づける.
ベースリンク仮想ジョイントを用いることにより
ヒューマノイドの腰が動き全身関節が駆動され,
運動学,ひいては動力学的な解空間が拡充されることが期待できる.

<div>

[]()[]()
+--------------------------------------------------------------------------+
| <div>                                                                    |
|                                                                          |
| ![Image 6dof-manip-glview](./6dof-manip-glview.jpg){width="427"          |
| height="428"} ![Image                                                    |
| 6dof-manip-skelton](./6dof-manip-skelton.jpg){width="427" height="428"}  |
|                                                                          |
| </div>                                                                   |
+--------------------------------------------------------------------------+

: **Figure 21:** Concept of the Virtual Joint of the Base Link (Left
figure) Overview of the Robot Model (Right figure) Skeleton Figure of
Robot Model with the Virtual Joint

</div>

### [ベースリンク仮想ジョイントヤコビアン]()

ベースリンク仮想ジョイントのヤコビアンは
基礎ヤコビ行列の計算(Equation [13](#eq:basic-jacobian-column-vector) )
を利用し， 絶対座標系<span class="MATH">**![\$
x\$](jmanual-img130.png){width="46" height="30"}**</span>，<span
class="MATH">**![\$ y\$](jmanual-img131.png){width="14"
height="13"}**</span>，<span class="MATH">**![\$
z\$](jmanual-img132.png){width="13" height="30"}**</span>軸の直動関節と
絶対座標系<span class="MATH">**![\$ x\$](jmanual-img130.png){width="46"
height="30"}**</span>，<span class="MATH">**![\$
y\$](jmanual-img131.png){width="14" height="13"}**</span>，<span
class="MATH">**![\$ z\$](jmanual-img132.png){width="13"
height="30"}**</span>軸回りの回転関節を それぞれ連結した<span
class="MATH">**![\$ 6\\times6\$](jmanual-img133.png){width="13"
height="13"}**</span>行列である．ちなみに,並進・回転成分のルートリンク仮想ジョイントのヤコビアンは
以下のように書き下すこともできる.

<div class="mathdisplay">

[]()
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\$\\displaystyle \\mbox{\\boldmath {\$J\$}}\$](jmanual-img30.png){width="60" height="52"}![\\begin{displaymath}\_{B,l} = \\left\[ \\begin{array}{cc} \\mbox{\\boldmath {\$E\$}}\_3 & ... ...{\\boldmath {\$0\$}} & \\mbox{\\boldmath {\$E\$}}\_3 \\end{array}\\right\]\\end{displaymath}](jmanual-img134.png){width="40" height="31"}           (<span class="arabic">29</span>)
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\
<span class="MATH">**<span class="MATH">**![\$
p\$](jmanual-img64.png){width="334" height="113"}**</span>![\$ \_{B\\to
l}\$](jmanual-img135.png){width="355"
height="54"}**</span>はベースリンク位置から添字<span class="MATH">**![\$
l\$](jmanual-img136.png){width="32"
height="30"}**</span>で表現する位置までの 差分ベクトルである．

### [マスプロパティ計算]()

複数の質量・重心・慣性行列を統合し 単一の質量・重心・慣性行列の組 <span
class="MATH">**![\$ \[m\_{new},\$](jmanual-img137.png){width="10"
height="14"}   <span class="MATH">**![\$
c\$](jmanual-img138.png){width="52" height="32"}**</span>![\$
\_{new},\$](jmanual-img139.png){width="11" height="13"}   <span
class="MATH">**![\$ I\$](jmanual-img140.png){width="33"
height="30"}**</span>![\$ \_{new}\]\$](jmanual-img141.png){width="13"
height="14"}**</span> を計算する演算関数を次のように定義する．

<div class="mathdisplay">

  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------
  <span class="MATH">![\$\\displaystyle \[m\_{new},\$](jmanual-img142.png){width="33" height="32"}   ![\$\\displaystyle \\mbox{\\boldmath {\$c\$}}\$](jmanual-img143.png){width="52" height="32"}![\$\\displaystyle \_{new},\$](jmanual-img144.png){width="13" height="30"}   ![\$\\displaystyle \\mbox{\\boldmath {\$I\$}}\$](jmanual-img145.png){width="33" height="30"}![\$\\displaystyle \_{new}\] = AddMassProperty( \[m\_{1},\$](jmanual-img146.png){width="14" height="30"}   ![\$\\displaystyle \\mbox{\\boldmath {\$c\$}}\$](jmanual-img143.png){width="52" height="32"}![\$\\displaystyle \_{1},\$](jmanual-img147.png){width="224" height="32"}   ![\$\\displaystyle \\mbox{\\boldmath {\$I\$}}\$](jmanual-img145.png){width="33" height="30"}![\$\\displaystyle \_{1}\] ,\\hdots, \[m\_{K},\$](jmanual-img148.png){width="16" height="30"}   ![\$\\displaystyle \\mbox{\\boldmath {\$c\$}}\$](jmanual-img143.png){width="52" height="32"}![\$\\displaystyle \_{K},\$](jmanual-img149.png){width="87" height="32"}   ![\$\\displaystyle \\mbox{\\boldmath {\$I\$}}\$](jmanual-img145.png){width="33" height="30"}![\$\\displaystyle \_{K}\] )\$](jmanual-img150.png){width="21" height="30"}</span>   (<span class="arabic">30</span>)
  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------

</div>

\

これは次のような演算である．

<div class="mathdisplay">

  -------------------------------------------------------------------------------------------------------------------------------- ----------------------------------
  <span class="MATH">![\$\\displaystyle m\_{new} = \\sum\_{j=1}\^{K}m\_{j}\$](jmanual-img151.png){width="27" height="32"}</span>   (<span class="arabic">31</span>)
  -------------------------------------------------------------------------------------------------------------------------------- ----------------------------------

</div>

\

<div class="mathdisplay">

  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------
  <span class="MATH">![\$\\displaystyle \\mbox{\\boldmath {\$c\$}}\$](jmanual-img143.png){width="52" height="32"}![\$\\displaystyle \_{new} = \\frac{1}{m\_{new}}\\sum\_{j=1}\^{K} m\_j\$](jmanual-img152.png){width="110" height="68"}   ![\$\\displaystyle \\mbox{\\boldmath {\$c\$}}\$](jmanual-img143.png){width="52" height="32"}![\$\\displaystyle \_j\$](jmanual-img153.png){width="141" height="68"}</span>   (<span class="arabic">32</span>)
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------

</div>

\

<div class="mathdisplay">

  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------
  <span class="MATH">![\$\\displaystyle \\mbox{\\boldmath {\$I\$}}\$](jmanual-img145.png){width="33" height="30"}![\$\\displaystyle \_{new} = \\sum\_{j=1}\^{K} \\left( \\mbox{\\boldmath {\$I\$}}\_j + m\_j \\mb... ...dmath {\$D\$}}(\\mbox{\\boldmath {\$c\$}}\_{j} - \\mbox{\\boldmath {\$c\$}}\_{new}) \\right)\$](jmanual-img154.png){width="11" height="30"}</span>   (<span class="arabic">33</span>)
  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------

</div>

\

ここで， <span class="MATH">**<span class="MATH">**![\$
D\$](jmanual-img155.png){width="239" height="68"}**</span>![\$
(\$](jmanual-img33.png){width="15" height="14"}<span class="MATH">**![\$
r\$](jmanual-img19.png){width="124" height="35"}**</span>![\$
)=\\hat{\\mbox{\\boldmath {\$r\$}}}\^T\\hat{\\mbox{\\boldmath
{\$r\$}}}\$](jmanual-img156.png){width="18"
height="14"}**</span>とする．

### [運動量・角運動量ヤコビアン]()

シリアルリンクマニピュレータを対象とし，
運動量・角運動量ヤコビアンを導出する．
運動量・原点まわりの角運動量を各関節変数で表現し，
その偏微分でヤコビアンの行を計算する．

第<span class="MATH">**![\$ j\$](jmanual-img61.png){width="125"
height="53"}**</span>関節の運動変数を<span class="MATH">**![\$
\\theta\_j\$](jmanual-img157.png){width="60"
height="38"}**</span>とする． まず，回転・並進の1自由度関節を考える．
![\\begin{numcases} {\\mbox{\\boldmath {\$P\$}}\_j =} \\begin{array}{cl}
\\mbox{\\boldmath ... ...\_j \\dot{\\theta}\_j \\tilde{m}\_j & \\text{if
linear joint}
\\end{array}\\end{numcases}](jmanual-img158.png){width="21" height="32"}
![\\begin{numcases} {\\mbox{\\boldmath {\$L\$}}\_j =} \\begin{array}{cl}
\\tilde{\\mbox{\\bo... ...nt}\\\\ \\mbox{\\boldmath {\$0\$}} & \\text{if
linear joint}
\\end{array}\\end{numcases}](jmanual-img159.png){width="450"
height="41"} ここで， <span class="MATH">**![\$ \[\\tilde{m}\_j,
\\tilde{\\mbox{\\boldmath {\$c\$}}}\_j, \\tilde{\\mbox{\\boldmath
{\$I\$}}}\_j\]\$](jmanual-img160.png){width="433"
height="40"}**</span>は AddMassProperty関数に第<span class="MATH">**![\$
j\$](jmanual-img61.png){width="125"
height="53"}**</span>関節の子リンクより
末端側のリンクのマスプロパティを与えたものであり，
実際には再帰計算により計算する[^<span
class="arabic">14</span>^](jmanual-footnode.html#foot1333)． これらを
<span class="MATH">**![\$
\\dot{\\theta}\_j\$](jmanual-img161.png){width="79"
height="39"}**</span>で割ることにより ヤコビアンの各列ベクトルを得る．
![\\begin{numcases} {\\mbox{\\boldmath {\$m\$}}\_j =} \\begin{array}{cl}
\\mbox{\\boldmath ... ...boldmath {\$a\$}}\_j \\tilde{m}\_j & \\text{if
linear joint}
\\end{array}\\end{numcases}](jmanual-img162.png){width="21" height="40"}
![\\begin{numcases} {\\mbox{\\boldmath {\$h\$}}\_j =} \\begin{array}{cl}
\\tilde{\\mbox{\\bo... ...nt}\\\\ \\mbox{\\boldmath {\$0\$}} & \\text{if
linear joint}
\\end{array}\\end{numcases}](jmanual-img163.png){width="445"
height="39"} これより慣性行列は次のように計算できる．

<div class="mathdisplay">

  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------
  <span class="MATH">![\$\\displaystyle \\mbox{\\boldmath {\$M\$}}\$](jmanual-img164.png){width="436" height="40"}![\$\\displaystyle \_{\\dot{\\mbox{\\boldmath {\$\\theta\$}}}} = \[\\mbox{\\boldmath {\$m\$}}\_1, \\hdots, \\mbox{\\boldmath {\$m\$}}\_{N}\]\$](jmanual-img165.png){width="25" height="30"}</span>   (<span class="arabic">34</span>)
  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------

</div>

\

<div class="mathdisplay">

  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------
  <span class="MATH">![\$\\displaystyle \\mbox{\\boldmath {\$H\$}}\$](jmanual-img166.png){width="132" height="32"}![\$\\displaystyle \_{\\dot{\\mbox{\\boldmath {\$\\theta\$}}}} = \[\\mbox{\\boldmath {\$h\$}}\_1,... ...boldmath {\$p\$}}}\_{G} \\mbox{\\boldmath {\$M\$}}\_{\\dot{\\mbox{\\boldmath {\$\\theta\$}}}}\$](jmanual-img167.png){width="21" height="30"}</span>   (<span class="arabic">35</span>)
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------

</div>

\

ここでは，全関節数を<span class="MATH">**![\$
N\$](jmanual-img111.png){width="15" height="14"}**</span>とした．
また，ベースリンクは 直動関節<span class="MATH">**![\$
x\$](jmanual-img130.png){width="46" height="30"}**</span>，<span
class="MATH">**![\$ y\$](jmanual-img131.png){width="14"
height="13"}**</span>，<span class="MATH">**![\$
z\$](jmanual-img132.png){width="13" height="30"}**</span>軸，
回転関節<span class="MATH">**![\$ x\$](jmanual-img130.png){width="46"
height="30"}**</span>，<span class="MATH">**![\$
y\$](jmanual-img131.png){width="14" height="13"}**</span>，<span
class="MATH">**![\$ z\$](jmanual-img132.png){width="13"
height="30"}**</span>軸を もつと考え整理し，次のようになる．

<div class="mathdisplay">

  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ --- --- ----------------------------------
  ![\\begin{displaymath}\\left\[ \\begin{array}{c} \\mbox{\\boldmath {\$M\$}}\_{B}\\\\ \\mbox{\\... ...math {\$0\$}} & \\tilde{\\mbox{\\boldmath {\$I\$}}} \\end{array}\\right\]\\end{displaymath}](jmanual-img168.png){width="184" height="32"}           (<span class="arabic">36</span>)
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ --- --- ----------------------------------

</div>

\
これを用いて重心まわりの角運動量・運動量は次のようになる．

<div class="mathdisplay">

  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------
  ![\\begin{displaymath}\\left\[ \\begin{array}{c} \\mbox{\\boldmath {\$P\$}}\\\\ \\mbox{\\bold... ...\$}}\_{B}\\\\ \\dot{\\mbox{\\boldmath {\$\\theta\$}}} \\end{array}\\right\]\\end{displaymath}](jmanual-img169.png){width="247" height="56"}           (<span class="arabic">37</span>)
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --- --- ----------------------------------

</div>

\
ここで ヒューマノイドの全質量<span class="MATH">**![\$
M\_{r}\$](jmanual-img170.png){width="393" height="64"}**</span>，
重心位置 <span class="MATH">**<span class="MATH">**![\$
p\$](jmanual-img64.png){width="334" height="113"}**</span>![\$
\_{G}\$](jmanual-img171.png){width="27" height="30"}**</span>，
慣性テンソル <span class="MATH">**![\$ \\tilde{\\mbox{\\boldmath
{\$I\$}}}\$](jmanual-img172.png){width="15"
height="30"}**</span>は次のように
全リンクのマスプロパティ演算より求める．

<div class="mathdisplay">

  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------
  <span class="MATH">![\$\\displaystyle \[M\_{r},\$](jmanual-img173.png){width="14" height="21"}   ![\$\\displaystyle \\mbox{\\boldmath {\$p\$}}\$](jmanual-img174.png){width="36" height="32"}![\$\\displaystyle \_{G}, \\tilde{\\mbox{\\boldmath {\$I\$}}}\] = AddMassProperty( \[m\_{1}, ... ...{1}\] ,\\hdots, \[m\_{N}, \\mbox{\\boldmath {\$c\$}}\_{N}, \\mbox{\\boldmath {\$I\$}}\_{N}\] )\$](jmanual-img175.png){width="14" height="30"}</span>   (<span class="arabic">38</span>)
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------

</div>

\

### [重心ヤコビアン]()

重心ヤコビアンは 重心速度と関節角速度の間のヤコビアンである．
本論文ではベースリンク仮想ジョイントを用いるため，
ベースリンクに6自由度関節がついたと考え
ベースリンク速度角速度・関節角速度の
重心速度に対するヤコビアンを重心ヤコビアンとして用いる． 具体的には，
ベースリンク成分 <span class="MATH">**<span class="MATH">**![\$
M\$](jmanual-img176.png){width="406" height="39"}**</span>![\$
\_{B}\$](jmanual-img177.png){width="22" height="14"}**</span>と
使用関節について抜き出した成分 <span class="MATH">**<span
class="MATH">**![\$ M\$](jmanual-img176.png){width="406"
height="39"}**</span>![\$ \_{\\dot{\\mbox{\\boldmath
{\$\\theta\$}}}}\^{\\prime}\$](jmanual-img178.png){width="15"
height="30"}**</span> による運動量ヤコビアンを
全質量で割ることで重心ヤコビアンを計算する．

<div class="mathdisplay">

  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------
  <span class="MATH">![\$\\displaystyle \\mbox{\\boldmath {\$J\$}}\$](jmanual-img30.png){width="60" height="52"}![\$\\displaystyle \_{G} = \\frac{1}{M\_{r}} \\left\[ \\begin{array}{cc} \\mbox{\\boldmath {... ...oldmath {\$M\$}}\_{\\dot{\\mbox{\\boldmath {\$\\theta\$}}}}\^{\\prime} \\end{array} \\right\]\$](jmanual-img179.png){width="15" height="32"}</span>   (<span class="arabic">39</span>)
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------

</div>

\

[ロボットの動作生成プログラミング]()
------------------------------------

### [三軸関節ロボットを使ったヤコビアン，逆運動学の例]()

3軸関節をもつロボットを定義し， 逆運動学やヤコビアンの計算例を紹介する．

ロボットの定義は以下の用になる．

    (defclass 3dof-robot
      :super cascaded-link
      :slots (end-coords l1 l2 l3 l4 j1 j2 j3))
    (defmethod 3dof-robot
      (:init ()
       (let (b)
         (send-super :init)

         (setq b (make-cube 10 10 20))
         (send b :locate #f(0 0 10))
         (send b :set-color :red)
         (setq l4 (instance bodyset-link :init (make-cascoords) :bodies (list b) :name 'l4))
         (setq end-coords (make-cascoords :pos #f(0 0 20)))
         (send l4 :assoc end-coords)
         (send l4 :locate #f(0 0 100))
         ;;
         (setq b (make-cube 10 10 100))
         (send b :locate #f(0 0 50))
         (send b :set-color :green)
         (setq l3 (instance bodyset-link :init (make-cascoords) :bodies (list b) :name 'l3))
         (send l3 :assoc l4)
         (send l3 :locate #f(0 0 100))
         ;;
         (setq b (make-cube 10 10 100))
         (send b :locate #f(0 0 50))
         (send b :set-color :blue)
         (setq l2 (instance bodyset-link :init (make-cascoords) :bodies (list b) :name 'l2))
         (send l2 :assoc l3)
         (send l2 :locate #f(0 0 20))
         ;;
         (setq b (body+ (make-cube 10 10 20 :pos #f(0 0 10)) (make-cube 300 300 2)))
         (send b :set-color :white)
         (setq l1 (instance bodyset-link :init (make-cascoords) :bodies (list b) :name 'l1))
         (send l1 :assoc l2)
         ;;
         (setq j1 (instance rotational-joint :init :name 'j1
                     :parent-link l1 :child-link l2 :axis :y :min -100 :max 100)
               j2 (instance rotational-joint :init  :name 'j2
                     :parent-link l2 :child-link l3 :axis :y :min -100 :max 100)
               j3 (instance rotational-joint :init  :name 'j3
                     :parent-link l3 :child-link l4 :axis :y :min -100 :max 100))
         ;;
         (setq links (list l1 l2 l3 l4))
         (setq joint-list (list j1 j2 j3))
         ;;
         (send self :init-ending)
         self))
      (:end-coords (&rest args) (forward-message-to end-coords args))
      )

ここではロボットの手先の座標を`end-coords`というスロット変数に格
納し，さらにこれにアクセスするためのメソッドを用意してある．

これまでと同様，

    (setq r (instance 3dof-robot :init))
    (objects (list r))
    (send r :angle-vector #f(30 30 30))

としてロボットモデルの生成，表示，関節角度の指定が可能である． さらに，

    (send (send r :end-coords) :draw-on :flush t)

とすると，ロボットの`end-coords`(端点座標系）の表示が可能であるが，
マウスイベントが発生すると消えてしまう．恒久的に表示するためには

    (objects (list r (send r :end-coords)))

とするとよい．

次に，ヤコビアン，逆運動学の例を示す．まず基本になるのが，

    (send r :link-list (send r :end-coords :parent))

として得られるリンクのリストである．これはロボットのルート（胴体）から
引数となるリンクまでのたどれるリンクを返す．

`:calc-jacobian-from-link-list`メソッドはリンクのリストを引数にと
り，この各リンクに存在するジョイント（関節）に
対応するヤコビアンを計算することができる．
また，`:move-target`キーワード引数でエンドエフェクタの座標系を
指定してる．その他のキーワード引数については後述する．

    (dotimes (i 100)
      (setq j (send r :calc-jacobian-from-link-list
                    (send r :link-list (send r :end-coords :parent))
                    :move-target (send r :end-coords)
                    :rotation-axis t
                    :translation-axis t))
      (setq j# (sr-inverse j))
      (setq da (transform j# #f(1 0 0 0 0 0)))
      ;;(setq da (transform j# #f(0 0 0 0 -1 0)))
      (send r :angle-vector (v+ (send r :angle-vector) da))
      (send *irtviewer* :draw-objects)
      )

ここではリンクの長さ（ジョイントの数）は3個なので6行3列のヤコビアン(`j`)が
計算される．これの逆行列(`j#`)を作り，位置姿勢の6自由度の目標速度・角速度
(`#f(1 0 0 0 0 0)`)を与えると，それに対応する関節速度(`da`)
が計算でき，これを現在の関節角度に足している
(`(v+ (send r :angle-vector) da)`)．

次に，ロボットの端点作業の位置は合わせるが姿勢は拘束せず任意のままでよい，とい
う場合の例を示す．ここでは，`:calc-jacobian-from-link-list`のオプ
ショナル引数として`:rotation-axis`, `:translation-axis`
があり，それぞれ位置，姿勢での拘束条件を示す．
`t`は三軸拘束，`nil`は拘束なし，その他に`:x`, `:y`,
`:z`を指定することができる．

    (setq translation-axis t)
    (setq rotation-axis nil)
    (dotimes (i 2000)
      (setq j (send r :calc-jacobian-from-link-list
                    (send r :link-list (send r :end-coords :parent))
                    :move-target (send r :end-coords)
                    :rotation-axis rotation-axis
                    :translation-axis translation-axis))
      (setq j# (sr-inverse j))
      (setq c (make-cascoords :pos (float-vector (* 100 (sin (/ i 500.0))) 0 200)))
      (setq dif-pos (send (send r :end-coords) :difference-position c))
      (setq da (transform j# dif-pos))
      (send r :angle-vector (v+ (send r :angle-vector) da))
      (send *irtviewer* :draw-objects :flush nil)
      (send c :draw-on :flush t)
      )

ここでは位置の三軸のみを拘束した3行3列のヤコビアンを計算し，これの
逆行列からロボットの関節に速度を与えている．さらに，ここでは

      (send *irtviewer* :draw-objects :flush nil)

として`*irtviewer*`に画面を描画しているが，実際に
ディスプレイに表示するフラッシュ処理は行わず，その次の行の

      (send c :draw-on :flush t)

で目標座標は表示し，かつフラッシュ処理を行っている．

上記の計算をまとめた逆運動学メソッドが`:inverse-kinematics`である．
第一引数に目標座標系を指定し，ヤコビアン計算のときと同様にキーワード
引数で `:move-target`, `:translation-axis`, `:rotation-axis`
を指定する． また，`:debug-view`キーワード引数に`t`を与えると計算中の様
子をテキスト並びに視覚的に提示してくれる．

    (setq c (make-cascoords :pos #f(100 0 0) :rpy (float-vector 0 pi 0)))
    (send r :inverse-kinematics  c
          :link-list (send r :link-list (send r :end-coords :parent))
          :move-target (send r :end-coords)
          :translation-axis t
          :rotation-axis t
          :debug-view t)

逆運運動学が失敗する場合のサンプルとして以下のプログラムを見てみよう．

    (dotimes (i 400)
      (setq c (make-cascoords
                 :pos (float-vector (+ 100 (* 80 (sin (/ i 100.0)))) 0 0)
                 :rpy (float-vector 0 pi 0)))
      (send r :inverse-kinematics  c
            :link-list (send r :link-list (send r :end-coords :parent))
            :move-target (send r :end-coords) :translation-axis t  :rotation-axis t)
      (x::window-main-one)
      (send *irtviewer* :draw-objects :flush nil)
      (send c :draw-on :flush t)
      )

このプログラムを実行すると以下のようなエラーが出てくる．

    ;; inverse-kinematics failed.
    ;; dif-pos : #f(11.7826 0.0 0.008449)/(11.7826/1)
    ;; dif-rot : #f(0.0 2.686130e-05 0.0)/(2.686130e-05/0.017453)
    ;;  coords : #<coordinates #X4bcccb0  0.0 0.0 0.0 / 0.0 0.0 0.0>
    ;;  angles : (14.9993 150 15.0006)
    ;;    args : ((#<cascaded-coords #X4b668a0  39.982 0.0 0.0 / 3.142 1.225e-16 3.14
    2>) :link-list (#<bodyset-link #X4cf8e60 l2  0.0 0.0 20.0 / 0.0 0.262 0.0> #<body
    set-link #X4cc8008 l3  25.866 0.0 116.597 / 3.142 0.262 3.142> #<bodyset-link #X4
    c7a0d0 l4  51.764 0.0 20.009 / 3.142 2.686e-05 3.142>) :move-target;; #<cascaded-
    coords #X4c93640  51.764 0.0 0.009 / 3.142 2.686e-05 3.142> :translation-axis t :
    rotation-axis t)

これは，関節の駆動範囲の制限から目標位置に手先が届かない状況である．
このような場面では，例えば，手先の位置さえ目標位置に届けばよく姿勢を
無視してよい場合`:rotation-axis nil`と指定することができる．

また，`:thre`や`:rthre`を使うことで逆運動学計算の終了条件で
ある位置姿勢の誤差を指定することができる．正確な計算が求められていない
状況ではこの値をデフォルトの`1`, `(deg2rad 1)`より大きい値を
利用するのもよい．

また，逆運動学の計算に失敗した場合，デフォルトでは逆運動学計算を始める
前の姿勢まで戻るが，`:revert-if-fail`というキーワード引数をnilと指定
すると，指定されたの回数の計算を繰り替えしたあと，その姿勢のまま関数か
ら抜けてくる．指定の回数もまた，`:stop`というキーワード引数で指
定することができる．

    (setq c (make-cascoords :pos #f(300 0 0) :rpy (float-vector 0 pi 0)))
    (send r :inverse-kinematics  c
          :link-list (send r :link-list (send r :end-coords :parent))
          :move-target (send r :end-coords)
          :translation-axis t
          :rotation-axis nil
          :revert-if-fail nil)

### [irteusのサンプルプログラムにおける例]()

cascaded-coordsクラスでは

-   (:link-list (to &optional form))
-   (:calc-jacobian-from-link-list (link-list &key move-target
    (rotation-axis nil)))

というメソッドが用意されている．

前者はリンクを引数として，ルートリンクからこのリンクまでの経路を計算し，
リンクのリストとして返す．後者はこのリンクのリストを引数とし，
move-target座標系をに対するヤコビアンを計算する．

concatenate result-type a bは a bを
連結しresult-type型に変換し返し，scale a b はベクトルbの全ての要素をス
カラーa倍し，matrix-logは行列対数関数を計算する．

    (if (not (boundp '*irtviewer*)) (make-irtviewer))

    (load "irteus/demo/sample-arm-model.l")
    (setq *sarm* (instance sarmclass :init))
    (send *sarm* :reset-pose)
    (setq *target* (make-coords :pos #f(350 200 400)))
    (objects (list *sarm* *target*))

    (do-until-key
      ;; step 3
      (setq c (send *sarm* :end-coords))
      (send c :draw-on :flush t)
      ;; step 4
      ;; step 4
      (setq dp (scale 0.001 (v- (send *target* :worldpos) (send c :worldpos))) ;; mm->m
            dw (matrix-log (m* (transpose (send c :worldrot)) (send *target* :worldrot))))
      (format t "dp = ~7,3f ~7,3f ~7,3f, dw = ~7,3f ~7,3f ~7,3f~%"
              (elt dp 0) (elt dp 1) (elt dp 2)
              (elt dw 0) (elt dw 1) (elt dw 2))
      ;; step 5
      (when (< (+ (norm dp) (norm dw)) 0.01) (return))
      ;; step 6
      (setq ll (send *sarm* :link-list (send *sarm* :end-coords :parent)))
      (setq j (send *sarm* :calc-jacobian-from-link-list
                    ll :move-target (send *sarm* :end-coords)
                    :trnaslation-axis t :rotation-axis t))
      (setq q (scale 1.0 (transform (pseudo-inverse j) (concatenate float-vector dp dw))))
      ;; step 7
      (dotimes (i (length ll))
        (send (send (elt ll i) :joint) :joint-angle (elt q i) :relative t))
      ;; draw
      (send *irtviewer* :draw-objects)
      (x::window-main-one))

実際のプログラミングでは:inverse-kinematicsというメソッドが用意されて
おり，ここでは特異点や関節リミットの回避，あるいは自己衝突回避等の機能
が追加されている．

### [実際のロボットモデル]()

実際のロボットや環境を利用した実践的なサンプルプログラムを見てみよう．

まず，最初はロボットや環境のモデルファイルを読み込む．これらのファイル
は\$EUSDIR/modelsに，これらのファイルをロードしインスタンスを生成す
るプログラムは以下のように書くことができる．`(room73b2)`や
`(h7)`はこれらのファイル内で定義されている関数である．
ロボットのモデル(`robot-model`)は`irtrobot.l`ファイルで定義
されており，`cascaded-link`クラスの子クラスになっている．
ロボットとは`larm,rarm,lleg,rleg,head`のリンクのツリーからなる
ものとして定義されており，
`(send *robot* :larm)`や`(send *robot* :head)`として
ロボットのリム(limb)にアクセスでき，右手の逆運動学，左手の逆運動学等と
いう利用方法が可能になっている．

    (load "models/room73b2-scene.l")
    (load "models/h7-robot.l")
    (setq *room* (room73b2))
    (setq *robot* (h7))
    (objects (list *robot* *room*))

ロボットには`:reset-pose`というメソッドがありこれで初期姿勢をとる
ことができる．

    (send *robot* :reset-pose)

次に，ロボットを部屋の中で移動させたい．部屋内の代表的な座標は
`(send *room* :spots)`で取得できる．この中から目的の座標を得る
場合はその座標の名前を引数として`:spot`メソッドを呼び出す．
ちなみに，このメソッドの定義は`prog/jskeus/irteus/irtscene.l` にあり

    (defmethod scene-model
      (:spots
       (&optional name)
       (append
        (mapcan
         #'(lambda(x)(if (derivedp x scene-model) (send x :spots name) nil))
         objs)
        (mapcan #'(lambda (o)
            (if (and (eq (class o) cascaded-coords)
                 (or (null name) (string= name (send o :name))))
                (list o)))
            objs)))
      (:spot
       (name)
       (let ((r (send self :spots name)))
         (case (length r)
           (0 (warning-message 1 "could not found spot(~A)" name) nil)
           (1 (car r))
           (t (warning-message 1 "found multiple spot ~A for given name(~A)" r name) (car r)))))
      )

となっている．

ロボットもまた`coordinates`クラスの子クラスなので`:move-to`
メソッドを利用できる．また，このロボットの原点は腰にあるので足が地面に
つくように`:locate`メソッドを使って移動する．

    (send *robot* :move-to (send *room* :spot "cook-spot") :world)
    (send *robot* :locate #f(0 0 550))

現状では`*irtviewer*`の画面上でロボットが小さくなっているので，
以下のメソッド利用し，ロボットが画面いっぱいになるように調整する．

    (send *irtviewer* :look-all
          (geo::make-bounding-box
           (flatten (send-all (send *robot* :bodies) :vertices))))

次に環境中の物体を選択する．ここでは`:object`メソッドを利用する．
これは，`:spots, :spot`と同様の振る舞いをするため，
どのような物体があるかは，`(send-all (send *room* :objects) :name)`
として知ることができる． `room73b2-kettle`の他に
`room73b2-mug-cup`や`room73b2-knife`等を利用するとよい．

    (setq *kettle* (send *room* :object "room73b2-kettle"))

環境モデルの初期化直後は物体は部屋にassocされているため，以下の用に
親子関係を解消しておく．こうしないと物体を把持するなどの場合に問題が生
じる．

    (if (send *kettle* :parent) (send (send *kettle* :parent) :dissoc *kettle*))

ロボットの視線を対象物に向けるためのメソッドとして以下のようなものがあ
る．

    (send *robot* :head :look-at (send *kettle* :worldpos))

対象物体には，その物体を把持するための利用したらよい座標系が
`:handle`メソッドとして記述されている場合がある．このメソッドは
リストを返すため以下の様に`(car (send *kettle* :handle))`として
その座標系を知ることができる．この座標がどこにあるか確認するためには
`(send (car (send *kettle* :handle)) :draw-on :flush t)`とすると よい．

したがってこの物体手を伸ばすためには

    (send *robot* :larm :inverse-kinematics
          (car (send *kettle* :handle))
          :link-list (send *robot* :link-list (send *robot* :larm :end-coords :parent))
          :move-target (send *robot* :larm :end-coords)
          :rotation-axis :z
          :debug-view t)

となる．

ここで，ロボットの手先と対象物体の座標系を連結し，

    (send *robot* :larm :end-coords :assoc *kettle*)

以下の様にして世界座標系で100\[mm\]持ち上げることができる．

    (send *robot* :larm :move-end-pos #f(0 0 100) :world
            :debug-view t :look-at-target t)

`:look-at-target`は移動中に首の向きを常に対象を見つづけるようにす
るという指令である．

[ロボットモデル]()
------------------

ロボットの身体はリンクとジョイントから構成されるが、それぞれ
`bodyset-link`と`joint`クラスを利用しモデル絵を作成する。ロ
ボットの身体はこれらの要素を含んだ`cascaded-link`という，連結リン
クとしてモデルを生成する．

実際には`joint`は抽象クラスであり `rotational-joint`,`linear-joint`,
`wheel-joint`,`omniwheel-joint`,
`sphere-joint`を選択肢、また四肢を持つロボットの場合は `cascaded-link`
ではなく`robot-model`クラスを利用する。

\
[]() **joint** \[クラス\]

      :super   propertied-object 
      :slots         parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&key \\= (name (intern
(format nil joint\~A (sy... ...:joint-min-max-target mm-target)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img180.png){width="175" height="52"}

 
:   abstract class of joint, users need to use rotational-joint,
    linear-joint, sphere-joint, 6dof-joint, wheel-joint or
    omniwheel-joint. use :parent-link/:child-link for specifying links
    that this joint connect to and :min/:min for range of joint angle
    in degree.

[]() **:min-angle** *&optional v* \[メソッド\]

 
:   If v is set, it updates min-angle of this instance. :min-angle
    returns minimal angle of this joint in degree.

[]() **:max-angle** *&optional v* \[メソッド\]

 
:   If v is set, it updates max-angle of this instance. :max-angle
    returns maximum angle of this joint in degree.

[]() **:parent-link** *&rest args* \[メソッド\]

 
:   Returns parent link of this joint. if any arguments is set, it is
    passed to the parent-link.

[]() **:child-link** *&rest args* \[メソッド\]

 
:   Returns child link of this joint. if any arguments is set, it is
    passed to the child-link.

[]() **:joint-dof** \[メソッド\]

 
:   Returns Degree of Freedom of this joint.

[]() **:speed-to-angle** *&rest args* \[メソッド\]

 
:   Returns values in deg/mm unit of input value in SI(rad/m) unit.

[]() **:angle-to-speed** *&rest args* \[メソッド\]

 
:   Returns values in SI(rad/m) unit of input value in deg/mm unit.

[]() **:joint-velocity** *&optional jv* \[メソッド\]

 
:   If jv is set, it updates joint-velocity of this instance.
    :joint-velocity returns velocity of this joint in SI(m/s, rad/s)
    unit.

[]() **:joint-acceleration** *&optional ja* \[メソッド\]

 
:   If ja is set, it updates joint-acceleration of this instance.
    :joint-acceleration returns acceleration of this joint in SI(m/<span
    class="MATH">**![\$ s\^2\$](jmanual-img181.png){width="646"
    height="187"}**</span>, rad/<span class="MATH">**![\$
    s\^2\$](jmanual-img181.png){width="646"
    height="187"}**</span>) unit.

[]() **:joint-torque** *&optional jt* \[メソッド\]

 
:   If jt is set, it updates joint-torque of this instance.
    :joint-torque returns torque of this joint in SI(N, Nm) unit.

[]() **:max-joint-velocity** *&optional mjv* \[メソッド\]

 
:   If mjv is set, it updates min-joint-velocity of this instance.
    :min-joint-velocity returns velocity of this joint in SI(m/s, rad/s)
    unit.

[]() **:max-joint-torque** *&optional mjt* \[メソッド\]

 
:   If mjt is set, it updates min-joint-torque of this instance.
    :min-joint-torque returns velocity of this joint in SI(N, Nm) unit.

[]() **:calc-dav-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **:calc-jacobian** *&rest args* \[メソッド\]

 

:   

[]() **:joint-min-max-table** *&optional mm-table* \[メソッド\]

 

:   

[]() **:joint-min-max-target** *&optional mm-target* \[メソッド\]

 

:   

[]() **:joint-min-max-table-angle-interpolate** *target-angle
min-or-max* \[メソッド\]

 

:   

[]() **:joint-min-max-table-min-angle** *&optional (target-angle (send
joint-min-max-target :joint-angle))* \[メソッド\]

 

:   

[]() **:joint-min-max-table-max-angle** *&optional (target-angle (send
joint-min-max-target :joint-angle))* \[メソッド\]

 

:   

[]() **:max-joint-torque** *&optional mjt* \[メソッド\]

 
:   If mjt is set, it updates min-joint-torque of this instance.
    :min-joint-torque returns velocity of this joint in SI(N, Nm) unit.

[]() **:max-joint-velocity** *&optional mjv* \[メソッド\]

 
:   If mjv is set, it updates min-joint-velocity of this instance.
    :min-joint-velocity returns velocity of this joint in SI(m/s, rad/s)
    unit.

[]() **:joint-torque** *&optional jt* \[メソッド\]

 
:   If jt is set, it updates joint-torque of this instance.
    :joint-torque returns torque of this joint in SI(N, Nm) unit.

[]() **:joint-acceleration** *&optional ja* \[メソッド\]

 
:   If ja is set, it updates joint-acceleration of this instance.
    :joint-acceleration returns acceleration of this joint in SI(m/<span
    class="MATH">**![\$ s\^2\$](jmanual-img181.png){width="646"
    height="187"}**</span>, rad/<span class="MATH">**![\$
    s\^2\$](jmanual-img181.png){width="646"
    height="187"}**</span>) unit.

[]() **:joint-velocity** *&optional jv* \[メソッド\]

 
:   If jv is set, it updates joint-velocity of this instance.
    :joint-velocity returns velocity of this joint in SI(m/s, rad/s)
    unit.

[]() **:angle-to-speed** *&rest args* \[メソッド\]

 
:   Returns values in SI(rad/m) unit of input value in deg/mm unit.

[]() **:speed-to-angle** *&rest args* \[メソッド\]

 
:   Returns values in deg/mm unit of input value in SI(rad/m) unit.

[]() **:joint-dof** \[メソッド\]

 
:   Returns Degree of Freedom of this joint.

[]() **:child-link** *&rest args* \[メソッド\]

 
:   Returns child link of this joint. if any arguments is set, it is
    passed to the child-link.

[]() **:parent-link** *&rest args* \[メソッド\]

 
:   Returns parent link of this joint. if any arguments is set, it is
    passed to the parent-link.

[]() **:max-angle** *&optional v* \[メソッド\]

 
:   If v is set, it updates max-angle of this instance. :max-angle
    returns maximum angle of this joint in degree.

[]() **:min-angle** *&optional v* \[メソッド\]

 
:   If v is set, it updates min-angle of this instance. :min-angle
    returns minimal angle of this joint in degree.

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&key \\= (name (intern
(format nil joint\~A (sy... ...:joint-min-max-target mm-target)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img180.png){width="175" height="52"}

 
:   abstract class of joint, users need to use rotational-joint,
    linear-joint, sphere-joint, 6dof-joint, wheel-joint or
    omniwheel-joint. use :parent-link/:child-link for specifying links
    that this joint connect to and :min/:min for range of joint angle
    in degree.

[]() **:joint-min-max-table-max-angle** *&optional (target-angle (send
joint-min-max-target :joint-angle))* \[メソッド\]

 

:   

[]() **:joint-min-max-table-min-angle** *&optional (target-angle (send
joint-min-max-target :joint-angle))* \[メソッド\]

 

:   

[]() **:joint-min-max-table-angle-interpolate** *target-angle
min-or-max* \[メソッド\]

 

:   

[]() **:joint-min-max-target** *&optional mm-target* \[メソッド\]

 

:   

[]() **:joint-min-max-table** *&optional mm-table* \[メソッド\]

 

:   

[]() **:calc-jacobian** *&rest args* \[メソッド\]

 

:   

[]() **:calc-dav-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **rotational-joint** \[クラス\]

      :super   joint 
      :slots         axis 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\=
((:axis ax) :z) \\\\lq \[metho... ... \\&gt; ((:max-joint-torque mjt)
100) \\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img182.png){width="21" height="20"}

 
:   create instance of rotational-joint. :axis is either (:x, :y, :z)
    or vector. :min-angle and :max-angle takes in radius, but velocity
    and torque are given in SI units.

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   Return joint-angle if v is not set, if v is given, set joint angle.
    v is rotational value in degree.

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of rotational joint, 1.

[]() **:speed-to-angle** *v* \[メソッド\]

 
:   Returns degree of given input in radian

[]() **:angle-to-speed** *v* \[メソッド\]

 
:   Returns radian of given input in degree

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **:calc-jacobian** *&rest args* \[メソッド\]

 

:   

[]() **:angle-to-speed** *v* \[メソッド\]

 
:   Returns radian of given input in degree

[]() **:speed-to-angle** *v* \[メソッド\]

 
:   Returns degree of given input in radian

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of rotational joint, 1.

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   Return joint-angle if v is not set, if v is given, set joint angle.
    v is rotational value in degree.

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\=
((:axis ax) :z) \\\\lq \[metho... ... \\&gt; ((:max-joint-torque mjt)
100) \\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img182.png){width="21" height="20"}

 
:   create instance of rotational-joint. :axis is either (:x, :y, :z)
    or vector. :min-angle and :max-angle takes in radius, but velocity
    and torque are given in SI units.

[]() **:calc-jacobian** *&rest args* \[メソッド\]

 

:   

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **linear-joint** \[クラス\]

      :super   joint 
      :slots         axis 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\=
((:axis ax) :z) \\\\lq \[metho... ... \\&gt; ((:max-joint-torque mjt)
100) \\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img184.png){width="552" height="34"}

 
:   Create instance of linear-joint. :axis is either (:x, :y, :z)
    or vector. :min-angle and :max-angle takes in \[mm\], but velocity
    and torque are given in SI units.

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   return joint-angle if v is not set, if v is given, set joint angle.
    v is linear value in \[mm\].

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of linear joint, 1.

[]() **:speed-to-angle** *v* \[メソッド\]

 
:   Returns \[mm\] of given input in \[m\]

[]() **:angle-to-speed** *v* \[メソッド\]

 
:   Returns \[m\] of given input in \[mm\]

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **:calc-jacobian** *&rest args* \[メソッド\]

 

:   

[]() **:angle-to-speed** *v* \[メソッド\]

 
:   Returns \[m\] of given input in \[mm\]

[]() **:speed-to-angle** *v* \[メソッド\]

 
:   Returns \[mm\] of given input in \[m\]

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of linear joint, 1.

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   return joint-angle if v is not set, if v is given, set joint angle.
    v is linear value in \[mm\].

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\=
((:axis ax) :z) \\\\lq \[metho... ... \\&gt; ((:max-joint-torque mjt)
100) \\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img184.png){width="552" height="34"}

 
:   Create instance of linear-joint. :axis is either (:x, :y, :z)
    or vector. :min-angle and :max-angle takes in \[mm\], but velocity
    and torque are given in SI units.

[]() **:calc-jacobian** *&rest args* \[メソッド\]

 

:   

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **wheel-joint** \[クラス\]

      :super   joint 
      :slots         axis 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= (min
(float-vector \\texta... ...rque mjt) (float-vector 100 100)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img185.png){width="552" height="72"}

 
:   Create instance of wheel-joint.

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   return joint-angle if v is not set, if v is given, set joint angle.
    v is joint-angle vector, which is (float-vector
    translation-x\[mm\] rotation-z\[deg\])

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of linear joint, 2.

[]() **:speed-to-angle** *dv* \[メソッド\]

 
:   Returns \[mm/deg\] of given input in SI unit \[m/rad\]

[]() **:angle-to-speed** *dv* \[メソッド\]

 
:   Returns SI unit \[m/rad\] of given input in \[mm/deg\]

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **:calc-jacobian** *fik row column joint paxis child-link
world-default-coords child-reverse move-target transform-coords
rotation-axis translation-axis tmp-v0 tmp-v1 tmp-v2 tmp-v3 tmp-v3a
tmp-v3b tmp-m33* \[メソッド\]

 

:   

[]() **:angle-to-speed** *dv* \[メソッド\]

 
:   Returns SI unit \[m/rad\] of given input in \[mm/deg\]

[]() **:speed-to-angle** *dv* \[メソッド\]

 
:   Returns \[mm/deg\] of given input in SI unit \[m/rad\]

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of linear joint, 2.

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   return joint-angle if v is not set, if v is given, set joint angle.
    v is joint-angle vector, which is (float-vector
    translation-x\[mm\] rotation-z\[deg\])

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= (min
(float-vector \\texta... ...rque mjt) (float-vector 100 100)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img185.png){width="552" height="72"}

 
:   Create instance of wheel-joint.

[]() **:calc-jacobian** *fik row column joint paxis child-link
world-default-coords child-reverse move-target transform-coords
rotation-axis translation-axis tmp-v0 tmp-v1 tmp-v2 tmp-v3 tmp-v3a
tmp-v3b tmp-m33* \[メソッド\]

 

:   

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **omniwheel-joint** \[クラス\]

      :super   joint 
      :slots         axis 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= (min
(float-vector \\texta... ... mjt) (float-vector 100 100 100)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img186.png){width="595" height="91"}

 
:   create instance of omniwheel-joint.

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   return joint-angle if v is not set, if v is given, set joint angle.
    v is joint-angle vector, which is (float-vector translation-x\[mm\]
    translation-y\[mm\] rotation-z\[deg\])

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of linear joint, 3.

[]() **:speed-to-angle** *dv* \[メソッド\]

 
:   Returns \[mm/deg\] of given input in SI unit \[m/rad\]

[]() **:angle-to-speed** *dv* \[メソッド\]

 
:   Returns SI unit \[m/rad\] of given input in \[mm/deg\]

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **:calc-jacobian** *fik row column joint paxis child-link
world-default-coords child-reverse move-target transform-coords
rotation-axis translation-axis tmp-v0 tmp-v1 tmp-v2 tmp-v3 tmp-v3a
tmp-v3b tmp-m33* \[メソッド\]

 

:   

[]() **:angle-to-speed** *dv* \[メソッド\]

 
:   Returns SI unit \[m/rad\] of given input in \[mm/deg\]

[]() **:speed-to-angle** *dv* \[メソッド\]

 
:   Returns \[mm/deg\] of given input in SI unit \[m/rad\]

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of linear joint, 3.

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   return joint-angle if v is not set, if v is given, set joint angle.
    v is joint-angle vector, which is (float-vector translation-x\[mm\]
    translation-y\[mm\] rotation-z\[deg\])

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= (min
(float-vector \\texta... ... mjt) (float-vector 100 100 100)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img186.png){width="595" height="91"}

 
:   create instance of omniwheel-joint.

[]() **:calc-jacobian** *fik row column joint paxis child-link
world-default-coords child-reverse move-target transform-coords
rotation-axis translation-axis tmp-v0 tmp-v1 tmp-v2 tmp-v3 tmp-v3a
tmp-v3b tmp-m33* \[メソッド\]

 

:   

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **sphere-joint** \[クラス\]

      :super   joint 
      :slots         axis 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= (min
(float-vector \\texta... ... mjt) (float-vector 100 100 100)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img186.png){width="595" height="91"}

 
:   Create instance of sphere-joint. min/max are defiend as a region of
    angular velocity in degree.

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   return joint-angle if v is not set, if v is given, set joint angle.
    v is joint-angle vector \[deg\] by axis-angle representation, i.e
    (scale rotation-angle-from-default-coords\[deg\] axis-unit-vector)

[]() **:joint-angle-rpy** *&optional v &key relative* \[メソッド\]

 
:   Return joint-angle if v is not set, if v is given, set joint-angle
    vector by RPY representation, i.e. (float-vector yaw\[deg\]
    roll\[deg\] pitch\[deg\])

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of linear joint, 3.

[]() **:speed-to-angle** *dv* \[メソッド\]

 
:   Returns degree of given input in radian

[]() **:angle-to-speed** *dv* \[メソッド\]

 
:   Returns radian of given input in degree

[]() ![\\begin{emtabbing} {\\bf :joint-euler-angle} \\it\\&key \\=
(axis-order '(:z :y :x)) \\\\lq \[method\]\\\\ \\&gt; ((:child-rot m)
(send child-link :rot)) \\rm
\\end{emtabbing}](jmanual-img187.png){width="692" height="91"}

 
:   Return joint-angle if v is not set, if v is given, set joint-angle
    vector by euler representation.

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **:calc-jacobian** *fik row column joint paxis child-link
world-default-coords child-reverse move-target transform-coords
rotation-axis translation-axis tmp-v0 tmp-v1 tmp-v2 tmp-v3 tmp-v3a
tmp-v3b tmp-m33* \[メソッド\]

 

:   

[]() ![\\begin{emtabbing} {\\bf :joint-euler-angle} \\it\\&key \\=
(axis-order '(:z :y :x)) \\\\lq \[method\]\\\\ \\&gt; ((:child-rot m)
(send child-link :rot)) \\rm
\\end{emtabbing}](jmanual-img187.png){width="692" height="91"}

 
:   Return joint-angle if v is not set, if v is given, set joint-angle
    vector by euler representation.

[]() **:angle-to-speed** *dv* \[メソッド\]

 
:   Returns radian of given input in degree

[]() **:speed-to-angle** *dv* \[メソッド\]

 
:   Returns degree of given input in radian

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of linear joint, 3.

[]() **:joint-angle-rpy** *&optional v &key relative* \[メソッド\]

 
:   Return joint-angle if v is not set, if v is given, set joint-angle
    vector by RPY representation, i.e. (float-vector yaw\[deg\]
    roll\[deg\] pitch\[deg\])

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   return joint-angle if v is not set, if v is given, set joint angle.
    v is joint-angle vector \[deg\] by axis-angle representation, i.e
    (scale rotation-angle-from-default-coords\[deg\] axis-unit-vector)

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= (min
(float-vector \\texta... ... mjt) (float-vector 100 100 100)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img186.png){width="595" height="91"}

 
:   Create instance of sphere-joint. min/max are defiend as a region of
    angular velocity in degree.

[]() **:calc-jacobian** *fik row column joint paxis child-link
world-default-coords child-reverse move-target transform-coords
rotation-axis translation-axis tmp-v0 tmp-v1 tmp-v2 tmp-v3 tmp-v3a
tmp-v3b tmp-m33* \[メソッド\]

 

:   

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **6dof-joint** \[クラス\]

      :super   joint 
      :slots         axis 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= (min
(float-vector \\texta... ...100 100)) \\\\ \\&gt; (absolute-p nil) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img188.png){width="552" height="35"}

 
:   Create instance of 6dof-joint.

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   Return joint-angle if v is not set, if v is given, set joint angle
    vector, which is 6D vector of 3D translation\[mm\] and 3D
    rotation\[deg\], i.e. (find-if \#'(lambda (x) (eq (send (car x)
    :name) 'sphere-joint)) (documentation :joint-angle))

[]() **:joint-angle-rpy** *&optional v &key relative* \[メソッド\]

 
:   Return joint-angle if v is not set, if v is given, set joint angle.
    v is joint-angle vector, which is 6D vector of 3D translation\[mm\]
    and 3D rotation\[deg\], for rotation, please see (find-if
    \#'(lambda (x) (eq (send (car x) :name) 'sphere-joint))
    (documentation :joint-angle-rpy))

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of linear joint, 6.

[]() **:speed-to-angle** *dv* \[メソッド\]

 
:   Returns \[mm/deg\] of given input in SI unit \[m/rad\]

[]() **:angle-to-speed** *dv* \[メソッド\]

 
:   Returns SI unit \[m/rad\] of given input in \[mm/deg\]

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **:calc-jacobian** *fik row column joint paxis child-link
world-default-coords child-reverse move-target transform-coords
rotation-axis translation-axis tmp-v0 tmp-v1 tmp-v2 tmp-v3 tmp-v3a
tmp-v3b tmp-m33* \[メソッド\]

 

:   

[]() **:angle-to-speed** *dv* \[メソッド\]

 
:   Returns SI unit \[m/rad\] of given input in \[mm/deg\]

[]() **:speed-to-angle** *dv* \[メソッド\]

 
:   Returns \[mm/deg\] of given input in SI unit \[m/rad\]

[]() **:joint-dof** \[メソッド\]

 
:   Returns DOF of linear joint, 6.

[]() **:joint-angle-rpy** *&optional v &key relative* \[メソッド\]

 
:   Return joint-angle if v is not set, if v is given, set joint angle.
    v is joint-angle vector, which is 6D vector of 3D translation\[mm\]
    and 3D rotation\[deg\], for rotation, please see (find-if
    \#'(lambda (x) (eq (send (car x) :name) 'sphere-joint))
    (documentation :joint-angle-rpy))

[]() ![\\begin{emtabbing} {\\bf :joint-angle} \\it\\&optional v \\&key
\\= relative \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img183.png){width="552" height="72"}

 
:   Return joint-angle if v is not set, if v is given, set joint angle
    vector, which is 6D vector of 3D translation\[mm\] and 3D
    rotation\[deg\], i.e. (find-if \#'(lambda (x) (eq (send (car x)
    :name) 'sphere-joint)) (documentation :joint-angle))

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= (min
(float-vector \\texta... ...100 100)) \\\\ \\&gt; (absolute-p nil) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img188.png){width="552" height="35"}

 
:   Create instance of 6dof-joint.

[]() **:calc-jacobian** *fik row column joint paxis child-link
world-default-coords child-reverse move-target transform-coords
rotation-axis translation-axis tmp-v0 tmp-v1 tmp-v2 tmp-v3 tmp-v3a
tmp-v3b tmp-m33* \[メソッド\]

 

:   

[]() **:calc-angle-speed-gain** *dav i periodic-time* \[メソッド\]

 

:   

[]() **bodyset-link** \[クラス\]

      :super   bodyset 
      :slots         joint parent-link child-links analysis-level default-coords weight acentroid inertia-tensor angular-velocity angular-acceleration spacial-velocity spacial-acceleration momentum-velocity angular-momentum-velocity momentum angular-momentum force moment ext-force ext-moment 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it coords \\&rest args \\&key
\\= ((:analysis-level... ...nertia-tensor i) (unit-matrix 3)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img189.png){width="908" height="110"}

 
:   Create instance of bodyset-link.

[]() **:worldcoords** *&optional (level analysis-level)* \[メソッド\]

 
:   Returns a coordinates object which represents this coord in the
    world by concatenating all the cascoords from the root to
    this coords.

[]() **:analysis-level** *&optional v* \[メソッド\]

 
:   Change analysis level :coords only changes kinematics level and
    :body changes geometry too.

[]() **:weight** *&optional w* \[メソッド\]

 
:   Returns a weight of the link. If w is given, set weight.

[]() **:centroid** *&optional c* \[メソッド\]

 
:   Returns a centroid of the link. If c is given, set new centroid.

[]() **:inertia-tensor** *&optional i* \[メソッド\]

 
:   Returns a inertia tensor of the link. If c is given, set new
    intertia tensor.

[]() **:joint** *&rest args* \[メソッド\]

 
:   Returns a joint associated with this link. If args is given, args
    are forward to the joint.

[]() **:add-joint** *j* \[メソッド\]

 
:   Set j as joint of this link

[]() **:del-joint** \[メソッド\]

 
:   Remove current joint of this link

[]() **:parent-link** \[メソッド\]

 
:   Returns parent link

[]() **:child-links** \[メソッド\]

 
:   Returns child links

[]() **:add-child-links** *l* \[メソッド\]

 
:   Add l to child links

[]() **:add-parent-link** *l* \[メソッド\]

 
:   Set l as parent link

[]() **:del-child-link** *l* \[メソッド\]

 
:   Delete l from child links

[]() **:del-parent-link** \[メソッド\]

 
:   Delete parent link

[]() **:default-coords** *&optional c* \[メソッド\]

 

:   

[]() **:del-parent-link** \[メソッド\]

 
:   Delete parent link

[]() **:del-child-link** *l* \[メソッド\]

 
:   Delete l from child links

[]() **:add-parent-link** *l* \[メソッド\]

 
:   Set l as parent link

[]() **:add-child-links** *l* \[メソッド\]

 
:   Add l to child links

[]() **:child-links** \[メソッド\]

 
:   Returns child links

[]() **:parent-link** \[メソッド\]

 
:   Returns parent link

[]() **:del-joint** \[メソッド\]

 
:   Remove current joint of this link

[]() **:add-joint** *j* \[メソッド\]

 
:   Set j as joint of this link

[]() **:joint** *&rest args* \[メソッド\]

 
:   Returns a joint associated with this link. If args is given, args
    are forward to the joint.

[]() **:inertia-tensor** *&optional i* \[メソッド\]

 
:   Returns a inertia tensor of the link. If c is given, set new
    intertia tensor.

[]() **:centroid** *&optional c* \[メソッド\]

 
:   Returns a centroid of the link. If c is given, set new centroid.

[]() **:weight** *&optional w* \[メソッド\]

 
:   Returns a weight of the link. If w is given, set weight.

[]() **:analysis-level** *&optional v* \[メソッド\]

 
:   Change analysis level :coords only changes kinematics level and
    :body changes geometry too.

[]() **:worldcoords** *&optional (level analysis-level)* \[メソッド\]

 
:   Returns a coordinates object which represents this coord in the
    world by concatenating all the cascoords from the root to
    this coords.

[]() ![\\begin{emtabbing} {\\bf :init} \\it coords \\&rest args \\&key
\\= ((:analysis-level... ...nertia-tensor i) (unit-matrix 3)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img189.png){width="908" height="110"}

 
:   Create instance of bodyset-link.

[]() **:default-coords** *&optional c* \[メソッド\]

 

:   

[]() **cascaded-link** \[クラス\]

      :super   cascaded-coords 
      :slots         links joint-list bodies collision-avoidance-links end-coords-list 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= name
\\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img190.png){width="552" height="91"}

 
:   Create cascaded-link.

[]() **:init-ending** \[メソッド\]

 
:   This method is to called finalize the instantiation of
    the cascaded-link. This update bodies and child-link and parent link
    from joint-list

[]() **:links** *&rest args* \[メソッド\]

 
:   Returns links, or args is passed to links

[]() **:joint-list** *&rest args* \[メソッド\]

 
:   Returns joint list, or args is passed to joints

[]() **:link** *name* \[メソッド\]

 
:   Return a link with given name.

[]() **:joint** *name* \[メソッド\]

 
:   Return a joint with given name.

[]() **:end-coords** *name* \[メソッド\]

 
:   Returns end-coords with given name

[]() **:bodies** *&rest args* \[メソッド\]

 
:   Return bodies of this object. If args is given it passed to all
    bodies

[]() **:faces** \[メソッド\]

 
:   Return faces of this object.

[]() **:angle-vector** *&optional vec (angle-vector (instantiate
float-vector (calc-target-joint-dimension joint-list)))* \[メソッド\]

 
:   Returns angle-vector of this object, if vec is given, it updates
    angles of all joint. If given angle-vector violate min/max range,
    the value is modified.

[]() **:link-list** *to &optional from* \[メソッド\]

 
:   Find link list from to link to from link.

[]() **:plot-joint-min-max-table** *joint0 joint1* \[メソッド\]

 
:   Plot joint min max table on Euslisp window.

[]() ![\\begin{emtabbing} {\\bf :calc-jacobian-from-link-list} \\it
link-list \\&rest args... ...\\ \\&gt; (tmp-m33 (make-matrix 3 3)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img191.png){width="552" height="34"}

 
:   Calculate jacobian matrix from link-list and move-target. Unit
    system is \[m\] or \[rad\], not \[mm\] or \[deg\].

[]() ![\\begin{emtabbing} {\\bf :inverse-kinematics-loop} \\it dif-pos
dif-rot \\&rest arg... ... \\\\ \\&gt; debug-view \\\\ \\&gt; ik-args
\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img192.png){width="1108" height="301"}

 
:   :inverse-kinematics-loop is one loop calculation for
    :inverse-kinematics. In this method, joint position difference
    satisfying workspace difference (dif-pos, dif-rot) are calculated
    and euslisp model joint angles are updated. Optional arguments:
    :additional-check This argument is to add optional best-effort
    convergence conditions. :additional-check should be function or
    lambda. best-effort =&gt;In :inverse-kinematics-loop, 'success' is
    overwritten by '(and success additional-check)' In
    :inverse-kinematics, 'success is not overwritten. So,
    :inverse-kinematics-loop wait until ':additional-check' becomes 't'
    as possible, but ':additional-check' is neglected in the final
    :inverse-kinematics return. :min-loop Minimam loop count (nil by
    default). If integer is specified, :inverse-kinematics-loop does
    returns :ik-continues and continueing solving IK. If min-loop is
    nil, do not consider loop counting for IK convergence.

[]() ![\\begin{emtabbing} {\\bf :inverse-kinematics} \\it target-coords
\\&rest args \\&key... ...l-jacobi) \\\\ \\&gt; (additional-vel) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img193.png){width="1198" height="492"}

 
:   Move move-target to target-coords. dump-command should be t, nil,
    :always, :fail-only, :always-with-debug-log, or
    :fail-only-with-debug-log. Log are success/fail log and ik debug
    information log. t or :always : dump log both in success and fail
    (for success/fail log). :always-with-debug-log : dump log both in
    success and fail (for success/fail log and ik debug information
    log). :fail-only : dump log only in fail (for success/fail log).
    :always-with-debug-log : dump log only in fail (for success/fail log
    and ik debug information log). nil : do not dump log.

[]() ![\\begin{emtabbing} {\\bf :calc-grasp-matrix} \\it contact-points
\\&key \\= (ret (ma... ...pcar \\char93 '(lambda (r) (unit-matrix 3))
contact-points)) \\rm \\end{emtabbing}](jmanual-img194.png){width="1151"
height="435"}

 
:   Calc grasp matrix. Grasp matrix is defined as | E\_3 0 E\_3 0 ... |
    | p1x E\_3 p2x E\_3 ... | Arguments: contact-points is list of
    contact points\[mm\]. contact-rots is list of contact coords
    rotation\[rad\]. If contact-rots is specified, grasp matrix as
    follow: | R1 0 R2 0 ... | | p1xR1 R1 p2xR2 R2 ... |

[]() ![\\begin{emtabbing} {\\bf
:inverse-kinematics-for-closed-loop-forward-kinematics} ...
...t-angle-list) \\\\ \\&gt; (min-loop 2) \\\\ \\&gt;
\\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img195.png){width="782" height="35"}

 
:   Solve inverse-kinematics for closed loop forward kinematics. Move
    move-target to target-coords with link-list. link-list loop should
    be close when move-target reachs target-coords.
    constrained-joint-list is list of joints specified given joint
    angles in closed loop. constrained-joint-angle-list is list of
    joint-angle for constrained-joint-list.

[]() **:calc-jacobian-for-interlocking-joints** *link-list &key
(interlocking-joint-pairs (send self :interlocking-joint-pairs))*
\[メソッド\]

 
:   Calculate jacobian to keep interlocking joint velocity same.
    dtheta\_0 = dtheta\_1 =&gt;\[... 0 1 0 ... 0 -1 0 ....
    \]\[...dtheta\_0...dtheta\_1...\]

[]() **:calc-vel-for-interlocking-joints** *link-list &key
(interlocking-joint-pairs (send self :interlocking-joint-pairs))*
\[メソッド\]

 
:   Calculate 0 velocity for keeping interlocking joint at the same
    joint angle.

[]() **:set-midpoint-for-interlocking-joints** *&key
(interlocking-joint-pairs (send self :interlocking-joint-pairs))*
\[メソッド\]

 
:   Set interlocking joints at mid point of each joint angle.

[]() **:interlocking-joint-pairs** \[メソッド\]

 
:   Interlocking joint pairs. pairs are (list (cons joint0 joint1) ... )
    If users want to use interlocking joints, please overwrite
    this method.

[]() ![\\begin{emtabbing} {\\bf
:check-interlocking-joint-angle-validity} \\it\\&key \\= (a...
...rlocking-joint-pairs (send self :interlocking-joint-pairs)) \\rm
\\end{emtabbing}](jmanual-img196.png){width="849" height="148"}

 
:   Check if all interlocking joint pairs are same values.

[]() **:update-descendants** *&rest args* \[メソッド\]

 

:   

[]() **:find-link-route** *to &optional from* \[メソッド\]

 

:   

[]() **:make-joint-min-max-table** *l0 l1 joint0 joint1 &key (fat 0)
(fat2 nil) (debug nil) (margin 0.0) (overwrite-collision-model nil)*
\[メソッド\]

 

:   

[]() **:make-min-max-table-using-collision-check** *l0 l1 joint0 joint1
joint-range0 joint-range1 min-joint0 min-joint1 fat fat2 debug margin*
\[メソッド\]

 

:   

[]() **:plot-joint-min-max-table-common** *joint0 joint1* \[メソッド\]

 

:   

[]() **:calc-target-axis-dimension** *rotation-axis translation-axis*
\[メソッド\]

 

:   

[]() **:calc-union-link-list** *link-list* \[メソッド\]

 

:   

[]() **:calc-target-joint-dimension** *link-list* \[メソッド\]

 

:   

[]() **:calc-inverse-jacobian** *jacobi &rest args &key
((:manipulability-limit ml) 0.1) ((:manipulability-gain mg) 0.001)
weight debug-view ret wmat tmat umat umat2 mat-tmp mat-tmp-rc tmp-mrr
tmp-mrr2 &allow-other-keys* \[メソッド\]

 

:   

[]() **:calc-gradh-from-link-list** *link-list &optional (res
(instantiate float-vector (length link-list)))* \[メソッド\]

 

:   

[]() **:calc-joint-angle-speed** *union-vel &rest args &key angle-speed
(angle-speed-blending 0.5) jacobi jacobi\# null-space i-j\#j debug-view
weight wmat tmp-len tmp-len2 fik-len &allow-other-keys* \[メソッド\]

 

:   

[]() **:calc-joint-angle-speed-gain** *union-link-list dav
periodic-time* \[メソッド\]

 

:   

[]() **:collision-avoidance-links** *&optional l* \[メソッド\]

 

:   

[]() **:collision-avoidance-link-pair-from-link-list** *link-lists &key
obstacles ((:collision-avoidance-links collision-links)
collision-avoidance-links) debug* \[メソッド\]

 

:   

[]() **:collision-avoidance-calc-distance** *&rest args &key
union-link-list (warnp t) ((:collision-avoidance-link-pair pair-list))
((:collision-distance-limit distance-limit) 10) &allow-other-keys*
\[メソッド\]

 

:   

[]() **:collision-avoidance-args** *pair link-list* \[メソッド\]

 

:   

[]() **:collision-avoidance** *&rest args &key avoid-collision-distance
avoid-collision-joint-gain avoid-collision-null-gain
((:collision-avoidance-link-pair pair-list)) (union-link-list)
(link-list) (weight) (fik-len (send self :calc-target-joint-dimension
union-link-list)) debug-view &allow-other-keys* \[メソッド\]

 

:   

[]() **:move-joints** *union-vel &rest args &key union-link-list
(periodic-time 0.05) (joint-args) (debug-view nil) (move-joints-hook)
&allow-other-keys* \[メソッド\]

 

:   

[]() **:find-joint-angle-limit-weight-old-from-union-link-list**
*union-link-list* \[メソッド\]

 

:   

[]() **:reset-joint-angle-limit-weight-old** *union-link-list*
\[メソッド\]

 

:   

[]() **:calc-weight-from-joint-limit** *avoid-weight-gain fik-len
link-list union-link-list debug-view weight tmp-weight tmp-len*
\[メソッド\]

 

:   

[]() **:calc-inverse-kinematics-weight-from-link-list** *link-list &key
(avoid-weight-gain 1.0) (union-link-list (send self
:calc-union-link-list link-list)) (fik-len (send self
:calc-target-joint-dimension union-link-list)) (weight (fill
(instantiate float-vector fik-len) 1)) (additional-weight-list)
(debug-view) (tmp-weight (instantiate float-vector fik-len)) (tmp-len
(instantiate float-vector fik-len))* \[メソッド\]

 

:   

[]() **:calc-nspace-from-joint-limit** *avoid-nspace-gain
union-link-list weight debug-view tmp-nspace* \[メソッド\]

 

:   

[]() **:calc-inverse-kinematics-nspace-from-link-list** *link-list &key
(avoid-nspace-gain 0.01) (union-link-list (send self
:calc-union-link-list link-list)) (fik-len (send self
:calc-target-joint-dimension union-link-list)) (null-space) (debug-view)
(additional-nspace-list) (cog-gain 0.0) (target-centroid-pos)
(centroid-offset-func) (cog-translation-axis :z) (cog-null-space nil)
(weight (fill (instantiate float-vector fik-len) 1.0))
(update-mass-properties t) (tmp-nspace (instantiate float-vector
fik-len))* \[メソッド\]

 

:   

[]() **:move-joints-avoidance** *union-vel &rest args &key
union-link-list link-list (fik-len (send self
:calc-target-joint-dimension union-link-list)) (weight (fill
(instantiate float-vector fik-len) 1)) (null-space) (avoid-nspace-gain
0.01) (avoid-weight-gain 1.0) (avoid-collision-distance 200)
(avoid-collision-null-gain 1.0) (avoid-collision-joint-gain 1.0)
((:collision-avoidance-link-pair pair-list) (send self
:collision-avoidance-link-pair-from-link-list link-list :obstacles (cadr
(memq :obstacles args)) :debug (cadr (memq :debug-view args))))
(cog-gain 0.0) (target-centroid-pos) (centroid-offset-func)
(cog-translation-axis :z) (cog-null-space nil) (additional-weight-list)
(additional-nspace-list) (tmp-len (instantiate float-vector fik-len))
(tmp-len2 (instantiate float-vector fik-len)) (tmp-weight (instantiate
float-vector fik-len)) (tmp-nspace (instantiate float-vector fik-len))
(tmp-mcc (make-matrix fik-len fik-len)) (tmp-mcc2 (make-matrix fik-len
fik-len)) (debug-view) (jacobi) &allow-other-keys* \[メソッド\]

 

:   

[]() **:inverse-kinematics-args** *&rest args &key union-link-list
rotation-axis translation-axis additional-jacobi-dimension
&allow-other-keys* \[メソッド\]

 

:   

[]() **:draw-collision-debug-view** \[メソッド\]

 

:   

[]() **:ik-convergence-check** *success dif-pos dif-rot rotation-axis
translation-axis thre rthre centroid-thre target-centroid-pos
centroid-offset-func cog-translation-axis &key (update-mass-properties
t)* \[メソッド\]

 

:   

[]() **:calc-vel-from-pos** *dif-pos translation-axis &rest args &key
(p-limit 100.0) (tmp-v0 (instantiate float-vector 0)) (tmp-v1
(instantiate float-vector 1)) (tmp-v2 (instantiate float-vector 2))
(tmp-v3 (instantiate float-vector 3)) &allow-other-keys* \[メソッド\]

 

:   

[]() **:calc-vel-from-rot** *dif-rot rotation-axis &rest args &key
(r-limit 0.5) (tmp-v0 (instantiate float-vector 0)) (tmp-v1 (instantiate
float-vector 1)) (tmp-v2 (instantiate float-vector 2)) (tmp-v3
(instantiate float-vector 3)) &allow-other-keys* \[メソッド\]

 

:   

[]() **:collision-check-pairs** *&key ((:links ls) (cons (car links)
(all-child-links (car links))))* \[メソッド\]

 

:   

[]() **:self-collision-check** *&key (mode :all) (pairs (send self
:collision-check-pairs)) (collision-func 'pqp-collision-check)*
\[メソッド\]

 

:   

[]() **:plot-joint-min-max-table** *joint0 joint1* \[メソッド\]

 
:   Plot joint min max table on Euslisp window.

[]() **:link-list** *to &optional from* \[メソッド\]

 
:   Find link list from to link to from link.

[]() **:angle-vector** *&optional vec (angle-vector (instantiate
float-vector (calc-target-joint-dimension joint-list)))* \[メソッド\]

 
:   Returns angle-vector of this object, if vec is given, it updates
    angles of all joint. If given angle-vector violate min/max range,
    the value is modified.

[]() **:faces** \[メソッド\]

 
:   Return faces of this object.

[]() **:bodies** *&rest args* \[メソッド\]

 
:   Return bodies of this object. If args is given it passed to all
    bodies

[]() **:end-coords** *name* \[メソッド\]

 
:   Returns end-coords with given name

[]() **:joint** *name* \[メソッド\]

 
:   Return a joint with given name.

[]() **:link** *name* \[メソッド\]

 
:   Return a link with given name.

[]() **:joint-list** *&rest args* \[メソッド\]

 
:   Returns joint list, or args is passed to joints

[]() **:links** *&rest args* \[メソッド\]

 
:   Returns links, or args is passed to links

[]() **:init-ending** \[メソッド\]

 
:   This method is to called finalize the instantiation of
    the cascaded-link. This update bodies and child-link and parent link
    from joint-list

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= name
\\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img190.png){width="552" height="91"}

 
:   Create cascaded-link.

[]() **:plot-joint-min-max-table-common** *joint0 joint1* \[メソッド\]

 

:   

[]() **:make-min-max-table-using-collision-check** *l0 l1 joint0 joint1
joint-range0 joint-range1 min-joint0 min-joint1 fat fat2 debug margin*
\[メソッド\]

 

:   

[]() **:make-joint-min-max-table** *l0 l1 joint0 joint1 &key (fat 0)
(fat2 nil) (debug nil) (margin 0.0) (overwrite-collision-model nil)*
\[メソッド\]

 

:   

[]() **:find-link-route** *to &optional from* \[メソッド\]

 

:   

[]() **:update-descendants** *&rest args* \[メソッド\]

 

:   

[]() ![\\begin{emtabbing} {\\bf
:check-interlocking-joint-angle-validity} \\it\\&key \\= (a...
...rlocking-joint-pairs (send self :interlocking-joint-pairs)) \\rm
\\end{emtabbing}](jmanual-img196.png){width="849" height="148"}

 
:   Check if all interlocking joint pairs are same values.

[]() **:interlocking-joint-pairs** \[メソッド\]

 
:   Interlocking joint pairs. pairs are (list (cons joint0 joint1) ... )
    If users want to use interlocking joints, please overwrite
    this method.

[]() **:set-midpoint-for-interlocking-joints** *&key
(interlocking-joint-pairs (send self :interlocking-joint-pairs))*
\[メソッド\]

 
:   Set interlocking joints at mid point of each joint angle.

[]() **:calc-vel-for-interlocking-joints** *link-list &key
(interlocking-joint-pairs (send self :interlocking-joint-pairs))*
\[メソッド\]

 
:   Calculate 0 velocity for keeping interlocking joint at the same
    joint angle.

[]() **:calc-jacobian-for-interlocking-joints** *link-list &key
(interlocking-joint-pairs (send self :interlocking-joint-pairs))*
\[メソッド\]

 
:   Calculate jacobian to keep interlocking joint velocity same.
    dtheta\_0 = dtheta\_1 =&gt;\[... 0 1 0 ... 0 -1 0 ....
    \]\[...dtheta\_0...dtheta\_1...\]

[]() ![\\begin{emtabbing} {\\bf
:inverse-kinematics-for-closed-loop-forward-kinematics} ...
...t-angle-list) \\\\ \\&gt; (min-loop 2) \\\\ \\&gt;
\\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img195.png){width="782" height="35"}

 
:   Solve inverse-kinematics for closed loop forward kinematics. Move
    move-target to target-coords with link-list. link-list loop should
    be close when move-target reachs target-coords.
    constrained-joint-list is list of joints specified given joint
    angles in closed loop. constrained-joint-angle-list is list of
    joint-angle for constrained-joint-list.

[]() ![\\begin{emtabbing} {\\bf :calc-grasp-matrix} \\it contact-points
\\&key \\= (ret (ma... ...pcar \\char93 '(lambda (r) (unit-matrix 3))
contact-points)) \\rm \\end{emtabbing}](jmanual-img194.png){width="1151"
height="435"}

 
:   Calc grasp matrix. Grasp matrix is defined as | E\_3 0 E\_3 0 ... |
    | p1x E\_3 p2x E\_3 ... | Arguments: contact-points is list of
    contact points\[mm\]. contact-rots is list of contact coords
    rotation\[rad\]. If contact-rots is specified, grasp matrix as
    follow: | R1 0 R2 0 ... | | p1xR1 R1 p2xR2 R2 ... |

[]() ![\\begin{emtabbing} {\\bf :inverse-kinematics} \\it target-coords
\\&rest args \\&key... ...l-jacobi) \\\\ \\&gt; (additional-vel) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img193.png){width="1198" height="492"}

 
:   Move move-target to target-coords. dump-command should be t, nil,
    :always, :fail-only, :always-with-debug-log, or
    :fail-only-with-debug-log. Log are success/fail log and ik debug
    information log. t or :always : dump log both in success and fail
    (for success/fail log). :always-with-debug-log : dump log both in
    success and fail (for success/fail log and ik debug information
    log). :fail-only : dump log only in fail (for success/fail log).
    :always-with-debug-log : dump log only in fail (for success/fail log
    and ik debug information log). nil : do not dump log.

[]() ![\\begin{emtabbing} {\\bf :inverse-kinematics-loop} \\it dif-pos
dif-rot \\&rest arg... ... \\\\ \\&gt; debug-view \\\\ \\&gt; ik-args
\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img192.png){width="1108" height="301"}

 
:   :inverse-kinematics-loop is one loop calculation for
    :inverse-kinematics. In this method, joint position difference
    satisfying workspace difference (dif-pos, dif-rot) are calculated
    and euslisp model joint angles are updated. Optional arguments:
    :additional-check This argument is to add optional best-effort
    convergence conditions. :additional-check should be function or
    lambda. best-effort =&gt;In :inverse-kinematics-loop, 'success' is
    overwritten by '(and success additional-check)' In
    :inverse-kinematics, 'success is not overwritten. So,
    :inverse-kinematics-loop wait until ':additional-check' becomes 't'
    as possible, but ':additional-check' is neglected in the final
    :inverse-kinematics return. :min-loop Minimam loop count (nil by
    default). If integer is specified, :inverse-kinematics-loop does
    returns :ik-continues and continueing solving IK. If min-loop is
    nil, do not consider loop counting for IK convergence.

[]() ![\\begin{emtabbing} {\\bf :calc-jacobian-from-link-list} \\it
link-list \\&rest args... ...\\ \\&gt; (tmp-m33 (make-matrix 3 3)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img191.png){width="552" height="34"}

 
:   Calculate jacobian matrix from link-list and move-target. Unit
    system is \[m\] or \[rad\], not \[mm\] or \[deg\].

[]() **:self-collision-check** *&key (mode :all) (pairs (send self
:collision-check-pairs)) (collision-func 'pqp-collision-check)*
\[メソッド\]

 

:   

[]() **:collision-check-pairs** *&key ((:links ls) (cons (car links)
(all-child-links (car links))))* \[メソッド\]

 

:   

[]() **:calc-vel-from-rot** *dif-rot rotation-axis &rest args &key
(r-limit 0.5) (tmp-v0 (instantiate float-vector 0)) (tmp-v1 (instantiate
float-vector 1)) (tmp-v2 (instantiate float-vector 2)) (tmp-v3
(instantiate float-vector 3)) &allow-other-keys* \[メソッド\]

 

:   

[]() **:calc-vel-from-pos** *dif-pos translation-axis &rest args &key
(p-limit 100.0) (tmp-v0 (instantiate float-vector 0)) (tmp-v1
(instantiate float-vector 1)) (tmp-v2 (instantiate float-vector 2))
(tmp-v3 (instantiate float-vector 3)) &allow-other-keys* \[メソッド\]

 

:   

[]() **:ik-convergence-check** *success dif-pos dif-rot rotation-axis
translation-axis thre rthre centroid-thre target-centroid-pos
centroid-offset-func cog-translation-axis &key (update-mass-properties
t)* \[メソッド\]

 

:   

[]() **:draw-collision-debug-view** \[メソッド\]

 

:   

[]() **:inverse-kinematics-args** *&rest args &key union-link-list
rotation-axis translation-axis additional-jacobi-dimension
&allow-other-keys* \[メソッド\]

 

:   

[]() **:move-joints-avoidance** *union-vel &rest args &key
union-link-list link-list (fik-len (send self
:calc-target-joint-dimension union-link-list)) (weight (fill
(instantiate float-vector fik-len) 1)) (null-space) (avoid-nspace-gain
0.01) (avoid-weight-gain 1.0) (avoid-collision-distance 200)
(avoid-collision-null-gain 1.0) (avoid-collision-joint-gain 1.0)
((:collision-avoidance-link-pair pair-list) (send self
:collision-avoidance-link-pair-from-link-list link-list :obstacles (cadr
(memq :obstacles args)) :debug (cadr (memq :debug-view args))))
(cog-gain 0.0) (target-centroid-pos) (centroid-offset-func)
(cog-translation-axis :z) (cog-null-space nil) (additional-weight-list)
(additional-nspace-list) (tmp-len (instantiate float-vector fik-len))
(tmp-len2 (instantiate float-vector fik-len)) (tmp-weight (instantiate
float-vector fik-len)) (tmp-nspace (instantiate float-vector fik-len))
(tmp-mcc (make-matrix fik-len fik-len)) (tmp-mcc2 (make-matrix fik-len
fik-len)) (debug-view) (jacobi) &allow-other-keys* \[メソッド\]

 

:   

[]() **:calc-inverse-kinematics-nspace-from-link-list** *link-list &key
(avoid-nspace-gain 0.01) (union-link-list (send self
:calc-union-link-list link-list)) (fik-len (send self
:calc-target-joint-dimension union-link-list)) (null-space) (debug-view)
(additional-nspace-list) (cog-gain 0.0) (target-centroid-pos)
(centroid-offset-func) (cog-translation-axis :z) (cog-null-space nil)
(weight (fill (instantiate float-vector fik-len) 1.0))
(update-mass-properties t) (tmp-nspace (instantiate float-vector
fik-len))* \[メソッド\]

 

:   

[]() **:calc-nspace-from-joint-limit** *avoid-nspace-gain
union-link-list weight debug-view tmp-nspace* \[メソッド\]

 

:   

[]() **:calc-inverse-kinematics-weight-from-link-list** *link-list &key
(avoid-weight-gain 1.0) (union-link-list (send self
:calc-union-link-list link-list)) (fik-len (send self
:calc-target-joint-dimension union-link-list)) (weight (fill
(instantiate float-vector fik-len) 1)) (additional-weight-list)
(debug-view) (tmp-weight (instantiate float-vector fik-len)) (tmp-len
(instantiate float-vector fik-len))* \[メソッド\]

 

:   

[]() **:calc-weight-from-joint-limit** *avoid-weight-gain fik-len
link-list union-link-list debug-view weight tmp-weight tmp-len*
\[メソッド\]

 

:   

[]() **:reset-joint-angle-limit-weight-old** *union-link-list*
\[メソッド\]

 

:   

[]() **:find-joint-angle-limit-weight-old-from-union-link-list**
*union-link-list* \[メソッド\]

 

:   

[]() **:move-joints** *union-vel &rest args &key union-link-list
(periodic-time 0.05) (joint-args) (debug-view nil) (move-joints-hook)
&allow-other-keys* \[メソッド\]

 

:   

[]() **:collision-avoidance** *&rest args &key avoid-collision-distance
avoid-collision-joint-gain avoid-collision-null-gain
((:collision-avoidance-link-pair pair-list)) (union-link-list)
(link-list) (weight) (fik-len (send self :calc-target-joint-dimension
union-link-list)) debug-view &allow-other-keys* \[メソッド\]

 

:   

[]() **:collision-avoidance-args** *pair link-list* \[メソッド\]

 

:   

[]() **:collision-avoidance-calc-distance** *&rest args &key
union-link-list (warnp t) ((:collision-avoidance-link-pair pair-list))
((:collision-distance-limit distance-limit) 10) &allow-other-keys*
\[メソッド\]

 

:   

[]() **:collision-avoidance-link-pair-from-link-list** *link-lists &key
obstacles ((:collision-avoidance-links collision-links)
collision-avoidance-links) debug* \[メソッド\]

 

:   

[]() **:collision-avoidance-links** *&optional l* \[メソッド\]

 

:   

[]() **:calc-joint-angle-speed-gain** *union-link-list dav
periodic-time* \[メソッド\]

 

:   

[]() **:calc-joint-angle-speed** *union-vel &rest args &key angle-speed
(angle-speed-blending 0.5) jacobi jacobi\# null-space i-j\#j debug-view
weight wmat tmp-len tmp-len2 fik-len &allow-other-keys* \[メソッド\]

 

:   

[]() **:calc-gradh-from-link-list** *link-list &optional (res
(instantiate float-vector (length link-list)))* \[メソッド\]

 

:   

[]() **:calc-inverse-jacobian** *jacobi &rest args &key
((:manipulability-limit ml) 0.1) ((:manipulability-gain mg) 0.001)
weight debug-view ret wmat tmat umat umat2 mat-tmp mat-tmp-rc tmp-mrr
tmp-mrr2 &allow-other-keys* \[メソッド\]

 

:   

[]() **:calc-target-joint-dimension** *link-list* \[メソッド\]

 

:   

[]() **:calc-union-link-list** *link-list* \[メソッド\]

 

:   

[]() **:calc-target-axis-dimension** *rotation-axis translation-axis*
\[メソッド\]

 

:   

[]() **eusmodel-validity-check** *robot* \[関数\]

 
:   Check if the robot model is validate

[]() **calc-jacobian-default-rotate-vector** *paxis world-default-coords
child-reverse transform-coords tmp-v3 tmp-m33* \[関数\]

 

:   

[]() **calc-jacobian-rotational** *fik row column joint paxis child-link
world-default-coords child-reverse move-target transform-coords
rotation-axis translation-axis tmp-v0 tmp-v1 tmp-v2 tmp-v3 tmp-v3a
tmp-v3b tmp-m33* \[関数\]

 

:   

[]() **calc-jacobian-linear** *fik row column joint paxis child-link
world-default-coords child-reverse move-target transform-coords
rotation-axis translation-axis tmp-v0 tmp-v1 tmp-v2 tmp-v3 tmp-v3a
tmp-v3b tmp-m33* \[関数\]

 

:   

[]() **calc-angle-speed-gain-scalar** *j dav i periodic-time* \[関数\]

 

:   

[]() **calc-angle-speed-gain-vector** *j dav i periodic-time* \[関数\]

 

:   

[]() **all-child-links** *s &optional (pred \#'identity)* \[関数\]

 

:   

[]() **calc-dif-with-axis** *dif axis &optional tmp-v0 tmp-v1 tmp-v2*
\[関数\]

 

:   

[]() **calc-target-joint-dimension** *joint-list* \[関数\]

 

:   

[]() **calc-joint-angle-min-max-for-limit-calculation** *j kk jamm*
\[関数\]

 

:   

[]() **joint-angle-limit-weight** *j-l &optional (res (instantiate
float-vector (calc-target-joint-dimension j-l)))* \[関数\]

 

:   

[]() **joint-angle-limit-nspace** *j-l &optional (res (instantiate
float-vector (calc-target-joint-dimension j-l)))* \[関数\]

 

:   

[]()
**calc-jacobian-from-link-list-including-robot-and-obj-virtual-joint**
*link-list move-target obj-move-target robot &key (rotation-axis '(t t))
(translation-axis '(t t)) (fik (make-matrix (send robot
:calc-target-axis-dimension rotation-axis translation-axis) (send robot
:calc-target-joint-dimension link-list)))* \[関数\]

 

:   

[]() **append-obj-virtual-joint** *link-list target-coords &key
(joint-class 6dof-joint) (joint-args) (vplink) (vplink-coords)
(vclink-coords)* \[関数\]

 

:   

[]() **append-multiple-obj-virtual-joint** *link-list target-coords &key
(joint-class '(6dof-joint)) (joint-args '(nil)) (vplink) (vplink-coords)
(vclink-coords)* \[関数\]

 

:   

[]() **eusmodel-validity-check-one** *robot* \[関数\]

 

:   

\
[]() **line** \[クラス\]

      :super   propertied-object 
      :slots         pvert nvert 

 

:   

[]() **:worldcoords** \[メソッド\]

 
:   Return a coordinates on the midpoint of the end points

[]() **coordinates** \[クラス\]

      :super   propertied-object 
      :slots         rot pos 

 

:   

[]() **:difference-rotation** *coords &key (geometry::rotation-axis t)*
\[メソッド\]

 
:   return diffece in rotation of given coords, rotation-axis can take
    (:x, :y, :z, :xx, :yy, :zz, :xm, :ym, :zm)

[]() **:difference-position** *coords &key (geometry::translation-axis
t)* \[メソッド\]

 
:   return diffece in positoin of given coords, translation-axis can
    take (:x, :y, :z, :xy, :yz, :zx).

[]() **:axis** *geometry::axis* \[メソッド\]

 

:   

[]() **coordinates** \[クラス\]

      :super   propertied-object 
      :slots         rot pos 

 

:   

[]() **:transform** *geometry::c &optional (geometry::wrt :local)*
\[メソッド\]

 

:   

[]() **:transformation** *geometry::c2 &optional (geometry::wrt :local)*
\[メソッド\]

 

:   

[]() **:move-to** *geometry::c &optional (geometry::wrt :local) &aux
geometry::cc* \[メソッド\]

 

:   

[]() **cascaded-coords** \[クラス\]

      :super   coordinates 
      :slots         rot pos parent descendants worldcoords manager changed 

 

:   

[]() **:move-coords** *geometry::target geometry::at* \[メソッド\]

 

:   

[]() **:move-to** *geometry::c &optional (geometry::wrt :local) &aux
geometry::cc* \[メソッド\]

 

:   

[]() **:transform** *geometry::c &optional (geometry::wrt :local)*
\[メソッド\]

 

:   

[]() **:transformation** *geometry::c2 &optional (geometry::wrt :local)*
\[メソッド\]

 

:   

[]() **:worldcoords** \[メソッド\]

 

:   

[]() **coordinates** \[クラス\]

      :super   propertied-object 
      :slots         rot pos 

 

:   

[]() **:inverse-rotate-vector** *geometry::v &optional geometry::r*
\[メソッド\]

 

:   

[]() **:rotate-vector** *geometry::v &optional geometry::r* \[メソッド\]

 

:   

[]() **cascaded-coords** \[クラス\]

      :super   coordinates 
      :slots         rot pos parent descendants worldcoords manager changed 

 

:   

[]() **:inverse-rotate-vector** *geometry::v &optional geometry::r*
\[メソッド\]

 

:   

[]() **:rotate-vector** *geometry::v &optional geometry::r* \[メソッド\]

 

:   

[]() **coordinates** \[クラス\]

      :super   propertied-object 
      :slots         rot pos 

 

:   

[]() **:inverse-transform-vector** *geometry::vec &optional
geometry::v3a geometry::v3b geometry::m33* \[メソッド\]

 

:   

[]() **cascaded-coords** \[クラス\]

      :super   coordinates 
      :slots         rot pos parent descendants worldcoords manager changed 

 

:   

[]() **:inverse-transform-vector** *geometry::v &optional geometry::v3a
geometry::v3b geometry::m33* \[メソッド\]

 

:   

[]() **bodyset** \[クラス\]

      :super   cascaded-coords 
      :slots         (geometry::bodies :type cons) 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it coords \\&rest args \\&key
\\= (name (intern (fo... ...d\]\\\\ \\&gt; ((:bodies geometry::bs)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img197.png){width="770" height="35"}

 
:   Create bodyset object

[]() **:bodies** *&rest args* \[メソッド\]

 

:   

[]() **:faces** \[メソッド\]

 

:   

[]() **:worldcoords** \[メソッド\]

 

:   

[]() **:draw-on** *&rest args* \[メソッド\]

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it coords \\&rest args \\&key
\\= (name (intern (fo... ...d\]\\\\ \\&gt; ((:bodies geometry::bs)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img197.png){width="770" height="35"}

 
:   Create bodyset object

[]() **:draw-on** *&rest args* \[メソッド\]

 

:   

[]() **:worldcoords** \[メソッド\]

 

:   

[]() **:faces** \[メソッド\]

 

:   

[]() **:bodies** *&rest args* \[メソッド\]

 

:   

[]() **midcoords** *geometry::p geometry::c1 geometry::c2* \[関数\]

 
:   Returns mid (or p) coordinates of given two cooridnates c1 and c2

[]() **orient-coords-to-axis** *geometry::target-coords geometry::v
&optional (geometry::axis :z) (geometry::eps epsilon)* \[関数\]

 
:   orient 'axis' in 'target-coords' to the direction specified by 'v'
    destructively. 'v' must be non-zero vector.

[]() **geometry::face-to-triangle-aux** *geometry::f* \[関数\]

 
:   triangulate the face.

[]() **geometry::face-to-triangle** *geometry::f* \[関数\]

 
:   convert face to set of triangles.

[]() **geometry::face-to-tessel-triangle** *geometry::f geometry::num*
\[関数\]

 
:   return polygon if triangable, return nil if it is not.

[]() **body-to-faces** *geometry::abody* \[関数\]

 
:   return triangled faces of given body

[]() **make-sphere** *geometry::r &rest args* \[関数\]

 
:   make sphere of given r

[]() **make-ring** *geometry::ring-radius geometry::pipe-radius &rest
args &key (geometry::segments 16)* \[関数\]

 
:   make ring of given ring and pipe radius

[]() ![\\begin{emtabbing} {\\bf make-fan-cylinder} \\it geometry::radius
geometry::height... ... \\&gt; (angle 2pi) \\\\ \\&gt;
(geometry::mid-angle (/ angle 2.0)) \\rm
\\end{emtabbing}](jmanual-img198.png){width="787" height="53"}

 
:   make a cylinder whose base face is a fan. the angle of fan is
    defined by :angle keyword. and, the csg of the returned body is
    (:cylinder radius height segments angle)

[]() **x-of-cube** *geometry::cub* \[関数\]

 
:   return x of cube.

[]() **y-of-cube** *geometry::cub* \[関数\]

 
:   return y of cube.

[]() **z-of-cube** *geometry::cub* \[関数\]

 
:   return z of cube.

[]() **height-of-cylinder** *geometry::cyl* \[関数\]

 
:   return height of cylinder.

[]() **radius-of-cylinder** *geometry::cyl* \[関数\]

 
:   return radius of cylinder.

[]() **radius-of-sphere** *geometry::sp* \[関数\]

 
:   return radius of shape.

[]() **geometry::make-faceset-from-vertices** *geometry::vs* \[関数\]

 
:   create faceset from vertices.

[]() **matrix-to-euler-angle** *geometry::m geometry::axis-order*
\[関数\]

 
:   return euler angle from matrix.

[]() **geometry::quaternion-from-two-vectors** *geometry::a geometry::b*
\[関数\]

 
:   Comupute quaternion which rotate vector a into b.

[]() **transform-coords** *geometry::c1 geometry::c2 &optional
(geometry::c3 (let ((geometry::dim (send geometry::c1 :dimension)))
(instance coordinates :newcoords (unit-matrix geometry::dim)
(instantiate float-vector geometry::dim))))* \[関数\]

 

:   

[]() **geometry::face-to-triangle-rest-polygon** *geometry::f
geometry::num geometry::edgs* \[関数\]

 

:   

[]() **geometry::face-to-triangle-make-simple** *geometry::f* \[関数\]

 

:   

[]() **body-to-triangles** *geometry::abody &optional (geometry::limit
50)* \[関数\]

 

:   

[]() **geometry::triangle-to-triangle** *geometry::aface &optional
(geometry::limit 50)* \[関数\]

 

:   

\
[]() **robot-model** \[クラス\]

      :super   cascaded-link 
      :slots         larm-end-coords rarm-end-coords lleg-end-coords rleg-end-coords head-end-coords torso-end-coords larm-root-link rarm-root-link lleg-root-link rleg-root-link head-root-link torso-root-link larm-collision-avoidance-links rarm-collision-avoidance-links larm rarm lleg rleg torso head force-sensors imu-sensors cameras support-polygons 

 

:   

[]() **:camera** *sensor-name* \[メソッド\]

 
:   Returns camera with given name

[]() **:force-sensor** *sensor-name* \[メソッド\]

 
:   Returns force sensor with given name

[]() **:imu-sensor** *sensor-name* \[メソッド\]

 
:   Returns imu sensor of given name

[]() **:force-sensors** \[メソッド\]

 
:   Returns force sensors.

[]() **:imu-sensors** \[メソッド\]

 
:   Returns imu sensors.

[]() **:cameras** \[メソッド\]

 
:   Returns camera sensors.

[]() **:look-at-hand** *l/r* \[メソッド\]

 
:   look at hand position, l/r supports :rarm, :larm, :arms, and
    '(:rarm :larm)

[]() ![\\begin{emtabbing} {\\bf :inverse-kinematics} \\it target-coords
\\&rest args \\&key... ...send mt :parent))) move-target))) \\\\ \\&gt;
\\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img199.png){width="755" height="54"}

 
:   solve inverse kinematics, move move-target to target-coords
    look-at-target suppots t, nil, float-vector, coords, list of
    float-vector, list of coords link-list is set by default based on
    move-target -&gt;root link link-list

[]() ![\\begin{emtabbing} {\\bf :inverse-kinematics-loop} \\it dif-pos
dif-rot \\&rest arg... ...send mt :parent))) move-target))) \\\\ \\&gt;
\\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img200.png){width="1477" height="72"}

 
:   move move-target using dif-pos and dif-rot, look-at-target suppots
    t, nil, float-vector, coords, list of float-vector, list of coords
    link-list is set by default based on move-target -&gt;root link
    link-list

[]() **:look-at-target** *look-at-target &key (target-coords)*
\[メソッド\]

 
:   move robot head to look at targets, look-at-target support t/nil
    float-vector coordinates, center of list of float-vector or list of
    coordinates

[]() **:init-pose** \[メソッド\]

 
:   Set robot to initial posture.

[]() ![\\begin{emtabbing} {\\bf :torque-vector} \\it\\&key \\=
(force-list) \\\\lq \[method\]\\\\ ... ...ords)))
:distribute-total-wrench-to-torque-method-default)) \\rm
\\end{emtabbing}](jmanual-img201.png){width="1524" height="110"}

 
:   Returns torque vector

[]() ![\\begin{emtabbing} {\\bf :calc-force-from-joint-torque} \\it limb
all-torque \\&key... ...(send self limb :end-coords)) \\\\lq
\[method\]\\\\ \\&gt; (use-torso) \\rm
\\end{emtabbing}](jmanual-img202.png){width="1455" height="188"}

 
:   Calculates end-effector force and moment from joint torques.

[]() ![\\begin{emtabbing} {\\bf :fullbody-inverse-kinematics} \\it
target-coords \\&rest a... ...ll-space nil) \\\\ \\&gt; (min-loop 2)
\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img203.png){width="736" height="35"}

 
:   fullbody inverse kinematics for legged robot. necessary args :
    target-coords, move-target, and link-list must include legs'
    (or leg's) parameters ex. (send robot:fullbody-inverse-kinematics
    (list rarm-tc rleg-tc lleg-tc) :move-target (list rarm-mt
    rleg-mt lleg-mt) :link-list (list rarm-ll rleg-ll lleg-ll))

[]() **:print-vector-for-robot-limb** *vec* \[メソッド\]

 
:   Print angle vector with limb alingment and limb indent. For example,
    if robot is rarm, larm, and torso, print result is: \#f( rarm-j0 ...
    rarm-jN larm-j0 ... larm-jN torso-j0 ... torso-jN )

[]() ![\\begin{emtabbing} {\\bf :calc-zmp-from-forces-moments} \\it
forces moments \\&key ... ...n-all-values t))) force-sensors forces
moments cop-coords)) \\rm
\\end{emtabbing}](jmanual-img204.png){width="1016" height="282"}

 
:   Calculate zmp\[mm\] from sensor local forces and moments If force\_z
    is large, zmp can be defined and returns 3D zmp. Otherwise, zmp
    cannot be defined and returns nil.

[]() **:foot-midcoords** *&optional (mid 0.5)* \[メソッド\]

 
:   Calculate midcoords of :rleg and :lleg end-coords. In the following
    codes, leged robot is assumed.

[]() ![\\begin{emtabbing} {\\bf :fix-leg-to-coords} \\it fix-coords
\\&optional (l/r :both) \\&key \\= (mid 0.5) \\\\lq \[method\]\\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img205.png){width="2394" height="111"}

 
:   Fix robot's legs to a coords In the following codes, leged robot
    is assumed.

[]() ![\\begin{emtabbing} {\\bf :move-centroid-on-foot} \\it leg
fix-limbs \\&rest args \\&... ...har93 f(0.1 0.1 0.0 0.0 0.0 0.5)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img206.png){width="552" height="34"}

 
:   Move robot COG to change centroid-on-foot location, leg : legs for
    target of robot's centroid, which should be :both, :rleg, and :lleg.
    fix-limbs : limb names which are fixed in this IK.

[]() ![\\begin{emtabbing} {\\bf :calc-walk-pattern-from-footstep-list}
\\it footstep-list... ...k-thre 1) \\\\ \\&gt; (ik-rthre (deg2rad 1))
\\\\ \\&gt; (calc-zmp t) \\rm
\\end{emtabbing}](jmanual-img207.png){width="2036" height="129"}

 
:   Calculate walking pattern from foot step list and return pattern
    list as a list of angle-vector, root-coords, time, and so on.

[]() **:gen-footstep-parameter** *&key (ratio 1.0)* \[メソッド\]

 
:   Generate footstep parameter

[]() ![\\begin{emtabbing} {\\bf :go-pos-params-\\textgreater
footstep-list} \\it xx yy th ... ...(assoc leg leg-translate-pos)))))
(send cc :name leg) cc))) \\rm
\\end{emtabbing}](jmanual-img208.png){width="1826" height="245"}

 
:   Calculate foot step list from goal x position \[mm\], goal y
    position \[mm\], and goal yaw orientation \[deg\].

[]() **:go-pos-quadruped-params-&gt;footstep-list** *xx yy th &key (type
:crawl)* \[メソッド\]

 
:   Calculate foot step list for quadruped walking from goal x position
    \[mm\], goal y position \[mm\], and goal yaw orientation \[deg\].

[]() **:support-polygons** \[メソッド\]

 
:   Return support polygons.

[]() **:support-polygon** *name* \[メソッド\]

 
:   Return support polygon. If name is list, return convex hull of all
    polygons. Otherwise, return polygon with given name

[]() **:make-default-linear-link-joint-between-attach-coords**
*attach-coords-0 attach-coords-1 end-coords-name linear-joint-name*
\[メソッド\]

 
:   Make default linear arctuator module such as muscle and cylinder and
    append lins and joint-list. Module includes parent-link =&gt;(j0)
    =&gt;l0 =&gt;(j1) =&gt;l1 (linear actuator) =&gt;(j2) =&gt;l2
    =&gt;end-coords. attach-coords-0 is root side coords which linear
    actulator is attached to. attach-coords-1 is end side coords which
    linear actulator is attached to. end-coords-name is the name of
    end-coords. linear-joint-name is the name of linear actuator.

[]() ![\\begin{emtabbing} {\\bf :calc-static-balance-point} \\it\\&key
\\= (target-points (... ...nd-coords :worldpos)) 2)) \\\\ \\&gt;
(update-mass-properties t) \\rm
\\end{emtabbing}](jmanual-img209.png){width="1607" height="111"}

 
:   Calculate static balance point which is equivalent to static
    extended ZMP. The output is expressed by the world coordinates.
    target-points are end-effector points on which force-list and
    moment-list apply. force-list \[N\] and moment-list \[Nm\] are list
    of force and moment at target-points. static-balance-point-height is
    height of static balance point \[mm\].

[]() **:init-ending** \[メソッド\]

 

:   

[]() **:rarm-end-coords** \[メソッド\]

 

:   

[]() **:larm-end-coords** \[メソッド\]

 

:   

[]() **:rleg-end-coords** \[メソッド\]

 

:   

[]() **:lleg-end-coords** \[メソッド\]

 

:   

[]() **:head-end-coords** \[メソッド\]

 

:   

[]() **:torso-end-coords** \[メソッド\]

 

:   

[]() **:rarm-root-link** \[メソッド\]

 

:   

[]() **:larm-root-link** \[メソッド\]

 

:   

[]() **:rleg-root-link** \[メソッド\]

 

:   

[]() **:lleg-root-link** \[メソッド\]

 

:   

[]() **:head-root-link** \[メソッド\]

 

:   

[]() **:torso-root-link** \[メソッド\]

 

:   

[]() **:limb** *limb method &rest args* \[メソッド\]

 

:   

[]() **:inverse-kinematics-loop-for-look-at** *limb &rest args*
\[メソッド\]

 

:   

[]() **:gripper** *limb &rest args* \[メソッド\]

 

:   

[]() **:get-sensor-method** *sensor-type sensor-name* \[メソッド\]

 

:   

[]() **:get-sensors-method-by-limb** *sensors-type limb* \[メソッド\]

 

:   

[]() **:larm** *&rest args* \[メソッド\]

 

:   

[]() **:rarm** *&rest args* \[メソッド\]

 

:   

[]() **:lleg** *&rest args* \[メソッド\]

 

:   

[]() **:rleg** *&rest args* \[メソッド\]

 

:   

[]() **:head** *&rest args* \[メソッド\]

 

:   

[]() **:torso** *&rest args* \[メソッド\]

 

:   

[]() **:arms** *&rest args* \[メソッド\]

 

:   

[]() **:legs** *&rest args* \[メソッド\]

 

:   

[]() **:distribute-total-wrench-to-torque-method-default** \[メソッド\]

 

:   

[]() **:joint-angle-limit-nspace-for-6dof** *&key (avoid-nspace-gain
0.01) (limbs '(:rleg :lleg))* \[メソッド\]

 

:   

[]() **:joint-order** *limb &optional jname-list* \[メソッド\]

 

:   

[]() **:draw-gg-debug-view** *end-coords-list contact-state rz cog pz
czmp dt* \[メソッド\]

 

:   

[]() **:footstep-parameter** \[メソッド\]

 

:   

[]() **:make-support-polygons** \[メソッド\]

 

:   

[]() **:make-sole-polygon** *name* \[メソッド\]

 

:   

[]() ![\\begin{emtabbing} {\\bf :calc-zmp-from-forces-moments} \\it
forces moments \\&key ... ...n-all-values t))) force-sensors forces
moments cop-coords)) \\rm
\\end{emtabbing}](jmanual-img204.png){width="1016" height="282"}

 
:   Calculate zmp\[mm\] from sensor local forces and moments If force\_z
    is large, zmp can be defined and returns 3D zmp. Otherwise, zmp
    cannot be defined and returns nil.

[]() **:print-vector-for-robot-limb** *vec* \[メソッド\]

 
:   Print angle vector with limb alingment and limb indent. For example,
    if robot is rarm, larm, and torso, print result is: \#f( rarm-j0 ...
    rarm-jN larm-j0 ... larm-jN torso-j0 ... torso-jN )

[]() ![\\begin{emtabbing} {\\bf :fullbody-inverse-kinematics} \\it
target-coords \\&rest a... ...ll-space nil) \\\\ \\&gt; (min-loop 2)
\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img203.png){width="736" height="35"}

 
:   fullbody inverse kinematics for legged robot. necessary args :
    target-coords, move-target, and link-list must include legs'
    (or leg's) parameters ex. (send robot:fullbody-inverse-kinematics
    (list rarm-tc rleg-tc lleg-tc) :move-target (list rarm-mt
    rleg-mt lleg-mt) :link-list (list rarm-ll rleg-ll lleg-ll))

[]() ![\\begin{emtabbing} {\\bf :calc-force-from-joint-torque} \\it limb
all-torque \\&key... ...(send self limb :end-coords)) \\\\lq
\[method\]\\\\ \\&gt; (use-torso) \\rm
\\end{emtabbing}](jmanual-img202.png){width="1455" height="188"}

 
:   Calculates end-effector force and moment from joint torques.

[]() ![\\begin{emtabbing} {\\bf :torque-vector} \\it\\&key \\=
(force-list) \\\\lq \[method\]\\\\ ... ...ords)))
:distribute-total-wrench-to-torque-method-default)) \\rm
\\end{emtabbing}](jmanual-img201.png){width="1524" height="110"}

 
:   Returns torque vector

[]() **:init-pose** \[メソッド\]

 
:   Set robot to initial posture.

[]() **:look-at-target** *look-at-target &key (target-coords)*
\[メソッド\]

 
:   move robot head to look at targets, look-at-target support t/nil
    float-vector coordinates, center of list of float-vector or list of
    coordinates

[]() ![\\begin{emtabbing} {\\bf :inverse-kinematics-loop} \\it dif-pos
dif-rot \\&rest arg... ...send mt :parent))) move-target))) \\\\ \\&gt;
\\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img200.png){width="1477" height="72"}

 
:   move move-target using dif-pos and dif-rot, look-at-target suppots
    t, nil, float-vector, coords, list of float-vector, list of coords
    link-list is set by default based on move-target -&gt;root link
    link-list

[]() ![\\begin{emtabbing} {\\bf :inverse-kinematics} \\it target-coords
\\&rest args \\&key... ...send mt :parent))) move-target))) \\\\ \\&gt;
\\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img199.png){width="755" height="54"}

 
:   solve inverse kinematics, move move-target to target-coords
    look-at-target suppots t, nil, float-vector, coords, list of
    float-vector, list of coords link-list is set by default based on
    move-target -&gt;root link link-list

[]() **:look-at-hand** *l/r* \[メソッド\]

 
:   look at hand position, l/r supports :rarm, :larm, :arms, and
    '(:rarm :larm)

[]() **:cameras** \[メソッド\]

 
:   Returns camera sensors.

[]() **:imu-sensors** \[メソッド\]

 
:   Returns imu sensors.

[]() **:force-sensors** \[メソッド\]

 
:   Returns force sensors.

[]() **:imu-sensor** *sensor-name* \[メソッド\]

 
:   Returns imu sensor of given name

[]() **:force-sensor** *sensor-name* \[メソッド\]

 
:   Returns force sensor with given name

[]() **:camera** *sensor-name* \[メソッド\]

 
:   Returns camera with given name

[]() **:joint-order** *limb &optional jname-list* \[メソッド\]

 

:   

[]() **:joint-angle-limit-nspace-for-6dof** *&key (avoid-nspace-gain
0.01) (limbs '(:rleg :lleg))* \[メソッド\]

 

:   

[]() **:distribute-total-wrench-to-torque-method-default** \[メソッド\]

 

:   

[]() **:legs** *&rest args* \[メソッド\]

 

:   

[]() **:arms** *&rest args* \[メソッド\]

 

:   

[]() **:torso** *&rest args* \[メソッド\]

 

:   

[]() **:head** *&rest args* \[メソッド\]

 

:   

[]() **:rleg** *&rest args* \[メソッド\]

 

:   

[]() **:lleg** *&rest args* \[メソッド\]

 

:   

[]() **:rarm** *&rest args* \[メソッド\]

 

:   

[]() **:larm** *&rest args* \[メソッド\]

 

:   

[]() **:get-sensors-method-by-limb** *sensors-type limb* \[メソッド\]

 

:   

[]() **:get-sensor-method** *sensor-type sensor-name* \[メソッド\]

 

:   

[]() **:gripper** *limb &rest args* \[メソッド\]

 

:   

[]() **:inverse-kinematics-loop-for-look-at** *limb &rest args*
\[メソッド\]

 

:   

[]() **:limb** *limb method &rest args* \[メソッド\]

 

:   

[]() **:torso-root-link** \[メソッド\]

 

:   

[]() **:head-root-link** \[メソッド\]

 

:   

[]() **:lleg-root-link** \[メソッド\]

 

:   

[]() **:rleg-root-link** \[メソッド\]

 

:   

[]() **:larm-root-link** \[メソッド\]

 

:   

[]() **:rarm-root-link** \[メソッド\]

 

:   

[]() **:torso-end-coords** \[メソッド\]

 

:   

[]() **:head-end-coords** \[メソッド\]

 

:   

[]() **:lleg-end-coords** \[メソッド\]

 

:   

[]() **:rleg-end-coords** \[メソッド\]

 

:   

[]() **:larm-end-coords** \[メソッド\]

 

:   

[]() **:rarm-end-coords** \[メソッド\]

 

:   

[]() **:init-ending** \[メソッド\]

 

:   

[]() ![\\begin{emtabbing} {\\bf :calc-static-balance-point} \\it\\&key
\\= (target-points (... ...nd-coords :worldpos)) 2)) \\\\ \\&gt;
(update-mass-properties t) \\rm
\\end{emtabbing}](jmanual-img209.png){width="1607" height="111"}

 
:   Calculate static balance point which is equivalent to static
    extended ZMP. The output is expressed by the world coordinates.
    target-points are end-effector points on which force-list and
    moment-list apply. force-list \[N\] and moment-list \[Nm\] are list
    of force and moment at target-points. static-balance-point-height is
    height of static balance point \[mm\].

[]() **:make-default-linear-link-joint-between-attach-coords**
*attach-coords-0 attach-coords-1 end-coords-name linear-joint-name*
\[メソッド\]

 
:   Make default linear arctuator module such as muscle and cylinder and
    append lins and joint-list. Module includes parent-link =&gt;(j0)
    =&gt;l0 =&gt;(j1) =&gt;l1 (linear actuator) =&gt;(j2) =&gt;l2
    =&gt;end-coords. attach-coords-0 is root side coords which linear
    actulator is attached to. attach-coords-1 is end side coords which
    linear actulator is attached to. end-coords-name is the name of
    end-coords. linear-joint-name is the name of linear actuator.

[]() **:support-polygon** *name* \[メソッド\]

 
:   Return support polygon. If name is list, return convex hull of all
    polygons. Otherwise, return polygon with given name

[]() **:support-polygons** \[メソッド\]

 
:   Return support polygons.

[]() **:go-pos-quadruped-params-&gt;footstep-list** *xx yy th &key (type
:crawl)* \[メソッド\]

 
:   Calculate foot step list for quadruped walking from goal x position
    \[mm\], goal y position \[mm\], and goal yaw orientation \[deg\].

[]() ![\\begin{emtabbing} {\\bf :go-pos-params-\\textgreater
footstep-list} \\it xx yy th ... ...(assoc leg leg-translate-pos)))))
(send cc :name leg) cc))) \\rm
\\end{emtabbing}](jmanual-img208.png){width="1826" height="245"}

 
:   Calculate foot step list from goal x position \[mm\], goal y
    position \[mm\], and goal yaw orientation \[deg\].

[]() **:gen-footstep-parameter** *&key (ratio 1.0)* \[メソッド\]

 
:   Generate footstep parameter

[]() ![\\begin{emtabbing} {\\bf :calc-walk-pattern-from-footstep-list}
\\it footstep-list... ...k-thre 1) \\\\ \\&gt; (ik-rthre (deg2rad 1))
\\\\ \\&gt; (calc-zmp t) \\rm
\\end{emtabbing}](jmanual-img207.png){width="2036" height="129"}

 
:   Calculate walking pattern from foot step list and return pattern
    list as a list of angle-vector, root-coords, time, and so on.

[]() ![\\begin{emtabbing} {\\bf :move-centroid-on-foot} \\it leg
fix-limbs \\&rest args \\&... ...har93 f(0.1 0.1 0.0 0.0 0.0 0.5)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img206.png){width="552" height="34"}

 
:   Move robot COG to change centroid-on-foot location, leg : legs for
    target of robot's centroid, which should be :both, :rleg, and :lleg.
    fix-limbs : limb names which are fixed in this IK.

[]() ![\\begin{emtabbing} {\\bf :fix-leg-to-coords} \\it fix-coords
\\&optional (l/r :both) \\&key \\= (mid 0.5) \\\\lq \[method\]\\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img205.png){width="2394" height="111"}

 
:   Fix robot's legs to a coords In the following codes, leged robot
    is assumed.

[]() **:foot-midcoords** *&optional (mid 0.5)* \[メソッド\]

 
:   Calculate midcoords of :rleg and :lleg end-coords. In the following
    codes, leged robot is assumed.

[]() **:make-sole-polygon** *name* \[メソッド\]

 

:   

[]() **:make-support-polygons** \[メソッド\]

 

:   

[]() **:footstep-parameter** \[メソッド\]

 

:   

[]() **:draw-gg-debug-view** *end-coords-list contact-state rz cog pz
czmp dt* \[メソッド\]

 

:   

[]() **make-default-robot-link** *len radius axis name &optional
extbody* \[関数\]

 

:   

[センサモデル]()
----------------

\
[]() **sensor-model** \[クラス\]

      :super   body 
      :slots         data profile 

 

:   

[]() **:profile** *&optional p* \[メソッド\]

 

:   

[]() **:signal** *rawinfo* \[メソッド\]

 

:   

[]() **:simulate** *model* \[メソッド\]

 

:   

[]() **:read** \[メソッド\]

 

:   

[]() **:draw-sensor** *v* \[メソッド\]

 

:   

[]() **:init** *shape &key name &allow-other-keys* \[メソッド\]

 

:   

[]() **:init** *shape &key name &allow-other-keys* \[メソッド\]

 

:   

[]() **:draw-sensor** *v* \[メソッド\]

 

:   

[]() **:read** \[メソッド\]

 

:   

[]() **:simulate** *model* \[メソッド\]

 

:   

[]() **:signal** *rawinfo* \[メソッド\]

 

:   

[]() **:profile** *&optional p* \[メソッド\]

 

:   

[]() **bumper-model** \[クラス\]

      :super   sensor-model 
      :slots         bumper-threshold 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it b \\&rest args \\&key \\=
((:bumper-threshold bt) 20) \\\\lq \[method\]\\\\ \\&gt; name \\rm
\\end{emtabbing}](jmanual-img210.png){width="1096" height="92"}

 
:   Create bumper model, b is the shape of an object and bt is the
    threshold in distance\[mm\].

[]() **:simulate** *objs* \[メソッド\]

 
:   Simulate bumper, with given objects, return 1 if the sensor detects
    an object and 0 if not.

[]() **:draw** *vwer* \[メソッド\]

 

:   

[]() **:draw-sensor** *vwer* \[メソッド\]

 

:   

[]() **:simulate** *objs* \[メソッド\]

 
:   Simulate bumper, with given objects, return 1 if the sensor detects
    an object and 0 if not.

[]() ![\\begin{emtabbing} {\\bf :init} \\it b \\&rest args \\&key \\=
((:bumper-threshold bt) 20) \\\\lq \[method\]\\\\ \\&gt; name \\rm
\\end{emtabbing}](jmanual-img210.png){width="1096" height="92"}

 
:   Create bumper model, b is the shape of an object and bt is the
    threshold in distance\[mm\].

[]() **:draw-sensor** *vwer* \[メソッド\]

 

:   

[]() **:draw** *vwer* \[メソッド\]

 

:   

[]() **camera-model** \[クラス\]

      :super   sensor-model 
      :slots         (vwing :forward (:projection :newprojection :screen :view :viewpoint :view-direction :viewdistance :yon :hither)) img-viewer pwidth pheight 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it b \\&rest args \\&key \\=
((:width pw) 320) \\\\lq \[... ...ither 100.0) \\\\ \\&gt; (yon 10000.0)
\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img211.png){width="552" height="31"}

 
:   Create camera model. b is the shape of an object

[]() **:create-viewer** *&optional cv* \[メソッド\]

 
:   Create camera viewer, or set viewer

[]() **:width** \[メソッド\]

 
:   Returns width of the camera in pixel.

[]() **:height** \[メソッド\]

 
:   Returns height of the camera in pixel.

[]() **:fovy** \[メソッド\]

 
:   Returns field of view in degree

[]() **:cx** \[メソッド\]

 
:   Returns center x.

[]() **:cy** \[メソッド\]

 
:   Returns center y.

[]() **:fx** \[メソッド\]

 
:   Returns focal length of x.

[]() **:fy** \[メソッド\]

 
:   Returns focal length of y.

[]() **:screen-point** *pos* \[メソッド\]

 
:   Returns point in screen corresponds to the given pos.

[]() **:3d-point** *x y d* \[メソッド\]

 
:   Returns 3d position

[]() **:ray** *x y* \[メソッド\]

 
:   Returns ray vector of given x and y.

[]() ![\\begin{emtabbing} {\\bf :draw-on} \\it\\&rest args \\&key \\=
((:viewer vwer) \\texta... ...textasteriskcentered ) \\\\lq
\[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img212.png){width="552" height="129"}

 
:   Draw camera raw in irtviewer, ex (send cam :draw-on :flush t)

[]() **:draw-objects** *objs* \[メソッド\]

 
:   Draw objects in camera viewer, expected type of objs is list of
    objects

[]() ![\\begin{emtabbing} {\\bf :get-image} \\it\\&key \\= (with-points)
\\\\lq \[method\]\\\\ \\&gt; (with-colors) \\rm
\\end{emtabbing}](jmanual-img213.png){width="552" height="34"}

 
:   Get image objects you need to call :draw-objects before calling this
    function

[]() **:select-drawmode** *mode objs* \[メソッド\]

 
:   Change drawmode for drawing with :draw-objects methods. mode is
    symbol of mode, 'hid is symbol for hidden line mode, the other
    symbols indicate default mode. objs is the same objects
    using :draw-objects.

[]() **:viewing** *&rest args* \[メソッド\]

 

:   

[]() **:image-viewer** *&rest args* \[メソッド\]

 

:   

[]() **:draw-sensor** *vwer &key flush (width 1) (color (float-vector 1
1 1))* \[メソッド\]

 

:   

[]() **:draw-objects-raw** *vwr objs* \[メソッド\]

 

:   

[]() **:get-image-raw** *vwr &key (points) (colors)* \[メソッド\]

 

:   

[]() **:select-drawmode** *mode objs* \[メソッド\]

 
:   Change drawmode for drawing with :draw-objects methods. mode is
    symbol of mode, 'hid is symbol for hidden line mode, the other
    symbols indicate default mode. objs is the same objects
    using :draw-objects.

[]() ![\\begin{emtabbing} {\\bf :get-image} \\it\\&key \\= (with-points)
\\\\lq \[method\]\\\\ \\&gt; (with-colors) \\rm
\\end{emtabbing}](jmanual-img213.png){width="552" height="34"}

 
:   Get image objects you need to call :draw-objects before calling this
    function

[]() **:draw-objects** *objs* \[メソッド\]

 
:   Draw objects in camera viewer, expected type of objs is list of
    objects

[]() ![\\begin{emtabbing} {\\bf :draw-on} \\it\\&rest args \\&key \\=
((:viewer vwer) \\texta... ...textasteriskcentered ) \\\\lq
\[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img212.png){width="552" height="129"}

 
:   Draw camera raw in irtviewer, ex (send cam :draw-on :flush t)

[]() **:ray** *x y* \[メソッド\]

 
:   Returns ray vector of given x and y.

[]() **:3d-point** *x y d* \[メソッド\]

 
:   Returns 3d position

[]() **:screen-point** *pos* \[メソッド\]

 
:   Returns point in screen corresponds to the given pos.

[]() **:fy** \[メソッド\]

 
:   Returns focal length of y.

[]() **:fx** \[メソッド\]

 
:   Returns focal length of x.

[]() **:cy** \[メソッド\]

 
:   Returns center y.

[]() **:cx** \[メソッド\]

 
:   Returns center x.

[]() **:fovy** \[メソッド\]

 
:   Returns field of view in degree

[]() **:height** \[メソッド\]

 
:   Returns height of the camera in pixel.

[]() **:width** \[メソッド\]

 
:   Returns width of the camera in pixel.

[]() **:create-viewer** *&optional cv* \[メソッド\]

 
:   Create camera viewer, or set viewer

[]() ![\\begin{emtabbing} {\\bf :init} \\it b \\&rest args \\&key \\=
((:width pw) 320) \\\\lq \[... ...ither 100.0) \\\\ \\&gt; (yon 10000.0)
\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img211.png){width="552" height="31"}

 
:   Create camera model. b is the shape of an object

[]() **:get-image-raw** *vwr &key (points) (colors)* \[メソッド\]

 

:   

[]() **:draw-objects-raw** *vwr objs* \[メソッド\]

 

:   

[]() **:draw-sensor** *vwer &key flush (width 1) (color (float-vector 1
1 1))* \[メソッド\]

 

:   

[]() **:image-viewer** *&rest args* \[メソッド\]

 

:   

[]() **:viewing** *&rest args* \[メソッド\]

 

:   

[]() ![\\begin{emtabbing} {\\bf make-camera-from-param} \\it\\&key \\=
pwidth \\\\lq \[function\]... ...ty 0) \\\\ \\&gt; parent-coords \\\\
\\&gt; name \\\\ \\&gt; create-viewer \\rm
\\end{emtabbing}](jmanual-img214.png){width="552" height="35"}

 
:   Create camera object from given parameters.

[環境モデル]()
--------------

\
[]() **scene-model** \[クラス\]

      :super   cascaded-coords 
      :slots         name objs 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\=
((:name n) scene) \\\\lq \[met... ...cts o)) \\\\ \\&gt; ((:remove-wall
w)) \\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img215.png){width="553" height="203"}

 
:   Create scene model

[]() **:objects** \[メソッド\]

 
:   Returns objects in the scene.

[]() **:add-objects** *objects* \[メソッド\]

 
:   Add objects to scene with identifiable names. Returns all objects.

[]() **:add-object** *obj* \[メソッド\]

 
:   Add object to scene with identifiable name. Returns all objects.

[]() **:remove-objects** *objs-or-names* \[メソッド\]

 
:   Remove objects or objects with given names from scene. Returns
    removed objects.

[]() **:remove-object** *obj-or-name* \[メソッド\]

 
:   Remove object or object with given name from scene. Returns
    removed object.

[]() **:find-object** *name* \[メソッド\]

 
:   Returns objects with given name.

[]() **:add-spots** *spots* \[メソッド\]

 
:   Add spots to scene with identifiable names. All spots will be :assoc
    with this scene. Returns T if added spots successfly, otherwise
    returns NIL.

[]() **:add-spot** *spot* \[メソッド\]

 
:   Add spot to scene with identifiable name. The spot will be :assoc
    with this scene. Returns T if spot is added successfly, otherwise
    returns NIL.

[]() **:remove-spots** *spots* \[メソッド\]

 
:   Remove spots from this scene. All spots will be :dissoc with
    this scene. Returns removed spots.

[]() **:remove-spot** *spot* \[メソッド\]

 
:   Remove spot from scene. the spot will be :dissoc with this scene.
    Returns removed spot.

[]() **:spots** \[メソッド\]

 
:   Return all spots in the scene.

[]() **:object** *name* \[メソッド\]

 
:   Return an object of given name.

[]() **:spot** *name* \[メソッド\]

 
:   Return a spot of given name.

[]() **:bodies** \[メソッド\]

 

:   

[]() **:spot** *name* \[メソッド\]

 
:   Return a spot of given name.

[]() **:object** *name* \[メソッド\]

 
:   Return an object of given name.

[]() **:spots** \[メソッド\]

 
:   Return all spots in the scene.

[]() **:remove-spot** *spot* \[メソッド\]

 
:   Remove spot from scene. the spot will be :dissoc with this scene.
    Returns removed spot.

[]() **:remove-spots** *spots* \[メソッド\]

 
:   Remove spots from this scene. All spots will be :dissoc with
    this scene. Returns removed spots.

[]() **:add-spot** *spot* \[メソッド\]

 
:   Add spot to scene with identifiable name. The spot will be :assoc
    with this scene. Returns T if spot is added successfly, otherwise
    returns NIL.

[]() **:add-spots** *spots* \[メソッド\]

 
:   Add spots to scene with identifiable names. All spots will be :assoc
    with this scene. Returns T if added spots successfly, otherwise
    returns NIL.

[]() **:find-object** *name* \[メソッド\]

 
:   Returns objects with given name.

[]() **:remove-object** *obj-or-name* \[メソッド\]

 
:   Remove object or object with given name from scene. Returns
    removed object.

[]() **:remove-objects** *objs-or-names* \[メソッド\]

 
:   Remove objects or objects with given names from scene. Returns
    removed objects.

[]() **:add-object** *obj* \[メソッド\]

 
:   Add object to scene with identifiable name. Returns all objects.

[]() **:add-objects** *objects* \[メソッド\]

 
:   Add objects to scene with identifiable names. Returns all objects.

[]() **:objects** \[メソッド\]

 
:   Returns objects in the scene.

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\=
((:name n) scene) \\\\lq \[met... ...cts o)) \\\\ \\&gt; ((:remove-wall
w)) \\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img215.png){width="553" height="203"}

 
:   Create scene model

[]() **:bodies** \[メソッド\]

 

:   

[動力学計算・歩行動作生成]()
----------------------------

\
[]() **joint** \[クラス\]

      :super   propertied-object 
      :slots         parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target 

 

:   

[]() **:calc-inertia-matrix** *&rest args* \[メソッド\]

 

:   

[]() **rotational-joint** \[クラス\]

      :super   joint 
      :slots         parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target axis 

 

:   

[]() **:calc-inertia-matrix** *mat row column paxis m-til c-til i-til
axis-for-angular world-default-coords translation-axis rotation-axis
tmp-v0 tmp-v1 tmp-v2 tmp-va tmp-vb tmp-vc tmp-vd tmp-m* \[メソッド\]

 

:   

[]() **linear-joint** \[クラス\]

      :super   joint 
      :slots         parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target axis 

 

:   

[]() **:calc-inertia-matrix** *mat row column paxis m-til c-til i-til
axis-for-angular world-default-coords translation-axis rotation-axis
tmp-v0 tmp-v1 tmp-v2 tmp-va tmp-vb tmp-vc tmp-vd tmp-m* \[メソッド\]

 

:   

[]() **omniwheel-joint** \[クラス\]

      :super   joint 
      :slots         parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target axis 

 

:   

[]() **:calc-inertia-matrix** *mat row column paxis m-til c-til i-til
axis-for-angular world-default-coords translation-axis rotation-axis
tmp-v0 tmp-v1 tmp-v2 tmp-va tmp-vb tmp-vc tmp-vd tmp-m* \[メソッド\]

 

:   

[]() **sphere-joint** \[クラス\]

      :super   joint 
      :slots         parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target axis 

 

:   

[]() **:calc-inertia-matrix** *mat row column paxis m-til c-til i-til
axis-for-angular world-default-coords translation-axis rotation-axis
tmp-v0 tmp-v1 tmp-v2 tmp-va tmp-vb tmp-vc tmp-vd tmp-m* \[メソッド\]

 

:   

[]() **6dof-joint** \[クラス\]

      :super   joint 
      :slots         parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target axis 

 

:   

[]() **:calc-inertia-matrix** *mat row column paxis m-til c-til i-til
axis-for-angular world-default-coords translation-axis rotation-axis
tmp-v0 tmp-v1 tmp-v2 tmp-va tmp-vb tmp-vc tmp-vd tmp-m* \[メソッド\]

 

:   

[]() **bodyset-link** \[クラス\]

      :super   bodyset 
      :slots         rot pos parent descendants worldcoords manager changed geometry::bodies joint parent-link child-links analysis-level default-coords weight acentroid inertia-tensor angular-velocity angular-acceleration spacial-velocity spacial-acceleration momentum-velocity angular-momentum-velocity momentum angular-momentum force moment ext-force ext-moment 

 

:   

[]() **:calc-inertia-matrix-column** *column &rest args &key
(rotation-axis nil) (translation-axis t) ((:inertia-matrix im))
(axis-for-angular (float-vector 0 0 0)) (tmp-v0 (instantiate
float-vector 0)) (tmp-v1 (instantiate float-vector 1)) (tmp-v2
(instantiate float-vector 2)) (tmp-va (instantiate float-vector 3))
(tmp-vb (instantiate float-vector 3)) (tmp-vc (instantiate float-vector
3)) (tmp-vd (instantiate float-vector 3)) (tmp-ma (make-matrix 3 3))
&allow-other-keys* \[メソッド\]

 

:   

[]() **:propagate-mass-properties** *&key (debug-view nil) (tmp-va
(instantiate float-vector 3)) (tmp-vb (instantiate float-vector 3))
(tmp-ma (make-matrix 3 3)) (tmp-mb (make-matrix 3 3)) (tmp-mc
(make-matrix 3 3))* \[メソッド\]

 

:   

[]() **:append-mass-properties** *additional-links &key (update t)
(tmp-va (float-vector 0 0 0)) (tmp-vb (float-vector 0 0 0)) (tmp-ma
(make-matrix 3 3)) (tmp-mb (make-matrix 3 3)) (tmp-mc (make-matrix 3 3))
(tmp-md (make-matrix 3 3)) (additional-weights (send-all
additional-links :weight)) (additional-centroids (send-all
additional-links :centroid)) (additional-inertias (mapcar \#'(lambda (x)
(m(m(send x :worldrot) (send x :inertia-tensor) tmp-ma) (transpose (send
x :worldrot) tmp-mb))) additional-links)) (self-centroid (send self
:centroid))* \[メソッド\]

 

:   

[]() **:append-inertia-no-update** *additional-weights
additional-centroids additional-inertias self-centroid new-centroid &key
(tmp-ma (make-matrix 3 3)) (tmp-mb (make-matrix 3 3)) (tmp-mc
(make-matrix 3 3)) (tmp-md (make-matrix 3 3)) (tmp-va (float-vector 0 0
0)) (len (length additional-weights))* \[メソッド\]

 

:   

[]() **:append-centroid-no-update** *additional-weights
additional-centroids self-centroid new-weight &key (tmp-va (float-vector
0 0 0)) (tmp-vb (float-vector 0 0 0)) (len (length additional-weights))*
\[メソッド\]

 

:   

[]() **:append-weight-no-update** *additional-weights &key (len (length
additional-weights))* \[メソッド\]

 

:   

[]() **cascaded-link** \[クラス\]

      :super   cascaded-coords 
      :slots         rot pos parent descendants worldcoords manager changed links joint-list bodies collision-avoidance-links end-coords-list 

 

:   

[]() **:cog-convergence-check** *centroid-thre target-centroid-pos &key
(centroid-offset-func) (translation-axis :z) (update-mass-properties t)*
\[メソッド\]

 

:   

[]() **:difference-cog-position** *target-centroid-pos &key
(centroid-offset-func) (translation-axis :z) (add-draw-on-param)
(update-mass-properties t)* \[メソッド\]

 

:   

[]() **:calc-vel-for-cog** *cog-gain translation-axis
target-centroid-pos &key (centroid-offset-func) (update-mass-properties
t)* \[メソッド\]

 

:   

[]() **:cog-jacobian-balance-nspace** *link-list &rest args &key
(cog-gain 1.0) (translation-axis :z) (target-centroid-pos)
(centroid-offset-func) (update-mass-properties t) &allow-other-keys*
\[メソッド\]

 

:   

[]() **:calc-cog-jacobian-from-link-list** *&rest args &key (link-list
(send-all joint-list :child-link)) (rotation-axis nil) (translation-axis
t) (axis-dim (send self :calc-target-axis-dimension rotation-axis
translation-axis)) (inertia-matrix (make-matrix axis-dim (send self
:calc-target-joint-dimension link-list))) (update-mass-properties t)
&allow-other-keys* \[メソッド\]

 

:   

[]() **:calc-inertia-matrix-from-link-list** *&rest args &key (link-list
(send-all joint-list :child-link)) (rotation-axis nil) (translation-axis
t) (axis-dim (send self :calc-target-axis-dimension rotation-axis
translation-axis)) (inertia-matrix (make-matrix axis-dim (send self
:calc-target-joint-dimension link-list))) (update-mass-properties t)
(axis-for-angular) (tmp-v0 (instantiate float-vector 0)) (tmp-v1
(instantiate float-vector 1)) (tmp-v2 (instantiate float-vector 2))
(tmp-va (instantiate float-vector 3)) (tmp-vb (instantiate float-vector
3)) (tmp-vc (instantiate float-vector 3)) (tmp-vd (instantiate
float-vector 3)) (tmp-ma (make-matrix 3 3)) (tmp-mb (make-matrix 3 3))
(tmp-mc (make-matrix 3 3)) &allow-other-keys* \[メソッド\]

 

:   

[]() **:update-mass-properties** *&key (tmp-va (instantiate float-vector
3)) (tmp-vb (instantiate float-vector 3)) (tmp-ma (make-matrix 3 3))
(tmp-mb (make-matrix 3 3)) (tmp-mc (make-matrix 3 3))* \[メソッド\]

 

:   

[]() **joint** \[クラス\]

      :super   propertied-object 
      :slots         parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target 

 

:   

[]() **:calc-angular-acceleration-jacobian** *&rest args* \[メソッド\]

 

:   

[]() **:calc-spacial-acceleration-jacobian** *&rest args* \[メソッド\]

 

:   

[]() **:calc-angular-velocity-jacobian** *&rest args* \[メソッド\]

 

:   

[]() **:calc-spacial-velocity-jacobian** *&rest args* \[メソッド\]

 

:   

[]() **rotational-joint** \[クラス\]

      :super   joint 
      :slots         parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target axis 

 

:   

[]() **:calc-angular-acceleration-jacobian** *avj tmp-va* \[メソッド\]

 

:   

[]() **:calc-spacial-acceleration-jacobian** *svj avj tmp-va tmp-vb*
\[メソッド\]

 

:   

[]() **:calc-angular-velocity-jacobian** *ax tmp-va* \[メソッド\]

 

:   

[]() **:calc-spacial-velocity-jacobian** *ax tmp-va tmp-vb* \[メソッド\]

 

:   

[]() **linear-joint** \[クラス\]

      :super   joint 
      :slots         parent-link child-link joint-angle min-angle max-angle default-coords joint-velocity joint-acceleration joint-torque max-joint-velocity max-joint-torque joint-min-max-table joint-min-max-target axis 

 

:   

[]() **:calc-angular-acceleration-jacobian** *avj tmp-va* \[メソッド\]

 

:   

[]() **:calc-spacial-acceleration-jacobian** *svj avj tmp-va tmp-vb*
\[メソッド\]

 

:   

[]() **:calc-angular-velocity-jacobian** *ax tmp-va* \[メソッド\]

 

:   

[]() **:calc-spacial-velocity-jacobian** *ax tmp-va tmp-vb* \[メソッド\]

 

:   

[]() **bodyset-link** \[クラス\]

      :super   bodyset 
      :slots         rot pos parent descendants worldcoords manager changed geometry::bodies joint parent-link child-links analysis-level default-coords weight acentroid inertia-tensor angular-velocity angular-acceleration spacial-velocity spacial-acceleration momentum-velocity angular-momentum-velocity momentum angular-momentum force moment ext-force ext-moment 

 

:   

[]() **:inverse-dynamics** *&key (debug-view nil) (tmp-va (float-vector
0 0 0)) (tmp-vb (float-vector 0 0 0)) (tmp-vc (float-vector 0 0 0))
(tmp-ma (make-matrix 3 3)) (tmp-mb (make-matrix 3 3)) (tmp-mc
(make-matrix 3 3)) (tmp-md (make-matrix 3 3))* \[メソッド\]

 

:   

[]() **:forward-all-kinematics** *&key (debug-view nil) (tmp-va
(float-vector 0 0 0))* \[メソッド\]

 

:   

[]() **:ext-moment** *&optional m* \[メソッド\]

 

:   

[]() **:ext-force** *&optional f* \[メソッド\]

 

:   

[]() **:moment** \[メソッド\]

 

:   

[]() **:force** \[メソッド\]

 

:   

[]() **:spacial-acceleration** *&optional sa* \[メソッド\]

 

:   

[]() **:spacial-velocity** *&optional aa* \[メソッド\]

 

:   

[]() **:angular-acceleration** *&optional aa* \[メソッド\]

 

:   

[]() **:angular-velocity** *&optional aa* \[メソッド\]

 

:   

[]() **:reset-dynamics** \[メソッド\]

 

:   

[]() **cascaded-link** \[クラス\]

      :super   cascaded-coords 
      :slots         rot pos parent descendants worldcoords manager changed links joint-list bodies collision-avoidance-links end-coords-list 

 

:   

[]() **:inertia-tensor** *&optional (update-mass-properties t)*
\[メソッド\]

 
:   Calculate total robot inertia tensor \[g <span class="MATH">**![\$
    mm\^2\$](jmanual-img216.png){width="552" height="72"}**</span>\]
    around total robot centroid in euslisp world coordinates. If
    update-mass-properties argument is t, propagate total mass prop
    calculation for all links and returns total robot inertia tensor.
    Otherwise, do not calculate total mass prop, just returns
    pre-computed total robot inertia tensor.

[]() **:centroid** *&optional (update-mass-properties t)* \[メソッド\]

 
:   Calculate total robot centroid (Center Of Gravity, COG) \[mm\] in
    euslisp world coordinates. If update-mass-properties argument is t,
    propagate total mass prop calculation for all links and returns
    total robot centroid. Otherwise, do not calculate total mass prop,
    just returns pre-computed total robot centroid.

[]() **:weight** *&optional (update-mass-properties t)* \[メソッド\]

 
:   Calculate total robot weight \[g\]. If update-mass-properties
    argument is t, propagate total mass prop calculation for all links
    and returns total robot weight. Otherwise, do not calculate total
    weight, just returns pre-computed total robot weight.

[]() ![\\begin{emtabbing} {\\bf :calc-zmp} \\it\\&optional (av (send
self :angle-vector)) ... ...lc-torque-buffer-args (send self
:calc-torque-buffer-args)) \\rm
\\end{emtabbing}](jmanual-img217.png){width="42" height="20"}

 
:   Calculate Zero Moment Point based on Inverse Dynamics. The output is
    expressed by the world coordinates, and depends on historical robot
    states of the past 3 steps. Step is incremented when this method is
    called. After solving Inverse Dynamics, ZMP is calculated from total
    root-link force and moment. necessary arguments -&gt;av and
    root-coords. If update is t, call inverse dynamics, otherwise, just
    return zmp from total root-link force and moment. dt \[s\] is time
    step used only when update is t. pZMPz is ZMP height \[mm\]. After
    this method, (send robot :get :zmp) is ZMP and (send robot
    :get :zmp-moment) is moment around ZMP.

[]() **:preview-control-dynamics-filter** *dt avs &key
(preview-controller-class preview-controller) (cog-method
:move-base-pos) (delay 0.8)* \[メソッド\]

 

:   

[]() **:draw-torque** *vwer &key flush (width 2) (size 100) (color
(float-vector 1 0.3 0)) (warning-color (float-vector 1 0 0))
(torque-threshold nil) (torque-vector (send self :torque-vector))
((:joint-list jlist) (send self :joint-list))* \[メソッド\]

 

:   

[]() **:calc-contact-wrenches-from-total-wrench** *target-pos-list &key
(total-wrench) (weight (fill (instantiate float-vector (6 (length
target-pos-list))) 1))* \[メソッド\]

 

:   

[]() **:wrench-list-&gt;wrench-vector** *wrench-list* \[メソッド\]

 

:   

[]() **:wrench-vector-&gt;wrench-list** *wrench-vector* \[メソッド\]

 

:   

[]() **:calc-cop-from-force-moment** *force moment sensor-coords
cop-coords &key (fz-thre 1) (return-all-values)* \[メソッド\]

 

:   

[]() **:calc-torque-from-ext-wrenches** *&key (force-list) (moment-list)
(target-coords) ((:jacobi tmp-jacobi))* \[メソッド\]

 

:   

[]() **:calc-av-vel-acc-from-pos** *dt av* \[メソッド\]

 

:   

[]() **:calc-root-coords-vel-acc-from-pos** *dt root-coords*
\[メソッド\]

 

:   

[]() **:calc-torque-from-vel-acc** *&key (debug-view nil) (jvv
(instantiate float-vector (length joint-list))) (jav (instantiate
float-vector (length joint-list))) (root-spacial-velocity (float-vector
0 0 0)) (root-angular-velocity (float-vector 0 0 0))
(root-spacial-acceleration (float-vector 0 0 0))
(root-angular-acceleration (float-vector 0 0 0))
(calc-torque-buffer-args (send self :calc-torque-buffer-args))*
\[メソッド\]

 

:   

[]() **:calc-torque-without-ext-wrench** *&key (debug-view nil)
(calc-statics-p t) (dt 0.005) (av (send self :angle-vector))
(root-coords (send (car (send self :links)) :copy-worldcoords))
(calc-torque-buffer-args (send self :calc-torque-buffer-args))*
\[メソッド\]

 

:   

[]() **:calc-torque** *&key (debug-view nil) (calc-statics-p t) (dt
0.005) (av (send self :angle-vector)) (root-coords (send (car (send self
:links)) :copy-worldcoords)) (force-list) (moment-list) (target-coords)
(calc-torque-buffer-args (send self :calc-torque-buffer-args))*
\[メソッド\]

 

:   

[]() **:calc-torque-buffer-args** \[メソッド\]

 

:   

[]() **:torque-ratio-vector** *&rest args &key (torque (sendself
:torque-vector args))* \[メソッド\]

 

:   

[]() **:max-torque-vector** \[メソッド\]

 

:   

[]() **riccati-equation** \[クラス\]

      :super   propertied-object 
      :slots         a b c p q r k a-bkt r+btpb-1 

 

:   

[]() **:init** *aa bb cc qq rr* \[メソッド\]

 

:   

[]() **:solve** \[メソッド\]

 

:   

[]() **:solve** \[メソッド\]

 

:   

[]() **:init** *aa bb cc qq rr* \[メソッド\]

 

:   

[]() **preview-controller** \[クラス\]

      :super   riccati-equation 
      :slots         xk uk delay f1-n y1-n queue-index initialize-queue-p additional-data-queue finishedp initialized-p output-dim input-dim 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it dt \\&key \\= (q) \\\\lq
\[method\]\\\\ \\&gt; (r) \\\\ \\&gt; ... ...ray-dimension \\\_b 1) 1))
\\\\ \\&gt; ((:initialize-queue-p iqp)) \\rm
\\end{emtabbing}](jmanual-img218.png){width="1242" height="92"}

 
:   Initialize preview-controller. Q is weighting of output error and R
    is weighting of input. dt is sampling time \[s\]. delay is preview
    time \[s\]. init-xk is initial state value. A, B, C are state eq
    matrices. If initialize-queue-p is t, fill all queue by the first
    input at the begenning, otherwise, do not fill queue at the first.

[]() **:update-xk** *p &optional (add-data)* \[メソッド\]

 
:   Update xk by inputting reference output. Return value :
    nil (initializing) =&gt;return values (middle) =&gt;nil (finished)
    If p is nil, automatically the last value in queue is used as input
    and preview controller starts finishing.

[]() **:finishedp** \[メソッド\]

 
:   Finished or not.

[]() **:last-reference-output-vector** \[メソッド\]

 
:   Last value of reference output queue vector (y\_k+N\_ref). Last
    value is latest future value.

[]() **:current-reference-output-vector** \[メソッド\]

 
:   First value of reference output queue vector (y\_k\_ref). First
    value is oldest future value and it can be used as current
    reference value.

[]() **:current-state-vector** \[メソッド\]

 
:   Current state value (xk).

[]() **:current-output-vector** \[メソッド\]

 
:   Current output value (yk).

[]() **:current-additional-data** \[メソッド\]

 
:   Current additional data value. First value of additional-data-queue.

[]() **:pass-preview-controller** *reference-output-vector-list*
\[メソッド\]

 
:   Get preview controller results from reference-output-vector-list and
    returns list.

[]() **:calc-f** \[メソッド\]

 

:   

[]() **:calc-u** \[メソッド\]

 

:   

[]() **:calc-xk** \[メソッド\]

 

:   

[]() **:pass-preview-controller** *reference-output-vector-list*
\[メソッド\]

 
:   Get preview controller results from reference-output-vector-list and
    returns list.

[]() **:current-additional-data** \[メソッド\]

 
:   Current additional data value. First value of additional-data-queue.

[]() **:current-output-vector** \[メソッド\]

 
:   Current output value (yk).

[]() **:current-state-vector** \[メソッド\]

 
:   Current state value (xk).

[]() **:current-reference-output-vector** \[メソッド\]

 
:   First value of reference output queue vector (y\_k\_ref). First
    value is oldest future value and it can be used as current
    reference value.

[]() **:last-reference-output-vector** \[メソッド\]

 
:   Last value of reference output queue vector (y\_k+N\_ref). Last
    value is latest future value.

[]() **:finishedp** \[メソッド\]

 
:   Finished or not.

[]() **:update-xk** *p &optional (add-data)* \[メソッド\]

 
:   Update xk by inputting reference output. Return value :
    nil (initializing) =&gt;return values (middle) =&gt;nil (finished)
    If p is nil, automatically the last value in queue is used as input
    and preview controller starts finishing.

[]() ![\\begin{emtabbing} {\\bf :init} \\it dt \\&key \\= (q) \\\\lq
\[method\]\\\\ \\&gt; (r) \\\\ \\&gt; ... ...ray-dimension \\\_b 1) 1))
\\\\ \\&gt; ((:initialize-queue-p iqp)) \\rm
\\end{emtabbing}](jmanual-img218.png){width="1242" height="92"}

 
:   Initialize preview-controller. Q is weighting of output error and R
    is weighting of input. dt is sampling time \[s\]. delay is preview
    time \[s\]. init-xk is initial state value. A, B, C are state eq
    matrices. If initialize-queue-p is t, fill all queue by the first
    input at the begenning, otherwise, do not fill queue at the first.

[]() **:calc-xk** \[メソッド\]

 

:   

[]() **:calc-u** \[メソッド\]

 

:   

[]() **:calc-f** \[メソッド\]

 

:   

[]() **extended-preview-controller** \[クラス\]

      :super   preview-controller 
      :slots         orga orgb orgc xk

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it dt \\&key \\= (q) \\\\lq
\[method\]\\\\ \\&gt; (r) \\\\ \\&gt; ... ...\_orga 0)) \\\\ \\&gt;
((:initialize-queue-p iqp)) \\\\ \\&gt; (q-mat) \\rm
\\end{emtabbing}](jmanual-img219.png){width="552" height="226"}

 
:   Initialize preview-controller in extended system (error system). Q
    is weighting of output error and R is weighting of input. dt is
    sampling time \[s\]. delay is preview time \[s\]. init-xk is initial
    state value. A, B, C are state eq matrices for original system and
    slot variables A,B,C are used for error system matrices. If
    initialize-queue-p is t, fill all queue by the first input at the
    begenning, otherwise, do not fill queue at the first.

[]() **:current-output-vector** \[メソッド\]

 
:   Current additional data value. First value of additional-data-queue.

[]() **:calc-f** \[メソッド\]

 

:   

[]() **:calc-u** \[メソッド\]

 

:   

[]() **:calc-xk** \[メソッド\]

 

:   

[]() **:current-output-vector** \[メソッド\]

 
:   Current additional data value. First value of additional-data-queue.

[]() ![\\begin{emtabbing} {\\bf :init} \\it dt \\&key \\= (q) \\\\lq
\[method\]\\\\ \\&gt; (r) \\\\ \\&gt; ... ...\_orga 0)) \\\\ \\&gt;
((:initialize-queue-p iqp)) \\\\ \\&gt; (q-mat) \\rm
\\end{emtabbing}](jmanual-img219.png){width="552" height="226"}

 
:   Initialize preview-controller in extended system (error system). Q
    is weighting of output error and R is weighting of input. dt is
    sampling time \[s\]. delay is preview time \[s\]. init-xk is initial
    state value. A, B, C are state eq matrices for original system and
    slot variables A,B,C are used for error system matrices. If
    initialize-queue-p is t, fill all queue by the first input at the
    begenning, otherwise, do not fill queue at the first.

[]() **:calc-xk** \[メソッド\]

 

:   

[]() **:calc-u** \[メソッド\]

 

:   

[]() **:calc-f** \[メソッド\]

 

:   

[]() **preview-control-cart-table-cog-trajectory-generator** \[クラス\]

      :super   propertied-object 
      :slots         pcs cog-z zmp-z 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it dt \\\_zc \\&key \\= (q 1)
\\\\lq \[method\]\\\\ \\&gt; (r 1... ... \\&gt;
(preview-controller-class extended-preview-controller) \\rm
\\end{emtabbing}](jmanual-img220.png){width="552" height="207"}

 
:   COG (xy) trajectory generator using preview-control convert
    reference ZMP from reference COG. dt -&gt;sampling time\[s\], \_zc
    is height of COG \[mm\]. preview-controller-class is preview
    controller class (extended-preview-controller by default). For other
    arguments, please see preview-controller and
    extended-preview-controller :init documentation.

[]() **:refcog** \[メソッド\]

 
:   Reference COG \[mm\].

[]() **:cart-zmp** \[メソッド\]

 
:   Cart-table system ZMP\[mm\] as an output variable.

[]() **:last-refzmp** \[メソッド\]

 
:   Reference zmp at the last of queue.

[]() **:current-refzmp** \[メソッド\]

 
:   Current reference zmp at the first of queue.

[]() **:update-xk** *p &optional (add-data)* \[メソッド\]

 
:   Update xk and returns zmp and cog values. For arguments, please see
    preview-controller and extended-preview-controller :update-xk.

[]() **:finishedp** \[メソッド\]

 
:   Finished or not.

[]() **:current-additional-data** \[メソッド\]

 
:   Current additional data value.

[]() **:pass-preview-controller** *reference-output-vector-list*
\[メソッド\]

 
:   Get preview controller results from reference-output-vector-list and
    returns list.

[]() **:cog-z** \[メソッド\]

 
:   COG Z \[mm\].

[]() **:update-cog-z** *zc* \[メソッド\]

 

:   

[]() **:cog-z** \[メソッド\]

 
:   COG Z \[mm\].

[]() **:pass-preview-controller** *reference-output-vector-list*
\[メソッド\]

 
:   Get preview controller results from reference-output-vector-list and
    returns list.

[]() **:current-additional-data** \[メソッド\]

 
:   Current additional data value.

[]() **:finishedp** \[メソッド\]

 
:   Finished or not.

[]() **:update-xk** *p &optional (add-data)* \[メソッド\]

 
:   Update xk and returns zmp and cog values. For arguments, please see
    preview-controller and extended-preview-controller :update-xk.

[]() **:current-refzmp** \[メソッド\]

 
:   Current reference zmp at the first of queue.

[]() **:last-refzmp** \[メソッド\]

 
:   Reference zmp at the last of queue.

[]() **:cart-zmp** \[メソッド\]

 
:   Cart-table system ZMP\[mm\] as an output variable.

[]() **:refcog** \[メソッド\]

 
:   Reference COG \[mm\].

[]() ![\\begin{emtabbing} {\\bf :init} \\it dt \\\_zc \\&key \\= (q 1)
\\\\lq \[method\]\\\\ \\&gt; (r 1... ... \\&gt;
(preview-controller-class extended-preview-controller) \\rm
\\end{emtabbing}](jmanual-img220.png){width="552" height="207"}

 
:   COG (xy) trajectory generator using preview-control convert
    reference ZMP from reference COG. dt -&gt;sampling time\[s\], \_zc
    is height of COG \[mm\]. preview-controller-class is preview
    controller class (extended-preview-controller by default). For other
    arguments, please see preview-controller and
    extended-preview-controller :init documentation.

[]() **:update-cog-z** *zc* \[メソッド\]

 

:   

[]() **gait-generator** \[クラス\]

      :super   propertied-object 
      :slots         robot dt footstep-node-list support-leg-list support-leg-coords-list swing-leg-dst-coords-list swing-leg-src-coords refzmp-cur-list refzmp-next refzmp-prev step-height-list one-step-len index-count default-step-height default-double-support-ratio default-zmp-offsets finalize-p apreview-controller all-limbs end-with-double-support ik-thre ik-rthre swing-leg-proj-ratio-interpolation-acc swing-leg-proj-ratio-interpolation-vel swing-leg-proj-ratio-interpolation-pos swing-rot-ratio-interpolation-acc swing-rot-ratio-interpolation-vel swing-rot-ratio-interpolation-pos 

 

:   

[]() **:init** *rb \_dt* \[メソッド\]

 

:   

[]() **:get-footstep-limbs** *fs* \[メソッド\]

 

:   

[]() **:get-counter-footstep-limbs** *fs* \[メソッド\]

 

:   

[]() **:get-limbs-zmp-list** *limb-coords limb-names* \[メソッド\]

 

:   

[]() **:get-limbs-zmp** *limb-coords limb-names* \[メソッド\]

 

:   

[]() **:get-swing-limbs** *limbs* \[メソッド\]

 

:   

[]() **:initialize-gait-parameter** *fsl time cog &key
((:default-step-height dsh) 50) ((:default-double-support-ratio ddsr)
0.2) (delay 1.6) ((:all-limbs al) '(:rleg :lleg)) ((:default-zmp-offsets
dzo) (mapcan \#'(lambda (x) (list x (float-vector 0 0 0))) al)) (q 1.0)
(r 1.000000e-06) (thre 1) (rthre (deg2rad 1)) (start-with-double-support
t) ((:end-with-double-support ewds) t)* \[メソッド\]

 

:   

[]() **:finalize-gait-parameter** \[メソッド\]

 

:   

[]() **:make-gait-parameter** \[メソッド\]

 

:   

[]() **:calc-hoffarbib-pos-vel-acc** *tmp-remain-time tmp-goal old-acc
old-vel old-pos* \[メソッド\]

 

:   

[]() **:calc-current-swing-leg-coords** *ratio src dst &key (type
:shuffling) (step-height default-step-height)* \[メソッド\]

 

:   

[]() **:calc-ratio-from-double-support-ratio** \[メソッド\]

 

:   

[]() **:calc-current-refzmp** *prev cur next* \[メソッド\]

 

:   

[]() **:calc-one-tick-gait-parameter** *type* \[メソッド\]

 

:   

[]() **:proc-one-tick** *&key (type :shuffling) (solve-angle-vector
:solve-av-by-move-centroid-on-foot) (solve-angle-vector-args) (debug
nil)* \[メソッド\]

 

:   

[]() **:update-current-gait-parameter** \[メソッド\]

 

:   

[]() **:solve-angle-vector** *support-leg support-leg-coords
swing-leg-coords cog &key (solve-angle-vector
:solve-av-by-move-centroid-on-foot) (solve-angle-vector-args)*
\[メソッド\]

 

:   

[]() **:solve-av-by-move-centroid-on-foot** *support-leg
support-leg-coords swing-leg-coords cog robot &rest args &key (cog-gain
3.5) (stop 100) (additional-nspace-list) &allow-other-keys* \[メソッド\]

 

:   

[]() **:cycloid-midpoint** *ratio start goal height &key (top-ratio
0.5)* \[メソッド\]

 

:   

[]() **:cycloid-midcoords** *ratio start goal height &key (top-ratio
0.5) (rot-ratio ratio)* \[メソッド\]

 

:   

[]() **:cycloid-midcoords** *ratio start goal height &key (top-ratio
0.5) (rot-ratio ratio)* \[メソッド\]

 

:   

[]() **:cycloid-midpoint** *ratio start goal height &key (top-ratio
0.5)* \[メソッド\]

 

:   

[]() **:solve-av-by-move-centroid-on-foot** *support-leg
support-leg-coords swing-leg-coords cog robot &rest args &key (cog-gain
3.5) (stop 100) (additional-nspace-list) &allow-other-keys* \[メソッド\]

 

:   

[]() **:solve-angle-vector** *support-leg support-leg-coords
swing-leg-coords cog &key (solve-angle-vector
:solve-av-by-move-centroid-on-foot) (solve-angle-vector-args)*
\[メソッド\]

 

:   

[]() **:update-current-gait-parameter** \[メソッド\]

 

:   

[]() **:proc-one-tick** *&key (type :shuffling) (solve-angle-vector
:solve-av-by-move-centroid-on-foot) (solve-angle-vector-args) (debug
nil)* \[メソッド\]

 

:   

[]() **:calc-one-tick-gait-parameter** *type* \[メソッド\]

 

:   

[]() **:calc-current-refzmp** *prev cur next* \[メソッド\]

 

:   

[]() **:calc-ratio-from-double-support-ratio** \[メソッド\]

 

:   

[]() **:calc-current-swing-leg-coords** *ratio src dst &key (type
:shuffling) (step-height default-step-height)* \[メソッド\]

 

:   

[]() **:calc-hoffarbib-pos-vel-acc** *tmp-remain-time tmp-goal old-acc
old-vel old-pos* \[メソッド\]

 

:   

[]() **:make-gait-parameter** \[メソッド\]

 

:   

[]() **:finalize-gait-parameter** \[メソッド\]

 

:   

[]() **:initialize-gait-parameter** *fsl time cog &key
((:default-step-height dsh) 50) ((:default-double-support-ratio ddsr)
0.2) (delay 1.6) ((:all-limbs al) '(:rleg :lleg)) ((:default-zmp-offsets
dzo) (mapcan \#'(lambda (x) (list x (float-vector 0 0 0))) al)) (q 1.0)
(r 1.000000e-06) (thre 1) (rthre (deg2rad 1)) (start-with-double-support
t) ((:end-with-double-support ewds) t)* \[メソッド\]

 

:   

[]() **:get-swing-limbs** *limbs* \[メソッド\]

 

:   

[]() **:get-limbs-zmp** *limb-coords limb-names* \[メソッド\]

 

:   

[]() **:get-limbs-zmp-list** *limb-coords limb-names* \[メソッド\]

 

:   

[]() **:get-counter-footstep-limbs** *fs* \[メソッド\]

 

:   

[]() **:get-footstep-limbs** *fs* \[メソッド\]

 

:   

[]() **:init** *rb \_dt* \[メソッド\]

 

:   

[]() **calc-inertia-matrix-rotational** *mat row column paxis m-til
c-til i-til axis-for-angular child-link world-default-coords
translation-axis rotation-axis tmp-v0 tmp-v1 tmp-v2 tmp-va tmp-vb tmp-vc
tmp-vd tmp-m* \[関数\]

 

:   

[]() **calc-inertia-matrix-linear** *mat row column paxis m-til c-til
i-til axis-for-angular child-link world-default-coords translation-axis
rotation-axis tmp-v0 tmp-v1 tmp-v2 tmp-va tmp-vb tmp-vc tmp-vd tmp-m*
\[関数\]

 

:   

[ロボットビューワ]()
====================

\
[]() **viewer** \[クラス\]

      :super   propertied-object 
      :slots         geometry::eye geometry::port geometry::surface 

 

:   

[]() **:draw-objects** *&rest args* \[メソッド\]

 

:   

[]() **:draw-circle** *x::c &key (x::radius 50) (x:flush nil) (x::arrow
nil) (x::arc 2pi) (x::arrow-scale \#f(1.0 1.0))* \[メソッド\]

 

:   

[]() **x::irtviewer** \[クラス\]

      :super   x:panel 
      :slots         x::viewer x::objects x::draw-things x::previous-cursor-pos x::left-right-angle x::up-down-angle x::viewpoint x::viewtarget x::drawmode x::draw-origin x::draw-floor 

 

:   

[]() **:create** *&rest args &key (x::title IRT viewer) (x::view-name
(gensym title)) (x::hither 200.0) (x::yon 50000.0) (x::width 500)
(x::height 500) ((:draw-origin do) 150) ((:draw-floor x::df) nil)
&allow-other-keys* \[メソッド\]

 

:   

[]() **:viewer** *&rest args* \[メソッド\]

 

:   

[]() **:redraw** \[メソッド\]

 

:   

[]() **:expose** *x:event* \[メソッド\]

 

:   

[]() **:resize** *x::newwidth x::newheight* \[メソッド\]

 

:   

[]() **:configurenotify** *x:event* \[メソッド\]

 

:   

[]() **:viewtarget** *&optional x::p* \[メソッド\]

 

:   

[]() **:viewpoint** *&optional x::p* \[メソッド\]

 

:   

[]() **:look1** *&optional (x::vt x::viewtarget) (x::lra
x::left-right-angle) (x::uda x::up-down-angle)* \[メソッド\]

 

:   

[]() **:look-all** *&optional x::bbox* \[メソッド\]

 

:   

[]() **:move-viewing-around-viewtarget** *x:event x::x x::y x::dx x::dy
x::vwr* \[メソッド\]

 

:   

[]() **:set-cursor-pos-event** *x:event* \[メソッド\]

 

:   

[]() **:move-coords-event** *x:event* \[メソッド\]

 

:   

[]() **:draw-event** *x:event* \[メソッド\]

 

:   

[]() **:draw-objects** *&rest args* \[メソッド\]

 

:   

[]() **:objects** *&rest args* \[メソッド\]

 

:   

[]() **:select-drawmode** *x::mode* \[メソッド\]

 

:   

[]() **:flush** \[メソッド\]

 

:   

[]() **:change-background** *x::col* \[メソッド\]

 

:   

[]() **:change-background** *x::col* \[メソッド\]

 

:   

[]() **:flush** \[メソッド\]

 

:   

[]() **:select-drawmode** *x::mode* \[メソッド\]

 

:   

[]() **:objects** *&rest args* \[メソッド\]

 

:   

[]() **:draw-objects** *&rest args* \[メソッド\]

 

:   

[]() **:draw-event** *x:event* \[メソッド\]

 

:   

[]() **:move-coords-event** *x:event* \[メソッド\]

 

:   

[]() **:set-cursor-pos-event** *x:event* \[メソッド\]

 

:   

[]() **:move-viewing-around-viewtarget** *x:event x::x x::y x::dx x::dy
x::vwr* \[メソッド\]

 

:   

[]() **:look-all** *&optional x::bbox* \[メソッド\]

 

:   

[]() **:look1** *&optional (x::vt x::viewtarget) (x::lra
x::left-right-angle) (x::uda x::up-down-angle)* \[メソッド\]

 

:   

[]() **:viewpoint** *&optional x::p* \[メソッド\]

 

:   

[]() **:viewtarget** *&optional x::p* \[メソッド\]

 

:   

[]() **:configurenotify** *x:event* \[メソッド\]

 

:   

[]() **:resize** *x::newwidth x::newheight* \[メソッド\]

 

:   

[]() **:expose** *x:event* \[メソッド\]

 

:   

[]() **:redraw** \[メソッド\]

 

:   

[]() **:viewer** *&rest args* \[メソッド\]

 

:   

[]() **:create** *&rest args &key (x::title IRT viewer) (x::view-name
(gensym title)) (x::hither 200.0) (x::yon 50000.0) (x::width 500)
(x::height 500) ((:draw-origin do) 150) ((:draw-floor x::df) nil)
&allow-other-keys* \[メソッド\]

 

:   

[]() **viewing** \[クラス\]

      :super   cascaded-coords 
      :slots         rot pos parent descendants worldcoords manager changed geometry::viewcoords 

 

:   

[]() **:look** *geometry::from &optional (geometry::to (float-vector 0 0
0)) (geometry::view-up (float-vector 0 0 1))* \[メソッド\]

 

:   

[]() **viewer-dummy** \[クラス\]

      :super   propertied-object 
      :slots         nil 

 

:   

[]() **:nomethod** *&rest args* \[メソッド\]

 

:   

[]() **:nomethod** *&rest args* \[メソッド\]

 

:   

[]() **irtviewer-dummy** \[クラス\]

      :super   propertied-object 
      :slots         objects draw-things 

 

:   

[]() **:objects** *&rest args* \[メソッド\]

 

:   

[]() **:nomethod** *&rest args* \[メソッド\]

 

:   

[]() **:nomethod** *&rest args* \[メソッド\]

 

:   

[]() **:objects** *&rest args* \[メソッド\]

 

:   

[]() **make-irtviewer** *&rest args* \[関数\]

 
:   Create irtviewer :view-name title :hither near cropping plane :yon
    far cropping plane :width width of the window :height height of the
    window :draw-origin size of origin arrow, use nil to disable it
    :draw-floor use t to view floor

[]() **x::make-lr-ud-coords** *x::lra x::uda* \[関数\]

 

:   

[]() **x::draw-things** *x::objs* \[関数\]

 

:   

[]() **objects** *&optional (objs t) vw* \[関数\]

 

:   

[]() **make-irtviewer-dummy** *&rest args* \[関数\]

 

:   

[干渉計算]()
============

干渉計算には２組の幾何モデルが交差するかを判定する物である．
irteusではノースカロライナ大学のLin氏らのグループにより開発されたPQPを
他言語インターフェースを介して利用できるようにしてある．
(他の干渉計算ソフトウェアパッケージについてはhttp://gamma.cs.unc.edu/research/collision/に詳しい．)
PQPは (1)２つのモデルが交差するかを判定する衝突検出，
(2)２つのモデル間の最初距離を算出する距離計算，
(3)２つのモデルがある距離以下であるかを判定する近接検証，
等の３つ機能を提供する．

PQPソフトウェアパッケージの使い方はirteus/PQP/README.txtに
書いてあり，irteus/PQP/src/PQP.hを読むことで理解できるようになって
いる．

[irteusからPQPの呼び出し]()
---------------------------

irteusでPQPを使うためのファイルは CPQP.C, euspqp.c, pqp.l からなる．
２つの幾何モデルが衝突してしるか否かを判定するためには，

    (defun pqp-collision-check (model1 model2
                           &optional (flag PQP_FIRST_CONTACT) &key (fat 0) (fat2 nil))
      (let ((m1 (get model1 :pqpmodel))  (m2 (get model2 :pqpmodel))
            (r1 (send model1 :worldrot)) (t1 (send model1 :worldpos))
            (r2 (send model2 :worldrot)) (t2 (send model2 :worldpos)))
        (if (null fat2) (setq fat2 fat))
        (if (null m1) (setq m1 (send model1 :make-pqpmodel :fat fat)))
        (if (null m2) (setq m2 (send model2 :make-pqpmodel :fat fat2)))
        (pqpcollide r1 t1 m1 r2 t2 m2 flag)))

を呼び出せば良い．
r1,r1,r2,t1はそれぞれの物体の並進ベクトル，回転行列となり， (get model1
:pqpmodel)でPQPの幾何モデルへのポインタを参照する．
このポインタは:make-pqpmodelメソッドの中で以下のよう計算される．

    (defmethod cascaded-coords
      (:make-pqpmodel
       (&key (fat 0))
       (let ((m (pqpmakemodel))
             vs v1 v2 v3 (id 0))
         (setf (get self :pqpmodel) m)
         (pqpbeginmodel m)
         (dolist (f (send self :faces))
           (dolist (poly (face-to-triangle-aux f))
             (setq vs (send poly :vertices)
                   v1 (send self :inverse-transform-vector (first vs))
                   v2 (send self :inverse-transform-vector (second vs))
                   v3 (send self :inverse-transform-vector (third vs)))
             (when (not (= fat 0))
               (setq v1 (v+ v1 (scale fat (normalize-vector v1)))
                     v2 (v+ v2 (scale fat (normalize-vector v2)))
                     v3 (v+ v3 (scale fat (normalize-vector v3)))))
             (pqpaddtri m v1 v2 v3 id)
             (incf id)))
         (pqpendmodel m)
         m)))

ここでは，まず(pqpmakemodel)が呼び出されている．
pqpmakemodelの中では，euqpqp.cで定義されている，

    pointer PQPMAKEMODEL(register context *ctx, int n, register pointer *argv)
    {
        int addr = PQP_MakeModel();
        return makeint(addr);
    }

が呼び出されており，これは，CPQP.Cの

    PQP\_Model *PQP_MakeModel()
    {
        return new PQP_Model();
    }

が呼ばれている．PQP\_Model()はPQP.hで定義されているものであり，
この様にしてeuslisp内の関数が実際のPQPライブラリの関数に渡されてい
る以降，(pqpbeginmodel m)でPQPの幾何モデルのインスタンスを作成し，
(pqpaddtri m v1 v2 v3 id)として面情報を登録している．

### [物体形状モデル同士の干渉計算例]()

pqp-collision-checkやpqp-collision-distanceを利用した例を示す．

    ;; Make models
    (setq *b0* (make-cube 100 100 100))
    (setq *b1* (make-cube 100 100 100))

    ;; Case 1 : no collision
    (send *b0* :newcoords (make-coords :pos #f(100 100 -100)
                                       :rpy (list (deg2rad 10) (deg2rad -20) (deg2rad 30))))
    (objects (list *b0* *b1*))
    (print (pqp-collision-check *b0* *b1*)) ;; Check collision
    (let ((ret (pqp-collision-distance *b0* *b1*))) ;; Check distance and nearest points
      (print (car ret)) ;; distance
      (send (cadr ret) :draw-on :flush nil :size 20 :color #f(1 0 0)) ;; nearest point on *b0*
      (send (caddr ret) :draw-on :flush nil :size 20 :color #f(1 0 0)) ;; nearest point on *b1*
      (send *irtviewer* :viewer :draw-line (cadr ret) (caddr ret))
      (send *irtviewer* :viewer :viewsurface :flush))

    ;; Case 2 : collision
    (send *b0* :newcoords (make-coords :pos #f(50 50 -50)
                                       :rpy (list (deg2rad 10) (deg2rad -20) (deg2rad 30))))
    (objects (list *b0* *b1*))
    (print (pqp-collision-check *b0* *b1*)) ;; Check collision
    (let ((ret (pqp-collision-distance *b0* *b1*))) ;; Check distance and nearest points
      (print (car ret)) ;; distance
      ;; In case of collision, nearest points are insignificant values.
      (send (cadr ret) :draw-on :flush nil :size 20 :color #f(1 0 0)) ;; nearest point on *b0*
      (send (caddr ret) :draw-on :flush nil :size 20 :color #f(1 0 0)) ;; nearest point on *b1*
      (send *irtviewer* :viewer :draw-line (cadr ret) (caddr ret))
      (send *irtviewer* :viewer :viewsurface :flush))

[ロボット動作と干渉計算]()
--------------------------

ハンドで物体をつかむ，という動作の静的なシミュレーションを行う場合に
手（指）のリンクと対象物体の干渉を調べ，これが起こるところで物体をつか
む動作を停止させるということが出来る．

    (objects (list *sarm* *target*))

    (send *sarm* :solve-ik *target* :debug-view t)
    (while (> a 0)
      (if (pqp-collision-check-objects
           (list (send *sarm* :joint-fr :child-link)
                 (send *sarm* :joint-fl :child-link))
           (list *target*))
          (return))
      (decf a 0.1)
      (send *irtviewer* :draw-objects)
      (send *sarm* :move-fingers a))
    (send *sarm* :end-coords :assoc *target*)

    (dotimes (i 100)
      (send *sarm* :joint0 :joint-angle 1 :relative t)
      (send *irtviewer* :draw-objects))
    (send *sarm* :end-coords :dissoc *target*)
    (dotimes (i 100)
      (send *sarm* :joint0 :joint-angle -1 :relative t)
      (send *irtviewer* :draw-objects))

同様の機能が，"irteus/demo/sample-arm-model.l"ファイルの:open-hand,
:close-handというメソッドで提供されている．

\
[]() **cascaded-coords** \[クラス\]

      :super   coordinates 
      :slots         rot pos parent descendants worldcoords manager changed 

 

:   

[]() **:make-pqpmodel** *&key (geometry::fat 0) ((:faces geometry::fs)
(send self :faces))* \[メソッド\]

 

:   

[]() ![\\begin{emtabbing} {\\bf pqp-collision-check} \\it
geometry::model1 geometry::mode... ... (geometry::fat 0) \\\\lq
\[function\]\\\\ \\&gt; (geometry::fat2 nil) \\rm
\\end{emtabbing}](jmanual-img221.png){width="739" height="169"}

 
:   Check collision between model1 and model2 using PQP. If return value
    is 0, no collision. Otherwise (return value is 1), collision.

[]() ![\\begin{emtabbing} {\\bf pqp-collision-distance} \\it
geometry::model1 geometry::m... ...tion\]\\\\ \\&gt; (geometry::fat2
nil) \\\\ \\&gt; (geometry::qsize 2) \\rm
\\end{emtabbing}](jmanual-img222.png){width="1016" height="35"}

 
:   Calculate collision distance between model1 and model2 using PQP.
    Return value is (list \[distance\] \[nearest point on model1\]
    \[nearest point on model2\]). If collision occurs, \[distance\] is 0
    and nearest points are insignificant values.

[]() **pqp-collision-check-objects** *geometry::obj1 geometry::obj2 &key
(geometry::fat 0.2)* \[関数\]

 
:   return nil or t

[BVHデータ]()
=============

\
[]() **bvh-link** \[クラス\]

      :super   bodyset-link 
      :slots         type offset channels neutral 

 

:   

[]() **:init** *name typ offst chs parent children* \[メソッド\]

 
:   create link for bvh model

[]() **:type** \[メソッド\]

 

:   

[]() **:offset** \[メソッド\]

 

:   

[]() **:channels** \[メソッド\]

 

:   

[]() **:init** *name typ offst chs parent children* \[メソッド\]

 
:   create link for bvh model

[]() **:channels** \[メソッド\]

 

:   

[]() **:offset** \[メソッド\]

 

:   

[]() **:type** \[メソッド\]

 

:   

[]() **bvh-sphere-joint** \[クラス\]

      :super   sphere-joint 
      :slots         axis-order bvh-offset-rotation 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\=
(order (list :z :x :y)) \\... ...on bvh-rotation) (unit-matrix 3)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img223.png){width="660" height="54"}

 
:   create joint for bvh model

[]() **:joint-angle-bvh** *&optional v* \[メソッド\]

 

:   

[]() **:joint-angle-bvh-offset** *&optional v* \[メソッド\]

 

:   

[]() **:joint-angle-bvh-impl** *v bvh-offset* \[メソッド\]

 

:   

[]() **:axis-order** \[メソッド\]

 

:   

[]() **:bvh-offset-rotation** \[メソッド\]

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\=
(order (list :z :x :y)) \\... ...on bvh-rotation) (unit-matrix 3)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img223.png){width="660" height="54"}

 
:   create joint for bvh model

[]() **:bvh-offset-rotation** \[メソッド\]

 

:   

[]() **:axis-order** \[メソッド\]

 

:   

[]() **:joint-angle-bvh-impl** *v bvh-offset* \[メソッド\]

 

:   

[]() **:joint-angle-bvh-offset** *&optional v* \[メソッド\]

 

:   

[]() **:joint-angle-bvh** *&optional v* \[メソッド\]

 

:   

[]() **bvh-6dof-joint** \[クラス\]

      :super   6dof-joint 
      :slots         scale axis-order bvh-offset-rotation 

 

:   

[]() **:init** *&rest args &key (order (list :x :y :z :z :x :y))
((:scale scl)) ((:bvh-offset-rotation bvh-rotation) (unit-matrix 3))
&allow-other-keys* \[メソッド\]

 

:   

[]() **:joint-angle-bvh** *&optional v* \[メソッド\]

 

:   

[]() **:joint-angle-bvh-offset** *&optional v* \[メソッド\]

 

:   

[]() **:joint-angle-bvh-impl** *v bvh-offset* \[メソッド\]

 

:   

[]() **:axis-order** \[メソッド\]

 

:   

[]() **:bvh-offset-rotation** \[メソッド\]

 

:   

[]() **:bvh-offset-rotation** \[メソッド\]

 

:   

[]() **:axis-order** \[メソッド\]

 

:   

[]() **:joint-angle-bvh-impl** *v bvh-offset* \[メソッド\]

 

:   

[]() **:joint-angle-bvh-offset** *&optional v* \[メソッド\]

 

:   

[]() **:joint-angle-bvh** *&optional v* \[メソッド\]

 

:   

[]() **:init** *&rest args &key (order (list :x :y :z :z :x :y))
((:scale scl)) ((:bvh-offset-rotation bvh-rotation) (unit-matrix 3))
&allow-other-keys* \[メソッド\]

 

:   

[]() **bvh-robot-model** \[クラス\]

      :super   robot-model 
      :slots         nil 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= tree
\\\\lq \[method\]\\\\ \\&gt; coords \\\\ \\&gt; ((:scale scl)) \\rm
\\end{emtabbing}](jmanual-img224.png){width="552" height="53"}

 
:   create robot model for bvh model

[]() **:make-bvh-link** *tree &key parent ((:scale scl))* \[メソッド\]

 

:   

[]() **:angle-vector** *&optional vec (angle-vector (instantiate
float-vector (calc-target-joint-dimension joint-list)))* \[メソッド\]

 

:   

[]() **:dump-joints** *links &key (depth 0) (strm standard-output)*
\[メソッド\]

 

:   

[]() **:dump-hierarchy** *&optional (strm standard-output)* \[メソッド\]

 

:   

[]() **:dump-motion** *&optional (strm standard-output)* \[メソッド\]

 

:   

[]() **:copy-state-to** *robot* \[メソッド\]

 

:   

[]() **:fix-joint-angle** *i limb joint-name joint-order a* \[メソッド\]

 

:   

[]() **:fix-joint-order** *jo limb* \[メソッド\]

 

:   

[]() **:bvh-offset-rotate** *name* \[メソッド\]

 

:   

[]() **:init-end-coords** \[メソッド\]

 

:   

[]() **:init-root-link** \[メソッド\]

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\= tree
\\\\lq \[method\]\\\\ \\&gt; coords \\\\ \\&gt; ((:scale scl)) \\rm
\\end{emtabbing}](jmanual-img224.png){width="552" height="53"}

 
:   create robot model for bvh model

[]() **:bvh-offset-rotate** *name* \[メソッド\]

 

:   

[]() **:fix-joint-order** *jo limb* \[メソッド\]

 

:   

[]() **:fix-joint-angle** *i limb joint-name joint-order a* \[メソッド\]

 

:   

[]() **:copy-state-to** *robot* \[メソッド\]

 

:   

[]() **:dump-motion** *&optional (strm standard-output)* \[メソッド\]

 

:   

[]() **:dump-hierarchy** *&optional (strm standard-output)* \[メソッド\]

 

:   

[]() **:dump-joints** *links &key (depth 0) (strm standard-output)*
\[メソッド\]

 

:   

[]() **:angle-vector** *&optional vec (angle-vector (instantiate
float-vector (calc-target-joint-dimension joint-list)))* \[メソッド\]

 

:   

[]() **:make-bvh-link** *tree &key parent ((:scale scl))* \[メソッド\]

 

:   

[]() **motion-capture-data** \[クラス\]

      :super   propertied-object 
      :slots         frame model animation 

 

:   

[]() **:init** *fname &key (coords (make-coords)) ((:scale scl))*
\[メソッド\]

 

:   

[]() **:model** *&rest args* \[メソッド\]

 

:   

[]() **:animation** *&rest args* \[メソッド\]

 

:   

[]() **:frame** *&optional f* \[メソッド\]

 

:   

[]() **:frame-length** \[メソッド\]

 

:   

[]() **:animate** *&rest args &key (start 0) (step 1) (end (send self
:frame-length)) (interval 20) &allow-other-keys* \[メソッド\]

 

:   

[]() **:animate** *&rest args &key (start 0) (step 1) (end (send self
:frame-length)) (interval 20) &allow-other-keys* \[メソッド\]

 

:   

[]() **:frame-length** \[メソッド\]

 

:   

[]() **:frame** *&optional f* \[メソッド\]

 

:   

[]() **:animation** *&rest args* \[メソッド\]

 

:   

[]() **:model** *&rest args* \[メソッド\]

 

:   

[]() **:init** *fname &key (coords (make-coords)) ((:scale scl))*
\[メソッド\]

 

:   

[]() **:init-root-link** \[メソッド\]

 

:   

[]() **:init-end-coords** \[メソッド\]

 

:   

[]() **rikiya-bvh-robot-model** \[クラス\]

      :super   bvh-robot-model 
      :slots         nil 

 

:   

[]() **:init** *&rest args* \[メソッド\]

 

:   

[]() **:larm-collar** *&rest args* \[メソッド\]

 

:   

[]() **:larm-shoulder** *&rest args* \[メソッド\]

 

:   

[]() **:larm-elbow** *&rest args* \[メソッド\]

 

:   

[]() **:larm-wrist** *&rest args* \[メソッド\]

 

:   

[]() **:rarm-collar** *&rest args* \[メソッド\]

 

:   

[]() **:rarm-shoulder** *&rest args* \[メソッド\]

 

:   

[]() **:rarm-elbow** *&rest args* \[メソッド\]

 

:   

[]() **:rarm-wrist** *&rest args* \[メソッド\]

 

:   

[]() **:lleg-crotch** *&rest args* \[メソッド\]

 

:   

[]() **:lleg-knee** *&rest args* \[メソッド\]

 

:   

[]() **:lleg-ankle** *&rest args* \[メソッド\]

 

:   

[]() **:rleg-crotch** *&rest args* \[メソッド\]

 

:   

[]() **:rleg-knee** *&rest args* \[メソッド\]

 

:   

[]() **:rleg-ankle** *&rest args* \[メソッド\]

 

:   

[]() **:torso-chest** *&rest args* \[メソッド\]

 

:   

[]() **:head-neck** *&rest args* \[メソッド\]

 

:   

[]() **:copy-joint-to** *robot limb joint &optional (sign 1)*
\[メソッド\]

 

:   

[]() **:copy-state-to** *robot* \[メソッド\]

 

:   

[]() **:copy-state-to** *robot* \[メソッド\]

 

:   

[]() **:copy-joint-to** *robot limb joint &optional (sign 1)*
\[メソッド\]

 

:   

[]() **:head-neck** *&rest args* \[メソッド\]

 

:   

[]() **:torso-chest** *&rest args* \[メソッド\]

 

:   

[]() **:rleg-ankle** *&rest args* \[メソッド\]

 

:   

[]() **:rleg-knee** *&rest args* \[メソッド\]

 

:   

[]() **:rleg-crotch** *&rest args* \[メソッド\]

 

:   

[]() **:lleg-ankle** *&rest args* \[メソッド\]

 

:   

[]() **:lleg-knee** *&rest args* \[メソッド\]

 

:   

[]() **:lleg-crotch** *&rest args* \[メソッド\]

 

:   

[]() **:rarm-wrist** *&rest args* \[メソッド\]

 

:   

[]() **:rarm-elbow** *&rest args* \[メソッド\]

 

:   

[]() **:rarm-shoulder** *&rest args* \[メソッド\]

 

:   

[]() **:rarm-collar** *&rest args* \[メソッド\]

 

:   

[]() **:larm-wrist** *&rest args* \[メソッド\]

 

:   

[]() **:larm-elbow** *&rest args* \[メソッド\]

 

:   

[]() **:larm-shoulder** *&rest args* \[メソッド\]

 

:   

[]() **:larm-collar** *&rest args* \[メソッド\]

 

:   

[]() **:init** *&rest args* \[メソッド\]

 

:   

[]() **tum-bvh-robot-model** \[クラス\]

      :super   bvh-robot-model 
      :slots         nil 

 

:   

[]() **:init** *&rest args* \[メソッド\]

 

:   

[]() **:init** *&rest args* \[メソッド\]

 

:   

[]() **cmu-bvh-robot-model** \[クラス\]

      :super   bvh-robot-model 
      :slots         nil 

 

:   

[]() **:init** *&rest args* \[メソッド\]

 

:   

[]() **:init** *&rest args* \[メソッド\]

 

:   

[]() **read-bvh** *fname &key scale* \[関数\]

 
:   read bvh file

[]() ![\\begin{emtabbing} {\\bf bvh2eus} \\it fname \\&rest args \\&key
\\= ((:objects obj) nil) \\\\lq \[function\]\\\\ \\&gt;
\\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img225.png){width="552" height="54"}

 
:   read bvh file and anmiate robot model in the viewer for Supported
    bvh data, such as - CMU motion capture database
    (https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/cmu-bvh-conversion) -
    The TUM Kitchen Data Set
    (http://ias.cs.tum.edu/download/kitchen-activity-data) Use
    (tum-bvh2eus ''Take 005.bvh'') ;; tum (rikiya-bvh2eus ''A01.bvh'')
    ;; rikiya (cmu-bvh2eus ''01\_01.bvh'') ;; cmu Other Sites are:
    (http://www.mocapdata.com/page.cgi?p=free\_motions)
    (http://www.motekentertainment.com/)
    (http://www.mocapclub.com/Pages/Library.htm) (bvh2eus ''poses.bvh'')

[]() ![\\begin{emtabbing} {\\bf load-mcd} \\it fname \\&key \\= (scale)
\\\\lq \[function\]\\\\ \\&gt;... ...obot-model-class bvh-robot-model)
\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img226.png){width="553" height="34"}

 
:   load motion capture data

[]() **rikiya-bvh2eus** *fname &rest args* \[関数\]

 
:   read rikiya bvh file and anmiate robot model in the viewer
    (rikiya-bvh2eus ''A01.bvh'')

[]() **cmu-bvh2eus** *fname &rest args* \[関数\]

 
:   read cmu bvh file and anmiate robot model in the viewer CMU motion
    capture database
    (https://sites.google.com/a/cgspeed.com/cgspeed/motion-capture/cmu-bvh-conversion)
    (cmu-bvh2eus ''01\_01.bvh'' :scale 100.0)

[]() **tum-bvh2eus** *fname &rest args* \[関数\]

 
:   read tum file and anmiate robot model in the viewer The TUM Kitchen
    Data Set (http://ias.cs.tum.edu/download/kitchen-activity-data)
    (tum-bvh2eus ''Take 005.bvh'' :scale 10.0)

[]() **parse-bvh-sexp** *src &key ((:scale scl))* \[関数\]

 

:   

[]() **make-bvh-robot-model** *bvh-data &rest args* \[関数\]

 

:   

[Colladaデータ]()
=================

[]() **collada::eusmodel-description** *collada::model* \[関数\]

 
:   convert a \`model' to eusmodel-description

[]() **collada::eusmodel-link-specs** *collada::links* \[関数\]

 
:   convert \`links' to &lt;link-specs&gt;

[]() **collada::eusmodel-joint-specs** *collada::joints* \[関数\]

 
:   convert \`joints' to &lt;joint-specs&gt;

[]() **collada::eusmodel-description-&gt;collada** *name
collada::description &key (scale 0.001)* \[関数\]

 
:   convert eusmodel-descrption to collada sxml

[]() **collada::matrix-&gt;collada-rotate-vector** *collada::mat*
\[関数\]

 
:   convert a rotation matrix to axis-angle.

[]() **convert-irtmodel-to-collada** *collada::model-file &optional
(collada::output-full-dir (send (truename ./) :namestring))
(collada::model-name) (collada::exit-p t)* \[関数\]

 
:   convert irtmodel to collada model. (convert-irtmodel-to-collada
    irtmodel-file-path &optional (output-full-dir (send
    (truename ''./'') :namestring)) (model-name))

[]() **collada::symbol-&gt;string** *collada::sym* \[関数\]

 

:   

[]() **collada::-&gt;string** *collada::val* \[関数\]

 

:   

[]() **collada::string-append** *&rest args* \[関数\]

 

:   

[]() **collada::make-attr** *collada::l collada::ac* \[関数\]

 

:   

[]() **collada::make-xml** *collada::x collada::bef collada::aft*
\[関数\]

 

:   

[]() **collada::sxml-&gt;xml** *collada::sxml* \[関数\]

 

:   

[]() **collada::xml-output-to-string-stream** *collada::ss collada::l*
\[関数\]

 

:   

[]() **collada::cat-normal** *collada::l collada::s* \[関数\]

 

:   

[]() **collada::cat-clark** *collada::l collada::s collada::i* \[関数\]

 

:   

[]() **collada::verificate-unique-strings** *names* \[関数\]

 

:   

[]() **collada::eusmodel-link-spec** *collada::link* \[関数\]

 

:   

[]() **collada::eusmodel-mesh-spec** *collada::link* \[関数\]

 

:   

[]() **collada::eusmodel-joint-spec** *collada::\_joint* \[関数\]

 

:   

[]() **collada::eusmodel-limit-spec** *collada::\_joint* \[関数\]

 

:   

[]() **collada::eusmodel-endcoords-specs** *collada::model* \[関数\]

 

:   

[]() **collada::eusmodel-link-description** *collada::description*
\[関数\]

 

:   

[]() **collada::eusmodel-joint-description** *collada::description*
\[関数\]

 

:   

[]() **collada::eusmodel-endcoords-description** *collada::description*
\[関数\]

 

:   

[]() **collada::setup-collada-filesystem** *collada::obj-name
collada::base-dir* \[関数\]

 

:   

[]() **collada::range2** *collada::n* \[関数\]

 

:   

[]() **eus2collada** *collada::obj collada::full-root-dir &key (scale
0.001) (collada::file-suffix .dae)* \[関数\]

 

:   

[]() **collada::collada-node-id** *collada::link-descrption* \[関数\]

 

:   

[]() **collada::collada-node-name** *collada::link-descrption* \[関数\]

 

:   

[]() **collada::links-description-&gt;collada-library-materials**
*collada::links-desc* \[関数\]

 

:   

[]() **collada::link-description-&gt;collada-materials**
*collada::link-desc* \[関数\]

 

:   

[]() **collada::mesh-description-&gt;collada-material** *collada::mat
collada::effect* \[関数\]

 

:   

[]() **collada::links-description-&gt;collada-library-effects**
*collada::links-desc* \[関数\]

 

:   

[]() **collada::link-description-&gt;collada-effects**
*collada::link-desc* \[関数\]

 

:   

[]() **collada::mesh-description-&gt;collada-effect** *collada::mesh id*
\[関数\]

 

:   

[]() **collada::matrix-&gt;collada-string** *collada::mat* \[関数\]

 

:   

[]() **collada::find-parent-liks-from-link-description**
*collada::target-link collada::desc* \[関数\]

 

:   

[]() **collada::eusmodel-description-&gt;collada-node-transformations**
*collada::target-link collada::desc* \[関数\]

 

:   

[]() **collada::eusmodel-description-&gt;collada-node**
*collada::target-link collada::desc* \[関数\]

 

:   

[]() **collada::eusmodel-description-&gt;collada-library-visual-scenes**
*name collada::desc* \[関数\]

 

:   

[]() **collada::mesh-description-&gt;instance-material** *collada::s*
\[関数\]

 

:   

[]() **collada::link-description-&gt;collada-bind-material**
*collada::link* \[関数\]

 

:   

[]()
**collada::eusmodel-description-&gt;collada-library-kinematics-scenes**
*name collada::desc* \[関数\]

 

:   

[]()
**collada::eusmodel-description-&gt;collada-library-kinematics-models**
*name collada::desc* \[関数\]

 

:   

[]() **collada::eusmodel-description-&gt;collada-kinematics-model**
*name collada::desc* \[関数\]

 

:   

[]()
**collada::eusmodel-description-&gt;collada-library-physics-scenes**
*name collada::desc* \[関数\]

 

:   

[]()
**collada::eusmodel-description-&gt;collada-library-physics-models**
*name collada::desc* \[関数\]

 

:   

[]() **collada::find-root-link-from-joints-description**
*collada::joints-description* \[関数\]

 

:   

[]() **collada::find-link-from-links-description** *name
collada::links-description* \[関数\]

 

:   

[]() **collada::eusmodel-description-&gt;collada-links**
*collada::description* \[関数\]

 

:   

[]() **collada::find-joint-from-link-description** *collada::target
collada::joints* \[関数\]

 

:   

[]() **collada::find-child-link-descriptions** *collada::parent
collada::links collada::joints* \[関数\]

 

:   

[]()
**collada::eusmodel-description-&gt;collada-library-articulated-systems**
*collada::desc name* \[関数\]

 

:   

[]()
**collada::eusmodel-endcoords-description-&gt;openrave-manipulator**
*collada::end-coords collada::description* \[関数\]

 

:   

[]() **collada::eusmodel-description-&gt;collada-links-tree**
*collada::target collada::links collada::joints* \[関数\]

 

:   

[]() **collada::joints-description-&gt;collada-instance-joints**
*collada::joints-desc* \[関数\]

 

:   

[]() **collada::joint-description-&gt;collada-instance-joint**
*collada::joint-desc* \[関数\]

 

:   

[]() **collada::eusmodel-description-&gt;collada-library-joints**
*collada::description* \[関数\]

 

:   

[]() **collada::joints-description-&gt;collada-joints**
*collada::joints-description* \[関数\]

 

:   

[]() **collada::collada-joint-id** *collada::joint-description* \[関数\]

 

:   

[]() **collada::joint-description-&gt;collada-joint**
*collada::joint-description* \[関数\]

 

:   

[]() **collada::linear-joint-description-&gt;collada-joint**
*collada::joint-description* \[関数\]

 

:   

[]() **collada::rotational-joint-description-&gt;collada-joint**
*collada::joint-description* \[関数\]

 

:   

[]() **collada::eusmodel-description-&gt;collada-scene**
*collada::description* \[関数\]

 

:   

[]() **collada::eusmodel-description-&gt;collada-library-geometries**
*collada::description* \[関数\]

 

:   

[]() **collada::collada-geometry-id-base** *collada::link-descrption*
\[関数\]

 

:   

[]() **collada::collada-geometry-name-base** *collada::link-descrption*
\[関数\]

 

:   

[]() **collada::links-description-&gt;collada-geometries**
*collada::links-description* \[関数\]

 

:   

[]() **collada::mesh-object-&gt;collada-mesh** *collada::mesh id*
\[関数\]

 

:   

[]() **collada::link-description-&gt;collada-geometry**
*collada::link-description* \[関数\]

 

:   

[]() **collada::mesh-&gt;collada-indices** *collada::mesh* \[関数\]

 

:   

[]() **collada::mesh-vertices-&gt;collada-string** *collada::mesh*
\[関数\]

 

:   

[]() **collada::mesh-normals-&gt;collada-string** *collada::mesh*
\[関数\]

 

:   

[]() **collada::float-vector-&gt;collada-string** *collada::v* \[関数\]

 

:   

[]() **collada::enum-integer-list** *collada::n* \[関数\]

 

:   

[]() **collada::search-minimum-rotation-matrix** *collada::mat* \[関数\]

 

:   

[]() **collada::estimate-class-name** *collada::model-file* \[関数\]

 

:   

[]() **collada::remove-directory-name** *fname* \[関数\]

 

:   

[ポイントクラウドデータ]()
==========================

\
[]() **pointcloud** \[クラス\]

      :super   cascaded-coords 
      :slots         parray carray narray curvature pcolor psize awidth asize box height width view-coords drawnormalmode transparent tcarray 

 

:   

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\=
((:points mat)) \\\\lq \[metho... ... \\&gt; (fill) \\\\ \\&gt;
(arrow-width 2.0) \\\\ \\&gt; (arrow-size 0.0) \\rm
\\end{emtabbing}](jmanual-img227.png){width="553" height="72"}

 
:   Create point cloud object

[]() **:size-change** *&optional wd ht* \[メソッド\]

 
:   change width and height, this method does not change points data

[]() **:points** *&optional pts wd ht* \[メソッド\]

 
:   replace points, pts should be list of points or n<span
    class="MATH">**![\$ times\$](jmanual-img228.png){width="552"
    height="207"}**</span> matrix

[]() **:colors** *&optional cls* \[メソッド\]

 
:   replace colors, cls should be list of points or n<span
    class="MATH">**![\$ times\$](jmanual-img228.png){width="552"
    height="207"}**</span> matrix

[]() **:normals** *&optional nmls* \[メソッド\]

 
:   replace normals by, nmls should be list of points or n<span
    class="MATH">**![\$ times\$](jmanual-img228.png){width="552"
    height="207"}**</span>3 matrix

[]() **:point-list** *&optional (remove-nan)* \[メソッド\]

 
:   return list of points

[]() **:color-list** \[メソッド\]

 
:   return list of colors

[]() **:normal-list** \[メソッド\]

 
:   return list of normals

[]() **:centroid** \[メソッド\]

 
:   retrun centroid of this point cloud

[]() **:append** *point-list &key (create t)* \[メソッド\]

 
:   append point cloud list to this point cloud. if :create is true,
    return appended point cloud and original point cloud does
    not change.

[]() ![\\begin{emtabbing} {\\bf :filter} \\it\\&rest args \\&key \\=
create \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img229.png){width="47" height="18"}

 
:   this method can take the same keywords with :filter-with-indices
    method. if :create is true, return filtered point cloud and original
    point cloud does not change.

[]() ![\\begin{emtabbing} {\\bf :filter-with-indices} \\it idx-lst
\\&key \\= (create) \\\\lq \[method\]\\\\ \\&gt; (negative) \\rm
\\end{emtabbing}](jmanual-img230.png){width="552" height="34"}

 
:   filter point cloud with list of index (points which are indicated by
    indices will remain). if :create is true, return filtered point
    cloud and original point cloud does not change. if :negative is
    true, points which are indicated by indices will be removed.

[]() ![\\begin{emtabbing} {\\bf :filtered-indices} \\it\\&key \\= key
\\\\lq \[method\]\\\\ \\&gt; cke... ...key \\\\ \\&gt; pcnkey \\\\
\\&gt; negative \\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img231.png){width="552" height="35"}

 
:   create list of index where filter function retrun true. key, ckey,
    nkey are filter function for points, colors, normals. They are
    expected to take one argument and return t or nil. pckey, pnkey are
    filter function for points and colors, points and normals. They are
    expected to take two arguments and return t or nil. pcnkey is filter
    function for points, colors and normals. It is expected to take
    three arguments and return t or nil.

[]() ![\\begin{emtabbing} {\\bf :step} \\it step \\&key \\= (fixsize)
\\\\lq \[method\]\\\\ \\&gt; (create) \\rm
\\end{emtabbing}](jmanual-img232.png){width="552" height="148"}

 
:   downsample points with step

[]() **:copy-from** *pc* \[メソッド\]

 
:   update object by pc

[]() **:transform-points** *coords &key (create)* \[メソッド\]

 
:   transform points and normals with coords. if :create is true, return
    transformed point cloud and original point cloud does not change.

[]() **:convert-to-world** *&key (create)* \[メソッド\]

 
:   transform points and normals with self coords. points data should be
    the same as displayed

[]() **:reset-box** \[メソッド\]

 

:   

[]() **:box** \[メソッド\]

 

:   

[]() **:vertices** \[メソッド\]

 

:   

[]() **:size** \[メソッド\]

 

:   

[]() **:width** \[メソッド\]

 

:   

[]() **:height** \[メソッド\]

 

:   

[]() **:view-coords** *&optional vc* \[メソッド\]

 

:   

[]() **:curvatures** *&optional curv* \[メソッド\]

 

:   

[]() **:curvature-list** \[メソッド\]

 

:   

[]() **:set-color** *col &optional (\_transparent)* \[メソッド\]

 

:   

[]() **:point-color** *&optional pc* \[メソッド\]

 

:   

[]() **:point-size** *&optional ps* \[メソッド\]

 

:   

[]() **:axis-length** *&optional al* \[メソッド\]

 

:   

[]() **:axis-width** *&optional aw* \[メソッド\]

 

:   

[]() **:clear-color** \[メソッド\]

 

:   

[]() **:clear-normal** \[メソッド\]

 

:   

[]() **:nfilter** *&rest args* \[メソッド\]

 

:   

[]() **:viewangle-inlier** *&key (min-z 0.0) (hangle 44.0) (vangle
35.0)* \[メソッド\]

 

:   

[]() **:image-position-inlier** *&key (ipkey) (height 144) (width 176)
(cy (/ (float (- height 1)) 2)) (cx (/ (float (- width 1)) 2)) negative*
\[メソッド\]

 

:   

[]() **:image-circle-filter** *dist &key (height 144) (width 176) (cy (/
(float (- height 1)) 2)) (cx (/ (float (- width 1)) 2)) create negative*
\[メソッド\]

 

:   

[]() **:step-inlier** *step offx offy* \[メソッド\]

 

:   

[]() **:generate-color-histogram-hs** *&key (h-step 9) (s-step 7)
(hlimits (cons 360.0 0.0)) (vlimits (cons 1.0 0.15)) (slimits (cons 1.0
0.25)) (rotate-hue) (color-scale 255.0) (sizelimits 1)* \[メソッド\]

 

:   

[]() **:set-offset** *cds &key (create)* \[メソッド\]

 

:   

[]() **:drawnormalmode** *&optional mode* \[メソッド\]

 

:   

[]() **:transparent** *&optional trs* \[メソッド\]

 

:   

[]() **:draw** *vwer* \[メソッド\]

 

:   

[]() **:convert-to-world** *&key (create)* \[メソッド\]

 
:   transform points and normals with self coords. points data should be
    the same as displayed

[]() **:transform-points** *coords &key (create)* \[メソッド\]

 
:   transform points and normals with coords. if :create is true, return
    transformed point cloud and original point cloud does not change.

[]() **:copy-from** *pc* \[メソッド\]

 
:   update object by pc

[]() ![\\begin{emtabbing} {\\bf :step} \\it step \\&key \\= (fixsize)
\\\\lq \[method\]\\\\ \\&gt; (create) \\rm
\\end{emtabbing}](jmanual-img232.png){width="552" height="148"}

 
:   downsample points with step

[]() ![\\begin{emtabbing} {\\bf :filtered-indices} \\it\\&key \\= key
\\\\lq \[method\]\\\\ \\&gt; cke... ...key \\\\ \\&gt; pcnkey \\\\
\\&gt; negative \\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img231.png){width="552" height="35"}

 
:   create list of index where filter function retrun true. key, ckey,
    nkey are filter function for points, colors, normals. They are
    expected to take one argument and return t or nil. pckey, pnkey are
    filter function for points and colors, points and normals. They are
    expected to take two arguments and return t or nil. pcnkey is filter
    function for points, colors and normals. It is expected to take
    three arguments and return t or nil.

[]() ![\\begin{emtabbing} {\\bf :filter-with-indices} \\it idx-lst
\\&key \\= (create) \\\\lq \[method\]\\\\ \\&gt; (negative) \\rm
\\end{emtabbing}](jmanual-img230.png){width="552" height="34"}

 
:   filter point cloud with list of index (points which are indicated by
    indices will remain). if :create is true, return filtered point
    cloud and original point cloud does not change. if :negative is
    true, points which are indicated by indices will be removed.

[]() ![\\begin{emtabbing} {\\bf :filter} \\it\\&rest args \\&key \\=
create \\\\lq \[method\]\\\\ \\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img229.png){width="47" height="18"}

 
:   this method can take the same keywords with :filter-with-indices
    method. if :create is true, return filtered point cloud and original
    point cloud does not change.

[]() **:append** *point-list &key (create t)* \[メソッド\]

 
:   append point cloud list to this point cloud. if :create is true,
    return appended point cloud and original point cloud does
    not change.

[]() **:centroid** \[メソッド\]

 
:   retrun centroid of this point cloud

[]() **:normal-list** \[メソッド\]

 
:   return list of normals

[]() **:color-list** \[メソッド\]

 
:   return list of colors

[]() **:point-list** *&optional (remove-nan)* \[メソッド\]

 
:   return list of points

[]() **:normals** *&optional nmls* \[メソッド\]

 
:   replace normals by, nmls should be list of points or n<span
    class="MATH">**![\$ times\$](jmanual-img228.png){width="552"
    height="207"}**</span>3 matrix

[]() **:colors** *&optional cls* \[メソッド\]

 
:   replace colors, cls should be list of points or n<span
    class="MATH">**![\$ times\$](jmanual-img228.png){width="552"
    height="207"}**</span> matrix

[]() **:points** *&optional pts wd ht* \[メソッド\]

 
:   replace points, pts should be list of points or n<span
    class="MATH">**![\$ times\$](jmanual-img228.png){width="552"
    height="207"}**</span> matrix

[]() **:size-change** *&optional wd ht* \[メソッド\]

 
:   change width and height, this method does not change points data

[]() ![\\begin{emtabbing} {\\bf :init} \\it\\&rest args \\&key \\=
((:points mat)) \\\\lq \[metho... ... \\&gt; (fill) \\\\ \\&gt;
(arrow-width 2.0) \\\\ \\&gt; (arrow-size 0.0) \\rm
\\end{emtabbing}](jmanual-img227.png){width="553" height="72"}

 
:   Create point cloud object

[]() **:draw** *vwer* \[メソッド\]

 

:   

[]() **:transparent** *&optional trs* \[メソッド\]

 

:   

[]() **:drawnormalmode** *&optional mode* \[メソッド\]

 

:   

[]() **:set-offset** *cds &key (create)* \[メソッド\]

 

:   

[]() **:generate-color-histogram-hs** *&key (h-step 9) (s-step 7)
(hlimits (cons 360.0 0.0)) (vlimits (cons 1.0 0.15)) (slimits (cons 1.0
0.25)) (rotate-hue) (color-scale 255.0) (sizelimits 1)* \[メソッド\]

 

:   

[]() **:step-inlier** *step offx offy* \[メソッド\]

 

:   

[]() **:image-circle-filter** *dist &key (height 144) (width 176) (cy (/
(float (- height 1)) 2)) (cx (/ (float (- width 1)) 2)) create negative*
\[メソッド\]

 

:   

[]() **:image-position-inlier** *&key (ipkey) (height 144) (width 176)
(cy (/ (float (- height 1)) 2)) (cx (/ (float (- width 1)) 2)) negative*
\[メソッド\]

 

:   

[]() **:viewangle-inlier** *&key (min-z 0.0) (hangle 44.0) (vangle
35.0)* \[メソッド\]

 

:   

[]() **:nfilter** *&rest args* \[メソッド\]

 

:   

[]() **:clear-normal** \[メソッド\]

 

:   

[]() **:clear-color** \[メソッド\]

 

:   

[]() **:axis-width** *&optional aw* \[メソッド\]

 

:   

[]() **:axis-length** *&optional al* \[メソッド\]

 

:   

[]() **:point-size** *&optional ps* \[メソッド\]

 

:   

[]() **:point-color** *&optional pc* \[メソッド\]

 

:   

[]() **:set-color** *col &optional (\_transparent)* \[メソッド\]

 

:   

[]() **:curvature-list** \[メソッド\]

 

:   

[]() **:curvatures** *&optional curv* \[メソッド\]

 

:   

[]() **:view-coords** *&optional vc* \[メソッド\]

 

:   

[]() **:height** \[メソッド\]

 

:   

[]() **:width** \[メソッド\]

 

:   

[]() **:size** \[メソッド\]

 

:   

[]() **:vertices** \[メソッド\]

 

:   

[]() **:box** \[メソッド\]

 

:   

[]() **:reset-box** \[メソッド\]

 

:   

[]() **make-random-pointcloud** *&key (num 1000) (with-color)
(with-normal) (scale 100.0)* \[関数\]

 

:   

[グラフ表現]()
==============

\
[]() **node** \[クラス\]

      :super   propertied-object 
      :slots         arc-list 

 

:   

[]() **:init** *n* \[メソッド\]

 

:   

[]() **:arc-list** \[メソッド\]

 

:   

[]() **:successors** \[メソッド\]

 

:   

[]() **:add-arc** *a* \[メソッド\]

 

:   

[]() **:remove-arc** *a* \[メソッド\]

 

:   

[]() **:remove-all-arcs** \[メソッド\]

 

:   

[]() **:unlink** *n* \[メソッド\]

 

:   

[]() **:add-neighbor** *n &optional a* \[メソッド\]

 

:   

[]() **:neighbors** *&optional args* \[メソッド\]

 

:   

[]() **:unlink** *n* \[メソッド\]

 

:   

[]() **:remove-all-arcs** \[メソッド\]

 

:   

[]() **:remove-arc** *a* \[メソッド\]

 

:   

[]() **:add-arc** *a* \[メソッド\]

 

:   

[]() **:successors** \[メソッド\]

 

:   

[]() **:arc-list** \[メソッド\]

 

:   

[]() **:init** *n* \[メソッド\]

 

:   

[]() **arc** \[クラス\]

      :super   propertied-object 
      :slots         from to 

 

:   

[]() **:init** *from\_ to\_* \[メソッド\]

 

:   

[]() **:from** \[メソッド\]

 

:   

[]() **:to** \[メソッド\]

 

:   

[]() **:prin1** *&optional (strm t) &rest msgs* \[メソッド\]

 

:   

[]() **:prin1** *&optional (strm t) &rest msgs* \[メソッド\]

 

:   

[]() **:to** \[メソッド\]

 

:   

[]() **:from** \[メソッド\]

 

:   

[]() **:init** *from\_ to\_* \[メソッド\]

 

:   

[]() **directed-graph** \[クラス\]

      :super   propertied-object 
      :slots         nodes 

 

:   

[]() **:write-to-dot-stream** *&optional (strm standard-output)
result-path (title output)* \[メソッド\]

 
:   write graph structure to stream as dot(graphviz) style Args: strm:
    stream class for output result-path: list of solver-node, it's
    result of (send solver :solve graph) title: title of graph

[]() **:write-to-dot** *fname &optional result-path (title output)*
\[メソッド\]

 
:   write graph structure to dot(graphviz) file Args: fname: filename
    for output result-path: list of solver-node, it's result of (send
    solver :solve graph) title: title of graph

[]() **:write-to-file** *basename &optional result-path title (type
pdf)* \[メソッド\]

 
:   write graph structure to various type of file Args: basename:
    basename for output (output filename is 'basename.type')
    result-path: list of solver-node, it's result of (send solver :solve
    graph) title: title of graph type: type of output

[]() **:write-to-pdf** *fname &optional result-path (title
(string-right-trim .pdf fname))* \[メソッド\]

 
:   write graph structure to pdf Args: fname: filename for output
    result-path: list of solver-node, it's result of (send solver :solve
    graph) title: title of graph

[]() **:original-draw-mode** \[メソッド\]

 
:   change draw-mode to original mode

[]() **:current-draw-mode** \[メソッド\]

 
:   change draw-mode to latest mode

[]() **:draw-both-arc** *&optional (bothq :both)* \[メソッド\]

 
:   change draw-mode, if true is set, draw bidirectional arc as two arcs

[]() **:draw-arc-label** *&optional (writeq :write)* \[メソッド\]

 
:   change draw-mode, if true is set, draw label(name) of arcs

[]() **:draw-merged-result** *&optional (mergeq :merge)* \[メソッド\]

 
:   change draw-mode, if true is set, draw result arc as red. if not,
    draw red arc independently

[]() **:init** \[メソッド\]

 

:   

[]() **:successors** *node &rest args* \[メソッド\]

 

:   

[]() **:node** *name* \[メソッド\]

 

:   

[]() **:nodes** *&optional arg* \[メソッド\]

 

:   

[]() **:add-node** *n* \[メソッド\]

 

:   

[]() **:remove-node** *n* \[メソッド\]

 

:   

[]() **:clear-nodes** \[メソッド\]

 

:   

[]() **:add-arc-from-to** *from to* \[メソッド\]

 

:   

[]() **:remove-arc** *arc* \[メソッド\]

 

:   

[]() **:adjacency-matrix** \[メソッド\]

 

:   

[]() **:adjacency-list** \[メソッド\]

 

:   

[]() **:adjacency-list** \[メソッド\]

 

:   

[]() **:adjacency-matrix** \[メソッド\]

 

:   

[]() **:remove-arc** *arc* \[メソッド\]

 

:   

[]() **:add-arc-from-to** *from to* \[メソッド\]

 

:   

[]() **:clear-nodes** \[メソッド\]

 

:   

[]() **:remove-node** *n* \[メソッド\]

 

:   

[]() **:add-node** *n* \[メソッド\]

 

:   

[]() **:nodes** *&optional arg* \[メソッド\]

 

:   

[]() **:node** *name* \[メソッド\]

 

:   

[]() **:successors** *node &rest args* \[メソッド\]

 

:   

[]() **:init** \[メソッド\]

 

:   

[]() **costed-arc** \[クラス\]

      :super   arc 
      :slots         cost 

 

:   

[]() **:init** *from to c* \[メソッド\]

 

:   

[]() **:cost** \[メソッド\]

 

:   

[]() **:cost** \[メソッド\]

 

:   

[]() **:init** *from to c* \[メソッド\]

 

:   

[]() **costed-graph** \[クラス\]

      :super   directed-graph 
      :slots         nil 

 

:   

[]() **:add-arc** *from to cost &key (both nil)* \[メソッド\]

 

:   

[]() **:add-arc-from-to** *from to cost &key (both nil)* \[メソッド\]

 

:   

[]() **:path-cost** *from arc to* \[メソッド\]

 

:   

[]() **:path-cost** *from arc to* \[メソッド\]

 

:   

[]() **:add-arc-from-to** *from to cost &key (both nil)* \[メソッド\]

 

:   

[]() **:add-arc** *from to cost &key (both nil)* \[メソッド\]

 

:   

[]() **graph** \[クラス\]

      :super   costed-graph 
      :slots         start-state goal-state 

 

:   

[]() **:goal-test** *gs* \[メソッド\]

 

:   

[]() **:path-cost** *from arc to* \[メソッド\]

 

:   

[]() **:start-state** *&optional arg* \[メソッド\]

 

:   

[]() **:goal-state** *&optional arg* \[メソッド\]

 

:   

[]() **:add-arc** *from to &key (both nil)* \[メソッド\]

 

:   

[]() **:add-arc-from-to** *from to &key (both nil)* \[メソッド\]

 

:   

[]() **:add-arc-from-to** *from to &key (both nil)* \[メソッド\]

 

:   

[]() **:add-arc** *from to &key (both nil)* \[メソッド\]

 

:   

[]() **:goal-state** *&optional arg* \[メソッド\]

 

:   

[]() **:start-state** *&optional arg* \[メソッド\]

 

:   

[]() **:path-cost** *from arc to* \[メソッド\]

 

:   

[]() **:goal-test** *gs* \[メソッド\]

 

:   

[]() **:draw-merged-result** *&optional (mergeq :merge)* \[メソッド\]

 
:   change draw-mode, if true is set, draw result arc as red. if not,
    draw red arc independently

[]() **:draw-arc-label** *&optional (writeq :write)* \[メソッド\]

 
:   change draw-mode, if true is set, draw label(name) of arcs

[]() **:draw-both-arc** *&optional (bothq :both)* \[メソッド\]

 
:   change draw-mode, if true is set, draw bidirectional arc as two arcs

[]() **:current-draw-mode** \[メソッド\]

 
:   change draw-mode to latest mode

[]() **:original-draw-mode** \[メソッド\]

 
:   change draw-mode to original mode

[]() **:write-to-pdf** *fname &optional result-path (title
(string-right-trim .pdf fname))* \[メソッド\]

 
:   write graph structure to pdf Args: fname: filename for output
    result-path: list of solver-node, it's result of (send solver :solve
    graph) title: title of graph

[]() **:write-to-file** *basename &optional result-path title (type
pdf)* \[メソッド\]

 
:   write graph structure to various type of file Args: basename:
    basename for output (output filename is 'basename.type')
    result-path: list of solver-node, it's result of (send solver :solve
    graph) title: title of graph type: type of output

[]() **:write-to-dot** *fname &optional result-path (title output)*
\[メソッド\]

 
:   write graph structure to dot(graphviz) file Args: fname: filename
    for output result-path: list of solver-node, it's result of (send
    solver :solve graph) title: title of graph

[]() **:write-to-dot-stream** *&optional (strm standard-output)
result-path (title output)* \[メソッド\]

 
:   write graph structure to stream as dot(graphviz) style Args: strm:
    stream class for output result-path: list of solver-node, it's
    result of (send solver :solve graph) title: title of graph

[]() **:neighbors** *&optional args* \[メソッド\]

 

:   

[]() **:add-neighbor** *n &optional a* \[メソッド\]

 

:   

[]() **arced-node** \[クラス\]

      :super   node 
      :slots         nil 

 

:   

[]() **:init** *&key name* \[メソッド\]

 

:   

[]() **:find-action** *n* \[メソッド\]

 

:   

[]() **:neighbor-action-alist** \[メソッド\]

 

:   

[]() **:neighbor-action-alist** \[メソッド\]

 

:   

[]() **:find-action** *n* \[メソッド\]

 

:   

[]() **:init** *&key name* \[メソッド\]

 

:   

[]() **solver-node** \[クラス\]

      :super   propertied-object 
      :slots         state cost parent action memorized-path 

 

:   

[]() **:init** *st &key ((:cost c) 0) ((:parent p) nil) ((:action a)
nil)* \[メソッド\]

 

:   

[]() **:path** *&optional (prev nil)* \[メソッド\]

 

:   

[]() **:expand** *prblm &rest args* \[メソッド\]

 

:   

[]() **:state** *&optional arg* \[メソッド\]

 

:   

[]() **:cost** *&optional arg* \[メソッド\]

 

:   

[]() **:parent** *&optional arg* \[メソッド\]

 

:   

[]() **:action** *&optional arg* \[メソッド\]

 

:   

[]() **:action** *&optional arg* \[メソッド\]

 

:   

[]() **:parent** *&optional arg* \[メソッド\]

 

:   

[]() **:cost** *&optional arg* \[メソッド\]

 

:   

[]() **:state** *&optional arg* \[メソッド\]

 

:   

[]() **:expand** *prblm &rest args* \[メソッド\]

 

:   

[]() **:path** *&optional (prev nil)* \[メソッド\]

 

:   

[]() **:init** *st &key ((:cost c) 0) ((:parent p) nil) ((:action a)
nil)* \[メソッド\]

 

:   

[]() **solver** \[クラス\]

      :super   propertied-object 
      :slots         nil 

 

:   

[]() **:init** \[メソッド\]

 

:   

[]() **:solve** *prblm* \[メソッド\]

 

:   

[]() **:solve-by-name** *prblm s g &key (verbose nil)* \[メソッド\]

 

:   

[]() **:solve-by-name** *prblm s g &key (verbose nil)* \[メソッド\]

 

:   

[]() **:solve** *prblm* \[メソッド\]

 

:   

[]() **:init** \[メソッド\]

 

:   

[]() **graph-search-solver** \[クラス\]

      :super   solver 
      :slots         open-list close-list 

 

:   

[]() **:solve-init** *prblm* \[メソッド\]

 

:   

[]() **:find-node-in-close-list** *n* \[メソッド\]

 

:   

[]() **:solve** *prblm &key (verbose nil)* \[メソッド\]

 

:   

[]() **:add-to-open-list** *obj/list* \[メソッド\]

 

:   

[]() **:null-open-list?** \[メソッド\]

 

:   

[]() **:clear-open-list** \[メソッド\]

 

:   

[]() **:add-list-to-open-list** *lst* \[メソッド\]

 

:   

[]() **:add-object-to-open-list** *lst* \[メソッド\]

 

:   

[]() **:pop-from-open-list** *&key (debug)* \[メソッド\]

 

:   

[]() **:open-list** *&optional arg* \[メソッド\]

 

:   

[]() **:close-list** *&optional arg* \[メソッド\]

 

:   

[]() **:close-list** *&optional arg* \[メソッド\]

 

:   

[]() **:open-list** *&optional arg* \[メソッド\]

 

:   

[]() **:pop-from-open-list** *&key (debug)* \[メソッド\]

 

:   

[]() **:add-object-to-open-list** *lst* \[メソッド\]

 

:   

[]() **:add-list-to-open-list** *lst* \[メソッド\]

 

:   

[]() **:clear-open-list** \[メソッド\]

 

:   

[]() **:null-open-list?** \[メソッド\]

 

:   

[]() **:add-to-open-list** *obj/list* \[メソッド\]

 

:   

[]() **:solve** *prblm &key (verbose nil)* \[メソッド\]

 

:   

[]() **:find-node-in-close-list** *n* \[メソッド\]

 

:   

[]() **:solve-init** *prblm* \[メソッド\]

 

:   

[]() **breadth-first-graph-search-solver** \[クラス\]

      :super   graph-search-solver 
      :slots         nil 

 

:   

[]() **:init** \[メソッド\]

 

:   

[]() **:clear-open-list** \[メソッド\]

 

:   

[]() **:add-list-to-open-list** *lst* \[メソッド\]

 

:   

[]() **:add-object-to-open-list** *obj* \[メソッド\]

 

:   

[]() **:pop-from-open-list** *&key (debug)* \[メソッド\]

 

:   

[]() **:pop-from-open-list** *&key (debug)* \[メソッド\]

 

:   

[]() **:add-object-to-open-list** *obj* \[メソッド\]

 

:   

[]() **:add-list-to-open-list** *lst* \[メソッド\]

 

:   

[]() **:clear-open-list** \[メソッド\]

 

:   

[]() **:init** \[メソッド\]

 

:   

[]() **depth-first-graph-search-solver** \[クラス\]

      :super   graph-search-solver 
      :slots         nil 

 

:   

[]() **:init** \[メソッド\]

 

:   

[]() **:clear-open-list** \[メソッド\]

 

:   

[]() **:add-list-to-open-list** *lst* \[メソッド\]

 

:   

[]() **:add-object-to-open-list** *obj* \[メソッド\]

 

:   

[]() **:pop-from-open-list** *&key (debug)* \[メソッド\]

 

:   

[]() **:pop-from-open-list** *&key (debug)* \[メソッド\]

 

:   

[]() **:add-object-to-open-list** *obj* \[メソッド\]

 

:   

[]() **:add-list-to-open-list** *lst* \[メソッド\]

 

:   

[]() **:clear-open-list** \[メソッド\]

 

:   

[]() **:init** \[メソッド\]

 

:   

[]() **best-first-graph-search-solver** \[クラス\]

      :super   graph-search-solver 
      :slots         aproblem 

 

:   

[]() **:init** *p* \[メソッド\]

 

:   

[]() **:clear-open-list** \[メソッド\]

 

:   

[]() **:add-list-to-open-list** *lst* \[メソッド\]

 

:   

[]() **:add-object-to-open-list** *obj* \[メソッド\]

 

:   

[]() **:pop-from-open-list** *&key (debug nil)* \[メソッド\]

 

:   

[]() **:fn** *n p* \[メソッド\]

 

:   

[]() **:fn** *n p* \[メソッド\]

 

:   

[]() **:pop-from-open-list** *&key (debug nil)* \[メソッド\]

 

:   

[]() **:add-object-to-open-list** *obj* \[メソッド\]

 

:   

[]() **:add-list-to-open-list** *lst* \[メソッド\]

 

:   

[]() **:clear-open-list** \[メソッド\]

 

:   

[]() **:init** *p* \[メソッド\]

 

:   

[]() **a-graph-search-solver** \[クラス\]

      :super   best-first-graph-search-solver 
      :slots         nil 

 

:   

[]() **:init** *p* \[メソッド\]

 

:   

[]() **:fn** *n p &key (debug nil)* \[メソッド\]

 

:   

[]() **:gn** *n p* \[メソッド\]

 

:   

[]() **:hn** *n p* \[メソッド\]

 

:   

[]() **:hn** *n p* \[メソッド\]

 

:   

[]() **:gn** *n p* \[メソッド\]

 

:   

[]() **:fn** *n p &key (debug nil)* \[メソッド\]

 

:   

[]() **:init** *p* \[メソッド\]

 

:   

[irteus拡張]()
==============

[GL/X表示]()
------------

\
[]() **glviewsurface** \[クラス\]

      :super   x:xwindow 
      :slots         x::display x::drawable x::gcon x::bg-color x::width x::height x::parent x::subwindows x::visual x::backing-pixmap x::event-forward gl::x gl::y gl::visualinfo gl::glcon 

 

:   

[]() ![\\begin{emtabbing} {\\bf :getglimage} \\it\\&key \\= (gl::x 0)
\\\\lq \[method\]\\\\ \\&gt; (gl... ...iskcentered gl::width gl::height
3))) \\\\ \\&gt; (gl::depthbuf) \\rm
\\end{emtabbing}](jmanual-img233.png){width="552" height="35"}

 
:   Get current view to a image object. It returns color-image24 object.

[]() ![\\begin{emtabbing} {\\bf :write-to-image-file} \\it gl::file
\\&key \\= (gl::x 0) \\\\lq ... ...) \\\\ \\&gt; (gl::width x::width)
\\\\ \\&gt; (gl::height x::height) \\rm
\\end{emtabbing}](jmanual-img234.png){width="573" height="111"}

 
:   Write current view to file name

[]() **:flush** \[メソッド\]

 
:   send glflush

[]() ![\\begin{emtabbing} {\\bf :3d-lines} \\it gl::points \\&key \\=
(gl::depth-test t) \\\\lq \[method\]\\\\ \\&gt; (gl::lighting t) \\rm
\\end{emtabbing}](jmanual-img235.png){width="552" height="73"}

 
:   Draw 3D lines that connecting points

[]() ![\\begin{emtabbing} {\\bf :3d-line} \\it gl::start gl::end \\&key
\\= (gl::depth-test t) \\\\lq \[method\]\\\\ \\&gt; (gl::lighting t)
\\rm \\end{emtabbing}](jmanual-img236.png){width="552" height="35"}

 
:   Draw 3D line from start to end

[]() ![\\begin{emtabbing} {\\bf :3d-point} \\it pos \\&key \\=
(gl::depth-test t) \\\\lq \[method\]\\\\ \\&gt; (gl::lighting t) \\rm
\\end{emtabbing}](jmanual-img237.png){width="552" height="35"}

 
:   Draw 3D point

[]() **:point-size** *&optional gl::x* \[メソッド\]

 
:   Returns point size, if x is given, it set point size

[]() **:line-width** *&optional gl::x* \[メソッド\]

 
:   Returns line width, if x is given, it set line width

[]() **:color** *&optional gl::color-vector* \[メソッド\]

 
:   Returns color, if color-vector is given it set color

[]() **:redraw** *&rest args* \[メソッド\]

 

:   

[]() **:makecurrent** \[メソッド\]

 

:   

[]() **polygon** \[クラス\]

      :super   plane 
      :slots         normal distance convexp edges vertices model-normal model-distance 

 

:   

[]() **:draw-on** *&key ((:viewer gl::vwer) viewer) gl::flush (gl::width
1) (gl::color \#f(1.0 1.0 1.0))* \[メソッド\]

 

:   

[]() **line** \[クラス\]

      :super   propertied-object 
      :slots         pvert nvert 

 

:   

[]() **:draw-on** *&key ((:viewer gl::vwer) viewer) gl::flush (gl::width
1) (gl::color \#f(1.0 1.0 1.0))* \[メソッド\]

 

:   

[]() **faceset** \[クラス\]

      :super   cascaded-coords 
      :slots         rot pos parent descendants worldcoords manager changed box faces edges vertices model-vertices 

 

:   

[]() **:set-color** *gl::color &optional (gl::transparent)* \[メソッド\]

 
:   Set color of given color name, color sample and color name are
    referenced from http://en.wikipedia.org/wiki/X11\_color\_names

[]() **:paste-texture-to-face** *gl::aface &key gl::file gl::image
(gl::tex-coords (list (float-vector 0 0) (float-vector 0 1)
(float-vector 1 1) (float-vector 1 0)))* \[メソッド\]

 

:   

[]() **:draw-on** *&key ((:viewer gl::vwer) viewer) gl::flush (gl::width
1) (gl::color \#f(1.0 1.0 1.0))* \[メソッド\]

 

:   

[]() **coordinates** \[クラス\]

      :super   propertied-object 
      :slots         rot pos 

 

:   

[]() **:draw-on** *&key ((:viewer gl::vwer) viewer) gl::flush (gl::width
(get self :width)) (gl::color (get self :color)) (size (get self
:size))* \[メソッド\]

 

:   

[]() **:vertices** \[メソッド\]

 

:   

[]() **float-vector** \[クラス\]

      :super   vector 
      :slots         nil 

 

:   

[]() **:draw-on** *&key ((:viewer gl::vwer) viewer) gl::flush (gl::width
1) (gl::color \#f(1.0 1.0 1.0)) (size 50)* \[メソッド\]

 

:   

[]() **:vertices** \[メソッド\]

 

:   

[]() **gl::glvertices** \[クラス\]

      :super   cascaded-coords 
      :slots         gl::mesh-list gl::filename gl::bbox 

 

:   

[]() **:set-color** *gl::color &optional (gl::transparent)* \[メソッド\]

 
:   set color as float vector of 3 elements, and transparent as float,
    all values are betwenn 0 to 1

[]() **:actual-vertices** \[メソッド\]

 
:   return list of vertices(float-vector), it returns all vertices of
    this object

[]() **:calc-bounding-box** \[メソッド\]

 
:   calculate and set bounding box of this object

[]() **:vertices** \[メソッド\]

 
:   return list of vertices(float-vector), it returns vertices of
    bounding box of this object

[]() **:reset-offset-from-parent** \[メソッド\]

 
:   move vertices in this object using self transformation, this method
    change values of vertices. coordinates's method such as :transform
    just change view of this object

[]() **:expand-vertices** \[メソッド\]

 
:   expand vertices number as same number of indices, it enable to set
    individual normal to every vertices

[]() **:use-flat-shader** \[メソッド\]

 
:   use flat shader mode, use opengl function of glShadeModel(GL\_FLAT)

[]() **:use-smooth-shader** \[メソッド\]

 
:   use smooth shader mode, use opengl function
    of glShadeModel(GL\_SMOOTH) default

[]() **:calc-normals** *&optional (gl::force nil) (gl::expand t)
(gl::flat t)* \[メソッド\]

 
:   normal calculation if force option is true, clear current normalset.
    if exapnd option is ture, do :expand-vertices. if flat option is
    true, use-flat-shader

[]() ![\\begin{emtabbing} {\\bf :mirror-axis} \\it\\&key \\= (gl::create
t) \\\\lq \[method\]\\\\ \\&gt; (gl::invert-faces t) \\\\ \\&gt;
(gl::axis :y) \\rm \\end{emtabbing}](jmanual-img238.png){width="552"
height="35"}

 
:   creating mirror vertices respect to :axis

[]() ![\\begin{emtabbing} {\\bf :convert-to-faces} \\it\\&rest args
\\&key \\= (gl::wrt :local) \\\\lq \[method\]\\\\ \\&gt;
\\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img239.png){width="552" height="54"}

 
:   create list of faces using vertices of this object

[]() **:convert-to-faceset** *&rest args* \[メソッド\]

 
:   create faceset using vertices of this object

[]() **:set-offset** *gl::cds &key (gl::create)* \[メソッド\]

 
:   move vertices in this object using given coordinates, this method
    change values of vertices. coordinates's method such as :transform
    just change view of this object

[]() **:convert-to-world** *&key (gl::create)* \[メソッド\]

 
:   move vertices in this object using self's coordinates. vertices data
    should be moved as the same as displayed

[]() **:glvertices** *&optional (name) (gl::test \#'string=)*
\[メソッド\]

 
:   create individual glvertices objects from mesh-list. if name is
    given, search mesh has the same name

[]() **:append-glvertices** *gl::glv-lst* \[メソッド\]

 
:   append list of glvertices to this object

[]() **:init** *gl::mlst &rest args &key ((:filename gl::fn))
&allow-other-keys* \[メソッド\]

 

:   

[]() **:filename** *&optional gl::nm* \[メソッド\]

 

:   

[]() **:get-meshinfo** *gl::key &optional (pos -1)* \[メソッド\]

 

:   

[]() **:set-meshinfo** *gl::key gl::info &optional (pos -1)*
\[メソッド\]

 

:   

[]() **:get-material** *&optional (pos -1)* \[メソッド\]

 

:   

[]() **:set-material** *gl::mat &optional (pos -1)* \[メソッド\]

 

:   

[]() **:expand-vertices-info** *gl::minfo* \[メソッド\]

 

:   

[]() **:faces** \[メソッド\]

 

:   

[]() **:draw-on** *&key ((:viewer gl::vwer) viewer)* \[メソッド\]

 

:   

[]() **:draw** *gl::vwr &rest args* \[メソッド\]

 

:   

[]() **:collision-check-objects** *&rest args* \[メソッド\]

 

:   

[]() **:box** \[メソッド\]

 

:   

[]() **:make-pqpmodel** *&key (gl::fat 0)* \[メソッド\]

 

:   

[]() **:append-glvertices** *gl::glv-lst* \[メソッド\]

 
:   append list of glvertices to this object

[]() **:glvertices** *&optional (name) (gl::test \#'string=)*
\[メソッド\]

 
:   create individual glvertices objects from mesh-list. if name is
    given, search mesh has the same name

[]() **:convert-to-world** *&key (gl::create)* \[メソッド\]

 
:   move vertices in this object using self's coordinates. vertices data
    should be moved as the same as displayed

[]() **:set-offset** *gl::cds &key (gl::create)* \[メソッド\]

 
:   move vertices in this object using given coordinates, this method
    change values of vertices. coordinates's method such as :transform
    just change view of this object

[]() **:convert-to-faceset** *&rest args* \[メソッド\]

 
:   create faceset using vertices of this object

[]() ![\\begin{emtabbing} {\\bf :convert-to-faces} \\it\\&rest args
\\&key \\= (gl::wrt :local) \\\\lq \[method\]\\\\ \\&gt;
\\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img239.png){width="552" height="54"}

 
:   create list of faces using vertices of this object

[]() ![\\begin{emtabbing} {\\bf :mirror-axis} \\it\\&key \\= (gl::create
t) \\\\lq \[method\]\\\\ \\&gt; (gl::invert-faces t) \\\\ \\&gt;
(gl::axis :y) \\rm \\end{emtabbing}](jmanual-img238.png){width="552"
height="35"}

 
:   creating mirror vertices respect to :axis

[]() **:calc-normals** *&optional (gl::force nil) (gl::expand t)
(gl::flat t)* \[メソッド\]

 
:   normal calculation if force option is true, clear current normalset.
    if exapnd option is ture, do :expand-vertices. if flat option is
    true, use-flat-shader

[]() **:use-smooth-shader** \[メソッド\]

 
:   use smooth shader mode, use opengl function
    of glShadeModel(GL\_SMOOTH) default

[]() **:use-flat-shader** \[メソッド\]

 
:   use flat shader mode, use opengl function of glShadeModel(GL\_FLAT)

[]() **:expand-vertices** \[メソッド\]

 
:   expand vertices number as same number of indices, it enable to set
    individual normal to every vertices

[]() **:reset-offset-from-parent** \[メソッド\]

 
:   move vertices in this object using self transformation, this method
    change values of vertices. coordinates's method such as :transform
    just change view of this object

[]() **:vertices** \[メソッド\]

 
:   return list of vertices(float-vector), it returns vertices of
    bounding box of this object

[]() **:calc-bounding-box** \[メソッド\]

 
:   calculate and set bounding box of this object

[]() **:actual-vertices** \[メソッド\]

 
:   return list of vertices(float-vector), it returns all vertices of
    this object

[]() **:set-color** *gl::color &optional (gl::transparent)* \[メソッド\]

 
:   set color as float vector of 3 elements, and transparent as float,
    all values are betwenn 0 to 1

[]() **:make-pqpmodel** *&key (gl::fat 0)* \[メソッド\]

 

:   

[]() **:box** \[メソッド\]

 

:   

[]() **:collision-check-objects** *&rest args* \[メソッド\]

 

:   

[]() **:draw** *gl::vwr &rest args* \[メソッド\]

 

:   

[]() **:draw-on** *&key ((:viewer gl::vwer) viewer)* \[メソッド\]

 

:   

[]() **:faces** \[メソッド\]

 

:   

[]() **:expand-vertices-info** *gl::minfo* \[メソッド\]

 

:   

[]() **:set-material** *gl::mat &optional (pos -1)* \[メソッド\]

 

:   

[]() **:get-material** *&optional (pos -1)* \[メソッド\]

 

:   

[]() **:set-meshinfo** *gl::key gl::info &optional (pos -1)*
\[メソッド\]

 

:   

[]() **:get-meshinfo** *gl::key &optional (pos -1)* \[メソッド\]

 

:   

[]() **:filename** *&optional gl::nm* \[メソッド\]

 

:   

[]() **:init** *gl::mlst &rest args &key ((:filename gl::fn))
&allow-other-keys* \[メソッド\]

 

:   

[]() **gl::glbody** \[クラス\]

      :super   body 
      :slots         gl::aglvertices 

 

:   

[]() **:glvertices** *&rest args* \[メソッド\]

 

:   

[]() **:draw** *gl::vwr* \[メソッド\]

 

:   

[]() **:set-color** *&rest args* \[メソッド\]

 

:   

[]() **:set-color** *&rest args* \[メソッド\]

 

:   

[]() **:draw** *gl::vwr* \[メソッド\]

 

:   

[]() **:glvertices** *&rest args* \[メソッド\]

 

:   

[]() **gl::find-color** *gl::color* \[関数\]

 
:   returns color vector of given color name, the name is defiend in
    https://github.com/euslisp/jskeus/blob/master/irteus/irtglrgb.l

[]() **gl::transparent** *gl::abody gl::param* \[関数\]

 
:   Set abody to transparent with param

[]() **gl::make-glvertices-from-faceset** *gl::fs &key (gl::material)*
\[関数\]

 
:   returns glvertices instance fs is geomatry::faceset

[]() **gl::make-glvertices-from-faces** *gl::flst &key (gl::material)*
\[関数\]

 
:   returns glvertices instance flst is list of geomatry::face

[]() **gl::write-wrl-from-glvertices** *fname gl::glv &rest args*
\[関数\]

 
:   write .wrl file from instance of glvertices

[]() **gl::set-stereo-gl-attribute** \[関数\]

 

:   

[]() **gl::reset-gl-attribute** \[関数\]

 

:   

[]() **gl::delete-displaylist-id** *gl::dllst* \[関数\]

 

:   

[]() **gl::transpose-image-rows** *gl::img &optional gl::ret* \[関数\]

 

:   

[]() **gl::draw-globjects** *gl::vwr gl::draw-things &key (gl::clear t)
(gl::flush t) (gl::draw-origin 150) (gl::draw-floor nil)* \[関数\]

 

:   

[]() **gl::draw-glbody** *gl::vwr gl::abody* \[関数\]

 

:   

[]() **gl::\_dump-wrl-shape** *gl::strm gl::mesh &key ((:scale gl::scl)
1.0) (gl::use\_ambient nil) (gl::use\_normal nil) (gl::use\_texture nil)
&allow-other-keys* \[関数\]

 

:   

\
[]() **x:xwindow** \[クラス\]

      :super   x:xdrawable 
      :slots         x::display x::drawable x::gcon x::bg-color x::width x::height x::parent x::subwindows x::visual x::backing-pixmap x::event-forward 

 

:   

[]() **:buttonpress** *x:event* \[メソッド\]

 

:   

[]() **:motionnotify** *x:event* \[メソッド\]

 

:   

[]() **:buttonrelease** *x:event* \[メソッド\]

 

:   

[]() **:set-event-proc** *type x::method x::receiver* \[メソッド\]

 

:   

[]() **:clientmessage** *x:event* \[メソッド\]

 

:   

[]() **:quit** *&rest x::a* \[メソッド\]

 

:   

[]() **:event-notify** *type x:event* \[メソッド\]

 

:   

[]() **:create** *&rest args* \[メソッド\]

 

:   

[]() **x:panel** \[クラス\]

      :super   x:xwindow 
      :slots         x::display x::drawable x::gcon x::bg-color x::width x::height x::parent x::subwindows x::visual x::backing-pixmap x::event-forward x::pos x::items x::fontid x::rows x::columns x::next-x x::next-y x::item-width x::item-height x::dark-edge-color x::light-edge-color x::topleft-edge-polygon x::active-menu 

 

:   

[]() **:quit** *&rest args* \[メソッド\]

 

:   

[]() **x:xscroll-bar** \[クラス\]

      :super   x:xwindow 
      :slots         x::display x::drawable x::gcon x::bg-color x::width x::height x::parent x::subwindows x::visual x::backing-pixmap x::event-forward x::arrow-length x::handle-pos x::handle-length x::verticalp x::handle-grabbed 

 

:   

[]() **:redraw** \[メソッド\]

 

:   

[]() **x::tabbed-panel** \[クラス\]

      :super   x:panel 
      :slots         x::tabbed-buttons x::tabbed-panels x::selected-tabbed-panel 

 

:   

[]() **:create** *&rest args* \[メソッド\]

 

:   

[]() **:add-tabbed-panel** *name* \[メソッド\]

 

:   

[]() **:change-tabbed-panel** *x::obj* \[メソッド\]

 

:   

[]() **:tabbed-button** *name &rest args* \[メソッド\]

 

:   

[]() **:tabbed-panel** *name &rest args* \[メソッド\]

 

:   

[]() **:resize** *x::w h* \[メソッド\]

 

:   

[]() **:resize** *x::w h* \[メソッド\]

 

:   

[]() **:tabbed-panel** *name &rest args* \[メソッド\]

 

:   

[]() **:tabbed-button** *name &rest args* \[メソッド\]

 

:   

[]() **:change-tabbed-panel** *x::obj* \[メソッド\]

 

:   

[]() **:add-tabbed-panel** *name* \[メソッド\]

 

:   

[]() **:create** *&rest args* \[メソッド\]

 

:   

[]() **x::panel-tab-button-item** \[クラス\]

      :super   x:button-item 
      :slots         nil 

 

:   

[]() **:draw-label** *&optional (x::state :up) (x::offset 0)*
\[メソッド\]

 

:   

[]() **:draw-label** *&optional (x::state :up) (x::offset 0)*
\[メソッド\]

 

:   

[]() **x::window-main-one** *&optional fd* \[関数\]

 

:   

[]() **x::event-far** *x::e* \[関数\]

 

:   

[]() **x::event-near** *x::e* \[関数\]

 

:   

[ユーティリティ関数]()
----------------------

\
[]() **mtimer** \[クラス\]

      :super   object 
      :slots         buf 

 

:   

[]() **:init** \[メソッド\]

 
:   Initialize timer object.

[]() **:start** \[メソッド\]

 
:   Start timer.

[]() **:stop** \[メソッド\]

 
:   Stop timer and returns elapsed time in seconds.

[]() **:stop** \[メソッド\]

 
:   Stop timer and returns elapsed time in seconds.

[]() **:start** \[メソッド\]

 
:   Start timer.

[]() **:init** \[メソッド\]

 
:   Initialize timer object.

[]() **interpolator** \[クラス\]

      :super   propertied-object 
      :slots         (position-list :type cons) (time-list :type cons) (position :type float-vector) (time :type float) (segment-num :type integer) (segment-time :type float) (segment :type integer) (interpolatingp :type symbol) 

 

:   

[]() **:init** \[メソッド\]

 
:   Abstract class of interpolator

[]() ![\\begin{emtabbing} {\\bf :reset} \\it\\&rest args \\&key \\=
((:position-list pl) (se... ...-list tl) (send self :time-list)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img240.png){width="552" height="34"}

 
:   Initialize interpolator position-list: list of control point
    time-list: list of time from start for each control point, time in
    fisrt contrall point is zero, so length of this list is length of
    control point minus 1

[]() **:position-list** \[メソッド\]

 
:   returns position list

[]() **:position** \[メソッド\]

 
:   returns current position

[]() **:time-list** \[メソッド\]

 
:   returns time list

[]() **:time** \[メソッド\]

 
:   returns current time

[]() **:segment-time** \[メソッド\]

 
:   returns time\[sec\] with in each segment

[]() **:segment** \[メソッド\]

 
:   returns index of segment which is currently processing

[]() **:segment-num** \[メソッド\]

 
:   returns number of total segment

[]() **:interpolatingp** \[メソッド\]

 
:   returns if it is currently processing

[]() **:start-interpolation** \[メソッド\]

 
:   start interpolation

[]() **:stop-interpolation** \[メソッド\]

 
:   stop interpolation

[]() **:pass-time** *dt* \[メソッド\]

 
:   process interpolation for dt\[sec\]

[]() **:pass-time** *dt* \[メソッド\]

 
:   process interpolation for dt\[sec\]

[]() **:stop-interpolation** \[メソッド\]

 
:   stop interpolation

[]() **:start-interpolation** \[メソッド\]

 
:   start interpolation

[]() **:interpolatingp** \[メソッド\]

 
:   returns if it is currently processing

[]() **:segment-num** \[メソッド\]

 
:   returns number of total segment

[]() **:segment** \[メソッド\]

 
:   returns index of segment which is currently processing

[]() **:segment-time** \[メソッド\]

 
:   returns time\[sec\] with in each segment

[]() **:time** \[メソッド\]

 
:   returns current time

[]() **:time-list** \[メソッド\]

 
:   returns time list

[]() **:position** \[メソッド\]

 
:   returns current position

[]() **:position-list** \[メソッド\]

 
:   returns position list

[]() ![\\begin{emtabbing} {\\bf :reset} \\it\\&rest args \\&key \\=
((:position-list pl) (se... ...-list tl) (send self :time-list)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img240.png){width="552" height="34"}

 
:   Initialize interpolator position-list: list of control point
    time-list: list of time from start for each control point, time in
    fisrt contrall point is zero, so length of this list is length of
    control point minus 1

[]() **:init** \[メソッド\]

 
:   Abstract class of interpolator

[]() **linear-interpolator** \[クラス\]

      :super   interpolator 
      :slots         nil 

 

:   

[]() **:interpolation** \[メソッド\]

 
:   Linear interpolator

[]() **:interpolation** \[メソッド\]

 
:   Linear interpolator

[]() **minjerk-interpolator** \[クラス\]

      :super   interpolator 
      :slots         velocity acceleration velocity-list acceleration-list 

 

:   

[]() **:velocity** \[メソッド\]

 
:   returns current velocity

[]() **:velocity-list** \[メソッド\]

 
:   returns velocity list

[]() **:acceleration** \[メソッド\]

 
:   returns current acceleration

[]() **:acceleration-list** \[メソッド\]

 
:   returns acceleration list

[]() ![\\begin{emtabbing} {\\bf :reset} \\it\\&rest args \\&key \\=
((:velocity-list vl) (se... ...) (send self :acceleration-list)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img241.png){width="552" height="53"}

 
:   minjerk interopolator position-list : list of control point
    velocity-list : list of velocity in each control point
    acceleration-list : list of acceleration in each control point

[]() **:interpolation** \[メソッド\]

 
:   Minjerk interpolator, a.k.a Hoff & Arbib Example code is: (setq l
    (instance minjerk-interpolator :init)) (send l :reset :position-list
    (list \#f(1 2 3) \#f(3 4 5) \#f(1 2 3)) :time-list (list 0.1 0.18))
    (send l :start-interpolation) (while (send l :interpolatingp) (send
    l :pass-time 0.02) (print (send l :position)))

[]() **:interpolation** \[メソッド\]

 
:   Minjerk interpolator, a.k.a Hoff & Arbib Example code is: (setq l
    (instance minjerk-interpolator :init)) (send l :reset :position-list
    (list \#f(1 2 3) \#f(3 4 5) \#f(1 2 3)) :time-list (list 0.1 0.18))
    (send l :start-interpolation) (while (send l :interpolatingp) (send
    l :pass-time 0.02) (print (send l :position)))

[]() ![\\begin{emtabbing} {\\bf :reset} \\it\\&rest args \\&key \\=
((:velocity-list vl) (se... ...) (send self :acceleration-list)) \\\\
\\&gt; \\&allow-other-keys \\rm
\\end{emtabbing}](jmanual-img241.png){width="552" height="53"}

 
:   minjerk interopolator position-list : list of control point
    velocity-list : list of velocity in each control point
    acceleration-list : list of acceleration in each control point

[]() **:acceleration-list** \[メソッド\]

 
:   returns acceleration list

[]() **:acceleration** \[メソッド\]

 
:   returns current acceleration

[]() **:velocity-list** \[メソッド\]

 
:   returns velocity list

[]() **:velocity** \[メソッド\]

 
:   returns current velocity

[]() **forward-message-to** *to args* \[関数\]

 
:   forward \_args\_ message to \_to\_ object

[]() **forward-message-to-all** *to args* \[関数\]

 
:   forward \_args\_ message to all \_to\_ object

[]() **permutation** *lst n* \[関数\]

 
:   Returns permutation of given list

[]() **combination** *lst n* \[関数\]

 
:   Returns combination of given list

[]() ![\\begin{emtabbing} {\\bf find-extreams} \\it datum \\&key \\=
(key \\char93 'identity... ...identity \\char93 '=) \\\\ \\&gt; (bigger
\\char93 '\\textgreater ) \\rm
\\end{emtabbing}](jmanual-img242.png){width="552" height="53"}

 
:   Returns the elements of datum which maximizes key function

[]() **eus-server** *&optional (port 6666) &key (host
(unix:gethostname))* \[関数\]

 
:   Create euslisp interpreter server, data sent to socket is evaluated
    as lisp expression

[]() ![\\begin{emtabbing} {\\bf connect-server-until-success} \\it host
port \\&key \\= (max-port (+ port 20)) \\\\lq \[function\]\\\\ \\&gt;
(return-with-port nil) \\rm
\\end{emtabbing}](jmanual-img243.png){width="553" height="54"}

 
:   Connect euslisp interpreter server until success

[]() **format-array** *arr &optional (header ) (in 7) (fl 3) (strm
error-output) (use-line-break t)* \[関数\]

 
:   print formatted array

[]() **his2rgb** *h &optional (i 1.0) (s 1.0) ret* \[関数\]

 
:   convert his to rgb (0 &lt;= h &lt;= 360, 0.0 &lt;= i &lt;= 1.0, 0.0
    &lt;= s &lt;= 1.0)

[]() **hvs2rgb** *h &optional (i 1.0) (s 1.0) ret* \[関数\]

 
:   convert hvs to rgb (0 &lt;= h &lt;= 360, 0.0 &lt;= i &lt;= 1.0, 0.0
    &lt;= s &lt;= 1.0)

[]() **rgb2his** *r &optional g b ret* \[関数\]

 
:   convert rgb to his (0 &lt;= r,g,b &lt;= 255)

[]() **rgb2hvs** *r &optional g b ret* \[関数\]

 
:   convert rt to hvs (0 &lt;= r,g,b &lt;= 255)

[]() **color-category10** *i* \[関数\]

 
:   Choose good color from 10 colors

[]() **color-category20** *i* \[関数\]

 
:   Choose good color from 20 colors

[]() **kbhit** \[関数\]

 
:   Checks the console for a keystroke. retuns keycode value if a key
    has been pressed, otherwise it returns nil. Note that this does not
    work well on Emacs Shell mode, run EusLisp program from
    terminal shell.

[]() **piped-fork-returns-list** *cmd &optional args* \[関数\]

 
:   piped fork returning result as list

[]() **make-robot-model-from-name** *name &rest args* \[関数\]

 
:   make a robot model from string: (make-robot-model ''pr2'')

[]() **mapjoin** *expr seq1 seq2* \[関数\]

 

:   

[]() **need-thread** *n &optional (lsize (512 1024)) (csize lsize)*
\[関数\]

 

:   

[]() **termios-c\_iflag** *lisp::s* \[関数\]

 

:   

[]() **set-termios-c\_iflag** *lisp::s lisp::val* \[関数\]

 

:   

[]() **termios-c\_oflag** *lisp::s* \[関数\]

 

:   

[]() **set-termios-c\_oflag** *lisp::s lisp::val* \[関数\]

 

:   

[]() **termios-c\_cflag** *lisp::s* \[関数\]

 

:   

[]() **set-termios-c\_cflag** *lisp::s lisp::val* \[関数\]

 

:   

[]() **termios-c\_lflag** *lisp::s* \[関数\]

 

:   

[]() **set-termios-c\_lflag** *lisp::s lisp::val* \[関数\]

 

:   

[]() **termios-c\_line** *lisp::s* \[関数\]

 

:   

[]() **set-termios-c\_line** *lisp::s lisp::val* \[関数\]

 

:   

[]() **termios-c\_cc** *lisp::s &optional lisp::i* \[関数\]

 

:   

[]() **set-termios-c\_cc** *lisp::s lisp::i &rest lisp::val* \[関数\]

 

:   

[]() **termios-c\_ispeed** *lisp::s* \[関数\]

 

:   

[]() **set-termios-c\_ispeed** *lisp::s lisp::val* \[関数\]

 

:   

[]() **termios-c\_ospeed** *lisp::s* \[関数\]

 

:   

[]() **set-termios-c\_ospeed** *lisp::s lisp::val* \[関数\]

 

:   

[数学関数]()
------------

[]() **inverse-matrix** *mat* \[関数\]

 
:   returns inverse matrix of mat

[]() **diagonal** *v* \[関数\]

 
:   make diagonal matrix from given vecgtor, diagonal \#f(1 2)
    -&gt;\#2f((1 0)(0 2))

[]() **minor-matrix** *m ic jc* \[関数\]

 
:   return a matrix removing ic row and jc col elements from m

[]() **atan2** *y x* \[関数\]

 
:   returns atan2 of y and x (atan (/ y x))

[]() **outer-product-matrix** *v &optional (ret (unit-matrix 3))*
\[関数\]

 
:   returns outer product matrix of given v
    `  matrix(a) v = a v    0 -w2 w1   w2 0 -w0     -w1 w0  0     `

[]() **matrix2quaternion** *m* \[関数\]

 
:   returns quaternion of given matrix

[]() **quaternion2matrix** *q* \[関数\]

 
:   returns matrix of given quaternion

[]() **matrix-log** *m* \[関数\]

 
:   returns matrix log of given m, it returns \[-pi, pi\]

[]() **matrix-exponent** *omega &optional (p 1.0)* \[関数\]

 
:   returns exponent of given omega

[]() **midrot** *p r1 r2* \[関数\]

 
:   returns mid (or p) rotation matrix of given two matrix r1 and r1

[]() **pseudo-inverse** *mat &optional weight-vector ret wmat mat-tmp*
\[関数\]

 
:   returns pseudo inverse of given mat

[]() **sr-inverse** *mat &optional (k 1.0) weight-vector ret wmat tmat
umat umat2 mat-tmp mat-tmp-rc mat-tmp-rr mat-tmp-rr2* \[関数\]

 
:   returns sr-inverse of given mat

[]() **manipulability** *jacobi &optional tmp-mrr tmp-mcr* \[関数\]

 
:   return manipulability of given matrix

[]() **random-gauss** *&optional (m 0) (s 1)* \[関数\]

 
:   make random gauss, m:mean s:standard-deviation

[]() **gaussian-random** *dim &optional (m 0) (s 1)* \[関数\]

 
:   make random gauss vector, replacement for quasi-random defined in
    matlib.c

[]() **concatenate-matrix-column** *&rest args* \[関数\]

 
:   Concatenate matrix in column direction.

[]() **concatenate-matrix-row** *&rest args* \[関数\]

 
:   Concatenate matrix in row direction.

[]() **concatenate-matrix-diagonal** *&rest args* \[関数\]

 
:   Concatenate matrix in diagonal.

[]() **vector-variance** *vector-list* \[関数\]

 
:   returns vector, each element represents variance of elements in the
    same index of vector within vector-list

[]() **covariance-matrix** *vector-list* \[関数\]

 
:   make covariance matrix of given input vector-list

[]() **normalize-vector** *v &optional r (eps 1.000000e-20)* \[関数\]

 
:   calculate normalize-vector \#f(0 0 0)-&gt;\#f(0 0 0).

[]() **pseudo-inverse-org** *m &optional ret winv mat-tmp-cr* \[関数\]

 

:   

[]() **sr-inverse-org** *mat &optional (k 1) me mat-tmp-cr mat-tmp-rr*
\[関数\]

 

:   

[]() **eigen-decompose** *m* \[関数\]

 

:   

[]() **lms** *point-list* \[関数\]

 

:   

[]() **lms-estimate** *res point-* \[関数\]

 

:   

[]() **lms-error** *result point-list* \[関数\]

 

:   

[]() **lmeds** *point-list &key (num 5) (err-rate 0.3) (iteration)
(ransac-threshold) (lms-func \#'lms) (lmeds-error-func \#'lmeds-error)
(lms-estimate-func \#'lms-estimate)* \[関数\]

 

:   

[]() **lmeds-error** *result point-list &key (lms-estimate-func
\#'lms-estimate)* \[関数\]

 

:   

[]() **lmeds-error-mat** *result mat &key (lms-estimate-func
\#'lms-estimate)* \[関数\]

 

:   

[画像関数]()
------------

[]() **read-image-file** *fname* \[関数\]

 
:   read image of given fname. It returns instance of
    **grayscale-image** or **color-image24**.

[]() **write-image-file** *fname image::img* \[関数\]

 
:   write img to given fname

[]() **read-png-file** *fname* \[関数\]

 

:   

[]() **write-png-file** *fname image::img* \[関数\]

 

:   

[Index]()
---------

**6dof-joint**

[ロボットモデル](jmanual.html#6331) |
[動力学計算・歩行動作生成](jmanual.html#17745)

**:3d-line**

[GL/X表示](jmanual.html#34457)

**:3d-lines**

[GL/X表示](jmanual.html#34446)

**:3d-point**

[センサモデル](jmanual.html#15408) | [センサモデル](jmanual.html#15577)
| [GL/X表示](jmanual.html#34468)

**:acceleration**

[ユーティリティ関数](jmanual.html#37976) |
[ユーティリティ関数](jmanual.html#38048)

**:acceleration-list**

[ユーティリティ関数](jmanual.html#37986) |
[ユーティリティ関数](jmanual.html#38038)

**:action**

[グラフ表現](jmanual.html#31458) | [グラフ表現](jmanual.html#31469)

**:actual-vertices**

[GL/X表示](jmanual.html#34747) | [GL/X表示](jmanual.html#35186)

**:add-arc**

[グラフ表現](jmanual.html#30283) | [グラフ表現](jmanual.html#30382) |
[グラフ表現](jmanual.html#30962) | [グラフ表現](jmanual.html#31017) |
[グラフ表現](jmanual.html#31090) | [グラフ表現](jmanual.html#31123)

**:add-arc-from-to**

[グラフ表現](jmanual.html#30717) | [グラフ表現](jmanual.html#30794) |
[グラフ表現](jmanual.html#30973) | [グラフ表現](jmanual.html#31006) |
[グラフ表現](jmanual.html#31101) | [グラフ表現](jmanual.html#31112)

**:add-child-links**

[ロボットモデル](jmanual.html#6645) |
[ロボットモデル](jmanual.html#6726)

**:add-joint**

[ロボットモデル](jmanual.html#6605) |
[ロボットモデル](jmanual.html#6766)

**:add-list-to-open-list**

[グラフ表現](jmanual.html#31714) | [グラフ表現](jmanual.html#31813) |
[グラフ表現](jmanual.html#31930) | [グラフ表現](jmanual.html#31985) |
[グラフ表現](jmanual.html#32058) | [グラフ表現](jmanual.html#32113) |
[グラフ表現](jmanual.html#32186) | [グラフ表現](jmanual.html#32263)

**:add-neighbor**

[グラフ表現](jmanual.html#30327) | [グラフ表現](jmanual.html#31279)

**:add-node**

[グラフ表現](jmanual.html#30684) | [グラフ表現](jmanual.html#30827)

**:add-object**

[環境モデル](jmanual.html#16457) | [環境モデル](jmanual.html#16678)

**:add-object-to-open-list**

[グラフ表現](jmanual.html#31725) | [グラフ表現](jmanual.html#31802) |
[グラフ表現](jmanual.html#31941) | [グラフ表現](jmanual.html#31974) |
[グラフ表現](jmanual.html#32069) | [グラフ表現](jmanual.html#32102) |
[グラフ表現](jmanual.html#32197) | [グラフ表現](jmanual.html#32252)

**:add-objects**

[環境モデル](jmanual.html#16447) | [環境モデル](jmanual.html#16688)

**:add-parent-link**

[ロボットモデル](jmanual.html#6655) |
[ロボットモデル](jmanual.html#6716)

**:add-spot**

[環境モデル](jmanual.html#16507) | [環境モデル](jmanual.html#16628)

**:add-spots**

[環境モデル](jmanual.html#16497) | [環境モデル](jmanual.html#16638)

**:add-tabbed-panel**

[GL/X表示](jmanual.html#36761) | [GL/X表示](jmanual.html#36860)

**:add-to-open-list**

[グラフ表現](jmanual.html#31681) | [グラフ表現](jmanual.html#31846)

**:adjacency-list**

[グラフ表現](jmanual.html#30750) | [グラフ表現](jmanual.html#30761)

**:adjacency-matrix**

[グラフ表現](jmanual.html#30739) | [グラフ表現](jmanual.html#30772)

**:analysis-level**

[ロボットモデル](jmanual.html#6555) |
[ロボットモデル](jmanual.html#6816)

**:angle-to-speed**

[ロボットモデル](jmanual.html#5113) |
[ロボットモデル](jmanual.html#5300) |
[ロボットモデル](jmanual.html#5518) |
[ロボットモデル](jmanual.html#5550) |
[ロボットモデル](jmanual.html#5684) |
[ロボットモデル](jmanual.html#5716) |
[ロボットモデル](jmanual.html#5850) |
[ロボットモデル](jmanual.html#5882) |
[ロボットモデル](jmanual.html#6016) |
[ロボットモデル](jmanual.html#6048) |
[ロボットモデル](jmanual.html#6192) |
[ロボットモデル](jmanual.html#6246) |
[ロボットモデル](jmanual.html#6400) |
[ロボットモデル](jmanual.html#6432)

**:angle-vector**

[ロボットモデル](jmanual.html#6967) |
[ロボットモデル](jmanual.html#7475) | [BVHデータ](jmanual.html#23630) |
[BVHデータ](jmanual.html#23828)

**:angular-acceleration**

[動力学計算・歩行動作生成](jmanual.html#18244)

**:angular-velocity**

[動力学計算・歩行動作生成](jmanual.html#18255)

**:animate**

[BVHデータ](jmanual.html#23923) | [BVHデータ](jmanual.html#23934)

**:animation**

[BVHデータ](jmanual.html#23890) | [BVHデータ](jmanual.html#23967)

**:append**

[ポイントクラウドデータ](jmanual.html#28015) |
[ポイントクラウドデータ](jmanual.html#28459)

**:append-centroid-no-update**

[動力学計算・歩行動作生成](jmanual.html#17835)

**:append-glvertices**

[GL/X表示](jmanual.html#34890) | [GL/X表示](jmanual.html#35043)

**:append-inertia-no-update**

[動力学計算・歩行動作生成](jmanual.html#17824)

**:append-mass-properties**

[動力学計算・歩行動作生成](jmanual.html#17813)

**:append-weight-no-update**

[動力学計算・歩行動作生成](jmanual.html#17846)

**:arc-list**

[グラフ表現](jmanual.html#30261) | [グラフ表現](jmanual.html#30404)

**:arms**

[ロボットモデル](jmanual.html#13074) |
[ロボットモデル](jmanual.html#13383)

**:axis**

[ロボットモデル](jmanual.html#10966)

**:axis-length**

[ポイントクラウドデータ](jmanual.html#28231) |
[ポイントクラウドデータ](jmanual.html#28703)

**:axis-order**

[BVHデータ](jmanual.html#23352) | [BVHデータ](jmanual.html#23396) |
[BVHデータ](jmanual.html#23502) | [BVHデータ](jmanual.html#23535)

**:axis-width**

[ポイントクラウドデータ](jmanual.html#28242) |
[ポイントクラウドデータ](jmanual.html#28692)

**:bodies**

[ロボットモデル](jmanual.html#11268) |
[ロボットモデル](jmanual.html#11356) |
[ロボットモデル](jmanual.html#6947) |
[ロボットモデル](jmanual.html#7495) | [環境モデル](jmanual.html#16567) |
[環境モデル](jmanual.html#16719)

**:box**

[ポイントクラウドデータ](jmanual.html#28110) |
[ポイントクラウドデータ](jmanual.html#28824) |
[GL/X表示](jmanual.html#35021) | [GL/X表示](jmanual.html#35217)

**:buttonpress**

[GL/X表示](jmanual.html#36586)

**:buttonrelease**

[GL/X表示](jmanual.html#36608)

**:bvh-offset-rotate**

[BVHデータ](jmanual.html#23707) | [BVHデータ](jmanual.html#23751)

**:bvh-offset-rotation**

[BVHデータ](jmanual.html#23363) | [BVHデータ](jmanual.html#23385) |
[BVHデータ](jmanual.html#23513) | [BVHデータ](jmanual.html#23524)

**:calc-angle-speed-gain**

[ロボットモデル](jmanual.html#5528) |
[ロボットモデル](jmanual.html#5613) |
[ロボットモデル](jmanual.html#5694) |
[ロボットモデル](jmanual.html#5779) |
[ロボットモデル](jmanual.html#5860) |
[ロボットモデル](jmanual.html#5945) |
[ロボットモデル](jmanual.html#6026) |
[ロボットモデル](jmanual.html#6111) |
[ロボットモデル](jmanual.html#6213) |
[ロボットモデル](jmanual.html#6319) |
[ロボットモデル](jmanual.html#6410) |
[ロボットモデル](jmanual.html#6505)

**:calc-angular-acceleration-jacobian**

[動力学計算・歩行動作生成](jmanual.html#17970) |
[動力学計算・歩行動作生成](jmanual.html#18032) |
[動力学計算・歩行動作生成](jmanual.html#18094)

**:calc-angular-velocity-jacobian**

[動力学計算・歩行動作生成](jmanual.html#17992) |
[動力学計算・歩行動作生成](jmanual.html#18054) |
[動力学計算・歩行動作生成](jmanual.html#18116)

**:calc-av-vel-acc-from-pos**

[動力学計算・歩行動作生成](jmanual.html#18413)

**:calc-bounding-box**

[GL/X表示](jmanual.html#34757) | [GL/X表示](jmanual.html#35176)

**:calc-cog-jacobian-from-link-list**

[動力学計算・歩行動作生成](jmanual.html#17919)

**:calc-contact-wrenches-from-total-wrench**

[動力学計算・歩行動作生成](jmanual.html#18358)

**:calc-cop-from-force-moment**

[動力学計算・歩行動作生成](jmanual.html#18391)

**:calc-current-refzmp**

[動力学計算・歩行動作生成](jmanual.html#19347) |
[動力学計算・歩行動作生成](jmanual.html#19512)

**:calc-current-swing-leg-coords**

[動力学計算・歩行動作生成](jmanual.html#19325) |
[動力学計算・歩行動作生成](jmanual.html#19534)

**:calc-dav-gain**

[ロボットモデル](jmanual.html#5173) |
[ロボットモデル](jmanual.html#5447)

**:calc-f**

[動力学計算・歩行動作生成](jmanual.html#18672) |
[動力学計算・歩行動作生成](jmanual.html#18818) |
[動力学計算・歩行動作生成](jmanual.html#18868) |
[動力学計算・歩行動作生成](jmanual.html#18944)

**:calc-force-from-joint-torque**

[ロボットモデル](jmanual.html#12652) |
[ロボットモデル](jmanual.html#13205)

**:calc-gradh-from-link-list**

[ロボットモデル](jmanual.html#7202) |
[ロボットモデル](jmanual.html#7979)

**:calc-grasp-matrix**

[ロボットモデル](jmanual.html#7030) |
[ロボットモデル](jmanual.html#7693)

**:calc-hoffarbib-pos-vel-acc**

[動力学計算・歩行動作生成](jmanual.html#19314) |
[動力学計算・歩行動作生成](jmanual.html#19545)

**:calc-inertia-matrix**

[動力学計算・歩行動作生成](jmanual.html#17617) |
[動力学計算・歩行動作生成](jmanual.html#17646) |
[動力学計算・歩行動作生成](jmanual.html#17675) |
[動力学計算・歩行動作生成](jmanual.html#17704) |
[動力学計算・歩行動作生成](jmanual.html#17733) |
[動力学計算・歩行動作生成](jmanual.html#17762)

**:calc-inertia-matrix-column**

[動力学計算・歩行動作生成](jmanual.html#17791)

**:calc-inertia-matrix-from-link-list**

[動力学計算・歩行動作生成](jmanual.html#17930)

**:calc-inverse-jacobian**

[ロボットモデル](jmanual.html#7191) |
[ロボットモデル](jmanual.html#7990)

**:calc-inverse-kinematics-nspace-from-link-list**

[ロボットモデル](jmanual.html#7356) |
[ロボットモデル](jmanual.html#7825)

**:calc-inverse-kinematics-weight-from-link-list**

[ロボットモデル](jmanual.html#7334) |
[ロボットモデル](jmanual.html#7847)

**:calc-jacobian**

[ロボットモデル](jmanual.html#5184) |
[ロボットモデル](jmanual.html#5436) |
[ロボットモデル](jmanual.html#5539) |
[ロボットモデル](jmanual.html#5602) |
[ロボットモデル](jmanual.html#5705) |
[ロボットモデル](jmanual.html#5768) |
[ロボットモデル](jmanual.html#5871) |
[ロボットモデル](jmanual.html#5934) |
[ロボットモデル](jmanual.html#6037) |
[ロボットモデル](jmanual.html#6100) |
[ロボットモデル](jmanual.html#6224) |
[ロボットモデル](jmanual.html#6308) |
[ロボットモデル](jmanual.html#6421) |
[ロボットモデル](jmanual.html#6494)

**:calc-jacobian-for-interlocking-joints**

[ロボットモデル](jmanual.html#7052) |
[ロボットモデル](jmanual.html#7672)

**:calc-jacobian-from-link-list**

[ロボットモデル](jmanual.html#6997) |
[ロボットモデル](jmanual.html#7726)

**:calc-joint-angle-speed**

[ロボットモデル](jmanual.html#7213) |
[ロボットモデル](jmanual.html#7968)

**:calc-joint-angle-speed-gain**

[ロボットモデル](jmanual.html#7224) |
[ロボットモデル](jmanual.html#7957)

**:calc-normals**

[GL/X表示](jmanual.html#34818) | [GL/X表示](jmanual.html#35115)

**:calc-nspace-from-joint-limit**

[ロボットモデル](jmanual.html#7345) |
[ロボットモデル](jmanual.html#7836)

**:calc-one-tick-gait-parameter**

[動力学計算・歩行動作生成](jmanual.html#19358) |
[動力学計算・歩行動作生成](jmanual.html#19501)

**:calc-ratio-from-double-support-ratio**

[動力学計算・歩行動作生成](jmanual.html#19336) |
[動力学計算・歩行動作生成](jmanual.html#19523)

**:calc-root-coords-vel-acc-from-pos**

[動力学計算・歩行動作生成](jmanual.html#18424)

**:calc-spacial-acceleration-jacobian**

[動力学計算・歩行動作生成](jmanual.html#17981) |
[動力学計算・歩行動作生成](jmanual.html#18043) |
[動力学計算・歩行動作生成](jmanual.html#18105)

**:calc-spacial-velocity-jacobian**

[動力学計算・歩行動作生成](jmanual.html#18003) |
[動力学計算・歩行動作生成](jmanual.html#18065) |
[動力学計算・歩行動作生成](jmanual.html#18127)

**:calc-static-balance-point**

[ロボットモデル](jmanual.html#12799) |
[ロボットモデル](jmanual.html#13658)

**:calc-target-axis-dimension**

[ロボットモデル](jmanual.html#7158) |
[ロボットモデル](jmanual.html#8023)

**:calc-target-joint-dimension**

[ロボットモデル](jmanual.html#7180) |
[ロボットモデル](jmanual.html#8001)

**:calc-torque**

[動力学計算・歩行動作生成](jmanual.html#18457)

**:calc-torque-buffer-args**

[動力学計算・歩行動作生成](jmanual.html#18468)

**:calc-torque-from-ext-wrenches**

[動力学計算・歩行動作生成](jmanual.html#18402)

**:calc-torque-from-vel-acc**

[動力学計算・歩行動作生成](jmanual.html#18435)

**:calc-torque-without-ext-wrench**

[動力学計算・歩行動作生成](jmanual.html#18446)

**:calc-u**

[動力学計算・歩行動作生成](jmanual.html#18683) |
[動力学計算・歩行動作生成](jmanual.html#18807) |
[動力学計算・歩行動作生成](jmanual.html#18879) |
[動力学計算・歩行動作生成](jmanual.html#18933)

**:calc-union-link-list**

[ロボットモデル](jmanual.html#7169) |
[ロボットモデル](jmanual.html#8012)

**:calc-vel-for-cog**

[動力学計算・歩行動作生成](jmanual.html#17897)

**:calc-vel-for-interlocking-joints**

[ロボットモデル](jmanual.html#7062) |
[ロボットモデル](jmanual.html#7662)

**:calc-vel-from-pos**

[ロボットモデル](jmanual.html#7411) |
[ロボットモデル](jmanual.html#7770)

**:calc-vel-from-rot**

[ロボットモデル](jmanual.html#7422) |
[ロボットモデル](jmanual.html#7759)

**:calc-walk-pattern-from-footstep-list**

[ロボットモデル](jmanual.html#12727) |
[ロボットモデル](jmanual.html#13730)

**:calc-weight-from-joint-limit**

[ロボットモデル](jmanual.html#7323) |
[ロボットモデル](jmanual.html#7858)

**:calc-xk**

[動力学計算・歩行動作生成](jmanual.html#18694) |
[動力学計算・歩行動作生成](jmanual.html#18796) |
[動力学計算・歩行動作生成](jmanual.html#18890) |
[動力学計算・歩行動作生成](jmanual.html#18922)

**:calc-zmp**

[動力学計算・歩行動作生成](jmanual.html#18325)

**:calc-zmp-from-forces-moments**

[ロボットモデル](jmanual.html#12684) |
[ロボットモデル](jmanual.html#13173)

**:camera**

[ロボットモデル](jmanual.html#12529) |
[ロボットモデル](jmanual.html#13329)

**:cameras**

[ロボットモデル](jmanual.html#12579) |
[ロボットモデル](jmanual.html#13279)

**:cart-zmp**

[動力学計算・歩行動作生成](jmanual.html#18994) |
[動力学計算・歩行動作生成](jmanual.html#19155)

**:centroid**

[ロボットモデル](jmanual.html#6575) |
[ロボットモデル](jmanual.html#6796) |
[動力学計算・歩行動作生成](jmanual.html#18305) |
[ポイントクラウドデータ](jmanual.html#28005) |
[ポイントクラウドデータ](jmanual.html#28469)

**:change-background**

[ロボットビューワ](jmanual.html#21680) |
[ロボットビューワ](jmanual.html#21691)

**:change-tabbed-panel**

[GL/X表示](jmanual.html#36772) | [GL/X表示](jmanual.html#36849)

**:channels**

[BVHデータ](jmanual.html#23236) | [BVHデータ](jmanual.html#23257)

**:check-interlocking-joint-angle-validity**

[ロボットモデル](jmanual.html#7092) |
[ロボットモデル](jmanual.html#7631)

**:child-link**

[ロボットモデル](jmanual.html#5083) |
[ロボットモデル](jmanual.html#5330)

**:child-links**

[ロボットモデル](jmanual.html#6635) |
[ロボットモデル](jmanual.html#6736)

**:clear-color**

[ポイントクラウドデータ](jmanual.html#28253) |
[ポイントクラウドデータ](jmanual.html#28681)

**:clear-nodes**

[グラフ表現](jmanual.html#30706) | [グラフ表現](jmanual.html#30805)

**:clear-normal**

[ポイントクラウドデータ](jmanual.html#28264) |
[ポイントクラウドデータ](jmanual.html#28670)

**:clear-open-list**

[グラフ表現](jmanual.html#31703) | [グラフ表現](jmanual.html#31824) |
[グラフ表現](jmanual.html#31919) | [グラフ表現](jmanual.html#31996) |
[グラフ表現](jmanual.html#32047) | [グラフ表現](jmanual.html#32124) |
[グラフ表現](jmanual.html#32175) | [グラフ表現](jmanual.html#32274)

**:clientmessage**

[GL/X表示](jmanual.html#36630)

**:close-list**

[グラフ表現](jmanual.html#31758) | [グラフ表現](jmanual.html#31769)

**:cog-convergence-check**

[動力学計算・歩行動作生成](jmanual.html#17875)

**:cog-jacobian-balance-nspace**

[動力学計算・歩行動作生成](jmanual.html#17908)

**:cog-z**

[動力学計算・歩行動作生成](jmanual.html#19064) |
[動力学計算・歩行動作生成](jmanual.html#19085)

**:collision-avoidance**

[ロボットモデル](jmanual.html#7279) |
[ロボットモデル](jmanual.html#7902)

**:collision-avoidance-args**

[ロボットモデル](jmanual.html#7268) |
[ロボットモデル](jmanual.html#7913)

**:collision-avoidance-calc-distance**

[ロボットモデル](jmanual.html#7257) |
[ロボットモデル](jmanual.html#7924)

**:collision-avoidance-link-pair-from-link-list**

[ロボットモデル](jmanual.html#7246) |
[ロボットモデル](jmanual.html#7935)

**:collision-avoidance-links**

[ロボットモデル](jmanual.html#7235) |
[ロボットモデル](jmanual.html#7946)

**:collision-check-objects**

[GL/X表示](jmanual.html#35010) | [GL/X表示](jmanual.html#35228)

**:collision-check-pairs**

[ロボットモデル](jmanual.html#7433) |
[ロボットモデル](jmanual.html#7748)

**:color**

[GL/X表示](jmanual.html#34499)

**:color-list**

[ポイントクラウドデータ](jmanual.html#27985) |
[ポイントクラウドデータ](jmanual.html#28489)

**:colors**

[ポイントクラウドデータ](jmanual.html#27955) |
[ポイントクラウドデータ](jmanual.html#28519)

**:configurenotify**

[ロボットビューワ](jmanual.html#21537) |
[ロボットビューワ](jmanual.html#21834)

**:convert-to-faces**

[GL/X表示](jmanual.html#34839) | [GL/X表示](jmanual.html#35093)

**:convert-to-faceset**

[GL/X表示](jmanual.html#34850) | [GL/X表示](jmanual.html#35083)

**:convert-to-world**

[ポイントクラウドデータ](jmanual.html#28089) |
[ポイントクラウドデータ](jmanual.html#28385) |
[GL/X表示](jmanual.html#34870) | [GL/X表示](jmanual.html#35063)

**:copy-from**

[ポイントクラウドデータ](jmanual.html#28069) |
[ポイントクラウドデータ](jmanual.html#28405)

**:copy-joint-to**

[BVHデータ](jmanual.html#24227) | [BVHデータ](jmanual.html#24260)

**:copy-state-to**

[BVHデータ](jmanual.html#23674) | [BVHデータ](jmanual.html#23784) |
[BVHデータ](jmanual.html#24238) | [BVHデータ](jmanual.html#24249)

**:cost**

[グラフ表現](jmanual.html#30911) | [グラフ表現](jmanual.html#30922) |
[グラフ表現](jmanual.html#31436) | [グラフ表現](jmanual.html#31491)

**:create**

[ロボットビューワ](jmanual.html#21482) |
[ロボットビューワ](jmanual.html#21889) | [GL/X表示](jmanual.html#36663)
| [GL/X表示](jmanual.html#36750) | [GL/X表示](jmanual.html#36871)

**:create-viewer**

[センサモデル](jmanual.html#15318) | [センサモデル](jmanual.html#15667)

**:current-additional-data**

[動力学計算・歩行動作生成](jmanual.html#18652) |
[動力学計算・歩行動作生成](jmanual.html#18715) |
[動力学計算・歩行動作生成](jmanual.html#19044) |
[動力学計算・歩行動作生成](jmanual.html#19105)

**:current-draw-mode**

[グラフ表現](jmanual.html#30600) | [グラフ表現](jmanual.html#31208)

**:current-output-vector**

[動力学計算・歩行動作生成](jmanual.html#18642) |
[動力学計算・歩行動作生成](jmanual.html#18725) |
[動力学計算・歩行動作生成](jmanual.html#18858) |
[動力学計算・歩行動作生成](jmanual.html#18901)

**:current-reference-output-vector**

[動力学計算・歩行動作生成](jmanual.html#18622) |
[動力学計算・歩行動作生成](jmanual.html#18745)

**:current-refzmp**

[動力学計算・歩行動作生成](jmanual.html#19014) |
[動力学計算・歩行動作生成](jmanual.html#19135)

**:current-state-vector**

[動力学計算・歩行動作生成](jmanual.html#18632) |
[動力学計算・歩行動作生成](jmanual.html#18735)

**:curvature-list**

[ポイントクラウドデータ](jmanual.html#28187) |
[ポイントクラウドデータ](jmanual.html#28747)

**:curvatures**

[ポイントクラウドデータ](jmanual.html#28176) |
[ポイントクラウドデータ](jmanual.html#28758)

**:cx**

[センサモデル](jmanual.html#15358) | [センサモデル](jmanual.html#15627)

**:cy**

[センサモデル](jmanual.html#15368) | [センサモデル](jmanual.html#15617)

**:cycloid-midcoords**

[動力学計算・歩行動作生成](jmanual.html#19424) |
[動力学計算・歩行動作生成](jmanual.html#19435)

**:cycloid-midpoint**

[動力学計算・歩行動作生成](jmanual.html#19413) |
[動力学計算・歩行動作生成](jmanual.html#19446)

**:default-coords**

[ロボットモデル](jmanual.html#6685) |
[ロボットモデル](jmanual.html#6847)

**:del-child-link**

[ロボットモデル](jmanual.html#6665) |
[ロボットモデル](jmanual.html#6706)

**:del-joint**

[ロボットモデル](jmanual.html#6615) |
[ロボットモデル](jmanual.html#6756)

**:del-parent-link**

[ロボットモデル](jmanual.html#6675) |
[ロボットモデル](jmanual.html#6696)

**:difference-cog-position**

[動力学計算・歩行動作生成](jmanual.html#17886)

**:difference-position**

[ロボットモデル](jmanual.html#10956)

**:difference-rotation**

[ロボットモデル](jmanual.html#10946)

**:distribute-total-wrench-to-torque-method-default**

[ロボットモデル](jmanual.html#13096) |
[ロボットモデル](jmanual.html#13361)

**:draw**

[センサモデル](jmanual.html#15224) | [センサモデル](jmanual.html#15278)
| [ポイントクラウドデータ](jmanual.html#28374) |
[ポイントクラウドデータ](jmanual.html#28560) |
[GL/X表示](jmanual.html#34999) | [GL/X表示](jmanual.html#35239) |
[GL/X表示](jmanual.html#35378) | [GL/X表示](jmanual.html#35411)

**:draw-arc-label**

[グラフ表現](jmanual.html#30620) | [グラフ表現](jmanual.html#31188)

**:draw-both-arc**

[グラフ表現](jmanual.html#30610) | [グラフ表現](jmanual.html#31198)

**:draw-circle**

[ロボットビューワ](jmanual.html#21453)

**:draw-collision-debug-view**

[ロボットモデル](jmanual.html#7389) |
[ロボットモデル](jmanual.html#7792)

**:draw-event**

[ロボットビューワ](jmanual.html#21625) |
[ロボットビューワ](jmanual.html#21746)

**:draw-gg-debug-view**

[ロボットモデル](jmanual.html#13129) |
[ロボットモデル](jmanual.html#13806)

**:draw-label**

[GL/X表示](jmanual.html#36900) | [GL/X表示](jmanual.html#36911)

**:draw-merged-result**

[グラフ表現](jmanual.html#30630) | [グラフ表現](jmanual.html#31178)

**:draw-objects**

[センサモデル](jmanual.html#15439) | [センサモデル](jmanual.html#15546)
| [ロボットビューワ](jmanual.html#21442) |
[ロボットビューワ](jmanual.html#21636) |
[ロボットビューワ](jmanual.html#21735)

**:draw-objects-raw**

[センサモデル](jmanual.html#15503) | [センサモデル](jmanual.html#15699)

**:draw-on**

[ロボットモデル](jmanual.html#11301) |
[ロボットモデル](jmanual.html#11323) |
[センサモデル](jmanual.html#15428) | [センサモデル](jmanual.html#15556)
| [GL/X表示](jmanual.html#34549) | [GL/X表示](jmanual.html#34578) |
[GL/X表示](jmanual.html#34628) | [GL/X表示](jmanual.html#34657) |
[GL/X表示](jmanual.html#34697) | [GL/X表示](jmanual.html#34988) |
[GL/X表示](jmanual.html#35250)

**:draw-sensor**

[センサモデル](jmanual.html#15097) | [センサモデル](jmanual.html#15130)
| [センサモデル](jmanual.html#15235) |
[センサモデル](jmanual.html#15267) | [センサモデル](jmanual.html#15492)
| [センサモデル](jmanual.html#15710)

**:draw-torque**

[動力学計算・歩行動作生成](jmanual.html#18347)

**:drawnormalmode**

[ポイントクラウドデータ](jmanual.html#28352) |
[ポイントクラウドデータ](jmanual.html#28582)

**:dump-hierarchy**

[BVHデータ](jmanual.html#23652) | [BVHデータ](jmanual.html#23806)

**:dump-joints**

[BVHデータ](jmanual.html#23641) | [BVHデータ](jmanual.html#23817)

**:dump-motion**

[BVHデータ](jmanual.html#23663) | [BVHデータ](jmanual.html#23795)

**:end-coords**

[ロボットモデル](jmanual.html#6937) |
[ロボットモデル](jmanual.html#7505)

**:event-notify**

[GL/X表示](jmanual.html#36652)

**:expand**

[グラフ表現](jmanual.html#31414) | [グラフ表現](jmanual.html#31513)

**:expand-vertices**

[GL/X表示](jmanual.html#34787) | [GL/X表示](jmanual.html#35146)

**:expand-vertices-info**

[GL/X表示](jmanual.html#34966) | [GL/X表示](jmanual.html#35272)

**:expose**

[ロボットビューワ](jmanual.html#21515) |
[ロボットビューワ](jmanual.html#21856)

**:ext-force**

[動力学計算・歩行動作生成](jmanual.html#18189)

**:ext-moment**

[動力学計算・歩行動作生成](jmanual.html#18178)

**:faces**

[ロボットモデル](jmanual.html#11279) |
[ロボットモデル](jmanual.html#11345) |
[ロボットモデル](jmanual.html#6957) |
[ロボットモデル](jmanual.html#7485) | [GL/X表示](jmanual.html#34977) |
[GL/X表示](jmanual.html#35261)

**:filename**

[GL/X表示](jmanual.html#34911) | [GL/X表示](jmanual.html#35327)

**:filter**

[ポイントクラウドデータ](jmanual.html#28025) |
[ポイントクラウドデータ](jmanual.html#28448)

**:filter-with-indices**

[ポイントクラウドデータ](jmanual.html#28036) |
[ポイントクラウドデータ](jmanual.html#28437)

**:filtered-indices**

[ポイントクラウドデータ](jmanual.html#28047) |
[ポイントクラウドデータ](jmanual.html#28426)

**:finalize-gait-parameter**

[動力学計算・歩行動作生成](jmanual.html#19292) |
[動力学計算・歩行動作生成](jmanual.html#19567)

**:find-action**

[グラフ表現](jmanual.html#31319) | [グラフ表現](jmanual.html#31352)

**:find-joint-angle-limit-weight-old-from-union-link-list**

[ロボットモデル](jmanual.html#7301) |
[ロボットモデル](jmanual.html#7880)

**:find-link-route**

[ロボットモデル](jmanual.html#7114) |
[ロボットモデル](jmanual.html#7609)

**:find-node-in-close-list**

[グラフ表現](jmanual.html#31659) | [グラフ表現](jmanual.html#31868)

**:find-object**

[環境モデル](jmanual.html#16487) | [環境モデル](jmanual.html#16648)

**:finishedp**

[動力学計算・歩行動作生成](jmanual.html#18602) |
[動力学計算・歩行動作生成](jmanual.html#18765) |
[動力学計算・歩行動作生成](jmanual.html#19034) |
[動力学計算・歩行動作生成](jmanual.html#19115)

**:fix-joint-angle**

[BVHデータ](jmanual.html#23685) | [BVHデータ](jmanual.html#23773)

**:fix-joint-order**

[BVHデータ](jmanual.html#23696) | [BVHデータ](jmanual.html#23762)

**:fix-leg-to-coords**

[ロボットモデル](jmanual.html#12705) |
[ロボットモデル](jmanual.html#13752)

**:flush**

[ロボットビューワ](jmanual.html#21669) |
[ロボットビューワ](jmanual.html#21702) | [GL/X表示](jmanual.html#34436)

**:fn**

[グラフ表現](jmanual.html#32219) | [グラフ表現](jmanual.html#32230) |
[グラフ表現](jmanual.html#32325) | [グラフ表現](jmanual.html#32380)

**:foot-midcoords**

[ロボットモデル](jmanual.html#12695) |
[ロボットモデル](jmanual.html#13763)

**:footstep-parameter**

[ロボットモデル](jmanual.html#13140) |
[ロボットモデル](jmanual.html#13795)

**:force**

[動力学計算・歩行動作生成](jmanual.html#18211)

**:force-sensor**

[ロボットモデル](jmanual.html#12539) |
[ロボットモデル](jmanual.html#13319)

**:force-sensors**

[ロボットモデル](jmanual.html#12559) |
[ロボットモデル](jmanual.html#13299)

**:forward-all-kinematics**

[動力学計算・歩行動作生成](jmanual.html#18167)

**:fovy**

[センサモデル](jmanual.html#15348) | [センサモデル](jmanual.html#15637)

**:frame**

[BVHデータ](jmanual.html#23901) | [BVHデータ](jmanual.html#23956)

**:frame-length**

[BVHデータ](jmanual.html#23912) | [BVHデータ](jmanual.html#23945)

**:from**

[グラフ表現](jmanual.html#30455) | [グラフ表現](jmanual.html#30510)

**:fullbody-inverse-kinematics**

[ロボットモデル](jmanual.html#12663) |
[ロボットモデル](jmanual.html#13194)

**:fx**

[センサモデル](jmanual.html#15378) | [センサモデル](jmanual.html#15607)

**:fy**

[センサモデル](jmanual.html#15388) | [センサモデル](jmanual.html#15597)

**:gen-footstep-parameter**

[ロボットモデル](jmanual.html#12738) |
[ロボットモデル](jmanual.html#13720)

**:generate-color-histogram-hs**

[ポイントクラウドデータ](jmanual.html#28330) |
[ポイントクラウドデータ](jmanual.html#28604)

**:get-counter-footstep-limbs**

[動力学計算・歩行動作生成](jmanual.html#19237) |
[動力学計算・歩行動作生成](jmanual.html#19622)

**:get-footstep-limbs**

[動力学計算・歩行動作生成](jmanual.html#19226) |
[動力学計算・歩行動作生成](jmanual.html#19633)

**:get-image**

[センサモデル](jmanual.html#15449) | [センサモデル](jmanual.html#15535)

**:get-image-raw**

[センサモデル](jmanual.html#15514) | [センサモデル](jmanual.html#15688)

**:get-limbs-zmp**

[動力学計算・歩行動作生成](jmanual.html#19259) |
[動力学計算・歩行動作生成](jmanual.html#19600)

**:get-limbs-zmp-list**

[動力学計算・歩行動作生成](jmanual.html#19248) |
[動力学計算・歩行動作生成](jmanual.html#19611)

**:get-material**

[GL/X表示](jmanual.html#34944) | [GL/X表示](jmanual.html#35294)

**:get-meshinfo**

[GL/X表示](jmanual.html#34922) | [GL/X表示](jmanual.html#35316)

**:get-sensor-method**

[ロボットモデル](jmanual.html#12986) |
[ロボットモデル](jmanual.html#13471)

**:get-sensors-method-by-limb**

[ロボットモデル](jmanual.html#12997) |
[ロボットモデル](jmanual.html#13460)

**:get-swing-limbs**

[動力学計算・歩行動作生成](jmanual.html#19270) |
[動力学計算・歩行動作生成](jmanual.html#19589)

**:getglimage**

[GL/X表示](jmanual.html#34414)

**:glvertices**

[GL/X表示](jmanual.html#35367) | [GL/X表示](jmanual.html#35422) |
[GL/X表示](jmanual.html#34880) | [GL/X表示](jmanual.html#35053)

**:gn**

[グラフ表現](jmanual.html#32336) | [グラフ表現](jmanual.html#32369)

**:go-pos-params-&gt;footstep-list**

[ロボットモデル](jmanual.html#12748) |
[ロボットモデル](jmanual.html#13709)

**:go-pos-quadruped-params-&gt;footstep-list**

[ロボットモデル](jmanual.html#12759) |
[ロボットモデル](jmanual.html#13699)

**:goal-state**

[グラフ表現](jmanual.html#31079) | [グラフ表現](jmanual.html#31134)

**:goal-test**

[グラフ表現](jmanual.html#31046) | [グラフ表現](jmanual.html#31167)

**:gripper**

[ロボットモデル](jmanual.html#12975) |
[ロボットモデル](jmanual.html#13482)

**:head**

[ロボットモデル](jmanual.html#13052) |
[ロボットモデル](jmanual.html#13405)

**:head-end-coords**

[ロボットモデル](jmanual.html#12865) |
[ロボットモデル](jmanual.html#13592)

**:head-neck**

[BVHデータ](jmanual.html#24216) | [BVHデータ](jmanual.html#24271)

**:head-root-link**

[ロボットモデル](jmanual.html#12931) |
[ロボットモデル](jmanual.html#13526)

**:height**

[センサモデル](jmanual.html#15338) | [センサモデル](jmanual.html#15647)
| [ポイントクラウドデータ](jmanual.html#28154) |
[ポイントクラウドデータ](jmanual.html#28780)

**:hn**

[グラフ表現](jmanual.html#32347) | [グラフ表現](jmanual.html#32358)

**:ik-convergence-check**

[ロボットモデル](jmanual.html#7400) |
[ロボットモデル](jmanual.html#7781)

**:image-circle-filter**

[ポイントクラウドデータ](jmanual.html#28308) |
[ポイントクラウドデータ](jmanual.html#28626)

**:image-position-inlier**

[ポイントクラウドデータ](jmanual.html#28297) |
[ポイントクラウドデータ](jmanual.html#28637)

**:image-viewer**

[センサモデル](jmanual.html#15481) | [センサモデル](jmanual.html#15721)

**:imu-sensor**

[ロボットモデル](jmanual.html#12549) |
[ロボットモデル](jmanual.html#13309)

**:imu-sensors**

[ロボットモデル](jmanual.html#12569) |
[ロボットモデル](jmanual.html#13289)

**:inertia-tensor**

[ロボットモデル](jmanual.html#6585) |
[ロボットモデル](jmanual.html#6786) |
[動力学計算・歩行動作生成](jmanual.html#18295)

**:init**

[ロボットモデル](jmanual.html#5042) |
[ロボットモデル](jmanual.html#5370) |
[ロボットモデル](jmanual.html#5476) |
[ロボットモデル](jmanual.html#5591) |
[ロボットモデル](jmanual.html#5642) |
[ロボットモデル](jmanual.html#5757) |
[ロボットモデル](jmanual.html#5808) |
[ロボットモデル](jmanual.html#5923) |
[ロボットモデル](jmanual.html#5974) |
[ロボットモデル](jmanual.html#6089) |
[ロボットモデル](jmanual.html#6140) |
[ロボットモデル](jmanual.html#6297) |
[ロボットモデル](jmanual.html#6348) |
[ロボットモデル](jmanual.html#6483) |
[ロボットモデル](jmanual.html#6534) |
[ロボットモデル](jmanual.html#6836) |
[ロボットモデル](jmanual.html#6876) |
[ロボットモデル](jmanual.html#7565) |
[ロボットモデル](jmanual.html#11257) |
[ロボットモデル](jmanual.html#11312) |
[センサモデル](jmanual.html#15108) | [センサモデル](jmanual.html#15119)
| [センサモデル](jmanual.html#15203) |
[センサモデル](jmanual.html#15256) | [センサモデル](jmanual.html#15307)
| [センサモデル](jmanual.html#15677) | [環境モデル](jmanual.html#16426)
| [環境モデル](jmanual.html#16708) |
[動力学計算・歩行動作生成](jmanual.html#18519) |
[動力学計算・歩行動作生成](jmanual.html#18552) |
[動力学計算・歩行動作生成](jmanual.html#19215) |
[動力学計算・歩行動作生成](jmanual.html#19644) |
[動力学計算・歩行動作生成](jmanual.html#18581) |
[動力学計算・歩行動作生成](jmanual.html#18785) |
[動力学計算・歩行動作生成](jmanual.html#18847) |
[動力学計算・歩行動作生成](jmanual.html#18911) |
[動力学計算・歩行動作生成](jmanual.html#18973) |
[動力学計算・歩行動作生成](jmanual.html#19175) |
[BVHデータ](jmanual.html#23458) | [BVHデータ](jmanual.html#23579) |
[BVHデータ](jmanual.html#23868) | [BVHデータ](jmanual.html#23989) |
[BVHデータ](jmanual.html#24040) | [BVHデータ](jmanual.html#24447) |
[BVHデータ](jmanual.html#24476) | [BVHデータ](jmanual.html#24487) |
[BVHデータ](jmanual.html#24516) | [BVHデータ](jmanual.html#24527) |
[BVHデータ](jmanual.html#23204) | [BVHデータ](jmanual.html#23247) |
[BVHデータ](jmanual.html#23308) | [BVHデータ](jmanual.html#23374) |
[BVHデータ](jmanual.html#23608) | [BVHデータ](jmanual.html#23740) |
[ポイントクラウドデータ](jmanual.html#27924) |
[ポイントクラウドデータ](jmanual.html#28549) |
[グラフ表現](jmanual.html#30250) | [グラフ表現](jmanual.html#30415) |
[グラフ表現](jmanual.html#30444) | [グラフ表現](jmanual.html#30521) |
[グラフ表現](jmanual.html#30640) | [グラフ表現](jmanual.html#30871) |
[グラフ表現](jmanual.html#30900) | [グラフ表現](jmanual.html#30933) |
[グラフ表現](jmanual.html#31308) | [グラフ表現](jmanual.html#31363) |
[グラフ表現](jmanual.html#31392) | [グラフ表現](jmanual.html#31535) |
[グラフ表現](jmanual.html#31564) | [グラフ表現](jmanual.html#31619) |
[グラフ表現](jmanual.html#31908) | [グラフ表現](jmanual.html#32007) |
[グラフ表現](jmanual.html#32036) | [グラフ表現](jmanual.html#32135) |
[グラフ表現](jmanual.html#32164) | [グラフ表現](jmanual.html#32285) |
[グラフ表現](jmanual.html#32314) | [グラフ表現](jmanual.html#32391) |
[GL/X表示](jmanual.html#34900) | [GL/X表示](jmanual.html#35338) |
[ユーティリティ関数](jmanual.html#37560) |
[ユーティリティ関数](jmanual.html#37610) |
[ユーティリティ関数](jmanual.html#37638) |
[ユーティリティ関数](jmanual.html#37890)

**:init-end-coords**

[BVHデータ](jmanual.html#23718) | [BVHデータ](jmanual.html#24011)

**:init-ending**

[ロボットモデル](jmanual.html#12810) |
[ロボットモデル](jmanual.html#13647) |
[ロボットモデル](jmanual.html#6887) |
[ロボットモデル](jmanual.html#7555)

**:init-pose**

[ロボットモデル](jmanual.html#12631) |
[ロボットモデル](jmanual.html#13227)

**:init-root-link**

[BVHデータ](jmanual.html#23729) | [BVHデータ](jmanual.html#24000)

**:initialize-gait-parameter**

[動力学計算・歩行動作生成](jmanual.html#19281) |
[動力学計算・歩行動作生成](jmanual.html#19578)

**:interlocking-joint-pairs**

[ロボットモデル](jmanual.html#7082) |
[ロボットモデル](jmanual.html#7642)

**:interpolatingp**

[ユーティリティ関数](jmanual.html#37729) |
[ユーティリティ関数](jmanual.html#37799)

**:interpolation**

[ユーティリティ関数](jmanual.html#37918) |
[ユーティリティ関数](jmanual.html#37928) |
[ユーティリティ関数](jmanual.html#38007) |
[ユーティリティ関数](jmanual.html#38017)

**:inverse-dynamics**

[動力学計算・歩行動作生成](jmanual.html#18156)

**:inverse-kinematics**

[ロボットモデル](jmanual.html#7019) |
[ロボットモデル](jmanual.html#7704) |
[ロボットモデル](jmanual.html#12599) |
[ロボットモデル](jmanual.html#13258)

**:inverse-kinematics-args**

[ロボットモデル](jmanual.html#7378) |
[ロボットモデル](jmanual.html#7803)

**:inverse-kinematics-for-closed-loop-forward-kinematics**

[ロボットモデル](jmanual.html#7041) |
[ロボットモデル](jmanual.html#7682)

**:inverse-kinematics-loop**

[ロボットモデル](jmanual.html#7008) |
[ロボットモデル](jmanual.html#7715) |
[ロボットモデル](jmanual.html#12610) |
[ロボットモデル](jmanual.html#13247)

**:inverse-kinematics-loop-for-look-at**

[ロボットモデル](jmanual.html#12964) |
[ロボットモデル](jmanual.html#13493)

**:inverse-rotate-vector**

[ロボットモデル](jmanual.html#11119) |
[ロボットモデル](jmanual.html#11159)

**:inverse-transform-vector**

[ロボットモデル](jmanual.html#11199) |
[ロボットモデル](jmanual.html#11228)

**:joint**

[ロボットモデル](jmanual.html#6595) |
[ロボットモデル](jmanual.html#6776) |
[ロボットモデル](jmanual.html#6927) |
[ロボットモデル](jmanual.html#7515)

**:joint-acceleration**

[ロボットモデル](jmanual.html#5133) |
[ロボットモデル](jmanual.html#5280)

**:joint-angle**

[ロボットモデル](jmanual.html#5487) |
[ロボットモデル](jmanual.html#5580) |
[ロボットモデル](jmanual.html#5653) |
[ロボットモデル](jmanual.html#5746) |
[ロボットモデル](jmanual.html#5819) |
[ロボットモデル](jmanual.html#5912) |
[ロボットモデル](jmanual.html#5985) |
[ロボットモデル](jmanual.html#6078) |
[ロボットモデル](jmanual.html#6151) |
[ロボットモデル](jmanual.html#6286) |
[ロボットモデル](jmanual.html#6359) |
[ロボットモデル](jmanual.html#6472)

**:joint-angle-bvh**

[BVHデータ](jmanual.html#23319) | [BVHデータ](jmanual.html#23429) |
[BVHデータ](jmanual.html#23469) | [BVHデータ](jmanual.html#23568)

**:joint-angle-bvh-impl**

[BVHデータ](jmanual.html#23341) | [BVHデータ](jmanual.html#23407) |
[BVHデータ](jmanual.html#23491) | [BVHデータ](jmanual.html#23546)

**:joint-angle-bvh-offset**

[BVHデータ](jmanual.html#23330) | [BVHデータ](jmanual.html#23418) |
[BVHデータ](jmanual.html#23480) | [BVHデータ](jmanual.html#23557)

**:joint-angle-limit-nspace-for-6dof**

[ロボットモデル](jmanual.html#13107) |
[ロボットモデル](jmanual.html#13350)

**:joint-angle-rpy**

[ロボットモデル](jmanual.html#6162) |
[ロボットモデル](jmanual.html#6276) |
[ロボットモデル](jmanual.html#6370) |
[ロボットモデル](jmanual.html#6462)

**:joint-dof**

[ロボットモデル](jmanual.html#5093) |
[ロボットモデル](jmanual.html#5320) |
[ロボットモデル](jmanual.html#5498) |
[ロボットモデル](jmanual.html#5570) |
[ロボットモデル](jmanual.html#5664) |
[ロボットモデル](jmanual.html#5736) |
[ロボットモデル](jmanual.html#5830) |
[ロボットモデル](jmanual.html#5902) |
[ロボットモデル](jmanual.html#5996) |
[ロボットモデル](jmanual.html#6068) |
[ロボットモデル](jmanual.html#6172) |
[ロボットモデル](jmanual.html#6266) |
[ロボットモデル](jmanual.html#6380) |
[ロボットモデル](jmanual.html#6452)

**:joint-euler-angle**

[ロボットモデル](jmanual.html#6202) |
[ロボットモデル](jmanual.html#6235)

**:joint-list**

[ロボットモデル](jmanual.html#6907) |
[ロボットモデル](jmanual.html#7535)

**:joint-min-max-table**

[ロボットモデル](jmanual.html#5195) |
[ロボットモデル](jmanual.html#5425)

**:joint-min-max-table-angle-interpolate**

[ロボットモデル](jmanual.html#5217) |
[ロボットモデル](jmanual.html#5403)

**:joint-min-max-table-max-angle**

[ロボットモデル](jmanual.html#5239) |
[ロボットモデル](jmanual.html#5381)

**:joint-min-max-table-min-angle**

[ロボットモデル](jmanual.html#5228) |
[ロボットモデル](jmanual.html#5392)

**:joint-min-max-target**

[ロボットモデル](jmanual.html#5206) |
[ロボットモデル](jmanual.html#5414)

**:joint-order**

[ロボットモデル](jmanual.html#13118) |
[ロボットモデル](jmanual.html#13339)

**:joint-torque**

[ロボットモデル](jmanual.html#5143) |
[ロボットモデル](jmanual.html#5270)

**:joint-velocity**

[ロボットモデル](jmanual.html#5123) |
[ロボットモデル](jmanual.html#5290)

**:larm**

[ロボットモデル](jmanual.html#13008) |
[ロボットモデル](jmanual.html#13449)

**:larm-collar**

[BVHデータ](jmanual.html#24051) | [BVHデータ](jmanual.html#24436)

**:larm-elbow**

[BVHデータ](jmanual.html#24073) | [BVHデータ](jmanual.html#24414)

**:larm-end-coords**

[ロボットモデル](jmanual.html#12832) |
[ロボットモデル](jmanual.html#13625)

**:larm-root-link**

[ロボットモデル](jmanual.html#12898) |
[ロボットモデル](jmanual.html#13559)

**:larm-shoulder**

[BVHデータ](jmanual.html#24062) | [BVHデータ](jmanual.html#24425)

**:larm-wrist**

[BVHデータ](jmanual.html#24084) | [BVHデータ](jmanual.html#24403)

**:last-reference-output-vector**

[動力学計算・歩行動作生成](jmanual.html#18612) |
[動力学計算・歩行動作生成](jmanual.html#18755)

**:last-refzmp**

[動力学計算・歩行動作生成](jmanual.html#19004) |
[動力学計算・歩行動作生成](jmanual.html#19145)

**:legs**

[ロボットモデル](jmanual.html#13085) |
[ロボットモデル](jmanual.html#13372)

**:limb**

[ロボットモデル](jmanual.html#12953) |
[ロボットモデル](jmanual.html#13504)

**:line-width**

[GL/X表示](jmanual.html#34489)

**:link**

[ロボットモデル](jmanual.html#6917) |
[ロボットモデル](jmanual.html#7525)

**:link-list**

[ロボットモデル](jmanual.html#6977) |
[ロボットモデル](jmanual.html#7465)

**:links**

[ロボットモデル](jmanual.html#6897) |
[ロボットモデル](jmanual.html#7545)

**:lleg**

[ロボットモデル](jmanual.html#13030) |
[ロボットモデル](jmanual.html#13427)

**:lleg-ankle**

[BVHデータ](jmanual.html#24161) | [BVHデータ](jmanual.html#24326)

**:lleg-crotch**

[BVHデータ](jmanual.html#24139) | [BVHデータ](jmanual.html#24348)

**:lleg-end-coords**

[ロボットモデル](jmanual.html#12854) |
[ロボットモデル](jmanual.html#13603)

**:lleg-knee**

[BVHデータ](jmanual.html#24150) | [BVHデータ](jmanual.html#24337)

**:lleg-root-link**

[ロボットモデル](jmanual.html#12920) |
[ロボットモデル](jmanual.html#13537)

**:look**

[ロボットビューワ](jmanual.html#21918)

**:look-all**

[ロボットビューワ](jmanual.html#21581) |
[ロボットビューワ](jmanual.html#21790)

**:look-at-hand**

[ロボットモデル](jmanual.html#12589) |
[ロボットモデル](jmanual.html#13269)

**:look-at-target**

[ロボットモデル](jmanual.html#12621) |
[ロボットモデル](jmanual.html#13237)

**:look1**

[ロボットビューワ](jmanual.html#21570) |
[ロボットビューワ](jmanual.html#21801)

**:make-bvh-link**

[BVHデータ](jmanual.html#23619) | [BVHデータ](jmanual.html#23839)

**:make-default-linear-link-joint-between-attach-coords**

[ロボットモデル](jmanual.html#12789) |
[ロボットモデル](jmanual.html#13669)

**:make-gait-parameter**

[動力学計算・歩行動作生成](jmanual.html#19303) |
[動力学計算・歩行動作生成](jmanual.html#19556)

**:make-joint-min-max-table**

[ロボットモデル](jmanual.html#7125) |
[ロボットモデル](jmanual.html#7598)

**:make-min-max-table-using-collision-check**

[ロボットモデル](jmanual.html#7136) |
[ロボットモデル](jmanual.html#7587)

**:make-pqpmodel**

[ロボット動作と干渉計算](jmanual.html#22671) |
[GL/X表示](jmanual.html#35032) | [GL/X表示](jmanual.html#35206)

**:make-sole-polygon**

[ロボットモデル](jmanual.html#13162) |
[ロボットモデル](jmanual.html#13773)

**:make-support-polygons**

[ロボットモデル](jmanual.html#13151) |
[ロボットモデル](jmanual.html#13784)

**:makecurrent**

[GL/X表示](jmanual.html#34520)

**:max-angle**

[ロボットモデル](jmanual.html#5063) |
[ロボットモデル](jmanual.html#5350)

**:max-joint-torque**

[ロボットモデル](jmanual.html#5163) |
[ロボットモデル](jmanual.html#5250)

**:max-joint-velocity**

[ロボットモデル](jmanual.html#5153) |
[ロボットモデル](jmanual.html#5260)

**:max-torque-vector**

[動力学計算・歩行動作生成](jmanual.html#18490)

**:min-angle**

[ロボットモデル](jmanual.html#5053) |
[ロボットモデル](jmanual.html#5360)

**:mirror-axis**

[GL/X表示](jmanual.html#34828) | [GL/X表示](jmanual.html#35104)

**:model**

[BVHデータ](jmanual.html#23879) | [BVHデータ](jmanual.html#23978)

**:moment**

[動力学計算・歩行動作生成](jmanual.html#18200)

**:motionnotify**

[GL/X表示](jmanual.html#36597)

**:move-centroid-on-foot**

[ロボットモデル](jmanual.html#12716) |
[ロボットモデル](jmanual.html#13741)

**:move-coords**

[ロボットモデル](jmanual.html#11046)

**:move-coords-event**

[ロボットビューワ](jmanual.html#21614) |
[ロボットビューワ](jmanual.html#21757)

**:move-joints**

[ロボットモデル](jmanual.html#7290) |
[ロボットモデル](jmanual.html#7891)

**:move-joints-avoidance**

[ロボットモデル](jmanual.html#7367) |
[ロボットモデル](jmanual.html#7814)

**:move-to**

[ロボットモデル](jmanual.html#11017) |
[ロボットモデル](jmanual.html#11057)

**:move-viewing-around-viewtarget**

[ロボットビューワ](jmanual.html#21592) |
[ロボットビューワ](jmanual.html#21779)

**:neighbor-action-alist**

[グラフ表現](jmanual.html#31330) | [グラフ表現](jmanual.html#31341)

**:neighbors**

[グラフ表現](jmanual.html#30338) | [グラフ表現](jmanual.html#31268)

**:nfilter**

[ポイントクラウドデータ](jmanual.html#28275) |
[ポイントクラウドデータ](jmanual.html#28659)

**:node**

[グラフ表現](jmanual.html#30662) | [グラフ表現](jmanual.html#30849)

**:nodes**

[グラフ表現](jmanual.html#30673) | [グラフ表現](jmanual.html#30838)

**:nomethod**

[ロボットビューワ](jmanual.html#21947) |
[ロボットビューワ](jmanual.html#21958) |
[ロボットビューワ](jmanual.html#21998) |
[ロボットビューワ](jmanual.html#22009)

**:normal-list**

[ポイントクラウドデータ](jmanual.html#27995) |
[ポイントクラウドデータ](jmanual.html#28479)

**:normals**

[ポイントクラウドデータ](jmanual.html#27965) |
[ポイントクラウドデータ](jmanual.html#28509)

**:null-open-list?**

[グラフ表現](jmanual.html#31692) | [グラフ表現](jmanual.html#31835)

**:object**

[環境モデル](jmanual.html#16547) | [環境モデル](jmanual.html#16588)

**:objects**

[環境モデル](jmanual.html#16437) | [環境モデル](jmanual.html#16698) |
[ロボットビューワ](jmanual.html#21647) |
[ロボットビューワ](jmanual.html#21724) |
[ロボットビューワ](jmanual.html#21987) |
[ロボットビューワ](jmanual.html#22020)

**:offset**

[BVHデータ](jmanual.html#23225) | [BVHデータ](jmanual.html#23268)

**:open-list**

[グラフ表現](jmanual.html#31747) | [グラフ表現](jmanual.html#31780)

**:original-draw-mode**

[グラフ表現](jmanual.html#30590) | [グラフ表現](jmanual.html#31218)

**:parent**

[グラフ表現](jmanual.html#31447) | [グラフ表現](jmanual.html#31480)

**:parent-link**

[ロボットモデル](jmanual.html#5073) |
[ロボットモデル](jmanual.html#5340) |
[ロボットモデル](jmanual.html#6625) |
[ロボットモデル](jmanual.html#6746)

**:pass-preview-controller**

[動力学計算・歩行動作生成](jmanual.html#18662) |
[動力学計算・歩行動作生成](jmanual.html#18705) |
[動力学計算・歩行動作生成](jmanual.html#19054) |
[動力学計算・歩行動作生成](jmanual.html#19095)

**:pass-time**

[ユーティリティ関数](jmanual.html#37759) |
[ユーティリティ関数](jmanual.html#37769)

**:paste-texture-to-face**

[GL/X表示](jmanual.html#34617)

**:path**

[グラフ表現](jmanual.html#31403) | [グラフ表現](jmanual.html#31524)

**:path-cost**

[グラフ表現](jmanual.html#30984) | [グラフ表現](jmanual.html#30995) |
[グラフ表現](jmanual.html#31057) | [グラフ表現](jmanual.html#31156)

**:plot-joint-min-max-table**

[ロボットモデル](jmanual.html#6987) |
[ロボットモデル](jmanual.html#7455)

**:plot-joint-min-max-table-common**

[ロボットモデル](jmanual.html#7147) |
[ロボットモデル](jmanual.html#7576)

**:point-color**

[ポイントクラウドデータ](jmanual.html#28209) |
[ポイントクラウドデータ](jmanual.html#28725)

**:point-list**

[ポイントクラウドデータ](jmanual.html#27975) |
[ポイントクラウドデータ](jmanual.html#28499)

**:point-size**

[ポイントクラウドデータ](jmanual.html#28220) |
[ポイントクラウドデータ](jmanual.html#28714) |
[GL/X表示](jmanual.html#34479)

**:points**

[ポイントクラウドデータ](jmanual.html#27945) |
[ポイントクラウドデータ](jmanual.html#28529)

**:pop-from-open-list**

[グラフ表現](jmanual.html#31736) | [グラフ表現](jmanual.html#31791) |
[グラフ表現](jmanual.html#31952) | [グラフ表現](jmanual.html#31963) |
[グラフ表現](jmanual.html#32080) | [グラフ表現](jmanual.html#32091) |
[グラフ表現](jmanual.html#32208) | [グラフ表現](jmanual.html#32241)

**:position**

[ユーティリティ関数](jmanual.html#37669) |
[ユーティリティ関数](jmanual.html#37859)

**:position-list**

[ユーティリティ関数](jmanual.html#37659) |
[ユーティリティ関数](jmanual.html#37869)

**:preview-control-dynamics-filter**

[動力学計算・歩行動作生成](jmanual.html#18336)

**:prin1**

[グラフ表現](jmanual.html#30477) | [グラフ表現](jmanual.html#30488)

**:print-vector-for-robot-limb**

[ロボットモデル](jmanual.html#12674) |
[ロボットモデル](jmanual.html#13184)

**:proc-one-tick**

[動力学計算・歩行動作生成](jmanual.html#19369) |
[動力学計算・歩行動作生成](jmanual.html#19490)

**:profile**

[センサモデル](jmanual.html#15053) | [センサモデル](jmanual.html#15174)

**:propagate-mass-properties**

[動力学計算・歩行動作生成](jmanual.html#17802)

**:quit**

[GL/X表示](jmanual.html#36641) | [GL/X表示](jmanual.html#36692)

**:rarm**

[ロボットモデル](jmanual.html#13019) |
[ロボットモデル](jmanual.html#13438)

**:rarm-collar**

[BVHデータ](jmanual.html#24095) | [BVHデータ](jmanual.html#24392)

**:rarm-elbow**

[BVHデータ](jmanual.html#24117) | [BVHデータ](jmanual.html#24370)

**:rarm-end-coords**

[ロボットモデル](jmanual.html#12821) |
[ロボットモデル](jmanual.html#13636)

**:rarm-root-link**

[ロボットモデル](jmanual.html#12887) |
[ロボットモデル](jmanual.html#13570)

**:rarm-shoulder**

[BVHデータ](jmanual.html#24106) | [BVHデータ](jmanual.html#24381)

**:rarm-wrist**

[BVHデータ](jmanual.html#24128) | [BVHデータ](jmanual.html#24359)

**:ray**

[センサモデル](jmanual.html#15418) | [センサモデル](jmanual.html#15567)

**:read**

[センサモデル](jmanual.html#15086) | [センサモデル](jmanual.html#15141)

**:redraw**

[ロボットビューワ](jmanual.html#21504) |
[ロボットビューワ](jmanual.html#21867) | [GL/X表示](jmanual.html#34509)
| [GL/X表示](jmanual.html#36721)

**:refcog**

[動力学計算・歩行動作生成](jmanual.html#18984) |
[動力学計算・歩行動作生成](jmanual.html#19165)

**:remove-all-arcs**

[グラフ表現](jmanual.html#30305) | [グラフ表現](jmanual.html#30360)

**:remove-arc**

[グラフ表現](jmanual.html#30294) | [グラフ表現](jmanual.html#30371) |
[グラフ表現](jmanual.html#30728) | [グラフ表現](jmanual.html#30783)

**:remove-node**

[グラフ表現](jmanual.html#30695) | [グラフ表現](jmanual.html#30816)

**:remove-object**

[環境モデル](jmanual.html#16477) | [環境モデル](jmanual.html#16658)

**:remove-objects**

[環境モデル](jmanual.html#16467) | [環境モデル](jmanual.html#16668)

**:remove-spot**

[環境モデル](jmanual.html#16527) | [環境モデル](jmanual.html#16608)

**:remove-spots**

[環境モデル](jmanual.html#16517) | [環境モデル](jmanual.html#16618)

**:reset**

[ユーティリティ関数](jmanual.html#37648) |
[ユーティリティ関数](jmanual.html#37879) |
[ユーティリティ関数](jmanual.html#37996) |
[ユーティリティ関数](jmanual.html#38027)

**:reset-box**

[ポイントクラウドデータ](jmanual.html#28099) |
[ポイントクラウドデータ](jmanual.html#28835)

**:reset-dynamics**

[動力学計算・歩行動作生成](jmanual.html#18266)

**:reset-joint-angle-limit-weight-old**

[ロボットモデル](jmanual.html#7312) |
[ロボットモデル](jmanual.html#7869)

**:reset-offset-from-parent**

[GL/X表示](jmanual.html#34777) | [GL/X表示](jmanual.html#35156)

**:resize**

[ロボットビューワ](jmanual.html#21526) |
[ロボットビューワ](jmanual.html#21845) | [GL/X表示](jmanual.html#36805)
| [GL/X表示](jmanual.html#36816)

**:rleg**

[ロボットモデル](jmanual.html#13041) |
[ロボットモデル](jmanual.html#13416)

**:rleg-ankle**

[BVHデータ](jmanual.html#24194) | [BVHデータ](jmanual.html#24293)

**:rleg-crotch**

[BVHデータ](jmanual.html#24172) | [BVHデータ](jmanual.html#24315)

**:rleg-end-coords**

[ロボットモデル](jmanual.html#12843) |
[ロボットモデル](jmanual.html#13614)

**:rleg-knee**

[BVHデータ](jmanual.html#24183) | [BVHデータ](jmanual.html#24304)

**:rleg-root-link**

[ロボットモデル](jmanual.html#12909) |
[ロボットモデル](jmanual.html#13548)

**:rotate-vector**

[ロボットモデル](jmanual.html#11130) |
[ロボットモデル](jmanual.html#11170)

**:screen-point**

[センサモデル](jmanual.html#15398) | [センサモデル](jmanual.html#15587)

**:segment**

[ユーティリティ関数](jmanual.html#37709) |
[ユーティリティ関数](jmanual.html#37819)

**:segment-num**

[ユーティリティ関数](jmanual.html#37719) |
[ユーティリティ関数](jmanual.html#37809)

**:segment-time**

[ユーティリティ関数](jmanual.html#37699) |
[ユーティリティ関数](jmanual.html#37829)

**:select-drawmode**

[センサモデル](jmanual.html#15460) | [センサモデル](jmanual.html#15525)
| [ロボットビューワ](jmanual.html#21658) |
[ロボットビューワ](jmanual.html#21713)

**:self-collision-check**

[ロボットモデル](jmanual.html#7444) |
[ロボットモデル](jmanual.html#7737)

**:set-color**

[ポイントクラウドデータ](jmanual.html#28198) |
[ポイントクラウドデータ](jmanual.html#28736) |
[GL/X表示](jmanual.html#35389) | [GL/X表示](jmanual.html#35400) |
[GL/X表示](jmanual.html#34607) | [GL/X表示](jmanual.html#34737) |
[GL/X表示](jmanual.html#35196)

**:set-cursor-pos-event**

[ロボットビューワ](jmanual.html#21603) |
[ロボットビューワ](jmanual.html#21768)

**:set-event-proc**

[GL/X表示](jmanual.html#36619)

**:set-material**

[GL/X表示](jmanual.html#34955) | [GL/X表示](jmanual.html#35283)

**:set-meshinfo**

[GL/X表示](jmanual.html#34933) | [GL/X表示](jmanual.html#35305)

**:set-midpoint-for-interlocking-joints**

[ロボットモデル](jmanual.html#7072) |
[ロボットモデル](jmanual.html#7652)

**:set-offset**

[ポイントクラウドデータ](jmanual.html#28341) |
[ポイントクラウドデータ](jmanual.html#28593) |
[GL/X表示](jmanual.html#34860) | [GL/X表示](jmanual.html#35073)

**:signal**

[センサモデル](jmanual.html#15064) | [センサモデル](jmanual.html#15163)

**:simulate**

[センサモデル](jmanual.html#15075) | [センサモデル](jmanual.html#15152)
| [センサモデル](jmanual.html#15214) |
[センサモデル](jmanual.html#15246)

**:size**

[ポイントクラウドデータ](jmanual.html#28132) |
[ポイントクラウドデータ](jmanual.html#28802)

**:size-change**

[ポイントクラウドデータ](jmanual.html#27935) |
[ポイントクラウドデータ](jmanual.html#28539)

**:solve**

[動力学計算・歩行動作生成](jmanual.html#18530) |
[動力学計算・歩行動作生成](jmanual.html#18541) |
[グラフ表現](jmanual.html#31575) | [グラフ表現](jmanual.html#31608) |
[グラフ表現](jmanual.html#31670) | [グラフ表現](jmanual.html#31857)

**:solve-angle-vector**

[動力学計算・歩行動作生成](jmanual.html#19391) |
[動力学計算・歩行動作生成](jmanual.html#19468)

**:solve-av-by-move-centroid-on-foot**

[動力学計算・歩行動作生成](jmanual.html#19402) |
[動力学計算・歩行動作生成](jmanual.html#19457)

**:solve-by-name**

[グラフ表現](jmanual.html#31586) | [グラフ表現](jmanual.html#31597)

**:solve-init**

[グラフ表現](jmanual.html#31648) | [グラフ表現](jmanual.html#31879)

**:spacial-acceleration**

[動力学計算・歩行動作生成](jmanual.html#18222)

**:spacial-velocity**

[動力学計算・歩行動作生成](jmanual.html#18233)

**:speed-to-angle**

[ロボットモデル](jmanual.html#5103) |
[ロボットモデル](jmanual.html#5310) |
[ロボットモデル](jmanual.html#5508) |
[ロボットモデル](jmanual.html#5560) |
[ロボットモデル](jmanual.html#5674) |
[ロボットモデル](jmanual.html#5726) |
[ロボットモデル](jmanual.html#5840) |
[ロボットモデル](jmanual.html#5892) |
[ロボットモデル](jmanual.html#6006) |
[ロボットモデル](jmanual.html#6058) |
[ロボットモデル](jmanual.html#6182) |
[ロボットモデル](jmanual.html#6256) |
[ロボットモデル](jmanual.html#6390) |
[ロボットモデル](jmanual.html#6442)

**:spot**

[環境モデル](jmanual.html#16557) | [環境モデル](jmanual.html#16578)

**:spots**

[環境モデル](jmanual.html#16537) | [環境モデル](jmanual.html#16598)

**:start**

[ユーティリティ関数](jmanual.html#37570) |
[ユーティリティ関数](jmanual.html#37600)

**:start-interpolation**

[ユーティリティ関数](jmanual.html#37739) |
[ユーティリティ関数](jmanual.html#37789)

**:start-state**

[グラフ表現](jmanual.html#31068) | [グラフ表現](jmanual.html#31145)

**:state**

[グラフ表現](jmanual.html#31425) | [グラフ表現](jmanual.html#31502)

**:step**

[ポイントクラウドデータ](jmanual.html#28058) |
[ポイントクラウドデータ](jmanual.html#28415)

**:step-inlier**

[ポイントクラウドデータ](jmanual.html#28319) |
[ポイントクラウドデータ](jmanual.html#28615)

**:stop**

[ユーティリティ関数](jmanual.html#37580) |
[ユーティリティ関数](jmanual.html#37590)

**:stop-interpolation**

[ユーティリティ関数](jmanual.html#37749) |
[ユーティリティ関数](jmanual.html#37779)

**:successors**

[グラフ表現](jmanual.html#30272) | [グラフ表現](jmanual.html#30393) |
[グラフ表現](jmanual.html#30651) | [グラフ表現](jmanual.html#30860)

**:support-polygon**

[ロボットモデル](jmanual.html#12779) |
[ロボットモデル](jmanual.html#13679)

**:support-polygons**

[ロボットモデル](jmanual.html#12769) |
[ロボットモデル](jmanual.html#13689)

**:tabbed-button**

[GL/X表示](jmanual.html#36783) | [GL/X表示](jmanual.html#36838)

**:tabbed-panel**

[GL/X表示](jmanual.html#36794) | [GL/X表示](jmanual.html#36827)

**:time**

[ユーティリティ関数](jmanual.html#37689) |
[ユーティリティ関数](jmanual.html#37839)

**:time-list**

[ユーティリティ関数](jmanual.html#37679) |
[ユーティリティ関数](jmanual.html#37849)

**:to**

[グラフ表現](jmanual.html#30466) | [グラフ表現](jmanual.html#30499)

**:torque-ratio-vector**

[動力学計算・歩行動作生成](jmanual.html#18479)

**:torque-vector**

[ロボットモデル](jmanual.html#12641) |
[ロボットモデル](jmanual.html#13216)

**:torso**

[ロボットモデル](jmanual.html#13063) |
[ロボットモデル](jmanual.html#13394)

**:torso-chest**

[BVHデータ](jmanual.html#24205) | [BVHデータ](jmanual.html#24282)

**:torso-end-coords**

[ロボットモデル](jmanual.html#12876) |
[ロボットモデル](jmanual.html#13581)

**:torso-root-link**

[ロボットモデル](jmanual.html#12942) |
[ロボットモデル](jmanual.html#13515)

**:transform**

[ロボットモデル](jmanual.html#10995) |
[ロボットモデル](jmanual.html#11068)

**:transform-points**

[ポイントクラウドデータ](jmanual.html#28079) |
[ポイントクラウドデータ](jmanual.html#28395)

**:transformation**

[ロボットモデル](jmanual.html#11006) |
[ロボットモデル](jmanual.html#11079)

**:transparent**

[ポイントクラウドデータ](jmanual.html#28363) |
[ポイントクラウドデータ](jmanual.html#28571)

**:type**

[BVHデータ](jmanual.html#23214) | [BVHデータ](jmanual.html#23279)

**:unlink**

[グラフ表現](jmanual.html#30316) | [グラフ表現](jmanual.html#30349)

**:update-cog-z**

[動力学計算・歩行動作生成](jmanual.html#19074) |
[動力学計算・歩行動作生成](jmanual.html#19186)

**:update-current-gait-parameter**

[動力学計算・歩行動作生成](jmanual.html#19380) |
[動力学計算・歩行動作生成](jmanual.html#19479)

**:update-descendants**

[ロボットモデル](jmanual.html#7103) |
[ロボットモデル](jmanual.html#7620)

**:update-mass-properties**

[動力学計算・歩行動作生成](jmanual.html#17941)

**:update-xk**

[動力学計算・歩行動作生成](jmanual.html#18592) |
[動力学計算・歩行動作生成](jmanual.html#18775) |
[動力学計算・歩行動作生成](jmanual.html#19024) |
[動力学計算・歩行動作生成](jmanual.html#19125)

**:use-flat-shader**

[GL/X表示](jmanual.html#34797) | [GL/X表示](jmanual.html#35136)

**:use-smooth-shader**

[GL/X表示](jmanual.html#34807) | [GL/X表示](jmanual.html#35125)

**:velocity**

[ユーティリティ関数](jmanual.html#37956) |
[ユーティリティ関数](jmanual.html#38068)

**:velocity-list**

[ユーティリティ関数](jmanual.html#37966) |
[ユーティリティ関数](jmanual.html#38058)

**:vertices**

[ポイントクラウドデータ](jmanual.html#28121) |
[ポイントクラウドデータ](jmanual.html#28813) |
[GL/X表示](jmanual.html#34668) | [GL/X表示](jmanual.html#34708) |
[GL/X表示](jmanual.html#34767) | [GL/X表示](jmanual.html#35166)

**:view-coords**

[ポイントクラウドデータ](jmanual.html#28165) |
[ポイントクラウドデータ](jmanual.html#28769)

**:viewangle-inlier**

[ポイントクラウドデータ](jmanual.html#28286) |
[ポイントクラウドデータ](jmanual.html#28648)

**:viewer**

[ロボットビューワ](jmanual.html#21493) |
[ロボットビューワ](jmanual.html#21878)

**:viewing**

[センサモデル](jmanual.html#15470) | [センサモデル](jmanual.html#15732)

**:viewpoint**

[ロボットビューワ](jmanual.html#21559) |
[ロボットビューワ](jmanual.html#21812)

**:viewtarget**

[ロボットビューワ](jmanual.html#21548) |
[ロボットビューワ](jmanual.html#21823)

**:weight**

[ロボットモデル](jmanual.html#6565) |
[ロボットモデル](jmanual.html#6806) |
[動力学計算・歩行動作生成](jmanual.html#18315)

**:width**

[センサモデル](jmanual.html#15328) | [センサモデル](jmanual.html#15657)
| [ポイントクラウドデータ](jmanual.html#28143) |
[ポイントクラウドデータ](jmanual.html#28791)

**:worldcoords**

[ロボットモデル](jmanual.html#11090) |
[ロボットモデル](jmanual.html#11290) |
[ロボットモデル](jmanual.html#11334) |
[ロボットモデル](jmanual.html#6545) |
[ロボットモデル](jmanual.html#6826) |
[ロボットモデル](jmanual.html#10918)

**:wrench-list-&gt;wrench-vector**

[動力学計算・歩行動作生成](jmanual.html#18369)

**:wrench-vector-&gt;wrench-list**

[動力学計算・歩行動作生成](jmanual.html#18380)

**:write-to-dot**

[グラフ表現](jmanual.html#30560) | [グラフ表現](jmanual.html#31248)

**:write-to-dot-stream**

[グラフ表現](jmanual.html#30550) | [グラフ表現](jmanual.html#31258)

**:write-to-file**

[グラフ表現](jmanual.html#30570) | [グラフ表現](jmanual.html#31238)

**:write-to-image-file**

[GL/X表示](jmanual.html#34425)

**:write-to-pdf**

[グラフ表現](jmanual.html#30580) | [グラフ表現](jmanual.html#31228)

**a-graph-search-solver**

[グラフ表現](jmanual.html#32297)

**all-child-links**

[ロボットモデル](jmanual.html#8099)

**append-multiple-obj-virtual-joint**

[ロボットモデル](jmanual.html#8187)

**append-obj-virtual-joint**

[ロボットモデル](jmanual.html#8176)

**arc**

[グラフ表現](jmanual.html#30427)

**arced-node**

[グラフ表現](jmanual.html#31291)

**atan2**

[数学関数](jmanual.html#39330)

**best-first-graph-search-solver**

[グラフ表現](jmanual.html#32147)

**body-to-faces**

[ロボットモデル](jmanual.html#11417)

**body-to-triangles**

[ロボットモデル](jmanual.html#11581)

**bodyset**

[ロボットモデル](jmanual.html#11240)

**bodyset-link**

[ロボットモデル](jmanual.html#6517) |
[動力学計算・歩行動作生成](jmanual.html#17774) |
[動力学計算・歩行動作生成](jmanual.html#18139)

**breadth-first-graph-search-solver**

[グラフ表現](jmanual.html#31891)

**bumper-model**

[センサモデル](jmanual.html#15186)

**bvh-6dof-joint**

[BVHデータ](jmanual.html#23441)

**bvh-link**

[BVHデータ](jmanual.html#23187)

**bvh-robot-model**

[BVHデータ](jmanual.html#23591)

**bvh-sphere-joint**

[BVHデータ](jmanual.html#23291)

**bvh2eus**

[BVHデータ](jmanual.html#24548)

**calc-angle-speed-gain-scalar**

[ロボットモデル](jmanual.html#8077)

**calc-angle-speed-gain-vector**

[ロボットモデル](jmanual.html#8088)

**calc-dif-with-axis**

[ロボットモデル](jmanual.html#8110)

**calc-inertia-matrix-linear**

[動力学計算・歩行動作生成](jmanual.html#19666)

**calc-inertia-matrix-rotational**

[動力学計算・歩行動作生成](jmanual.html#19655)

**calc-jacobian-default-rotate-vector**

[ロボットモデル](jmanual.html#8044)

**calc-jacobian-from-link-list-including-robot-and-obj-virtual-joint**

[ロボットモデル](jmanual.html#8165)

**calc-jacobian-linear**

[ロボットモデル](jmanual.html#8066)

**calc-jacobian-rotational**

[ロボットモデル](jmanual.html#8055)

**calc-joint-angle-min-max-for-limit-calculation**

[ロボットモデル](jmanual.html#8132)

**calc-target-joint-dimension**

[ロボットモデル](jmanual.html#8121)

**camera-model**

[センサモデル](jmanual.html#15290)

**cascaded-coords**

[ロボットモデル](jmanual.html#11029) |
[ロボットモデル](jmanual.html#11142) |
[ロボットモデル](jmanual.html#11211) |
[ロボット動作と干渉計算](jmanual.html#22654)

**cascaded-link**

[ロボットモデル](jmanual.html#6859) |
[動力学計算・歩行動作生成](jmanual.html#17858) |
[動力学計算・歩行動作生成](jmanual.html#18278)

**cmu-bvh-robot-model**

[BVHデータ](jmanual.html#24499)

**cmu-bvh2eus**

[BVHデータ](jmanual.html#24580)

**collada::-&gt;string**

[Colladaデータ](jmanual.html#26083)

**collada::cat-clark**

[Colladaデータ](jmanual.html#26160)

**collada::cat-normal**

[Colladaデータ](jmanual.html#26149)

**collada::collada-geometry-id-base**

[Colladaデータ](jmanual.html#26721)

**collada::collada-geometry-name-base**

[Colladaデータ](jmanual.html#26732)

**collada::collada-joint-id**

[Colladaデータ](jmanual.html#26655)

**collada::collada-node-id**

[Colladaデータ](jmanual.html#26303)

**collada::collada-node-name**

[Colladaデータ](jmanual.html#26314)

**collada::enum-integer-list**

[Colladaデータ](jmanual.html#26820)

**collada::estimate-class-name**

[Colladaデータ](jmanual.html#26842)

**collada::eusmodel-description**

[Colladaデータ](jmanual.html#26012)

**collada::eusmodel-description-&gt;collada**

[Colladaデータ](jmanual.html#26042)

**collada::eusmodel-description-&gt;collada-kinematics-model**

[Colladaデータ](jmanual.html#26490)

**collada::eusmodel-description-&gt;collada-library-articulated-systems**

[Colladaデータ](jmanual.html#26578)

**collada::eusmodel-description-&gt;collada-library-geometries**

[Colladaデータ](jmanual.html#26710)

**collada::eusmodel-description-&gt;collada-library-joints**

[Colladaデータ](jmanual.html#26633)

**collada::eusmodel-description-&gt;collada-library-kinematics-models**

[Colladaデータ](jmanual.html#26479)

**collada::eusmodel-description-&gt;collada-library-kinematics-scenes**

[Colladaデータ](jmanual.html#26468)

**collada::eusmodel-description-&gt;collada-library-physics-models**

[Colladaデータ](jmanual.html#26512)

**collada::eusmodel-description-&gt;collada-library-physics-scenes**

[Colladaデータ](jmanual.html#26501)

**collada::eusmodel-description-&gt;collada-library-visual-scenes**

[Colladaデータ](jmanual.html#26435)

**collada::eusmodel-description-&gt;collada-links**

[Colladaデータ](jmanual.html#26545)

**collada::eusmodel-description-&gt;collada-links-tree**

[Colladaデータ](jmanual.html#26600)

**collada::eusmodel-description-&gt;collada-node**

[Colladaデータ](jmanual.html#26424)

**collada::eusmodel-description-&gt;collada-node-transformations**

[Colladaデータ](jmanual.html#26413)

**collada::eusmodel-description-&gt;collada-scene**

[Colladaデータ](jmanual.html#26699)

**collada::eusmodel-endcoords-description**

[Colladaデータ](jmanual.html#26259)

**collada::eusmodel-endcoords-description-&gt;openrave-manipulator**

[Colladaデータ](jmanual.html#26589)

**collada::eusmodel-endcoords-specs**

[Colladaデータ](jmanual.html#26226)

**collada::eusmodel-joint-description**

[Colladaデータ](jmanual.html#26248)

**collada::eusmodel-joint-spec**

[Colladaデータ](jmanual.html#26204)

**collada::eusmodel-joint-specs**

[Colladaデータ](jmanual.html#26032)

**collada::eusmodel-limit-spec**

[Colladaデータ](jmanual.html#26215)

**collada::eusmodel-link-description**

[Colladaデータ](jmanual.html#26237)

**collada::eusmodel-link-spec**

[Colladaデータ](jmanual.html#26182)

**collada::eusmodel-link-specs**

[Colladaデータ](jmanual.html#26022)

**collada::eusmodel-mesh-spec**

[Colladaデータ](jmanual.html#26193)

**collada::find-child-link-descriptions**

[Colladaデータ](jmanual.html#26567)

**collada::find-joint-from-link-description**

[Colladaデータ](jmanual.html#26556)

**collada::find-link-from-links-description**

[Colladaデータ](jmanual.html#26534)

**collada::find-parent-liks-from-link-description**

[Colladaデータ](jmanual.html#26402)

**collada::find-root-link-from-joints-description**

[Colladaデータ](jmanual.html#26523)

**collada::float-vector-&gt;collada-string**

[Colladaデータ](jmanual.html#26809)

**collada::joint-description-&gt;collada-instance-joint**

[Colladaデータ](jmanual.html#26622)

**collada::joint-description-&gt;collada-joint**

[Colladaデータ](jmanual.html#26666)

**collada::joints-description-&gt;collada-instance-joints**

[Colladaデータ](jmanual.html#26611)

**collada::joints-description-&gt;collada-joints**

[Colladaデータ](jmanual.html#26644)

**collada::linear-joint-description-&gt;collada-joint**

[Colladaデータ](jmanual.html#26677)

**collada::link-description-&gt;collada-bind-material**

[Colladaデータ](jmanual.html#26457)

**collada::link-description-&gt;collada-effects**

[Colladaデータ](jmanual.html#26369)

**collada::link-description-&gt;collada-geometry**

[Colladaデータ](jmanual.html#26765)

**collada::link-description-&gt;collada-materials**

[Colladaデータ](jmanual.html#26336)

**collada::links-description-&gt;collada-geometries**

[Colladaデータ](jmanual.html#26743)

**collada::links-description-&gt;collada-library-effects**

[Colladaデータ](jmanual.html#26358)

**collada::links-description-&gt;collada-library-materials**

[Colladaデータ](jmanual.html#26325)

**collada::make-attr**

[Colladaデータ](jmanual.html#26105)

**collada::make-xml**

[Colladaデータ](jmanual.html#26116)

**collada::matrix-&gt;collada-rotate-vector**

[Colladaデータ](jmanual.html#26052)

**collada::matrix-&gt;collada-string**

[Colladaデータ](jmanual.html#26391)

**collada::mesh-&gt;collada-indices**

[Colladaデータ](jmanual.html#26776)

**collada::mesh-description-&gt;collada-effect**

[Colladaデータ](jmanual.html#26380)

**collada::mesh-description-&gt;collada-material**

[Colladaデータ](jmanual.html#26347)

**collada::mesh-description-&gt;instance-material**

[Colladaデータ](jmanual.html#26446)

**collada::mesh-normals-&gt;collada-string**

[Colladaデータ](jmanual.html#26798)

**collada::mesh-object-&gt;collada-mesh**

[Colladaデータ](jmanual.html#26754)

**collada::mesh-vertices-&gt;collada-string**

[Colladaデータ](jmanual.html#26787)

**collada::range2**

[Colladaデータ](jmanual.html#26281)

**collada::remove-directory-name**

[Colladaデータ](jmanual.html#26853)

**collada::rotational-joint-description-&gt;collada-joint**

[Colladaデータ](jmanual.html#26688)

**collada::search-minimum-rotation-matrix**

[Colladaデータ](jmanual.html#26831)

**collada::setup-collada-filesystem**

[Colladaデータ](jmanual.html#26270)

**collada::string-append**

[Colladaデータ](jmanual.html#26094)

**collada::sxml-&gt;xml**

[Colladaデータ](jmanual.html#26127)

**collada::symbol-&gt;string**

[Colladaデータ](jmanual.html#26072)

**collada::verificate-unique-strings**

[Colladaデータ](jmanual.html#26171)

**collada::xml-output-to-string-stream**

[Colladaデータ](jmanual.html#26138)

**color-category10**

[ユーティリティ関数](jmanual.html#38200)

**color-category20**

[ユーティリティ関数](jmanual.html#38210)

**combination**

[ユーティリティ関数](jmanual.html#38108)

**concatenate-matrix-column**

[数学関数](jmanual.html#39451)

**concatenate-matrix-diagonal**

[数学関数](jmanual.html#39471)

**concatenate-matrix-row**

[数学関数](jmanual.html#39461)

**connect-server-until-success**

[ユーティリティ関数](jmanual.html#38139)

**convert-irtmodel-to-collada**

[Colladaデータ](jmanual.html#26062)

**coordinates**

[ロボットモデル](jmanual.html#10929) |
[ロボットモデル](jmanual.html#10978) |
[ロボットモデル](jmanual.html#11102) |
[ロボットモデル](jmanual.html#11182) | [GL/X表示](jmanual.html#34640)

**costed-arc**

[グラフ表現](jmanual.html#30883)

**costed-graph**

[グラフ表現](jmanual.html#30945)

**covariance-matrix**

[数学関数](jmanual.html#39491)

**depth-first-graph-search-solver**

[グラフ表現](jmanual.html#32019)

**diagonal**

[数学関数](jmanual.html#39310)

**directed-graph**

[グラフ表現](jmanual.html#30533)

**eigen-decompose**

[数学関数](jmanual.html#39533)

**eus-server**

[ユーティリティ関数](jmanual.html#38129)

**eus2collada**

[Colladaデータ](jmanual.html#26292)

**eusmodel-validity-check**

[ロボットモデル](jmanual.html#8034)

**eusmodel-validity-check-one**

[ロボットモデル](jmanual.html#8198)

**extended-preview-controller**

[動力学計算・歩行動作生成](jmanual.html#18830)

**faceset**

[GL/X表示](jmanual.html#34590)

**find-extreams**

[ユーティリティ関数](jmanual.html#38118)

**float-vector**

[GL/X表示](jmanual.html#34680)

**format-array**

[ユーティリティ関数](jmanual.html#38150)

**forward-message-to**

[ユーティリティ関数](jmanual.html#38078)

**forward-message-to-all**

[ユーティリティ関数](jmanual.html#38088)

**gait-generator**

[動力学計算・歩行動作生成](jmanual.html#19198)

**gaussian-random**

[数学関数](jmanual.html#39441)

**geometry::face-to-tessel-triangle**

[ロボットモデル](jmanual.html#11407)

**geometry::face-to-triangle**

[ロボットモデル](jmanual.html#11397)

**geometry::face-to-triangle-aux**

[ロボットモデル](jmanual.html#11387)

**geometry::face-to-triangle-make-simple**

[ロボットモデル](jmanual.html#11570)

**geometry::face-to-triangle-rest-polygon**

[ロボットモデル](jmanual.html#11559)

**geometry::make-faceset-from-vertices**

[ロボットモデル](jmanual.html#11518)

**geometry::quaternion-from-two-vectors**

[ロボットモデル](jmanual.html#11538)

**geometry::triangle-to-triangle**

[ロボットモデル](jmanual.html#11592)

**gl::\_dump-wrl-shape**

[GL/X表示](jmanual.html#35549)

**gl::delete-displaylist-id**

[GL/X表示](jmanual.html#35505)

**gl::draw-glbody**

[GL/X表示](jmanual.html#35538)

**gl::draw-globjects**

[GL/X表示](jmanual.html#35527)

**gl::find-color**

[GL/X表示](jmanual.html#35433)

**gl::glbody**

[GL/X表示](jmanual.html#35350)

**gl::glvertices**

[GL/X表示](jmanual.html#34720)

**gl::make-glvertices-from-faces**

[GL/X表示](jmanual.html#35463)

**gl::make-glvertices-from-faceset**

[GL/X表示](jmanual.html#35453)

**gl::reset-gl-attribute**

[GL/X表示](jmanual.html#35494)

**gl::set-stereo-gl-attribute**

[GL/X表示](jmanual.html#35483)

**gl::transparent**

[GL/X表示](jmanual.html#35443)

**gl::transpose-image-rows**

[GL/X表示](jmanual.html#35516)

**gl::write-wrl-from-glvertices**

[GL/X表示](jmanual.html#35473)

**glviewsurface**

[GL/X表示](jmanual.html#34397)

**graph**

[グラフ表現](jmanual.html#31029)

**graph-search-solver**

[グラフ表現](jmanual.html#31631)

**height-of-cylinder**

[ロボットモデル](jmanual.html#11488)

**his2rgb**

[ユーティリティ関数](jmanual.html#38160)

**hvs2rgb**

[ユーティリティ関数](jmanual.html#38170)

**interpolator**

[ユーティリティ関数](jmanual.html#37621)

**inverse-matrix**

[数学関数](jmanual.html#39300)

**irtviewer-dummy**

[ロボットビューワ](jmanual.html#21970)

**joint**

[ロボットモデル](jmanual.html#5025) |
[動力学計算・歩行動作生成](jmanual.html#17600) |
[動力学計算・歩行動作生成](jmanual.html#17953)

**joint-angle-limit-nspace**

[ロボットモデル](jmanual.html#8154)

**joint-angle-limit-weight**

[ロボットモデル](jmanual.html#8143)

**kbhit**

[ユーティリティ関数](jmanual.html#38220)

**line**

[ロボットモデル](jmanual.html#10901) | [GL/X表示](jmanual.html#34561)

**linear-interpolator**

[ユーティリティ関数](jmanual.html#37901)

**linear-joint**

[ロボットモデル](jmanual.html#5625) |
[動力学計算・歩行動作生成](jmanual.html#17658) |
[動力学計算・歩行動作生成](jmanual.html#18077)

**lmeds**

[数学関数](jmanual.html#39577)

**lmeds-error**

[数学関数](jmanual.html#39588)

**lmeds-error-mat**

[数学関数](jmanual.html#39599)

**lms**

[数学関数](jmanual.html#39544)

**lms-error**

[数学関数](jmanual.html#39566)

**lms-estimate**

[数学関数](jmanual.html#39555)

**load-mcd**

[BVHデータ](jmanual.html#24559)

**make-bvh-robot-model**

[BVHデータ](jmanual.html#24611)

**make-camera-from-param**

[センサモデル](jmanual.html#15743)

**make-default-robot-link**

[ロボットモデル](jmanual.html#13817)

**make-fan-cylinder**

[ロボットモデル](jmanual.html#11447)

**make-irtviewer**

[ロボットビューワ](jmanual.html#22031)

**make-irtviewer-dummy**

[ロボットビューワ](jmanual.html#22074)

**make-random-pointcloud**

[ポイントクラウドデータ](jmanual.html#28846)

**make-ring**

[ロボットモデル](jmanual.html#11437)

**make-robot-model-from-name**

[ユーティリティ関数](jmanual.html#38240)

**make-sphere**

[ロボットモデル](jmanual.html#11427)

**manipulability**

[数学関数](jmanual.html#39421)

**mapjoin**

[ユーティリティ関数](jmanual.html#38250)

**matrix-exponent**

[数学関数](jmanual.html#39381)

**matrix-log**

[数学関数](jmanual.html#39371)

**matrix-to-euler-angle**

[ロボットモデル](jmanual.html#11528)

**matrix2quaternion**

[数学関数](jmanual.html#39351)

**midcoords**

[ロボットモデル](jmanual.html#11367)

**midrot**

[数学関数](jmanual.html#39391)

**minjerk-interpolator**

[ユーティリティ関数](jmanual.html#37939)

**minor-matrix**

[数学関数](jmanual.html#39320)

**motion-capture-data**

[BVHデータ](jmanual.html#23851)

**mtimer**

[ユーティリティ関数](jmanual.html#37543)

**need-thread**

[ユーティリティ関数](jmanual.html#38261)

**node**

[グラフ表現](jmanual.html#30233)

**normalize-vector**

[数学関数](jmanual.html#39501)

**objects**

[ロボットビューワ](jmanual.html#22063)

**omniwheel-joint**

[ロボットモデル](jmanual.html#5957) |
[動力学計算・歩行動作生成](jmanual.html#17687)

**orient-coords-to-axis**

[ロボットモデル](jmanual.html#11377)

**outer-product-matrix**

[数学関数](jmanual.html#39340)

**parse-bvh-sexp**

[BVHデータ](jmanual.html#24600)

**permutation**

[ユーティリティ関数](jmanual.html#38098)

**piped-fork-returns-list**

[ユーティリティ関数](jmanual.html#38230)

**pointcloud**

[ポイントクラウドデータ](jmanual.html#27907)

**polygon**

[GL/X表示](jmanual.html#34532)

**pqp-collision-check**

[ロボット動作と干渉計算](jmanual.html#22682)

**pqp-collision-check-objects**

[ロボット動作と干渉計算](jmanual.html#22704)

**pqp-collision-distance**

[ロボット動作と干渉計算](jmanual.html#22693)

**preview-control-cart-table-cog-trajectory-generator**

[動力学計算・歩行動作生成](jmanual.html#18956)

**preview-controller**

[動力学計算・歩行動作生成](jmanual.html#18564)

**pseudo-inverse**

[数学関数](jmanual.html#39401)

**pseudo-inverse-org**

[数学関数](jmanual.html#39511)

**quaternion2matrix**

[数学関数](jmanual.html#39361)

**radius-of-cylinder**

[ロボットモデル](jmanual.html#11498)

**radius-of-sphere**

[ロボットモデル](jmanual.html#11508)

**random-gauss**

[数学関数](jmanual.html#39431)

**read-bvh**

[BVHデータ](jmanual.html#24538)

**read-image-file**

[画像関数](jmanual.html#39881)

**read-png-file**

[画像関数](jmanual.html#39937)

**rgb2his**

[ユーティリティ関数](jmanual.html#38180)

**rgb2hvs**

[ユーティリティ関数](jmanual.html#38190)

**riccati-equation**

[動力学計算・歩行動作生成](jmanual.html#18502)

**rikiya-bvh-robot-model**

[BVHデータ](jmanual.html#24023)

**rikiya-bvh2eus**

[BVHデータ](jmanual.html#24570)

**robot-model**

[ロボットモデル](jmanual.html#12512)

**rotational-joint**

[ロボットモデル](jmanual.html#5459) |
[動力学計算・歩行動作生成](jmanual.html#17629) |
[動力学計算・歩行動作生成](jmanual.html#18015)

**scene-model**

[環境モデル](jmanual.html#16409)

**sensor-model**

[センサモデル](jmanual.html#15036)

**set-termios-c\_cc**

[ユーティリティ関数](jmanual.html#38393)

**set-termios-c\_cflag**

[ユーティリティ関数](jmanual.html#38327)

**set-termios-c\_iflag**

[ユーティリティ関数](jmanual.html#38283)

**set-termios-c\_ispeed**

[ユーティリティ関数](jmanual.html#38415)

**set-termios-c\_lflag**

[ユーティリティ関数](jmanual.html#38349)

**set-termios-c\_line**

[ユーティリティ関数](jmanual.html#38371)

**set-termios-c\_oflag**

[ユーティリティ関数](jmanual.html#38305)

**set-termios-c\_ospeed**

[ユーティリティ関数](jmanual.html#38437)

**solver**

[グラフ表現](jmanual.html#31547)

**solver-node**

[グラフ表現](jmanual.html#31375)

**sphere-joint**

[ロボットモデル](jmanual.html#6123) |
[動力学計算・歩行動作生成](jmanual.html#17716)

**sr-inverse**

[数学関数](jmanual.html#39411)

**sr-inverse-org**

[数学関数](jmanual.html#39522)

**termios-c\_cc**

[ユーティリティ関数](jmanual.html#38382)

**termios-c\_cflag**

[ユーティリティ関数](jmanual.html#38316)

**termios-c\_iflag**

[ユーティリティ関数](jmanual.html#38272)

**termios-c\_ispeed**

[ユーティリティ関数](jmanual.html#38404)

**termios-c\_lflag**

[ユーティリティ関数](jmanual.html#38338)

**termios-c\_line**

[ユーティリティ関数](jmanual.html#38360)

**termios-c\_oflag**

[ユーティリティ関数](jmanual.html#38294)

**termios-c\_ospeed**

[ユーティリティ関数](jmanual.html#38426)

**transform-coords**

[ロボットモデル](jmanual.html#11548)

**tum-bvh-robot-model**

[BVHデータ](jmanual.html#24459)

**tum-bvh2eus**

[BVHデータ](jmanual.html#24590)

**vector-variance**

[数学関数](jmanual.html#39481)

**viewer**

[ロボットビューワ](jmanual.html#21425)

**viewer-dummy**

[ロボットビューワ](jmanual.html#21930)

**viewing**

[ロボットビューワ](jmanual.html#21901)

**wheel-joint**

[ロボットモデル](jmanual.html#5791)

**write-image-file**

[画像関数](jmanual.html#39893)

**write-png-file**

[画像関数](jmanual.html#39948)

**x-of-cube**

[ロボットモデル](jmanual.html#11458)

**x::draw-things**

[ロボットビューワ](jmanual.html#22052)

**x::event-far**

[GL/X表示](jmanual.html#36933)

**x::event-near**

[GL/X表示](jmanual.html#36944)

**x::irtviewer**

[ロボットビューワ](jmanual.html#21465)

**x::make-lr-ud-coords**

[ロボットビューワ](jmanual.html#22041)

**x::panel-tab-button-item**

[GL/X表示](jmanual.html#36883)

**x::tabbed-panel**

[GL/X表示](jmanual.html#36733)

**x::window-main-one**

[GL/X表示](jmanual.html#36922)

**x:panel**

[GL/X表示](jmanual.html#36675)

**x:xscroll-bar**

[GL/X表示](jmanual.html#36704)

**x:xwindow**

[GL/X表示](jmanual.html#36569)

**y-of-cube**

[ロボットモデル](jmanual.html#11468)

**z-of-cube**

[ロボットモデル](jmanual.html#11478)

[About this document ...]()
===========================

****EusLisp** **EusLisp version 9.00/ irteus version 1.00**
**リファレンスマニュアル** -ロボットモデリングの拡張- ETL-TR-95-19 +
JSK-TR-10-03 November 24, 2017**

This document was generated using the
[**LaTeX**2`HTML`](http://www.latex2html.org/) translator Version 2008
(1.71)

Copyright © 1993, 1994, 1995, 1996, Nikos Drakos, Computer Based
Learning Unit, University of Leeds. Copyright © 1997, 1998, 1999, [Ross
Moore](http://www.maths.mq.edu.au/~ross/), Mathematics Department,
Macquarie University, Sydney.

The command line arguments were: **latex2html**
`-dir /tmp/html/ -local_icons -auto_prefix -iso_language JP jmanual -split 1 -no_navigation`

The translation was initiated by Kei Okada on 2017-11-24

------------------------------------------------------------------------

Kei Okada 2017-11-24
