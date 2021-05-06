# 2021.05.06 Room 

## Room이란?
- 안드로이드 앱에서 SQLite를 쉽고 편리하게 사용할 수 있도록 하는 기능.
- SQLite위에 만든 구글의 ORM
- 룸을 통해 앱의 단일 정보 소스로 제공되는 캐시를 통해 인터넷 연결 여부와 상관없이 앱의 주요 정보와 일관된 사본을 볼 수 있다.

## 구성요소
- DB
  - DB홀더를 포함하며 앱의 지속적인 관계형 데이터에 대한 기본 연결을 위한 Access Point 역할 수행
  - @Database로 처리된 클래스는 3가지 조건을 만족해야 한다.
    - RoomDatabase를 확장하는 추상 클래스
    - 어노테이션 내 DB와 연결된 엔티티의 목록을 포함
    - 인수가 0개인 추상 메서드를 포함하며, @Dao로 처리된 클래스 반환
  - 앱이 단일 프로세스에서 실행되는 경우 RoomDatabase 인스턴스의 리소스가 많이 소비되므로 싱글톤 패턴을 통해 인스턴스화 한다.
```
@Database(entities = arrayOf(User::class), version = 1)
abstract class AppDatabase : RoomDatabase(){
  abstract fun userDao() : UserDao
}
```
```
val db = Room.databaseBuilder(
  applicationContext,
  AppDatabase::class.java, "DB_NAME"
).build()
```
- Entity
