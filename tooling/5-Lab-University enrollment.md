# Part 1:

Create a Maven project to model and test a Universities enrollment of Students for Courses.

- use the maven quickstart archetype
- change the version of JUnit to 4.11+
- add the maven compiler directive to 1.8
- import it into IntelliJ/Eclipse

# Part 2:

Create a Student class with the following:

A private String variable named “name” to store the student’s name
A private integer variable named “studentNumber” that contains the unique ID number for this student
A private String variable named “dob” to store the student’s date of birth
A public constructor Student(String name, int studentNumber, String dob)
Several public get/set methods for all the properties
getName/setName
getStudentNumber/setStudentNumber
getDob/setDob

Part 4: Create a Course class with the following:

A private String variable named “name” to store the course’s name, such as COP3502 etc.
A private arrayList named “students” <Student>.
A private integer variable “capacity” for the maximum number of students allowed in this class
A private static integer variable named numberOfCourses that keeps track of the number of courses that have been created so far
A public constructor Course(String name, int cap)
Several set/set methods for all the properties
getName/setName
getCap/setCap
etc.
A public method enrollStudent/unenrollStudent (Student s) to add/remove s from this course and return true if the student was successfully added/removed, or false if not

# Part 3:

Create a Unit test class to test the requirements:

- Create Course and Student objects,
- Enroll student or drop student to these courses
- printout current enrolled students for specific course.

 

# Solution

<https://github.com/roryp/university>