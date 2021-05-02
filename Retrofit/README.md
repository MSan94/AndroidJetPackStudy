# 2021.05.02 Retrofit

## Retrofit이란?
- 사전적의미는 ***개조*** 라는 뜻
- Retrofit은 안드로이드 애플리케이션에서 통신기능을 위해 만들어놓은 라이브러리
- REST 기반의 웹 서비스를 통해 JSON, XML 구조의 데이터를 쉽게 가져오고 업로드 가능
- 주로 Gson과 같이 사용

## Retrofit을 사용하는 이유?
- Retrofit은 TypeSafe한 Http 통신
- java.net에 포함된 클래스로 별도 라이브러리 추가x
- 커스텀 가능
- 단점으로는, 직접 구현해야하는 경우가 많다.

## Retrofit 공식주소
- https://square.github.io/retrofit

## Retrofit Http Api Interface
```
public interface GitHubService{
  @GET("user/{user}/repos")
  Call<List<Repo>> listRepos(@Path("user") String user);
}
```

## Retrofit 생성
```
Retrofit retrofit = new Retrofit.Builder()
  .baseUrl("https://api.github.com/")
  .build();
```

## API 선언
```
@GET("user/list")
// 아웃풋 설정 , output은 각 api마다 다르다.
@GET("user/list?output=json")
```

## URL 설정
```
@GET("group/{id}/users") Call<List<User>> groupList (@Path ("id") int groudId)
// 매개변수 추가
@GET("group/{id}/users") Call<List<User>> groupList (@Path ("id") int groudId, @Query ("sort") String sort)
// Map 사용
@GET("group/{id}/users") Call<List<User>> groupList (@Path ("id") int groudId, @QueryMap Map <String, String> options)
```

## 바디 요청
```
@POST("users/new") Call <User> createUser(@Body User user)
```


## 인터파크 도서 가져오기

### API폴더
- BookService
```
package com.example.aop_part2.bookreview.api

import com.example.aop_part2.bookreview.model.BestSellerDto
import com.example.aop_part2.bookreview.model.SearchBookDto
import retrofit2.Call
import retrofit2.http.GET
import retrofit2.http.Query

interface BookService {

    @GET("/api/search.api?output=json")
    fun getBooksByName(
        @Query("key") apiKey : String,
        @Query("query") keyword : String
    ): Call<SearchBookDto>

    @GET("/api/bestSeller.api?output=json&categoryId=100")
    fun getBestSellerBooks(
        @Query("key") apiKey : String
    ): Call<BestSellerDto>
}
```

### Model 폴더
- Book
```
package com.example.aop_part2.bookreview.model

import com.google.gson.annotations.SerializedName

data class Book(
    @SerializedName("itemId") val id: Long,
    @SerializedName("title") val title: String,
    @SerializedName("description") val description: String,
    @SerializedName("coverSmallUrl") val coverSmallUrl: String
)
```
- BestSellerDto
```
package com.example.aop_part2.bookreview.model

import com.google.gson.annotations.SerializedName

data class BestSellerDto(
    @SerializedName("title") val title: String,
    @SerializedName("item") val books: List<Book>
)
```
- SearchBookDto
```
package com.example.aop_part2.bookreview.model

import com.google.gson.annotations.SerializedName

data class SearchBookDto(
    @SerializedName("title") val title: String,
    @SerializedName("item") val books: List<Book>
)
```

### MainActivity
```
package com.example.aop_part2.bookreview

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import com.example.aop_part2.bookreview.api.BookService
import com.example.aop_part2.bookreview.model.BestSellerDto
import retrofit2.Call
import retrofit2.Callback
import retrofit2.Response
import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val retrofit = Retrofit.Builder()
            .baseUrl("https://book.interpark.com")
            .addConverterFactory(GsonConverterFactory.create())
            .build()

        val bookService = retrofit.create(BookService::class.java)

        bookService.getBestSellerBooks("6F7A2BF06A59D655C0D7B9996CB5ACBE635BF946C0B1C733A9A3E03550A143D1")
            .enqueue(object : Callback<BestSellerDto> { //큐에다 삽입
                override fun onResponse( //성공시 응답
                    call: Call<BestSellerDto>,
                    response: Response<BestSellerDto>
                ) {
                    if(response.isSuccessful.not()){ //실패시 예외처리
                        Log.e(TAG, "Not Success")
                        return
                    }
                    response.body()?.let{
                        Log.d(TAG,it.toString())
                        it.books.forEach{ book ->
                            Log.d(TAG, book.toString())
                        }
                    }
                }

                override fun onFailure(call: Call<BestSellerDto>, t: Throwable) { //실패시 응답
                    Log.e(TAG, t.toString())
                }

            })
    }

    companion object {
        private const val TAG = "MainActivity"
    }
}

//network security policy -> http 형식이라 발생
// https 요청 방법 , http 요청방법
```
