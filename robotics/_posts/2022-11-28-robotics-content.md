# nvidia_graphic_card 인식 해결

## ubuntu 22.04 설치 후 nvidia_drive(graphic card) 설치

* 먼저 ubuntu22.04를 설치하고 실행하였는데 그래픽 카드 인식 오류가 발생한다면 PC 뒤에서 Display Port (or HDMI) 를 그래픽카드가 아닌 마더보드에 연결할 수 있도록 하고 재부팅한다. 그리고 terminal 창을 열어서 그래픽 카드에 적합한 드라이버를 검색한다.

`sudo ubuntu-drivers devices`

![Untitled1](https://raw.githubusercontent.com/kyu8456/kyu8456.github.io/main/robotics/images/Untitled1.png)

드라이버 중 추천해주는 모델을 설치를 진행한다.

`sudo apt install nvidia-driver-{number}`

nvidia 드라이버 모델 설치가 완료된 것을 확인한다.

`nvidia-smi`

![Untitled2](https://raw.githubusercontent.com/kyu8456/kyu8456.github.io/main/robotics/images/Untitled2.png)

정상적으로 진행이 되었다면 nvidia 드라이버가 설치가 되었으니 그래픽카드로 부터 HDMI를 연결해 디스플레이를 확인할 수 있다.
