
Ch6. 센서 데이터 접근, 주소록/앨법 등 접근
==============
---
layout: post
title: Ch6. 센서 데이터 접근, 주소록/앨범등 접근
작성자: 홍민우, 마지막 수정:2017.01.15
tag : ios swift nrcrzl93 git github Xcode Contacts ContactsUI CoreMotion 

---


#### Index

* [1. 가속도 센서](#ch-1)
* [2. 자이로 센서](#ch-2)
* [3. 가속도 센서와 자이로 센서 접근](#ch-3)
* [4. 주소록 접근](#ch-4)
* [5. 앨범 접근](#ch-5)

#### Task
* 센서 데이터 접근법 이해
* 주소록 및 앨범 접근법 이해
* 응용 예제 만들기

#### Resource
* https://developer.apple.com/library/content/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/motion_event_basics/motion_event_basics.html
* https://developer.apple.com/reference/contacts

## 1. 가속도 센서 <a id="ch-1"></a>
 사물이 얼마만큼의 중력 가속도(힘)을 측정하는 센서. 가속도 센서는 뉴턴 제 2법칙(가속도 법칙), 후크의 법칙으로 만들어지고, 즉 3축 센서는 각 축에 걸리는 중력가속도의 크기를 반환한다.
아이폰 단말의 경우 핸드폰의 디스플레이를 아래의 그림과 같이 세웠을 때 
X축은 가로 방향 (오른쪽 + / 왼쪽 -)
Y축은 세로 방향 (위 + / 아래 -)
Z축은 디스플레이를 뚫고 나오는 방향 (디스플레이 앞 + / 디스플레이 뒤 -) 가 된다.
![사진](https://github.com/nrcrzl93/nrcrzl93.github.io/blob/master/images/%EA%B0%80%EC%86%8D%EB%8F%84.PNG?raw=true)

 가속도 센서는 가속도를 측정하며 보통 중력가속도가 측정된다.
#### 중력가속도(9.8m/s² = G)

> 중력가속도는 물체에 가해진 중력(gravitaional force)에 의한 가속도를 의미하고, 일반적으로 g기호 를 사용한다. 좁은 의미에서 지구 표면에서의 중력(힘)에 의한 가속도를 의미하는 용어로도 사용된다.

#### 화면 각도 구하는 공식

>arctan(y/x) = angle (탄젠트의 역함수)

#### 가속도 센서의 활용

![사진](https://github.com/nrcrzl93/IOS-STUDY/blob/master/%EA%B0%80%EC%86%8D%EB%8F%84%20%ED%99%9C%EC%9A%A9.PNG?raw=true)
화면 각도, 게임, 모션 등에 활용된다.

## 2. 자이로 센서 <a id="ch-2"></a>
 자이로 센서는 가속도를 측정하는 가속도 센서와 달리 각속도를 측정한다. 자이로스코프(Gyroscope)가 각속도를 측정하는 기구인데 MEMS(초소형 정밀 기계체계, 멤스) 기술을 적용한 칩 형태의 자이로센서도 각속도를 측정한다. 각속도는 시간당 회전하는 각도를 의미한다. 
#### 자이로 센서의 측정원리

> 수평한 상태(정지 상태)에서 각속도도 0도/sec이다. 물체가 10초 동안 움직이는 동안 50도만큼 기울어졌다면, 10초 동안의 평균 각속도는 5도/sec다. 정지 상태에서 기울어진 각도 50도를 유지하였다면 각속도가 0도/sec가 된다. 이러한 과정을 거치면서 각속도는 0 → 5 → 0으로 바뀌었고, 각도는 0도에서 증가해서 50도가 되었다. 

![사진](https://github.com/nrcrzl93/IOS-STUDY/blob/master/%EC%9E%90%EC%9D%B4%EB%A1%9C.PNG?raw=true)



#### 자이로 센서의 활용

![사진](https://github.com/nrcrzl93/IOS-STUDY/blob/master/WII.jpg?raw=true)

 우리 주변의 예로 닌텐도 Wll가 있다.

## 3. 가속도 센서와 자이로 센서 접근 <a id="ch-3"></a>

 가속도 센서와 자이로 센서 접근 시 CoreMotion 라이브러리를 import 시켜준다.

```swift
import UIKit
//가속도 센서와 자이로 센서를 사용하기 위한 CoreMotion 
import CoreMotion
```
 CMMotionManager 인스턴스 생성
```swift
class ViewController: UIViewController{
	//CMMotionManager 클래스의 인스턴스 생성
	var movementManager = CMMotionManager()
}
```

 이벤트 주기 설정은 movementManager 객체의 gyroUpdateInterval와 accelerometerUpdateInterval의 값을 정해줌으로써 가능하게 된다.

```swift
  override func viewDidLoad()
        //이벤트 주기 설정(Hz단위)
        movementManager.gyroUpdateInterval = 0.2
        movementManager.accelerometerUpdateInterval = 0.2

        //Start Recording Data
        //CMAccelerometerData 객체 각각 방향의 가속도를 잡아낸다.
        //startAccelerometerUpdates(to: OperationQueue.current!) 메서드는 지정된 업데이트 간격에서 코어 모션은 큐에있는 작업으로 실행되는 블록에 가속도계 활동의 최신 샘플을 전달한다.
        movementManager.startAccelerometerUpdates(to: OperationQueue.current!) { (accelerometerData: CMAccelerometerData?, NSError) -> Void in
            
            //outputAccData 메서드 호출 
            self.outputAccData(acceleration: accelerometerData!.acceleration)
            if(NSError != nil) {
                print("\(NSError)")
            }
        }
        //CMGyroData object 각각 방향의 회전률을 잡아낸다
        //startGyroUpdates(to: OperationQueue.current!) 메서드는 지정된 업데이트 간격에서 코어 모션은 대기열에서 작업으로 실행되는 블록에 자이로 스코프 활동의 최신 샘플을 전달한다.
        movementManager.startGyroUpdates(to: OperationQueue.current!, withHandler: { (gyroData: CMGyroData?, NSError) -> Void in
            self.outputRotData(rotation: gyroData!.rotationRate)
            if (NSError != nil){
                print("\(NSError)")
            }
        })
    }
```
#### 측정주기 

| Event Frequency(Hz) | Usage                                    |
| ------------------- | ---------------------------------------- |
| 10-20               | Suitable for determining a device’s current orientation vector. |
| 30-60               | Suitable for games and other apps that use the accelerometer for real-time user input. |
| 70–100              | Suitable for apps that need to detect high-frequency motion. For example, you might use this interval to detect the user hitting the device or shaking it very quickly. |

#### 참고사항

* `With Core Motion, you have to test and debug your app on a device. There is no support in iOS Simulator for accelerometer or gyroscope data.`
* `You don’t need to include the accelerometer key if your app detects only device orientation changes.`


## 4. 주소록 접근<a id="ch-4"></a>

#### 개요

주소록에 접근하기 위해서는 Contracts framework를 사용해야 한다. Contracts framework는 사용자의 연락처 정보에 액세스하는 Swift 및 Objective-C의 API를 제공한다. 대부분의 앱은 연락처 정보를 변경하지 않고 읽으므로 이 프레임 워크는 스레드 안전, 읽기 전용 용도로 최적화되어 있다.

#### Contracts Framework 구성

![사진](https://github.com/nrcrzl93/IOS-STUDY/blob/master/Contacts.PNG?raw=true)

 연락처 클래스는 연락처 속성을 수정하는 데 사용할 수있는 변경 가능한 하위 클래스 인 CNMutableContact가 있다. 또한 전화 번호나 전자 메일 주소와 같이 여러 값을 가질 수 있는 연락처 속성의 경우 CNLabeledValue 개체의 배열이 사용된다. 또한 연락처 프레임 워크는 미리 정의 된 레이블을 제공하며 사용자 정의 레이블을 만들 수 있다.

#### 권한 설정(Info.plist)

![사진](https://github.com/nrcrzl93/IOS-STUDY/blob/master/연락처.png?raw=true)

#### contact 만들기

```swift
import Contacts
 
// 변하기 쉬운 객체 만들기
let contact = CNMutableContact()
 
contact.imageData = NSData() // 프로필 사진은 NSData 형식
 
contact.givenName = "민우"
contact.familyName = "홍"
 
let homeEmail = CNLabeledValue(label:CNLabelHome, value:"nrcrzl93@daum.net")
let workEmail = CNLabeledValue(label:CNLabelWork, value:"blue_ness93@naver.com")
contact.emailAddresses = [homeEmail, workEmail]
 
contact.phoneNumbers = [CNLabeledValue(
    label:CNLabelPhoneNumberiPhone,
    value:CNPhoneNumber(stringValue:"(010) 3825-6168"))]
 
let homeAddress = CNMutablePostalAddress()
homeAddress.street = "금곡로"
homeAddress.city = "수원시"
homeAddress.state = "경기도"
homeAddress.postalCode = "0000"
contact.postalAddresses = [CNLabeledValue(label:CNLabelHome, value:homeAddress)]
 
let birthday = NSDateComponents()
birthday.day = 14
birthday.month = 4
birthday.year = 1993  
contact.birthday = birthday as DateComponents
 
 // 새로 생성된 연락처 추가
let store = CNContactStore()
let saveRequest = CNSaveRequest()
saveRequest.add(contact, toContainerWithIdentifier:nil)
try! store.executeSaveRequest(saveRequest)
```

#### 연락처 가져오기 

 사용자의 연락처 데이터베이스를 나타내는 연락처 저장소 (CNContactStore)를 사용하여 연락처를 가져올 수 있다. 또한 연락처 프레임 워크는 미리 정의 된 조건 자 및 keysToFetch 속성을 포함하여 가져 오기에서 반환 된 연락처를 제한하는 몇 가지 방법을 제공한다.

```swift
//CNContact는 가져 오려는 연락처를 필터링하기 위한 조건자를 제공.
let predicate: NSPredicate = CNContact.predicateForContactsMatchingName("홍민우")

let store = CNContactStore()

let contacts = try store.unifiedContactsMatchingPredicate(CNContact.predicateForContactsMatchingName("홍민우"), keysToFetch:[CNContactGivenNameKey, CNContactFamilyNameKey])
```

#### 연락처 저장하기

```swift
let contact = CNMutableContact()
contact.imageData = NSData() // 프로필 사진은 NSData 형식
contact.givenName = "민우"
contact.familyName = "홍"

// 새로 생성된 연락처 추가
let store = CNContactStore()
let saveRequest = CNSaveRequest()
saveRequest.add(contact, toContainerWithIdentifier:nil)
try! store.executeSaveRequest(saveRequest)
```

#### Contacts UI 사용

 우리가 아이폰을 사용할 때 연락처 목록 보기를 누르면 나오는 일반적인 연락처 목록을 보여줄 때 사용하는 프레임워크다. 쉬운 방법으로 접근 가능하며,  Info.plist에 권한추가는 필요없다. 하지만 Contacts 프레임워크와 거의 같이 사용하기 때문에, 권한은 설정해주는게 좋다.

```swift
//ContactsUI import
import ContactsUI
class ViewController: UIViewCpntrpller, CNContactPickerDelegate {
  //CNContactPickerViewController 클래스의 인스턴스 생성
  let contactPickerViewController = CNContactPickerViewController()
  contactPickerViewController.delegate = self
  present(contactPickerViewController, animated: true, completion: nil)
}
```



## 5. 앨범 접근 <a id="ch-5"></a>

 앨범 접근 기능은 우리가 사용하는 거의 모든 Application에 내재되어 있으며, 그 접근법 또한 간단하다. 앨범 접근은 다음과 같은 순서로 구성된다.

1. 권한 설정
2. UIImagePickerController 인스턴스 생성과 구성
3. UIImagePickerController 델리게이트 구성
4. 디바이스 지원 여부 확인
5. 이미지 저장

#### 확대 축소의 종류(UIViewContentMode)

| 이름              | 설명                                       |
| --------------- | ---------------------------------------- |
| scaleToFil      | 이미지를 이미지 뷰에 딱 맞게 확대하거나 축소한다.             |
| scaleAspectFit  | 이미지의 가로세로 비율을 유지한 채로 이미지를 모두 출력할 수 있게 확대하거나 축소 |
| scaleAspectFill | 이미지의 가로세로 비율을 유지한 채로 이미지 뷰의 여백 없이 출력하게 확대하거나 축소 |

#### 권한 설정(Info.plist)

![사진](https://github.com/nrcrzl93/IOS-STUDY/blob/master/KakaoTalk_20170113_181737600.png?raw=true)

#### UIImagePickerController 인스턴스 생성과 구성

 UIImagePickerController를 사용하기 위해서는 클래스의 인스턴스를 만들어야 한다. 그리고 사진이나 동영상 소스(카메라, 카메라 롤, 라이브러리)를 위해 속성을 설정해야 한다. 또, 애플리케이션에서 사용할 미디어의 형식(사진,동영상 또는 모두)을 정의해야 한다. 사진을 촬영한 후 애플리케이션에 전달되기 전에 이를 편집할 수 있는 옵션도 설정할 수 있다.

UIImagPickerController 객체의 sourceType 속성을 통해 설정할 수 있는 미디어 소스의 형태는 아래와 같다.

* UIImagePickerControllerSourceType.Camera - 카메라
* UIImagePickerControllerSourceType.SavedPhotosAlbum - 카메라 롤
* UIImagePickerControllerSourceType.PhotoLibrary - 라이브러리

```swift
		//UIImagePickerController 인스턴스 생성
        let imagePicker = UIImagePickerController()
        imagePicker.delegate = self
        //imagePicker 객체가 참조하는 타입 - photoLibrary(사진 앨범)
        imagePicker.sourceType = .photoLibrary
        //애플리케이션에서 처리할 미디어의 형태는 mediaTypes 속성을 통해 정의되며, 이 속성은 사진과 동영상의 두가지 형태를 지원
        imagePicker.mediaTypes = [kUTTypeImage as NSString]
        //편집 가능
        imagePicker.allowsEditing = true
        present(imagePicker, animated: true, completion: nil)
```

#### UIImagePickerController 델리게이트 구성

 UIImagePickerController 객체 사용자가 어떤 Action을 했을 경우 이를 애플리케이션에 알려줄 수 있는 방법이 필요한데, 이를 델리게이트 메서드를 호출하여 해결한다.

 따라서 UIImagePickerController 인스턴스를 만드는 클래스는 반드시 자신을 객체의 델리게이트로 선언하고, 이를 위해 UIImagePickerControllerDelegate와 UINavigationControllerDelegate 프로토콜을 준수해야 하며, 사용자가 미디어를 선택하거나 새로 만든 경우 didFinishPickingMediaWithInfo 메서드가 호출되며 사용자가 취소를 한 경우는 imagePickerControllerDidCance 메서드가 호출.

```swift
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {
		//편집되지 않았거나 촬영된 원본 사진일 경우
        if let image = info[UIImagePickerControllerOriginalImage] as? UIImage {
            //이미지의 가로세로 비율을 유지한 채로 이미지를 모두 출력할 수 있게 확대하거나 축소
            imagePickerView.contentMode = .scaleAspectFit
            //이미지 뷰에 저장
            imagePickerView.image = image
            //cancel 클릭 시 
            dismiss(animated: true, completion: nil)
        }
    }
```
이미지 피커 컨트롤러 객체에 편집이 허용되어 있다면 
```swift
let image = info[UIImagePickerControllerEditedImage] as UIImage
```
#### 디바이스 지원 여부 확인

 모든 iOS 디바이스가 같은 기능을 제공하지는 않는다.  3GS 이전의 아이폰 모델은 비디오 녹화를 지원하지 않았다.일부 아이팟 터치 모델은 카메라가 없기 때문에 이미지 피커 컨트롤러에서도 카메라와 카메라 롤을 사용할 수 없다. 이러한 디바이스간의 차이가 있기 때문에 UIImagePickerController 클래스를 사용할때 디바이스의 기능을 체크하는 것이 중요하다. UIImagePickerController의 isSourceTypeAvailable 클래스 메서드를 사용하여 이를 체크할수 있다.
```swift
//카메라의 존재 유무 체크
  if UIImagePickerController.isSourceTypeAvailable(UIImagePickerControllerSourceType.camera) {
            let imagePicker = UIImagePickerController()
            imagePicker.delegate = self
            //타입을 예측할 수 있다면 타입명의 생략이 가능함.UIImagePickerControllerSourceType
            imagePicker.sourceType = .camera
            //편집 가능
            imagePicker.allowsEditing = false
            self.present(imagePicker, animated: true, completion: nil)
        }
```
#### 이미지 저장

 작업한 이미지를 저장하는 소스는 다음과 같다.
```swift
 let imageData = UIImageJPEGRepresentation(imagePickerView.image!, 0.5)
        let compressedJPGImage = UIImage(data: imageData!)
        UIImageWriteToSavedPhotosAlbum(compressedJPGImage!, nil, nil, nil)
```

>UIImageJPEGRepresentation 메서드는 지정된 이미지의 데이터를 JPEG 형식으로 반환한다. 파라미터에는 원래의 imageData와 압축 품질을 지정하는데 0이 가장 높은 압축률, 1이 가장 낮은 압축률을 의미한다.
>
>UIImageWriteToSavedPhotosAlbum 메서드는 앨범에 저장하는 메서드다.
