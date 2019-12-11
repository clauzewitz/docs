# Apple 영수증 검증시 영수증 유형

## Document
[https://developer.apple.com/jp/documentation/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html](https://developer.apple.com/jp/documentation/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)

## in_app 필드
구매한 모든 종류의 상품에 대한 구매내역이 저장된다. 앱이 해당 거래를 종료할 때까지 보관되며, 사용자가 다른 상품을 구매하거나, 앱에서 강제로 새로고침을 하여 새로이 발급되는 영수증부터는 삭제된다.  
영수증 검증 시, 영수증이 발급된 시점의 구매내역까지의 정보만이 조회된다.  
in_app 필드가 비어있는 경우도 유효한 영수증이며, 비 소모품, 자동갱신구독 상품, 비갱신구독 상품, 무료구독 상품 구매 내역은 영원히 저장된다.  
[참고문서](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ReceiptFields.html#//apple_ref/doc/uid/TP40010573-CH106-SW1)

## latest_receipt_info 필드
자동갱신구독 상품에 대한 구매내역이 저장된다. iOS 7 형식의 영수증은 모든 자동갱신구독상품에 대한 구매내역이 저장된다.  
영수증 검증 시, 영수증이 발급된 이후의 자동갱신구독 상품의 구매내역도 조회된다.
> Only returned for receipts containing auto-renewable subscriptions. For iOS 6 style transaction receipts, this is the JSON representation of the receipt for the most recent renewal. For iOS 7 style app receipts, the value of this key is an array containing all in-app purchase transactions. This excludes transactions for a consumable product that have been marked as finished by your app.

[참고문서](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html#//apple_ref/doc/uid/TP40010573-CH104-SW1)

## pending_renewal_info 필드
> Only returned for iOS 7 style app receipts containing auto-renewable subscriptions. In the JSON file, the value of this key is an array where each element contains the pending renewal information for each auto-renewable subscription identified by the [Product Identifier](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ReceiptFields.html#//apple_ref/doc/uid/TP40010573-CH106-SW11). A pending renewal may refer to a renewal that is scheduled in the future or a renewal that failed in the past for some reason.