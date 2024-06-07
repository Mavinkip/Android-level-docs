note details layout

```xml
<ImageView  
    android:layout_width="match_parent"  
    android:layout_height="match_parent"  
    android:scaleType="centerCrop"  
    android:src="@drawable/darkback" />  
<RelativeLayout  
    android:layout_width="wrap_content"  
    android:layout_height="wrap_content"  
    android:layout_margin="20dp"  
    android:id="@+id/title_bar_layout">  
  
    <TextView        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
  
        android:id="@+id/page_title"  
        android:text="Add New Note"  
        android:textSize="32sp"  
        android:textColor="@color/white"/>  
  
    <ImageButton        android:id="@+id/save_note"  
        android:layout_width="50dp"  
        android:layout_height="50dp"  
        android:layout_alignParentEnd="true"  
        android:layout_centerVertical="true"  
        android:background="?attr/selectableItemBackgroundBorderless"  
  
        android:src="@drawable/baseline_done_24"  
        android:tint="@color/red"  
  
        tools:ignore="SpeakableTextPresentCheck,UseAppTint" />  
</RelativeLayout>  
<LinearLayout  
    android:layout_width="match_parent"  
    android:layout_height="wrap_content"  
    android:layout_below="@id/title_bar_layout"  
    android:orientation="vertical"  
    android:padding="16dp"  
    android:layout_marginVertical="26dp"  
    android:id="@+id/cotent_bar_tv"  
    android:background="@color/white"  
    >  
    <EditText        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:id="@+id/note_title_text"  
        android:hint="Title"  
        android:textSize="20sp"  
        android:textStyle="bold"  
        android:layout_marginVertical="10dp"  
        android:padding="12dp"  
        android:textColor="@color/black"/>  
    <EditText        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:id="@+id/note_contet_text"  
        android:hint="Content"  
        android:gravity="top"  
        android:minLines="15"  
        android:textSize="20sp"  
        android:layout_marginVertical="10dp"  
        android:padding="12dp"  
        android:textColor="@color/black"/>  
</LinearLayout>  
<TextView  
    android:layout_width="match_parent"  
    android:layout_height="wrap_content"  
    android:id="@+id/delet_tv"  
    android:layout_alignParentBottom="true"  
    android:text="Delete Note"  
    android:textColor="@color/myprimary"  
    android:gravity="center"  
    android:textSize="25sp"  
    android:visibility="gone"  
    android:textStyle="italic"  
    />
```

```kotlin
private lateinit var titlenoteet : EditText  
private lateinit var contentnoteet : EditText  
private lateinit var savenotbt : ImageButton  
private lateinit var pageTitle : TextView  
private lateinit var  title : String  
private lateinit var content : String  
private lateinit var docId : String  
private var isEditMode = false  
private lateinit var deletNote : TextView


//step 1
override fun onCreate(savedInstanceState: Bundle?) {  
    super.onCreate(savedInstanceState) 
    //process 1 
    setContentView(R.layout.activity_note_details)  
    titlenoteet=findViewById(R.id.note_title_text)  
    contentnoteet=findViewById(R.id.note_contet_text)  
    savenotbt=findViewById(R.id.save_note)  
    pageTitle=findViewById(R.id.page_title)  
    deletNote=findViewById(R.id.delet_tv)  
  
            //later when making and editing notes
            //editing the note    title = intent.getStringExtra("title") ?: ""
              
        content = intent.getStringExtra("content") ?: ""  
        docId = intent.getStringExtra("docid") ?: ""  
  
        if (docId.isNotEmpty()) {  
            isEditMode = true  
        }  
  
                titlenoteet.setText(title)  
                contentnoteet.setText(content)  
  
        if (isEditMode) {  
            pageTitle.text = "Edit your Note"  
            deletNote.visibility = View.VISIBLE  
        }  
  
                savenotbt.setOnClickListener {  
            saveNote()  
        }  
    deletNote.setOnClickListener{  
        deleteNoteFromFirebase()  
    }  
}

//step2
private fun saveNote() {  
    val noteTitle = titlenoteet.text.toString()  
    val noteContent = contentnoteet.text.toString()  
  
    if (noteTitle.isEmpty()) {  
        titlenoteet.error = "Title required"  
        return  
    }  
  
    val note = Note().apply {  
        title = noteTitle  
        content = noteContent  
        timestamp = com.google.firebase.Timestamp.now()  
    }  
  
    saveNoteToFirebase(note)  
}


//step 3
private fun saveNoteToFirebase(note: Note) {  
    val collectionReference = getNoteCollectionReference()  
  
    if (isEditMode) {  
        val documentReference: DocumentReference = collectionReference.document(docId)  
        documentReference.set(note)  
            .addOnSuccessListener {  
                showToast("Note updated successfully")  
                finish()  
            }  
            .addOnFailureListener { e ->  
                showToast("Failed to update note: ${e.message}")  
            }  
    } else {  
        collectionReference.add(note)  
            .addOnSuccessListener {  
                showToast("Note added successfully")  
                finish()  
            }  
            .addOnFailureListener { e ->  
                showToast("Failed to add note: ${e.message}")  
            }  
    }  
}

//step 4
private fun deleteNoteFromFirebase() {  
    val collectionReference = getNoteCollectionReference()  
    val documentReference: DocumentReference = collectionReference.document(docId)  
    documentReference.delete()  
        .addOnSuccessListener {  
            showToast("Note deleted successfully")  
            finish()  
        }  
        .addOnFailureListener { e ->  
            showToast("Failed to delete note: ${e.message}")  
        }  
}

//step 5

  
private fun getNoteCollectionReference() = FirebaseFirestore.getInstance().collection("notes")  
    .document(FirebaseAuth.getInstance().currentUser?.uid  
        ?: throw IllegalStateException("User not authenticated"))  
    .collection("mynotes")

	//displaying messages
	private fun showToast(message: String) {  
    Toast.makeText(this, message, Toast.LENGTH_SHORT).show()  
}
```


Note dwtais Activity
```kotlin

  
import androidx.appcompat.app.AppCompatActivity  
import android.os.Bundle  
import android.view.View  
import android.widget.EditText  
import android.widget.ImageButton  
import android.widget.TextView  
import android.widget.Toast  
import com.google.firebase.auth.FirebaseAuth  
import com.google.firebase.firestore.DocumentReference  
import com.google.firebase.firestore.FirebaseFirestore  
  
class NoteDetailsActivity : AppCompatActivity() {  
    private lateinit var titlenoteet : EditText  
    private lateinit var contentnoteet : EditText  
    private lateinit var savenotbt : ImageButton  
    private lateinit var pageTitle : TextView  
    private lateinit var  title : String  
    private lateinit var content : String  
    private lateinit var docId : String  
    private var isEditMode = false  
    private lateinit var deletNote : TextView  
  
  
  
  
  
    override fun onCreate(savedInstanceState: Bundle?) {  
        super.onCreate(savedInstanceState)  
        setContentView(R.layout.activity_note_details)  
        titlenoteet=findViewById(R.id.note_title_text)  
        contentnoteet=findViewById(R.id.note_contet_text)  
        savenotbt=findViewById(R.id.save_note)  
        pageTitle=findViewById(R.id.page_title)  
        deletNote=findViewById(R.id.delet_tv)  
  
                //recive data from  
                //editing the note        title = intent.getStringExtra("title") ?: ""  
            content = intent.getStringExtra("content") ?: ""  
            docId = intent.getStringExtra("docid") ?: ""  
  
            if (docId.isNotEmpty()) {  
                isEditMode = true  
            }  
  
                    titlenoteet.setText(title)  
                    contentnoteet.setText(content)  
  
            if (isEditMode) {  
                pageTitle.text = "Edit your Note"  
                deletNote.visibility = View.VISIBLE  
            }  
  
                    savenotbt.setOnClickListener {  
                saveNote()  
            }  
        deletNote.setOnClickListener{  
            deleteNoteFromFirebase()  
        }  
    }  
    private fun saveNote() {  
        val noteTitle = titlenoteet.text.toString()  
        val noteContent = contentnoteet.text.toString()  
  
        if (noteTitle.isEmpty()) {  
            titlenoteet.error = "Title required"  
            return  
        }  
  
        val note = Note().apply {  
            title = noteTitle  
            content = noteContent  
            timestamp = com.google.firebase.Timestamp.now()  
        }  
  
        saveNoteToFirebase(note)  
    }  
  
    private fun saveNoteToFirebase(note: Note) {  
        val collectionReference = getNoteCollectionReference()  
  
        if (isEditMode) {  
            val documentReference: DocumentReference = collectionReference.document(docId)  
            documentReference.set(note)  
                .addOnSuccessListener {  
                    showToast("Note updated successfully")  
                    finish()  
                }  
                .addOnFailureListener { e ->  
                    showToast("Failed to update note: ${e.message}")  
                }  
        } else {  
            collectionReference.add(note)  
                .addOnSuccessListener {  
                    showToast("Note added successfully")  
                    finish()  
                }  
                .addOnFailureListener { e ->  
                    showToast("Failed to add note: ${e.message}")  
                }  
        }  
    }  
  
    private fun deleteNoteFromFirebase() {  
        val collectionReference = getNoteCollectionReference()  
        val documentReference: DocumentReference = collectionReference.document(docId)  
        documentReference.delete()  
            .addOnSuccessListener {  
                showToast("Note deleted successfully")  
                finish()  
            }  
            .addOnFailureListener { e ->  
                showToast("Failed to delete note: ${e.message}")  
            }  
    }  
  
    private fun getNoteCollectionReference() = FirebaseFirestore.getInstance().collection("notes")  
        .document(FirebaseAuth.getInstance().currentUser?.uid  
            ?: throw IllegalStateException("User not authenticated"))  
        .collection("mynotes")  
  
    private fun showToast(message: String) {  
        Toast.makeText(this, message, Toast.LENGTH_SHORT).show()  
    }  
}
```


Not details layout 
```xml

<ImageView  
    android:layout_width="match_parent"  
    android:layout_height="match_parent"  
    android:scaleType="centerCrop"  
    android:src="@drawable/darkback" />  
<RelativeLayout  
    android:layout_width="wrap_content"  
    android:layout_height="wrap_content"  
    android:layout_margin="20dp"  
    android:id="@+id/title_bar_layout">  
  
    <TextView        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
  
        android:id="@+id/page_title"  
        android:text="Add New Note"  
        android:textSize="32sp"  
        android:textColor="@color/white"/>  
  
    <ImageButton        android:id="@+id/save_note"  
        android:layout_width="50dp"  
        android:layout_height="50dp"  
        android:layout_alignParentEnd="true"  
        android:layout_centerVertical="true"  
        android:background="?attr/selectableItemBackgroundBorderless"  
  
        android:src="@drawable/baseline_done_24"  
        android:tint="@color/red"  
  
        tools:ignore="SpeakableTextPresentCheck,UseAppTint" />  
</RelativeLayout>  
<LinearLayout  
    android:layout_width="match_parent"  
    android:layout_height="wrap_content"  
    android:layout_below="@id/title_bar_layout"  
    android:orientation="vertical"  
    android:padding="16dp"  
    android:layout_marginVertical="26dp"  
    android:id="@+id/cotent_bar_tv"  
    android:background="@color/white"  
    >  
    <EditText        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:id="@+id/note_title_text"  
        android:hint="Title"  
        android:textSize="20sp"  
        android:textStyle="bold"  
        android:layout_marginVertical="10dp"  
        android:padding="12dp"  
        android:textColor="@color/black"/>  
    <EditText        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:id="@+id/note_contet_text"  
        android:hint="Content"  
        android:gravity="top"  
        android:minLines="15"  
        android:textSize="20sp"  
        android:layout_marginVertical="10dp"  
        android:padding="12dp"  
        android:textColor="@color/black"/>  
</LinearLayout>  
<TextView  
    android:layout_width="match_parent"  
    android:layout_height="wrap_content"  
    android:id="@+id/delet_tv"  
    android:layout_alignParentBottom="true"  
    android:text="Delete Note"  
    android:textColor="@color/myprimary"  
    android:gravity="center"  
    android:textSize="25sp"  
    android:visibility="gone"  
    android:textStyle="italic"  
    />
```