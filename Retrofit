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
