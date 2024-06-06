# Data Communication
## term project 2 - RDT protocol


## STUDENT INFO
- 이름 : Yoo da yeon
- 학번 : 202110789

----------------

## Project INFO
- Implement a Reliable Data Transfer over my output in the *Term Project #1.


> #### What is Term Project #1?
> Sender / Receiver program that communicate via **UDP sockets**.

- The file should be split into chunks of data of fixed length.
- Th data of each chunk would be transmitted via a UDP socket.
- The RDT protocol must hand the followings : Packet loss, Duplicate packets.
- The RDT protocol is unidirectional. (text file : sender -> receiver / ACK : receiver -> sender)
- In this project, transmission follows a stop-and-wait model.
- Packet loss : sender and receiver should be able to discard packets received, based on a user-defined probability.
- Packet header - type(DATA, ACK, EOT), seqNum, ackNum, length(0 ~ 1000, ACK/EOT : 0)
- Define a class named "Packet".
- The packet class needs to be serializable.
- For a timer to retransmit lost packets, use alarm system call.
- Make logs for events of packet transmission/reception, packet discard, timeout and retransmission. For each event log, also print time and relevant packet information such as seqNum/ackNum.

--------------------

## 개발 환경
프로젝트 1과 동일하게, **Ubuntu Linux** 개발 환경에서 **C언어**로 작성하였다.

--------------------

## 컴파일 과정
이 프로젝트 (RDT protocol) 를 위해 sender.c, receiver.c를 작성해주었고, 패킷 구조체와 직렬화, 역직렬화를 위해 packet.h 도 작성해주었다.

**프로그램을 실행하기 위해 sender.c,  receiver.c,  packet.h,  파일이 필요하다. 추가적으로, 전송할 파일(test.txt)도 필요하다.**
 과제 제출 시 sender.c, receiver.c, packet.h 파일 모두 제출할 것이고, 전송할 파일은 아무 것이나 상관 없으나, 테스트에 사용한 파일도 추가적으로 제출할 것이다.

### **1.  Ubuntu linux 개발 환경에 sender.c, receiver.c, packet.h, 전송할 텍스트 파일을 준비해준다.** 

### **2.  그 다음으로 gcc 명령어를 이용해 sender.c 와 receiver.c 컴파일을 해준다.**

        gcc sender.c -o sender
        gcc receiver.c -o receiver

![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/95d8fb7f-f193-4235-ae44-e1b71ff8611f)

### **3. 위 과정을 성공적으로 수행하면 아래와 같이 sender 와 receiver 라는 실행 파일이 생성된다.**
파일들을 확인하기 위해서는 "ls -al"을 명령으로 입력하면 된다.

![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/fe4fa569-a6ab-40b7-9d69-5d431e240026)

### **4. 실행에 앞서, 프로그램에 필요한 인수들은 아래와 같다.**

> ### **sender 프로그램에 필요한 인수**
>  1. sender의 port
>  2. receiver의 ip 주소
>  3. receiver의 port
>  4. time interval (**5초 미만으로 잡을 것**)
>  5. 전송할 파일 이름
>  6. sender가 받은 ACK 를 drop할 확률


> ### **receiver 프로그램에 필요한 인수**
>  1. receiver의 port
>  2. receiver가 받은 Data packet을 drop할 확률

### **5. 인수들과 함께 명령을 입력해 프로그램을 실행해준다. 먼저 receiver 프로그램을 실행시키고, sender을 실행시켜준다.**
예시로, 포트 번호는 12345, IP 주소는 127.0.0.1, drop 확률은 0.3, time_interval은 3초를 사용해주었다.
예시를 사용하여 아래와 같이 순서대로 입력해준다.

    ./receiver 12345 0.3

![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/082c3b23-20ad-4abe-950c-04d601e3e158)



    ./sender 12345 127.0.0.1 12345 3 test.txt 0.3

![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/72628357-36fb-4ad4-84c6-6577b98ff09a)

- drop이 조금 나는 상황을 보려면 확률을 낮춰주고, drop이 많이 발생하는 상황을 보려면 확률을 높여주면 된다. (대략 0.1 ~ 0.9 정도)
- 아예 일어나지 않게 하려면 drop probability 에 0.0을 넣어준다.
- ack drop 이 발생하는 상황을 보려면 sender의 drop probability를 조금 더 높여주고, data packet drop 이 발생하는 상황을 보려면 receiver의 drop probability를 조금 더 높여주면 된다.

### **6. 두 프로그램이 실행되면 프로그램들 사이에서 send와 receive가 일어나면서 화면 출력과 log 파일이 생성된다.**
화면 출력으로 송수신 과정을 볼 수 있고 log file을 통해 조금 더 자세히 확인해볼 수 있다.

- log file 및 전송받은 데이터 파일이 잘 생성되었는지 확인하는 방법

  **1) find 명령어**
  ![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/e525f564-58ff-4237-9fb1-a313689b3d8b)
  
      find *_log.txt


  **2) ls 명령어**

  ![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/12d843eb-6a19-4c2a-86e2-1cb32fb2c82c)

      ls -al
  


- sender의 log file을 확인하는 방법
  
      vi sender_log.txt

- receiver의 log file을 확인하는 방법

      vi receiver_log.txt

- 전송받은 파일을 확인하는 방법

      vi received_file

-------------------------

## 프로그램 설계 과정 

이번 프로젝트를 프로그램으로 작성하기 위해 거친 설계 과정을 설명하겠다.

- 일단, 기본적인 구조는 Sender가 <pkt (seqNum = #)> 을 보내면 Receiver가 이를 확인하고 오류가 없을 때 <ack (ackNum = #)>을 보내는 것이다.
- Sender은 데이터 패킷을 보냄과 동시에 time interval초의 타이머를 설정한다.
- 만약, 타이머가 종료될 때까지 ACK가 오지 않으면 (data packet drop or ack drop) 재전송을 해준다.

- 패킷을 송수신하는 과정은 이전에 수행했던 project 1을 기반으로 한다. 달라지는 것은 단순한 문자열이 아닌 파일 데이터 전송인 점. (구조체 사용)
- 이것은 패킷 구조체를 사용해 직렬화 및 역직렬화를 이용해 송수신하는 것으로 구현한다. (memcpy 함수 이용)
  
- 여기서 이제 패킷을 어떻게 drop 시킬 것인지, 재전송은 어떻게 해줄 것인지, 프로그램은 어떻게 종료시킬 것인지 생각해야 한다.

#### 1) 패킷을 어떻게 drop 시킬 것인가? ####
  - drop 함수를 만든다. rand() 함수와 인수로 받는 (사용자가 입력한 drop_probability) 확률을 이용해 1 또는 0을 반환한다.
  - 데이터 패킷이나 ACK를 받았을 때, 이 함수가 반환하는 값에 의해 drop 시켰을 때 / drop 시키지 않았을 때의 상황을 나누어 처리한다.
 
#### 2) 재전송은 어떻게 해줄 것인가? ####
  - signalrm_handler를 사용한다.
  - 타이머를 설정하여 타임 아웃 발생 시 재전송을 해준다.
 
#### 3) 프로그램은 어떻게 종료시킬 것인가? ####
  - S_FIN과 R_FIN, signalrm_handler를 사용한다.
  - S_FIN은 Sender 측에서 보내는 FIN 패킷으로, 파일 전송이 끝마친 후, Recever에게 "파일 전송이 끝났다. 프로그램을 종료해도 된다"라는 의미로 보내는 것이다.
  - R_FIN은 Receiver 측에서 보내는 FIN 패킷으로, Sender로부터 S_FIN을 받은 후 "알겠다. 프로그램을 종료하겠다."라는 의미로 보내는 것이다.
  - 여기서 Receiver에 타이머를 설정해줄 것인데, 이유는 ack가 드롭될 상황을 생각하여 일정 시간이 지난 후 Receiver을 종료하도록 하기 위함이다.
  - Receiver은 R_FIN을 보낸 후 일정 시간(5초) 후 프로그램을 종료한다.
  - Sender은 R_FIN을 받으면 즉시 프로그램을 종료한다.
  - 만약, S_FIN이 drop 되었다면 Sender 측에서 타임 아웃 발생 후 재전송해줄 것이다.

이를 토대로 정리해보자면

- Sender에게 필요한 기능
  1) ack drop
  2) 데이터 패킷 재전송
  3) 로그 기록 및 화면 출력
  4) 패킷 전송
  5) rdt에 기반한 패킷 송수신

- Receiver에게 필요한 기능
  1) data packet drop
  2) ack 전송
  3) 로그 기록 및 화면 출력
  4) 일정 시간 후 프로그램 종료
  5) rdt에 기반한 패킷 송수신
     
-------------------------

## 프로그램 코드 설명

### packet.h ###
패킷 헤더 형식을 위해 작성해준 파일이다. 헤더에 있어야하는 필드들은 패킷 구조체로 작성해주었다. DATA는 0, ACK는 1, EOT는 2로 정의해 사용해주었다.

이 파일에서는 패킷 구조체와 직렬화 함수, 역직렬화 함수가 작성되어있다. 구조체는 다음과 같은 필드를 가진다.
- int type : DATA, ACK or EOT 를 나타내는 값
- int seqNum : 0부터 시작하는 sequence number. 데이터 패킷을 보낼 때 사용할 것이다.
- int ackNum : 확인 번호. ACK 패킷을 보낼 때 사용할 것이다.
- size_t length : 데이터 필드의 바이트 수. 0에서 1000 사이의 범위를 가지며, ACK나 EOT일 경우 길이는 0이다. 

두 함수는 memcpy 함수를 이용해 작성해주었다. 구조체를 통째로 메모리에 복사해주는 식으로 사용해주었다. 

[참고한 사이트 01 : 데이터 직렬화에 대해](https://surgach.tistory.com/31)

[참고한 사이트 02 : memcpy](https://blockdmask.tistory.com/442)


| 함수 | 설명 |
|-----|-----|
| void serialize (Packet *packet, char *buffer) | packet 구조체를 버퍼에 직렬화 해준다 |
| void deserialize(char *buffer, Packet *packet) | 버퍼에 직렬화 했던 것을 다시 구조체로 역직렬화 해준다 |


### Sender.c ###

Sender은 아래와 같은 네 가지 함수와 main 함수를 가진다.

| 함수 | 설명 |
|-----|-----|
| int drop_received_ack(float probability) | Receiver로부터 받은 ACK를 drop 시킬지 말지 정해주는 함수 |
| void log_and_print(char *event) | 로그 작성 및 화면 출력 |
| void signalrm_handler(int signal) | 타이머가 만료됐을 때 호출될 함수. 데이터 패킷 또는 FIN을 재전송을 해준다 |
| Packet send_packet (FILE *file, int seqNum) | 데이터 패킷 또는 FIN을 전송해준다 |

main 함수에서는 본격적으로 소켓을 생성하고 rdt 프로토콜에 기반한 송수신이 이루어진다.


### Receiver.c ###

Receiver은 아래와 같은 네 가지 함수와 main 함수를 가진다.

| 함수 | 설명 |
|-----|-----|
| int drop_received_data(float probability) | Sender로부터 받은 데이터 패킷을 drop 시킬지 말지 정해주는 함수 |
| void signalrm_handler (int signal) | 타이머가 만료됐을 때 호출될 함수. 마지막 종료 ACK를 전송했을 때 일정 시간 동안 재전송이 없다면 프로그램을 종료시켜준다 |
| void log_and_print(char *event) | 로그 작성 및 화면 출력 |
| void send_ack (int ackNum) | ACK 패킷을 전송해준다 |

main 함수에서는 본격적으로 소켓을 생성하고 rdt 프로토콜에 기반한 송수신이 이루어진다.


------------------------- 


## 프로그램 출력 후 화면 출력 설명

위에서 설명한 것과 같이 컴파일하여 실행해보았다. 전송할 파일은 따로 준비해 test.txt라는 이름으로 정해주었다. 이 test.txt 파일 안에는 마틴 루터킹의 연설문이 들어있다. (적당한 길이의 텍스트 파일을 준비함.)

### 1) drop 확률을 낮게 잡고 (두 프로그램 모두 0.1) 송수신이 제대로 되는지 확인 ###

- Sender의 출력 모습
![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/3e23eb29-7a9e-4f3c-b045-3876be8186a7)

- Receiver의 출력 모습
![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/8f916c2d-c6db-4649-9f14-dcfd4cf69b4a)

- 중간에 Receiver 측에서 Data packet drop 이 발생했다. 이것은 데이터 패킷이 제대로 가지 못한 상황을 나타낸 것이므로, Sender에서 타임아웃이 일어나 재전송이 
이루어졌고 이후 Receiver이 드롭 없이 잘 받아 송수신이 계속 잘 이어지는 모습을 볼 수 있다. drop이 일어났을 때 Receiver의 log의 모습이다. 확인해보면 3초 후 패킷을 다시 받은 것을 확인할 수 있다.
이는 sender에서 3초 후 재전송 했음을 보여준다.
![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/672b09ba-15ba-48da-8be6-1b2f138e6077)

- 또한 마지막에 Sender가 보낸 S_FIN (= 파일 전송이 다 끝났다. 종료해도 된다.) 을 보낸 것을 data packet drop으로 인해 Receiver가 받지 못했고 
타임 아웃이 발생해 S_FIN을 재전송하는 모습을 볼 수 있다.
- 사진 상에선 확인하기 어렵지만 Receiver가 R_FIN을 보내고 일정 시간 (5초로 지정) 후에 종료되는 모습이 나타난다. Receiver의 로그 파일을 통해
이를 확인할 수 있다.
![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/b6c7800a-e86b-4fdf-86b7-ae10cb75180a)


### 2) ACK drop 을 보기 위해 Sender의 drop 확률을 0.3으로 올려보았다. ###
확률을 조금 높였더니 출력이 길어서 drop이 일어난 부분만 캡쳐해 가져와보았다. 
- Sender의 출력 모습
  ![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/3d3f2728-a6dd-4b06-8000-59bfb718e6e0)

- Receiver의 출력 모습
  ![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/0efa0568-38ab-4aff-8ab0-c4cebabd67d8)

- 프로그램이 시작하고 0번 패킷 전송은 잘 이루어졌는데, 1번 패킷 과정을 보내는 과정에서 ack drop 을 볼 수 있다.
- Sender가 데이터 패킷 (1)을 전송했고 Receiver가 이를 확인후 ACK (1)을 보냈는데 이 ACK가 제대로 보내지지 않은 상황이다.
- 그래서 Sender은 ACK를 받지 못해 타임 아웃이 발생했고, Receiver 측이 데이터 패킷을 잘 못 받았다는 판단을 내려 패킷을 재전송하였다.
- 그러나 Receiver은 전에 데이터 패킷 (1)을 받았고, 이에 대한 ACK (1) 까지 보냈기에, 데이터 패킷 (1)을 중복적으로 받은 것이 된다.
- 이를 확인해 화면에 '다시 받았다' 라는 의미로 Receive again이라고 표시해주고 ACK를 다시 보내주었다.
- 로그를 확인해보면 아래와 같다.
- Sedner의 로그
  ![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/03428455-37b5-4d0e-ada3-ff5928c96c0e)
- Receiver의 로그
 ![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/9db1ce53-c64a-47d4-b7c2-c65098e775b8)


### 3) 기타 ###
- 확률적으로 packet loss가 일어나기 때문에 모든 상황을 보고서에 담기엔 어려움이 있어 중요한 기능을 보여주는 부분만 test 해보았다.
- 1, 2번 상황 외에도 loss가 반복적으로 일어나 재전송이 반복적으로 일어나는 상황이나, 마지막으로 Receiver가 보낸 R_FIN이 ACK loss 되는 상황 등 다양한 상황을 확인해볼 수 있다.
- (예시) 재전송이 반복적으로 일어났을 때 sender의 출력 모습. packet loss가 sender와 receiver에서 반복적으로 일어난 모습. 
  ![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/61f64ce0-5869-401c-884f-626bd3935b16)
- ack가 drop 되는 상황을 보려면 Sender의 drop probability를 조금 더 크게 잡아주고, data packet이 drop 되는 상황을 보려면 Receiver의 drop probability를 조금 더 크게 잡아주면 된다.
- 확률을 0.1로 잡아도 drop이 일어나긴 하지만 더 많은 packet loss 상황을 보려면 확률을 조금씩 높여주면 된다.
- 전송이 완료되고 received_file을 open 해보면 파일을 잘 전송 받았는지 확인해볼 수 있다. (ls 명령어, vi 명령어 사용)
- received_file (전송받은 파일)이 잘 생성된 모습
  ![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/1c05f0a1-5f6a-4592-b6c0-894f8b5e3cfd)
- received_file 열어봤을 때 (일부 가져옴)
  ![image](https://github.com/daaoooy/data_communication_project2/assets/143688136/799edcdd-4abc-47a5-9fc7-bb498b234251)


------------------


