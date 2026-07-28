```mermaid
flowchart LR

subgraph Inbound["Inbound Adapters"]
    CLI["CLI Adapter"]
    REST["REST API"]
end

subgraph App["Application"]

    subgraph InputPorts["Input Ports"]
        TaskInput["TaskInputPort"]
        UserInput["UserInputPort"]
        ReportInput["ReportInputPort"]
    end

    subgraph UseCases["Use Cases"]
        TaskUC["Task Commands / Use Cases"]
        UserUC["User Use Cases"]
        ReportUC["Report Use Cases"]
    end

    DatabaseFacade["DatabaseFacade"]

    subgraph OutputPorts["Output Ports"]
        TaskRepo["TaskRepository"]
        UserRepo["UserRepository"]
        NotificationPort["NotificationPort"]
        ClockPort["ClockPort"]
    end
end

subgraph Domain["Domain"]
    Task["Task"]
    User["User"]
    Priority["Priority"]
    TaskStatus["TaskStatus"]
    NotificationChannel["NotificationChannel"]
    ReminderPolicy["ReminderPolicy"]
end

subgraph Outbound["Outbound Adapters"]
    JsonTaskRepo["JsonTaskRepository"]
    JsonUserRepo["JsonUserRepository"]

    EmailNotifier["EmailNotifier"]
    SmsNotifier["SmsNotifier"]
    PushNotifier["PushNotifier"]

    SystemClock["SystemClock"]
end

subgraph Infra["Infrastructure"]
    JsonStore["JSON File Store"]
    SMTP["SMTP Client"]
    SMS["SMS Gateway"]
    PUSH["Push Gateway"]
    Config["Configuration"]
end

CLI --> TaskInput
CLI --> UserInput
CLI --> ReportInput

REST --> TaskInput
REST --> UserInput
REST --> ReportInput

TaskInput --> TaskUC
UserInput --> UserUC
ReportInput --> ReportUC

TaskUC --> Task
TaskUC --> User
TaskUC --> ReminderPolicy

UserUC --> User

ReportUC --> Task

Task --> Priority
Task --> TaskStatus
Task --> User
User --> NotificationChannel

TaskUC --> DatabaseFacade
UserUC --> DatabaseFacade
ReportUC --> DatabaseFacade

DatabaseFacade --> TaskRepo
DatabaseFacade --> UserRepo

TaskUC --> NotificationPort
TaskUC --> ClockPort
ReportUC --> ClockPort

JsonTaskRepo -. implementiert .-> TaskRepo
JsonUserRepo -. implementiert .-> UserRepo

EmailNotifier -. implementiert .-> NotificationPort
SmsNotifier -. implementiert .-> NotificationPort
PushNotifier -. implementiert .-> NotificationPort

SystemClock -. implementiert .-> ClockPort

JsonTaskRepo --> JsonStore
JsonUserRepo --> JsonStore

EmailNotifier --> SMTP
SmsNotifier --> SMS
PushNotifier --> PUSH

JsonStore --> Config
SMTP --> Config
SMS --> Config
PUSH --> Config
