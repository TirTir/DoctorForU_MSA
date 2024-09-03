# DoctorForU_MSA

### 프로젝트 소개

---

최근 의사 파업과 같은 비상 상황에서는 환자들이 필요한 의료 서비스와 실시간 응급 정보를 적시에 받기 어려워지고 있습니다. 또한, 진료내역, 운동기록, 병원 예약 등 의료 서비스가 여러 플랫폼에서 분산되어 운영되고 있으며, 이는 의료 서비스의 연속성을 크게 저하시킵니다. 

이를 해결하기 위해 마이크로서비스 아키텍처(MSA)를 도입한 DoctorForU 프로젝트를 기획하게 되었습니다. MSA 구조는 각 의료 서비스를 독립적으로 운영할 수 있도록 설계되어 있어, 시스템의 유연성과 확장성을 극대화합니다. 이를 통해 여러 의료 서비스가 분산되어 있어도, 각 서비스가 독립적으로 운영되고 필요에 따라 신속하게 확장될 수 있습니다.


<p align="center">
  <br/>
  <img src="https://github.com/user-attachments/assets/de8966f9-6dd6-4279-88d6-c65b84ba8c60" width="600" height="200">
</p>



### 시스템 구성도

---

<p align="center", justify="center">
  <img src="https://github.com/user-attachments/assets/bef86212-e59d-4c36-bad7-719995ca5c50" width="500" height="500">
  <img src="https://github.com/user-attachments/assets/74174e45-37e1-49a0-b934-a9f7245c5a60" width="500" height="200">
  </br>
</p>

- 사용자의 모든 요청은 **Spring Cloud Gateway**를 통해 처리되며, 이는 트래픽 관리와 로드 밸런싱, 인증 및 권한 부여를 담당
  
- **Config Server**를 통해 각 서비스별 설정을 DoctorForU Git에서 중앙 관리
  
- Reservation Service나 Emergency Service와 같은 핵심 서비스에 **Circuit Breaker**를 적용하여 시스템의 전반적인 안정성 보장

<p>
  <br/>
</p>


### 기술 스택

---

<p>
  <img src="https://github.com/user-attachments/assets/acf42320-c53d-4000-a54c-4b6763da91e1" width="400" height="150">
  <br/>
</p>

### 주요 기능

---

* 병원 찾기 및 실시간 병상 정보 확인

    공공데이터 API를 이용한 실시간 병원 데이터를 확인하고 사용자에게 정보를 제공합니다


* 응급실 현황

    응급환자를 수용할 수 있는 실시간 응급실 데이터를 제공합니다

  
* 진료 예약 기능

    예약이 가능한 시간대와 요일을 조회하여 사용자에게 제공하고 예약된 유저들을 관리 및 알림기능을 제공합니다.


* 진료 정보 열람

    평소에 확인하기 어려운 진료내역을 빠르고 간단하게 사용자에게 제공합니다

  
* 건강 기록 및 헬스 체크

    간편하게 건강상태를 기록하고 이를 요일별로 모니터링할 수 있게 제공합니다.


<p>
  <br/>
</p>

### 구현 기능

---

**[ 개인화 서비스 ]**

<p>
  <img src="https://github.com/user-attachments/assets/e3a044c6-8811-4c54-bcfe-2499363a3da4" width="500" height="300">
  <br/>
</p>


- **User Service**
    
    로그인, 회원가입 등의 기능을 담당합니다. **redis를 캐시**로 사용하여 사용자의 **토큰 정보와 같은 데이터를 빠르게 접근**할 수 있도록 하였으며, **TTL 설정으로 토큰 유효시간을 자동으로 관리**합니다. 또한, **SMTP 서버와 연동하여 회원가입 시 추가적인 인증 절차를 구현**했습니다. 
    
- **MyPage 서비스**
    
    개인 정보 관리 및 건강 기록, 진단 내역을 받아오는 서비스 제공

- **Reservation 서비스**

  예약 가능 시간대 조회 및 제공, 간편한 건강상태 기록과 요일별 모니터링 제공
    
<p>
  <br/>
</p>


**[ Hospital 서비스 ]**

<p>
  <img src="https://github.com/user-attachments/assets/d05c485e-0f39-472d-b0f0-d0d71129a398" width="500" height="300">
  <br/>
</p>


사용자의 요청에 따라 병원 정보 및 길찾기, 실시간 응급실 현황, 특정 병원의 응급 메세지 등을 사용자에게 제공합니다. 

<p>
  <br/>
</p>

**[ 가상 API 서버 제작 ]**

<p>
  <img src="https://github.com/user-attachments/assets/6efbba3b-04aa-47bd-b7c9-687ad244acda" width="500" height="250">
</p>


**제작 배경 :** 

저희는 실 진료 내역을 받아올 수 있는 마이데이터 사업자나 특정 권한이 없다는 문제점을 마주하였고, 이를 해결하기 위해 **인증 토큰 발급, 관리** 및 **개인의 진료기록을 전달하는 역할의 가상 API 서버를 직접 제작**하게 되었습니다.

서버는 '**easyResponse**'라고 이름지었으며 **등록된 기관에게만 진료내역을 제공**하도록 구성하였습니다. **easyResponse** 서버는 **JWT를 통해 인증 및 인가를 처리**합니다. 각 기관은 이름과 비밀번호를 이용하여 서버에 인증 요청을 하며, easyResponse 서버는 한 달 사용 가능한 토큰을 제공합니다. **DoctorForU는 주민번호와 유효한 토근을 사용하여 사용자의 진료내역을 요청**합니다.

<p>
  <br/>
</p>


**[ 모니터링 ]**


<p>
  <img src="https://github.com/user-attachments/assets/dfb346f5-7d17-45d1-92d4-a192626efbe1" width="500" height="250">
  <img src="https://github.com/user-attachments/assets/9147929b-b5d8-4ffb-bd46-586ed80cc741" width="500" height="250">
</p>

**자원 관리 측면**
-  Prometheus로 실시간 자원 사용량 모니터링
-  Grafana 대시보드를 통해 CPU, 메모리, 네트워크 등 자원 상태 시각화
-  자원 사용량 분석 및 최적화를 통한 효율적인 시스템 운영

**운영 업무 측면**
-  MSA의 각 서비스 엔드포인트 상태 추적
-  서비스 간 트래픽 및 요청/응답 시간 모니터링
