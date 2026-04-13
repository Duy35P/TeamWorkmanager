#  Ứng Dụng Quản Lý Công Việc Nhóm (Team Task Manager)
#NHÓM SỐ 1 -
> **Đề tài 2** - Xây dựng ứng dụng quản lý công việc và dự án theo nhóm với giao diện trực quan và đồng bộ real-time.

##  Mục Lục

- [Tổng quan](#tổng-quan)
- [Công nghệ sử dụng](#công-nghệ-sử-dụng)
- [Kiến trúc dự án](#kiến-trúc-dự-án)
- [Cấu trúc thư mục](#cấu-trúc-thư-mục)
- [Sơ đồ UI Flow](#sơ-đồ-ui-flow)
- [Yêu cầu tính năng chi tiết](#yêu-cầu-tính-năng-chi-tiết)
- [Hướng dẫn các giai đoạn phát triển](#hướng-dẫn-các-giai-đoạn-phát-triển)
- [Hướng dẫn cài đặt và chạy](#hướng-dẫn-cài-đặt-và-chạy)
- [Hướng dẫn Database Schema](#hướng-dẫn-database-schema)

---

##  Tổng Quan

Ứng dụng **Team Task Manager** là một ứng dụng di động được xây dựng bằng **Flutter**, cho phép các nhóm làm việc:

-  Tạo, gán và theo dõi công việc (tasks)
-  Sử dụng **Kanban Board** để quản lý luồng công việc
-  Quản lý thành viên nhóm và phân quyền
-  Theo dõi **Timeline** và **Deadline**
-  Nhận thông báo và cộng tác **real-time**
-  Bình luận và trao đổi trên từng task

---

##  Công Nghệ Sử Dụng

| Thành phần          | Công nghệ                           | Mục đích                             |
| ------------------- | ---------------------------------- | ------------------------------------- |
| **Frontend**        | Flutter + Dart                     | Xây dựng giao diện đa nền tảng        |
| **Backend**         | Firebase                           | Authentication, Database, Storage     |
| **Database**        | Cloud Firestore                    | Lưu trữ dữ liệu NoSQL real-time       |
| **Authentication**  | Firebase Auth                      | Đăng nhập/Đăng ký người dùng          |
| **Storage**         | Firebase Storage                   | Lưu trữ file đính kèm                 |
| **Notifications**   | Firebase Cloud Messaging (FCM)     | Thông báo đẩy                         |
| **State Management**| Provider / Riverpod                | Quản lý trạng thái ứng dụng           |
| **Routing**         | GoRouter                           | Điều hướng màn hình                   |

###  Các Package Cần Thiết (thêm vào `pubspec.yaml`)

```yaml
dependencies:
  flutter:
    sdk: flutter
  
  # Firebase
  firebase_core: ^3.12.1
  firebase_auth: ^5.5.1
  cloud_firestore: ^5.6.5
  firebase_storage: ^12.4.4
  firebase_messaging: ^15.2.4
  
  # State Management
  provider: ^6.1.2
  # hoặc flutter_riverpod: ^2.6.1
  
  # UI Components
  cupertino_icons: ^1.0.8
  google_fonts: ^6.2.1
  flutter_svg: ^2.0.17
  cached_network_image: ^3.4.1
  shimmer: ^3.0.0
  
  # Kanban Board
  drag_and_drop_lists: ^0.4.2
  # hoặc boardview: ^0.2.0
  
  # Date/Time
  intl: ^0.19.0
  table_calendar: ^3.2.0
  
  # Navigation
  go_router: ^14.8.1
  
  # Utils
  uuid: ^4.5.1
  image_picker: ^1.1.2
  file_picker: ^8.1.7
  url_launcher: ^6.3.1
  timeago: ^3.7.0
  
  # Local Storage
  shared_preferences: ^2.3.4
```

---

##  Kiến Trúc Dự Án

Dự án sử dụng kiến trúc **Clean Architecture** kết hợp **MVVM Pattern**:

```
┌───────────────────────────────────────────────┐
│              Presentation Layer               │
│  (Screens, Widgets, ViewModels/Providers)     │
├───────────────────────────────────────────────┤
│              Domain Layer                     │
│  (Models, Repositories Interface, UseCases)   │
├───────────────────────────────────────────────┤
│              Data Layer                       │
│  (Firebase Services, Repository Impl)         │
└───────────────────────────────────────────────┘
```

---

##  Cấu Trúc Thư Mục

```
lib/
├── main.dart                          # Entry point
├── app.dart                           # MaterialApp configuration
│
├── config/                            # Cấu hình
│   ├── routes/
│   │   └── app_router.dart            # Định nghĩa routes (GoRouter)
│   ├── themes/
│   │   ├── app_theme.dart             # Theme chung (light/dark)
│   │   ├── app_colors.dart            # Bảng màu
│   │   └── app_text_styles.dart       # Text styles
│   └── constants/
│       └── app_constants.dart         # Hằng số
│
├── models/                            # Data Models
│   ├── user_model.dart                # Model người dùng
│   ├── project_model.dart             # Model dự án
│   ├── task_model.dart                # Model công việc
│   ├── team_model.dart                # Model nhóm
│   ├── comment_model.dart             # Model bình luận
│   └── notification_model.dart        # Model thông báo
│
├── services/                          # Firebase Services
│   ├── auth_service.dart              # Xử lý authentication
│   ├── firestore_service.dart         # CRUD Firestore
│   ├── storage_service.dart           # Upload/Download files
│   └── notification_service.dart      # FCM notifications
│
├── providers/                         # State Management
│   ├── auth_provider.dart             # Auth state
│   ├── project_provider.dart          # Project state
│   ├── task_provider.dart             # Task state
│   ├── team_provider.dart             # Team state
│   └── theme_provider.dart            # Theme state (dark/light)
│
├── screens/                           # Các màn hình
│   ├── splash/
│   │   └── splash_screen.dart
│   ├── auth/
│   │   ├── login_screen.dart
│   │   ├── register_screen.dart
│   │   └── forgot_password_screen.dart
│   ├── home/
│   │   └── home_screen.dart           # Dashboard chính
│   ├── project/
│   │   ├── project_list_screen.dart
│   │   ├── project_detail_screen.dart
│   │   └── create_project_screen.dart
│   ├── task/
│   │   ├── task_detail_screen.dart
│   │   ├── create_task_screen.dart
│   │   └── edit_task_screen.dart
│   ├── kanban/
│   │   └── kanban_board_screen.dart
│   ├── team/
│   │   ├── team_list_screen.dart
│   │   ├── team_detail_screen.dart
│   │   └── invite_member_screen.dart
│   ├── calendar/
│   │   └── calendar_screen.dart
│   ├── notification/
│   │   └── notification_screen.dart
│   └── profile/
│       ├── profile_screen.dart
│       └── edit_profile_screen.dart
│
├── widgets/                           # Reusable Widgets
│   ├── common/
│   │   ├── custom_button.dart
│   │   ├── custom_text_field.dart
│   │   ├── loading_widget.dart
│   │   ├── error_widget.dart
│   │   └── empty_state_widget.dart
│   ├── task/
│   │   ├── task_card.dart
│   │   ├── task_status_badge.dart
│   │   └── task_priority_indicator.dart
│   ├── project/
│   │   ├── project_card.dart
│   │   └── project_progress_bar.dart
│   ├── kanban/
│   │   ├── kanban_column.dart
│   │   └── kanban_task_card.dart
│   └── team/
│       ├── member_avatar.dart
│       └── member_list_tile.dart
│
└── utils/                             # Tiện ích
    ├── helpers.dart
    ├── validators.dart
    └── date_formatter.dart
```

---

##  Sơ Đồ UI Flow



### Luồng điều hướng tổng quan

```
flowchart TD
    A[ Splash Screen] --> B{Đã đăng nhập?}
    B -->|Chưa| C[ Login Screen]
    B -->|Rồi| F[ Home Dashboard]
    
    C --> D[ Register Screen]
    C --> E[ Forgot Password]
    D --> C
    E --> C
    C -->|Đăng nhập thành công| F
    
    F --> G[ Kanban Board]
    F --> H[ Project List]
    F --> I[ Team Management]
    F --> J[ Calendar View]
    F --> K[ Notifications]
    F --> L[ Profile]
    
    H --> M[ Project Detail]
    M --> G
    M --> N[ Create Task]
    
    G --> O[ Task Detail]
    O --> P[ Edit Task]
    O --> Q[ Comments]
    O --> R[ Attachments]
    
    I --> S[ Member Detail]
    I --> T[Invite Member]
    
    L --> U[Edit Profile]
    L --> V[ Settings]
    V --> W[ Dark Mode Toggle]
```

### Luồng chi tiết từng nhóm màn hình

```
flowchart LR
    subgraph AUTH[" Authentication Flow"]
        direction TB
        A1[Splash] --> A2{Check Auth}
        A2 -->|No Token| A3[Login]
        A2 -->|Has Token| A4[Home]
        A3 -->|Tap Register| A5[Register]
        A3 -->|Tap Forgot| A6[Forgot Password]
        A5 -->|Success| A3
        A6 -->|Email Sent| A3
        A3 -->|Success| A4
    end

    subgraph TASK[" Task Management Flow"]
        direction TB
        B1[Kanban Board] --> B2[Task Card - Drag & Drop]
        B1 --> B3[+ New Task]
        B3 --> B4[Create Task Form]
        B4 --> B1
        B2 --> B5[Task Detail]
        B5 --> B6[Edit Task]
        B5 --> B7[Add Comment]
        B5 --> B8[Attach File]
        B6 --> B5
    end

    subgraph TEAM[" Team Flow"]
        direction TB
        C1[Team List] --> C2[Team Detail]
        C2 --> C3[Member Profile]
        C2 --> C4[Invite via Email/Link]
        C2 --> C5[Change Role]
    end

    AUTH --> TASK
    AUTH --> TEAM
```

### Mô tả các màn hình chính

| # | Màn hình | Mô tả | Thành phần UI chính |
|---|----------|--------|---------------------|
| 1 | **Splash Screen** | Màn hình khởi động, check auth state | Logo app, loading indicator |
| 2 | **Login Screen** | Đăng nhập tài khoản | Email/Password fields, Social login buttons, "Forgot password" link |
| 3 | **Register Screen** | Đăng ký tài khoản mới | Name, Email, Password, Confirm Password fields |
| 4 | **Home Dashboard** | Trang chủ tổng quan | Stats cards (tổng task, hoàn thành, deadline gần), Recent activity, Quick actions |
| 5 | **Kanban Board** | Quản lý task theo cột | 3+ columns (To Do, In Progress, Review, Done), Draggable task cards |
| 6 | **Project List** | Danh sách dự án | Project cards với progress bar, search/filter |
| 7 | **Project Detail** | Chi tiết dự án | Task list, Members, Timeline, Settings |
| 8 | **Task Detail** | Chi tiết công việc | Title, Description, Assignee, Priority, Due date, Comments, Attachments |
| 9 | **Create/Edit Task** | Tạo/sửa công việc | Form fields, Date picker, Member selector, Priority dropdown |
| 10 | **Team Management** | Quản lý nhóm | Member list, Roles, Invite button |
| 11 | **Calendar View** | Lịch deadline | Calendar widget, Task markers, Upcoming deadlines |
| 12 | **Notifications** | Thông báo | Notification list, Read/Unread status |
| 13 | **Profile** | Thông tin cá nhân | Avatar, Name, Email, Stats, Settings |

---

##  Yêu Cầu Tính Năng Chi Tiết

### 1.  Quản Lý Task Cơ Bản (CRUD)

```
Chức năng cần code:
├── Tạo task mới (title, description, priority, due_date)
├── Xem danh sách task (filter theo status, priority, assignee)
├── Xem chi tiết task
├── Cập nhật task (chỉnh sửa thông tin, thay đổi status)
├── Xóa task (soft delete)
├── Gán task cho thành viên (assignee)
└── Đặt mức ưu tiên (Low, Medium, High, Urgent)
```

**Files cần tạo:**
- `lib/models/task_model.dart` - Định nghĩa Task model
- `lib/services/task_service.dart` - CRUD operations với Firestore  
- `lib/providers/task_provider.dart` - State management cho tasks
- `lib/screens/task/create_task_screen.dart` - Form tạo task
- `lib/screens/task/task_detail_screen.dart` - Hiển thị chi tiết
- `lib/screens/task/edit_task_screen.dart` - Form chỉnh sửa
- `lib/widgets/task/task_card.dart` - Widget card hiển thị task

### 2.  Tổ Chức Công Việc

```
Chức năng cần code:
├── Tạo dự án (project) để nhóm các task
├── Phân loại task theo danh mục/nhãn (labels)
├── Gắn tag cho task (color-coded)
├── Tìm kiếm và lọc task (theo project, status, assignee, date)
└── Sắp xếp task (theo priority, due_date, created_date)
```

**Files cần tạo:**
- `lib/models/project_model.dart`
- `lib/services/project_service.dart`
- `lib/providers/project_provider.dart`
- `lib/screens/project/project_list_screen.dart`
- `lib/screens/project/project_detail_screen.dart`
- `lib/screens/project/create_project_screen.dart`
- `lib/widgets/project/project_card.dart`

### 3.  Kanban Board

```
Chức năng cần code:
├── Hiển thị board với các cột (To Do, In Progress, Review, Done)
├── Kéo thả (drag & drop) task giữa các cột
├── Tự động cập nhật status khi kéo thả
├── Tùy chỉnh tên cột
├── Thêm task nhanh từ board
└── Đếm số task mỗi cột
```

**Files cần tạo:**
- `lib/screens/kanban/kanban_board_screen.dart`
- `lib/widgets/kanban/kanban_column.dart`
- `lib/widgets/kanban/kanban_task_card.dart`
- `lib/providers/kanban_provider.dart`

### 4.  Quản Lý Nhóm

```
Chức năng cần code:
├── Tạo nhóm/ workspace
├── Mời thành viên (qua email hoặc link)
├── Phân quyền (Admin, Member, Viewer)
├── Xem danh sách thành viên
├── Xóa thành viên
├── Xem task được gán cho từng thành viên
└── Chuyển quyền admin
```

**Files cần tạo:**
- `lib/models/team_model.dart`
- `lib/models/user_model.dart`
- `lib/services/team_service.dart`
- `lib/providers/team_provider.dart`
- `lib/screens/team/team_list_screen.dart`
- `lib/screens/team/team_detail_screen.dart`
- `lib/screens/team/invite_member_screen.dart`

### 5.  Timeline và Deadline

```
Chức năng cần code:
├── Calendar view hiển thị deadline các task
├── Reminder/nhắc nhở trước deadline
├── Thông báo task quá hạn (overdue)
├── Gantt chart đơn giản (optional)
├── Thống kê tiến độ theo tuần/tháng
└── Widget countdown deadline
```

**Files cần tạo:**
- `lib/screens/calendar/calendar_screen.dart`
- `lib/widgets/common/deadline_countdown.dart`
- `lib/services/notification_service.dart`

### 6.  Cộng Tác Real-time

```
Chức năng cần code:
├── Đồng bộ dữ liệu real-time (Firestore snapshots)
├── Bình luận trên task
├── Thông báo khi task được gán/cập nhật
├── Activity log (ai đã làm gì)
├── Presence indicator (ai đang online)
└── Push notifications (FCM)
```

**Files cần tạo:**
- `lib/models/comment_model.dart`
- `lib/models/activity_model.dart`
- `lib/models/notification_model.dart`
- `lib/services/notification_service.dart`
- `lib/screens/notification/notification_screen.dart`

---

##  Hướng Dẫn Các Giai Đoạn Phát Triển

###  Giai đoạn 1: Thiết lập dự án (Tuần 1)

```
Checklist:
☐ Tạo Flutter project
☐ Cấu hình Firebase (tạo project trên Firebase Console)
☐ Thêm firebase_core, firebase_auth, cloud_firestore vào pubspec.yaml
☐ Chạy flutterfire configure
☐ Thiết lập cấu trúc thư mục (theo sơ đồ trên)
☐ Tạo theme (colors, text styles, dark mode)
☐ Cấu hình GoRouter
☐ Tạo các model classes
```

**Lệnh cài đặt Firebase:**
```bash
# Cài đặt Firebase CLI
npm install -g firebase-tools

# Đăng nhập Firebase
firebase login

# Cài FlutterFire CLI
dart pub global activate flutterfire_cli

# Cấu hình Firebase cho project
flutterfire configure
```

###  Giai đoạn 2: Authentication (Tuần 2)

```
Checklist:
☐ Tạo Splash Screen (check auth state)
☐ Tạo Login Screen (email/password)
☐ Tạo Register Screen
☐ Tạo Forgot Password Screen
☐ Implement AuthService (sign in, sign up, sign out, reset password)
☐ Implement AuthProvider
☐ Tạo auth guard (redirect nếu chưa login)
☐ Test flow: Register → Login → Home → Logout
```

**Code mẫu AuthService:**
```dart
class AuthService {
  final FirebaseAuth _auth = FirebaseAuth.instance;

  // Stream theo dõi auth state
  Stream<User?> get authStateChanges => _auth.authStateChanges();

  // Đăng nhập
  Future<UserCredential> signIn(String email, String password) async {
    return await _auth.signInWithEmailAndPassword(
      email: email, 
      password: password,
    );
  }

  // Đăng ký
  Future<UserCredential> signUp(String email, String password) async {
    return await _auth.createUserWithEmailAndPassword(
      email: email, 
      password: password,
    );
  }

  // Đăng xuất
  Future<void> signOut() async => await _auth.signOut();
}
```

###  Giai đoạn 3: Task CRUD + Kanban Board (Tuần 3-4)

```
Checklist:
☐ Tạo TaskModel (id, title, description, status, priority, assignee, dueDate, ...)
☐ Implement TaskService (CRUD với Firestore)
☐ Implement TaskProvider
☐ Tạo Create Task Screen (form)
☐ Tạo Task Detail Screen
☐ Tạo Edit Task Screen
☐ Tạo TaskCard widget
☐ Tạo Kanban Board Screen (3 cột)
☐ Implement drag & drop giữa các cột
☐ Real-time sync (Firestore snapshots)
```

**Code mẫu TaskModel:**
```dart
class TaskModel {
  final String id;
  final String title;
  final String description;
  final String projectId;
  final String assigneeId;
  final String createdBy;
  final TaskStatus status;      // todo, inProgress, review, done
  final TaskPriority priority;  // low, medium, high, urgent
  final DateTime? dueDate;
  final DateTime createdAt;
  final DateTime updatedAt;
  final List<String> labels;
  final List<String> attachments;
  
  // Constructor, fromJson, toJson, copyWith...
}

enum TaskStatus { todo, inProgress, review, done }
enum TaskPriority { low, medium, high, urgent }
```

###  Giai đoạn 4: Project & Team Management (Tuần 5-6)

```
Checklist:
☐ Tạo ProjectModel
☐ Implement ProjectService
☐ Tạo Project List Screen
☐ Tạo Project Detail Screen (chứa danh sách tasks + members)
☐ Tạo Create Project Screen
☐ Tạo TeamModel, UserModel
☐ Implement TeamService
☐ Tạo Team Management Screen
☐ Implement invite member (email)
☐ Implement role management (Admin, Member, Viewer)
```

###  Giai đoạn 5: Calendar, Notifications, Dashboard (Tuần 7-8)

```
Checklist:
☐ Tạo Calendar Screen (table_calendar)
☐ Hiển thị task markers trên calendar
☐ Tạo Home Dashboard với statistics cards
☐ Implement charts (tasks completed, overdue, etc.)
☐ Tạo Notification Screen
☐ Implement FCM push notifications
☐ Comment system trên task detail
☐ Activity log
```

###  Giai đoạn 6: Polish & Testing (Tuần 9-10)

```
Checklist:
☐ Dark mode support
☐ Responsive design
☐ Loading states (shimmer effect)
☐ Error handling & empty states
☐ Search & filter functionality
☐ Profile screen & settings
☐ Unit tests cho services
☐ Widget tests cho screens chính
☐ Integration tests
☐ Performance optimization
☐ Bug fixes
```

---

##  Hướng Dẫn Cài Đặt và Chạy

### Yêu cầu hệ thống

- Flutter SDK >= 3.10.0
- Dart SDK >= 3.10.0
- Android Studio / VS Code
- Firebase account

### Cài đặt

```bash
# 1. Clone project
git clone <repository-url>
cd doanltdddetai2

# 2. Cài đặt dependencies
flutter pub get

# 3. Cấu hình Firebase
flutterfire configure

# 4. Chạy ứng dụng
flutter run

# 5. Build APK (khi hoàn thành)
flutter build apk --release
```

---

## 🗄 Hướng Dẫn Database Schema (Firestore)

### Collections Structure

```
firestore/
├── users/                          # Collection người dùng
│   └── {userId}/
│       ├── displayName: string
│       ├── email: string
│       ├── photoUrl: string
│       ├── role: string
│       └── createdAt: timestamp
│
├── projects/                       # Collection dự án
│   └── {projectId}/
│       ├── name: string
│       ├── description: string
│       ├── ownerId: string
│       ├── memberIds: array<string>
│       ├── color: string
│       ├── createdAt: timestamp
│       └── updatedAt: timestamp
│
├── tasks/                          # Collection công việc
│   └── {taskId}/
│       ├── title: string
│       ├── description: string
│       ├── projectId: string
│       ├── assigneeId: string
│       ├── createdBy: string
│       ├── status: string          # "todo" | "inProgress" | "review" | "done"
│       ├── priority: string        # "low" | "medium" | "high" | "urgent"
│       ├── dueDate: timestamp
│       ├── labels: array<string>
│       ├── attachments: array<string>
│       ├── order: number           # Thứ tự trong Kanban column
│       ├── createdAt: timestamp
│       └── updatedAt: timestamp
│
├── comments/                       # Collection bình luận
│   └── {commentId}/
│       ├── taskId: string
│       ├── userId: string
│       ├── content: string
│       ├── createdAt: timestamp
│       └── updatedAt: timestamp
│
├── notifications/                  # Collection thông báo
│   └── {notificationId}/
│       ├── userId: string
│       ├── title: string
│       ├── body: string
│       ├── type: string            # "task_assigned" | "comment" | "deadline" | ...
│       ├── referenceId: string
│       ├── isRead: boolean
│       └── createdAt: timestamp
│
└── activities/                     # Collection activity log
    └── {activityId}/
        ├── projectId: string
        ├── userId: string
        ├── action: string          # "created_task" | "updated_status" | ...
        ├── targetType: string
        ├── targetId: string
        ├── details: map
        └── createdAt: timestamp
```

### Firestore Security Rules (cơ bản)

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users - chỉ đọc được profile người khác, chỉ sửa được profile mình
    match /users/{userId} {
      allow read: if request.auth != null;
      allow write: if request.auth.uid == userId;
    }
    
    // Projects - chỉ members mới đọc/ghi được
    match /projects/{projectId} {
      allow read: if request.auth.uid in resource.data.memberIds;
      allow create: if request.auth != null;
      allow update, delete: if request.auth.uid == resource.data.ownerId;
    }
    
    // Tasks - members của project mới truy cập được
    match /tasks/{taskId} {
      allow read, write: if request.auth != null;
    }
  }
}
```

---

##  Tài Liệu Tham Khảo

- [Flutter Documentation](https://docs.flutter.dev/)
- [Firebase for Flutter](https://firebase.google.com/docs/flutter/setup)
- [Cloud Firestore](https://firebase.google.com/docs/firestore)
- [Provider Package](https://pub.dev/packages/provider)
- [GoRouter](https://pub.dev/packages/go_router)
- [Table Calendar](https://pub.dev/packages/table_calendar)
- [Material Design 3](https://m3.material.io/)

---

##  Tác Giả

- **Phan Trần Hữu Duy - Nguyễn Gia Khang**
- Đồ án Lập trình Di động - Đề tài 2

---

