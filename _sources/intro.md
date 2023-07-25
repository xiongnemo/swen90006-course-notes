# Subject Introduction

These notes are available as a [PDF download](https://swen90006.github.io/notes/_static/SWEN90006-notes.pdf)

Software engineering is about exercising *control* over the software that you are developing. This can be control over the process that is used or the product. In this subject, we focus on the latter: processes and methods for controlling the quality of software. 

Like any other part of software engineering, it is important to follow approaches for software testing. If we do a particularly good job of testing in a project, we want to know what processes and methods we used so that we can do a good job in future projects. If we approach software testing in an ad-hoc manner, it makes it harder to repeat our successes.

This subject is about processes and methods for testing the functional correctness and the security of software. 

## Subject overview

The aim of this subject  is to explore systematic methods for testing software, selecting test inputs to maximise testing coverage and to maximise the likelihood of founding faults.

The subject is largely divided into three  parts:
1. Testing for functional correctness. This is what most people think about when they consider testing: running inputs and observing outputs to see if the software conforms to its requirements. We explore how to select good test inputs, how to measure whether these are sufficient, and how to determine if a test has passed (test oracles)
2. Testing for reliability. This is similar to functional correctness, but  we consider reliability to be a statistical notion of correctnes. We  explore reliability models for software, reliability measures, reliability and testing, and how to use reliability engineering principles to achieve more confidence in the correctness of software.

3. Testing for security. Security testing leaves behind the idea of functional correctness, complete, and related topics, and aims to determine: how secure is our system? Here, we will explore common security vulnerabilities, how to test effectively to detect these, and advanced symbolic methods for detecting these. Security testing is different in that we are not testing requirements directly -- we are looking for generic vulnerabilities.

### Intended learning outcomes
On completion of this subject the student is expected to:

1. Select appropriate methods to build in quality and dependability into software systems
2. Select and apply effective testing techniques for verifying medium and large scale software systems
3. Select and apply measures and models to evaluate the quality, reliability, and security of a software system
4. Work in a team to evaluate and apply different methods for quality, reliability, and security of a software system

### Generic skills

The subject is a technical subject. The aim is to explore the various approaches to building software with specific attributes into a system. The subject will also aim to develop a number of *generic skills*.

On completion of this subject, students should have the following generic skills:
1. Independent research skills, in order to develop your ability to
learn independently, assess what you learn, and apply what you learn.
2. The ability to work as part of a team, following processes and that meet  milestones as part of that team.
3. Interpret and assess of quantitative and qualitative data.
4. Critical analysis skills that will complement and round out what you have learned so far in your degree, and help you to think more deeply about all phases of software development.


## Expectations on Students
The subject has formal teaching through lectures and tutorials/workshops. There are also consultation times for you.

### Lectures and Lecture Notes

These subject notes are the main reference for the subject. They contain notes on all of the major topics contained in the subject, as well as a number of appendices that are included for interest and to round fill out the the material in the notes. *Material in the appendices will not
be examined*

**You are expected to read the subject notes.**

In addition to the subject notes, additional material will be handed out in lectures. Lectures are aimed at providing you with another view of the material in the subject notes. Lectures will primarily consist of problem-solving exercises and discussions, so it is expected that you will gain a deeper understanding of concepts and material from lectures than from the lecture notes.

**You are expected to attend lectures.**

Material from other subjects such as Algorithms and Data Structures, Object-oriented Software Development, and Software Processes and Management is assumed in the subject notes and in the lecture slides. Reference books are reserved for overnight loan at the Engineering library and for certain topics papers may also be handed out in the lectures.

**The staff of SWEN90006 will expect you to be familiar with the
material in the subject notes prior to tutorials and workshops and
certainly for assignments and projects.**

### Tutorials/Workshops

The aim of the tutorials is to create a bridge between theory and practice. The tutorial questions are similar in standard to those in the assignments and the final exam. They are also aimed at getting you to think about the ways in which the theory can be applied to practical evaluation problems.

**The staff of SWEN90006 will expect you to actively engage in tutorials
by conducting tutorial exercises and in engaging in the discussions.**

### Consultations 

Consultations are for you to ask questions about assessment work or to seek a deeper understanding of the subject material. You are encouraged to come to consultations whenever you are having difficulty with the subject matter or the assessment work.

## Assessment 

This subject is a blend of theory and practice. The practical assessment consists of three
assignments and a team project.

| Assessment  |   Topic  |               Type  |       Percentage |
   ---  | --- | --- | ---
|  Assignment 1 |   Software testing  |   Individual |  20% |
|  Assignment 2  | Choice by students |  Individual |  5% |
|  Assignment 3   |Security testing   |  Team     |    25% |
|  Quizzes   |     All topcs     |       Individual  | 15% |
|  Exam       |   All topics      |     Individual |  35% |

### Assignments

There are three  assignments in the subject. One is wotrh 5%, and the other two are worth 20% and 25% respectively.

The  5%  assignment will require you to submit a multiple-choice question with a sample answer into the *PeerWise* repository: <http://peerwise.cs.auckland.ac.nz/>. The aim of these assignments is to encourage deeper thinking about what you have learned in the subject. It also provides the class with a repository of sample questions during revision, and allows class members to peer review and comment on each others questions and answers.

To get the full 5%, you will be required to demonstrate understanding of the subject content, to provide a quality question, and to provide a helpful answer.

The two larger assignments will require you to apply theory learned in lectures to the analysis and testing of programs. There are limits to testing and analysis of programs --- so each of the assignments will ask some technical questions as well as some questions aimed at getting you to explore these limitations.

What we are looking for in assignment work is understanding as well as practical application. You assignments will only be graded above 80% if you demonstrate both understanding and technical ability adequately.

**In short --- we give marks if you show thinking**

The aim of the group project project is for you to build up your skills in security analysis, your evaluation skills, and your research skills. The group project is worth 25%.

The project will be group-based. You are expected to work as part of a team, and will be assessed as a team. This is to ensure that everyone has 'skin in the game' --- which simply means that there is an incentive to work as a team instead of as a group pursuing individual interests.

### Quizzes and exams

We will hold 4 quizzes of the semester: week 3, week 6, week 9, and week 12, worth 3%, 4%, 4%, and 4% respectively. These will be held during the lectures and will test materials from the previous 3 weeks.

The examination is a 2 hour exam based on the lecture, tutorial and assignment material. The tutorial questions are written in the same style and of similar difficulty to the exam questions. Being able to answer the assignment questions, tutorial questions, and project work means that you will be well prepared for the exam.

### Hurdles 

**Important note:** This subject, like many in the school, has a hurdle on both exam and coursework. This means that to pass the subject, you will need to pass both the coursework and the exam.

| Assessment component | Percentage of total | Hurdle |
 --- | --- | ---
| Coursework assignments     |   50%      |        25% |
| Quizzes + Final Examination |  50%       |       25% |
