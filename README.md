# Dell xps 15 9560 spec

- intel core i7-7700HQ CPU @ 2.80GHzx8
- 32gb ram
- 1TB ssd
- touch screen

# Dell xps 15 9560 ubuntu 18.04

## Touchpad
- 터치패드로 탭 할때 살짝 느린 현상 발견
- 터치패드 드라이버 충돌나서 그런듯
- synaptics 는 제거해준다.
- sudo apt-get install xserver-xorg-input-libinput
- sudo apt-get remove --purge xserver-xorg-input-synaptics
- sudo reboot 
- [참고](https://github.com/rcasero/doc/wiki/Ubuntu-linux-on-Dell-XPS-15-(9560)#touchpad)


## Touchpad 제스처 툴인 libinput-gesture 설치
- input 그룹에 포함되기 위해 현재 사용중인 userID 를 추가해줌
```
sudo gpasswd -a $USER input
```
- 그리고 로그아웃 혹은 재시작함
- 제스쳐와 의존된 툴을 설치하기 위해 다음 실행
```
sudo apt-get install xdotool wmctrl
sudo apt-get install libinput-tools
```
- 그리고 이 [libinput-gestures](https://github.com/bulletmark/libinput-gestures)를 설치해줌
```
git clone https://github.com/bulletmark/libinput-gestures.git
cd libinput-gestures
sudo make install (or sudo ./libinput-gestures-setup install)
```
- 끝!
- 설정파일을 설정하고 시작시키자.
```
libinput-gestures-setup autostart // 자동시작
libinput-gestures-setup start // 시작
libinput-gestures-setup stop // 정지
libinput-gestures-setup restart // 재시작

/etc/libinput-gestures.conf // 설정파일위치
```

- [참고](https://github.com/bulletmark/libinput-gestures)

### 참고 - 나의 제스쳐 설정 파일 - libinput-gestures.conf
```
gesture swipe left	2 xdotool key alt+Right
gesture swipe right	2 xdotool key alt+Left

gesture swipe left	3 xdotool key ctrl+Page_Down
gesture swipe right	3 xdotool key ctrl+Page_Up

gesture swipe up	3 xdotool key super
gesture swipe down 3 xdotool key super+v

gesture swipe up 4 xdotool key super+a
gesture swipe down 4 xdotool key super+alt+8

gesture pinch in	2 xdotool key super+alt+minus
gesture pinch out	2 xdotool key super+alt+plus

gesture pinch in	3 xdotool key super+alt+minus
gesture pinch out	3 xdotool key super+alt+plus

gesture pinch in	4 xdotool key super+l
gesture pinch out	4 xdotool key super+l
```


# Dell xps 15 9560 ubuntu 17.10 설치

여러 시행착오 후에 작성한 개인적인 기록으로 이 방법으로 일어난 이상현상에 대해서 책임지지 않습니다.





### 우분투 멀티 부팅 설치방법

- Dell xps 15 9560 은 기본 SATA config 가 `raid` 로 되어 있음
- 우분투는 SATA config 를 `AHCI` 방식만 지원하므로 window SATA config 를 `AHCI` 방식으로 바꿔줘야함.

### 전원관리 설정

1. window10 에서 `전원 및 절전 설정 선택` 옵션으로 진입
2. `절전 모드 해제 시 암호 사용` 선택
3. `현재 사용 할 수 없는 설정 변경` 선택
4. `빠른 시작 켜기(권장)` 해제 후 저장
5. reboot 하고 Dell 로고 뜰 때 `BIOS 설정`으로 가기위해 F12 연타
6. `Secure Boot Enable`을 해제 처리
- [참고](http://pinkwink.kr/998)


### SATA 를 raid 에서 ahci 로 바꾸기

1. 윈도우 10에서는 고급시동 옵션이 기본적으로 꺼져 있음.
2. 따라서 고급 부팅모드를 이용하여 접근해야 함.
3. window 10 에 로그인 후 `고급시작옵션`의 `다시시작` 버튼 클릭
4. `문제해결` -> `고급옵션` -> `시작설정` -> `다시시작` 클릭
5. 그러면 윈도우 10 부팅이 시작되고 Dell 마크가 뜰 때 F12 를 연타
6. 그러면 `BIOS 설정` 으로 진입하게 되고 
7. SATA Operation 을 RAID -> AHCI 로 바꿔주고 save 후 exit
8. 숫자 4를 눌러 `안전 모드 사용` 으로 진입
9. 안전 모드에서 `장치관리자` 에 가서 'Intel(R) 100 Series/C230 Chipset Family SATA AHCI Controller` 가 있는지 체크.
10. 설정이 완료되었으므로 reboot
- [참고](https://github.com/rcasero/doc/wiki/Ubuntu-linux-on-Dell-XPS-15-(9560))

### 우분투 설치
1. 대충 파티션 잡고 swap 할당 하고 설치. (swap은 보통 메모리 크기로 할당)
2. 파티션 잡기 힘들면 windows 10의 디스크관리에서 파티션을 미리 분할해둔다.
3. 부트로더는 windows boot loader 가 있는 파티션에 설치해줌.



### 우분투 설치 후 touchpad 안먹히는 현상

1. 우분투 터미널 진입
2. sudo nano /etc/default/grub 
3. GRUB_CMDLINE_LINUX_DEFAULT="quiet splash" ->  GRUB_CMDLINE_LINUX_DEFAULT="i8042.reset quiet splash" 로 변경
4. sudo update-grub
5. sudo shutdown -r now 
6. touchpad 정상 작동(뿐만아니라 전원패널 등도 동작됨)

- [참고](https://askubuntu.com/questions/763763/touchpad-under-16-04-not-working)


### 우분투 전원관리 이상현상(종료가 안되는 현상)

1. 우분투 터미널 진입
2. sudo nano /etc/default/grub 
3. GRUB_CMDLINE_LINUX_DEFAULT="quiet splash" ->  GRUB_CMDLINE_LINUX_DEFAULT="... quiet splash nouveau.modeset=0 " 로 변경
4. sudo update-grub
5. sudo shutdown -r now
6. 전원 종료 정상 작동(뿐만아니라 대기모드도 동작)

- [참고](https://askubuntu.com/questions/875173/nmi-watchdog-bug-soft-lockup-cpu2-stuck-for-23s-plymouthd305)