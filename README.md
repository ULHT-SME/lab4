# Lab4

## Description

This hands-on lab introduces students to networking and API integration in Flutter. Students will learn to make HTTP requests, parse JSON data, and display dynamic content from a REST API. By the end of this lab, students will have built a functional app that fetches and displays blog posts from a public API, handling loading states and errors gracefully.

## Learning Objectives

By completing this lab, students will be able to:

- Understand asynchronous programming in Dart using Future, async, and await
- Make HTTP GET requests to REST APIs using the http package
- Parse JSON responses into Dart model classes
- Use FutureBuilder to handle asynchronous data in the UI
- Implement proper loading, success, and error states for network requests
- Navigate to detail screens with data passed from API responses

## Setup Instructions

### 1. Create a New Flutter Project

Open your terminal/command prompt and run:

```bash
flutter create network_blog_app
cd network_blog_app
```

Option 2: Open Android Studio and go to File > New Project > New Flutter Project. Name it lab2. (Skip Step 2)

### 2. Open the project

Open the project folder in your preferred IDE.


### 3. Add HTTP Package

Open `pubspec.yaml` and add the http package under dependencies:

```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^1.1.0
```

Open a terminal in Android Studio > Run to install the package with the following command:

```bash
flutter pub get
```

### 4. Open and Run the Project

Open the project in your IDE and run:

```bash
flutter run
```

or

In Android Studio press the green icon ▶️ with the chosen device (Emulator of Phisical mobile)

---

## Lab Tasks

### Task 1: Understanding Asynchronous Operations (10 minutes)

**Objective:** Learn the basics of async/await and Future in Dart.

**What is Asynchronous Programming?**

In mobile apps, some operations take time to complete (network requests, database queries, file operations). We don't want the UI to freeze while waiting for these operations. Asynchronous programming allows the app to continue running while waiting for these tasks.

**Key Concepts:**
- **Future:** Represents a value that will be available at some point in the future
- **async:** Marks a function as asynchronous
- **await:** Pauses execution until a Future completes

**Your Challenge:**

Replace `main.dart` with this example to understand async operations:

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Network Blog App',
      theme: ThemeData(
        primarySwatch: Colors.indigo,
      ),
      home: AsyncDemoScreen(),
    );
  }
}

class AsyncDemoScreen extends StatelessWidget {
  // Simulated async operation
  Future<String> fetchMessage() async {
    // Simulate network delay
    await Future.delayed(Duration(seconds: 2));
    return 'Hello from the future!';
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Async Demo'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              'Understanding FutureBuilder',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            SizedBox(height: 30),
            FutureBuilder<String>(
              future: fetchMessage(),
              builder: (context, snapshot) {
                // TODO: Handle different states
                // Check snapshot.connectionState and snapshot.hasData
                if (snapshot.connectionState == ConnectionState.waiting) {
                  return CircularProgressIndicator();
                }
                
                if (snapshot.hasError) {
                  return Text('Error: ${snapshot.error}');
                }
                
                if (snapshot.hasData) {
                  return Text(
                    snapshot.data!,
                    style: TextStyle(fontSize: 20),
                  );
                }
                
                return Text('No data');
              },
            ),
          ],
        ),
      ),
    );
  }
}
```

**Test Your Understanding:**
- Run the app and observe the 2-second loading state
- Notice how the UI doesn't freeze during the wait
- Try changing the delay duration

**Key FutureBuilder States:**
- `ConnectionState.waiting` - Still loading
- `ConnectionState.done` - Completed (check hasData or hasError)
- `snapshot.hasData` - Data is available
- `snapshot.hasError` - An error occurred

### Task 2: Create a Data Model (10 minutes)

**Objective:** Create a Dart class to represent blog post data from the API.

**Understanding the API:**

We'll use JSONPlaceholder, a free fake REST API: `https://jsonplaceholder.typicode.com/posts`

Sample JSON response for a single post:
```json
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident",
  "body": "quia et suscipit\nsuscipit recusantae..."
}
```

**Your Challenge:**

Create a new file `lib/models/post.dart`:

```dart
class Post {
  final int userId;
  final int id;
  final String title;
  final String body;

  Post({
    required this.userId,
    required this.id,
    required this.title,
    required this.body,
  });

  // Factory constructor to create a Post from JSON
  factory Post.fromJson(Map<String, dynamic> json) {
    return Post(
      // TODO: Map JSON fields to Post properties
      // Hint: userId: json['userId'],
    );
  }

  // Optional: Convert Post back to JSON
  Map<String, dynamic> toJson() {
    return {
      // TODO: Map Post properties to JSON
      // Hint: 'userId': userId,
    };
  }
}
```

**Complete the Model:**
- Fill in the `fromJson` factory constructor
- Fill in the `toJson` method (optional but good practice)

**Hints:**
- Use `json['fieldName']` to access JSON fields
- Make sure field names match the API response exactly
- Use `as int` or `as String` if you need to cast types

### Task 3: Create an API Service (15 minutes)

**Objective:** Build a service class to handle network requests.

**Your Challenge:**

Create a new file `lib/services/api_service.dart`:

```dart
import 'dart:convert';
import 'package:http/http.dart' as http;
import '../models/post.dart';

class ApiService {
  static const String baseUrl = 'https://jsonplaceholder.typicode.com';

  // Fetch all posts
  Future<List<Post>> fetchPosts() async {
    // TODO: Construct the full URL
    final url = Uri.parse('$baseUrl/posts');
    
    try {
      // TODO: Make HTTP GET request
      final response = await http.get(url);
      
      // TODO: Check if request was successful (status code 200)
      if (response.statusCode == 200) {
        // TODO: Decode JSON response
        // Hint: jsonDecode(response.body) returns a dynamic type
        
        // TODO: Convert JSON list to List<Post>
        // Hint: Use map() and Post.fromJson()
        
        return []; // Replace with actual list
      } else {
        throw Exception('Failed to load posts. Status: ${response.statusCode}');
      }
    } catch (e) {
      throw Exception('Failed to load posts: $e');
    }
  }

  // Fetch a single post by ID
  Future<Post> fetchPostById(int id) async {
    final url = Uri.parse('$baseUrl/posts/$id');
    
    try {
      final response = await http.get(url);
      
      if (response.statusCode == 200) {
        // TODO: Decode JSON and return a single Post
        // Hint: jsonDecode returns Map<String, dynamic> for single objects
        
        return Post(userId: 0, id: 0, title: '', body: ''); // Replace
      } else {
        throw Exception('Failed to load post');
      }
    } catch (e) {
      throw Exception('Failed to load post: $e');
    }
  }
}
```

**Implementation Hints:**

For `fetchPosts()`:
```dart
// Decode JSON
List<dynamic> jsonList = jsonDecode(response.body);

// Convert to List<Post>
List<Post> posts = jsonList.map((json) => Post.fromJson(json)).toList();
```

For `fetchPostById()`:
```dart
// Decode JSON
Map<String, dynamic> json = jsonDecode(response.body);

// Convert to Post
return Post.fromJson(json);
```

**Key Concepts:**
- `http.get()` returns a `Response` object
- `response.statusCode` indicates success (200) or failure
- `jsonDecode()` converts JSON string to Dart objects
- Always handle exceptions for network errors

### Task 4: Display Posts with FutureBuilder (20 minutes)

**Objective:** Create a screen that fetches and displays blog posts.

**Your Challenge:**

Create a new file `lib/screens/posts_list_screen.dart`:

```dart
import 'package:flutter/material.dart';
import '../models/post.dart';
import '../services/api_service.dart';

class PostsListScreen extends StatefulWidget {
  @override
  _PostsListScreenState createState() => _PostsListScreenState();
}

class _PostsListScreenState extends State<PostsListScreen> {
  final ApiService apiService = ApiService();
  late Future<List<Post>> futurePosts;

  @override
  void initState() {
    super.initState();
    // TODO: Initialize futurePosts by calling apiService.fetchPosts()
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Blog Posts'),
        actions: [
          IconButton(
            icon: Icon(Icons.refresh),
            onPressed: () {
              // TODO: Refresh the data by calling setState
              // and reinitializing futurePosts
            },
          ),
        ],
      ),
      body: FutureBuilder<List<Post>>(
        future: futurePosts,
        builder: (context, snapshot) {
          // TODO: Handle loading state
          if (snapshot.connectionState == ConnectionState.waiting) {
            return Center(
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  CircularProgressIndicator(),
                  SizedBox(height: 16),
                  Text('Loading posts...'),
                ],
              ),
            );
          }

          // TODO: Handle error state
          if (snapshot.hasError) {
            return Center(
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  Icon(Icons.error_outline, size: 60, color: Colors.red),
                  SizedBox(height: 16),
                  Text('Error: ${snapshot.error}'),
                  SizedBox(height: 16),
                  ElevatedButton(
                    onPressed: () {
                      // TODO: Retry loading
                    },
                    child: Text('Retry'),
                  ),
                ],
              ),
            );
          }

          // TODO: Handle success state
          if (snapshot.hasData) {
            List<Post> posts = snapshot.data!;
            
            if (posts.isEmpty) {
              return Center(child: Text('No posts found'));
            }

            return ListView.builder(
              itemCount: posts.length,
              itemBuilder: (context, index) {
                Post post = posts[index];
                return Card(
                  margin: EdgeInsets.symmetric(horizontal: 12, vertical: 6),
                  child: ListTile(
                    leading: CircleAvatar(
                      child: Text('${post.id}'),
                      backgroundColor: Colors.indigo,
                    ),
                    title: Text(
                      post.title,
                      style: TextStyle(fontWeight: FontWeight.bold),
                      maxLines: 2,
                      overflow: TextOverflow.ellipsis,
                    ),
                    subtitle: Text(
                      post.body,
                      maxLines: 2,
                      overflow: TextOverflow.ellipsis,
                    ),
                    trailing: Icon(Icons.arrow_forward_ios, size: 16),
                    onTap: () {
                      // TODO: Navigate to detail screen in next task
                      print('Tapped post ${post.id}');
                    },
                  ),
                );
              },
            );
          }

          return Center(child: Text('Something went wrong'));
        },
      ),
    );
  }
}
```

**Update main.dart:**
```dart
import 'package:flutter/material.dart';
import 'screens/posts_list_screen.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Network Blog App',
      theme: ThemeData(
        primarySwatch: Colors.indigo,
      ),
      home: PostsListScreen(),
    );
  }
}
```

**Implementation Hints:**

Initialize future in initState:
```dart
@override
void initState() {
  super.initState();
  futurePosts = apiService.fetchPosts();
}
```

Refresh functionality:
```dart
setState(() {
  futurePosts = apiService.fetchPosts();
});
```

**Test Your App:**
- Run the app and wait for posts to load
- Observe the loading indicator
- Try the refresh button
- Test error handling by turning off internet

### Task 5: Create a Detail Screen (20 minutes)

**Objective:** Navigate to a detail screen to show full post content.

**Your Challenge:**

Create a new file `lib/screens/post_detail_screen.dart`:

```dart
import 'package:flutter/material.dart';
import '../models/post.dart';

class PostDetailScreen extends StatelessWidget {
  final Post post;

  PostDetailScreen({required this.post});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Post Details'),
      ),
      body: SingleChildScrollView(
        padding: EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            // Post ID Badge
            Container(
              padding: EdgeInsets.symmetric(horizontal: 12, vertical: 6),
              decoration: BoxDecoration(
                color: Colors.indigo.shade100,
                borderRadius: BorderRadius.circular(20),
              ),
              child: Text(
                'Post #${post.id}',
                style: TextStyle(
                  color: Colors.indigo.shade700,
                  fontWeight: FontWeight.bold,
                ),
              ),
            ),
            
            SizedBox(height: 16),
            
            // Title
            Text(
              post.title,
              style: TextStyle(
                fontSize: 24,
                fontWeight: FontWeight.bold,
              ),
            ),
            
            SizedBox(height: 8),
            
            // User ID
            Row(
              children: [
                Icon(Icons.person, size: 16, color: Colors.grey),
                SizedBox(width: 4),
                Text(
                  'User ${post.userId}',
                  style: TextStyle(color: Colors.grey[600]),
                ),
              ],
            ),
            
            Divider(height: 32),
            
            // Body
            Text(
              post.body,
              style: TextStyle(
                fontSize: 16,
                height: 1.5,
              ),
            ),
            
            SizedBox(height: 24),
            
            // Action Buttons
            Row(
              children: [
                Expanded(
                  child: ElevatedButton.icon(
                    onPressed: () {
                      // TODO: Add share functionality (demo)
                      ScaffoldMessenger.of(context).showSnackBar(
                        SnackBar(content: Text('Share feature coming soon!')),
                      );
                    },
                    icon: Icon(Icons.share),
                    label: Text('Share'),
                  ),
                ),
                SizedBox(width: 12),
                Expanded(
                  child: OutlinedButton.icon(
                    onPressed: () {
                      // TODO: Add favorite functionality (demo)
                      ScaffoldMessenger.of(context).showSnackBar(
                        SnackBar(content: Text('Added to favorites!')),
                      );
                    },
                    icon: Icon(Icons.favorite_border),
                    label: Text('Favorite'),
                  ),
                ),
              ],
            ),
          ],
        ),
      ),
    );
  }
}
```

**Update PostsListScreen Navigation:**

In `posts_list_screen.dart`, update the ListTile's onTap:

```dart
onTap: () {
  Navigator.push(
    context,
    MaterialPageRoute(
      builder: (context) => PostDetailScreen(post: post),
    ),
  );
},
```

Don't forget to import the detail screen:
```dart
import 'post_detail_screen.dart';
```

**Test the Navigation:**
- Tap on any post in the list
- Verify the detail screen shows full content
- Test the back button
- Try the action buttons

### Task 6: Optional Challenge - Add Comments Feature (15 minutes)

**Objective:** Extend the app to fetch and display comments for each post.

**API Endpoint:** `https://jsonplaceholder.typicode.com/posts/{id}/comments`

**Your Challenge:**

1. Create a `Comment` model class
2. Add a method to `ApiService` to fetch comments
3. Display comments in the detail screen using FutureBuilder

**Comment Model Template:**
```dart
// lib/models/comment.dart
class Comment {
  final int postId;
  final int id;
  final String name;
  final String email;
  final String body;

  Comment({
    required this.postId,
    required this.id,
    required this.name,
    required this.email,
    required this.body,
  });

  factory Comment.fromJson(Map<String, dynamic> json) {
    // TODO: Implement JSON parsing
    return Comment(
      postId: json['postId'],
      // Add other fields...
    );
  }
}
```

**ApiService Method:**
```dart
Future<List<Comment>> fetchComments(int postId) async {
  final url = Uri.parse('$baseUrl/posts/$postId/comments');
  // TODO: Implement similar to fetchPosts()
}
```

**Display Comments in Detail Screen:**
```dart
// Add to PostDetailScreen
SizedBox(height: 24),
Text(
  'Comments',
  style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
),
SizedBox(height: 12),
FutureBuilder<List<Comment>>(
  future: apiService.fetchComments(post.id),
  builder: (context, snapshot) {
    // TODO: Handle loading, error, and data states
  },
)
```

---

## Deliverables

Submit the following:

1. **Complete Flutter project folder** (zip the entire project directory)
2. **Screenshots** showing:
   - Posts list loading state
   - Posts list with data loaded
   - Error state (if you can capture it)
   - Post detail screen
   - Comments section (if completed optional task)
3. **Screen recording** (1-2 minutes) demonstrating:
   - App launching and loading posts
   - Scrolling through the list
   - Tapping a post to view details
   - Using the refresh button
4. **Brief write-up** (300-400 words) answering the reflection questions

---

## Reflection Questions

After completing this lab, reflect on and discuss:

1. **Asynchronous Programming:** Explain the difference between synchronous and asynchronous operations. Why is asynchronous programming essential for mobile app development, particularly for networking?

2. **Error Handling:** What types of errors can occur when making network requests? How did you handle these in your app? What improvements could you make to error handling?

3. **FutureBuilder vs State Management:** Compare using FutureBuilder versus managing network requests with Provider or setState. What are the advantages and disadvantages of each approach?

4. **User Experience:** How do loading states and error messages affect user experience? What strategies did you implement to make waiting for data less frustrating for users?

5. **API Design:** Based on your experience with JSONPlaceholder, what makes a good REST API? Consider aspects like data structure, endpoint naming, and response formats.

---

## Tips and Resources

### Common Issues and Solutions

**HTTP package not found:**
- Run `flutter pub get` after adding http to pubspec.yaml
- Check that you're importing: `import 'package:http/http.dart' as http;`
- Restart your IDE if necessary

**JSON parsing errors:**
- Use online JSON validators to check API response format
- Print the raw response: `print(response.body)`
- Check that your model field names match JSON keys exactly
- Use try-catch blocks around jsonDecode for better error messages

**Network permissions (Android):**
- Android apps need internet permission (usually added by default)
- Check `android/app/src/main/AndroidManifest.xml` has:
```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

**CORS errors (Web):**
- Some APIs may not work with Flutter web due to CORS policies
- JSONPlaceholder should work fine
- For production, ensure your API has proper CORS headers

### Best Practices

- Always handle all three FutureBuilder states: waiting, error, data
- Use meaningful error messages for users
- Implement refresh/retry functionality
- Cache data when appropriate to reduce API calls
- Use const constructors where possible for better performance
- Separate API logic from UI code (use service classes)

---

### Some Additional Learning for the most curious minds 😄

- Try other JSONPlaceholder endpoints (users, albums, photos)
- Implement POST, PUT, DELETE requests
- Add pull-to-refresh functionality using RefreshIndicator
- Implement search functionality
- Add pagination for large datasets
- Cache API responses locally
