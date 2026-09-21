
# Ex.No:3 Design an android application Send SMS using Intent.


## AIM:

To create and design an android application Send SMS using Intent using Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Latest Version)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as smsintent and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Send SMS and Display details give in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to create and design an android application Send SMS using Intent.
Developed by: Kanishk Arya
Registeration Number : 212225220047
*/
```
mainacitivity.java
```
package com.example.muiex3;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;
import androidx.core.content.ContextCompat;

import android.Manifest;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.net.Uri;
import android.os.Bundle;
import android.telephony.SmsManager;
import android.util.Log;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MainActivity";
    private static final int SMS_PERMISSION_CODE = 101;
    
    EditText message, phoneNumber;
    Button send;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        message = findViewById(R.id.editTextText);
        phoneNumber = findViewById(R.id.editTextPhone);
        send = findViewById(R.id.button);

        send.setOnClickListener(v -> checkPermissionAndSend());
    }

    private void checkPermissionAndSend() {
        if (ContextCompat.checkSelfPermission(this, Manifest.permission.SEND_SMS) 
                == PackageManager.PERMISSION_GRANTED) {
            sendSMSDirectly();
        } else {
            ActivityCompat.requestPermissions(this, 
                    new String[]{Manifest.permission.SEND_SMS}, SMS_PERMISSION_CODE);
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        if (requestCode == SMS_PERMISSION_CODE) {
            if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                sendSMSDirectly();
            } else {
                Toast.makeText(this, "Permission denied. Using Intent fallback.", Toast.LENGTH_SHORT).show();
                sendSMSViaIntent();
            }
        }
    }

    private void sendSMSDirectly() {
        String msg = message.getText().toString().trim();
        String phoneNo = phoneNumber.getText().toString().trim();

        if (phoneNo.isEmpty()) {
            Toast.makeText(this, "Please enter a phone number", Toast.LENGTH_SHORT).show();
            return;
        }

        try {
            SmsManager smsManager;
            if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.S) {
                smsManager = this.getSystemService(SmsManager.class);
            } else {
                smsManager = SmsManager.getDefault();
            }
            
            if (smsManager != null) {
                smsManager.sendTextMessage(phoneNo, null, msg, null, null);
                Toast.makeText(this, "SMS Sent Successfully", Toast.LENGTH_SHORT).show();
                Log.d(TAG, "SMS sent directly to " + phoneNo);
            } else {
                Log.e(TAG, "SmsManager is null");
                sendSMSViaIntent();
            }
        } catch (Exception e) {
            Log.e(TAG, "Failed to send SMS directly", e);
            Toast.makeText(this, "Failed to send SMS: " + e.getMessage(), Toast.LENGTH_SHORT).show();
            // Fallback to Intent if direct sending fails
            sendSMSViaIntent();
        }
    }

    private void sendSMSViaIntent() {
        String msg = message.getText().toString().trim();
        String phoneNo = phoneNumber.getText().toString().trim();

        if (phoneNo.isEmpty()) {
            Toast.makeText(this, "Please enter a phone number", Toast.LENGTH_SHORT).show();
            return;
        }

        try {
            Intent intent = new Intent(Intent.ACTION_SENDTO);
            intent.setData(Uri.parse("smsto:" + Uri.encode(phoneNo)));
            intent.putExtra("sms_body", msg);
            startActivity(intent);
        } catch (Exception e) {
            Log.e(TAG, "Error starting SMS Intent", e);
            Toast.makeText(this, "Error: " + e.getMessage(), Toast.LENGTH_SHORT).show();
        }
    }
}
```
.xml
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/editTextText"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="24dp"
        android:layout_marginEnd="24dp"
        android:hint="Enter Message"
        android:inputType="textMultiLine"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_marginTop="80dp"/>

    <EditText
        android:id="@+id/editTextPhone"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="24dp"
        android:layout_marginTop="20dp"
        android:layout_marginEnd="24dp"
        android:hint="Enter Phone Number"
        android:inputType="phone"
        app:layout_constraintTop_toBottomOf="@id/editTextText"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"/>

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="30dp"
        android:text="Send SMS"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.52"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/editTextPhone" />

</androidx.constraintlayout.widget.ConstraintLayout>
```
manifest.xml
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <uses-permission android:name="android.permission.SEND_SMS"/>
    <uses-feature android:name="android.hardware.telephony" android:required="false" />

    <queries>
        <intent>
            <action android:name="android.intent.action.SENDTO" />
            <data android:scheme="smsto" />
        </intent>
    </queries>

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Muiex3">
        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

## OUTPUT

<img width="1536" height="815" alt="image" src="https://github.com/user-attachments/assets/064cd868-58fc-4aab-b365-b48d679f929b" />

<img width="1536" height="813" alt="image" src="https://github.com/user-attachments/assets/8163041e-f8af-4d00-96f0-01fdfc30d512" />





## RESULT

Thus a Simple Android Application create and design an android application Send SMS using Intent using Android Studio is developed and executed successfully.
