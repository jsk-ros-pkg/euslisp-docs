### play-sound ###
&nbsp;&nbsp;&nbsp;*sound* *&key* *arg2* <br>&nbsp;&nbsp;&nbsp;*(topic-name robotsound)* <br>&nbsp;&nbsp;&nbsp;*wait* <br>&nbsp;&nbsp;&nbsp;*(volume 1.0)* 

- Plays sound using sound_play server <br>
   Args: <br>
     sound: if sound is pathname, plays sound file located at given path <br>
            if it is number, server plays builtin sound <br>
            otherwise server plays sound as speech sentence <br>
     topic-name: namespace of sound_play server <br>
     wait: wait until sound is played <br>
     volume: Volume at which to play the sound. 0 is mute, 1.0 is 100%. <br>


### speak ###
&nbsp;&nbsp;&nbsp;*text* *&key* *(lang )* <br>&nbsp;&nbsp;&nbsp;*(topic-name robotsound)* <br>&nbsp;&nbsp;&nbsp;*wait* <br>&nbsp;&nbsp;&nbsp;*(volume 1.0)* 

- Speak sentence using text-to-speech services. <br>
   Args: <br>
     text: sentence to speak <br>
     lang: language to speak, currently :en or :ja are supported. <br>
     topic-name: namespace of sound_play node <br>
     wait: wait the end of speech if enabled <br>
     volume: Volume at which to play the sound. 0 is mute, 1.0 is 100%. <br>


### speak-en ###
&nbsp;&nbsp;&nbsp;*text* *&key* *(topic-name robotsound)* <br>&nbsp;&nbsp;&nbsp;*wait* <br>&nbsp;&nbsp;&nbsp;*(volume 1.0)* 

- Speak english sentence <br>


### speak-jp ###
&nbsp;&nbsp;&nbsp;*text* *&key* *(topic-name robotsound_jp)* <br>&nbsp;&nbsp;&nbsp;*wait* <br>&nbsp;&nbsp;&nbsp;*(volume 1.0)* 

- Speak japanese sentence <br>


