# 2021.04.30 데이터바인딩

## 데이터바인딩이란?
- 선언적 방식으로 레이아웃의 UI 구성요소를 앱의 데이터 소스와 결합할 수 있도록 지원하는 라이브러리
- 기존
  ```
  findViewById<TextView>(R.id.sample_text).apply {
    text = viewModel.userName
  }
  ```
- 변경
  ```
  <TextView
        android:text="@{viewmodel.userName}" />
  ```

## 데이터바인딩을 왜 사용할까?
- 안드로이드에서 지원하는 라이브러리로 deprecated에 대한 우려가 없다.
- 코드를 통해 적용했던 작업들을 XML에서 처리할 수 있다.
- View 자체에서 데이터 변경에 대한 동적 처리가 가능해진다.
  - View Model에 데이터만 남게 함으로써, View-ViewModel 분리가 용이해진다.
- Activity에서 View들을 바인딩 할 때 실수가 발생할 확률이 적다.
- 많은 UI 프레임워크 호출을 삭제할 수 있고, 파일이 더욱 단순화되어 유지관리가 쉬워지며, 앱 성능 향상과 메모리 누수 및 NULL 포인터 방지

## 어댑터
- 어댑터를 통해 setText() 메서드를 호출하여 텍스트 속성을 설정하거나, setOnClickListener() 메서드를 호출하여 이벤트등을 추가 가능
```
    @BindingAdapter("app:goneUnless")
    fun goneUnless(view: View, visible: Boolean) {
        view.visibility = if (visible) View.VISIBLE else View.GONE
    }

```

## 사용
```
dataBinding{
  enabled = true
}
```
