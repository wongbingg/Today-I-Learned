
###### tags: `TIL`

# KeyChain 로직분석
## 최초에 key-value 를 등록 
```swift
struct Credentials {
    let userName: String
    let password: String
}
func addNew(userName: String, password: String) {
    // 키체인을 활용해 패스워드를 저장합니다.
    let credentials = Credentials(userName: userName, password: password)
    let account = credentials.userName
    let password = credentials.password.data(using: String.Encoding.utf8)!
    let query: [CFString: Any] = [kSecClass: kSecClassGenericPassword,
                            kSecAttrAccount: account,
                              kSecValueData: password]
    let status = SecItemAdd(query as CFDictionary, nil)
    if status == errSecSuccess {
        print("저장이 되었다")
    } else if status == errSecDuplicateItem {
        print("이미 등록된 비밀번호가 있다")
    } else {
        print("add failed")
    }
}
```
> qeury 상수를 CFDictionary타입으로 캐스팅해 SecItemAdd() 메서드에 대입 -> OSStatus 반환
> status 라는 OSStatus 타입의 상수가 작업의 현재상태를 나타낸다 (성공, 중복, 실패)
## SecItemAdd(_:, _:)
[SecItemAdd문서](https://developer.apple.com/documentation/security/1401659-secitemadd)
<details>
    
Add one or more items to a keychain.
하나이상의 항목을 키체인에 추가한다.
```swift!
func SecItemAdd(_ attributes: CFDictionary, _ result: UnsafeMutablePointer<CFTypeRef?>?) -> OSStatus
```
### Parameters
- attributes
    A dictionary that describes the item to add. A typical attributes dictionary consists of:
    - The item's class.
        - `kSecClass` 키를 사용한다
    - The Data
        - `kSecValueData` 키를 사용한다.
    - Optional attributes
        -  See [Item Attribute Keys and Values](https://developer.apple.com/documentation/security/1401659-secitemadd) for the complete list.
        - 링크에 들어가보면 kSecAttrAccount 가 나온다.
    - Optional return types
        - 
- result
    On return, a reference to the newly added items. The exact type of the result is based on the values supplied in attributes, as discussed in Item Return Result Keys. Pass nil if you don't need the result. Otherwise, your app becomes responsible for releasing the referenced object. 결과가 필요 없으면 nil 처리
### Return Value
A result code. See Security Framework Result Codes.
### Discussion
한번에 여러개의 항목을 키체인에 추가하는 방법에 대한 설명
</details>
    
## keyChain에 key값에 해당 value값을 수정
```swift
func updateItemOnKeyChain(value: Data, key: String) {
    let previousQuery: [CFString: Any] = [kSecClass: kSecClassGenericPassword,
                                    kSecAttrAccount: key]
    let updateQuery: [CFString: Any] = [kSecValueData: value]
    let status = SecItemUpdate(previousQuery as CFDictionary, 
                               updateQuery as CFDictionary)
    if status == errSecSuccess {
        print("update complete")
    } else {
        print("not finished update")
    }
}
```
## SecItemUpdate(_:, _:)
[SecItemUpdate문서](https://developer.apple.com/documentation/security/1393617-secitemupdate)
<details>
    
Modifies items that match a search query
찾는 쿼리에 맞는 아이템 수정

### Declaration

```swift!
func SecItemUpdate(_ query: CFDictionary, _ attirbutesToUpdate: CFDictionary) -> OSStatus
```
### Parameters
- query
    A dictionary that describes the search for the keychain items you want to update.
- attributesToUpdate
    A dictionary containing the attributes whose values should be changed, along with the new value. Only real keychain attributes are permitted in this dictionary(no "meta" attributes are allowed.) See Item Attribute Keys and Values
### Return Value
A result code. See Security Framework Result Codes.
### Discussion
See the SecItemCopyMatching(_:,_:) function for information about how to construct a search dictionary
search dictionary 를 구성하는 방법에 대한 정보를 알려면 SecItemCopyMatching() 메서드를 봐라
    
</details>
    
## keyChain에 등록된 key값을 이용해 value 값을 반환
```swift
func readItemsOnKeyChain(account: String) -> String? {
        let query: [CFString: Any] = [kSecClass: kSecClassGenericPassword,
                                kSecAttrAccount: account,
                           kSecReturnAttributes: true,
                                 kSecReturnData: true]
        var item: CFTypeRef?
        if SecItemCopyMatching(query as CFDictionary, &item) != errSecSuccess {
            print("read failed")
            return nil
        }
        guard let existingItem = item as? [String: Any] else { return nil }
        guard let data = existingItem[kSecValueData as String] as? Data else { return nil }
        guard let password = String(data: data, encoding: .utf8) else { return nil }
        
        return password
    }
```
## SecItemCopyMatching(_:, _:)   
[SecItemCopyMatching문서](https://developer.apple.com/documentation/security/1398306-secitemcopymatching)
<details>
    
Return one or more keychain items that match a search query, or copies attributes of specific keychain items.
### Declaration
```swift!
func SecItemCopyMatching(_ query: CFDictionary, _ result: UnsafeMutablePointer<CFTypeRef?>?) -> OSStatus
```
### Parameters
- query
    A dictionary that describes the search. A typical query dictionary consist of:
    - The item's class. [Item Class Keys and Values](https://developer.apple.com/documentation/security/1398306-secitemcopymatching) 에 들어가보면 kSecClassGenericPassword 가 있다. 
    - Attributes
         [Item Attribute Keys and Values](https://developer.apple.com/documentation/security/1398306-secitemcopymatching) 에 들어가보면 kSecAttrAccount 가 있다.
    - Search parameters
    - One or more return types.
- result
### Return Value
### Discussion
    
</details>
    
## keyChain에 등록된 key값을 이용해 값 삭제
```swift
func deleteItemOnKeyChain(account: String) {
    let deleteQuery: [CFString: Any] = [kSecClass: kSecClassGenericPassword,
                                  kSecAttrAccount: account]
    let status = SecItemDelete(deleteQuery as CFDictionary)
    if status == errSecSuccess {
        print("remove key-data complete")
    } else {
        print("remove key-data failed")
    }
}
```

## SecItemDelete(_:)
Deletes items that match a search query
search query에 맞는 항목을 제거
### Declaration
```swift!
func SecItemDelete(_ query: CFDictionary) -> OSStatus
```
    
### Parameters
- query
    A dictionary that describes the search for the keychain items you want to delete.
### Return Value
### Discussion
    
