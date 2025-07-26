# OOP-Based-System-Design
## JavaScript Code Implementation :
### Abstract User Class
```
class User {
    constructor(id, name, email) {
        if (new.target === User) {
            throw new Error("Cannot instantiate abstract class User");
        }
        this._id = id;
        this._name = name;
        this._email = email;
    }

    login() {
        console.log(`${this._name} logged in.`);
    }

    viewProfile() {
        console.log(`Name: ${this._name}, Email: ${this._email}`);
    }
}
```
### Student Subclass
```
class Student extends User {
    constructor(id, name, email) {
        super(id, name, email);
        this.enrolledCourses = [];
    }

    enroll(course) {
        course.addStudent(this);
        this.enrolledCourses.push(course);
    }

    uploadAssignment(course, assignment, file) {
        assignment.submit(this, file);
    }

    viewGrades(course) {
        course.assignments.forEach(assign => {
            const grade = assign.submissions.get(this);
            if (grade) console.log(`${assign.title}: ${grade.getScore()} - ${grade.getFeedback()}`);
        });
    }
}
```
### Instructor Subclass
```
class Instructor extends User {
    constructor(id, name, email) {
        super(id, name, email);
        this.createdCourses = [];
    }

    createCourse(title) {
        const course = new Course(Date.now(), title, this);
        this.createdCourses.push(course);
        return course;
    }

    gradeAssignment(assignment, student, score, feedback) {
        const grade = new Grade(score, feedback);
        assignment.assignGrade(student, grade);
    }

    viewSubmissions(assignment) {
        assignment.submissions.forEach((grade, student) => {
            console.log(`Student: ${student._name}, Submission: ${grade.getScore()}`);
        });
    }
}
```
### Course Class
```
class Course {
    constructor(courseId, title, instructor) {
        this.courseId = courseId;
        this.title = title;
        this.instructor = instructor;
        this.students = [];
        this.assignments = [];
    }

    addStudent(student) {
        this.students.push(student);
    }

    addAssignment(assignment) {
        this.assignments.push(assignment);
    }
}
```
### Assignment Class
```
class Assignment {
    constructor(id, title, description) {
        this.id = id;
        this.title = title;
        this.description = description;
        this.submissions = new Map(); // student -> Grade
    }

    submit(student, file) {
        this.submissions.set(student, new Grade(0, "Pending")); // default grade
    }

    assignGrade(student, grade) {
        this.submissions.set(student, grade);
    }
}
```
### Grade Class
```
class Grade {
    constructor(score, feedback) {
        this._score = score;
        this._feedback = feedback;
    }

    getScore() {
        return this._score;
    }

    getFeedback() {
        return this._feedback;
    }
}
```

## Explanation of OOP Principles
1. Abstraction:
User class hides unnecessary details and provides common methods like login() and viewProfile() for both Student and Instructor.

2. Encapsulation:
Private properties (like _score, _name) with public getters (e.g., getScore(), getFeedback()) prevent unauthorized direct access and manipulation.

3. Inheritance:
Student and Instructor classes inherit from User, reusing shared logic and extending functionality as needed.

4. Polymorphism:
Method overriding: both Student and Instructor have different behaviors (methods) relevant to their roles but derived from the same base class.

## SOLID Principles Used
S – Single Responsibility: Each class has one clear responsibility (e.g., Course manages students and assignments).
O – Open/Closed: You can extend the system (e.g., add Admin role) without modifying existing code.
L – Liskov Substitution: Subclasses (Student, Instructor) can be used anywhere a User is expected.
I – Interface Segregation: Implicitly followed by not forcing unused methods on classes.
D – Dependency Inversion: Higher-level modules like Course depend on abstraction (User) not concrete Student/Instructor.
