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

## 데이터바인딩 기본 ( Kotlin )
- activity_main
```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <variable
            name="main"
            type="com.example.livs2.MainActivity" />
        <variable
            name="user"
            type="com.example.livs2.model.User" />

    </data>


    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

        <EditText
            android:hint="이름"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            android:id="@+id/EditText_name"
            android:layout_width="200dp"
            android:layout_height="wrap_content"/>

        <EditText
            android:hint="나이"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/EditText_name"
            android:id="@+id/EditText_age"
            android:layout_width="200dp"
            android:layout_height="wrap_content"/>

        <EditText
            android:hint="주소"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/EditText_age"
            android:id="@+id/EditText_address"
            android:layout_width="200dp"
            android:layout_height="wrap_content"/>

        <Button
            android:id="@+id/btn_submit"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toBottomOf="@id/EditText_address"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="@{ () -> main.Btn_click()}"/>

        <TextView
            android:text="@{user._name}"
            android:id="@+id/TextView_name"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toBottomOf="@id/btn_submit"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>
        <TextView
            android:text="@{user._age}"
            android:id="@+id/TextView_age"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toBottomOf="@id/TextView_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>
        <TextView
            android:text="@{user._address}"
            android:id="@+id/TextView_address"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toBottomOf="@id/TextView_age"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>
    </androidx.constraintlayout.widget.ConstraintLayout>

</layout>
```
- User.kt
```
package com.example.livs2.model

data class User(
    val _name : String,
    val _age : String,
    val _address : String
)
```
- MainActivity.kt
```
package com.example.livs2

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Toast
import androidx.databinding.DataBindingUtil
import com.example.livs2.databinding.ActivityMainBinding
import com.example.livs2.model.User

class MainActivity : AppCompatActivity() {
    private lateinit var binding : ActivityMainBinding
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this,R.layout.activity_main)
        binding.main = this

//        binding.btnSubmit.setOnClickListener {
//            var name = binding.EditTextName.text.toString()
//            var age = binding.EditTextAge.text.toString()
//            var address = binding.EditTextAddress.text.toString()
//            Toast.makeText(this,"클릭 $name , $age , $address",Toast.LENGTH_SHORT).show()
//            binding.user = User(name, age, address)
//        }
    }
    fun Btn_click(){
        var name = binding.EditTextName.text.toString()
        var age = binding.EditTextAge.text.toString()
        var address = binding.EditTextAddress.text.toString()
        Toast.makeText(this,"클릭 $name , $age , $address",Toast.LENGTH_SHORT).show()
        binding.user = User(name, age, address)
    }
}
```

## 데이터바인딩 기본 ( Java )
- User.java
```
package com.example.lives.model;

public class User {
    private String name;
    private String age;
    private String address;

    public User(String name, String age, String address){
        this.name = name;
        this.age = age;
        this.address = address;
    }

    public String getName() {
        return name;
    }

    public String getAge() {
        return age;
    }

    public String getAddress() {
        return address;
    }
}

```
- MainActivity.java
```
package com.example.lives;

import androidx.appcompat.app.AppCompatActivity;
import androidx.databinding.DataBindingUtil;

import android.os.Bundle;
import android.view.View;

import com.example.lives.databinding.ActivityMainBinding;
import com.example.lives.model.User;

public class MainActivity extends AppCompatActivity{
    private ActivityMainBinding binding;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        binding.setMain(this);

        binding.btnSubmit.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v) {
                String name = binding.EditTextName.getText().toString();
                String age = binding.EditTextAge.getText().toString();
                String address = binding.EditTextAddress.getText().toString();

                User user = new User(name,age,address);
                binding.setUser(user);
            }
        });
    }
}
```
