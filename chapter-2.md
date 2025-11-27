# Chapter 2: Intermediate Concepts

## Introduction
This chapter builds upon the fundamentals from Chapter 1, diving into intermediate Android development topics. You'll learn advanced UI patterns, data persistence, networking, background processing, and permission handling. These concepts are essential for building production-ready Android applications.

**Prerequisites:** Solid understanding of Activity/Fragment lifecycle, basic UI components, Kotlin syntax, and View Binding from Chapter 1.

---

## 2.1 Advanced UI/UX

Modern Android apps require polished, intuitive interfaces that follow Material Design guidelines. This section covers advanced UI patterns and components.

### Material Design

**Q1: What is Material Design?**

Material Design is Google's design language that provides guidelines for visual, motion, and interaction design across platforms. It emphasizes:
- Surfaces and shadows
- Bold colors and typography
- Meaningful motion
- Responsive layouts
- Consistent user experience

**Key principles:**
- Material as metaphor (inspired by paper and ink)
- Bold, graphic, intentional design
- Motion provides meaning

**Try This:** Test your permission flows in these scenarios:
- First time user
- User who denied once
- User who selected "Don't ask again"
- User who granted then revoked in settings

---

## Key Takeaways - Chapter 2

### Advanced UI/UX
✓ Use MaterialComponents for consistent Material Design
✓ Leverage CoordinatorLayout for complex scrolling behaviors
✓ Create custom views only when necessary
✓ Always use ViewPager2 instead of ViewPager
✓ Prefer DialogFragment over Dialog for proper lifecycle handling

### Data Storage
✓ Use Room instead of raw SQLite for databases
✓ Understand difference between internal and external storage
✓ SharedPreferences for simple key-value pairs (or prefer DataStore)
✓ ContentProviders only when sharing data between apps
✓ Always use coroutines for Room operations (never main thread)

### Networking
✓ Retrofit is the standard for REST APIs
✓ Use interceptors for authentication and logging
✓ Handle all error scenarios properly (network, HTTP, parsing)
✓ Choose appropriate JSON library (Gson, Moshi, Kotlinx Serialization)
✓ Enable network security configuration for production

### Background Processing
✓ Use WorkManager for guaranteed background work
✓ Foreground services require notifications (API 26+)
✓ Most background services are restricted (API 26+)
✓ Chain WorkRequests for sequential operations
✓ BroadcastReceivers limited in manifest (API 26+)
✓ Never use AsyncTask (deprecated)

### Permissions
✓ Request dangerous permissions at runtime (API 23+)
✓ Request permissions when needed, not at launch
✓ Always provide rationale before requesting
✓ Handle "Don't ask again" scenario
✓ Guide users to settings when needed
✓ Implement graceful degradation when denied

### Common Pitfalls to Avoid
❌ Not unregistering BroadcastReceivers (memory leaks)
❌ Running database operations on main thread
❌ Not handling all network error scenarios
❌ Requesting all permissions at app launch
❌ Not calling recycle() on TypedArray
❌ Using AsyncTask in new code
❌ Forgetting notification for foreground services
❌ Not providing rationale for permission requests
❌ Blocking cleartext traffic without configuration

### Performance Tips
✅ Use OkHttp cache for network responses
✅ Implement proper error retry logic
✅ Use WorkManager constraints to respect battery
✅ Batch network requests when possible
✅ Use Room transactions for multiple operations
✅ Profile network calls with logging interceptor
✅ Implement offline-first architecture where appropriate

### Try This - Practice Exercises
1. Build a complete CRUD app with Room database
2. Create a music player with foreground service
3. Implement a photo gallery with runtime permission handling
4. Build a weather app fetching data from API with Retrofit
5. Create a sync feature using WorkManager with constraints
6. Implement a custom view with touch gestures
7. Build a multi-screen app with bottom navigation
8. Create an offline-first app with local caching
9. Implement authentication flow with token refresh
10. Build a settings screen using SharedPreferences

---

## What's Next?

In **Chapter 3: Advanced Concepts**, we'll explore:
- Architecture patterns (MVVM, Clean Architecture, Repository pattern)
- Jetpack components (ViewModel, LiveData, Navigation, DataStore)
- Dependency Injection (Hilt, Dagger, Koin)
- Coroutines and Flow (advanced patterns)
- Testing (Unit tests, UI tests, TDD)

These advanced concepts will help you build maintainable, scalable, and testable Android applications. Make sure you're comfortable with the intermediate topics covered in this chapter before proceeding.

---

**Important Notes for API 21+:**
- Runtime permissions mandatory from API 23
- Background service restrictions from API 26
- Foreground services require notification from API 26
- Cleartext traffic blocked by default from API 28
- Scoped storage enforced from API 29
- Package visibility restrictions from API 30

---

*End of Chapter 2* Always follow Material Design guidelines for consistent, professional-looking apps. Use Material Components library instead of basic widgets.

---

**Q2: What is MaterialComponents and how do you use it?**

MaterialComponents is a library that provides Material Design implementations of common UI components with improved styling and functionality.

```kotlin
// build.gradle.kts
dependencies {
    implementation("com.google.android.material:material:1.11.0")
}

// Use Material theme in styles.xml
<style name="AppTheme" parent="Theme.Material3.Light">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorOnPrimary">@color/white</item>
    <item name="colorSecondary">@color/secondary</item>
</style>

// Material Button in XML
<com.google.android.material.button.MaterialButton
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Material Button"
    app:icon="@drawable/ic_check" />
```

**Common Material Components:**
- MaterialButton
- MaterialCardView
- TextInputLayout (with TextInputEditText)
- BottomNavigationView
- TabLayout
- FloatingActionButton (FAB)
- Chip, ChipGroup

---

**Q3: What is TextInputLayout and why use it over EditText?**

TextInputLayout is a Material Design wrapper around EditText that provides floating labels, error handling, character counters, and helper text.

```xml
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/emailLayout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Email"
    app:errorEnabled="true"
    app:counterEnabled="true"
    app:counterMaxLength="50"
    app:helperText="Enter your email address">
    
    <com.google.android.material.textfield.TextInputEditText
        android:id="@+id/emailInput"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textEmailAddress" />
</com.google.android.material.textfield.TextInputLayout>
```

```kotlin
// Show error
binding.emailLayout.error = "Invalid email format"

// Clear error
binding.emailLayout.error = null
```

**Best Practice:** Always use TextInputLayout for form inputs - it provides better UX with floating labels and built-in error display.

---

### CoordinatorLayout and Behaviors

**Q4: What is CoordinatorLayout?**

CoordinatorLayout is a powerful FrameLayout that coordinates animations and transitions between child views. It's the foundation for complex scrolling behaviors and Material Design patterns.

```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    
    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        
        <com.google.android.material.appbar.MaterialToolbar
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_scrollFlags="scroll|enterAlways" />
    </com.google.android.material.appbar.AppBarLayout>
    
    <androidx.recyclerview.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />
    
    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="16dp" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

**Key features:**
- Coordinates scrolling behaviors
- Anchors views to other views
- Manages FAB show/hide on scroll
- Enables collapsing toolbar effects

---

**Q5: What are scroll flags in AppBarLayout?**

Scroll flags control how the toolbar behaves when content scrolls:

- **scroll**: View scrolls off screen with content
- **enterAlways**: View appears immediately when scrolling down
- **enterAlwaysCollapsed**: View enters at minimum height, expands on full scroll
- **exitUntilCollapsed**: View collapses to minimum height before scrolling off
- **snap**: View snaps to fully visible or fully hidden

```xml
<com.google.android.material.appbar.CollapsingToolbarLayout
    android:layout_width="match_parent"
    android:layout_height="200dp"
    app:layout_scrollFlags="scroll|exitUntilCollapsed">
    
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scaleType="centerCrop"
        app:layout_collapseMode="parallax" />
    
    <androidx.appcompat.widget.Toolbar
        android:layout_height="?attr/actionBarSize"
        android:layout_width="match_parent"
        app:layout_collapseMode="pin" />
</com.google.android.material.appbar.CollapsingToolbarLayout>
```

**Try This:** Experiment with different scroll flag combinations to create engaging scrolling experiences.

---

### MotionLayout

**Q6: What is MotionLayout?**

MotionLayout is a layout type that helps you manage motion and widget animation in your app. It's a subclass of ConstraintLayout and builds upon its rich layout capabilities.

```xml
<androidx.constraintlayout.motion.widget.MotionLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layoutDescription="@xml/scene_01">
    
    <ImageView
        android:id="@+id/image"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:src="@drawable/image" />
</androidx.constraintlayout.motion.widget.MotionLayout>
```

```xml
<!-- res/xml/scene_01.xml -->
<MotionScene>
    <Transition
        app:constraintSetStart="@id/start"
        app:constraintSetEnd="@id/end"
        app:duration="1000">
        <OnSwipe
            app:touchAnchorId="@id/image"
            app:touchAnchorSide="right"
            app:dragDirection="dragRight" />
    </Transition>
    
    <ConstraintSet android:id="@+id/start">
        <Constraint android:id="@id/image"
            android:layout_width="100dp"
            android:layout_height="100dp"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent" />
    </ConstraintSet>
    
    <ConstraintSet android:id="@+id/end">
        <Constraint android:id="@id/image"
            android:layout_width="200dp"
            android:layout_height="200dp"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintBottom_toBottomOf="parent" />
    </ConstraintSet>
</MotionScene>
```

**Advantages:**
- Declarative animations in XML
- Interactive gesture-driven animations
- Smooth transitions between layouts
- Performance optimized

---

### Custom Views

**Q7: How do you create a custom View in Android?**

Custom views extend existing View classes or create completely new ones by extending View directly. Override drawing and measurement methods to customize behavior.

```kotlin
class CircleView @JvmOverloads constructor(
    context: Context,
    attrs: AttributeSet? = null,
    defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {
    
    private val paint = Paint().apply {
        color = Color.BLUE
        style = Paint.Style.FILL
        isAntiAlias = true
    }
    
    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)
        val radius = (width.coerceAtMost(height) / 2f) - 10f
        canvas.drawCircle(
            width / 2f,
            height / 2f,
            radius,
            paint
        )
    }
}
```

**Key methods to override:**
- `onDraw(canvas: Canvas)`: Drawing logic
- `onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int)`: Size calculation
- `onTouchEvent(event: MotionEvent)`: Touch handling
- `onSizeChanged()`: Called when view size changes

---

**Q8: How do you handle custom attributes for custom views?**

Define custom attributes in XML and retrieve them in the constructor using TypedArray.

```xml
<!-- res/values/attrs.xml -->
<resources>
    <declare-styleable name="CircleView">
        <attr name="circleColor" format="color" />
        <attr name="circleRadius" format="dimension" />
    </declare-styleable>
</resources>
```

```kotlin
class CircleView @JvmOverloads constructor(
    context: Context,
    attrs: AttributeSet? = null,
    defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr) {
    
    private var circleColor: Int
    private var circleRadius: Float
    
    init {
        context.theme.obtainStyledAttributes(
            attrs,
            R.styleable.CircleView,
            0, 0
        ).apply {
            try {
                circleColor = getColor(R.styleable.CircleView_circleColor, Color.BLUE)
                circleRadius = getDimension(R.styleable.CircleView_circleRadius, 50f)
            } finally {
                recycle()
            }
        }
    }
}
```

```xml
<!-- Usage -->
<com.example.CircleView
    android:layout_width="100dp"
    android:layout_height="100dp"
    app:circleColor="@color/red"
    app:circleRadius="40dp" />
```

**Common Pitfall:** Always call `recycle()` on TypedArray to avoid memory leaks.

---

### ViewPager and ViewPager2

**Q9: What is ViewPager2 and how is it different from ViewPager?**

ViewPager2 is the improved version of ViewPager, supporting both horizontal and vertical scrolling, RTL support, and using RecyclerView internally.

**ViewPager2 advantages:**
- Vertical scrolling support
- RTL (right-to-left) support
- Uses RecyclerView (better performance)
- Supports DiffUtil for efficient updates
- Easier to use with Fragments

```kotlin
class MyPagerAdapter(fragment: Fragment) : FragmentStateAdapter(fragment) {
    override fun getItemCount() = 3
    
    override fun createFragment(position: Int): Fragment {
        return when (position) {
            0 -> FirstFragment()
            1 -> SecondFragment()
            else -> ThirdFragment()
        }
    }
}

// In Fragment/Activity
binding.viewPager.adapter = MyPagerAdapter(this)
```

**Best Practice:** Always use ViewPager2 for new projects. ViewPager is in maintenance mode.

---

**Q10: How do you integrate TabLayout with ViewPager2?**

TabLayout provides material design tabs. Use TabLayoutMediator to connect it with ViewPager2.

```xml
<com.google.android.material.tabs.TabLayout
    android:id="@+id/tabLayout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />

<androidx.viewpager2.widget.ViewPager2
    android:id="@+id/viewPager"
    android:layout_width="match_parent"
    android:layout_height="0dp" />
```

```kotlin
binding.viewPager.adapter = MyPagerAdapter(this)

TabLayoutMediator(binding.tabLayout, binding.viewPager) { tab, position ->
    tab.text = when (position) {
        0 -> "Tab 1"
        1 -> "Tab 2"
        else -> "Tab 3"
    }
}.attach()
```

**Try This:** Add icons to tabs using `tab.setIcon(R.drawable.ic_icon)` for better visual communication.

---

### Navigation Components

**Q11: What is BottomNavigationView?**

BottomNavigationView provides a Material Design bottom navigation bar for switching between top-level destinations in your app.

```xml
<com.google.android.material.bottomnavigation.BottomNavigationView
    android:id="@+id/bottomNav"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:menu="@menu/bottom_nav_menu" />
```

```xml
<!-- res/menu/bottom_nav_menu.xml -->
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/nav_home"
        android:icon="@drawable/ic_home"
        android:title="Home" />
    <item
        android:id="@+id/nav_search"
        android:icon="@drawable/ic_search"
        android:title="Search" />
    <item
        android:id="@+id/nav_profile"
        android:icon="@drawable/ic_profile"
        android:title="Profile" />
</menu>
```

```kotlin
binding.bottomNav.setOnItemSelectedListener { item ->
    when (item.itemId) {
        R.id.nav_home -> {
            // Navigate to home
            true
        }
        R.id.nav_search -> {
            // Navigate to search
            true
        }
        R.id.nav_profile -> {
            // Navigate to profile
            true
        }
        else -> false
    }
}
```

**Material Design guideline:** Use 3-5 destinations in bottom navigation. For more, use NavigationDrawer.

---

**Q12: What is Toolbar and ActionBar?**

**ActionBar** is the legacy app bar at the top of activities. **Toolbar** is the flexible, modern replacement that can be placed anywhere in the layout.

```xml
<androidx.appcompat.widget.Toolbar
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:elevation="4dp"
    app:title="My App"
    app:titleTextColor="@color/white" />
```

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(binding.root)
        
        setSupportActionBar(binding.toolbar)
        supportActionBar?.apply {
            setDisplayHomeAsUpEnabled(true)
            title = "Custom Title"
        }
    }
    
    override fun onCreateOptionsMenu(menu: Menu): Boolean {
        menuInflater.inflate(R.menu.main_menu, menu)
        return true
    }
    
    override fun onOptionsItemSelected(item: MenuItem): Boolean {
        return when (item.itemId) {
            R.id.action_settings -> {
                // Handle settings
                true
            }
            android.R.id.home -> {
                onBackPressedDispatcher.onBackPressed()
                true
            }
            else -> super.onOptionsItemSelected(item)
        }
    }
}
```

**Best Practice:** Use MaterialToolbar from Material Components instead of AppCompat Toolbar for better Material Design compliance.

---

### Dialogs

**Q13: What is DialogFragment and why use it instead of Dialog?**

DialogFragment is a Fragment that displays a dialog window. It properly handles lifecycle events and configuration changes, unlike traditional Dialog.

```kotlin
class MyDialogFragment : DialogFragment() {
    
    override fun onCreateDialog(savedInstanceState: Bundle?): Dialog {
        return AlertDialog.Builder(requireContext())
            .setTitle("Confirm Action")
            .setMessage("Are you sure?")
            .setPositiveButton("Yes") { _, _ ->
                // Handle positive action
            }
            .setNegativeButton("No", null)
            .create()
    }
}

// Show dialog
MyDialogFragment().show(supportFragmentManager, "dialog")
```

**Advantages over Dialog:**
- Survives configuration changes
- Proper lifecycle management
- Back stack support
- Fragment transaction support

---

**Q14: How do you create a custom DialogFragment with custom layout?**

Override `onCreateView()` for full control over dialog appearance.

```kotlin
class CustomDialogFragment : DialogFragment() {
    
    private var _binding: DialogCustomBinding? = null
    private val binding get() = _binding!!
    
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = DialogCustomBinding.inflate(inflater, container, false)
        return binding.root
    }
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        
        binding.buttonConfirm.setOnClickListener {
            // Handle confirmation
            dismiss()
        }
        
        binding.buttonCancel.setOnClickListener {
            dismiss()
        }
    }
    
    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

**Common Pitfall:** Not nulling the binding in `onDestroyView()` causes memory leaks. Always clean up view references.

---

**Q15: What's the difference between Snackbar and Toast (revisited with advanced usage)?**

Building on Chapter 1, Snackbar offers more advanced features:

```kotlin
Snackbar.make(view, "Message", Snackbar.LENGTH_LONG)
    .setAction("Action") {
        // Handle action
    }
    .setActionTextColor(ContextCompat.getColor(context, R.color.accent))
    .setBackgroundTint(ContextCompat.getColor(context, R.color.primary))
    .setTextColor(Color.WHITE)
    .setAnchorView(binding.fab)  // Positions above FAB
    .addCallback(object : Snackbar.Callback() {
        override fun onDismissed(transientBottomBar: Snackbar?, event: Int) {
            // Handle dismissal
        }
    })
    .show()
```

**Try This:** Use Snackbar with `setAnchorView()` to prevent it from covering important UI elements like FABs.

---

## 2.2 Data Storage

Android provides multiple ways to store data persistently. Choosing the right storage mechanism depends on data type, size, and access patterns.

### SharedPreferences

**Q16: What is SharedPreferences and when should you use it?**

SharedPreferences stores small amounts of primitive data in key-value pairs. It's ideal for user settings, preferences, and simple app state.

```kotlin
// Write data
val sharedPref = getSharedPreferences("MyPrefs", Context.MODE_PRIVATE)
sharedPref.edit {
    putString("username", "John")
    putInt("age", 25)
    putBoolean("isLoggedIn", true)
}

// Read data
val username = sharedPref.getString("username", "")
val age = sharedPref.getInt("age", 0)
val isLoggedIn = sharedPref.getBoolean("isLoggedIn", false)

// Remove data
sharedPref.edit {
    remove("username")
}

// Clear all
sharedPref.edit {
    clear()
}
```

**Limitations:**
- Only stores primitives and strings
- Not suitable for large datasets
- Not type-safe
- No encryption by default

**Best Practice:** For new projects, prefer DataStore (covered in Chapter 3) over SharedPreferences.

---

**Q17: How do you make SharedPreferences operations more concise?**

Use Kotlin extension functions and property delegation:

```kotlin
// Extension function
inline fun SharedPreferences.edit(
    commit: Boolean = false,
    action: SharedPreferences.Editor.() -> Unit
) {
    val editor = edit()
    action(editor)
    if (commit) editor.commit() else editor.apply()
}

// Property delegate
class PreferenceDelegate<T>(
    private val key: String,
    private val defaultValue: T
) : ReadWriteProperty<Any?, T> {
    
    override fun getValue(thisRef: Any?, property: KProperty<*>): T {
        val prefs = // get SharedPreferences
        return when (defaultValue) {
            is String -> prefs.getString(key, defaultValue) as T
            is Int -> prefs.getInt(key, defaultValue) as T
            is Boolean -> prefs.getBoolean(key, defaultValue) as T
            else -> throw IllegalArgumentException()
        }
    }
    
    override fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {
        // Similar implementation for setting
    }
}

// Usage
var username: String by PreferenceDelegate("username", "")
```

---

### File Storage

**Q18: What's the difference between internal and external storage?**

**Internal Storage:**
- Private to your app
- Always available
- Deleted when app is uninstalled
- Located in `/data/data/package_name/`

**External Storage:**
- Can be removable (SD card)
- May not always be available
- Publicly accessible (with permissions)
- Survives app uninstall

```kotlin
// Internal storage
val file = File(context.filesDir, "myfile.txt")
context.openFileOutput("myfile.txt", Context.MODE_PRIVATE).use {
    it.write("content".toByteArray())
}

val content = context.openFileInput("myfile.txt").bufferedReader().use {
    it.readText()
}

// External storage (requires permission)
if (Environment.getExternalStorageState() == Environment.MEDIA_MOUNTED) {
    val file = File(context.getExternalFilesDir(null), "myfile.txt")
    file.writeText("content")
}
```

**Important (API 21+):** From Android 10 (API 29), scoped storage limits access to external storage. Use app-specific directories or request special permissions.

---

**Q19: How do you perform file operations in Android?**

```kotlin
// Write file
fun writeToFile(context: Context, filename: String, content: String) {
    context.openFileOutput(filename, Context.MODE_PRIVATE).use {
        it.write(content.toByteArray())
    }
}

// Read file
fun readFromFile(context: Context, filename: String): String {
    return context.openFileInput(filename).bufferedReader().use {
        it.readText()
    }
}

// Delete file
fun deleteFile(context: Context, filename: String): Boolean {
    return context.deleteFile(filename)
}

// List files
fun listFiles(context: Context): Array<String> {
    return context.fileList()
}

// Check if file exists
fun fileExists(context: Context, filename: String): Boolean {
    return File(context.filesDir, filename).exists()
}
```

**Try This:** Use Kotlin's `use` function for automatic resource management - it ensures streams are properly closed.

---

### Room Database Basics

**Q20: What is Room and why use it instead of SQLite directly?**

Room is an abstraction layer over SQLite that provides:
- Compile-time verification of SQL queries
- Less boilerplate code
- Easy integration with LiveData and Flow
- Simplified database migrations

**Architecture components:**
- **Entity**: Represents a table
- **DAO** (Data Access Object): Defines database operations
- **Database**: Holds the database and serves as main access point

```kotlin
// build.gradle.kts
dependencies {
    implementation("androidx.room:room-runtime:2.6.1")
    implementation("androidx.room:room-ktx:2.6.1")
    ksp("androidx.room:room-compiler:2.6.1")
}
```

---

**Q21: How do you create a Room database?**

```kotlin
// Entity
@Entity(tableName = "users")
data class User(
    @PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    @ColumnInfo(name = "user_name")
    val name: String,
    val email: String,
    val age: Int
)

// DAO
@Dao
interface UserDao {
    @Insert
    suspend fun insert(user: User)
    
    @Update
    suspend fun update(user: User)
    
    @Delete
    suspend fun delete(user: User)
    
    @Query("SELECT * FROM users")
    fun getAllUsers(): List<User>
    
    @Query("SELECT * FROM users WHERE id = :userId")
    suspend fun getUserById(userId: Int): User?
    
    @Query("DELETE FROM users")
    suspend fun deleteAll()
}

// Database
@Database(entities = [User::class], version = 1, exportSchema = false)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
    
    companion object {
        @Volatile
        private var INSTANCE: AppDatabase? = null
        
        fun getDatabase(context: Context): AppDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    AppDatabase::class.java,
                    "app_database"
                ).build()
                INSTANCE = instance
                instance
            }
        }
    }
}
```

**Common Pitfall:** Room operations must not run on the main thread. Use coroutines (suspend functions) or RxJava.

---

**Q22: What are the common Room annotations?**

**Entity annotations:**
- `@Entity`: Marks a class as database table
- `@PrimaryKey`: Marks primary key field
- `@ColumnInfo`: Customizes column name
- `@Ignore`: Excludes field from database
- `@Embedded`: Nests fields of a class
- `@ForeignKey`: Defines foreign key relationship

**DAO annotations:**
- `@Insert`: Insert operation
- `@Update`: Update operation
- `@Delete`: Delete operation
- `@Query`: Custom SQL query

```kotlin
@Entity(
    tableName = "orders",
    foreignKeys = [ForeignKey(
        entity = User::class,
        parentColumns = ["id"],
        childColumns = ["user_id"],
        onDelete = ForeignKey.CASCADE
    )],
    indices = [Index(value = ["user_id"])]
)
data class Order(
    @PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    @ColumnInfo(name = "user_id")
    val userId: Int,
    val amount: Double,
    @Ignore
    val tempData: String = ""
)
```

---

### ContentProviders

**Q23: What is a ContentProvider?**

ContentProvider manages access to structured data, enabling data sharing between applications. It acts as an interface between your app's data and external apps.

**Common system ContentProviders:**
- Contacts
- Calendar
- Media Store
- Settings

```kotlin
// Query contacts (requires READ_CONTACTS permission)
val cursor = contentResolver.query(
    ContactsContract.Contacts.CONTENT_URI,
    null,
    null,
    null,
    null
)

cursor?.use {
    while (it.moveToNext()) {
        val name = it.getString(
            it.getColumnIndexOrThrow(ContactsContract.Contacts.DISPLAY_NAME)
        )
        println(name)
    }
}
```

**Note:** Creating custom ContentProviders is less common in modern apps. Use them only when you need to share data with other apps.

---

**Q24: What's the difference between SQLite and Room?**

**SQLite (direct):**
- Manual database operations
- String-based queries (error-prone)
- No compile-time verification
- More boilerplate code
- Manual cursor management

**Room:**
- Compile-time SQL verification
- Type-safe queries
- Less boilerplate
- Automatic cursor management
- LiveData/Flow integration
- Easier migrations

```kotlin
// SQLite - verbose and error-prone
val db = dbHelper.writableDatabase
val values = ContentValues().apply {
    put("name", "John")
    put("age", 25)
}
db.insert("users", null, values)

// Room - concise and type-safe
userDao.insert(User(name = "John", age = 25))
```

**Best Practice:** Always use Room for database operations. Direct SQLite usage is only needed for very specific edge cases.

---

## 2.3 Networking

Modern Android apps communicate with backend servers using REST APIs. Retrofit is the industry standard for networking in Android.

### REST API Basics

**Q25: What is REST and what are HTTP methods?**

REST (Representational State Transfer) is an architectural style for distributed systems. Common HTTP methods:

- **GET**: Retrieve data
- **POST**: Create new resource
- **PUT**: Update entire resource
- **PATCH**: Partial update
- **DELETE**: Delete resource

**Status codes:**
- **2xx**: Success (200 OK, 201 Created)
- **3xx**: Redirection
- **4xx**: Client error (400 Bad Request, 404 Not Found, 401 Unauthorized)
- **5xx**: Server error

---

### Retrofit

**Q26: What is Retrofit and how do you set it up?**

Retrofit is a type-safe HTTP client that converts your HTTP API into a Java/Kotlin interface.

```kotlin
// build.gradle.kts
dependencies {
    implementation("com.squareup.retrofit2:retrofit:2.9.0")
    implementation("com.squareup.retrofit2:converter-gson:2.9.0")
    implementation("com.squareup.okhttp3:logging-interceptor:4.12.0")
}

// AndroidManifest.xml
<uses-permission android:name="android.permission.INTERNET" />
```

```kotlin
// Data model
data class User(
    val id: Int,
    val name: String,
    val email: String
)

// API interface
interface ApiService {
    @GET("users")
    suspend fun getUsers(): List<User>
    
    @GET("users/{id}")
    suspend fun getUserById(@Path("id") userId: Int): User
    
    @POST("users")
    suspend fun createUser(@Body user: User): User
    
    @PUT("users/{id}")
    suspend fun updateUser(@Path("id") id: Int, @Body user: User): User
    
    @DELETE("users/{id}")
    suspend fun deleteUser(@Path("id") id: Int): Response<Unit>
    
    @GET("users")
    suspend fun searchUsers(@Query("name") name: String): List<User>
}

// Retrofit instance
object RetrofitClient {
    private const val BASE_URL = "https://api.example.com/"
    
    private val loggingInterceptor = HttpLoggingInterceptor().apply {
        level = HttpLoggingInterceptor.Level.BODY
    }
    
    private val okHttpClient = OkHttpClient.Builder()
        .addInterceptor(loggingInterceptor)
        .connectTimeout(30, TimeUnit.SECONDS)
        .readTimeout(30, TimeUnit.SECONDS)
        .build()
    
    val apiService: ApiService by lazy {
        Retrofit.Builder()
            .baseUrl(BASE_URL)
            .client(okHttpClient)
            .addConverterFactory(GsonConverterFactory.create())
            .build()
            .create(ApiService::class.java)
    }
}

// Usage with coroutines
lifecycleScope.launch {
    try {
        val users = RetrofitClient.apiService.getUsers()
        // Update UI with users
    } catch (e: Exception) {
        // Handle error
    }
}
```

**Best Practice:** Create a singleton Retrofit instance to avoid creating multiple instances throughout your app.

---

**Q27: What are Retrofit annotations and when to use each?**

**HTTP Method annotations:**
- `@GET`: Retrieve data
- `@POST`: Submit data
- `@PUT`: Update resource (full replacement)
- `@PATCH`: Partial update
- `@DELETE`: Delete resource

**Parameter annotations:**
- `@Path`: Replace placeholder in URL path
- `@Query`: Add query parameter to URL
- `@QueryMap`: Add multiple query parameters
- `@Body`: Send object in request body
- `@Field`: Form-encoded field (requires `@FormUrlEncoded`)
- `@Header`: Add header to request
- `@Headers`: Add multiple headers

```kotlin
interface ApiService {
    // Path parameter: /users/123
    @GET("users/{id}")
    suspend fun getUser(@Path("id") userId: Int): User
    
    // Query parameter: /users?page=1&limit=20
    @GET("users")
    suspend fun getUsers(
        @Query("page") page: Int,
        @Query("limit") limit: Int
    ): List<User>
    
    // Query map: /search?q=kotlin&type=repo&sort=stars
    @GET("search")
    suspend fun search(@QueryMap params: Map<String, String>): SearchResult
    
    // Headers
    @Headers("Accept: application/json")
    @GET("users")
    suspend fun getUsersWithHeader(): List<User>
    
    // Dynamic header
    @GET("users")
    suspend fun getUsers(@Header("Authorization") token: String): List<User>
    
    // Form data
    @FormUrlEncoded
    @POST("login")
    suspend fun login(
        @Field("username") username: String,
        @Field("password") password: String
    ): LoginResponse
}
```

---

### OkHttp

**Q28: What is OkHttp and how does it relate to Retrofit?**

OkHttp is the HTTP client that Retrofit uses internally. It handles the actual HTTP communication. You can customize OkHttp for:
- Interceptors (logging, authentication)
- Timeouts
- Cache configuration
- SSL/TLS settings

```kotlin
val okHttpClient = OkHttpClient.Builder()
    // Logging interceptor
    .addInterceptor(HttpLoggingInterceptor().apply {
        level = HttpLoggingInterceptor.Level.BODY
    })
    // Authentication interceptor
    .addInterceptor { chain ->
        val request = chain.request().newBuilder()
            .addHeader("Authorization", "Bearer $token")
            .build()
        chain.proceed(request)
    }
    // Timeouts
    .connectTimeout(30, TimeUnit.SECONDS)
    .readTimeout(30, TimeUnit.SECONDS)
    .writeTimeout(30, TimeUnit.SECONDS)
    // Cache
    .cache(Cache(context.cacheDir, 10 * 1024 * 1024)) // 10 MB
    .build()
```

**Try This:** Use interceptors to add authentication tokens automatically to all requests instead of passing them manually.

---

**Q29: How do you handle authentication with Retrofit?**

```kotlin
// Auth interceptor
class AuthInterceptor(private val tokenProvider: () -> String?) : Interceptor {
    override fun intercept(chain: Interceptor.Chain): Response {
        val token = tokenProvider()
        val request = if (token != null) {
            chain.request().newBuilder()
                .addHeader("Authorization", "Bearer $token")
                .build()
        } else {
            chain.request()
        }
        return chain.proceed(request)
    }
}

// Add to OkHttp
val okHttpClient = OkHttpClient.Builder()
    .addInterceptor(AuthInterceptor { 
        // Get token from SharedPreferences or secure storage
        getAuthToken()
    })
    .build()

// Authenticator for token refresh
class TokenAuthenticator(
    private val tokenRefreshService: TokenRefreshService
) : Authenticator {
    override fun authenticate(route: Route?, response: Response): Request? {
        // If we've already tried to refresh, give up
        if (response.request.header("Authorization") != null) {
            return null
        }
        
        // Refresh token
        val newToken = refreshToken()
        return response.request.newBuilder()
            .header("Authorization", "Bearer $newToken")
            .build()
    }
}
```

---

### JSON Parsing

**Q30: What are the common JSON parsing libraries in Android?**

**Gson** (Google's library):
```kotlin
dependencies {
    implementation("com.squareup.retrofit2:converter-gson:2.9.0")
}

// Custom Gson configuration
val gson = GsonBuilder()
    .setDateFormat("yyyy-MM-dd'T'HH:mm:ss")
    .serializeNulls()
    .create()

val retrofit = Retrofit.Builder()
    .addConverterFactory(GsonConverterFactory.create(gson))
    .build()
```

**Moshi** (Modern alternative):
```kotlin
dependencies {
    implementation("com.squareup.retrofit2:converter-moshi:2.9.0")
    implementation("com.squareup.moshi:moshi-kotlin:1.15.0")
    ksp("com.squareup.moshi:moshi-kotlin-codegen:1.15.0")
}

val moshi = Moshi.Builder()
    .add(KotlinJsonAdapterFactory())
    .build()

val retrofit = Retrofit.Builder()
    .addConverterFactory(MoshiConverterFactory.create(moshi))
    .build()
```

**Kotlinx Serialization** (Kotlin-first):
```kotlin
dependencies {
    implementation("com.jakewharton.retrofit:retrofit2-kotlinx-serialization-converter:1.0.0")
    implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.6.0")
}

@Serializable
data class User(val id: Int, val name: String)

val json = Json { ignoreUnknownKeys = true }

val retrofit = Retrofit.Builder()
    .addConverterFactory(json.asConverterFactory("application/json".toMediaType()))
    .build()
```

**Best Practice:** Moshi and Kotlinx Serialization are more Kotlin-friendly than Gson. Choose based on your project needs.

---

**Q31: How do you handle different JSON field names?**

```kotlin
// Gson
data class User(
    @SerializedName("user_id")
    val id: Int,
    @SerializedName("user_name")
    val name: String
)

// Moshi
data class User(
    @Json(name = "user_id")
    val id: Int,
    @Json(name = "user_name")
    val name: String
)

// Kotlinx Serialization
@Serializable
data class User(
    @SerialName("user_id")
    val id: Int,
    @SerialName("user_name")
    val name: String
)
```

---

### Error Handling

**Q32: How do you handle network errors in Retrofit?**

```kotlin
// Using sealed class for result
sealed class Result<out T> {
    data class Success<T>(val data: T) : Result<T>()
    data class Error(val exception: Throwable) : Result<Nothing>()
    object Loading : Result<Nothing>()
}

// In ViewModel or Repository
suspend fun getUsers(): Result<List<User>> {
    return try {
        val users = apiService.getUsers()
        Result.Success(users)
    } catch (e: IOException) {
        Result.Error(e)
    } catch (e: HttpException) {
        Result.Error(e)
    }
}

// In Activity/Fragment
lifecycleScope.launch {
    when (val result = viewModel.getUsers()) {
        is Result.Success -> {
            // Update UI with result.data
        }
        is Result.Error -> {
            when (result.exception) {
                is IOException -> {
                    // Network error
                    showError("Network error. Check your connection.")
                }
                is HttpException -> {
                    // HTTP error
                    val code = result.exception.code()
                    showError("Error: $code")
                }
            }
        }
        is Result.Loading -> {
            // Show loading
        }
    }
}
```

**Common exceptions:**
- `IOException`: Network connectivity issues
- `HttpException`: HTTP errors (4xx, 5xx)
- `JsonSyntaxException`: JSON parsing errors

---

**Q33: How do you handle API responses with Response wrapper?**

```kotlin
// API returns: { "success": true, "data": [...], "message": "" }
data class ApiResponse<T>(
    val success: Boolean,
    val data: T?,
    val message: String?
)

interface ApiService {
    @GET("users")
    suspend fun getUsers(): ApiResponse<List<User>>
}

// Usage
suspend fun fetchUsers(): Result<List<User>> {
    return try {
        val response = apiService.getUsers()
        if (response.success && response.data != null) {
            Result.Success(response.data)
        } else {
            Result.Error(Exception(response.message ?: "Unknown error"))
        }
    } catch (e: Exception) {
        Result.Error(e)
    }
}
```

---

### Network Security

**Q34: What is Network Security Configuration?**

Network Security Configuration allows you to customize your app's network security settings in a declarative configuration file instead of code.

```xml
<!-- res/xml/network_security_config.xml -->
<network-security-config>
    <!-- Allow cleartext (HTTP) traffic only for debug builds -->
    <base-config cleartextTrafficPermitted="false" />
    
    <!-- Domain-specific config -->
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">10.0.2.2</domain> <!-- Emulator localhost -->
        <domain includeSubdomains="true">localhost</domain>
    </domain-config>
    
    <!-- Certificate pinning -->
    <domain-config>
        <domain includeSubdomains="true">example.com</domain>
        <pin-set expiration="2025-12-31">
            <pin digest="SHA-256">base64EncodedPublicKey==</pin>
            <pin digest="SHA-256">backupPublicKey==</pin>
        </pin-set>
    </domain-config>
</network-security-config>
```

```xml
<!-- AndroidManifest.xml -->
<application
    android:networkSecurityConfig="@xml/network_security_config">
```

**Important (API 23+):** Since Android 6.0, cleartext (HTTP) traffic is discouraged. From Android 9.0 (API 28), it's blocked by default.

---

## 2.4 Background Processing

Android has strict rules about background work to preserve battery life and system resources. Understanding these constraints is crucial.

### Services

**Q35: What are the types of Services in Android?**

**Foreground Service:**
- Performs operations noticeable to user
- Must display a notification
- Can continue running even when app is not in foreground
- Example: Music player, fitness tracker

**Background Service:**
- Performs operations not directly noticed by user
- Severely restricted since Android 8.0 (API 26)
- Better alternatives: WorkManager

**Bound Service:**
- Provides client-server interface
- Runs only while bound to a component
- Multiple components can bind to it

```kotlin
// Foreground Service
class MusicService : Service() {
    
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        // Create notification
        val notification = createNotification()
        
        // Start foreground service
        startForeground(NOTIFICATION_ID, notification)
        
        // Do work
        
        return START_STICKY
    }
    
    private fun createNotification(): Notification {
        val notificationChannelId = "music_channel"
        
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel(
                notificationChannelId,
                "Music Playback",
                NotificationManager.IMPORTANCE_LOW
            )
            val manager = getSystemService(NotificationManager::class.java)
            manager.createNotificationChannel(channel)
        }
        
        return NotificationCompat.Builder(this, notificationChannelId)
            .setContentTitle("Playing Music")
            .setContentText("Song name")
            .setSmallIcon(R.drawable.ic_music)
            .build()
    }
    
    override fun onBind(intent: Intent?): IBinder? = null
}
```

```xml
<!-- AndroidManifest.xml -->
<service android:name=".MusicService"
    android:foregroundServiceType="mediaPlayback" />

<!-- API 28+ requires foreground service permission -->
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
```

**Important (API 26+):** Since Android 8.0, background services have strict limitations. Use WorkManager instead.

---

**Q36: What are the return values for onStartCommand?**

- **START_NOT_STICKY**: If killed, don't recreate unless there are pending intents
- **START_STICKY**: If killed, recreate service and call `onStartCommand()` with null intent
- **START_REDELIVER_INTENT**: If killed, recreate service and redeliver last intent

```kotlin
override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
    // Do work
    
    return when {
        // One-time task
        intent?.action == "ONE_TIME" -> START_NOT_STICKY
        
        // Continuous service (music player)
        intent?.action == "MUSIC" -> START_STICKY
        
        // Must complete intent processing
        intent?.action == "UPLOAD" -> START_REDELIVER_INTENT
        
        else -> START_NOT_STICKY
    }
}
```

---

### WorkManager

**Q37: What is WorkManager and when should you use it?**

WorkManager is the recommended solution for deferrable, guaranteed background work. It chooses the best underlying implementation (JobScheduler, AlarmManager, etc.) based on API level and conditions.

**Use cases:**
- Upload/download files
- Sync data with server
- Process images
- Send logs
- Database cleanup

**Guarantees:**
- Work will execute even if app is killed
- Work survives device reboot
- Respects system constraints (network, battery, storage)

```kotlin
// build.gradle.kts
dependencies {
    implementation("androidx.work:work-runtime-ktx:2.9.0")
}
```

```kotlin
// Worker class
class UploadWorker(
    context: Context,
    params: WorkerParameters
) : CoroutineWorker(context, params) {
    
    override suspend fun doWork(): Result {
        return try {
            // Get input data
            val imageUri = inputData.getString("image_uri")
            
            // Do work
            uploadImage(imageUri)
            
            // Return success
            Result.success()
        } catch (e: Exception) {
            // Retry or fail
            if (runAttemptCount < 3) {
                Result.retry()
            } else {
                Result.failure()
            }
        }
    }
    
    private suspend fun uploadImage(uri: String?) {
        // Upload logic
    }
}

// Schedule work
val uploadWorkRequest = OneTimeWorkRequestBuilder<UploadWorker>()
    .setInputData(workDataOf("image_uri" to uri.toString()))
    .setConstraints(
        Constraints.Builder()
            .setRequiredNetworkType(NetworkType.CONNECTED)
            .setRequiresBatteryNotLow(true)
            .build()
    )
    .setBackoffCriteria(
        BackoffPolicy.EXPONENTIAL,
        WorkRequest.MIN_BACKOFF_MILLIS,
        TimeUnit.MILLISECONDS
    )
    .build()

WorkManager.getInstance(context).enqueue(uploadWorkRequest)
```

---

**Q38: What are the types of WorkRequests?**

**OneTimeWorkRequest:**
- Executes once
- Can be chained with other work

**PeriodicWorkRequest:**
- Executes repeatedly
- Minimum interval: 15 minutes
- Cannot be chained

```kotlin
// One-time work
val oneTimeWork = OneTimeWorkRequestBuilder<MyWorker>()
    .build()

// Periodic work (every 6 hours)
val periodicWork = PeriodicWorkRequestBuilder<MyWorker>(
    6, TimeUnit.HOURS
).build()

// Periodic with flex interval (runs once in last 15 minutes of 1 hour window)
val flexPeriodicWork = PeriodicWorkRequestBuilder<MyWorker>(
    1, TimeUnit.HOURS,
    15, TimeUnit.MINUTES
).build()

WorkManager.getInstance(context).enqueue(oneTimeWork)
```

---

**Q39: How do you chain work requests?**

```kotlin
val cleanupWork = OneTimeWorkRequestBuilder<CleanupWorker>().build()
val uploadWork = OneTimeWorkRequestBuilder<UploadWorker>().build()
val notifyWork = OneTimeWorkRequestBuilder<NotifyWorker>().build()

// Sequential chain
WorkManager.getInstance(context)
    .beginWith(cleanupWork)
    .then(uploadWork)
    .then(notifyWork)
    .enqueue()

// Parallel work that joins
val work1 = OneTimeWorkRequestBuilder<Worker1>().build()
val work2 = OneTimeWorkRequestBuilder<Worker2>().build()
val work3 = OneTimeWorkRequestBuilder<Worker3>().build()

WorkManager.getInstance(context)
    .beginWith(listOf(work1, work2))  // Runs in parallel
    .then(work3)                       // Runs after both complete
    .enqueue()
```

---

**Q40: How do you observe work status?**

```kotlin
// Observe work by ID
WorkManager.getInstance(context)
    .getWorkInfoByIdLiveData(uploadWorkRequest.id)
    .observe(lifecycleOwner) { workInfo ->
        when (workInfo?.state) {
            WorkInfo.State.ENQUEUED -> {
                // Work is queued
            }
            WorkInfo.State.RUNNING -> {
                // Work is running
                val progress = workInfo.progress
                val value = progress.getInt("progress", 0)
            }
            WorkInfo.State.SUCCEEDED -> {
                // Work completed successfully
                val result = workInfo.outputData
            }
            WorkInfo.State.FAILED -> {
                // Work failed
            }
            WorkInfo.State.CANCELLED -> {
                // Work was cancelled
            }
            else -> {}
        }
    }

// Update progress from Worker
class UploadWorker : CoroutineWorker() {
    override suspend fun doWork(): Result {
        setProgress(workDataOf("progress" to 0))
        
        // Do work...
        setProgress(workDataOf("progress" to 50))
        
        // More work...
        setProgress(workDataOf("progress" to 100))
        
        return Result.success()
    }
}
```

---

### BroadcastReceiver

**Q41: What is a BroadcastReceiver?**

BroadcastReceiver responds to system-wide broadcast announcements. It allows your app to respond to events from the system or other apps.

**Common system broadcasts:**
- BOOT_COMPLETED
- BATTERY_LOW
- CONNECTIVITY_CHANGE
- AIRPLANE_MODE
- SMS_RECEIVED

```kotlin
class MyReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        when (intent.action) {
            Intent.ACTION_BOOT_COMPLETED -> {
                // Device booted
            }
            Intent.ACTION_BATTERY_LOW -> {
                // Battery is low
            }
        }
    }
}
```

```xml
<!-- Static registration in AndroidManifest.xml -->
<receiver android:name=".MyReceiver"
    android:exported="false">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>

<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
```

```kotlin
// Dynamic registration
val receiver = MyReceiver()
val filter = IntentFilter().apply {
    addAction(Intent.ACTION_BATTERY_LOW)
}
registerReceiver(receiver, filter)

// Don't forget to unregister
override fun onDestroy() {
    super.onDestroy()
    unregisterReceiver(receiver)
}
```

**Important (API 26+):** Since Android 8.0, most implicit broadcasts cannot be declared in manifest. Use dynamic registration or WorkManager.

---

**Q42: What's the difference between static and dynamic BroadcastReceiver registration?**

**Static (Manifest) Registration:**
- Receiver exists even if app is not running
- Responds to broadcasts when app is killed
- Limited broadcasts allowed (API 26+)

**Dynamic (Runtime) Registration:**
- Receiver only active while component is alive
- More flexible
- No restrictions on broadcast types
- Must unregister to avoid leaks

```kotlin
// Dynamic registration pattern
class MainActivity : AppCompatActivity() {
    private val receiver = object : BroadcastReceiver() {
        override fun onReceive(context: Context, intent: Intent) {
            // Handle broadcast
        }
    }
    
    override fun onStart() {
        super.onStart()
        val filter = IntentFilter("com.example.CUSTOM_ACTION")
        registerReceiver(receiver, filter)
    }
    
    override fun onStop() {
        super.onStop()
        unregisterReceiver(receiver)
    }
}
```

**Common Pitfall:** Forgetting to unregister receivers causes memory leaks. Always unregister in the corresponding lifecycle method.

---

### JobScheduler vs WorkManager

**Q43: When should you use JobScheduler vs WorkManager?**

**WorkManager (Recommended):**
- Backwards compatible to API 14
- Simpler API
- Integrates with LiveData
- Handles constraints automatically
- Better for most use cases

**JobScheduler:**
- Available API 21+
- More control over scheduling
- System-level API
- Use only if you need specific features not in WorkManager

```kotlin
// WorkManager (simple)
val work = OneTimeWorkRequestBuilder<MyWorker>()
    .setConstraints(
        Constraints.Builder()
            .setRequiredNetworkType(NetworkType.CONNECTED)
            .build()
    )
    .build()

WorkManager.getInstance(context).enqueue(work)

// JobScheduler (complex)
val jobScheduler = getSystemService(Context.JOB_SCHEDULER_SERVICE) as JobScheduler
val jobInfo = JobInfo.Builder(JOB_ID, ComponentName(this, MyJobService::class.java))
    .setRequiredNetworkType(JobInfo.NETWORK_TYPE_ANY)
    .build()

jobScheduler.schedule(jobInfo)
```

**Best Practice:** Use WorkManager unless you have a specific reason to use JobScheduler directly.

---

### Threads and Handlers

**Q44: What are Handlers and Loopers in Android?**

Handler and Looper enable inter-thread communication. The main thread has a Looper that processes messages from a MessageQueue.

```kotlin
// Post to main thread
val handler = Handler(Looper.getMainLooper())
handler.post {
    // Update UI
}

// Post with delay
handler.postDelayed({
    // Delayed UI update
}, 1000)

// Remove callbacks
handler.removeCallbacks(runnable)
```

**Note:** Handlers are older patterns. Modern apps should use coroutines or LiveData for threading instead.

---

**Q45: Why is AsyncTask deprecated and what should you use instead?**

AsyncTask was deprecated in API 30 because:
- Memory leaks if not handled properly
- No cancellation support
- Sequential execution by default
- Confusing lifecycle management

**Better alternatives:**
```kotlin
// Use Coroutines
lifecycleScope.launch {
    val result = withContext(Dispatchers.IO) {
        // Background work
        loadData()
    }
    // Update UI with result
}

// Or RxJava
disposable = Observable.fromCallable {
    loadData()
}
.subscribeOn(Schedulers.io())
.observeOn(AndroidSchedulers.mainThread())
.subscribe { result ->
    // Update UI
}
```

**Best Practice:** Never use AsyncTask in new code. Use coroutines for simplicity or RxJava for complex reactive streams.

---

## 2.5 Permissions

Android uses a permission system to protect user privacy and security. Understanding permissions is crucial for accessing sensitive resources.

### Permission Types

**Q46: What are the types of permissions in Android?**

**Normal Permissions:**
- Granted automatically at install
- Low risk to user privacy
- Examples: INTERNET, VIBRATE, ACCESS_NETWORK_STATE

**Dangerous Permissions:**
- Must request at runtime (API 23+)
- Access sensitive user data
- Grouped by function
- Examples: CAMERA, LOCATION, READ_CONTACTS

**Special Permissions:**
- Require special approval flow
- Very sensitive operations
- Examples: SYSTEM_ALERT_WINDOW, WRITE_SETTINGS, MANAGE_EXTERNAL_STORAGE

```xml
<!-- AndroidManifest.xml -->
<!-- Normal permission -->
<uses-permission android:name="android.permission.INTERNET" />

<!-- Dangerous permission -->
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

---

### Runtime Permissions

**Q47: How do you request runtime permissions (API 23+)?**

Since Android 6.0 (API 23), dangerous permissions must be requested at runtime.

```kotlin
class MainActivity : AppCompatActivity() {
    
    private val cameraPermissionLauncher = registerForActivityResult(
        ActivityResultContracts.RequestPermission()
    ) { isGranted ->
        if (isGranted) {
            openCamera()
        } else {
            showPermissionDenied()
        }
    }
    
    fun requestCamera() {
        when {
            ContextCompat.checkSelfPermission(
                this,
                Manifest.permission.CAMERA
            ) == PackageManager.PERMISSION_GRANTED -> {
                // Permission already granted
                openCamera()
            }
            
            shouldShowRequestPermissionRationale(Manifest.permission.CAMERA) -> {
                // Show rationale and request permission
                showRationale {
                    cameraPermissionLauncher.launch(Manifest.permission.CAMERA)
                }
            }
            
            else -> {
                // Request permission directly
                cameraPermissionLauncher.launch(Manifest.permission.CAMERA)
            }
        }
    }
    
    private fun showRationale(onAccept: () -> Unit) {
        AlertDialog.Builder(this)
            .setTitle("Camera Permission")
            .setMessage("Camera permission is needed to take photos")
            .setPositiveButton("OK") { _, _ -> onAccept() }
            .setNegativeButton("Cancel", null)
            .show()
    }
}
```

---

**Q48: How do you request multiple permissions?**

```kotlin
private val multiplePermissionsLauncher = registerForActivityResult(
    ActivityResultContracts.RequestMultiplePermissions()
) { permissions ->
    permissions.entries.forEach { (permission, isGranted) ->
        when (permission) {
            Manifest.permission.CAMERA -> {
                if (isGranted) {
                    // Camera granted
                }
            }
            Manifest.permission.RECORD_AUDIO -> {
                if (isGranted) {
                    // Audio granted
                }
            }
        }
    }
    
    // Check if all granted
    val allGranted = permissions.values.all { it }
    if (allGranted) {
        proceedWithAllPermissions()
    }
}

fun requestPermissions() {
    multiplePermissionsLauncher.launch(
        arrayOf(
            Manifest.permission.CAMERA,
            Manifest.permission.RECORD_AUDIO
        )
    )
}
```

---

**Q49: What is shouldShowRequestPermissionRationale?**

This method returns true if you should show a rationale (explanation) before requesting the permission. It returns false in two cases:
1. User hasn't been asked before
2. User selected "Don't ask again"

```kotlin
when {
    ContextCompat.checkSelfPermission(this, permission) == 
        PackageManager.PERMISSION_GRANTED -> {
        // Already have permission
    }
    
    shouldShowRequestPermissionRationale(permission) -> {
        // User denied before, show rationale
        showExplanation()
    }
    
    else -> {
        // First time or "Don't ask again"
        requestPermission()
    }
}
```

**Best Practice:** Always provide clear rationale before requesting permissions to improve grant rates.

---

**Q50: How do you handle the "Don't ask again" scenario?**

When a user selects "Don't ask again", you should guide them to app settings.

```kotlin
private fun openAppSettings() {
    Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS).also { intent ->
        val uri = Uri.fromParts("package", packageName, null)
        intent.data = uri
        startActivity(intent)
    }
}

private fun handlePermissionDenied() {
    AlertDialog.Builder(this)
        .setTitle("Permission Required")
        .setMessage("Camera permission is required. Please enable it in settings.")
        .setPositiveButton("Settings") { _, _ ->
            openAppSettings()
        }
        .setNegativeButton("Cancel", null)
        .show()
}
```

---

### Permission Best Practices

**Q51: What are best practices for handling permissions?**

1. **Request only necessary permissions** - Users are suspicious of apps requesting many permissions

2. **Request at appropriate time** - Request when feature is used, not at app launch

3. **Provide clear rationale** - Explain why you need the permission

4. **Handle all scenarios:**
   - Granted
   - Denied
   - Don't ask again

5. **Graceful degradation** - Provide alternative features if permission denied

```kotlin
// Good: Request when needed
fun takePhoto() {
    if (hasCameraPermission()) {
        openCamera()
    } else {
        requestCameraPermission()
    }
}

// Bad: Request all at launch
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    requestAllPermissions() // Don't do this!
}
```

**Try This:**