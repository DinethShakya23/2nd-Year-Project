# Student Academic Tracker - Quick Start Guide

## 🚀 Quick Overview

**Student Academic Tracker** is a web-based Learning Management System (LMS) that tracks student performance, manages courses, handles assignments/quizzes, and provides GPA-based rankings.

---

## 🛠️ Technology Stack at a Glance

| Component | Technology |
|-----------|-----------|
| **Backend** | Spring Boot 3.2.5 (Java 21) |
| **Frontend** | HTML/CSS/JavaScript + Bootstrap |
| **Database** | MySQL 8.0+ |
| **Security** | JWT + Spring Security |
| **Build** | Maven |
| **APIs** | Google Sheets, Google Drive, Gmail |

---

## 📋 Prerequisites

```bash
☑️ Java 21 or higher
☑️ Maven 3.6+
☑️ MySQL 8.0+
☑️ Git
```

---

## ⚡ Quick Setup (5 Minutes)

### 1️⃣ Clone & Navigate
```bash
git clone https://github.com/DinethShakya23/2nd-Year-Project.git
cd 2nd-Year-Project
```

### 2️⃣ Configure Database
Edit `Backend/Co original/src/main/resources/application.properties`:
```properties
spring.datasource.username=your_username
spring.datasource.password=your_password
```

### 3️⃣ Start Backend
```bash
cd "Backend/Co original"
mvn spring-boot:run
```
✅ Backend runs on `http://localhost:8090`

### 4️⃣ Deploy Frontend
```bash
cd Frontend
mvn clean package
```
Deploy `target/student_acca.war` to Tomcat or servlet container.

---

## 🎯 Key Features

| Feature | Description |
|---------|-------------|
| 📊 **Performance Tracking** | Monitor grades, GPA, and rankings |
| 📚 **Course Management** | Create/manage courses and materials |
| 📝 **Assignment System** | Submit and grade assignments |
| ✅ **Attendance Tracking** | Mark and view attendance records |
| 🏆 **GPA Ranking** | Automatic student ranking system |
| 📅 **Event Calendar** | Manage academic events and deadlines |
| 🔔 **Notifications** | Real-time alerts for students |
| 🔒 **JWT Security** | Token-based authentication |
| 📧 **Email Integration** | Automated notifications via Gmail |
| ☁️ **Google Sheets** | Export grades to cloud |

---

## 📂 Project Structure

```
2nd-Year-Project/
├── Backend/Co original/     # Spring Boot API (71+ endpoints)
│   ├── src/main/java/      # Java source code
│   │   └── com/project/co/
│   │       ├── controller/ # REST controllers
│   │       ├── service/    # Business logic
│   │       ├── entity/     # 24 JPA entities
│   │       ├── repo/       # Data repositories
│   │       └── config/     # Security config
│   └── src/main/resources/
│       └── application.properties
│
├── Frontend/               # Web application
│   └── src/main/webapp/
│       ├── *.html         # 48 HTML pages
│       └── *.js           # 37 JavaScript files
│
└── data/index.json        # Project metadata
```

---

## 🔑 User Roles

| Role | Capabilities |
|------|--------------|
| **Student** | View courses, submit assignments, check grades, view attendance |
| **Admin/Lecturer** | Manage courses, grade assignments, mark attendance, upload materials |

---

## 🌐 Main Pages

### Student Pages
- 🏠 `home.html` - Student dashboard
- 📖 `my_courses.html` - Enrolled courses
- 📝 `assignments.html` - View/submit assignments
- 📊 `Grades.html` - View grades
- 📈 `Ranking.html` - GPA rankings
- 📅 `calendar.html` - Events calendar

### Admin Pages
- 🏠 `homelec.html` - Lecturer dashboard
- 📚 `manageCourses.html` - Course management
- 📝 `manageAssignment.html` - Assignment management
- ✅ `Attendance.html` - Mark attendance
- 📄 `manageLectureNote.html` - Upload lecture notes

---

## 🔐 Authentication

### Student Login
```
Endpoint: GET /api/v1/user/login_student/{student_id}/{name}
```

### Admin Login
```
Endpoint: GET /api/v1/user/login_lecturer/{lecturer_id}/{password}
```

Both return JWT tokens for subsequent API requests.

---

## 📡 Key API Endpoints

### Course Management
```
POST /api/v1/user/savecourse           # Create course
GET  /api/v1/user/courses/{studentId}  # Get student courses
POST /api/v1/user/enroll                # Enroll in course
```

### Assignment Management
```
POST /api/v1/user/saveAssignment       # Create assignment
POST /api/v1/user/saveStdAssignment    # Submit assignment
GET  /api/v1/user/getfile/{adminId}/{courseId}
```

### Grades & Rankings
```
GET /api/v1/user/print_result/{studentId}   # Get results
GET /api/v1/user/print_rank                 # Get rankings
GET /api/v1/user/average_result/{studentId} # Get average
```

### Attendance
```
POST /api/v1/user/saves_attend              # Record attendance
PUT  /api/v1/user/attend/{studentId}/{courseId}
```

---

## 🗄️ Database Entities (24 Total)

### Core Entities
- **Student** - Student information and credentials
- **Course** - Course details
- **Admin** - Admin/lecturer accounts
- **Assignment** - Assignment definitions
- **StudentAssignment** - Student submissions
- **Quiz** - Quiz details
- **S_result** - Student results
- **S_attend** - Attendance records
- **S_rank** - Student rankings
- **CombinedMarks** - Aggregated grades

### Supporting Entities
- Event, Notification, LectureNote, Department, Instructor, etc.

---

## 🔧 Configuration Files

### Backend Config
- `application.properties` - Database & server config
- `SecurityConfigStudent.java` - Security settings
- `JwtService.java` - JWT token handling
- `pom.xml` - Maven dependencies

### Frontend Config
- `pom.xml` - WAR packaging config
- `Session.js` / `Session_admin.js` - Session management

---

## 🎓 Team

| Name | Email | ID |
|------|-------|-----|
| Kavindu Gajendra | e20016@eng.pdn.ac.lk | E/20/016 |
| Dineth Shakya | e20055@eng.pdn.ac.lk | E/20/055 |
| Janith Madhura | e20231@eng.pdn.ac.lk | E/20/231 |
| Nethmin Tharusha | e20404@eng.pdn.ac.lk | E/20/404 |

**University of Peradeniya** | **Department of Computer Engineering**

---

## 📚 Additional Documentation

📖 **For detailed information, see:**
- `PROJECT_OVERVIEW.md` - Comprehensive project documentation
- `README.md` - Original project README

---

## 🐛 Troubleshooting

### Backend won't start
```bash
# Check MySQL is running
sudo systemctl status mysql

# Check Java version
java -version  # Should be 21+

# Check port 8090 is free
lsof -i :8090
```

### Database connection error
```bash
# Verify MySQL credentials in application.properties
# Ensure database 'project' is created (auto-created on first run)
```

### Frontend pages not loading
```bash
# Ensure WAR is properly deployed
# Check servlet container logs
# Verify frontend connects to backend on port 8090
```

---

## 🚀 Next Steps

1. ✅ Set up the backend
2. ✅ Configure the database
3. ✅ Deploy the frontend
4. 👤 Create admin and student accounts
5. 📚 Add courses
6. 👥 Enroll students
7. 📝 Create assignments
8. 📊 Track performance

---

## 🔗 Useful Links

- 📁 [Repository](https://github.com/cepdnaclk/e20-co227-Student-Academic-Tracker)
- 🌐 [Project Page](https://projects.ce.pdn.ac.lk/co227/e20/Student-Academic-Tracker/)
- 🏛️ [Department](http://www.ce.pdn.ac.lk/)
- 🎓 [University](https://eng.pdn.ac.lk/)

---

## 📞 Support

For issues or questions:
1. Check `PROJECT_OVERVIEW.md` for detailed info
2. Review source code comments
3. Contact team members via email

---

**Happy Tracking! 🎓📊**
