# articubot_one - mo hinh AGV 4 banh

Ban nay thay mo hinh robot 2 banh + caster trong `robot_core.xacro` bang
4 banh LF/RF/LR/RR, giu ten package `articubot_one` va launch file
`rsp.launch.py` de tiep tuc dung workflow hien tai.

## Quy uoc toa do
- X duong: phia truoc xe; Y duong: ben trai; Z duong: huong len.
- `base_link` nam tai tam hinh hoc cua bon tam banh.
- LF: (X=+0.075, Y=+0.108) m
- RF: (X=+0.075, Y=-0.108) m
- LR: (X=-0.075, Y=+0.108) m
- RR: (X=-0.075, Y=-0.108) m
- Ban kinh banh 0.0375 m; be rong 0.0383 m.

## Pham vi ban nay
Hien thi 4 banh va cac khung TF cua banh. Khong them hinh hoc chassis,
LiDAR, camera, HC-SR04 hay cac bo phan khac. Banh duoc mo ta bang hinh tru
don gian, khong phai mesh rieng xuat tu FreeCAD. Vi vay day la mo hinh URDF
de kiem tra bo tri/TF, chua phai ban mo phong vat ly hoan chinh.

## Build va chay (ROS 2 Humble)
Dat thu muc `articubot_one` vao `~/ros2_ws/src/` (doi ten thu muc giai nen
thanh `articubot_one` neu can), sau do:

    cd ~/ros2_ws
    source /opt/ros/humble/setup.bash
    colcon build --packages-select articubot_one
    source install/setup.bash
    ros2 launch articubot_one rsp.launch.py

Neu muon xem tren RViz, mo RViz va them RobotModel, chon fixed frame `base_link`.
File launch nay chi chay `robot_state_publisher`; no khong tu mo RViz.

## Luu y
Vi `base_link` dat tren mat phang truc banh, phan banh nam ca tren va duoi
Z=0. Neu can dat banh tiep xuc mat dat trong mo phong, can them offset Z bang
ban kinh banh va xac dinh chuan mat dat. Khoi luong/quan tinh hien tai la gia
tri tam de URDF hop le; can thay bang so lieu that neu dung Gazebo/physics.


## Dieu khien AGV that qua UART STM32F407 (Raspberry Pi 4)

Package co them node `agv_uart_node` de cau noi ROS 2 `/cmd_vel` voi firmware STM32.
Node dung giao thuc nhi phan da co trong script UART ban dau: header `AA 55`,
CRC16-CCITT-FALSE, `CMD_VEL 0x01`, `SET_MODE 0x02`, `ODOM 0x81`, `STATUS 0x84`.

### Cai phu thuoc tren Pi 4

    sudo apt update
    sudo apt install python3-serial ros-humble-teleop-twist-keyboard -y

Dam bao UART serial console da tat, hardware UART da bat, va user co quyen `dialout`.
Day noi: Pi GPIO14/TX (pin 8) -> STM32 PA3/USART2_RX; Pi GPIO15/RX (pin 10)
<- STM32 PA2/USART2_TX; Pi GND (pin 6) -> STM32 GND. Logic UART 3.3 V.

### Build va chay node UART (khong chay Gazebo)

    cd ~/agv_ws
    source /opt/ros/humble/setup.bash
    colcon build --symlink-install --packages-select articubot_one
    source install/setup.bash
    ros2 launch articubot_one uart_control.launch.py

Node mac dinh mo `/dev/serial0` o 115200 baud. Co the doi cong:

    ros2 launch articubot_one uart_control.launch.py port:=/dev/ttyAMA0 baud:=115200

### Bat motor va dieu khien

Motor mac dinh DISABLED. Kiem tra xe da duoc ke an toan khoi mat dat, sau do bat:

    ros2 service call /agv/enable std_srvs/srv/SetBool "{data: true}"

Mo terminal khac va chay ban phim:

    source /opt/ros/humble/setup.bash
    source ~/agv_ws/install/setup.bash
    ros2 run teleop_twist_keyboard teleop_twist_keyboard

Tat motor:

    ros2 service call /agv/enable std_srvs/srv/SetBool "{data: false}"

Node subscribe `/cmd_vel`, gioi han mac dinh 100 mm/s va 400 mrad/s (de thu nghiem
toc do thap, giong script UART ban dau). Neu khong nhan `/cmd_vel` trong 0.5 giay,
node tu gui lenh van toc 0. Node publish encoder raw theo thu tu LF, LR, RF, RR:

- `/agv/encoder_ticks` (`std_msgs/Int32MultiArray`)
- `/agv/encoder_tick_rate` (`std_msgs/Float32MultiArray`, tick/s)
- `/agv/status` (`std_msgs/String`)

Chua publish `nav_msgs/Odometry` vi can xac nhan tick/rev va thong so co khi thuc te.
Khi dieu khien xe that, khong chay launch Gazebo `launch_sim.launch.py` cung luc.
