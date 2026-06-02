# 🚁 UAV-Vision 서브시스템 (담당자 1, 2, 3)

## 📌 시스템 아키텍처
드론의 비행, 탐색, 그리고 비전을 통한 정밀 착륙을 담당하는 파트의 ROS2 노드 및 토픽 통신 구조도입니다.

```mermaid
graph TD
    %% 가상 환경
    Env([🌍 Gazebo 및 PX4 가상 환경])
    
    %% 3개의 핵심 노드
    N_Vision([👁️ 1. 비전/저장])
    N_UAV([🚁 2. 비행/탐색])
    N_Land([🎯 3. 정밀 착륙])

    %% 토픽 흐름
    Env -->|카메라 영상| N_Vision
    Env -->|UAV 현재 위치| N_UAV

    N_Vision -->|마커 ID & 3D 좌표| N_Land
    
    N_UAV -.->|미션 상태 변경 신호| N_Vision
    N_UAV -.->|미션 상태 변경 신호| N_Land

    N_Land -->|UAV 하강 제어 명령| Env
    N_UAV -->|UAV 목표 위치 명령| Env

    %% 스타일링
    classDef nodeStyle fill:#f9f9f9,stroke:#333,stroke-width:2px;
    class N_Vision,N_UAV,N_Land nodeStyle;
```

## 💻 3인 담당자별 통신 명세서
코드를 작성할 때 아래의 토픽 이름과 메시지 타입을 반드시 지켜주세요!

### 1. 승윤: 비전/마커 저장 (`/aruco_detector`)
* **Sub:** `/uav/camera/image_raw` (`sensor_msgs/Image`), `/mission_state` (`std_msgs/Int32`)
* **Pub:** `/aruco/marker_pose` (`geometry_msgs/PoseStamped`)

### 2. 재형: UAV 비행/탐색 (`/uav_waypoint_follower`)
* **Sub:** `/mavros/local_position/pose` (`geometry_msgs/PoseStamped`)
* **Pub:** `/mavros/setpoint_position/local` (`geometry_msgs/PoseStamped`), `/mission_state` (`std_msgs/Int32`)

### 3. 예림: 정밀 착륙 (`/precision_landing`)
* **Sub:** `/aruco/marker_pose` (`geometry_msgs/PoseStamped`), `/mission_state` (`std_msgs/Int32`)
* **Pub:** `/mavros/setpoint_velocity/cmd_vel` (`geometry_msgs/Twist`)
