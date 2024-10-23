
# Backend Architecture for Android App

## Client Requirements Overview

### 1. **App Functionality:**
- The app includes sending SMS via API, receiving SMS, and sending it back to the server.
- Making outbound calls programmatically.
- Handling inbound SMS responses and integrating with the server for seamless communication.
- Additional features include background task handling (using WorkManager or similar) and monitoring call success/failure.

### 2. **User Permissions:**
- Permissions for CALL_PHONE, SEND_SMS, RECEIVE_SMS are requested in the AndroidManifest.xml.
- Runtime permissions for Android 6.0 and above.

### 3. **Background Services:**
- WorkManager or a foreground service can ensure the app remains responsive.
- The app should be able to ignore battery optimizations if needed.

### 4. **Error Handling:**
- Includes retry mechanisms for failed operations and logging to track issues.

### 5. **Network Stability:**
- The app verifies network stability before initiating calls or SMS operations.

### 6. **Device Compatibility:**
- Tested across multiple Android versions (Android 6.0 and above) and device manufacturers.

### 7. **Security Measures:**
- Encrypting sensitive data during transmission using HTTPS.

### 8. **Notifications:**
- The app may notify users of success or failure of SMS or call operations.

### 9. **Service Restart:**
- Use of BroadcastReceiver to restart services after a device reboot.

### 10. **Performance Metrics:**
- Logs key performance metrics such as call success rate and SMS delivery status.

## Backend Architecture

### Tools and Technologies
1. **NestJS**: The primary framework for building the backend.
2. **Twilio**: For SMS and call handling via API.
3. **Firebase Cloud Messaging (FCM)**: For real-time messaging and notifications.
4. **WebSockets**: For bidirectional communication with the Android app.
5. **TypeORM**: For database interactions.
6. **PostgreSQL**: Database for storing logs, call records, and performance metrics.
7. **NodeMailer**: For sending email notifications (if needed).
8. **Swagger**: API documentation and testing.
9. **JWT**: For secure authentication.
10. **BullMQ**: Task queue for handling background processes like retries.

### System Architecture

```
    +--------------------------+                      +-------------------+
    |   Android App (Client)    | <------ WebSocket -> |  NestJS Backend   |
    | (Handles SMS/Calls/FCM)   |                      | (API Services)    |
    +--------------------------+                      +-------------------+
                 |                                                    |
                 | Firebase Cloud Messaging / HTTP API Calls           |
                 v                                                    v
    +--------------------------+                      +--------------------+
    |      Twilio/Other SMS     | <---- API Calls ---> |  External Call/SMS |
    |      Service Provider     |                      |  Service (Twilio)  |
    +--------------------------+                      +--------------------+
```

### Backend Flow

#### 1. API Gateway
- Handles client requests for sending SMS or making calls.

#### 2. SMS Service
- Interacts with Twilio API to send and receive SMS.
- Logs inbound responses.

#### 3. Call Service
- Manages outbound calls and logs call success/failure.

#### 4. WebSocket Listener
- Listens to real-time commands from the Android app.
- Receives FCM or WebSocket instructions for initiating calls/SMS.

#### 5. Error Handling and Notifications
- Implements retry mechanisms and sends notifications back to the app regarding failures or success.

### Detailed Flow

#### Diagram 1: Backend Service Flow

```
+-----------------------------------------------------+
|             NestJS Backend                          |
+-----------------------------------------------------+
|  1. API Gateway  |  2. SMS Service  |  3. Call Service | 4. WebSocket Listener |
+------------------+------------------+-----------------+------------------------+
        |                     |                     |                      |
        v                     v                     v                      v
+----------------+  +----------------+  +-------------------+  +-----------------+
| Send SMS       |  | Receive SMS     |  | Send Call Command |  | Listen to FCM   |
| (via Twilio)   |  | from Service    |  | (Twilio API)      |  | or WebSocket    |
+----------------+  +----------------+  +-------------------+  +-----------------+
        |                     |                     |                      |
        v                     v                     v                      v
+----------------+  +----------------+  +-------------------+  +-----------------+
| Twilio API     |  | Twilio Callback |  | Twilio API        |  | Push to Mobile  |
+----------------+  +----------------+  +-------------------+  +-----------------+
```

#### Diagram 2: Error Handling and Notifications

```
+--------------------+ 
|   NestJS Backend   | 
+--------------------+
        |
        v
+--------------------+    Failure (SMS/Call)
| Error Handler      | <-------------------+
+--------------------+                     |
        |                                  |
        v                                  v
+--------------------+   Retry if needed   +-----------------+
| Notify User/Admin  |-------------------->| Twilio API      |
+--------------------+                     +-----------------+
```

## Security Measures
- Use HTTPS for secure communication.
- Encrypt sensitive data like phone numbers and SMS content.
- Ensure compliance with data protection regulations.

## Conclusion
This backend architecture provides a scalable solution for handling automated outbound calls, SMS, and background tasks, all while ensuring security and seamless integration with Android apps.
