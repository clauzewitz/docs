# Facebook & Google's get user info

## Facebook
* 승인이 필요하지 않은 권한
	+ email: 이메일 주소에 대한 읽기 권한
	+ public_profile: 이름 및 프로필 사진에 대한 읽기 권한
* 승인이 필요한 권한
	+ user_link: 프로필 URL에 대한 읽기/쓰기 권한
	+ user_gender: 성별에 대한 읽기/쓰기 권한
	+ user_friends: 친구 리스트에 대한 읽기 권한
	+ user_age_range: 연령대에 대한 읽기/쓰기 권한
	+ user_videos: 업로드한 동영상에 대한 읽기/쓰기 권한
	+ user_tagged_places: 게시물 등에 태그된 장소에 대한 읽기 권한
	+ user_posts: 게시물 읽기/삭제 권한
		- 경우에 따라 user_friends 권한 필요
		- 페이지의 게시물을 삭제 시 publish_pages 권한 필요
		- 페이지 내의 다른 사용자의 게시물을 삭제 시 user_managed_groups 권한 필요
		- 업데이트는 페이스북 SDK 의 공유 기능을 사용
	+ user_photos: 업로드한 사진에 대한 읽기 권한
	+ user_location: 프로필에 설정한 현재 거주지에 대한 읽기/쓰기 권한
	+ user_likes: 좋아요를 한 페이지 리스트에 대한 읽기 권한
	+ user_hometown: 프로필에 설정한 출신지에 대한 읽기/쓰기 권한
	+ user_events: 사용자가 주최자 이거나 참석한 이벤트에 대한 읽기 권한
	+ user_birthday: 생일에 대한 읽기/쓰기 권한
	+ publish_pages: 사용자가 관리하는 페이지에 대한 읽기/쓰기 권한
	+ pages_manage_instant_a: 사용자가 관리하는 페이지 내에 인스턴트 아티클 읽기/쓰기 권한
		- 모바일에서만 동작
	+ publish_video: 라이브 방송을 게시할 권한

## Google
[참고문서](https://developers.google.com/identity/protocols/googlescopes#oauth2v2)
* https://www.googleapis.com/auth/userinfo.email: 이메일 주소에 대한 읽기 권한
* https://www.googleapis.com/auth/userinfo.profile: 이름 언어, 프로필에 대한 읽기 권한