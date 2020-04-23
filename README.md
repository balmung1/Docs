# Event Storming
![Admin22_이벤트스토밍결과](https://user-images.githubusercontent.com/63028480/80064597-6296ba80-8573-11ea-8f0d-e8cf42a5fd05.PNG)

# Cloud 구현 화면


# 예제 - MovieReviews
프로젝트: MovieReviews 
개요       : 신규영화 정보에 사용자 리뷰를 등록

# 서비스 시나리오
기능적 요구사항
시나리오1:  'Movie' 서비스에 영화 등록 => MovieCreate 이벤트발생 =>  'Review'서비스에 영화정보 생성 =>  MovieInfoCreate 이벤트발생 => 'Notice'서비스에 알림요청 => NoticeSent 알림발송

시나리오2: 'Review'서비스에 영화리뷰 작성 => ReviewWrited 이벤트발생 => 'Notice'서비스에 알림요청 => NoticeSent 알림발송
