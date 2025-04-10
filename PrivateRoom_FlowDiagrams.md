# Private Room - Flow Diagrams

## 1. System Architecture Overview

```mermaid
flowchart TD
    subgraph Client Layer
        MobileApp["Mobile Application"]
        WebApp["Web Application"]
    end
    
    subgraph API Gateway
        APIGateway["API Gateway / Load Balancer"]
    end
    
    subgraph Application Services
        AuthService["Authentication Service"]
        ChatService["Chat Service"]
        LocationService["Location Service"]
        GroupService["Group Service"]
        PaymentService["Payment Service"]
        NotificationService["Notification Service"]
        UserService["User Service"]
    end
    
    subgraph Data Storage
        UserDB[("User Database")]
        ChatDB[("Chat Database")]
        PaymentDB[("Payment Database")]
        GroupDB[("Group Database")]
        LocationDB[("Location Database")]
    end
    
    subgraph External Services
        GeoAPI["Geolocation API"]
        PaymentGateway["Payment Gateway"]
        CloudStorage["Cloud Storage"]
        PushNotification["Push Notification Service"]
    end
    
    MobileApp --> APIGateway
    WebApp --> APIGateway
    
    APIGateway --> AuthService
    APIGateway --> ChatService
    APIGateway --> LocationService
    APIGateway --> GroupService
    APIGateway --> PaymentService
    APIGateway --> NotificationService
    APIGateway --> UserService
    
    AuthService --> UserDB
    ChatService --> ChatDB
    LocationService --> LocationDB
    GroupService --> GroupDB
    PaymentService --> PaymentDB
    UserService --> UserDB
    
    LocationService --> GeoAPI
    PaymentService --> PaymentGateway
    ChatService --> CloudStorage
    NotificationService --> PushNotification
    
    style MobileApp fill:#b5e8f7,stroke:#333,stroke-width:2px
    style WebApp fill:#b5e8f7,stroke:#333,stroke-width:2px
    style APIGateway fill:#f5d6c6,stroke:#333,stroke-width:2px
    style AuthService fill:#d9f7be,stroke:#333,stroke-width:2px
    style ChatService fill:#d9f7be,stroke:#333,stroke-width:2px
    style LocationService fill:#d9f7be,stroke:#333,stroke-width:2px
    style GroupService fill:#d9f7be,stroke:#333,stroke-width:2px
    style PaymentService fill:#d9f7be,stroke:#333,stroke-width:2px
    style NotificationService fill:#d9f7be,stroke:#333,stroke-width:2px
    style UserService fill:#d9f7be,stroke:#333,stroke-width:2px
```

## 2. User Registration and Onboarding Flow

```mermaid
sequenceDiagram
    actor User
    participant App as Private Room App
    participant Auth as Authentication Service
    participant User as User Service
    participant Geo as Location Service
    participant Group as Group Service
    
    User->>App: Open Application
    App->>User: Display Registration/Login Screen
    
    User->>App: Enter Registration Details
    App->>Auth: Validate & Create Account
    Auth->>User: Create User Profile
    Auth-->>User: Verification Email/SMS
    User->>Auth: Confirm Verification
    Auth-->>App: Account Verified
    
    App->>User: Request Personal Information
    User->>App: Provide Personal Information
    App->>User: Update User Profile
    
    App->>User: Request Extended Profile Info
    User->>App: Provide Extended Information
    App->>User: Update User Profile
    
    App->>User: Request Privacy Settings
    User->>App: Configure Privacy Settings
    App->>User: Save Privacy Preferences
    
    App->>Geo: Request Location Permission
    User->>App: Grant Location Permission
    Geo->>App: Location Enabled
    
    Geo->>Group: Get Nearby Groups
    Group->>App: Return Available Groups
    App->>User: Display Group Suggestions
    
    App->>User: Display Profile Completion
    App->>User: Show Onboarding Complete
```

## 3. Chat and Group Functionality Flow

```mermaid
flowchart TD
    Start[User Opens App] --> LoggedIn{Is Logged In?}
    LoggedIn -->|No| Login[Login Process]
    Login --> Dashboard
    LoggedIn -->|Yes| Dashboard[Main Dashboard]
    
    Dashboard --> ChatOptions[Chat Options]
    Dashboard --> NearbyGroups[View Nearby Groups]
    Dashboard --> Settings[User Settings]
    
    ChatOptions --> PrivateChat[Start Private Chat]
    ChatOptions --> GroupChat[Join Group Chat]
    
    PrivateChat --> SelectRecipient[Select Recipient]
    SelectRecipient --> PaymentCheck{Subscription\nActive?}
    
    PaymentCheck -->|Yes| StartChat[Start Chat]
    PaymentCheck -->|No| PaymentOptions[Payment Options]
    PaymentOptions --> PayPerMessage[Pay-per-Message]
    PaymentOptions --> Subscribe[Subscribe]
    PayPerMessage --> StartChat
    Subscribe --> StartChat
    
    NearbyGroups --> LocationCheck{Location\nEnabled?}
    LocationCheck -->|No| EnableLocation[Request Location Permission]
    LocationCheck -->|Yes| ShowNearby[Show Nearby Users & Groups]
    EnableLocation --> ShowNearby
    
    ShowNearby --> JoinProximity[Join Proximity Chat]
    ShowNearby --> FilterGroups[Filter Groups]
    FilterGroups --> JoinProximity
    
    GroupChat --> SelectGroup[Select Group]
    SelectGroup --> GroupType{Group Type?}
    
    GroupType -->|Public| JoinFree[Join Free]
    GroupType -->|Private| SubscriptionCheck{Has Required\nSubscription?}
    
    SubscriptionCheck -->|Yes| JoinPrivate[Join Private Group]
    SubscriptionCheck -->|No| UpgradeOptions[Show Upgrade Options]
    UpgradeOptions --> UpgradeNow[Upgrade Subscription]
    UpgradeNow --> JoinPrivate
    
    JoinFree --> ChatInterface[Chat Interface]
    JoinPrivate --> ChatInterface
    JoinProximity --> ChatInterface
    StartChat --> ChatInterface
    
    ChatInterface --> SendMessage[Send Message]
    ChatInterface --> ShareMedia[Share Media]
    ChatInterface --> ToggleGhost[Toggle Ghost Mode]
    ChatInterface --> ExitChat[Exit Chat]
```

## 4. Payment Processing Flow

```mermaid
sequenceDiagram
    actor User
    participant App as Private Room App
    participant Payment as Payment Service
    participant Gateway as Payment Gateway
    participant UserDB as User Database
    
    User->>App: Select Premium Feature
    App->>User: Show Payment Options
    
    alt Pay-per-Message
        User->>App: Select Pay-per-Message
        App->>Payment: Request Pay-per-Message
        Payment->>App: Show Message Cost
        User->>App: Confirm Payment
    else Subscribe
        User->>App: Select Subscription Plan
        App->>Payment: Request Subscription
        Payment->>App: Show Subscription Details
        User->>App: Confirm Subscription
    end
    
    App->>Gateway: Process Payment
    Gateway->>Payment: Payment Authorization Request
    User->>Gateway: Provide Payment Details
    Gateway->>Payment: Payment Confirmation
    
    Payment->>UserDB: Update User Payment Status
    Payment->>App: Payment Success Notification
    
    App->>User: Confirm Feature Access
    App->>User: Show Receipt
    
    Payment->>User: Send Email Receipt
```

## 5. Group Suggestion Algorithm Flow

```mermaid
flowchart TD
    Start[User Completes Profile] --> LocationCheck{Location\nPermission?}
    
    LocationCheck -->|Yes| GetLocation[Get User's Location]
    LocationCheck -->|No| UseInterests[Use Only Interest Data]
    
    GetLocation --> NearbyGroups[Find Groups within Radius]
    UseInterests --> InterestMatch[Match by Interests Only]
    
    NearbyGroups --> CombineData[Combine Location & Profile Data]
    InterestMatch --> CombineData
    
    CombineData --> AlgorithmProcess[Apply Suggestion Algorithm]
    
    AlgorithmProcess --> CalculateProximity[Calculate Proximity Score]
    AlgorithmProcess --> CalculateInterest[Calculate Interest Match Score]
    AlgorithmProcess --> CalculateProfessional[Calculate Professional Relevance]
    AlgorithmProcess --> CalculateSocial[Calculate Social Connection Score]
    
    CalculateProximity --> WeightScores[Weight and Combine Scores]
    CalculateInterest --> WeightScores
    CalculateProfessional --> WeightScores
    CalculateSocial --> WeightScores
    
    WeightScores --> RankGroups[Rank Groups by Final Score]
    RankGroups --> FilterGroups[Filter & Categorize Results]
    
    FilterGroups --> PublicGroups[Public Groups]
    FilterGroups --> PrivateGroups[Private Groups]
    
    PublicGroups --> PresentResults[Present Group Suggestions]
    PrivateGroups --> PresentResults
    
    PresentResults --> End[Display to User]
``` 