# ALDUS ANDROID SDK
![version](https://img.shields.io/badge/version-v1.1.8-blue)

Aldus SDK is a **Peer-to-peer**, **Feature-rich**, **Realtime** Identity Verification Solution through Video Chat. 

The Video Customer Identification Process (V-CIP) can be integrated into your existing App flow and connects to the Aldus Web Client. 

Available features
- Secure and Live P2P Video Chat with recording
- Screen Sharing
- Face & ID Capture
- Geo-Tagging
- Anti-Spoof Liveliness check

# Table Of Content

- [Prerequisite](#prerequisite)
- [Android SDK Requirements](#android-sdk-requirements)
- [Download](#download)
  - [Using maven repository](#using-maven-repository)
- [Setup](#setup)
  - [Permissions](#permissions)
- [Quick Start](#quick-start)
  - [Invoking the Aldus Sdk](#invoking-the-aldus-sdk)
  - [Handling the result](#handling-the-result)
- [Aldus Result](#aldus-result)
- [Aldus Error Codes](#aldus-error-codes)
- [Aldus Parameters](#aldus-parameters)
- [Help](#help)

## Prerequisite

The Aldus Android SDK communicates with its web counterpart, the Aldus Web Client. Make sure to have integrated the Aldus Web Client so that a Peer-to-peer connection can be established.

You will need a valid license to use the Aldus SDK, which can be obtained by contacting `support@frslabs.com` . Once you have the license , follow the below instructions for a successful integration of Aldus SDK onto your Android Application.

## Android SDK Requirements

**Minimum SDK Version** -  **21**

**Compile SDK Version** - **28**

**Target SDK Version** - **28**  

## Download

#### Using maven repository

Add the following code to your `project` level `build.gradle` file

```groovy
allprojects { 
    repositories { 
       maven { 
            // Maven Url and Credentials for Aldus SDK. 
            url "https://aldus-android.repo.frslabs.space/"                  
            credentials { 
                   username 'repo-username' 
                   password 'repo-password' 
            }
       }
       
        maven {
            // Maven Url and Credentials for Octus Aadhaar Offline SDK.
            url "https://octus-android-aadhaar-offline.repo.frslabs.space/"
            credentials {
                   username 'repo-username' 
                   password 'repo-password' 
            }
        }
        
    }
}
```

After that, add the following code to your `app` level `build.gradle` file

```groovy
// ...

  defaultConfig { 
  
      // ...
      
      ndk { 
          abiFilters "armeabi-v7a", "arm64-v8a", "x86", "x86_64" 
      } 
      
  }

// ...

  compileOptions {
       sourceCompatibility = 1.8
       targetCompatibility = 1.8
  }

// ...
```

And then, add the dependencies
```groovy

// ...

dependencies {
    /* Dependencies for Aldus SDK */ 
    implementation 'com.android.support:appcompat-v7:28.0.0'
    implementation 'com.android.support:design:<version above 23.4.0>'      
    implementation 'com.android.support.constraint:constraint-layout:<version above 1.1.3>'
   
    //Aldus Sdk Dependencies
    implementation 'com.frslabs.android.sdk:aldus:1.1.8' 
    implementation 'org.webrtc:google-webrtc:1.0.30039'
    implementation 'com.squareup.okhttp3:okhttp:4.2.0'
    
    //Octus Aadhaar Offline
    implementation 'com.frslabs.android.sdk.octus:aadhaaroffline:1.0.6'
    implementation 'net.lingala.zip4j:zip4j:1.3.2'
    implementation 'com.google.code.gson:gson:2.8.6'
    
}
```

## Setup

#### Permissions

Aldus uses the required permissions to operate
```xml

    <!--Permissions-->
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

    <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />

    <!--Features-->
    <uses-feature android:name="android.hardware.camera" android:required="true" />
    <uses-feature android:name="android.hardware.camera.front" android:required="true" />
    <uses-feature android:name="android.hardware.microphone" />

```

## Quick Start

#### Invoking the Aldus Sdk

Initialize the `Aldus` instance with the appropriate configurations to invoke the Aldus Sdk

```java
public class MainActivity extends AppCompatActivity implements AldusResultCallback {

    // ...

    /* Enter the Aldus license key here */
    private String ALDUS_LICENSE_KEY = "ENTER_YOUR_LICENSE_KEY_HERE";
    private String OCTUS_AADHAAR_OFFLINE_LICENSE_KEY = "ENTER_YOUR_LICENSE_KEY_HERE";
    private String ALDUS_URL = "ENTER_YOUR_ALDUS_URL_HERE";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button callSdk = findViewById(R.id.call_sdk);
        callSdk.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                /* Invoke the Aldus Sdk */
                callAldusSdk();
            }
        });
    }

    public void callAldusSdk() {

        //Initialize the Aldus Sdk Config object with the appropriate configurations
        AldusConfig aldusConfig = new AldusConfig.Builder()
                .setLicenseKey(ALDUS_LICENSE_KEY)
                .setOctusAadhaarOfflineLicenseKey(OCTUS_AADHAAR_OFFLINE_LICENSE_KEY)
                .setApiCredentials(new AldusApiCredentials(ALDUS_URL))
                .build();

        //Call the Aldus Sdk 
        Aldus aldus = new Aldus(aldusConfig);
        aldus.start(this, this);
        
    }

    // ...

}
```


#### Handling the result

Your activity must implement `AldusResultCallback` to receive the result.

```java
    // ...

    @Override
    public void onAldusSuccess(AldusResult aldusResult) {
        Toast.makeText(this, "SessionId: "+aldusResult.getSessionId(), Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onAldusFailure(AldusErrorCode aldusErrorCode) {
        /* Handle the Aldus Sdk failure result here */
        Toast.makeText(this, "Error " + aldusErrorCode.toString(), Toast.LENGTH_SHORT).show();
    }

    // ...
```

## Aldus Result

The result is obtained through the `AldusResult` object

Given below is the Result classes in brief.
<div>
<table style="width:100%">
 <tr>
 <th bgcolor="#F1F1F1" colspan="3">Public Methods</th>
 </tr>
 <tr>
 <td>String</td>
 <td>getSessionId()</td>
 <td>Returns the Session Id</td>
 </tr>
</table>
</div>

## Aldus Error Codes

Error codes and their meaning are tabulated below

| Label          | Code |Message                 |
| -------------- | ----- |---------------------- |
| ERROR_CODE_PERMISSION | 1000 | Required permissions for Aldus SDK were not granted |
| ERROR_CODE_EXPIRED_LICENSE | 1001 | Aldus SDK License has expired |
| ERROR_CODE_INVALID_LICENSE | 1002 | Invalid Aldus SDK License |
| ERROR_CODE_INVALID_CONFIG | 1003 | Invalid Aldus SDK Config |
| ERROR_CODE_TNC_LENGTH_EXCEEDED | 1004 | Terms and conditions URL should not exceed 120 characters |
| ERROR_CODE_TNC_INVALID_FORMAT | 1005 | Invalid format for terms and conditions URL |
| ERROR_CODE_NETWORK_ERROR | 1100 | Network Error |
| ERROR_CODE_INTERRUPTED | 1200 | Aldus SDK Interrupted |
| ERROR_CODE_DENIED_LOCATION | 1201 | User denied enabling location |
| ERROR_CODE_OUT_OF_COUNTRY | 1202 | User's location was found to be outside the country |
| ERROR_CODE_LOCATION_ERROR | 1203 | Error Determining Location |

## Aldus Parameters

- `setLicenseKey(String aldusLicenseKey)`   ***(Required)***
  
  Accepts the Aldus licence key as a `String`

- `setOctusAadhaarOfflineLicenseKey(String octusAadhaarOfflineLicenseKey)`   ***(Required)***
  
  Accepts the [Octus-Aadhaar-Offline](https://github.com/frslabs/octus-aadhar-offline-android) licence key as a `String`
  
- `setApiCredentials(AldusApiCredentials aldusApiCredentials)`   ***(Required)***
  
  Sets the `AldusApiCredentials` , required for networking inside the Aldus SDK
  
- `setTermsAndConditionsUrl(String termsAndConditionsUrl)`   ***(Optional)***

  Sets the `termsAndConditionsUrl` in the Terms And Conditions Screen. If not set , only the basic text is displayed.

- `skipTermsAndConditionsScreen()`   ***(Optional)***

  Skips the Terms And Conditions Screen. By Default, a basic Terms And Conditions Screen is included in the flow. If `setTermsAndConditionsUrl` is used its value is ignored.

- `skipInstructionScreen()`   ***(Optional)***

  Skips the Instruction Screen. By Default, a basic instruction screen is included in the flow.

## Help
For any queries/feedback , contact us at `support@frslabs.com` 
