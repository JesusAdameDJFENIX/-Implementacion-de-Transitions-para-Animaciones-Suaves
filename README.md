Objetivo:
El objetivo de esta tarea es que los estudiantes implementen animaciones suaves utilizando 
las clases de Transitions en Android para mejorar la experiencia de usuario en la navegación 
y la interacción con los elementos visuales (Views) dentro de la aplicación.

VIDEO DE EXPLICACION:

https://youtu.be/EpO2ExHLAyo

1.- activity_main.xml

Este archivo XML define el diseño de la actividad principal. 
Usa un RelativeLayout con un FrameLayout interno, que sirve como contenedor de los fragmentos.

RelativeLayout: permite colocar los elementos en posiciones relativas entre sí o al contenedor.

FrameLayout (@+id/fragment_container): contenedor donde se cargarán FragmentA y FragmentB.


![image](https://github.com/user-attachments/assets/daee72f8-3482-4966-869d-5c72cdb8e6c8)


<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <FrameLayout
        android:id="@+id/fragment_container"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</RelativeLayout>


2.-MainActivity.kt

Define la actividad principal de la aplicación y carga FragmentA por defecto. 
Contiene un método loadFragment que gestiona las transiciones y carga de fragmentos.


loadFragment(fragment: Fragment): carga el fragmento en fragment_container.

Transiciones:
Slide(Gravity.END): desliza el fragmento desde el borde derecho.
Fade(): desvanece el fragmento al salir.

![image](https://github.com/user-attachments/assets/4cb29182-7f2a-4335-be1d-297e7bba3183)

![image](https://github.com/user-attachments/assets/885a5694-43b4-4d53-9c0e-b3c9fc2abd88)


package com.example.transitions

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import androidx.fragment.app.Fragment
import androidx.fragment.app.FragmentTransaction
import android.transition.Fade
import android.transition.Slide
import android.view.Gravity

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)


        if (savedInstanceState == null) {
            loadFragment(FragmentA())
        }
    }

    fun loadFragment(fragment: Fragment) {
        val transaction: FragmentTransaction = supportFragmentManager.beginTransaction()


        val enterTransition = Slide(Gravity.END)
        val exitTransition = Fade()


        fragment.enterTransition = enterTransition
        fragment.exitTransition = exitTransition
        fragment.reenterTransition = enterTransition
        fragment.returnTransition = exitTransition

        transaction.replace(R.id.fragment_container, fragment)
        transaction.addToBackStack(null)
        transaction.commit()
    }
}


3.-FragmentA.kt

Define FragmentA, con enlargeButton para agrandar la imagen y 
navigateButton para cargar FragmentB.


enlargeButton.setOnClickListener: aumenta el tamaño de imageView aplicando ChangeBounds.

imageView.setOnClickListener: abre DetailActivity con una imagen específica.

navigateButton.setOnClickListener: cambia a FragmentB.

![image](https://github.com/user-attachments/assets/a300ced2-3d8f-4a2c-8f4c-0cfab31fc087)

![image](https://github.com/user-attachments/assets/a732f8fd-7f4e-4cf4-b2fb-8df2658450c1)


package com.example.transitions

import android.content.Intent
import android.os.Bundle
import android.transition.ChangeBounds
import android.transition.TransitionManager
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.Button
import android.widget.ImageView
import androidx.fragment.app.Fragment

class FragmentA : Fragment() {

    private lateinit var imageView: ImageView
    private lateinit var enlargeButton: Button
    private lateinit var navigateButton: Button

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        val view = inflater.inflate(R.layout.fragment_a, container, false)
        imageView = view.findViewById(R.id.image_view)
        enlargeButton = view.findViewById(R.id.enlarge_button)
        navigateButton = view.findViewById(R.id.navigate_button)

        enlargeButton.setOnClickListener {

            val transition = ChangeBounds()
            transition.duration = 500
            TransitionManager.beginDelayedTransition(container!!, transition)


            imageView.layoutParams.height = (imageView.layoutParams.height * 1.5).toInt()
            imageView.layoutParams.width = (imageView.layoutParams.width * 1.5).toInt()
            imageView.requestLayout()
        }

        imageView.setOnClickListener {
            val intent = Intent(activity, DetailActivity::class.java)
            intent.putExtra("imageResId", R.drawable.gta_v) 
            startActivity(intent)
        }

        navigateButton.setOnClickListener {
            loadFragment(FragmentB())
        }

        return view
    }

    private fun loadFragment(fragment: Fragment) {
        val transaction = parentFragmentManager.beginTransaction()
        transaction.setCustomAnimations(android.R.anim.fade_in, android.R.anim.fade_out)
        transaction.replace(R.id.fragment_container, fragment)
        transaction.addToBackStack(null)
        transaction.commit()
    }
}


4.- FragmentB.kt

Este es casi idéntico a FragmentA, solo cambia la imagen en DetailActivity.

imageView.setImageResource(imageResId): muestra la imagen recibida.

backButton.setOnClickListener: cierra la actividad y regresa al fragmento anterior.



![image](https://github.com/user-attachments/assets/dbef1c3d-d784-4de7-821f-4c6bce0688fd)

![image](https://github.com/user-attachments/assets/d5636832-d3b6-4605-9061-da5339673be7)


package com.example.transitions

import android.content.Intent
import android.os.Bundle
import android.transition.ChangeBounds
import android.transition.TransitionManager
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.Button
import android.widget.ImageView
import androidx.fragment.app.Fragment

class FragmentB : Fragment() {

    private lateinit var imageView: ImageView
    private lateinit var enlargeButton: Button
    private lateinit var navigateButton: Button

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        val view = inflater.inflate(R.layout.fragment_b, container, false)
        imageView = view.findViewById(R.id.image_view)
        enlargeButton = view.findViewById(R.id.enlarge_button)
        navigateButton = view.findViewById(R.id.navigate_button)

        enlargeButton.setOnClickListener {

            val transition = ChangeBounds()
            transition.duration = 500
            TransitionManager.beginDelayedTransition(container!!, transition)


            imageView.layoutParams.height = (imageView.layoutParams.height * 1.5).toInt()
            imageView.layoutParams.width = (imageView.layoutParams.width * 1.5).toInt()
            imageView.requestLayout()
        }

        imageView.setOnClickListener {
            val intent = Intent(activity, DetailActivity::class.java)
            intent.putExtra("imageResId", R.drawable.zelda_breath) 
            startActivity(intent)
        }

        navigateButton.setOnClickListener {
            loadFragment(FragmentA())
        }

        return view
    }

    private fun loadFragment(fragment: Fragment) {
        val transaction = parentFragmentManager.beginTransaction()
        transaction.setCustomAnimations(android.R.anim.fade_in, android.R.anim.fade_out)
        transaction.replace(R.id.fragment_container, fragment)
        transaction.addToBackStack(null)
        transaction.commit()
    }
}


5.-activity_detail.xml

Diseño de DetailActivity, que contiene una ImageView en pantalla completa y un botón para regresar.


![image](https://github.com/user-attachments/assets/5f9686d2-6fb5-4fd0-b50c-f5a89e68683d)


<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/fullscreen_image"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:scaleType="fitCenter" />

    <Button
        android:id="@+id/back_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Regresar"
        android:layout_gravity="center" />
</LinearLayout>

6.- fragment_a.xml y fragment_b.xml

Ambos archivos son casi idénticos, contienen una ImageView y dos botones (enlarge_button y navigate_button).


ImageView: muestra una imagen inicial.

enlarge_button: botón para agrandar la imagen.

navigate_button: botón para cambiar al otro fragmento.



![image](https://github.com/user-attachments/assets/0386a809-6827-4294-b50e-a2c186e08b5b)


<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/image_view"
        android:layout_width="150dp"
        android:layout_height="150dp"
        android:src="@drawable/gta_v"
        android:layout_gravity="center" />

    <Button
        android:id="@+id/enlarge_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Aumentar tamaño"
        android:layout_gravity="center" />

    <Button
        android:id="@+id/navigate_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Ir a Fragment B"
        android:layout_gravity="center" />
</LinearLayout>


![image](https://github.com/user-attachments/assets/3772ca78-b638-4305-8c2a-6669402cacc9)


<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/image_view"
        android:layout_width="150dp"
        android:layout_height="150dp"
        android:src="@drawable/zelda_breath"
        android:layout_gravity="center" />

    <Button
        android:id="@+id/enlarge_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Aumentar tamaño"
        android:layout_gravity="center" />

    <Button
        android:id="@+id/navigate_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Ir a Fragment A"
        android:layout_gravity="center" />

</LinearLayout>


7.-strings.xml

Define los textos para la aplicacion.

![image](https://github.com/user-attachments/assets/3ed91e35-5bac-4128-91dd-6a0d7273154e)


<resources>
    <string name="app_name">Transitions</string>
    <string name="image_description">Descripción de la imagen</string>
</resources>


Y por ultimo este es el resultado final:


![Grabación-2024-11-03-194322](https://github.com/user-attachments/assets/4dc2c3aa-4937-4136-b00c-bcecd3ed9a78)
