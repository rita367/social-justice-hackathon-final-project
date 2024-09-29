# social-justice-hackathon-final-project
social justice hackathon
Many African nations are currently facing urgent socio-political challenges. The recent wave of youth-led protests highlights a profound discontent with leadership, economic inequality, and a perceived lack of accountability. my hackathon is on mental health app. Mental Health Support App could have real-world impact, supporting those affected by unrest.
Goal: The app aims to provide mental health resources, real-time communication, and crisis management tools for individuals affected by socio-political unrest, ensuring that all user data is secure and accessible across multiple platforms. For both mobile and web applications.
Core Features:
Real-time Chat:

Objective: Connect users with mental health professionals or peer support groups.
Implementation:
Use WebSockets or Firebase for real-time communication.
Enable group chat, individual counseling sessions, and peer support chat rooms.
Chat history should be stored securely in the database and encrypted for user privacy.
Resource Directory:

Objective: Provide a directory of mental health services available in the user's region.
Implementation:
Dynamic directory that pulls in data based on the user’s location or manual input.
Include categories like free services, licensed professionals, and emergency resources.
Ability to save favorite resources and share them with others.
Crisis Management Tools:

Objective: Provide users with self-help resources during mental health crises.
Implementation:
Breathing exercises, stress-relief practices, and guided meditations embedded in the app.
Emergency hotlines specific to regions or countries.
A panic button that connects the user with a professional instantly or triggers an automated chat to help calm them down.
Data Privacy & Security:

Objective: Protect user conversations, personal information, and mental health data.
Implementation:
End-to-End Encryption (E2EE) for chat messages.
Use JSON Web Tokens (JWT) for secure user authentication.
Ensure all sensitive data is stored securely using PostgreSQL with encryption at rest.
Implement data anonymization techniques for user data analysis if required.
Localization and Accessibility:

Objective: Ensure the app is accessible to users from different regions and cultures.
Implementation:
Multi-language support based on the user's preferences or region.
Cultural customization for mental health resources (i.e., localized mental health services).
Accessibility features: Adjust font size, contrast mode, and audio assistance for visually impaired users.
Platform: Mobile and Web using Flutter
Why Flutter?
Flutter is a cross-platform development tool, which means you can develop both mobile (iOS, Android) and web versions from a single codebase using Dart.
It offers fast rendering and flexibility with design, making it perfect for creating a seamless user experience across devices.
Design User Interface with Figma
Design Considerations:

Clean, minimalist design with contrasting colors and readable fonts to reduce visual strain.
Color palette to convey calmness and trust (e.g., shades of blue, white, and light green).
Ensure high contrast options for visually impaired users.
Ensure all UI elements are responsive across different screen sizes (mobile and web).
Figma Components:

Landing Pages: Log in, Sign-up, or Welcome screen with a focus on accessibility.
Dashboard: Displays a summary of the user's current mental health resources, recent chats, and quick access to crisis management tools.
Chat Screen: Minimalist, clean interface with support for text, emojis, and attachments (images, files).
Settings: Language options, privacy controls, and access to saved resources.
Backend Infrastructure
Key Components
Database: PostgreSQL

Use PostgreSQL as the primary database for storing user data, chat logs, resource directory data, and other relevant information.
Implement encryption to secure sensitive information.
Structure tables for user profiles, chat messages, mental health resources, and emergency contacts.
Authentication: Secure Login with JSON Web Tokens (JWT)

JWT will be used to securely authenticate users.
JWTs allow for secure token-based authentication, which can be stored in a mobile app for automatic login sessions.
Use OAuth if you need to integrate with third-party logins like Google or Facebook.
Messaging: WebSockets or Firebase

WebSockets; implement WebSockets with Django Channels.

API Layer: Django

Django will act as the backend API layer, handling requests from the Flutter frontend.
Use Django’s REST framework (DRF) to build and expose RESTful API endpoints for user login, retrieving mental health resources, and managing chat data.
The backend will also handle database interactions with PostgreSQL and manage real-time features using Django Channels for WebSockets.
the codes for development
1. Setting Up the Flutter Project
2. flutter create mental_health_support-Step 1: Initialize Flutter Project for Web and Mobile
cd mental_health_support
flutter config --enable-web
flutter run -d chrome
Step 2. Login and Signup (Authentication)-implementation form
dependencies:
  flutter_form_builder: ^6.1.0
  dio: ^4.0.6
import 'package:flutter/material.dart';
import 'package:dio/dio.dart';
   step3; create a login screen

class LoginScreen extends StatefulWidget {
  @override
  _LoginScreenState createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {
  final _formKey = GlobalKey<FormState>();
  String email = '';
  String password = '';
  Dio dio = new Dio();

  Future<void> login() async {
    try {
      var response = await dio.post(
        'https://your-api-url.com/api/login/',
        data: {'email': email, 'password': password},
      );
      // Store JWT Token here
      print(response.data['token']);
    } catch (e) {
      print('Error: $e');
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Login')),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              TextFormField(
                decoration: InputDecoration(labelText: 'Email'),
                onChanged: (val) => email = val,
              ),
              TextFormField(
                decoration: InputDecoration(labelText: 'Password'),
                obscureText: true,
                onChanged: (val) => password = val,
              ),
              SizedBox(height: 20),
              ElevatedButton(
                onPressed: () {
                  if (_formKey.currentState!.validate()) {
                    login();
                  }
                },
                child: Text('Login'),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
3. Implementing Real-Time Chat using Firebase
Step 4: Set Up Firebase in the Flutter Project
1.dependencies:
  firebase_core: latest_version
  firebase_database: latest_version
2.initialize
import 'package:firebase_core/firebase_core.dart';
import 'package:flutter/material.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp();
  runApp(MyApp());
}
3. create a chat screen
import 'package:firebase_database/firebase_database.dart';
import 'package:flutter/material.dart';

class ChatScreen extends StatefulWidget {
  @override
  _ChatScreenState createState() => _ChatScreenState();
}

class _ChatScreenState extends State<ChatScreen> {
  final DatabaseReference _chatRef = FirebaseDatabase.instance.ref().child('chats');
  final TextEditingController _controller = TextEditingController();
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Chat')),
      body: Column(
        children: [
          Expanded(
            child: StreamBuilder(
              stream: _chatRef.onValue,
              builder: (context, snapshot) {
                if (!snapshot.hasData) return CircularProgressIndicator();
                var chatData = Map<String, dynamic>.from(snapshot.data.snapshot.value);
                return ListView(
                  children: chatData.values.map((msg) => Text(msg)).toList(),
                );
              },
            ),
          ),
          Padding(
            padding: EdgeInsets.all(8.0),
            child: Row(
              children: [
                Expanded(
                  child: TextField(
                    controller: _controller,
                    decoration: InputDecoration(labelText: 'Enter message'),
                  ),
                ),
                IconButton(
                  icon: Icon(Icons.send),
                  onPressed: () {
                    _chatRef.push().set(_controller.text);
                    _controller.clear();
                  },
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}
4. Crisis Management Tools
Step 5: Implement Crisis Management Screen
import 'package:flutter/material.dart';

class CrisisToolsScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Crisis Tools')),
      body: ListView(
        children: [
          ListTile(
            title: Text('Breathing Exercises'),
            onTap: () {
              // Navigate to breathing exercises page
            },
          ),
          ListTile(
            title: Text('Stress Relief Practices'),
            onTap: () {
              // Navigate to stress relief practices page
            },
          ),
          ListTile(
            title: Text('Emergency Hotlines'),
            onTap: () {
              // Show a list of emergency hotlines
            },
          ),
        ],
      ),
    );
  }
}
5. Backend: Django Setup
Step 6: Set Up Django Backend
Install Django and Django REST framework:
pip install django djangorestframework djangorestframework-jwt psycopg2
django-admin startproject backend
cd backend
Step 7: Set Up JWT Authentication
INSTALLED_APPS = [
    'rest_framework',
    'rest_framework_jwt',
    # other apps...
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
    ],
}

# Configure JWT settings
JWT_AUTH = {
    'JWT_SECRET_KEY': 'your-secret-key',
    'JWT_EXPIRATION_DELTA': datetime.timedelta(hours=24),
}
from rest_framework_jwt.views import obtain_jwt_token

urlpatterns = [
    path('api/login/', obtain_jwt_token),
]
6. Deploying the Flutter App (Mobile and Web)
Step 8: Deploy Web Version to Netlify
Build the web version of the Flutter app
flutter build web
netlify deploy --prod --dir=build/web
Step 9: Deploy Mobile Version
Android: Generate an APK for Android and Xcode.for iOS
 Deploying the Django Backend
Step 10: Deploy Backend to Heroku or DigitalOcean
Install Heroku CLI and push your Django backend
heroku create your-app-name
git push heroku main
Set up PostgreSQL for the database:
bash

heroku addons:create heroku-postgresql:hobby-dev
Add environment variables like JWT secret keys using:
bash

heroku config:set JWT_SECRET_KEY=your-secret-key
