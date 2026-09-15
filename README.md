Ticket Booking System

A console-based Train Ticket Booking System built with Java that demonstrates object-oriented programming, service-layer design, JSON-based local persistence, password hashing, train search, seat management, and Gradle-based project management.

Project type: Java CLI application
Build tool: Gradle
Persistence: Local JSON files
Authentication: BCrypt password hashing
Architecture: Entity → Service → Utility → Local JSON storage

📌 Table of Contents

Overview

Features

Application Flow

Architecture

Project Structure

Technology Stack

Data Model

How Train Search Works

How Seat Booking Works

Authentication and Password Security

Local JSON Database

Prerequisites

Setup and Installation

Running the Application

Using the Application

Gradle Commands

Example Workflow

Important Implementation Notes

Known Limitations

Future Improvements

Learning Outcomes

Author

🚀 Overview

The Ticket Booking System is a Java command-line application designed to simulate the core workflow of a railway reservation system.

The application allows a user to:

Create an account.

Log in using username and password.

Search for trains between two stations.

View train schedules and seat layouts.

Select an available seat.

Persist seat changes to a local JSON file.

View previously stored booking information.

Cancel bookings through the service layer.

Instead of using MySQL or another external database, this project uses JSON files as a lightweight local data store. Jackson is responsible for converting Java objects to and from JSON.

✨ Features

👤 User Management

User signup

User login

Unique UUID-based user identifiers

Password hashing using BCrypt

User data stored in users.json

🚆 Train Management

Load train information from JSON

Search trains using source and destination stations

Validate station order

Display train number and station timings

Maintain train seat layouts

Update train information in the local JSON database

💺 Seat Management

Display a two-dimensional seat layout

Represent an available seat using 0

Represent a booked seat using 1

Validate row and column indexes

Prevent booking an already occupied seat

Persist updated seat availability

🎟️ Ticket Model

The project contains a Ticket entity capable of representing:

Ticket ID

User ID

Source

Destination

Date of travel

Associated train

💾 Local Persistence

The application uses:

users.json
trains.json

as its local data store.

🛠️ Build and Dependency Management

The project uses Gradle and the Gradle Wrapper, allowing the application to be built without requiring a globally installed Gradle installation.

🔄 Application Flow

                    ┌──────────────────────┐
                    │       Start App      │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │    Main Menu / CLI   │
                    └──────────┬───────────┘
                               │
              ┌────────────────┼─────────────────┐
              │                │                 │
              ▼                ▼                 ▼
         Sign Up            Login         Search Trains
              │                │                 │
              ▼                ▼                 ▼
         users.json       Authenticate      TrainService
                                                │
                                                ▼
                                         Matching Trains
                                                │
                                                ▼
                                         Select a Train
                                                │
                                                ▼
                                          View Seats
                                                │
                                                ▼
                                        Select Row/Column
                                                │
                                                ▼
                                         Book the Seat
                                                │
                                                ▼
                                         trains.json

🏗️ Architecture

The project follows a simple layered structure.

┌─────────────────────────────────────────────┐
│              App.java / CLI                 │
│      User interaction & menu handling       │
└──────────────────────┬──────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────┐
│          UserBookingService                 │
│ User operations + booking-related logic    │
└───────────────┬─────────────────┬───────────┘
                │                 │
                ▼                 ▼
┌────────────────────────┐  ┌─────────────────┐
│    UserServiceUtil     │  │   TrainService  │
│ BCrypt hashing/check   │  │ Train search &  │
│                        │  │ seat persistence│
└────────────┬───────────┘  └────────┬────────┘
             │                       │
             ▼                       ▼
      ┌─────────────┐         ┌─────────────┐
      │ users.json  │         │ trains.json │
      └─────────────┘         └─────────────┘

Main layers

Layer

Responsibility

App

Command-line interface and user interaction

entities

Domain models such as User, Train and Ticket

service

Business logic and JSON persistence

util

Supporting utilities such as password hashing

localDB

Local JSON data files

📂 Project Structure

ticketBooking-main/
│
├── app/
│   ├── build.gradle
│   │
│   └── src/
│       ├── main/
│       │   └── java/
│       │       └── ticket/
│       │           └── booking/
│       │               │
│       │               ├── App.java
│       │               │
│       │               ├── entities/
│       │               │   ├── User.java
│       │               │   ├── Train.java
│       │               │   └── Ticket.java
│       │               │
│       │               ├── service/
│       │               │   ├── UserBookingService.java
│       │               │   └── TrainService.java
│       │               │
│       │               ├── util/
│       │               │   └── UserServiceUtil.java
│       │               │
│       │               └── localDB/
│       │                   ├── users.json
│       │                   └── trains.json
│       │
│       └── test/
│           └── java/
│               └── ticket/
│                   └── booking/
│                       └── AppTest.java
│
├── gradle/
│   ├── libs.versions.toml
│   └── wrapper/
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
│
├── gradle.properties
├── gradlew
├── gradlew.bat
├── settings.gradle
└── README.md

🧰 Technology Stack

Technology

Purpose

Java

Core application development

Gradle

Build and dependency management

Jackson Databind

JSON serialization/deserialization

BCrypt

Password hashing and verification

Lombok

Project dependency / boilerplate support

Guava

Utility library

JUnit 4

Testing framework configuration

JSON

Local persistence

Dependencies

The project uses:

implementation libs.guava
implementation 'com.fasterxml.jackson.core:jackson-databind:2.12.6'
implementation 'org.projectlombok:lombok:1.18.22'
implementation 'org.mindrot:jbcrypt:0.4'

🧩 Core Components

1. App.java

App.java is the application's entry point.

It creates the UserBookingService, initializes a Scanner, and provides the command-line menu.

The menu contains:

1. Sign up
2. Login
3. Fetch Bookings
4. Search Trains
5. Book a Seat
6. Cancel my Booking
7. Exit the App

The class is responsible mainly for:

Reading input

Displaying menus

Calling service methods

Showing operation results

This keeps the user interaction separate from most of the business logic.

2. User.java

The User entity represents an application user.

Important fields include:

name
password
hashedPassword
ticketsBooked
userId

A user can therefore be associated with multiple tickets.

The class also provides:

printTickets()

to display the user's stored ticket information.

3. Train.java

The Train entity represents a train and its schedule.

Important fields:

trainId
trainNo
seats
stationTimes
stations

Seat representation

Seats are stored as a two-dimensional list:

[
    [0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0]
]

Where:

0 → Available
1 → Booked

4. Ticket.java

The Ticket entity represents a reservation.

It contains:

ticketId
userId
source
destination
dateOfTravel
train

The method:

getTicketInfo()

generates a readable summary of a ticket.

Example:

Ticket ID: ABC123 belongs to User XYZ
from Bangalore to Delhi on 2023-12-08T18:30:00Z

5. UserBookingService.java

This is the main user-facing service layer.

It handles:

Loading users

Signup

Login verification

Fetching bookings

Train searching

Seat fetching

Seat booking

Booking cancellation logic

The service uses Jackson's ObjectMapper to read and write users.json.

Signup flow

User Input
   ↓
Create User Object
   ↓
Hash Password
   ↓
Add User to List
   ↓
Serialize List
   ↓
users.json

Login flow

Username + Password
        ↓
Find matching user
        ↓
BCrypt password verification
        ↓
Login success / failure

6. TrainService.java

TrainService manages train-related operations.

Responsibilities

Load trains from trains.json

Search trains

Add trains

Update trains

Save modified train data

Train search algorithm

The project stores the ordered stations for each train.

For example:

Bangalore → Jaipur → Delhi

If the user searches:

Source: Bangalore
Destination: Delhi

the route is valid because:

index(Bangalore) < index(Delhi)

If the user searches:

Source: Delhi
Destination: Bangalore

the route is rejected because the station order is reversed.

This logic is implemented through:

validTrain(train, source, destination)

🔐 Authentication and Password Security

Passwords are processed using BCrypt.

The utility class:

UserServiceUtil.java

provides two operations:

hashPassword(String plainPassword)

and:

checkPassword(String plainPassword, String hashedPassword)

Password flow

Plain Password
      │
      ▼
 BCrypt.hashpw()
      │
      ▼
Hashed Password
      │
      ▼
Stored in users.json

During authentication:

Entered Password
      │
      ▼
BCrypt.checkpw()
      │
      ▼
Compare with stored hash
      │
      ├── Match → Login success
      │
      └── No Match → Login failure

⚠️ Important: The repository's sample users.json contains demo user records, including plaintext password fields. Do not place real credentials or personal passwords in this file before making the repository public.

💾 Local JSON Database

The project does not require MySQL, PostgreSQL, MongoDB, or another external database.

Instead, it uses:

app/src/main/java/ticket/booking/localDB/

users.json

Stores user information and their stored tickets.

Conceptually:

[
  {
    "name": "username",
    "password": "demo-password",
    "hashed_password": "bcrypt-hash",
    "tickets_booked": [],
    "user_id": "uuid"
  }
]

trains.json

Stores:

Train ID

Train number

Seat matrix

Station timings

Station order

Example:

{
  "train_id": "bacs",
  "train_no": 12345,
  "seats": [
    [0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0]
  ],
  "stations": [
    "bangalore",
    "jaipur",
    "delhi"
  ]
}

🖥️ Prerequisites

Install the following:

JDK 8 or compatible Java version

Git

VS Code / IntelliJ IDEA / Eclipse (optional)

Internet connection for the first Gradle dependency download

The project includes the Gradle Wrapper, so you do not need to install Gradle globally.

Check Java:

java --version

Check Git:

git --version

⚙️ Setup and Installation

1. Clone the repository

git clone https://github.com/devkaran30/ticket-booking-system.git

Move into the project:

cd ticket-booking-system

2. Build the project

Windows

.\gradlew.bat build -x test

Linux / macOS

./gradlew build -x test

The -x test option is useful with the current starter test configuration because the generated AppTest expects an App.getGreeting() method that is not part of the application.

▶️ Running the Application

Windows

Run:

.\gradlew.bat :app:run

Linux / macOS

Run:

./gradlew :app:run

You should see:

Running Train Booking System

Choose option
1. Sign up
2. Login
3. Fetch Bookings
4. Search Trains
5. Book a Seat
6. Cancel my Booking
7. Exit the App

🎮 Using the Application

Step 1 — Sign Up

Select:

1

Enter:

Username
Password

A new user is created with:

Username

UUID

BCrypt password hash

Empty booking list

The user information is persisted to:

users.json

Step 2 — Login

Select:

2

Enter the username and password.

The stored BCrypt hash is checked against the entered password.

Step 3 — Search Trains

Select:

4

Enter a source and destination.

For the sample train data, valid examples include:

bangalore → jaipur
bangalore → delhi
jaipur → delhi

The station order matters.

For example:

delhi → bangalore

is not considered a valid route for a train whose station order is:

bangalore → jaipur → delhi

Step 4 — Select a Train

After searching, the application displays:

Train id
Station timings

Select the train using its displayed index.

Step 5 — View Seats

Select:

5

The application displays a seat matrix.

Example:

0 0 0 0 0 0
0 0 0 0 0 0
0 0 0 0 0 0
0 0 0 0 0 0

Step 6 — Book a Seat

Enter:

Row
Column

The service checks:

Whether the row exists.

Whether the column exists.

Whether the seat is available.

If the value is:

0

the seat is changed to:

1

and the updated train is persisted to trains.json.

🔄 Seat Booking Logic

The core booking logic follows this pattern:

Receive train + row + column
             │
             ▼
      Validate row index
             │
             ▼
     Validate column index
             │
             ▼
      Is seat value == 0?
          /         \
        YES         NO
         │           │
         ▼           ▼
 Set value to 1   Reject booking
         │
         ▼
 Update Train
         │
         ▼
 Save trains.json

This demonstrates basic validation and state persistence without requiring a relational database.

📦 Gradle Commands

Build

.\gradlew.bat build -x test

Run

.\gradlew.bat :app:run

View Gradle tasks

.\gradlew.bat tasks

Stop Gradle daemons

.\gradlew.bat --stop

Clean generated build files

.\gradlew.bat clean

If Windows reports that files inside app\build are locked, stop the Gradle daemon first with .\gradlew.bat --stop.

🧪 Testing

The project contains a generated JUnit test:

app/src/test/java/ticket/booking/AppTest.java

The current test is the default Gradle starter test and expects:

App.getGreeting()

The application does not currently define that method.

Therefore, the current project can be built with:

.\gradlew.bat build -x test

A future improvement is to replace the generated test with tests for actual application behavior.

🧭 Example End-to-End Workflow

A typical user journey is:

Start
  │
  ▼
Sign Up
  │
  ▼
Login
  │
  ▼
Search Trains
  │
  ├── Source: bangalore
  └── Destination: delhi
  │
  ▼
Select Train
  │
  ▼
Display Seat Matrix
  │
  ▼
Choose Available Seat
  │
  ▼
Validate Seat
  │
  ▼
Book Seat
  │
  ▼
Persist Updated Train
  │
  ▼
trains.json

⚠️ Important Implementation Notes

This repository is a learning/project implementation, rather than a production-ready railway reservation platform.

The code currently demonstrates the core concepts but still has areas that should be strengthened before being treated as a complete booking system.

Current areas to improve

Login should explicitly reject invalid credentials before allowing protected operations.

The selected train should persist correctly between menu iterations.

Search results should be checked for an empty list before selecting a train.

Train selection should use a user-friendly 1-based index while converting it safely to the internal 0-based list index.

Booking a seat currently updates train availability; a complete reservation flow should also create and persist a Ticket.

Ticket cancellation should persist the modified user data.

Signup should prevent duplicate usernames.

User input should be validated instead of assuming every input has the expected type.

The application should avoid creating multiple Scanner objects for System.in.

File paths should be centralized/configurable rather than being hard-coded.

Exceptions should be logged with meaningful error messages instead of silently returning empty results.

The current sample JSON data should be replaced with sanitized demo data before publishing publicly.

🚧 Known Limitations

1. Local JSON storage

JSON files are suitable for learning and small demonstrations but are not appropriate for concurrent production bookings.

2. No real database

There is currently no:

MySQL

PostgreSQL

MongoDB

JPA/Hibernate

integration.

3. No payment system

The application does not process payments.

4. No real-time concurrency control

Two users could theoretically attempt to modify the same train data simultaneously.

5. Limited booking lifecycle

A production reservation system would normally include:

Search
→ Availability
→ Temporary Seat Hold
→ Payment
→ Booking Confirmation
→ Ticket Generation
→ Cancellation
→ Refund

The current project focuses primarily on the core Java implementation and local persistence.

🔮 Future Improvements

The project can be evolved significantly.

🗄️ Database

Replace JSON persistence with:

MySQL / PostgreSQL
        ↓
Spring Data JPA
        ↓
Hibernate

Possible tables:

users
trains
stations
tickets
bookings

🌐 Backend API

Convert the CLI application into a Spring Boot REST API.

Possible endpoints:

POST   /api/auth/signup
POST   /api/auth/login

GET    /api/trains/search
GET    /api/trains/{id}/seats

POST   /api/bookings
GET    /api/bookings
DELETE /api/bookings/{id}

🔐 Better Authentication

Future versions could use:

Spring Security

JWT

Role-based authorization

Refresh tokens

Secure password storage

Session management

💳 Payment Integration

Add a payment workflow:

Seat Selection
      ↓
Booking Hold
      ↓
Payment
      ↓
Payment Success
      ↓
Ticket Confirmation

🎟️ Ticket Generation

Generate:

Booking ID

PNR

Passenger information

Train information

Seat number

Journey date

Source and destination

QR code

🧵 Concurrency Handling

For a real booking system, seat booking should be protected against race conditions.

Potential technologies:

Database transactions

Optimistic locking

Pessimistic locking

Distributed locks

Redis

🖥️ Frontend

A web or mobile frontend could be added using:

React
       ↓
Spring Boot REST API
       ↓
MySQL / PostgreSQL

🎓 Learning Outcomes

This project provides practical exposure to:

Java

Classes and objects

Encapsulation

Collections

Interfaces/service-style separation

Exception handling

UUID generation

Stream API

Optional

File I/O

Software Design

Entity modeling

Service-layer separation

Utility classes

Separation of concerns

Basic validation

State management

Data Handling

JSON serialization

JSON deserialization

Jackson ObjectMapper

Local persistence

Security

BCrypt password hashing

Password verification

Separation of plaintext input from stored hashes

Build Tools

Gradle

Gradle Wrapper

Dependency management

Java toolchains

Version Control

Git

GitHub

.gitignore

Repository management

🛣️ Development Roadmap

                CURRENT PROJECT
                      │
                      ▼
              Console Application
                      │
                      ▼
             JSON Local Storage
                      │
          ┌───────────┴───────────┐
          ▼                       ▼
   Improve Validation       Complete Tickets
          │                       │
          └───────────┬───────────┘
                      ▼
                MySQL Database
                      │
                      ▼
                Spring Boot API
                      │
                      ▼
              Spring Security
                      │
                      ▼
               React Frontend
                      │
                      ▼
           Production Architecture

👨‍💻 Author

Dev Karan

GitHub:

github.com/devkaran30

Project repository:

github.com/devkaran30/ticket-booking-system

⭐ Project Philosophy

This project is built as a practical Java learning application, with a focus on understanding how a real booking workflow can be broken into:

User Interaction
      ↓
Business Logic
      ↓
Domain Entities
      ↓
Persistence
      ↓
Stored Application State

The next natural step is to move the same business logic from a command-line application with JSON persistence into a Spring Boot + REST API + relational database architecture.
