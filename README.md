# **Maps_Diary**

Android Studio

GPS를 사용하여 사용자의 경로를 지도에 표시하여 기록하는 다이어리 앱입니다.

## **Files**

- [AndroidStudio_File]
    - [Maps_Diary](./Maps_Diary.java)

## **주요 기능(fundamental features)**

- GPS를 사용하여 현재 위치를 원과 선으로 표시
    - GPS 요청 주기(A)
    - GPS 요청 거리(B)
        - LocationManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, ***A***, ***B***, locationListener);
    - GPS 수신 정확도
        - Location.getAccuracy()
    - 원 그리기
        - addCircle()
    - 선 그리기
        - addPolyline()

- 현재 위치 정보를 데이터베이스에 저장

- 정보 추가, 수정, 삭제

- 다이어리

- 만보계, 거리 등을 활용하여 운동량 체크(시간, 거리, 속도 등)

- 공유, 초기화, 백업 등

---
### # 초기화면

- 시작, 설정, 도움말

#### ## 시작

- 처음 시작 시에만 초기설정
    - GPS 주기
        - 시간: 기본값 or 설정
        - 거리: 기본값 or 설정
    - 점 크기, 색, 모양
    - 선 굵기, 색
    - 마커 크기, 모양
    - 시작할 때마다 설정? (Yes / No)

- 목록
    - 하루의 기록들을 리스트로 표시
    - 기록을 표시하고 숨기는 기능(체크박스)
    - 선 색 변경
    - 달력으로 보기

- 달력(calendar view)
    - 날짜마다 기록한 총 거리, 시간, 운동량 등

- 점 클릭 시 레이아웃
    - 추가
        - 시간
        - 내용
        - 사진
    - 수정
    - 삭제

- 저장
    - SQLite 사용 ( 열(Column), 행(row) or 레코드(record) )
    - 해당되는 시간에 저장됨
    - 점, 선, 내용 등

#### ## 설정

- 백그라운드에서도 실행
- 시작과 종료시기
    - 기본(어플 시작했을 때)
    - 시간 입력 (Time picker)
    - GPS를 켰을 때?
    - 배터리가 30% 남았을 때?
- 점 크기, 색, 모양(원형, 사각형, 삼각형, ...)
- 선 굵기, 색
- 마커 모양, 크기?
- 시작할 때마다 설정? (Yes / No)

#### ## 도움말

- 스와이프 화면전환 --> ViewPager

---

## **궁금한 것들**

### 사용자 입장에서 봤을 때

- 앱이 켜진 상태에서 다른 지도 앱을 사용해도 괜찮은지
- 기록 중에 다시 처음부터 기록하는 기능이 필요한지(다시 시작)
- 

## **기능**

### 지도 api 가져오기

1. 안드로이드 스튜디오에서 새로운 프로젝트를 생성하고 Google Maps Activity를 선택한다.
2. google_maps_api.xml 에서 링크를 복사하여 진입한다.
3. 구글 로그인을 하고 api 키를 복사한다.
4. google_maps_api.xml 에 YOUR_KEY_HERE 부분을 지우고 api 키를 붙여넣는다.

### 사용자 위치 파악하기

- GPS 위치 제공자(GPS_PROVIDER)

- 네트워크 위치 제공자(NETWORK_PROVIDER)
    - 위치 제공자(Location Provider)
    - 위치 관리자(Location Manager)

```java
public class MainActivity extends AppCompatActivity {
    TextView status;

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        status = (TextView) findViewById(R.id.status);
        LocationManager locationManager = (LocationManager) this.getSystemService(Context.LOCATION_SERVICE);

        LocationListener = new LocationListener() {
            public void onLocationChanged(Location location) {
                status.setText("위도:" + location.getLatitude() + "\n경도:" + location.getLongitude() + "\n고도:" + location.getAltitude());
            }

            public void onStatusChanged(String provider, int status, Bundle extras) {}
            public void onProviderEnabled(String provider) {}
            public void onProviderDisabled(String provider) {}
        };

        // 위치를 업데이트 받기 위하여 리스너를 위치 관리자에 등록한다.
        LocationManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, 0, 0, locationListener);
    }
}
```
```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

### 지도 위에 마커 그리기

- 마커(Marker) 클래스의 객체로 지도 위에서 위치를 나타내는 이미지이다.

```java
public class MapsActivity extends FragmentActivity implements GoogleMap.OnMarkerClickListener, OnMapReadyCallback {

    private GoogleMap mMap;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_maps);
        // Obtain the SupportMapFragment and get notified when the map is ready to be used.
        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                .findFragmentById(R.id.map);
        mapFragment.getMapAsync(this);
    }

    @Override
    public void onMapReady(GoogleMap map) {
        mMap = map;

        // Add a marker in Sydney and move the camera
        LatLng SEOUL = new LatLng(37.566535, 126.977969);
        LatLng DAEJEON = new LatLng(36.350412, 127.384548);
        LatLng BUSAN = new LatLng(35.179554, 129.075642);

        Marker mSeoul = mMap.addMarker(new MarkerOptions().position(SEOUL).title("SEOUL"));
        mSeoul.setTag(0);
        Marker mDaejeon = mMap.addMarker(new MarkerOptions().position(DAEJEON).title("DAEJEON"));
        mDaejeon.setTag(0);
        Marker mBusan = mMap.addMarker(new MarkerOptions().position(BUSAN).title("BUSAN"));
        mBusan.setTag(0);

        mMap.setOnMarkerClickListener(this);
        mMap.moveCamera(CameraUpdateFactory.newLatLng(SEOUL));
    }

    @Override
    public boolean onMarkerClick(final Marker marker) {
        Integer clickCount = (Integer)marker.getTag();

        if(clickCount != null) {
            clickCount += 1;
            marker.setTag(clickCount);
            Toast.makeText(this, marker.getTitle() + " 가 클릭되었음, 클릭횟수: " + clickCount, Toast.LENGTH_SHORT).show();
        }
        return false;
    }
}
```

### 지도 위에 그림 그리기

- addPolyline() : 선분

```java
// 새로운 Polyline 객체를 생성하고 점들을 추가한다.
PolylineOptions rectOptions = new PolylineOptions()
    .add(new LatLng(37.35, -122.0))
    .add(new LatLng(37.45, -122.0))
    .add(new LatLng(37.45, -122.2))
    .add(new LatLng(37.35, -122.2))
    .add(new LatLng(37.35, -122.0));

// 지도에 폴리 라인을 추가한다.
Polyline polyline = myMap.addPolyline(rectOptions);
```

- addPolygon()  : 다각형

```java
// 새로운 다각형 객체를 생성하고 점들을 추가한다.
PolygonOptions rectOptions = new PolygonOptions()
    .add(new LatLng(37.35, -122.0),
         new LatLng(37.45, -122.0),
         new LatLng(37.45, -122.2),
         new LatLng(37.35, -122.2),
         new LatLng(37.35, -122.0));

// 다각형을 지도에 추가한다.
Polygon polygon = myMap.addPolygon(rectOptions);
```

- addCircle()   : 원

```java
// 새로운 CircleOptions 객체를 생성하고 중심과 반지름을 정의한다.
CircleOptions circleOptions = new CircleOptions()
    .cetner(new LatLng(37.4, -122.1))
    .radius(1000)); // 미터

// 지도에 원을 추가한다.
Circle circle = myMap.addCircle(circleOptions);
```

- 도형의 속성 변경

```java
Polyline line = map.addPolyline(new PolylineOptions()
    .add(new LatLng(-37.81319, 144.96298), new LatLng(-31.95285, 155.85734))
    .width(25)
    .color(Color.BLUE)
    .geodesic(true));
```

---

### **데이터베이스 사용하기**

#### SQLiteOpenHelper 클래스로 데이터베이스 생성하기

- SQLiteOpenHelper 클래스
    - onCreate() : 데이터베이스 안에 테이블과 초기 데이터를 생성한다.
    - onUpgrade() : 데이터베이스를 업그레이드한다.

```java
class dbHelper extends SQLiteOpenHelper {
    
    public dbHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
        /*
            SQLiteOpenHelper(Context context, String name, SQLiteDatabase, CursorFactory factory, int version)
            매개변수 context에는 데이터베이스를 생성하는 액티비티를 전달한다.
            name은 데이터베이스 파일 이름이다.
            factory는 커서를 지정하는 매개변수로서 null을 전달하면 표준 커서가 사용된다.
            version은 데이터베이스의 버전이다.
        */
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL("CREATE TABLE contacts ( _id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, tel TEXT)");
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("DROP TABLE IF EXISTS contact");
        onCreate(db);
        /*
            데이터베이스의 버전이 올라가면 호출되므로 기존의 테이블을 삭제하고 새로 만들어주면 된다.
            일반적으로는 기존의 테이블을 버리고 새로운 테이블을 생성한다.
        */
    }
}
```

- SQLiteDatabase getWritableDatabase()

읽기/쓰기 모드로 데이터베이스를 오픈한다.
만약 처음으로 호출되는 경우에는 onCreate()가 호출된다.
만약 데이터베이스 스키마가 달라지는 경우에는 onUpgrade()가 호출된다.

- SQLiteDatabase getReadableDatabase()

읽기 전용 모드로 데이터베이스를 오픈한다.
근본적으로 getWritableDatabase()와 같지만 디스크가 꽉 찼거나 권한이 없어서 데이터베이스를 읽기 전용으로만 오픈할 때 사용한다.

```java
public class MainActivity extends AppCompatActivity {
    DBHelper helper;
    SQLiteDatabase db;

    public void onCreate(Bundle savedInstanceState) {
        ...
        helper = new DBHelper(this);
        try {
            db = helper.getWritableDatabase();
        } catch (SQLiteException ex) {
            db = helper.getReadableDatabase();
        }
        /*
            데이터베이스가 필요하면 getWritableDatabase()나 getReadableDatabase()를 호출한다.
            이들은 모두 SQLiteDatabase 객체를 반환하고 이들 객체는 데이터베이스를 나타내며 
            데이터베이스 연산을 제공하는 메소드들을 제공한다.
            이들 메소드들을 사용해서 데이터를 저장하거나 삭제하면 된다.
        */

        // 이제 필요할 때마다 db를 통해서 SQL 문장을 실행하면 된다.
        db.execSQL("INSERT INTO contact VALUES (null, '" + name + "', '" + tel + "');");
    }
}
```

```
데이터베이스 테이블을 생성할 때, 자동으로 증가되는 값을 _id 필드로 정의하는 것이 권장되는데, _id 필드가 있으면 레코드를 빠르게 탐색할 수 있다.
물론 데이터베이스를 개인적인 용도로만 사용한다면 없어도 된다.
하지만 만약 콘텐트 제공자를 구현한다면 반드시 유일한 _id를 포함시켜야 한다.
```

#### SQL을 사용하여서 데이터 추가, 삭제, 쿼리하기

SQLite 데이터베이스에 데이터를 추가, 삭제, 검색하는 작업은 기본적으로 SQL 문장을 통하여 이루어진다.
SQLiteDatabase 객체는 다음과 같이 일반적인 SQL 문장을 실행할 수 있는 메소드를 제공한다.

| Method | Description |
|--------|-------------|
| void execSQL(String sql) | SELECT 명령을 제외한 모든 SQL 문장을 실행한다. 예를 들어서 CREATE TABLE, DELETE, INSERT 등을 실행한다. |
| Cursor rawQuery (String sql, String[] selectionArgs) | SELECT 명령어를 사용하여 쿼리를 실행하려면 rawQuery()를 사용하면 된다. 쿼리의 결과는 Cursor 객체로 반환된다. Cursur는 데이터베이스에서 결과를 순회하고 데이터를 읽는 데 사용되는 표준적인 메커니즘이다. |

```java
// 레코드를 추가하는 문장
SQLiteDatabase db = helper.getWritableDatabase();
db.execSQL("INSERT INTO CUSTOMERS (ID, NAME, AGE, ADDRESS, SALARY)
    VALUES (1, 'Hong', 32, 'Seoul, Korea', 2000.00);";
```

쿼리에 관련된 SQL 문장은 `rawQuery()` 메소드를 사용하여야 한다.

```java
// CUSTOMERS 테이블에서 ID, NAME, SALARY 만 출력
SQLiteDatabase db = helper.getWritableDatabase();
Cursor cursor = db.rawQuery("SELECT ID, NAME, SALARY FROM CUSTOMERS", null);
```

```java
// 연봉이 2000 이상인 사람의 아이디와 이름, 월급을 출력
SQLiteDatabase db = helper.getWritableDatabase();
Cursor cursor = db.rawQuery("SELECT ID, NAME, SALARY
    FROM CUSTOMERS WHERE SALARY > 20000:", null);
```


```java
// 쿼리의 결과가 커서로 반환된다.
Cursor cursor = squliteDB.rawQuery("SELECT NAME FROM CUSTOMERS", null);
// 만약 커서가 결과를 가지고 있으면
if (cursor != null) {
    // 커서를 첫 번째 레코드로 이동한다.
    if (cursor.moveToFirst()) {
        do {
            // 커서로부터 이름을 얻는다.
            String name = cursor.getString(cursor.getColumnIndex("NAME"));
            // 이름을 ArrayList인 list에 추가한다.
            list.add(name);
            // 다음 레코드로 간다.
        } while (cursor.moveToNext());
    }
}
```

#### 전용 메소드를 사용하여서 데이터 추가, 삭제, 쿼리하기

SQLiteDatabase 객체는 데이터베이스에 데이터를 추가, 삭제, 검색하는 전용 메소드도 지원한다.

```java
String[] tableColumns = new String[] {
    "ID",
    "NAME"
};
String whereClause = "SALARY > ? AND AGE < ?";
String[] whereArgs = new String[] {
    "2000",
    "25"
};
String orderBy = "AGE";
Cursor c = sqLiteDatabase.query("CUSTOMERS", tableColumns, whereClause, whereArgs, null, null, orderBy);
```

? 기호는 whereArgs 배열에 있는 문자열로 차례대로 대치된다.

```java
String queryString = 
    "SELECT ID, NAME FROM CUSTOMERS " +
    "WHERE SALARY > ? AND AGE < ? ORDER BY AGE";
String[] whereArgs = new String[] {
    "2000",
    "25"
};
sqLiteDatabase.rawQuery(queryString, whereArgs);
```

```java
SQLiteDatabase db = mDbHelper.getWritableDatabase();
ContentValues values = new ContentValues();

values.put("NAME", "kim");
values.put("AGE", "26");
values.put("ADDRESS", "Incheon");
values.put("SALARY", "3000");

long newRowId;
newRowId = db.insert("CUSTOMERS", null, values);
```

```java
class DBHelper extends SQLiteOpenHelper {
    private static final String DATABASE_NAME = "mycontacts.db";
    private static final int DATABASE_VERSION = 2;

    public DBHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    public void onCreate(SQLiteDatabase db) {
        db.execSQL("CREATE TABLE contacts ( _id INTEGER PRIMARY KEY"+
                   " AUTOINCREMENT, name TEXT, tel TEXT);");
    }

    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("DROP TABLE IF EXISTS contacts");
        onCreate(db);
    }
}

public class DatabaseTest01Activity extends AppCompatActivity {
    DBHelper helper;
    SQLiteDatabase db;
    EditText edit_name, edit_tel;

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        helper = new DBHelper(this);

        try {
            db = helper.getWritableDatabase();
        } catch (SQLiteException ex) {
            db = helper.getReadableDatabase();
        }
        edit_name = (EditText) findViewById(R.id.name);
        edit_tel = (EditText) findViewById(R.id.tel);
    }

    public void insert(View target) {
        String name = edit_name.getText().toString();
        String tel = edit_tel.getText().toString();
        db.execSQL("INSERT INTO contacts VALUES (null, '" + name + "', '" + tel + "');");
        Toast.makeText(getApplicationContext(), "성공적으로 추가되었음", Toast.LENGTH_SHORT).show();
        edit_name.setText("");
        edit_tel.setText("");
    }

    public void search(View target) {
        String name = edit_name.getText().toString();
        Cursor cursor;
        cursor = db.rawQuery("SELECT name, tel FROM contacts WHERE name='" + name + "';", null);
        while (cursor.moveToNext()) {
            String tel = cursor.getString(1);
            edit_tel.setText(tel);
        }
    }
}
```

