
# 2021.04.30 LiveData

## LiveData란?
- LiveData는 식별 가능한 데이터 홀더 클래스
- LiveData는 Activity, Fragment, Service 등의 수명주기를 알고있다.
- 즉, 수명주기의 활성 상태에 있는 앱의 구성요소만 관찰하여 업데이트 한다.

## LiveData의 장점
- UI와 데이터 상태의 일치 보장
  - 옵저버패턴을 잉요하여 기본 데이터가 변경될 시 Observer 객체에 이를 알리며, UI를 업데이트 한다.
- 메모리 누수가 없다
  - Lifecycle 객체에 결합되어 수명 주기가 끝나면 자동 삭제
- 비정상 종료가 없다
  - 수명주기가 비활성 상태일 경우 이벤트 수신을 받지 않는다.
- 수명주기를 자동으로 관리
- 최신 데이터 유지
  - 비활성 상태에서 다시 활성화가 될 시 최신 데이터를 받는다.
  - ex) BackGround에 있던 활동은 ForeGround로 돌아온 직후 최신 데이터를 받는다.
- 장치의 회전이나 프래그먼트, 액티비티가 재생성 되어도 데이터의 변화가 없다.
- 리소스 공유
  - 시스템 서비스를 공유할 수 있도록 싱글톤 패턴을 사용하는 LiveData 객체를 확장하여 서비스 래핑 가능
  - LiveData 객체가 시스템 서비스에 한번 연결되면 리소스가 필요한 모든 옵저버가 LiveData 객체를 볼 수 있다.

## Gradle 셋팅
```
id 'kotlin-kapt'

dataBinding{
    enabled = true
}

def lifecycle_version = "2.2.0"
implementation "androidx.lifecycle:lifecycle-viewmodel-ktx:$lifecycle_version"
implementation "androidx.lifecycle:lifecycle-livedata-ktx:$lifecycle_version"
implementation "androidx.activity:activity-ktx:1.1.0"

```

## ViewModel이란?
- MVVM 패턴으로부터 파생
- MVVM의 관점에서 보는 ViewModel과 Jetpack에 포함된 ViewModel 클래스를 구분하기 위해 AAC ViewModel이라고도 부른다

## ViewModel의 필요성
- ViewModel은 View로부터 독립적이며, View가 필요한 데이터만을 소유
- MVVM 패턴을 적용하면 Activity나 Fragment같은 UI 컨트롤러의 과도한 책임을 분담하여 클래스가 커지는 것을 방지
- 유지보수, 재사용성, 테스트 등을 용이하게 한다.

## ViewModel의 특징
- Activity가 완전히 종료, Fragment가 완전히 분리될 때까지 메모리에 남아있도록 설계
- 액티비티가 최초 생성될 때 일반적으로 ViewModel을 인스턴스화 하여 생명주기를 함께 시작
- Configuration 변경이 발생 시 Activity가 다시 시작되는 것을 확인 가능 -> but, ViewModel은 여전히 메모리 상에 남아있다.
  - Activity 내부에서 Configuration 변경과 무관하게 유지되는 NonConfigurationInstances 객체를 따로 관리
- Activity의 finish() 호출등에 의해 Activity의 생명주기가 종료되면 내부 LifecycleEventObserver를 통해 ViewModel도 onCleared() 콜백 호출

## ViewModel 요청 프로세스
![image](https://user-images.githubusercontent.com/81352078/116787446-00a25680-aadf-11eb-9d89-6f1d4cdcb9d9.png)
- ViewModelProvider를 통해 ViewModel 인스턴스 요청
- ViewModelProvider 내부에서는 ViewModelStoreOwner를 참조하여 ViewModelStore를 가져온다.
- ViewModelStore에게 이미 생성된 ViewModel 인스턴스 요청
- ViewModelStore가 적합한 ViewModel 인스턴스를 가지고 있지 않다면, Factory를 통해 ViewModel을 생성
- 생성한 ViewModel 인스턴스를 ViewModelStore에 저장하고 만들어진 ViewModel 인스턴스를 클라이언트에 반환

## ViewModel 구현
- ViewModel을 상속받는 서브 클래스 정의
```
class MainViewModel : ViewModel(){}
```
- ViewModel 인스턴스 접근
```
class MainActivity : AppCompayActivity(){
  private lateinit var viewModel : MainViewModel
  override fun onCreate(savedInstanceState : Bundle?){
    super.onCreate(savedInstanceState)
    viewModel = ViewModelProvider(this, ViewModelProvider.NewInstanceFactory()).get(MainViewModel::class.java)
  }
}
```
- ViewModel을 생성하기 위해선 ViewModelProvider 객체 필요
- ViewModelProvider을 생성하기 위해 매개변수로 ViewModelStoreOwner, ViewModelProvider.Factory 필요
- 
