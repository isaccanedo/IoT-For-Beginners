# 온도 게시 - 가상 IoT 하드웨어와 라즈베리 파이

이 단원에서는 나중에 GDD를 계산하는 데 사용될 수 있도록 MQTT를 이용해 라즈베리 파이나 가상 IoT 장치에서 감지한 온도 값을 게시합니다.

## 온도 게시

온도를 읽은 후에는 MQTT를 통해 값을 읽고 GDD 계산에 사용할 수 있도록 저장하는 '서버' 코드에 게시할 수 있습니다.

### 임무 - 온도 게시하기

온도를 게시하기 위해 장치를 프로그래밍하십시오.

1. `temperature-sensor` 앱 프로젝트가 열려있지 않다면 해당 프로젝트를 엽니다.

1. 레슨 4에서 했던 MQTT를 연결하고 원격 분석을 전송하는 다음 단계들을 반복합니다. 동일한 공개 Mosquitto 브로커를 사용하게 됩니다.

   이를 위한 단계는 다음과 같습니다:

   - MQTT pip 패키지를 추가합니다.
   - MQTT 브로커와 연결하는 코드를 추가합니다.
   - 원격 분석을 게시하는 코드르 추가합니다.

   > ⚠️ 필요하다면 레슨 4의 [MQTT 연결을 위한 지침](../../../../1-getting-started/lessons/4-connect-internet/translations/single-board-computer-mqtt.md)과 [원격 분석 전송 지침](../../../../1-getting-started/lessons/4-connect-internet/translations/single-board-computer-telemetry.ko.md)를 참고하십시오.

1. `client_name`이 이 프로젝트의 이름을 반영 하는지 확인하십시오.

   ```python
   client_name = id + 'temperature_sensor_client'
   ```

1. 원격 분석 전송의 경우 빛의 값을 전송하는 대신 DHT 센서에서 읽은 온도 값을 전송하기 위해 JSON 문서의 속성 `temperature`에서 다음과 같이 보냅니다.

   ```python
   _, temp = sensor.read()
   telemetry = json.dumps({'temperature' : temp})
   ```

1. 온도 값은 자주 읽어올 필요가 없습니다. 짧은 시간에 크게 변하지 않기 때문에 `time.sleep`을 10분으로 설정합니다.

   ```cpp
   time.sleep(10 * 60);
   ```

   > 💁 `sleep`함수는 몇 초간 시간이 가지는 함수입니다. 따라서 계산식의 결과로 전달되는 값을 읽어 오는 것을 더 쉽게 만들어줍니다. 60초는 1분이므로, 10 x (1분에 60초)는 10분의 지연을 제공합니다.

1. 과제의 이전 부분에서 코드를 실행한 것과 동일한 방식으로 코드를 실행합니다. 가상 IoT 장치를 사용한다면 CounterFit 앱이 실행 중인지, 습도 및 온도 센서가 올바른 핀에 생성되었는지 확인하십시오.

   ```output
   pi@raspberrypi:~/temperature-sensor $ python3 app.py
   MQTT connected!
   Sending telemetry  {"temperature": 25}
   Sending telemetry  {"temperature": 25}
   ```

> 💁 이 코드는 [code-publish-temperature/virtual-device](../code-publish-temperature/virtual-device) 폴더 또는 [code-publish-temperature/pi](../code-publish-temperature/pi) 폴더에서 찾을 수 있습니다.

😀장치에서 원격 분석 전송으로 온도를 성공적으로 게시했습니다.
