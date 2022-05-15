## UML based on plantuml
[http://www.plantuml.com/plantuml/uml](http://www.plantuml.com/plantuml/uml/)

### AuthService events
```puml
@startuml
title Data Streaming events for Auth-service
loop RabbitMQ event - core.users.created(user_id)

AuthService -> AuthService : CreateUser

note left
Создаем нового 
пользователя
end note

activate AuthService
AuthService -> RabbitMQ : send core.users.created(user_id)

RabbitMQ -> TasksService : Handle core.users.created(user_id)

activate TasksService
TasksService -> AuthService : RPC GetUserById(user_id)
AuthService -> TasksService : return User {id, full_name, email}
TasksService -> TasksService : CreateUser
deactivate TasksService

RabbitMQ -> AccountingService : Handle core.users.created(user_id)
activate AccountingService
AccountingService -> AuthService : RPC GetUserById(user_id)
AuthService -> AccountingService : return User {id, full_name, email}
AccountingService -> AccountingService : CreateUser
deactivate AccountingService

deactivate AuthService
end


loop RabbitMQ event - core.users.updated(user_id)

AuthService -> AuthService : UpdateUser

note left
Обновляем данные 
пользователя
end note

activate AuthService
AuthService -> RabbitMQ : send core.users.updated(user_id)

RabbitMQ -> TasksService : Handle core.users.updated(user_id)

activate TasksService
TasksService -> AuthService : RPC GetUserById(user_id)
AuthService -> TasksService : return User {id, full_name, email}
TasksService -> TasksService : UpdateUser
deactivate TasksService

RabbitMQ -> AccountingService : Handle core.users.updated(user_id)
activate AccountingService
AccountingService -> AuthService : RPC GetUserById(user_id)
AuthService -> AccountingService : return User {id, full_name, email}
AccountingService -> AccountingService : UpdateUser
deactivate AccountingService

deactivate AuthService
end

@enduml
```

### TasksService events
```puml
title Data Streaming events for Tasks-service
loop RabbitMQ event - core.tasks.created(task_id)

TasksService -> TasksService : CreateTask

note left
Добавляем новый
таск
end note

activate TasksService
TasksService -> RabbitMQ : send core.tasks.created(task_id)

RabbitMQ -> AccountingService : Handle core.tasks.created(task_id)

activate AccountingService
AccountingService -> TasksService : RPC GetTaskById(task_id)
TasksService -> AccountingService : return Task {id, name, status}
AccountingService -> AccountingService : CreateTask
AccountingService -> AccountingService : CreateTaskCost
deactivate AccountingService

deactivate TasksService
end
```