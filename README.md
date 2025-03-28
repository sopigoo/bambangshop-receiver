# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
##### 1. Why is `RwLock<>` necessary in this case, and why not use `Mutex<>` instead?  
We use `RwLock<>` because it allows multiple reads simultaneously but only one write at a time. This is useful in this case since the list of notifications is read more frequently than it is modified. By using `RwLock<>`, multiple threads can read the notifications without blocking each other, improving performance. If we used `Mutex<>`, every access, whether reading or writing, would require exclusive locking, forcing reads to wait unnecessarily even when no writes are happening. This would reduce efficiency in a read-heavy scenario. 

##### 2. Why does Rust not allow mutating a static variable directly, unlike Java?
Rust enforces strict memory safety rules, which prevent data races and undefined behavior in multi-threaded programs. In Java, a `static` variable can be modified from any thread, potentially leading to race conditions if not handled properly. Rust, however, requires explicit synchronization when modifying shared state. That’s why we use `lazy_static!` along with `RwLock<>` or `Mutex<>` to safely manage mutable static data. This ensures that access to the static variable is properly controlled, preventing issues like data corruption and unexpected behavior in concurrent environments.

#### Reflection Subscriber-2
##### 1. Have you explored things outside of the steps in the tutorial, for example: `src/lib.rs`?  
Yes, I have explored `src/lib.rs`, which provided deeper insight into how the application manages configuration and HTTP requests. From this exploration, I learned that the project utilizes **lazy_static** to initialize a global `reqwest` client and `AppConfig`, ensuring efficient resource management. The `AppConfig` struct is designed to be flexible, with default values that can be overridden by environment variables using `dotenvy` and `Figment`, making configuration management dynamic. The project also defines a custom error response structure (`ErrorResponse`) with Rocket's `Custom` type, allowing structured and consistent error handling. This deeper dive helped me understand the project's architecture beyond just following a tutorial, particularly how global state, configuration, and error handling are structured in Rust with Rocket.

##### 2. How the Observer pattern eases you to plug in more subscribers? How about spawning more than one instance of the main app? Will it still be easy enough to add to the system?  
By implementing the Observer pattern, adding new subscribers becomes easier because the system is flexible and does not require major changes in the main code. A new subscriber can simply register itself through the available API without modifying the publisher’s core functionality. This pattern allows the main app to notify multiple subscribers dynamically, ensuring better scalability. However, when running multiple instances of the main app, data consistency and resource management become crucial. Proper handling is needed to prevent issues like message duplication or conflicts between instances, especially if they share the same resources.

##### 3. Have you tried to make your own tests or enhance documentation on your Postman collection? 
Yes, I have explored writing my own tests and improving documentation in Postman. Creating tests in Postman helps validate API responses automatically, ensuring the endpoints behave as expected under different conditions. Enhancing the documentation by including request parameters, expected responses, and example scenarios makes it easier to share and maintain the API for both personal use and group projects. These features have been particularly useful in structuring and verifying API behavior and reducing debugging time.
