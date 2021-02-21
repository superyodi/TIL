# Room



## 🧐 Room은 무엇일까?



+ 구글에서 제공하는 ORM
  + ORM(Object Relational Mapping)

+ 기기에 데이터를 저장하고자 할 때 사용

+ 구성 요소
  + Room DataBase
    + 기본 SQLite 데이터베이스에 대한 엑세스 포인트 역할
    + 데이터베이스 작업 단순화
    + DAO를 사용해서 SQLite 데이터베이스에 쿼리 실행
  + DAO(Data Access Object)
    + SQL 쿼리들을 함수들로 매핑한다.
    + 사용자가 DAO의 함수를 호출하면 Room Database에서 해당 함수의 작업을 실행
  + Entitity
    + Room으로 작업할때 데이터베이스 테이블을 설명하는 <u>(1)annotated class</u>





## :sweat: Room은 개체 참조를 허용하지 않는다

보통 ORM에서는 Entity가 서로를 참조할 수 있지만 Room은 명시적으로 금지하고 있다.

왜냐하면 클라이언트 단위의 데이터베이스이기 때문에 개체를 참조한다면 쿼리 처리에 **오랜 시간이 걸리고** 결과적으로 **UI 스레드의 성능에 지장**을 줄 수 있기 때문이다. 



이런 이유로 개체 참조가 아니라 **Entity간의 관계를 정의하는 형태**로 사용해야한다. 

*예시 코드* 

```kotlin
@Entity 
data class Character ( 
  @PrimaryKey @ColumnInfo(name = "character_id") val characterId: Int, 
  @ColumnInfo(name = "place_belong_id") val placeBelongId: Int, 
  @ColumnInfo(name = "character_name") val characterName: String 
) 

data class PlaceWithCharacter(
  @Embedded val place: Place, 
  @Relation(
    parentColumn = "placeId", 
    entityColumn = "placeBelongId"
  ) 
  val characters: List<Character> 
)

출처: https://dunchi.tistory.com/94 [둔치의 개발이야기]
```

위 코드는` place`와 `characters:List<Character>` 속성을 가지므로 palce와 character가 1:n 매핑이다.

만약 ` place`와 `character: Character ` 였으면 1:1 매핑이었겠지.



더 정확하고 자세한건 안드로이드 공식문서(https://developer.android.com/training/data-storage/room/referencing-data?hl=ko)를 참고하자 







## :no_mouth: Type Converter



위와 같은 이유로 

Room은 Primitive Type과 Boxed Type(원시 자료형의 Wrapper Class)을 변환하는 기능은 제공하지만 항목 간 개체 참조는 허용하지 않는다.

(*Room provides functionality for converting between primitive and boxed types but doesn't allow for object references between entities.*)



때문에 사용자가 정의한 data type의 값을 단일 데이터베이스의 column에 저장하려고 할 때 TypeConverter를 사용해야 한다.

TypeConverter를 통해 Room에서 처리할 수 있는 데이터 타입으로 상호변환할 수 있다. 



### 예시 코드

아래 코드는 `Date` 인스턴스를 유지하려는 상황이다. 

아래와 같이  [`TypeConverter`](https://developer.android.com/reference/androidx/room/TypeConverter)를 작성하여 데이터베이스에 동등한 Unix 타임스탬프를 저장할 수 있다. 

```kotlin
class Converters {
        @TypeConverter
        fun fromTimestamp(value: Long?): Date? {
            return value?.let { Date(it) }
        }

        @TypeConverter
        fun dateToTimestamp(date: Date?): Long? {
            return date?.time?.toLong()
        }
    }
    
출처: https://developer.android.com/training/data-storage/room/referencing-data [Room을 사용하여 복잡한 데이터 참조]
```





### 활용 사례



뽀모도로 어플 프로젝트를 진행하다 빌드 에러가 났다. 

`error: Cannot figure out how to save tags field into database. You can consider adding a type converter for it.`



Entity 역할을 하는 Data Class에 `tags: ArrayList<String>` 변수가 있었는데 이걸 처리하는 과정에서 발생한 오류였다.

아래와 같이 List<String>을  Gson으로 변환하는 Converter를 만들어서 에러를 해결했다. 

```kotlin
class Converters {
    @TypeConverter
    fun listToJson(value: List<String>?) = Gson().toJson(value)

    @TypeConverter
    fun jsonToList(value: String) = Gson().fromJson(value, Array<String>::class.java).toList()
}
```



---

>  덧

(1) annotated class

자바 어노테이션이란,





![스크린샷 2021-02-10 오후 7.00.44](/Users/superyodi/Library/Application Support/typora-user-images/스크린샷 2021-02-10 오후 7.00.44.png)

 (출처: https://www.nextree.co.kr/p5864/)





---



> 참고
>
> + https://jinee0717.tistory.com/43



![8e4b761713e3a76b.png](https://developer.android.com/codelabs/android-room-with-a-view-kotlin/img/8e4b761713e3a76b.png?hl=ko)

