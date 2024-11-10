# DataStoreExample


the objective of this workshop is to add and retrieve data from datastore

We will save and retrieve login and password in datastore

# Step 1 
Create Jetpack Android Project DataStoreExample
add dependencies to your project
```kotlin
    implementation ("androidx.datastore:datastore-preferences-rxjava3:1.0.0")
    implementation ("androidx.datastore:datastore:1.0.0")
    implementation ("androidx.datastore:datastore-core:1.0.0")
```

# Step 2 
Add a class named UserStore.kt under the data package in your project
the class will contain tokens stocked in the datastore and how to retrieve and push in the datastore
# Step 3
The class will contain tokenUser and 2 methods to accede and store in the datastore
```kotlin
port android.content.Context
import androidx.datastore.core.DataStore
import androidx.datastore.preferences.core.Preferences
import androidx.datastore.preferences.core.stringPreferencesKey
import androidx.datastore.preferences.preferencesDataStore
import androidx.datastore.preferences.core.edit

import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.map

class UserStore(private val context: Context) {
    companion object {
        private val Context.dataStore: DataStore<Preferences> by preferencesDataStore("userToken")
        private val USER_TOKEN_KEY = stringPreferencesKey("user_token")
    }

    val getAccessToken: Flow<String> = context.dataStore.data.map { preferences ->
        preferences[USER_TOKEN_KEY] ?: ""
    }

    suspend fun saveToken(token: String) {
        context.dataStore.edit { preferences ->
            preferences[USER_TOKEN_KEY] = token
        }
    }
}
```
# Step 4

create a LoginScreen.kt in your view package that contain the login screen composable
```kotlin
@Composable
    public void loginScreen() {

        // State variables to store user input
        val userName = remember {
            mutableStateOf(TextFieldValue()
        }
        val userPassword = remember {
            mutableStateOf(TextFieldValue())
        }

        // Column to arrange UI elements vertically
        Column(modifier = Modifier
            .fillMaxHeight()
            .padding(40.dp)) {

            // Welcome message
            Text(text = "Hello,\nWelcome to the login page", fontSize = 25.sp, color = Color.Blue,
                modifier = Modifier.fillMaxWidth().padding(0.dp, 50.dp, 0.dp, 0.dp)
            )

            // Username input field
            OutlinedTextField(value = userName.value, onValueChange = {
                userName.value = it
            },
                leadingIcon = {
                    Icon(Icons.Default.Person, contentDescription = "person")
                },
                label = {
                    Text(text = "username")
                },
                modifier = Modifier.fillMaxWidth().padding(0.dp, 20.dp, 0.dp, 0.dp)
            )

            // Password input field
            OutlinedTextField(value = userPassword.value, onValueChange = {
                userPassword.value = it
            },
                leadingIcon = {
                    Icon(Icons.Default.Info, contentDescription = "password")
                },
                label = {
                    Text(text = "password")
                },
                modifier = Modifier.fillMaxWidth().padding(0.dp, 20.dp, 0.dp, 0.dp),
                visualTransformation = PasswordVisualTransformation()
            )

            // Login button
            OutlinedButton(onClick = { },
                modifier = Modifier.fillMaxWidth().padding(0.dp, 25.dp, 0.dp, 0.dp)) {
                Text(text = "Login",
                    modifier = Modifier.fillMaxWidth().padding(5.dp),
                    textAlign = TextAlign.Center,
                    fontSize = 20.sp
                )
            }
        }
    }
}
```
# Step 5 
retrieve usertoken from datastore. in the login screen create an instance of the userstore and accede to usertoken 
```kotlin
val context = LocalContext.current
    val store = UserStore(context)
    val tokenUser = store.getAccessToken.collectAsState(initial = "")
```
change code to update username when user token change
```kotlin
// Update userName whenever tokenUser.value changes
    LaunchedEffect(tokenUser.value) {
        userName.value = TextFieldValue(tokenUser.value)
    }
```
# Step 6
Update the datastore when we click on login button
```kotlin
 // Login button
        OutlinedButton(onClick = {
            CoroutineScope(Dispatchers.IO).launch {
                store.saveToken(userName.value.text)
            } },
            modifier = Modifier.fillMaxWidth().padding(0.dp, 25.dp, 0.dp, 0.dp)) {
            Text(text = "Login",
                modifier = Modifier.fillMaxWidth().padding(5.dp),
                textAlign = TextAlign.Center,
                fontSize = 20.sp
            )
        }
```

# Step 7
test this code and add your password to datastore
