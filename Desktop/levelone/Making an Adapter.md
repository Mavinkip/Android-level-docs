you will need a data class in our case  Note
an adapter class NoteAdapter
and an item layout in our case 'recyclerview_note_item' an xml file


Data class contain the type of data you will be using

```kotlin

import com.google.firebase.Timestamp  
  
class Note(var title: String = "", var content: String = "", var timestamp: Any = Timestamp.now()) {  
  
    // You can add additional properties or methods here if needed  
  
}
```


the item layout will show how  items will appear  on the recyclerView
plus the item appear in widget card view

```xml
<ImageView  
    android:layout_width="match_parent"  
    android:layout_height="100dp"  
    android:scaleType="centerCrop"  
    android:src="@drawable/fbbackground" />  
<LinearLayout  
    android:layout_width="match_parent"  
    android:layout_height="wrap_content"  
  
  
    android:orientation="vertical">  
    <TextView        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:text="note title"  
        android:id="@+id/title_tv"  
        android:textColor="@color/white"  
        android:textSize="20sp"  
        android:textStyle="bold"  
        android:layout_marginVertical="5dp"  
  
        />  
    <TextView        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:id="@+id/content_tv"  
        android:text="note content"  
        android:textColor="@color/white"  
        android:maxLines="2"  
        android:textSize="20sp"  
        android:layout_marginVertical="5dp"  
  
        />  
    <TextView        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:text="note title"  
        android:id="@+id/time_tv"  
        android:textColor="@color/white"  
        android:textSize="15sp"  
        android:layout_marginVertical="5dp"  
        android:gravity="right"  
        />  
  
</LinearLayout>
```


the Adapter 'Note adapter ' connects them all


```kotlin
//step 1
FirestoreRecyclerAdapter<Note, NoteAdapter.NoteViewHolder>(options) {  
  
class NoteViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {  
    private val titleTv: TextView = itemView.findViewById(R.id.title_tv)  
    private val contentTv: TextView = itemView.findViewById(R.id.content_tv)  
    private val timestampTv: TextView = itemView.findViewById(R.id.time_tv)  
  
    fun bind(note: Note) {  
        titleTv.text = note.title  
        contentTv.text = note.content  
  
        val currentTime = Calendar.getInstance().time  
        val sdf = SimpleDateFormat("yyyy-MM-dd HH:mm:ss", Locale.getDefault())  
        val formattedDate = sdf.format(currentTime)  
        timestampTv.text = formattedDate  
    }  
}


//step 2
override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): NoteViewHolder {  
    val view = LayoutInflater.from(parent.context).inflate(R.layout.recyclerview_note_item, parent, false)  
    return NoteViewHolder(view)  
}

//step3

override fun onBindViewHolder(holder: NoteViewHolder, position: Int, model: Note) {  
    holder.bind(model)  
  
    // Editing note  
    // Editing note    holder.itemView.setOnClickListener {  
        val context = holder.itemView.context  
        val docId = snapshots.getSnapshot(position).id // Retrieve the document ID  
  
        // Check if the position is valid        if (position != RecyclerView.NO_POSITION) {  
            val intent = Intent(context, NoteDetailsActivity::class.java).apply {  
                putExtra("title", model.title)  
                putExtra("content", model.content)  
                putExtra("docid", docId) // Pass the document ID to NoteDetailsActivity  
            }  
            context.startActivity(intent)  
        } else {  
            // Handle the case where the position is invalid  
            Toast.makeText(context, "Invalid position", Toast.LENGTH_SHORT).show()  
        }  
    }  
}
```