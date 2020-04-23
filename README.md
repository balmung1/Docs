# Event Storming
![Admin22_이벤트스토밍결과](https://user-images.githubusercontent.com/63028480/80064597-6296ba80-8573-11ea-8f0d-e8cf42a5fd05.PNG)

# Cloud CI/CD 구현 화면
![Admin22_CICD](https://user-images.githubusercontent.com/63028480/80064750-c91bd880-8573-11ea-8799-c4f87fdb0a16.PNG)

# 예제 - MovieReviews
프로젝트: MovieReviews 
개요       : 신규영화 정보에 사용자 리뷰를 등록

# 서비스 시나리오
기능적 요구사항          
시나리오1:  'Movie' 서비스에 영화 등록 => MovieCreate 이벤트발생 =>  'Review'서비스에 영화정보 생성 =>  MovieInfoCreate 이벤트발생 => 'Notice'서비스에 알림요청 => NoticeSent 알림발송

시나리오2: 'Review'서비스에 영화리뷰 작성 => ReviewWrited 이벤트발생 => 'Notice'서비스에 알림요청 => NoticeSent 알림발송

# 시나리오1
시나리오1 흐름도     
![시나리오1_흐름](https://user-images.githubusercontent.com/63028480/80065575-4eec5380-8575-11ea-8595-a2575b336246.PNG)

1) MovieCreate    
![시나리오1_스타트](https://user-images.githubusercontent.com/63028480/80065135-99210500-8574-11ea-8365-516fffd08382.PNG)

2) MovieCreated / MovieInfoCreated
![시나리오1_Movie이벤트와Review이벤트발생](https://user-images.githubusercontent.com/63028480/80065145-9c1bf580-8574-11ea-8c68-572a9a3fd0db.PNG)

3) Notice 알림
![시나리오1_Notice알림](https://user-images.githubusercontent.com/63028480/80065150-9de5b900-8574-11ea-9dd7-0a44670db657.PNG)
