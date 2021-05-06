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

# 2021.05.06 실습

## 롤 로테이션 챔피언 id를 가져와서 이름값 변경

```
interface LolAPI {
    @GET("/lol/platform/v3/champion-rotations")
    fun getMovie(
        @Query("api_key") key : String
    ): Call<LolDto>
}
```

```
data class LolDto(
    @SerializedName("freeChampionIds") val freeChampionIds : List<Int>
)
```

```
class ChampionName {
    var champ_Name: String = ""

    constructor(id: Int) {
        champ_Name = when (id) {
            1 -> "Annie"
            2 -> "Olaf"
            3 -> "Galio"
            4 -> "Twisted Fate"
            5 -> "Xin Zhao"
            6 -> "Urgot"
            7 -> "LeBlanc"
            8 -> "Vladimir"
            9 -> "Fiddlesticks"
            10 -> "Kayle"
            11 -> "Master Yi"
            12 -> "Alistar"
            13 -> "Ryze"
            14 -> "Sion"
            15 -> "Sivir"
            16 -> "Soraka"
            17 -> "Teemo"
            18 -> "Tristana"
            19 -> "Warwick"
            20 -> "Nunu & Willump"
            21 -> "Miss Fortune"
            22 -> "Ashe"
            23 -> "Tryndamere"
            24 -> "Jax"
            25 -> "Morgana"
            26 -> "Zilean"
            27 -> "Singed"
            28 -> "Evelynn"
            29 -> "Twitch"
            30 -> "Karthus"
            31 -> "Cho'Gath"
            32 -> "Amumu"
            33 -> "Rammus"
            34 -> "Anivia"
            35 -> "Shaco"
            36 -> "Dr. Mundo"
            37 -> "Sona"
            38 -> "Kassadin"
            39 -> "Irelia"
            40 -> "Janna"
            41 -> "Gangplank"
            42 -> "Corki"
            43 -> "Karma"
            44 -> "Taric"
            45 -> "Veigar"
            48 -> "Trundle"
            50 -> "Swain"
            51 -> "Caitlyn"
            53 -> "Blitzcrank"
            54 -> "Malphite"
            55 -> "Katarina"
            56 -> "Nocturne"
            57 -> "Maokai"
            58 -> "Renekton"
            59 -> "Jarvan IV"
            60 -> "Elise"
            61 -> "Orianna"
            62 -> "Wukong"
            63 -> "Brand"
            64 -> "Lee Sin"
            67 -> "Vayne"
            68 -> "Rumble"
            69 -> "Cassiopeia"
            72 -> "Skarner"
            74 -> "Heimerdinger"
            75 -> "Nasus"
            76 -> "Nidalee"
            77 -> "Udyr"
            78 -> "Poppy"
            79 -> "Gragas"
            80 -> "Pantheon"
            81 -> "Ezreal"
            82 -> "Mordekaiser"
            83 -> "Yorick"
            84 -> "Akali"
            85 -> "Kennen"
            86 -> "Garen"
            89 -> "Leona"
            90 -> "Malzahar"
            91 -> "Talon"
            92 -> "Riven"
            96 -> "Kog'Maw"
            98 -> "Shen"
            99 -> "Lux"
            101 -> "Xerath"
            102 -> "Shyvana"
            103 -> "Ahri"
            104 -> "Graves"
            105 -> "Fizz"
            106 -> "Volibear"
            107 -> "Rengar"
            110 -> "Varus"
            111 -> "Nautilus"
            112 -> "Viktor"
            113 -> "Sejuani"
            114 -> "Fiora"
            115 -> "Ziggs"
            117 -> "Lulu"
            119 -> "Draven"
            120 -> "Hecarim"
            121 -> "Kha'Zix"
            122 -> "Darius"
            126 -> "Jayce"
            127 -> "Lissandra"
            131 -> "Diana"
            133 -> "Quinn"
            134 -> "Syndra"
            136 -> "Aurelion Sol"
            141 -> "Kayn"
            142 -> "Zoe"
            143 -> "Zyra"
            145 -> "Kai'Sa"
            150 -> "Gnar"
            154 -> "Zac"
            157 -> "Yasuo"
            161 -> "Vel'Koz"
            163 -> "Taliyah"
            164 -> "Camille"
            201 -> "Braum"
            202 -> "Jhin"
            203 -> "Kindred"
            222 -> "Jinx"
            223 -> "Tahm Kench"
            235 -> "Senna"
            236 -> "Lucian"
            238 -> "Zed"
            240 -> "Kled"
            245 -> "Ekko"
            246 -> "Qiyana"
            254 -> "Vi"
            266 -> "Aatrox"
            267 -> "Nami"
            268 -> "Azir"
            350 -> "Yuumi"
            412 -> "Thresh"
            420 -> "Illaoi"
            421 -> "Rek'Sai"
            427 -> "Ivern"
            429 -> "Kalista"
            432 -> "Bard"
            497 -> "Rakan"
            498 -> "Xayah"
            516 -> "Ornn"
            517 -> "Sylas"
            518 -> "Neeko"
            523 -> "Aphelios"
            555 -> "Pyke"
            875 -> "Sett"
            else -> "noData"
        }
    }
}
```
```
package com.example.retrofit2

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import com.example.retrofit2.api.LolAPI
import com.example.retrofit2.model.ChampionName
import com.example.retrofit2.model.LolDto
import retrofit2.Call
import retrofit2.Callback
import retrofit2.Response
import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory

class MainActivity : AppCompatActivity() {
    private lateinit var retrofit : Retrofit
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        var LotationList = mutableListOf<ChampionName>()

        retrofit = Retrofit.Builder()
            .baseUrl("https://kr.api.riotgames.com/")
            .addConverterFactory(GsonConverterFactory.create())
            .build()

        val movieAPI = retrofit.create(LolAPI::class.java)
        movieAPI.getMovie("RGAPI-6f0350dd-375d-40d3-8443-dcada36b27d4")
            .enqueue(object: Callback<LolDto>{
                override fun onResponse(call: Call<LolDto>, response: Response<LolDto>) {
                    if(response.isSuccessful.not()){
                        Log.d("Test", "장애")
                        return
                    }
                    response.body()?.let {
                        it.freeChampionIds.forEach {
                            val championName = ChampionName(it)
                            LotationList.add(championName)
                        }

                        for(i in 0 until LotationList.size){
                            Log.d("Test", "${LotationList[i].champ_Name.toString()}")
                        }
                    }

                }

                override fun onFailure(call: Call<LolDto>, t: Throwable) {
                    Log.d("Test", "실패")
                    Log.d("Test", "${t.toString()}")
                    Log.d("Test", "${call.request()}")
                }

            })
    }
}
```
