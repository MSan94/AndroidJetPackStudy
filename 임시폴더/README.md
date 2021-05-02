- manifest
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.aop_part2.bookreview">

    <uses-permission android:name="android.permission.INTERNET"/>
    <!-- usesCleartextTraffic : http 허용 -->
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:usesCleartextTraffic="true"
        android:theme="@style/Theme.BookReview">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

- adapter.BookAdapter
```
package com.example.aop_part2.bookreview.adapter

import android.view.LayoutInflater
import android.view.ViewGroup
import androidx.recyclerview.widget.DiffUtil
import androidx.recyclerview.widget.ListAdapter
import androidx.recyclerview.widget.RecyclerView
import com.bumptech.glide.Glide
import com.example.aop_part2.bookreview.databinding.ItemBookBinding
import com.example.aop_part2.bookreview.model.Book

//diffUtil -> 같은 아이템이 올라오면 새 값을 할당할지 안할지 결정
class BookAdapter : ListAdapter<Book, BookAdapter.BookItemViewHolder>(diffUtil) {
    //레이아웃이 item_book이라 ItemBookBinding
    inner class BookItemViewHolder(private val binding : ItemBookBinding): RecyclerView.ViewHolder(binding.root){
        fun bind(bookModel : Book){
            binding.titleTextView.text = bookModel.title
            binding.descriptionTextView.text = bookModel.description
            //이미지 로딩
            Glide
                .with(binding.coverImageView.context)
                .load(bookModel.coverSmallUrl)
                .into(binding.coverImageView)
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): BookItemViewHolder {
        return BookItemViewHolder(ItemBookBinding.inflate(LayoutInflater.from(parent.context), parent, false))
    }

    override fun onBindViewHolder(holder: BookItemViewHolder, position: Int) {
        holder.bind(currentList[position]) //바인딩된 뷰들은 정렬이 되어있기에 currentList[position]
    }

    companion object{
        val diffUtil = object : DiffUtil.ItemCallback<Book>(){
            //아이템이 같으면
            override fun areItemsTheSame(oldItem: Book, newItem: Book): Boolean {
                return oldItem == newItem
            }

            // 고유값이 같은지 확인
            override fun areContentsTheSame(oldItem: Book, newItem: Book): Boolean {
                return oldItem.id == newItem.id
            }

        }
    }
}
```

- api.BookService
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

- mode폴더
```
package com.example.aop_part2.bookreview.model

import com.google.gson.annotations.SerializedName

data class BestSellerDto(
    @SerializedName("title") val title: String,
    @SerializedName("item") val books: List<Book>
)

package com.example.aop_part2.bookreview.model

import com.google.gson.annotations.SerializedName

data class Book(
    @SerializedName("itemId") val id: Long,
    @SerializedName("title") val title: String,
    @SerializedName("description") val description: String,
    @SerializedName("coverSmallUrl") val coverSmallUrl: String
)


package com.example.aop_part2.bookreview.model

import com.google.gson.annotations.SerializedName

data class SearchBookDto(
    @SerializedName("title") val title: String,
    @SerializedName("item") val books: List<Book>
)
```

- Main
```
package com.example.aop_part2.bookreview

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import androidx.recyclerview.widget.LinearLayoutManager
import com.example.aop_part2.bookreview.adapter.BookAdapter
import com.example.aop_part2.bookreview.api.BookService
import com.example.aop_part2.bookreview.databinding.ActivityMainBinding
import com.example.aop_part2.bookreview.model.BestSellerDto
import com.example.aop_part2.bookreview.model.Book
import retrofit2.Call
import retrofit2.Callback
import retrofit2.Response
import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory

class MainActivity : AppCompatActivity() {

    private lateinit var binding : ActivityMainBinding
    private lateinit var adapter : BookAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        //ViewBinding
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        //리사이클뷰 초기화
        initBookRecyclerView()

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
                        //데이터 출력
                        adapter.submitList(it.books)
                    }
                }

                override fun onFailure(call: Call<BestSellerDto>, t: Throwable) { //실패시 응답
                    Log.e(TAG, t.toString())
                }

            })
    }

    fun initBookRecyclerView(){
        //어댑터 생성 -> 리사이클뷰 초기화
//        val adapter = BookAdapter()
        adapter = BookAdapter()
        binding.bookRecyclerView.layoutManager = LinearLayoutManager(this)
        binding.bookRecyclerView.adapter = adapter
    }

    companion object {
        private const val TAG = "MainActivity"
    }
}


//network security policy -> http 형식이라 발생
// https 요청 방법 , http 요청방법
```

- activity_main
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/searchEditText"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/bookRecyclerView"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/searchEditText" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

- item_book
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="16dp">

    <ImageView
        android:id="@+id/coverImageView"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:background="@drawable/background_gray_stroke_radius_16"
        android:padding="8dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/titleTextView"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="12dp"
        android:lines="1"
        android:textColor="@color/black"
        android:textSize="16sp"
        app:layout_constraintStart_toEndOf="@id/coverImageView"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="안드로이드 마스터하기" />

    <TextView
        android:id="@+id/descriptionTextView"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_marginTop="12dp"
        android:ellipsize="end"
        android:maxLines="3"
        android:textSize="12sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="@id/titleTextView"
        app:layout_constraintTop_toBottomOf="@+id/titleTextView" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

- 그외
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="purple_200">#FFBB86FC</color>
    <color name="purple_500">#FF6200EE</color>
    <color name="purple_700">#FF3700B3</color>
    <color name="teal_200">#FF03DAC5</color>
    <color name="teal_700">#FF018786</color>
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>
    <color name="gray">#aaaaaa</color>
</resources>
```
- background_gray_stroke_radius_16.
```
<?xml version="1.0" encoding="utf-8"?>
<shape android:shape="rectangle"
    xmlns:android="http://schemas.android.com/apk/res/android">

    <stroke
        android:width="1dp"
        android:color="@color/gray"/>
    <corners android:radius="16dp"/>
</shape>
```
