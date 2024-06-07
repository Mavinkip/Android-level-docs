
display notes layout
```xml
<ImageView  
    android:layout_width="match_parent"  
    android:layout_height="match_parent"  
    android:scaleType="centerCrop"  
    android:src="@drawable/darkback" />  
<RelativeLayout  
    android:layout_width="wrap_content"  
    android:layout_height="wrap_content"  
    android:id="@+id/title_bar_layout">  
  
    <TextView        android:id="@+id/page_title"  
        android:layout_width="match_parent"  
        android:layout_height="53dp"  
        android:text="@string/my_notes"  
        android:gravity="center"  
        android:textColor="@color/white"  
        android:textSize="32sp" />  
  
    <com.google.android.material.floatingactionbutton.FloatingActionButton        android:id="@+id/menu_btn"  
        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
        android:layout_alignParentEnd="true"  
        android:contentDescription="@string/menu"  
        android:src="@drawable/ic_menu" />  
</RelativeLayout>  
<androidx.recyclerview.widget.RecyclerView  
    android:layout_width="match_parent"  
  
    android:layout_height="match_parent"  
    android:id="@+id/showrv"  
    android:layout_below="@id/title_bar_layout"  
    android:backgroundTint="@color/black"  
    tools:listitem="@layout/recyclerview_note_item"/>  
  
<com.google.android.material.floatingactionbutton.FloatingActionButton  
    android:layout_width="wrap_content"  
    android:layout_height="wrap_content"  
    android:backgroundTint="@color/white"  
    android:layout_alignParentEnd="true"  
    android:id="@+id/add_note_btn"  
    android:layout_alignParentBottom="true"  
    android:src="@drawable/baseline_add_24"  
  
    />
```


displaying the notes

```kotlin
private lateinit var addNotebtn: FloatingActionButton  
private lateinit var menubt: FloatingActionButton  
private lateinit var recyclerView: RecyclerView  
private lateinit var noteAdapter: NoteAdapter

override fun onCreate(savedInstanceState: Bundle?) {  
    super.onCreate(savedInstanceState)  
    setContentView(R.layout.activity_main)  
  
    addNotebtn = findViewById(R.id.add_note_btn)  
    menubt = findViewById(R.id.menu_btn)  
    recyclerView = findViewById(R.id.showrv)  
  
    addNotebtn.setOnClickListener {  
        val intent = Intent(this, NoteDetailsActivity::class.java)  
        startActivity(intent)  
    }  
  
    menubt.setOnClickListener {  
        showMenu()  
    }  
  
    setupRecyclerView()  
}


//step 2
  
private fun showMenu() {  
    // Implement menu logic here  
    // Create the PopupMenu    val popupMenu = PopupMenu(this, menubt)  
  
    // Inflate the menu resource into the PopupMenu  
    popupMenu.menu.add("Logout")  
  
    // Show the PopupMenu  
    popupMenu.show()  
  
    // Set the click listener for menu items  
    popupMenu.setOnMenuItemClickListener { menuItem: MenuItem ->  
        if (menuItem.title == "Logout") {  
            // Perform the logout action  
            FirebaseAuth.getInstance().signOut()  
            startActivity(Intent(this, LoginActivity::class.java))  
            finish()  
            true  
        } else {  
            false  
        }  
    }  
}

//step 3
private fun setupRecyclerView() {  
    val currentUser = FirebaseAuth.getInstance().currentUser  
    if (currentUser != null) {  
        val collectionReference = FirebaseFirestore.getInstance().collection("notes")  
            .document(currentUser.uid)  
            .collection("mynotes")  
  
        val query = collectionReference.orderBy("timestamp", Query.Direction.DESCENDING)  
  
        val options = FirestoreRecyclerOptions.Builder<Note>()  
            .setQuery(query, Note::class.java)  
            .build()  
  
  
        noteAdapter = NoteAdapter(options)  
  
        recyclerView.layoutManager = LinearLayoutManager(this)  
        recyclerView.adapter = noteAdapter  
  
        noteAdapter.startListening()  
    }  
}


//step 4
override fun onStart() {  
    super.onStart()  
    if (::noteAdapter.isInitialized) {  
        noteAdapter.startListening()  
    }  
}
```



Main Activity


```kotlin


  
import android.content.Intent  
import androidx.appcompat.app.AppCompatActivity  
import android.os.Bundle  
import android.view.MenuInflater  
import android.view.MenuItem  
import android.widget.PopupMenu  
import androidx.recyclerview.widget.LinearLayoutManager  
import androidx.recyclerview.widget.RecyclerView  
import com.firebase.ui.firestore.FirestoreRecyclerOptions  
import com.google.android.material.floatingactionbutton.FloatingActionButton  
import com.google.firebase.auth.FirebaseAuth  
import com.google.firebase.firestore.FirebaseFirestore  
import com.google.firebase.firestore.Query  
  
class MainActivity : AppCompatActivity() {  
    private lateinit var addNotebtn: FloatingActionButton  
    private lateinit var menubt: FloatingActionButton  
    private lateinit var recyclerView: RecyclerView  
    private lateinit var noteAdapter: NoteAdapter  
  
    override fun onCreate(savedInstanceState: Bundle?) {  
        super.onCreate(savedInstanceState)  
        setContentView(R.layout.activity_main)  
  
        addNotebtn = findViewById(R.id.add_note_btn)  
        menubt = findViewById(R.id.menu_btn)  
        recyclerView = findViewById(R.id.showrv)  
  
        addNotebtn.setOnClickListener {  
            val intent = Intent(this, NoteDetailsActivity::class.java)  
            startActivity(intent)  
        }  
  
        menubt.setOnClickListener {  
            showMenu()  
        }  
  
        setupRecyclerView()  
    }  
  
    private fun showMenu() {  
        // Implement menu logic here  
        // Create the PopupMenu        val popupMenu = PopupMenu(this, menubt)  
  
        // Inflate the menu resource into the PopupMenu  
        popupMenu.menu.add("Logout")  
  
        // Show the PopupMenu  
        popupMenu.show()  
  
        // Set the click listener for menu items  
        popupMenu.setOnMenuItemClickListener { menuItem: MenuItem ->  
            if (menuItem.title == "Logout") {  
                // Perform the logout action  
                FirebaseAuth.getInstance().signOut()  
                startActivity(Intent(this, LoginActivity::class.java))  
                finish()  
                true  
            } else {  
                false  
            }  
        }  
    }  
  
    private fun setupRecyclerView() {  
        val currentUser = FirebaseAuth.getInstance().currentUser  
        if (currentUser != null) {  
            val collectionReference = FirebaseFirestore.getInstance().collection("notes")  
                .document(currentUser.uid)  
                .collection("mynotes")  
  
            val query = collectionReference.orderBy("timestamp", Query.Direction.DESCENDING)  
  
            val options = FirestoreRecyclerOptions.Builder<Note>()  
                .setQuery(query, Note::class.java)  
                .build()  
  
  
            noteAdapter = NoteAdapter(options)  
  
            recyclerView.layoutManager = LinearLayoutManager(this)  
            recyclerView.adapter = noteAdapter  
  
            noteAdapter.startListening()  
        }  
    }  
  
    override fun onStart() {  
        super.onStart()  
        if (::noteAdapter.isInitialized) {  
            noteAdapter.startListening()  
        }  
    }  
  
  
}
```