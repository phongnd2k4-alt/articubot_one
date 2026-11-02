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
