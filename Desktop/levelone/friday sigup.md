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
  
class CreateAccountActivity : AppCompatActivity() {  
    private lateinit var emailet: EditText  
    private lateinit var passwordet: EditText  
    private lateinit var confirmPasswordet: EditText  
    private lateinit var loginbacktv: TextView  
    private lateinit var createaccountbt: Button  
    private lateinit var progressBar: ProgressBar  
  
  
  
    override fun onCreate(savedInstanceState: Bundle?) {  
        super.onCreate(savedInstanceState)  
        setContentView(R.layout.activity_create_account_aactivity)  
  
        emailet =findViewById(R.id.email_edit_text)  
        passwordet=findViewById(R.id.password_edit_text)  
        confirmPasswordet=findViewById(R.id.confirm_edit_text)  
        loginbacktv=findViewById(R.id.login_back)  
        createaccountbt=findViewById(R.id.create_account_btn)  
        progressBar=findViewById(R.id.progres_bar)  
  
        createaccountbt.setOnClickListener {  
            createAccount()  
        }  
        loginbacktv.setOnClickListener {  
            val intent = Intent(this, LoginActivity::class.java)  
            startActivity(intent)  
        }  
    }  
  
    private fun createAccount() {  
        val email = emailet.text.toString()  
        val password = passwordet.text.toString()  
        val confirmPassword = confirmPasswordet.text.toString()  
  
        val isValidated = validateData(email, password, confirmPassword)  
  
        if (isValidated) {  
            createAccountInFirebase(email, password)  
        }  
    }  
  
    private fun validateData(email: String, password: String, confirmPassword: String): Boolean {  
        if (!Patterns.EMAIL_ADDRESS.matcher(email).matches()) {  
            emailet.error = "Email is invalid"  
            return false  
        }  
        if (password.length < 6) {  
            passwordet.error = "Password too short"  
            return false  
        }  
        if (password != confirmPassword) {  
            confirmPasswordet.error = "Does not match password"  
            return false  
        }  
        return true  
    }  
  
    private fun createAccountInFirebase(email: String, password: String) {  
        //progressbar chages  
        changeInProgress(true)  
        val firebaseAuth = FirebaseAuth.getInstance()  
        firebaseAuth.createUserWithEmailAndPassword(email, password)  
            .addOnCompleteListener(this) { task ->  
                changeInProgress(false)  
                if (task.isSuccessful) {  
                    Toast.makeText(this, "Successfully created account. Check your email for verification.", Toast.LENGTH_SHORT).show()  
                    firebaseAuth.currentUser?.sendEmailVerification()  
                    FirebaseAuth.getInstance().signOut()  
                    finish()  
                } else {  
                    Toast.makeText(this, task.exception?.localizedMessage, Toast.LENGTH_SHORT).show()  
                }  
            }  
    }  
  
    private fun changeInProgress(inProgress: Boolean) {  
        if (inProgress) {  
            progressBar.visibility = View.VISIBLE  
            createaccountbt.visibility = View.GONE  
        } else {  
            progressBar.visibility = View.GONE  
            createaccountbt.visibility = View.VISIBLE  
        }  
    }  
}