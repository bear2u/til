# 배달앱 클론 3주차 내용

>  메인화면에서 ListActivity 로 이동

메인화면에 `RecyclerView` 를 등록 한 후에 `GridLayoutManager` 를 등록한 후 일것이다. 

그럼 `Adapter` 에 클릭 이벤트를 등록해서 `ListAcitivity` 로 이동 처리 한다. 

```kotlin
fun <T : RecyclerView.ViewHolder> T.listen(event: (position: Int, type: Int) -> Unit): T {
    itemView.setOnClickListener {
        event.invoke(adapterPosition, itemViewType)
    }
    return this
}
```

- MainRecyclerViewAdapter

```kotlin
override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
	val view = LayoutInflater.from(context).inflate(R.layout.main_recyclerview_item, parent, false)
	return ViewHolder(view).listen{ pos, type ->
		clickListener(items[pos])
	}
}
```

>  `ListActivity` 생성 및 `ViewPager` 등록

- ListActivity 생성

- Main 으로 부터 `category` 데이터를 `parcelable` 형태로 데이터 전달 받는다.

```kotlin
category = intent.getParcelableExtra("item")
```

- `ViewPager` 등록

```kotlin
val pagerAdapter = TabPageAdapter(supportFragmentManager, items)
        pager.adapter = pagerAdapter
        pager.addOnPageChangeListener(TabLayout.TabLayoutOnPageChangeListener(tabLayout))

tabLayout.addOnTabSelectedListener(object : TabLayout.OnTabSelectedListener{
    override fun onTabReselected(tab: TabLayout.Tab?) {

    }

    override fun onTabUnselected(tab: TabLayout.Tab?) {

    }

    override fun onTabSelected(tab: TabLayout.Tab) {
        pager.currentItem = tab.position
    }
})

pager.currentItem = items.indexOf(category?.title)
```

- ListRecyclerViewAdapter 생성해서 바인딩 해준다.

```kotlin
class ListRecylerViewAdapter(
    private var items: List<Store>,
    private val context: Context,
    private val clickListener : (item: Store) -> Unit
) : RecyclerView.Adapter<ListRecylerViewAdapter.ViewHolder>() {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(context).inflate(R.layout.list_recyclerview_item, parent, false)
        return ViewHolder(view).listen{ pos, type ->
            clickListener(items[pos])
        }
    }

    override fun getItemCount(): Int = items.size

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.title.text = items[position].name
        val requestOptions = RequestOptions().apply{
            this.placeholder(R.drawable.place_hoder_icon)
            this.error(R.drawable.no_image)
            this.circleCrop()
        }
        Glide.with(holder.itemView)
            .setDefaultRequestOptions(requestOptions)
            .load(items[position].thumbnail)
            .into(holder.thumbnail)
    }

    class ViewHolder(view: View) : RecyclerView.ViewHolder(view) {
        val title = view.list_title
        val thumbnail = view.list_iv
    }

    fun updateItem(items : List<Store>) {
        this.items = items
        notifyDataSetChanged()
    }

}
```

- Category 데이터 클래스 등록

- `Parcelable` 을 상속받아서 전달할 수 있게끔 해준다.

```kotlin
data class Category (
    val no: Int,
    val resId: Int,
    val background: Int = R.drawable.bdt_btn_white,
    val title: String = "",
    val type: String = ""
) : Parcelable {

    constructor(parcel: Parcel) : this(
            parcel.readInt(),
            parcel.readInt(),
            parcel.readInt(),
            parcel.readString(),
            parcel.readString()) {
    }

    override fun writeToParcel(parcel: Parcel, flags: Int) {
        parcel.writeInt(no)
        parcel.writeInt(resId)
        parcel.writeInt(background)
        parcel.writeString(title)
        parcel.writeString(type)
    }

    override fun describeContents(): Int {
        return 0
    }

    companion object CREATOR : Parcelable.Creator<Category> {
        override fun createFromParcel(parcel: Parcel): Category {
            return Category(parcel)
        }

        override fun newArray(size: Int): Array<Category?> {
            return arrayOfNulls(size)
        }
    }

}
```

- 액션바에 뒤로가기 아이콘 및 타이틀 구성

```kotlin
supportActionBar?.setDisplayHomeAsUpEnabled(true)
supportActionBar?.setDisplayShowHomeEnabled(true)
supportActionBar?.elevation = 0f
```

- 플로팅 액션 버튼 등록

- 탭 및 viewPager 등록

```kotlin
private fun initTab() {

    items.forEach {
        tabLayout.addTab(tabLayout.newTab().setText(it))
    }
}
private fun initViewPager() {
        val pagerAdapter = TabPageAdapter(supportFragmentManager, items)
        pager.adapter = pagerAdapter
        pager.addOnPageChangeListener(TabLayout.TabLayoutOnPageChangeListener(tabLayout))

        tabLayout.addOnTabSelectedListener(object : TabLayout.OnTabSelectedListener{
            override fun onTabReselected(tab: TabLayout.Tab?) {

            }

            override fun onTabUnselected(tab: TabLayout.Tab?) {

            }

            override fun onTabSelected(tab: TabLayout.Tab) {
                pager.currentItem = tab.position
            }
        })

        pager.currentItem = items.indexOf(category?.title)
    }
```


- ListActivity 전체 소스

```kotlin
class ListActivity : AppCompatActivity() {

    var category : Category? = null

    lateinit var items : Array<String>

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_list)

        supportActionBar?.setDisplayHomeAsUpEnabled(true)
        supportActionBar?.setDisplayShowHomeEnabled(true)
        supportActionBar?.elevation = 0f

        category = intent.getParcelableExtra("item")
        title = category?.title

        fab.setOnClickListener {            
            startActivity(Intent(this, RegisterActivity::class.java))
        }

        items = resources.getStringArray(R.array.menus)

        initTab()
        initViewPager()

    }

    private fun initTab() {

        items.forEach {
            tabLayout.addTab(tabLayout.newTab().setText(it))
        }
    }

    private fun initViewPager() {
        val pagerAdapter = TabPageAdapter(supportFragmentManager, items)
        pager.adapter = pagerAdapter
        pager.addOnPageChangeListener(TabLayout.TabLayoutOnPageChangeListener(tabLayout))

        tabLayout.addOnTabSelectedListener(object : TabLayout.OnTabSelectedListener{
            override fun onTabReselected(tab: TabLayout.Tab?) {

            }

            override fun onTabUnselected(tab: TabLayout.Tab?) {

            }

            override fun onTabSelected(tab: TabLayout.Tab) {
                pager.currentItem = tab.position
            }
        })

        pager.currentItem = items.indexOf(category?.title)
    }

    override fun onOptionsItemSelected(item: MenuItem?): Boolean {
        if(item?.itemId == android.R.id.home)
            finish()
        return super.onOptionsItemSelected(item)
    }
}
```



> `ListFragment`, `ListFragContract`, `ListFragPresente`   생성

- newInstance 는 Fragment 생성시 static 으로 데이터를 생성하는 방법중 하나


  ```kotlin
      companion object {
          private const val ARG_PARAM = "type"
  
          fun newInstance(type : String) : ListFrag {
              val listFrag = ListFrag()
              val args = Bundle()
              args.putString(ARG_PARAM, type)
              listFrag.arguments = args
              return listFrag
          }
      }
  ```

- Fragment 가 열리고 firestore 로 부터 카테고리에 맞는 리스트를 가져오기 위해서 presenter 에 getStories 를 호출한다. 

  ```kotlin
  arguments
      ?.getString(ARG_PARAM)
      ?.let{
          mPresenter.getStores(it)
      }
  ```
- ListFrag 전체 소스
  
  ```kotlin
  class ListFrag : BaseMvpFragment<ListFragContract.View, ListFragContract.Presenter>(), ListFragContract.View {
  
      private var items : ArrayList<Store> = ArrayList()
  
      override var mPresenter: ListFragContract.Presenter = ListFragPresenter()
  
      lateinit var act : Activity
  
      private lateinit var listRecylerViewAdapter : ListRecylerViewAdapter
  
      companion object {
          private const val ARG_PARAM = "type"
  
          fun newInstance(type : String) : ListFrag {
              val listFrag = ListFrag()
              val args = Bundle()
              args.putString(ARG_PARAM, type)
              listFrag.arguments = args
              return listFrag
          }
      }
  
      override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
          return inflater.inflate(R.layout.frag_list, container, false)
      }
  
      override fun onActivityCreated(savedInstanceState: Bundle?) {
          super.onActivityCreated(savedInstanceState)
          act = activity as Activity
          initRecyclerView()
  
          arguments
              ?.getString(ARG_PARAM)
              ?.let{
                  mPresenter.getStores(it)
              }
      }
  
      private fun initRecyclerView() {
          listRecyclerView.layoutManager = LinearLayoutManager(context)
          listRecylerViewAdapter = ListRecylerViewAdapter(items, act, ::handleItem)
          listRecyclerView.adapter = listRecylerViewAdapter
          val itemDecoration = ItemOffsetDecoration(act, R.dimen.list_item_offset)
          listRecyclerView.addItemDecoration(itemDecoration)
          listRecyclerView.setEmptyView(empty_view)
      }
  
      fun handleItem(item: Store) {
  
      }
  
      override fun updateList(items: MutableList<Store>) {
          listRecylerViewAdapter.updateItem(items)
      }
  }
  ```


> RegisterActivity 생성
- Register 화면에서 사진 및 내용을 등록하는게 목표
- RegisterActivity 생성시 Contract, Presetenter 도 같이 생성
  
  ```kotlin
  class RegisterActivity : BaseMvpActivity<RegisterContract.View, RegisterContract.Presenter>(), RegisterContract.View{
  ```
- Presenter 생성 
  ```kotlin
  override var mPresenter: RegisterContract.Presenter = RegisterPresenter()
  ```
> 사진 및 데이터 `Firestore` 및 `Firestorage` 에 등록 흐름
1. 사진 등록 버튼 이벤트 등록
2. 피커 열기 (카메라, 갤러리) 후 완료후 이미지 뷰에 업데이트
3. RxImagePicker 이용해서 이미지 Uri 획득
4. 제목 입력 후 등록 버튼 클릭
5. 클릭과 동시 ProgressBar를 보여주기
6. Presenter에 register 함수 실행
7. ImageUpload 스트림 과 register 스트림을 조합
8. Repository에서 FirebaseRepositoy을 이용해서 리턴
9. FirebaseRepository 에서 RxFirestoreage 와 RxFirestore 를 이용해서 등록
10. Presenter에서 정상 입력 콜백 받아서 UI로 업데이트
11. Progressbar 종료 한 후 업데이트
12. 등록 완료 메세지 보여주고 다시 리스트 화면으로 이동 하기

> 사진 등록 버튼 이벤트 등록
 ```kotlin
    btnRegister.setOnClickListener {
        registerProc()
    }

    btnGallery.setOnClickListener {
        openImagePicker(Sources.GALLERY)
    }

    btnCamera.setOnClickListener {
        openImagePicker(Sources.CAMERA)
    }
 ```
> 피커 열기 (카메라, 갤러리) 후 완료후 이미지 뷰에 업데이트

- RxImagePicker 이용해서 이미지 Uri 획득
- 이미지 피커를 열어서 카메라 또는 갤러리에서 이미지를 선택하게끔 한다.
- `RxImagePicker` 를 이용해서 최종 선택된 Uri 를 가져온다. 
- `Glide` 은 서버, 로컬이미지를 안드로이드에 바인딩 하기 위한 라이버러리 중 하나이다. (추천) 
  
```kotlin
fun openImagePicker(source: Sources) {
    val disposable = RxImagePicker.with(fragmentManager).requestImage(source)
        .subscribeBy(
            onNext = {
                this.uri = it
                val options = RequestOptions()
                options.circleCrop()
                Glide.with(this)
                    .load(it)
                    .apply(options)
                    .into(thumbnail)
            }
        )

    compositeDisposable.add(disposable)
}
```
- compositeDisposable 는 disposable 을 정리하기 위한 disposeBag 이라고 생각하면 된다. 즉 subscribe 한 후에 구독한 자원을 클리어 하기 위해서 사용된다. 

```kotlin
compositeDisposable.clear() 
```

> 제목 입력 후 등록 버튼 클릭
- 클릭과 동시 ProgressBar를 보여주기
```kotlin
    fun registerProc() {
        btnRegister.visibility = View.GONE
        registerPogressBar.visibility = View.VISIBLE
        val store = Store(
            name=tv_title.text.toString(),
            categoryName = spinner.selectedItem.toString()
        )
        Timber.d("$store")
        mPresenter.register(uri = this.uri, store = store)
    }
```
> `Presenter` `register` 함수 실행
-  ImageUpload 스트림 과 register 스트림을 조합
    -  ImageUpload Stream -> Firebase storeage
        ```kotlin
            val imageObservable : Maybe<Uri>? = uri?.let{
                repository.uploadImage(uri)
            }
        ```
    -  Register Stream -> Firestore
        ```kotlin
            val registerObservable = imageObservable
            ?.map {
                store.apply {
                    this.thumbnail = it.toString()
                }
            }
            ?.flatMapCompletable(::registerProc)
            ?:registerProc(store)
        ```

- `regiser()` 전체 소스  
  
```kotlin
    override fun register(uri : Uri?, store: Store) {
        store.apply {
            id = getUUID()
        }

        val imageObservable : Maybe<Uri>? = uri?.let{
            repository.uploadImage(uri)
        }

        val registerObservable = imageObservable
            ?.map {
                store.apply {
                    this.thumbnail = it.toString()
                }
            }
            ?.flatMapCompletable(::registerProc)
            ?:registerProc(store)

        val disposable = registerObservable
            .subscribeBy(
                onComplete = {
                    mView?.registerDone()
                }
            )

        compositeDisposable.add(disposable)
    }

    fun registerProc(store : Store): Completable {
        return repository.register(store)
    }
```


> Repository에서 FirebaseRepositoy을 이용해서 리턴
- Image 등록
  ```kotlin
    fun uploadImage(uri: Uri): Maybe<Uri>? {
        return FirebaseRepository.uploadImage(uri)
    }
  ```
- Firestore 에 이미지 링크와 데이터 등록
  ```kotlin
    fun register(store: Store) : Completable {
        return FirebaseRepository.register(store)
    }
  ```
1.  FirebaseRepository 에서 RxFirestoreage 와 RxFirestore 를 이용해서 등록
- FirebaseRepository 에서 uploadImage 함수 구현
    ```kotlin
    override fun uploadImage(uri: Uri): Maybe<Uri>? {
        val ref = firebaseStorage.reference.child(getUUID())

        return RxFirebaseStorage.putFile(ref, uri)
            .flatMapMaybe {
                RxFirebaseStorage.getDownloadUrl(ref)
            }
    }
    ```
- Firebase firestore 에 데이터 입력
  ```kotlin
    override fun register(store: Store): Completable {
        val document = firestoreApp.collection(store.categoryName ?: "").document( store.id ?: throw Exception("Empty ID") )
        return RxFirestore.setDocument(document, store)
    }
  ```
1.  Presenter에서 정상 입력 콜백 받아서 UI로 업데이트
- 정상적으로 받아서 UI 에 등록 완료 푸시
```kotlin
    ...
    mView?.registerDone()
    ...    
```
1.  Progressbar 종료 한 후 업데이트
```
    override fun registerDone() {
        btnRegister.visibility = View.VISIBLE
        registerPogressBar.visibility = View.GONE
        Toast.makeText(this, "등록완료", Toast.LENGTH_SHORT).show()
        finish()
    }
```

> 주의 해야 할 부분

- Source 인터페이스에서 네트워크와 파베 리포지토리에 등록할 공통 함수 구현
```kotlin
interface Source {

    fun getConvertedAddr(lat : Double, lng : Double ) : Observable<Address>

    fun register(store: Store) : Completable

    fun uploadImage(uri: Uri) : Maybe<Uri>?

    fun getStores(type: String) : Maybe<MutableList<Store>>
}
```

> rxfirestore 및 rxfirebaseStorage 디펜시즈 추가
```gradle
    //RxFirebase
    implementation 'com.github.FrangSierra:RxFirebase:1.5.0'
    implementation 'com.oakwoodsc.rxfirestore:rxfirestore:1.1.0'
    implementation 'com.oakwoodsc.rxfirestore:rxfirestorekt:1.1.0'
```

- FirebaseRepository by Source
    - RxFirestore 이용해서 등록 후 `Completable` 형태로 리턴받음
    - RxFirebaseStorage 에 파일을 등록 하면 `Maybe` 로 리턴 받음
        - `Maybe` 는 http://reactivex.io/RxJava/javadoc/io/reactivex/Maybe.html 에서 자세히 볼수 있다. 
        - 값이 나올수도 있거나 `null` 이 올수 있다는 뜻이다. 
        - `Single` 과 `Maybe` 과 차이점은 `Single` 은 `null` 값을 받을 경우 에러로 리턴된다.   
        - `flatMapMaybe` 은 옵저버블을 리턴시 `Maybe` 로 리턴이 되는 경우이다.
        ```kotlin
        object FirebaseRepository : Source {
            val firestoreApp by lazy {
                FirebaseFirestore.getInstance()
            }

            val firebaseStorage by lazy {
                FirebaseStorage.getInstance("버킷 주소")
            }

            override fun register(store: Store): Completable {
                val document = firestoreApp.collection(store.categoryName ?: "").document( store.id ?: throw Exception("Empty ID") )
                return RxFirestore.setDocument(document, store)
            }

            override fun uploadImage(uri: Uri): Maybe<Uri>? {
                val ref = firebaseStorage.reference.child(getUUID())

                return RxFirebaseStorage.putFile(ref, uri)
                    .flatMapMaybe {
                        RxFirebaseStorage.getDownloadUrl(ref)
                    }
            }

            override fun getStores(type: String): Maybe<MutableList<Store>> {
                val collectionRef = firestoreApp.collection(type)
                return RxFirestore.getCollection(collectionRef, Store::class.java)
                    .doOnError {
                        Timber.e(it)
                    }
            }
        }
        ```

> Firebase storage에 버킷 주소 꼭 확인 후 맞는 걸 넣어줘야 함


> 업체 등록 후 List 화면에서 정상적인 업체가 나오는지 확인

- 이미 등록한 내용에 대해서 잘 나오는 지 firestore 콘솔에 가서 직접 확인 필요
  
  1. Presenter에서 getStores 를 호출
        ```kotlin
            override fun getStores(type: String) {
                val disposable = repository.getStores(type)
                    .subscribeBy(
                        onSuccess = {
                            Timber.d("list : $it")
                            mView?.updateList(it)
                        }
                    )

                compositeDisposable.add(disposable)
            }
        ```
  2. RepositoryImpl 에서 getStore 호출해서 firebase 에서 호출
        ```kotlin
            fun getStores(type: String) : Maybe<MutableList<Store>>{
                return FirebaseRepository.getStores(type)
            }
        ```
  3. FirebaseRepository 에서 RxFirestore 이용해서 데이터를 가져와서 리턴함
        ```kotlin
        override fun getStores(type: String): Maybe<MutableList<Store>> {
            val collectionRef = firestoreApp.collection(type)
            return RxFirestore.getCollection(collectionRef, Store::class.java)
        }
        ```
  4. Presenter 에서 UI 단으로 업데이트 처리
    
        ```kotlin
        ...
            mView?.updateList(it)
        ...
        ```
  5. ListFrag에서 adapter에서 리스트 처리
        ```kotlin
            override fun updateList(items: MutableList<Store>) {
                listRecylerViewAdapter.updateItem(items)
            }
        ```
  6. Adapter에서 notifyDataSetChanged() (갱신) 호출   
        ```kotlin
            fun updateItem(items : List<Store>) {
                this.items = items
                notifyDataSetChanged()
            }
        ```
  
이상으로 3주차 배달앱 클론 스터디 내용입니다. 

다음시간에는(마지막시간) 복습 후에 마지막 메인쪽 왼쪽 서랍 메뉴 커스텀과 삭제 기능 및 디테일을 좀 더 다를 예정입니다. 

