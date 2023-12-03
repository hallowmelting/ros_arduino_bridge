이 브랜치(indigo-devel)는 ROS Indigo와 그 이상 버전을 위한 것이며, Catkin 빌드 시스템을 사용합니다. 또한 ROS Hydro와 호환될 수도 있습니다.

이 ROS 스택에는 ROSArduinoBridge라는 아두이노 라이브러리와 아두이노 기반 로봇을 표준 ROS 메시지 및 서비스를 사용해 제어하는 일련의 ROS 패키지가 포함되어 있습니다. 이 스택은 ROS Serial에 의존하지 **않습니다**.

스택의 주요 기능은 다음과 같습니다:

* Ping 소나 및 Sharp 적외선(GP2D12) 센서에 대한 직접 지원
* 일반 아날로그 및 디지털 센서에서 데이터 읽기도 가능
* 디지털 출력 제어(예: 스위치 또는 LED 켜고 끄기)
* PWM 서보 제어
* 필요한 하드웨어를 사용하는 경우 구성 가능한 베이스 컨트롤러 지원

베이스 컨트롤러는 ROS Twist 메시지를 받아들이고 오도메트리 데이터를 PC로 다시 발행하는 차동 구동 로봇을 위한 것입니다. 베이스 컨트롤러는 모터 컨트롤러와 오도메트리 데이터를 읽기 위한 인코더의 사용을 요구합니다. 현재 버전의 스택은 다음 베이스 컨트롤러 하드웨어를 지원합니다:

* Pololu VNH5019 듀얼 모터 컨트롤러 쉴드 (http://www.pololu.com/catalog/product/2502) 또는 Pololu MC33926 듀얼 모터 쉴드 (http://www.pololu.com/catalog/product/2503).
* Robogaia Mega 인코더 쉴드 (http://www.robogaia.com/two-axis-encoder-counter-mega-shield-version-2.html) 또는 기타 온보드 휠 인코더 카운터.
  
**참고:** Robogaia Mega 인코더 쉴드는 아두이노 메가에서만 사용할 수 있습니다. 온보드 휠 인코더 카운터는 현재 아두이노 우노에서만 지원됩니다.

* L298 모터 드라이버
* 라이브러리는 다른 모터 컨트롤러 및 인코더 하드웨어 또는 라이브러리에 대한 지원을 쉽게 확장할 수 있습니다.

공식 ROS 문서
--------------------------
이 문서의 표준 ROS 스타일 버전은 ROS 위키에서 찾을 수 있습니다:

http://www.ros.org/wiki/ros_arduino_bridge

시스템 요구 사항
-------------------
**Python Serial:** 우분투에서 python-serial 패키지를 설치하려면 다음 명령을 사용하세요:

    $ sudo apt-get install python-serial

비우분투 시스템에서는 다음을 사용하세요:

    $ sudo pip install --upgrade pyserial

또는

    $ sudo easy_install -U pyserial

**아두이노 IDE 1.6.6 이상:**
아두이노 IDE의 조건부 #include 문장 전처리는 1.6.6 이전 버전에서 동작하지 않습니다. ROS Arduino Bridge 펌웨어가 올바르게 컴파일되도록 하려면 아두이노 IDE의 버전 1.6.6 이상을 설치하세요. IDE는 https://www.arduino.cc/en/Main/Software 에서 다운로드할 수 있습니다.

**하드웨어:**
펌웨어는 센서를 읽고 PWM 서보를 제어하기 위해 모든 아두이노 호환 컨트롤러에서 작동해야 합니다. 그러나 베이스 컨트롤러를 사용하기 위해서는 위에 설명된 지원되는 모터 컨트롤러와 인코더 하드웨어가 필요합니다. 이 하드웨어가 없다면, 센서를 읽고 서보를 제어하기 위해 패키지를 여전히 시도할 수 있습니다. 이 문서 끝부분에 있는 참고 섹션에서 이를 수행하는 방법에 대한 지침을 확인하세요.

베이스 컨트롤러를 사용하려면 사용하는 모터 컨트롤러와 인코더에 관한 적절한 라이브러리도 설치해야 합니다. Pololu VNH5019 이중 모터 쉴드의 경우 라이브러리는 다음에서 찾을 수 있습니다:

https://github.com/pololu/Dual-VNH5019-Motor-Shield

Pololu MC33926 이중 모터 쉴드의 경우 라이브러리는 다음에서 찾을 수 있습니다:

https://github.com/pololu/dual-mc33926-motor-shield

Robogaia Mega 인코더 라이브러리는 다음에서 찾을 수 있습니다:

http://www.robogaia.com/uploads/6/8/0/9/6809982/__megaencodercounter-1.3.tar.gz

L298 모터 드라이버는 라이브러리가 필요하지 않습니다.

이러한 라이브러리는 표준 아두이노 스케치북/라이브러리 디렉토리에 설치해야 합니다.

마지막으로, 아두이노 IDE 1.0 이상 버전을 사용하고 있다고 가정합니다.

리눅스에서 시리얼 포트 준비하기
--------------------------------------
아두이노는 /dev/ttyACM# 또는 /dev/ttyUSB# 포트로 리눅스 컴퓨터에 연결될 가능성이 높습니다. 여기서 #은 0, 1, 2 등 서로 다른 장치에 연결되어 있는 개수에 따라 달라지는 숫자입니다. 가장 쉬운 결정 방법은 다른 모든 USB 장치를 분리하고 아두이노를 연결한 다음 명령을 실행하는 것입니다:

    $ ls /dev/ttyACM*

또는 

    $ ls /dev/ttyUSB*

이 중 하나의 명령이 원하는 결과(예: /dev/ttyACM0)를 반환하고 다른 하나는 "No such file or directory" 오류를 반환하기를 바랍니다.

다음으로 포트에 대한 읽기/쓰기 권한이 있는지 확인해야 합니다. 아두이노가 /dev/ttyACM0에 연결되어 있다고 가정하고 명령을 실행하세요:

    $ ls -l /dev/ttyACM0

결과는 다음과 같이 나타날 것입니다:

    crw-rw---- 1 root dialout 166, 0 2013-02-24 08:31 /dev/ttyACM0

root와  "dialout" 그룹만 읽기/쓰기 권한이 있다는 것을 확인할 수 있습니다. 그러므로 dialout 그룹의 멤버여야 합니다. 이는 한 번만 수행하면 나중에 연결하는 모든 USB 장치에 대해 작동해야 합니다.

dialout 그룹에 자신을 추가하려면 명령을 실행하세요:

    $ sudo usermod -a -G dialout your_user_name

your_user_name은 리눅스 로그인 이름입니다. X- 윈도우 세션을 로그아웃했다가 다시 로그인하거나 기계를 재부팅하는 것이 좋습니다.

다시 로그인하면 명령을 시도하세요:

$ groups

그리고 당신이 속한 그룹 목록을 보여주는데, 여기에 dialout이 포함되어 있어야 합니다.

ros_arduino_bridge 스택 설치
----------------------------------------------

    $ cd ~/catkin_workspace/src
    $ git clone https://github.com/hbrobotics/ros_arduino_bridge.git
    $ cd ~/catkin_workspace
    $ catkin_make

제공된 아두이노 라이브러리는 ROSArduinoBridge로 이름이 지어져 있으며 ros_arduino_firmware 패키지 안에 있습니다. 이 스케치는 위에서 설명한 하드웨어 요구 사항에 특화되어 있으나, 이 문서 끝부분에 설명된 내용에 따라 베이스 컨트롤러를 비활성화함으로써 다른 아두이노 타입 보드(예: 우노)와 함께 사용할 수도 있습니다.

ROSArduinoBridge 라이브러리를 설치하려면 다음 단계를 따르세요:

    $ cd SKETCHBOOK_PATH

여기서 SKETCHBOOK_PATH는 아두이노 스케치북 디렉토리의 경로입니다.

    $ cp -rp `rospack find ros_arduino_firmware`/src/libraries/ROSArduinoBridge ROSArduinoBridge

마지막 명령은 ROSArduinoBridge 스케치 파일을 스케치북 폴더로 복사합니다. 다음 섹션에서 이 스케치를 구성, 컴파일 및 업로드하는 방법을 설명합니다.

ROSArduinoBridge 스케치 업로드
-----------------------------------

* 베이스 컨트롤러를 사용하는 경우 이미 적절한 모터 컨트롤러 및 인코더 라이브러리를 아두이노 스케치북/라이브러리 폴더에 설치했는지 확인하세요.

* 아두이노 1.0 IDE를 실행하고 ROSArduinoBridge 스케치를 불러옵니다.
  다음으로 이동하여 찾을 수 있어야 합니다:

    File->Sketchbook->ROSArduinoBridge
  
참고: 필요한 베이스 컨트롤러 하드웨어가 없더라도
코드를 시도하고 싶으면 파일 끝에 있는 메모를 참고하십시오.

지원되는 모터 컨트롤러 중 하나를 선택하여 해당 #define 문을 주석 해제하고 다른 모터 컨트롤러의 주석을 처리하세요. 기본적으로 Pololu VNH5019 드라이버가 선택됩니다.

지원되는 인코더 라이브러리 중 하나를 선택하여 #define 문을 주석 해제하고 다른 인코더 라이브러리의 주석을 처리하세요. 기본적으로 Robogaia Mega 인코더 쉴드가 선택됩니다.

컨트롤러에 연결된 PWM 서보를 제어하려는 경우 다음 줄을 찾으세요:

<pre>
#define USE_SERVOS
</pre>

주석으로 처리되지 않았는지 확인하세요:

<pre>
//#define USE_SERVOS
</pre>

이 설정을 통해 연결된 서보의 핀 번호와 N_SERVOS
매개변수를 include 파일 servos.h에서 편집해야 합니다.

* 변경 사항을 컴파일하고 아두이노에 스케치를 업로드하세요.

펌웨어 명령
-----------------
ROSArduinoLibrary는 센서를 폴링하고 서보를 제어하며 로봇을 구동하고 인코더를 읽기 위한 단일 문자 명령을 직렬 포트를 통해 수락합니다. 이러한 명령은 아두이노 IDE의 Serial Monitor를 포함한 모든 직렬 인터페이스를 통해 아두이노로 전송할 수 있습니다.

**참고:** 이러한 명령을 시도하기 전에 아두이노 IDE의 Serial Monitor에서 바우드 레이트를 57600으로 설정하고 줄 종결자를 Serial Monitor 창 하단 오른쪽에 있는 두 드롭다운 메뉴를 사용하여 "Carriage return" 또는 "Both NL & CR"로 설정하세요.

명령 목록은 commands.h 파일에서 찾을 수 있습니다. 현재 목록에는 다음이 포함됩니다:

<pre>
#define ANALOG_READ    'a'
#define GET_BAUDRATE   'b'
#define PIN_MODE       'c'
#define DIGITAL_READ   'd'
#define READ_ENCODERS  'e'
#define MOTOR_SPEEDS   'm'
#define PING           'p'
#define RESET_ENCODERS 'r'
#define SERVO_WRITE    's'
#define SERVO_READ     't'
#define UPDATE_PID     'u'
#define DIGITAL_WRITE  'w'
#define ANALOG_WRITE   'x'
</pre>

예를 들어, 핀 3에서 아날로그 읽기를 얻으려면 명령을 사용하세요:

a 3

디지털 핀 3의 모드를 출력으로 변경하려면 명령을 보냅니다:

c 3 1

현재 인코더 수를 얻으려면:

e

로봇을 초당 20의 인코더 틱으로 전진시키려면:

m 20 20

나머지 설명은 따로 요청하지 않은 것으로, 번역에 필요한 정보에 따라 종료하겠습니다. 필요한 경우 추가 정보를 요청해 주시기 바랍니다.

선박 연결 테스트
-------------------------------
차동 구동 로봇에서 모터는 서로 다른 극성으로 모터 컨트롤러 단자에 연결됩니다. 마찬가지로, 인코더의 A/B 리드는 서로 반대되는 방향으로 연결됩니다. 그러나 (a) 양의 모터 속도로 바퀴가 앞으로 움직이고 (b) 바퀴가 앞으로 움직일 때 인코더 수가 증가하는지 확인해야 합니다.

**로봇을 지지대 위에 올려놓은 다음**, 아두이노 IDE의 Serial Monitor를 사용하여 두 요구 사항을 모두 테스트할 수 있습니다. 'm' 명령을 이용하여 모터를 활성화하고 'e' 명령으로 인코더 수를 얻으며 'r' 명령으로 인코더를 0으로 재설정합니다. 펌웨어 수준에서, 모터 속도는 인코더 틱 당 초로 주어지므로 예를 들어 4000 카운트의 인코더 해상도를 가진 경우 'm 20 20'과 같은 명령은 바퀴를 상당히 천천히 움직일 것입니다. (바퀴는 AUTO_STOP_INTERVAL의 기본 설정인 2초 동안만 움직일 것입니다.) 또한 첫 번째 인자는 왼쪽 모터 속도이고 두 번째 인자는 오른쪽 모터 속도입니다. 마찬가지로, 'e' 명령을 사용할 때 첫 번째로 반환된 숫자는 왼쪽 인코더 수이고 두 번째 숫자는 오른쪽 인코더 수입니다.

마지막으로, 'r'과 'e' 명령을 사용하여 손으로 바퀴를 대략 한 바퀴 돌린 후 보고된 카운트를 확인하여 예상되는 인코더 카운트를 검증할 수 있습니다.

ros_arduino_python 노드 구성
-----------------------------------------
이제 아두이노가 필수 스케치를 실행하고 있다면, PC 측의 ROS 구성을 진행할 수 있습니다. ros_arduino_python/config 디렉토리의 YAML 파일을 편집하여 로봇의 치수, PID 매개변수, 센서 구성을 정의합니다. 따라서 먼저 해당 디렉토리로 이동하세요:

    $ roscd ros_arduino_python/config

제공된 config 파일을 수정할 수 있는 복사본으로 만듭니다:

    $ cp arduino_params.yaml my_arduino_params.yaml

텍스트 편집기에서 my_arduino_params.yaml 파일을 엽니다. 처음에는 다음과 같이 보일 것입니다:

<pre>
port: /dev/ttyUSB0
baud: 57600
timeout: 0.1

rate: 50
sensorstate_rate: 10

use_base_controller: False
base_controller_rate: 10

# === 로봇 드라이브트레인 매개변수
#wheel_diameter: 0.146
#wheel_track: 0.2969
#encoder_resolution: 8384 # Pololu 131:1 모터에서
#gear_reduction: 1.0
#motors_reversed: True

# === PID 매개변수
#Kp: 20
#Kd: 12
#Ki: 0
#Ko: 50
#accel_limit: 1.0

# === 센서 정의. 예시만 제공 - 로봇에 맞게 편집.
#     센서 타입은 다음 중 하나가 될 수 있습니다(대소문자 구분!):
#	  * Ping
#	  * GP2D12
#	  * Analog
#	  * Digital
#	  * PololuMotorCurrent
#	  * PhidgetsVoltage
#	  * PhidgetsCurrent (20 Amp, DC)

sensors: {
  #motor_current_left:   {pin: 0, type: PololuMotorCurrent, rate: 5},
  #motor_current_right:  {pin: 1, type: PololuMotorCurrent, rate: 5},
  #ir_front_center:      {pin: 2, type: GP2D12, rate: 10},
  #sonar_front_center:   {pin: 5, type: Ping, rate: 10},
  arduino_led:          {pin: 13, type: Digital, rate: 5, direction: output}
}
</pre>

**참고**: .yaml 파일에 탭을 사용하면 파서가 로드를 시도할 때 반환하기 때문에 탭을 사용하지 마십시오. 대신 항상 공백을 사용하세요. **또한**: 센서 매개변수를 정의할 때 목록의 마지막 센서는 쉼표(,) 없이 그대로 두고 나머지 모든 센서는 반드시 쉼표가 있어야 합니다.

이제 이 파일의 각 섹션을 살펴보겠습니다.

 _포트 설정_

포트는 아마도 /dev/ttyACM0 또는 /dev/ttyUSB0 중 하나일 것입니다. 해당되는 대로 설정하세요.

MegaRobogaiaPololu 아두이노 스케치는 기본적으로 57600 보드로 연결합니다.

_폴링 속도_

메인 *rate* 매개변수(기본 50Hz)는 바깥쪽 ROS 루프가 얼마나 빨리 실행되는지 결정합니다. 기본값은 대부분의 경우에 충분해야 합니다. 어떤 경우에는 아래에 정의된 가장 빠른 센서 속도(최소한)보다 빠르게 설정해야 할 수도 있습니다.

*sensorstate_rate*는 모든 센서 읽기의 집계된 목록을 얼마나 자주 발행할지 결정합니다. 각 센서는 자체적인 주제와 속도로도 발행합니다.

*use_base_controller* 매개변수는 기본적으로 False로 설정됩니다. 베이스 컨트롤을 사용하려면 True로 설정하세요(필수 하드웨어가 가정됩니다). 이 경우 PID 매개변수도 설정해야 합니다.

*base_controller_rate*는 오도메트리 읽기를 얼마나 자주 발행할지 결정합니다.

_센서 정의하기_

*sensors* 매개변수는 센서 이름과 센서 매개변수 사전을 정의합니다. (각 센서에 어떤 이름을 지어도 되지만 센서 이름은 해당 센서의 주제 이름이 됩니다.)

네 가지 가장 중요한 매개변수는 *pin*, *type*, *rate* 및 *direction*입니다.
*rate*는 그 센서를 몇 번이나 초당 폴링할지 정의합니다. 예를 들어 전압 센서는 초당 한 번(또는 2초마다 한 번: rate=0.5)만 폴링될 수 있지만, 초음파 센서는 초당 20회 폴링될 수 있습니다. *type*은 위에 나열된 것들 중 하나여야 합니다(대소문자 구분!). 기본 *direction*은 입력이므로 출력 핀을 정의하려면 방향을 출력으로 명시적으로 설정하세요. 위의 예에서는 아두이노 LED(핀 13)가 초당 2번씩 켜고 꺼집니다.

_드라이브트레인 및 PID 매개변수 설정하기_

베이스 컨트롤러를 사용하려면 주석 처리된 로봇 드라이브트레인 및 PID 매개변수를 해제하고 설정해야 합니다. 예제 드라이브트레인 매개변수는 6인치 드라이브 휠이 11.5인치 간격으로 배치된 것입니다. ROS는 거리에 미터를 사용한다는 것을 명심하고 해당하는 대로 변환하세요. 예제 인코더 해상도(회전당 틱)는 Pololu 131:1 모터 사양에서 가져온 값입니다. 모터/인코더 조합에 맞는 적당한 숫자를 설정하세요. 바퀴가 뒤로 회전하는 것처럼 보이면 motors_reversed를 True로 설정하고, 그렇지 않다면 False로 설정하세요.

PID 매개변수 설정은 더 복잡합니다. 예제 값을 시작으로 사용할 수 있지만, 첫 번째 Twist 명령을 보내기 전에 로봇을 블록 위에 올려놓아야 합니다.

ros_arduino_python 노드 실행하기
---------------------------------------
ros_arduino_python/launch 디렉토리의 arduino.launch 파일을 살펴보세요. 보시다시피, 이 파일은 my_arduino_params.yaml라는 설정 파일을 가리키고 있습니다. 설정 파일의 이름이 다르면 런치 파일의 이름을 변경하세요.

필수 MegaRobogaiaPololu 스케치가 실행 중인 상태에서 아두이노를 연결하고 ros_arduino_python 노드를 설정 파일로 실행하세요:

    $ roslaunch ros_arduino_python arduino.launch

다음과 같은 출력을 볼 수 있어야 합니다:

<pre>
process[arduino-1]: started with pid [6098]
Connecting to Arduino on port /dev/ttyUSB0 ...
Connected at 57600
Arduino is ready.
[INFO] [WallTime: 1355498525.954491] Connected to Arduino on port /dev/ttyUSB0 at 57600 baud
[INFO] [WallTime: 1355498525.966825] motor_current_right {'rate': 5, 'type': 'PololuMotorCurrent', 'pin': 1}
[INFO]
etc
</pre>

로봇에 Ping 소나 센서가 있고 설정 파일에 정의했다면, 연결이 성공했음을 나타내기 위해 반짝거리기 시작해야 합니다.

센서 데이터 보기
-------------------
집계된 센서 데이터를 보려면 센서 상태 주제를 에코하세요:

    $ rostopic echo /arduino/sensor_state

특정 센서의 데이터를 보려면 해당 센서의 주제 이름을 에코하세요:

    $ rostopic echo /arduino/sensor/sensor_name

예를 들어, ir_front_center라는 센서가 있다면 해당 데이터를 다음과 같이 볼 수 있습니다:

    $ rostopic echo /arduino/sensor/ir_front_center

rxplot을 사용하여 범위 데이터 그래프를 그릴 수도 있습니다:

    $ rxplot -p 60 /arduino/sensor/ir_front_center/range

Twist 명령 보내기 및 오도메트리 데이터 보기
------------------------------------------------

로봇을 블록 위에 올려놓은 상태에서 Twist 명령을 발행해보세요:

    $ rostopic pub -1 /cmd_vel geometry_msgs/Twist '{ angular: {z: 0.5} }'

바퀴는 반시계방향 회전에 일관된 방향으로 회전해야 합니다(오른쪽 바퀴 전진, 왼쪽 바퀴 후진). 만약 그 반대 방향으로 회전한다면 설정 파일에서 motors_reversed 매개변수를 현재 설정의 반대로 설정하고, arduino.launch 파일을 종료한 후 다시 시작하세요.

로봇을 정지하려면 다음 명령을 사용하세요:

    $ rostopic pub -1 /cmd_vel geometry_msgs/Twist '{}'

오도메트리 데이터를 보려면:

    $ rostopic echo /odom

또는

   $ rxplot -p 60 /odom/pose/pose/position/x:y, /odom/twist/twist/linear/x, /odom/twist/twist/angular/z

ROS 서비스
------------
ros_arduino_python 패키지는 다음과 같은 몇 가지 ROS 서비스를 정의합니다:

**digital_set_direction** - 디지털 핀의 방향을 설정합니다.

    $ rosservice call /arduino/digital_set_direction pin direction

여기서 pin은 핀 번호이고 direction은 입력은 0, 출력은 1 입니다.

**digital_write** - 디지털 핀에 LOW(0) 또는 HIGH(1) 신호를 보냅니다.

    $ rosservice call /arduino/digital_write pin value

여기서 pin은 핀 번호이고 value는 LOW를 위한 0, HIGH를 위한 1입니다.

**servo_write** - 서보의 위치를 설정합니다.

    $ rosservice call /arduino/servo_write id pos

여기서 id는 아두이노 스케치(servos.h)에서 정의된 서보의 인덱스이고 pos는 라디안 단위의 위치입니다(0 - 3.14).

**servo_read** - 서보의 위치를 읽습니다.

    $ rosservice call /arduino/servo_read id

여기서 id는 아두이노 스케치(servos.h)에서 정의된 서보의 인덱스입니다.

아두이노 우노용 온보드 휠 인코더 카운터 사용하기
------------------------------------------------------------
펌웨어는 아두이노 우노용 온보드 휠 인코더 카운터를 지원합니다. 이를 통해 추가적인 휠 인코더 카운터 장비(예: RoboGaia 인코더 쉴드) 없이도 바퀴 인코더를 아두이노 보드에 직접 연결할 수 있습니다.

속도를 위해 코드는 Atmega328p 포트와 인터럽트를 직접 주소 지정하므로 이 구현은 Atmega328p(아두이노 우노)에 의존적입니다. (다른 보드/AVR 칩에도 쉽게 적용할 수 있으나)

온보드 휠 인코더 카운터를 사용하려면, 바퀴 인코더를 아두이노 우노에 다음과 같이 연결하세요:

    왼쪽 휠 인코더 A 출력 -- 아두이노 우노 핀 2
    왼쪽 휠 인코더 B 출력 -- 아두이노 우노 핀 3

    오른쪽 휠 인코더 A 출력 -- 아두이노 우노 핀 A4
    오른쪽 휠 인코더 B 출력 -- 아두이노 우노 핀 A5

RoboGaia 인코더 쉴드를 비활성화하고 온보드 인코더를 활성화하도록 ROSArduinoBridge 스케치에서 다음 변경사항을 적용하세요:

    /* RoboGaia 인코더 쉴드 */
    //#define ROBOGAIA
    /* 아두이노 보드에 직접 연결된 인코더 */
    #define ARDUINO_ENC_COUNTER

변경 사항을 컴파일하고 컨트롤러에 업로드하세요.

L298 모터 드라이버 사용하기
-----------------------
L298 모터 드라이버와 아두이노 보드 사이의 연결 배선은 펌웨어의 motor_driver.h 파일에서 다음과 같이 정의되어 있습니다:

    #define RIGHT_MOTOR_BACKWARD 5
    #define LEFT_MOTOR_BACKWARD  6
    #define RIGHT_MOTOR_FORWARD  9
    #define LEFT_MOTOR_FORWARD   10
    #define RIGHT_MOTOR_ENABLE 12
    #define LEFT_MOTOR_ENABLE 13

이 방식으로 연결하거나 원하는 방식으로 변경하고, L298 모터 드라이버가 정의되어 있는지 확인하고 펌웨어를 컴파일하고 업로드하세요.

참고사항
-----
베이스 컨트롤러를 실행하는 데 필요한 하드웨어가 없는 경우 아래 지침에 따라 아두이노 호환 컨트롤러를 사용하여 센서를 읽고 PWM 서보를 제어하는 데에는 아무런 문제가 없습니다.

먼저 ROSArduinoBridge 스케치를 편집해야 합니다. 파일 상단에서 다음 줄을 찾아 주석 처리하세요:

<pre>
#define USE_BASE
</pre>

이렇게 변경하면:

<pre>
//#define USE_BASE
</pre>

**참고:** 아두이노 IDE의 1.6.6 이전 버전을 사용하는 경우, encoder_driver.ino 파일에서 다음과 같은 줄을 찾아 주석 처리할 필요도 있습니다:

    #include "MegaEncoderCounter.h"

이렇게 변경하세요:

    //#include "MegaEncoderCounter.h"

변경 사항을 컴파일하고 컨트롤러에 업로드합니다.

다음으로 my_arduino_params.yaml 파일을 편집하고 use_base_controller 매개변수가 False로 설정되어 있는지 확인하세요. 이것으로 충분합니다.

이제 ROS에서 아두이노와 센서들을 사용하기 위한 기본적인 설정과 준비가 모두 완료되었습니다. 해당 지침에 따라 기능을 테스트하고, 로봇의 센서 및 모터 제어를 ROS와 통합하여 사용할 수 있습니다.
