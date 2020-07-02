# Apple Refund Notification

## Document
[https://developer.apple.com/documentation/storekit/in-app_purchase/handling_refund_notifications](https://developer.apple.com/documentation/storekit/in-app_purchase/handling_refund_notifications)

## WWDC 2020 발표자료
WWDC 2020 에서 공개된 인앱결제의 환불이 발생되면 설정된 Endpoint URL 로 관련 정보를 송신하는 기능이다.  
[참고영상](https://developer.apple.com/videos/play/wwdc2020/10661/)

## notification data
Refund Notification 을 통해 전달받은 데이터는 아래와 같다.
```
{
   "notification_type":"REFUND",
   "environment": "PROD",
   "password": APPSTORE_SHARED_SECRET_KEY,
   "latest_receipt": LAST_RECEIPT,
   "latest_receipt_info": {
      "cancellation_date_ms": "1593105601000",
      "transaction_id": "31000061234123",
      "original_transaction_id": "31000061234123",
      "quantity": "1",
      "unique_identifier": "c5294879b234w7d8997c3cb6267ab548sadfzx3",
      "is_in_intro_offer_period": "false",
      "purchase_date_pst": "2020-06-19 09:23:10 America/Los_Angeles",
      "original_purchase_date_ms": "1592583790000",
      "item_id": "1175273222",
      "app_item_id": "1175273333",
      "cancellation_date_pst": "2020-06-25 10:20:01 America/Los_Angeles",
      "is_trial_period": "false",
      "original_purchase_date_pst": "2020-06-19 09:23:10 America/Los_Angeles",
      "cancellation_reason": "0",
      "cancellation_date": "2020-06-25 17:20:01 Etc/GMT",
      "original_purchase_date": "2020-06-19 16:23:10 Etc/GMT",
      "purchase_date_ms": "1592583790000",
      "product_id": PRODUCT_ID,
      "version_external_identifier": "834846438905",
      "bvrs": "100",
      "purchase_date": "2020-06-19 16:23:10 Etc/GMT",
      "bid": BUNDLE_ID,
      "unique_vendor_identifier": "TEST12-T9T2-42A3-9D85-8392808088"
   }
}
```
* notification_type: 전달받은 notification 의 종류
	+ REFUND
* environment: notification 이 발생환 환경. Sandbox 환경과 Production 환경으로 나뉜다.
	+ Sandbox or PROD
* password: App 등록 시 설정된 shared secret 의 값. 영수증 검증 시에 사용된다.
* last_receipt: notification 이 발생한 영수증
* last_receipt_info: last_receipt 의 상세 내용. last_receipt 과 password 의 값을 이용하여 직접 영수증 검증을 하면 전달받은 데이터와 같은 것을 확인할 수 있다.
	+ [last_receipt_info 의 객체 정보](https://developer.apple.com/documentation/appstoreservernotifications/responsebody/latest_receipt_info)
		- 아직 canellation_reason 의 내용은 갱신되지 않음
		- cancellation_reason 의 내용은 [다른 문서](https://developer.apple.com/documentation/appstorereceipts/responsebody/latest_receipt_info)에서 확인할 수 있다.
			* 0: 고객이 실수로 구매한 경우 혹은 기타 이유로 취소
			* 1: 고객이 인지할 수 있는 문제(앱 내 잘못된 설명 혹은 설명과 다른 상품)으로 취소
	+ last_receipt 과 password 를 이용해 영수증을 검증한 내용
	```
	{
		"receipt": {
			"original_purchase_date_pst": "2020-06-19 09:23:10 America/Los_Angeles",
			"cancellation_date_ms": "1593105601000",
			"quantity": "1",
			"cancellation_reason": "0",
			"unique_vendor_identifier": "TEST12-T9T2-42A3-9D85-8392808088",
			"bvrs": "100",
			"is_in_intro_offer_period": "false",
			"purchase_date_ms": "1592583790000",
			"is_trial_period": "false",
			"item_id": "1175273222",
			"unique_identifier": "c5294879b234w7d8997c3cb6267ab548sadfzx3",
			"original_transaction_id": "31000061234123",
			"original_purchase_date_ms": "1592583790000",
			"app_item_id": "1175273333",
			"transaction_id": "31000061234123",
			"version_external_identifier": "834846438905",
			"cancellation_date": "2020-06-25 17:20:01 Etc/GMT",
			"product_id": PRODUCT_ID,
			"purchase_date": "2020-06-19 16:23:10 Etc/GMT",
			"original_purchase_date": "2020-06-19 16:23:10 Etc/GMT",
			"cancellation_date_pst": "2020-06-25 10:20:01 America/Los_Angeles",
			"bid": BUNDLE_ID,
			"purchase_date_pst": "2020-06-19 09:23:10 America/Los_Angeles"
			},
		"status": 0
	}
	```