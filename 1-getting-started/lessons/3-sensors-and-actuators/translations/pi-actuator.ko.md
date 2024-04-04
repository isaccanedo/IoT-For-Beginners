# 야간 조명 만들기 - Raspberry Pi

라즈베리 파이에 LED를 추가하여 야간 조명을 만들어봅시다.

## 하드웨어

이 야간 조명에는 액추에이터가 필요합니다.

사용되는 액추에이터는 **LED**로, 전류가 흐를 때 빛을 방출하는 [발광 다이오드](https://wikipedia.org/wiki/Light-emitting_diode) 입니다. LED는 on /off 두 가지 상태를 가진 디지털 액추에이터입니다. 1을 값으로 전송하면 LED가 켜지고 0을 전송하면 꺼집니다. LED는 외부 Grove 액추에이터이며 라즈베리 파이의 Grove Base Hat에 연결해야 합니다

의사 코드에서 야간 조명의 로직은 다음과 같습니다.:

```output
탐지되는 빛의 정도(level)를 확인합니다
빛의 정도가 300 미만일 경우
    LED를 켭니다
그렇지 않을 경우
    LED를 끕니다
```

### LED 연결하기

Grove LED는 다양한 LED가 포함된 모듈로 제공되므로 색상을 선택할 수 있습니다.

#### 할 일 - LED 연결

LED에 연결 해 봅시다.

![grove LED](../../../../images/grove-led.png)

1. 좋아하는 LED를 선택하고 LED 모듈의 두 구멍에 다리를 삽입합니다.

    LED는 발광 다이오드이며, 다이오드는 전류를 한 방향으로만 전달할 수 있는 전자 장치입니다. 즉, LED를 올바른 방향으로 연결해야 합니다. 그렇지 않으면 작동하지 않습니다.

    LED 다리 중 하나는 양극 핀이고 다른 하나는 음극 핀입니다. LED는 완벽하게 둥글지 않고 한쪽이 약간 평평합니다. 약간 평평한 면이 음극 핀입니다. LED를 모듈에 연결할 때 둥근 쪽의 핀이 모듈 외부에 +로 표시된 소켓에 연결되고 평평한 쪽이 모듈 중앙에 더 가까운 소켓에 연결되었는지 확인하십시오.

1. LED 모듈에는 밝기를 제어할 수 있는 회전 버튼이 있습니다. 작은 십자 드라이버를 사용하여 시계 반대 방향으로 끝까지 돌립니다.

1. Grove 케이블의 한쪽 끝을 LED 모듈의 소켓에 삽입합니다. 이는 한 방향으로만 돌아갈 것 입니다.

1. Rasberry Pi 전원을 끈 상태에서 Grove 케이블의 다른 쪽 끝을 Pi에 부착된 Grove Base 모자에 D5로 표시된 디지털 소켓에 연결합니다. 이 소켓은 왼쪽에서 두 번째, GPIO 핀 옆에 있는 소켓 줄입니다.

![소켓 D5에 연결된 Grove LED](../../../../images/pi-led.png)

## 야간 조명을 프로그래밍 해봅시다

Grove 조명 센서와 Grove LED를 사용하여 야간 조명 프로그래밍을 진행할 수 있습니다.

### 할 일 - 야간 조명 동작에 대한 프로그래밍을 해 봅시다.

야간 조명에 대한 프로그래밍을 합니다.

1. 라즈베리 파이 전원을 켜고 부팅될 때 까지 기다립니다.

1. 이 할당의 이전 부분에서 만든 VS Code에서 야간 조명 프로젝트를 Pi에서 직접 실행하거나 원격 SSH 확장을 사용하여 연결합니다.

1. 필요한 라이브러리를 가져오려면 아래 코드를 `app.py` 파일에 추가하십시오. 이는 다른`import` 줄 바로 아래 추가되어야 합니다.


    ```python
    from grove.grove_led import GroveLed
    ```

    `from grove.grove_led import GroveLed` 문은 Grove Python 라이브러리에서  `GroveLed` 를 import 한다. 이 라이브러리에는 Grove LED와 상호 작용하는 코드가 있습니다.
    
1. `light_sensor` 선언 뒤에 다음 코드를 추가하여 LED를 관리하는 클래스의 인스턴스를 만듭니다.

    ```python
    led = GroveLed(5)
    ```

    `led = GroveLed(5)` 코드는 핀 **D5**(LED가 연결된 디지털 Grove 핀)에 연결하는 `GroveLed` 클래스의 인스턴스를 생성합니다. 

    > 💁 모든 소켓에 고유한 핀 번호가 있습니다. 핀 0, 2, 4, 6은 아날로그 핀이고 핀 5, 16, 18, 22, 24 및 26은 디지털 핀입니다.
    
1. `while` 루프 내부에 확인용 변수를 추가합니다,  `time.sleep` 전에 조명 레벨을 확인하고 LED를 켜거나 끌 수 있습니다.

    ```python
    if light < 300:
        led.on()
    else:
        led.off()
    ```

    이 코드는 감지되는 `빛` 의값을 확인합니다. 이 값이 300 미만인 경우 `GroveLed` 클래스의 `on` 메서드를 호출하여 LED에 디지털 값 1을 전송하고 LED를 켭니다. 조명 값이 300보다 크거나 같으면 꺼짐 방법을 호출하여 디지털 값 0을 LED로 전송하고 LED를 끕니다.
    
    > 💁 이 코드는 `print('Light level:', light)` 라인과 동일한 레벨로 들여써야 하며, while 루프 안에 있어야 합니다!

    > 💁 액추에이터로 디지털 값을 전송할 때 0 값은 0V, 1 값은 장치의 최대 전압입니다. Grove 센서 및 액추에이터가 장착된 라즈베리 파이의 경우 1 전압은 3.3V입니다.
    
1. VS Code Terminal에서 다음을 실행하여 Python 앱을 실행합니다.:

    ```sh
    python3 app.py
    ```

    조명 값이 콘솔에 출력됩니다.

    ```output
    pi@raspberrypi:~/nightlight $ python3 app.py 
    Light level: 634
    Light level: 634
    Light level: 634
    Light level: 230
    Light level: 104
    Light level: 290
    ```

1. 조명 센서를 가려도 봅시다. 조명 레벨이 300 이하인 경우 LED가 어떻게 켜지고, 조명 레벨이 300 이상인 경우 LED가 꺼지는지 확인합니다.

    > 💁 LED가 동작하지 않는다면 제대로 연결 되어있는지 확인하고 스핀 버튼이 완전히 켜져 있는지 확인 해 보세요
    
![Light Level 변화에 따라 Pi에 연결된 LED가 켜지고 꺼집니다.](../../../../images/pi-running-assignment-1-1.gif)

> 💁  [code-actuator/pi](code-actuator/pi) 폴더 안에서 이 코드를 찾을 수 있습니다..

😀 여러분이  만든 야간 조명 프로젝트는 성공적으로 동작합니다!
