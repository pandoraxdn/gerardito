---

1. Configurar los permisos en el archivo AndroidManifest.xml

Agrega el permiso de internet para que la aplicación pueda realizar solicitudes de red.

<uses-permission android:name="android.permission.INTERNET" />


---

2. Crear el diseño del formulario

En el archivo XML de diseño (por ejemplo, activity_main.xml), define los campos de entrada y un botón para enviar.

<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <EditText
        android:id="@+id/et_name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Nombre"
        android:inputType="text" />

    <EditText
        android:id="@+id/et_email"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Correo electrónico"
        android:inputType="textEmailAddress" />

    <Button
        android:id="@+id/btn_submit"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Enviar" />
</LinearLayout>


---

3. Configurar la lógica en el archivo Java/Kotlin

Utiliza una librería de red como Retrofit o Volley para enviar los datos a la API REST. En este ejemplo, usaremos Retrofit.

a. Agregar dependencias al archivo build.gradle

En el nivel de módulo (app):

implementation 'com.squareup.retrofit2:retrofit:2.9.0'
implementation 'com.squareup.retrofit2:converter-gson:2.9.0'

b. Crear la interfaz de la API

Define los endpoints de la API REST:

public interface ApiService {
    @POST("formulario")
    Call<Void> enviarFormulario(@Body Formulario formulario);
}

c. Crear la clase del modelo de datos

Define cómo se enviarán los datos:

public class Formulario {
    private String nombre;
    private String correo;

    public Formulario(String nombre, String correo) {
        this.nombre = nombre;
        this.correo = correo;
    }

    // Getters y setters si son necesarios
}

d. Configurar Retrofit y manejar el envío

En tu MainActivity:

import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;
import retrofit2.Retrofit;
import retrofit2.converter.gson.GsonConverterFactory;

public class MainActivity extends AppCompatActivity {

    private EditText etName, etEmail;
    private Button btnSubmit;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        etName = findViewById(R.id.et_name);
        etEmail = findViewById(R.id.et_email);
        btnSubmit = findViewById(R.id.btn_submit);

        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("https://tu-api.com/") // Cambia a tu URL base
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        ApiService apiService = retrofit.create(ApiService.class);

        btnSubmit.setOnClickListener(view -> {
            String nombre = etName.getText().toString();
            String correo = etEmail.getText().toString();

            Formulario formulario = new Formulario(nombre, correo);

            apiService.enviarFormulario(formulario).enqueue(new Callback<Void>() {
                @Override
                public void onResponse(Call<Void> call, Response<Void> response) {
                    if (response.isSuccessful()) {
                        Toast.makeText(MainActivity.this, "Formulario enviado correctamente", Toast.LENGTH_SHORT).show();
                    } else {
                        Toast.makeText(MainActivity.this, "Error al enviar el formulario", Toast.LENGTH_SHORT).show();
                    }
                }

                @Override
                public void onFailure(Call<Void> call, Throwable t) {
                    Toast.makeText(MainActivity.this, "Error de red: " + t.getMessage(), Toast.LENGTH_SHORT).show();
                }
            });
        });
    }
}


---

4. Probar la aplicación

1. Ejecuta tu aplicación en un emulador o dispositivo físico.


2. Llena los campos del formulario y presiona el botón de enviar.


3. Asegúrate de que la API esté en funcionamiento y accesible desde la red.
