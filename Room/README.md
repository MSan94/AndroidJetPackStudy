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
  - 각 Entity는 하나 이상의 필드를 기본 키로 정의해야 한다.
  - 만약, ID를 자동으로 할당하려면 @PrimaryKey의 autoGenerate 속성을 설정
  - 복합 기본키로 @Entity 어노테이션의 primaryKeys 속성 사용
```
@Entity(primaryKeys = arrayOf("uid", "number"))
data class User(
  val uid : Int?,
  val number : Int?
)
```
  - 테이블의 이름 설정 -> tableName 속성을 통해 테이블의 이름을 다르게 지정하며, 이때 칼럼의 이름은 @ColumnInfo를 통해 지정
```
@Entity(tableName = "student")
data class Student(
  @PrimaryKey val Num : Int,
  @ColumnInfo(name = "st_Name")
  val Name : String?,
  @ColumnInfo(name = "st_Addr")
  val Addr : String?
)
```
  - Entity에 유지하지 않으려는 필드는 @Ignore를 사용
  - 상위 필드를 상속 시 @Entity 속성의 ignoreColumns 속성 사용
```
@Entity
data class Student(
  @PrimaryKey val Num : Int,
  @ColumnInfo(name = "st_Name")
  val Name : String?,
  @ColumnInfo(name = "st_Addr")
  val Addr : String?,
  @Ignore
  val age : String?
)
```
   - Room 역시 Index를 통해 성능을 향상시킬 수 있다.
   - unique 속성을 통해 제약조선 표시 가능
```
@Entity(indices = arrayOf(Index(value = ["st_Name","st_Addr"], unique = true)))
```

- Dao ( Data Access Objects)
  - DAO는 DB에 엑세스 하는 데 사용되는 메서드를 가진다.
  - 쿼리 사용 메서드 정의
  - 각 DAO에는 앱 DB에 대한 추상적 엑세스를 제공하는 방법이 있으므로 DAO객체는 room의 주요 구성 요소 형성
  - DAO는 인터페스스 , 추상 클래스일 수 있으며 추상 클래스의 경우 선택적으로 RoomDatabase를 유일한 매개변수로 사용하는 생성자를 가질 수 있다.
  
  - Insert : @Insert 어노테이션을 사용하며, 단일 트랜잭션의 DB에 모든 매개변수를 삽입하는 구현 생성
  - onConflict를 통해 충돌 시 해결방법을 정의하며, REPLACE 속성을 통해 충돌 시 새로 들어온 데이터로 교체
```
@Dao
interface MyDao{
  @Insert(onConflict = OnConflictStrategy.REPLACE)
  fun insertStudent(varage students : Student)
  @Insert
  fun insertBothStudent(student1 : Student, student2 : Student)
  @Insert
  fun insertStudentAndSubject(student : Student , friends : List<Student>)
}
```
  - Delete : 매개변수로 지정된 엔티티 집합을 DB에서 제거, 기본키를 통해 삭제할 엔티티를 찾는다.
  - 
