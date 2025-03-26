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

1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

    In the Observer pattern, defining Subscriber as a trait (or interface) is generally useful for maintaining flexibility and extensibility. However, in the current BambangShop case, a single Subscriber struct is sufficient since all subscribers follow the same structure without variations. If different types of subscribers with unique behaviors were needed in the future, implementing a trait would allow polymorphism and better code maintainability.

2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

    The id in Program and the url in Subscriber serve as unique identifiers. While a Vec (list) could be used to store subscribers, it would require linear searches (O(n)) for lookups, additions, and deletions. Using DashMap, which is a thread-safe concurrent HashMap, ensures O(1) lookups and provides better performance, especially when handling a large number of subscribers. Given that unique identification and quick access are critical, DashMap is the better choice for this implementation.

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

    While the Singleton pattern ensures that there is only one instance of SUBSCRIBERS throughout the application using lazy_static!, it does not inherently handle concurrent access safely. Rust enforces thread safety, meaning that if multiple threads access and modify shared data, we need a mechanism to prevent data races. Using a standard HashMap within a Singleton would require additional synchronization, such as wrapping it with a Mutex or RwLock, which could introduce performance bottlenecks due to locking overhead.

    In contrast, DashMap is specifically designed for concurrent access, allowing multiple threads to read and write to different keys simultaneously with fine-grained locking. This makes it more efficient than a Mutex<HashMap>, where the entire structure would be locked even for independent operations. Therefore, while a Singleton pattern provides a globally accessible instance, it is not a replacement for DashMap in this case. We still need DashMap to ensure efficient and safe concurrent modifications, making it the better choice for managing subscribers in a multi-threaded environment.

#### Reflection Publisher-2

1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

    In the traditional MVC pattern, the Model is responsible for both business logic and data storage. However, separating Service and Repository enhances maintainability, scalability, and modularity. The Repository layer abstracts database interactions, ensuring that the persistence logic is separate from business logic. Meanwhile, the Service layer handles business rules, making the code more organized and reusable. This separation follows the Single Responsibility Principle (SRP), preventing the Model from becoming too complex and tightly coupled to database operations.

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

    If we only use the Model without Service and Repository layers, it would lead to tightly coupled code, making changes more difficult. For example, interactions between Program, Subscriber, and Notification would be handled directly in the Model, leading to excessive dependencies. This would increase complexity, as each Model would need to handle database queries, business logic, and data manipulation in the same place. As the application grows, modifying one part of the system could introduce unintended side effects, making maintenance and debugging more difficult.

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

    Postman is a powerful tool for testing APIs, making it easier to verify whether our endpoints function correctly. In this project, Postman allows us to send requests, inspect responses, and debug API behavior without needing to build a frontend or use curl commands manually. Useful features include collections (organizing API requests), environment variables (for dynamic values), and test scripts (automating request validation). These features are valuable not only for this project but also for future group projects, as they improve collaboration, testing efficiency, and API documentation.
    
#### Reflection Publisher-3

1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

    In this tutorial case, we use the Push Model of the Observer Pattern. The NotificationService::notify method automatically sends updates to all subscribers whenever a relevant event occurs. Subscribers do not request updates but instead receive notifications as soon as a product's status changes. This is evident in the use of the thread::spawn function, which ensures that each subscriber receives the notification immediately without having to request it.

2. What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

    If we were to use the Pull Model instead of the Push Model, the subscribers would need to periodically request updates from the publisher instead of receiving notifications automatically.

    One advantage of the Pull Model is that it gives subscribers more control over when they receive updates, reducing unnecessary notifications if they are not interested in frequent updates. This can also help with load management, as the system does not need to push updates to all subscribers immediately. Additionally, it avoids sending redundant data if the subscriber does not need constant updates.

    However, there are significant disadvantages in this tutorial case. The Pull Model would require each subscriber to implement polling mechanisms, meaning they would need to make repeated requests to check for new updates, increasing network traffic and server load. This approach can also introduce delays in receiving important notifications, as subscribers may not check frequently enough. In contrast, the Push Model ensures that subscribers receive updates in real-time, making it more efficient for event-driven notifications, such as new product updates.

3. Explain what will happen to the program if we decide to not use multi-threading in the notification process.

    If we decide not to use multi-threading in the notification process, the program's performance and responsiveness would be significantly affected. Currently, the program spawns a new thread for each subscriber when sending notifications, allowing multiple notifications to be processed concurrently. This ensures that the main execution flow remains responsive and does not block other operations.

    Without multi-threading, the notification process would become sequential, meaning that each subscriber would be notified one by one in a blocking manner. If there are many subscribers, this could lead to significant delays, especially if some subscribers have slow response times or network issues. The entire system could become sluggish, and if notifications take too long, other parts of the application (such as handling new product requests or managing subscriptions) might also experience delays.