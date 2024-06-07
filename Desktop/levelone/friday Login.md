
login layout
```xml

<ImageView  
    android:layout_width="match_parent"  
    android:layout_height="match_parent"  
    android:scaleType="centerCrop"  
    android:src="@drawable/darkback" />  
<ImageView  
    android:layout_width="128dp"  
    android:id="@+id/sign_up_icon"  
    android:layout_height="128dp"  
    android:layout_centerHorizontal="true"  
    android:layout_marginVertical="16dp"  
    android:src="@drawable/logofb"/>  
<LinearLayout  
    android:layout_width="match_parent"  
    android:layout_height="wrap_content"  
    android:id="@+id/hello_text"  
    android:orientation="vertical"  
    android:layout_below="@id/sign_up_icon">  
  
    <TextView        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:text="  Welcome back Fam"  
        android:textStyle="bold"  
        android:textSize="40sp"  
        android:textColor="@color/red"/>  
  
</LinearLayout>  
<LinearLayout  
    android:layout_width="match_parent"  
    android:layout_height="wrap_content"  
    android:layout_below="@id/hello_text"  
    android:id="@+id/form_edit"  
    android:orientation="vertical">  
  
    <EditText        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:id="@+id/email_edit_text"  
        android:textColorHint="@color/white"  
        android:hint="Email"  
        android:gravity="center"  
        android:layout_marginTop="15dp"  
        android:inputType="textEmailAddress"  
        android:textSize="32sp"/>  
    <EditText        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:hint="Password"  
        android:textColorHint="@color/white"  
        android:id="@+id/password_edit_text"  
        android:layout_marginTop="15dp"  
        android:inputType="textPassword"  
        android:gravity="center"  
        android:textSize="32sp"/>  
  
    <com.google.android.material.button.MaterialButton        android:layout_width="match_parent"  
        android:layout_height="64dp"  
        android:text="Login"  
        android:gravity="center"  
        android:layout_marginTop="15dp"  
        android:textSize="32sp"  
        android:id="@+id/login_btn"  
        android:backgroundTint="@color/redb"  
  
        />  
    <ProgressBar        android:layout_width="64dp"  
        android:layout_height="64dp"  
        android:id="@+id/progres_bar"  
        android:layout_gravity="center"  
        android:layout_marginTop="15dp"  
        android:backgroundTint="@color/myprimary"  
        android:visibility="gone"/>  
  
</LinearLayout>  
<LinearLayout  
    android:layout_width="match_parent"  
    android:layout_height="wrap_content"  
    android:layout_below="@id/form_edit"  
    android:orientation="horizontal"  
    android:layout_marginTop="40dp"  
    android:gravity="center">  
  
    <TextView        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
        android:text="Dont Have An Account:"  
        android:textColor="@color/white"  
        android:textSize="20sp" />  
  
    <TextView        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
        android:text="Signup"  
        android:textSize="20sp"  
        android:textColor="@color/white"  
        android:textStyle="bold"  
        android:id="@+id/to_signup"  
        />  
</LinearLayout>
```







```kotlin

    private lateinit var emailet: EditText  
    private lateinit var passwordet: EditText  
    private lateinit var tosignup: TextView  
    private lateinit var loginbt: Button  
    private lateinit var progressBar: ProgressBar  
//no 1
override fun onCreate(savedInstanceState: Bundle?) {  
    super.onCreate(savedInstanceState)  
    setContentView(R.layout.activity_login)  
  
    emailet = findViewById(R.id.email_edit_text)  
    passwordet = findViewById(R.id.password_edit_text)  
    tosignup = findViewById(R.id.to_signup)  
    loginbt = findViewById(R.id.login_btn)  
    progressBar = findViewById(R.id.progres_bar)  
  
    loginbt.setOnClickListener {  
        loginUser()  
    }  
    tosignup.setOnClickListener {  
        val intent = Intent(this, CreateAccountActivity::class.java)  
        startActivity(intent)  
        finish()  
    }  
}


//no2
private fun loginUser() {  
        val email = emailet.text.toString()  
        val password = passwordet.text.toString()  
  
        if (validateData(email, password)) {  
            loginAccountInFirebase(email, password)  
        }  
    } 



//no3
private fun loginAccountInFirebase(email: String, password: String) {  
        val firebaseAuth = FirebaseAuth.getInstance()  
        changeInProgress(true)  
        firebaseAuth.signInWithEmailAndPassword(email, password)  
            .addOnCompleteListener(this) { task ->  
                changeInProgress(false)  
                if (task.isSuccessful) {  
                    // Login success  
                    val currentUser = firebaseAuth.currentUser  
                    if (currentUser != null && currentUser.isEmailVerified) {  
                        // Go to MainActivity  
                        val intent = Intent(this, MainActivity::class.java)  
                        startActivity(intent)  
                        finish() // Close the LoginActivity  
                    } else {  
                        Toast.makeText(this, "Email not verified", Toast.LENGTH_SHORT).show()  
                    }  
                } else {  
                    // Login failed  
                    Toast.makeText(this, task.exception?.localizedMessage, Toast.LENGTH_SHORT).show()  
                }  
            }  
    } 

private fun validateData(email: String, password: String): Boolean {  
        return when {  
            !Patterns.EMAIL_ADDRESS.matcher(email).matches() -> {  
                emailet.error = "Email is invalid"  
                false  
            }  
            password.length < 6 -> {  
                passwordet.error = "Password too short"  
                false  
            }  
            else -> true  
        }  
    }  
  



```


loginActivity
```kotlin
package com.example.notebook2  
  
import android.content.Intent  
import androidx.appcompat.app.AppCompatActivity  
import android.os.Bundle  
import android.util.Patterns  
import android.view.View  
import android.widget.Button  
import android.widget.EditText  
import android.widget.ProgressBar  
import android.widget.TextView  
import android.widget.Toast  
import com.google.firebase.auth.FirebaseAuth  
  
class LoginActivity : AppCompatActivity() {  
    private lateinit var emailet: EditText  
    private lateinit var passwordet: EditText  
    private lateinit var tosignup: TextView  
    private lateinit var loginbt: Button  
    private lateinit var progressBar: ProgressBar  
  
    override fun onCreate(savedInstanceState: Bundle?) {  
        super.onCreate(savedInstanceState)  
        setContentView(R.layout.activity_login)  
  
        emailet = findViewById(R.id.email_edit_text)  
        passwordet = findViewById(R.id.password_edit_text)  
        tosignup = findViewById(R.id.to_signup)  
        loginbt = findViewById(R.id.login_btn)  
        progressBar = findViewById(R.id.progres_bar)  
  
        loginbt.setOnClickListener {  
            loginUser()  
        }  
        tosignup.setOnClickListener {  
            val intent = Intent(this, CreateAccountActivity::class.java)  
            startActivity(intent)  
            finish()  
        }  
    }  
  
    private fun loginUser() {  
        val email = emailet.text.toString()  
        val password = passwordet.text.toString()  
  
        if (validateData(email, password)) {  
            loginAccountInFirebase(email, password)  
        }  
    }  
  
    private fun loginAccountInFirebase(email: String, password: String) {  
        val firebaseAuth = FirebaseAuth.getInstance()  
        changeInProgress(true)  
        firebaseAuth.signInWithEmailAndPassword(email, password)  
            .addOnCompleteListener(this) { task ->  
                changeInProgress(false)  
                if (task.isSuccessful) {  
                    // Login success  
                    val currentUser = firebaseAuth.currentUser  
                    if (currentUser != null && currentUser.isEmailVerified) {  
                        // Go to MainActivity  
                        val intent = Intent(this, MainActivity::class.java)  
                        startActivity(intent)  
                        finish() // Close the LoginActivity  
                    } else {  
                        Toast.makeText(this, "Email not verified", Toast.LENGTH_SHORT).show()  
                    }  
                } else {  
                    // Login failed  
                    Toast.makeText(this, task.exception?.localizedMessage, Toast.LENGTH_SHORT).show()  
                }  
            }  
    }  
  
    private fun validateData(email: String, password: String): Boolean {  
        return when {  
            !Patterns.EMAIL_ADDRESS.matcher(email).matches() -> {  
                emailet.error = "Email is invalid"  
                false  
            }  
            password.length < 6 -> {  
                passwordet.error = "Password too short"  
                false  
            }  
            else -> true  
        }  
    }  
  
    private fun changeInProgress(inProgress: Boolean) {  
        if (inProgress) {  
            progressBar.visibility = View.VISIBLE  
            loginbt.visibility = View.GONE  
        } else {  
            progressBar.visibility = View.GONE  
            loginbt.visibility = View.VISIBLE  
        }  
    }  
}
```




