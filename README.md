# BambangShop Publisher App
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
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. **Do We Need an Interface (Trait) for Subscriber?**</br>

In the Observer Pattern, the Subscriber (or Observer) is typically an interface (trait in Rust) because different concrete types can subscribe and react differently to updates. However, in BambangShop’s case, if all subscribers behave the same way and don’t require polymorphism, a single Subscriber struct is enough. If we anticipate multiple types of subscribers with different behaviors, then defining a trait like SubscriberTrait would be useful.

2. **Is Vec Sufficient or Is DashMap Necessary?**</br>

Since id in Program and url in Subscriber are unique identifiers, a Vec (list) is not the best choice because:

- Searching in a Vec requires an O(n) linear scan, whereas a DashMap (or any HashMap) provides O(1) lookup time.

- DashMap ensures uniqueness of url as a key, avoiding duplicates.

Thus, DashMap (or at least a HashMap) is necessary here for efficient lookups and uniqueness enforcement.

3. **Do We Still Need DashMap, or Can We Use a Singleton Instead?**</br>

Rust enforces thread safety, so if we used a Singleton pattern with a normal HashMap, we would need manual locking mechanisms (e.g., Mutex<HashMap> or RwLock<HashMap>) to ensure safe concurrent access. DashMap already provides built-in thread safety without explicit locks, making it more efficient.

So, while a Singleton can work, DashMap is a better choice in Rust for concurrent environments because it avoids the overhead of frequent locking and unlocking.

#### Reflection Publisher-2

1. **Why separate “Service” and “Repository” from a Model?**</br>

Separating Service and Repository from the Model follows the Separation of Concerns principle. The Repository layer manages data persistence (e.g., interacting with databases), while the Service layer handles business logic (e.g., processing data, applying rules). This separation makes the codebase more modular, testable, and scalable. If everything were inside the Model, it would become tightly coupled, making future changes or scaling difficult.

2. **What happens if we only use the Model?**</br>

If all functionalities (storage, business logic, and interactions) are packed inside the Model, the code will become monolithic and tightly coupled. For example, in the system involving Program, Subscriber, and Notification models:

If Program needs to notify a subscriber, it would directly modify Subscriber and Notification, making dependency management hard.

Any change in one model might require updating all related models.

Testing would be harder, as testing business logic would require database interactions.
Using Service and Repository layers allows independent handling of logic and storage, keeping the models clean and focused.

3. **Postman Exploration and Its Benefits**</br>

Postman is a great tool for testing APIs. It helps in:
- Sending HTTP requests (GET, POST, PUT, DELETE) to test endpoints.
- Managing request collections for easy API testing.
- Viewing responses with status codes to debug API calls.
- Automating tests with scripts to check response structures and correctness.

Features I find helpful:

- Environment Variables (for managing different setups: dev, test, production).
- Mock Servers (to simulate API responses without backend changes).
- Pre-request and Post-response Scripts (to automate API testing workflows).

#### Reflection Publisher-3

1. **Observer Pattern Variation Used**</br>

In this case, the Push Model is used. The NotificationService.notify() method actively pushes notifications to all subscribers whenever an event occurs (e.g., product creation or promotion). The function subscriber.update(payload_clone) sends data directly to each subscriber.

2. **Advantages & Disadvantages of Using Pull Model Instead**</br>

If we used the Pull Model, subscribers would request updates from the publisher instead of receiving them automatically.

- Advantages of Pull Model:
    - Less network overhead if subscribers don’t need frequent updates.
    - More control for subscribers, as they can decide when to fetch data.

- Disadvantages of Pull Model (in this case):
    - Delayed notifications—subscribers might not receive updates immediately.
    - Inefficiency—subscribers may repeatedly check for updates even when there's nothing new.
    - Higher database load—if many subscribers poll frequently, it increases the strain on the system.

3. **Effect of Removing Multi-threading**</br>

Without multi-threading, the notification process would block the main execution flow. This means:
- Notifications would be sent one at a time, making the process slower.
- If a subscriber takes too long to respond, the entire notification system would be delayed.
- The system’s responsiveness would degrade, especially with many subscribers.
