# pending_renewal_info
[참고문서](https://qiita.com/natty420/items/530de949455fdb41caf4)

| Name | Description | Etc |
|---|---|---|
| auto_renew_product_id | 구독 예정인 상품ID | 다음 자동갱신에 구독할 상품ID |
| product_id | 현재 구독중인 상품ID | 현재 구독중인 상품ID |
| original_transaction_id | Apple 계정의 고유한 구매ID | latest_receipt 항목의 것과 동일함 |
| auto_renew_status | 정기구독상품의 자동갱신 상태 | 1: 자동갱신<br>0: 사용자가 구독을 취소함 |
| expiration_intent	| 기한만료 사유	| 1: 사용자가 구독을 취소함<br>2: 사용자의 결제정보가 유효하지 않음<br>3: 사용자가 해당 상품의 변경된 가격을 동의하지 않음<br>4: 갱신 시점에서 해당 상품이 미판매 상태였음<br>5: 원인 불명의 오류 |
| is_in_billing_retry_period | 기한만료된 구독에 대해서 Apple이 자동갱신을 시도하는지 여부 | 1: Apple에서 자동갱신을 시도하고 있음<br>0: Apple에서 자동갱신을 시도하지 않음 |

## auto_renew_product_id와 product_id의 차이
product_id는 latest_receipt_info 항목의 마지막 영수증의 상품 ID이며 현재 구독중이거나 구독만료된 상태일 수 있다.  
auto_renew_product_id는 현재 Apple 계정이 결제 중이거나 결졔 예정인 상품의 ID를 나타낸다.  
업그레이드/다운그레이드나, 크로스 그레이드를 사용하는 경우에는 아래의 예제에 따라 product_id와 auto_renew_product_id의 값이 결정된다.

한 어플리케이션에 product_a 상품과 product_b 상품이 존재한다고 가정할 때, 같은 Group의 같은 Level로 설정되어있고, product_a 상품을 구독 중일 때
product_b 상품을 구독하려고 하는 행위를 크로스 그레이드라고 한다.

유저가 product_a를 구매하면 pending_renewal_info는 아래와 같다.
```
"pending_renewal_info":[
	{
		"auto_renew_product_id":"product_a",
		"original_transaction_id":"XXXXXXXXXXXXXXXX",
		"product_id":"product_a",
		"auto_renew_status":"1"
	}
]
```

product_a의 유효기간 내에 유저가 product_b로 구독을 변경하게 되면 유효기간이 남아있어서 latest_receipt_info 의 정보는 변경되지 않는다.
```
"pending_renewal_info":[
	{
		"auto_renew_product_id":"product_b",
		"original_transaction_id":"XXXXXXXXXXXXXXXX",
		"product_id":"product_a",
		"auto_renew_status":"1"
	}
]
```

product_a의 유효기간이 지나서 product_b로 구독갱신이 되었다면, 이 시점에서는 latest_receipt_info 정보가 갱신되어 아래와 같이 pending_renewal_info 가 변경된다.
```
"pending_renewal_info":[
	{
		"auto_renew_product_id":"product_b",
		"original_transaction_id":"XXXXXXXXXXXXXXXX",
		"product_id":"product_b",
		"auto_renew_status":"1"
	}
]
```