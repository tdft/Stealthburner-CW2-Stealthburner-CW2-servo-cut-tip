# Stealthburner-CW2-Stealthburner-CW2-servo-cut-tip
Cutting tip of Stealthburner-CW2 servo steering gear

bilibili UID:450728302

by 3D造物主

![1708151775972fbb961577cd52aac32a4a684aa6e033fde65](https://github.com/tdft/Stealthburner-CW2-Stealthburner-CW2-servo-cut-tip/assets/52322373/c964d3c7-3fd0-443d-9fc7-fa530b00edc1)

新版Happy-Hare可根据官方教程修改 https://github.com/moggieuk/Happy-Hare 进行修改

具体测试见哔哩哔哩【voron sb头舵机切刀测试】https://www.bilibili.com/video/BV19A4m157Jf?vd_source=c7e963c712fcfe6c78b672060f20e54b

https://github.com/tdft/Stealthburner-CW2-Stealthburner-CW2-servo-cut-tip/assets/52322373/de4dca90-7167-4381-bc9b-4ff3145389e5



本人对于所有事故不承担任何责任（包括但不限于触电、电器损坏等）

安装模块前务必断电拆机以免发生安全事故。

MG90S舵机待验证！！！

MG90S舵机待验证！！！

MG90S舵机待验证！！！

安装前准备：

M3热熔螺母3*4*5若干

M3*12螺丝一颗

ES08MD舵机*1（MG90S）

电烙铁

cw2螺丝套装

舵机线预留100mm+

调试前拆掉切刀撞块，以免损坏部件
![IMG_20240216_155934](https://github.com/tdft/Stealthburner-CW2-Stealthburner-CW2-servo-cut-tip/assets/52322373/cd3275af-8d06-4722-98c8-d851bb7323f9)

mmu/base/mmu_filametrix.cfg内的如下需根据机器大小、清理位置进行修改，仅参考

    ######################################
    
    #这应设置为从内部喷嘴尖端到切割刀片的距离，并用于上的计算
    #灯丝末端的最终位置(输出_停止_位置)和剩余灯丝片段大小(输出_剩余_灯丝)
    #供快乐野兔使用
    variable_blade_pos:37.5
    
    #切割前缩回的距离，这减少了浪费的细丝，但可能会造成堵塞
    #如果设置太大和/或轴端组件中有间隙
    #这必须小于“balde_pos”，即从喷嘴到刀具的距离
    variable_retract_length:32.5
    
    #销的位置，这应该是刀具架切刀时的位置
    #轻轻碰一下针
    variable_pin_loc_x:165 #22
    variable_pin_loc_y: 305
    
    #切割时的开始和结束位置
    #具体来说，不是通过移动到上面的销位置来进行切割，
    #我们沿X轴留有一个小的安全余量，以避免旅行时刮伤销
    #这也应该提供一小段距离，以便在按压销时产生一些动量
    variable_pin_park_x_dist:0 #9
    
    #刀具完全压缩时刀具架的位置
    #应留有较小的净空(例如，应稍大于0，或xmin值),以避免撞击工具架或台架
    variable_pin_loc_x_compressed: 165  #1
    
    #切削后缩回长度和速度，以便刀具可以回到原点位置
    variable_rip_length: 1			#收回以帮助手柄减压的距离(> = 0)
    variable_rip_speed: 750
    
    #将剩余的尖端从冷端推入热端。不能大于“回缩_长度”
    variable_pushback_length: 10
    variable_pushback_dwell_time: 0		#推后停留的时间
    ##########################################################################

安装

##########################################################################
1.
安装模块后需将mmu/base/mmu_hardware.cfg中的FILAMETRIX切丝机的可选龙门伺服系统[mmu_servo mmu_gantry_servo]
打开
参数仅供ES08MD数字舵机使用！！！！
    #用于FILAMETRIX切丝机的可选龙门伺服系统-
    #基本伺服PWM设置。如果这些值改变，那么为不同位置定义的角度也将改变
    #
    #仅当有机架伺服时取消注释
    
    #######################################
    
      [mmu_servo mmu_gantry_servo]
    pin:  EBBCan:PB9
    maximum_servo_angle: 180
    minimum_pulse_width: 0.0005
    maximum_pulse_width: 0.0025
    initial_angle: 130
    #######################################

2.
打开mmu/base/mmu_filametrix.cfg，启用机架伺服选项
    
    ########################################
    
    #如果安装了机架伺服选项，启用伺服并设置上下角度位置
    variable_gantry_servo_enabled: 1
    variable_gantry_servo_down_angle: -5  #即切料
    variable_gantry_servo_up_angle: 130
    
    ########################################

*3.选用
(直接复制替换掉mmu/base/mmu_filametrix.cfg内的参数)
    
    ########################################
    
    [gcode_macro _FILAMETRIX_GANTRY_SERVO_DOWN]
    description: Operate optional gantry servo operated pin
    gcode:
        {% if printer['gcode_macro _MMU_CUT_TIP']['gantry_servo_enabled'] > 0 %}
            {% set ANGLE = printer['gcode_macro _MMU_CUT_TIP']['gantry_servo_down_angle'] | float %}
            status_busy
            SET_SERVO SERVO=mmu_gantry_servo ANGLE={ANGLE}
            G4 P500
            _FILAMETRIX_GANTRY_SERVO_UP
            G4 P300
            SET_SERVO SERVO=mmu_gantry_servo ANGLE={ANGLE}
            G4 P200
            _FILAMETRIX_GANTRY_SERVO_UP
            SET_SERVO SERVO=mmu_gantry_servo ANGLE={ANGLE}
            G4 P600
            status_part_ready
        {% endif %}
    
    [gcode_macro _FILAMETRIX_GANTRY_SERVO_UP]
    description: Operate optional gantry servo operated pin
    gcode:
        {% if printer['gcode_macro _MMU_CUT_TIP']['gantry_servo_enabled'] > 0 %}
            {% set ANGLE = printer['gcode_macro _MMU_CUT_TIP']['gantry_servo_up_angle'] | float %}
            SET_SERVO SERVO=mmu_gantry_servo ANGLE={ANGLE}
        {% endif %}
        
    ########################################
