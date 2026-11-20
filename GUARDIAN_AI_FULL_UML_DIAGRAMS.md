# GUARDIAN-GO AI: Complete UML Diagrams & Architectural Specifications
**Unified Modeling Language (UML 2.5) Documentation for Academic Report and Production Engineering**

---

## Table of Contents
1. [Use Case Diagram & Actor Specifications](#1-use-case-diagram)
2. [Comprehensive System Class Diagram](#2-system-class-diagram)
3. [Sequence Diagrams](#3-sequence-diagrams)
   - [3.1 Autonomous Multimodal Incident Detection & Dispatch](#31-autonomous-multimodal-incident-detection--dispatch)
   - [3.2 Periodic Location Tracking & Haversine Geofencing](#32-periodic-location-tracking--haversine-geofencing)
   - [3.3 Zero-Connectivity Network Failover & SMS Gateway](#33-zero-connectivity-network-failover--sms-gateway)
4. [State Machine Diagram (Incident Lifecycle & Sensor Sentinel)](#4-state-machine-diagram)
5. [Activity Diagram (Autonomous Sensing, Triage & Escalation Workflow)](#5-activity-diagram)
6. [Component Diagram (Software Subsystems & Interfaces)](#6-component-diagram)
7. [Deployment Diagram (Physical Nodes & Execution Environments)](#7-deployment-diagram)
8. [Entity-Relationship (ER) Diagram (Database Schema)](#8-entity-relationship-er-diagram)

---

## 1. Use Case Diagram

The use case diagram illustrates the functional interactions between four key actors:
- **Tourist**: The primary mobile end-user.
- **Autonomous AI Sentinel**: On-device background sensing and anomaly inference system.
- **Administrator / Police Dispatcher**: First responders monitoring the live emergency HUD.
- **External Gateway Services**: Firebase Authentication and Cellular SMS/Twilio Gateway.

```mermaid
flowchart LR
    Tourist(("👤 Tourist"))
    AISentinel(("🤖 AI Sentinel (Sensors)"))
    Admin(("👮 Admin / Dispatcher"))
    ExtServices(("☁️ External Gateways\n(Firebase, Twilio)"))

    subgraph GuardianGoSystem["Guardian-Go AI Safety Platform"]
        direction TB
        UC1(["UC-1: Authenticate via Firebase"])
        UC2(["UC-2: Stream Live GPS Location"])
        UC3(["UC-3: View Live Map & Zones"])
        UC4(["UC-4: Manual One-Tap SOS"])
        UC5(["UC-5: Cancel Alarm via Safe PIN"])
        
        UC6(["UC-6: Continuous Acoustic Monitoring"])
        UC7(["UC-7: Kinematic Impact / Fall Sensing"])
        UC8(["UC-8: Spatial Trajectory Anomaly Check"])
        UC9(["UC-9: Covert Visual & Audio Capture"])
        UC10(["UC-10: Autonomous Multi-Sensor Triage"])
        UC11(["UC-11: Dispatch Silent / Audible SOS"])
        
        UC12(["UC-12: Monitor Live Tourist Fleet HUD"])
        UC13(["UC-13: Acknowledge & Manage SOS Alerts"])
        UC14(["UC-14: Configure Safe & Danger Geofences"])
        UC15(["UC-15: Trigger Offline SMS Failover"])
    end

    %% Tourist Interactions
    Tourist --> UC1
    Tourist --> UC2
    Tourist --> UC3
    Tourist --> UC4
    Tourist --> UC5

    %% AI Sentinel Interactions
    AISentinel --> UC6
    AISentinel --> UC7
    AISentinel --> UC8
    AISentinel --> UC10
    
    %% Relationships & Inclusions
    UC10 -.->|"<<include>>"| UC9
    UC10 -.->|"<<include>>"| UC11
    UC11 -.->|"<<extend>> (No Internet)"| UC15

    %% Admin Interactions
    Admin --> UC12
    Admin --> UC13
    Admin --> UC14

    %% External Gateway Links
    UC1 --- ExtServices
    UC15 --- ExtServices
```

---

## 2. System Class Diagram

The class diagram captures both the **Mobile Client Edge Architecture** (Flutter/Dart + On-Device AI models) and the **FastAPI Cloud Backend Architecture** (Python/SQLAlchemy + Database Models).

```mermaid
classDiagram
    %% =======================
    %% MOBILE CLIENT EDGE LAYER
    %% =======================
    class SensorSentinel {
        <<interface>>
        +startMonitoring() void
        +stopMonitoring() void
        +isCalibrated() bool
    }

    class KinematicSentinelService {
        -double impactGThreshold
        -double freeFallGThreshold
        -bool isInFreeFall
        -DateTime freeFallTimestamp
        +startMonitoring() void
        +stopMonitoring() void
        +calculateMagnitude(x, y, z) double
        -_handleImpactDetected(peakG, reason) void
    }

    class AcousticSentinelService {
        -double screamConfidenceThreshold
        -String duressPassphrase
        -TFLiteInterpreter yamnetInterpreter
        +startMonitoring() void
        +processAudioBuffer(byte[] pcmData) double
        +detectDuressPhrase(String transcribedText) bool
    }

    class VisionCaptureService {
        -bool isCameraArmed
        +captureCovertEvidence() EvidencePackage
        +analyzeFacialAgony(CameraImage frame) double
    }

    class MultiSensorFusionEngine {
        -double currentThreatScore
        -ThreatLevel systemState
        +evaluateSensors(AcousticScore, KinematicScore, SpatialScore) double
        +triggerTriageCountdown(double threatScore, String triggerType) void
        +abortCountdown(String enteredPin) bool
        +dispatchImmediateAlert(bool isSilent) void
    }

    class OfflineQueueManager {
        -SQLiteDatabase localDb
        +enqueueIncident(IncidentPayload payload) void
        +getPendingPackets() List~IncidentPayload~
        +markSynced(String incidentId) void
        +dispatchViaGsmSms(IncidentPayload payload) bool
    }

    SensorSentinel <|.. KinematicSentinelService
    SensorSentinel <|.. AcousticSentinelService
    MultiSensorFusionEngine --> KinematicSentinelService : listens to
    MultiSensorFusionEngine --> AcousticSentinelService : listens to
    MultiSensorFusionEngine --> VisionCaptureService : triggers
    MultiSensorFusionEngine --> OfflineQueueManager : persists/fails over

    %% ========================
    %% BACKEND ORCHESTRATION & DB
    %% ========================
    class IncidentOrchestrationService {
        +verifyToken(String idToken) UserPayload
        +ingestIncident(IncidentTelemetry telemetry) IncidentResponse
        +evaluateGeofence(double lat, double lon) GeofenceResult
        +broadcastWebSocketAlert(IncidentAlert alert) void
    }

    class HaversineCalculator {
        <<utility>>
        +EARTH_RADIUS_METERS double
        +computeDistance(lat1, lon1, lat2, lon2) double
        +isInsideZone(touristLat, touristLon, zone) bool
    }

    class User {
        +String id (Firebase UID)
        +String email
        +String emergencyContactPhone
        +String medicalBloodGroup
        +DateTime createdAt
    }

    class LocationPing {
        +int id
        +String userId
        +double latitude
        +double longitude
        +double speed
        +DateTime timestamp
    }

    class Zone {
        +int id
        +String name
        +String type
        +double centreLatitude
        +double centreLongitude
        +double radius
        +String riskSeverity
    }

    class IncidentAlert {
        +int id
        +String userId
        +double latitude
        +double longitude
        +String threatCategory
        +double threatScore
        +bool inDangerZone
        +String zoneName
        +bool isSilent
        +String status
        +String evidenceAudioUrl
        +String evidenceSnapshotUrl
        +DateTime timestamp
    }

    IncidentOrchestrationService --> HaversineCalculator : uses
    IncidentOrchestrationService --> User : queries
    IncidentOrchestrationService --> IncidentAlert : creates
    IncidentOrchestrationService --> LocationPing : appends
    IncidentOrchestrationService --> Zone : checks against

    User "1" -- "0..*" LocationPing : generates
    User "1" -- "0..*" IncidentAlert : triggers
```

---

## 3. Sequence Diagrams

### 3.1 Autonomous Multimodal Incident Detection & Dispatch
This sequence details a real-time event: an attacker confronts the tourist or a severe fall/impact occurs, causing hands-free autonomous detection, countdown, and instant dispatch.

```mermaid
sequenceDiagram
    autonumber
    actor Tourist as 👤 Tourist / Physical Environment
    participant Sensors as 📱 Hardware Sensors (IMU / Mic)
    participant EdgeAI as 🧠 SensorFusionEngine (Edge)
    participant UI as 🖥️ Mobile Screen (HUD)
    participant Backend as ☁️ FastAPI Backend
    participant AdminHUD as 🚨 Admin Dispatcher Dashboard

    Note over Tourist, Sensors: High-G Impact (4.2G) OR Scream Detected
    Sensors->>EdgeAI: Stream Sensor Vectors (Acc: 4.2G, ScreamProb: 0.88)
    EdgeAI->>EdgeAI: Compute Threat Score T = 0.89 (Code Red)
    
    alt Silent Duress Triggered (e.g., Secret Keyword)
        EdgeAI->>EdgeAI: Keep Screen Silent (No siren, no screen flash)
        EdgeAI->>EdgeAI: Covertly snap front/rear camera photos & 10s audio
        EdgeAI->>Backend: POST /api/v1/incidents/dispatch (isSilent: true)
    else Overt Emergency (Fall / Vehicle Crash)
        EdgeAI->>UI: Show 15s Countdown Screen + Urgent Haptic Vibration
        UI-->>Tourist: "Are you okay? Slide to cancel (PIN Required)"
        
        alt Tourist Cancels within 15 seconds
            Tourist->>UI: Slide & Enter Safe PIN "1234"
            UI->>EdgeAI: Abort Emergency Sequence
            EdgeAI->>EdgeAI: Reset to Normal Monitoring
        else Countdown Expires (No response or Incapacitated)
            UI->>EdgeAI: Timer Expired (Triage confirmed: UNRESPONSIVE)
            EdgeAI->>Backend: POST /api/v1/incidents/dispatch (Emergency Payload)
        end
    end

    Backend->>Backend: Verify Firebase Token & Persist Incident in PostgreSQL
    Backend->>AdminHUD: WebSocket Broadcast: ALERT_CODE_RED (Sound + Flashing Pin)
    AdminHUD-->>Tourist: Dispatch Emergency Police / Medical Responders
```

---

### 3.2 Periodic Location Tracking & Haversine Geofencing
Shows the continuous cycle of tracking tourist movement and assessing danger zone violations.

```mermaid
sequenceDiagram
    autonumber
    actor TouristApp as 📱 Tourist Mobile App
    participant Geolocation as 🛰️ Geolocator Service
    participant FastAPIServer as ☁️ FastAPI Backend
    participant PostGIS as 🗄️ PostgreSQL Database
    participant AdminClient as 💻 Admin Live Map

    loop Every 10 Seconds (Configurable Interval)
        TouristApp->>Geolocation: Get Current Position (lat, lon, accuracy, speed)
        Geolocation-->>TouristApp: Lat: 32.2396, Lon: 77.1887, Speed: 4.2 km/h
        TouristApp->>FastAPIServer: POST /api/v1/locations/ping (Bearer Token + Coordinates)
        FastAPIServer->>FastAPIServer: Verify Firebase ID Token
        FastAPIServer->>PostGIS: INSERT INTO location_pings (user_id, lat, lon, time)
        
        FastAPIServer->>PostGIS: SELECT * FROM zones WHERE is_active = true
        PostGIS-->>FastAPIServer: Return Active Safe and Danger Zones
        
        loop For Every Zone in Zones
            FastAPIServer->>FastAPIServer: Execute Haversine Distance: d = 2R * arcsin(...)
            alt Distance d <= Zone.Radius AND Zone.Type == "DANGER"
                FastAPIServer->>FastAPIServer: Flag Hazard Violation (Zone: "Cliff Edge")
            end
        end
        
        FastAPIServer->>AdminClient: Push Updated Tourist Pin Coordinates
        FastAPIServer-->>TouristApp: 200 OK (ZoneStatus: "IN_DANGER_ZONE", WarningMsg)
        
        opt Danger Zone Warning
            TouristApp->>TouristApp: Trigger In-App Audible/Haptic Boundary Warning
        end
    end
```

---

### 3.3 Zero-Connectivity Network Failover & SMS Gateway
Shows the resilience workflow when a tourist is lost in remote valleys without 4G/5G data coverage.

```mermaid
sequenceDiagram
    autonumber
    actor TouristApp as 📱 Tourist Device
    participant LocalDB as 💾 SQLite Local Cache
    participant NetMonitor as 📶 Network Connectivity Monitor
    participant GSMModule as 📡 Device Cellular GSM / SMS
    participant TwilioGW as 🏢 Twilio SMS Inbound Gateway
    participant Backend as ☁️ FastAPI Backend Server
    participant Dispatcher as 🚨 Emergency Dispatcher

    Note over TouristApp: Critical Incident Triggered (Crash / SOS)
    TouristApp->>LocalDB: Encrypt & Store Incident Packet (status: "QUEUED")
    TouristApp->>NetMonitor: Check Active Internet Route
    NetMonitor-->>TouristApp: HTTP Connection Timed Out / No Mobile Data
    
    Note over TouristApp, GSMModule: Immediate Failover to Telephony GSM Protocol
    TouristApp->>TouristApp: Encode Compressed Payload: "SOS|UID883|32.2396|77.1887|CRASH|1900Z"
    TouristApp->>GSMModule: Send Encoded SMS to Emergency Server Dispatch Number
    GSMModule->>TwilioGW: Deliver Cellular SMS via Telco Network
    TwilioGW->>Backend: Webhook Callback: POST /api/v1/webhooks/sms-incident
    Backend->>Backend: Decode String, Parse Coordinates & Threat Type
    Backend->>Dispatcher: Trigger Critical Pop-up Alarm (Source: CELLULAR_SMS)
    
    opt When Internet Connection Restores Later
        NetMonitor->>TouristApp: Connectivity Restored (4G Re-established)
        TouristApp->>LocalDB: Fetch All Records Where status == "QUEUED"
        TouristApp->>Backend: POST /api/v1/incidents/bulk-sync
        Backend-->>TouristApp: 200 OK (Acknowledged)
        TouristApp->>LocalDB: UPDATE status = "SYNCED"
    end
```

---

## 4. State Machine Diagram

Describes the complete lifecycle of the **On-Device Sentinel Agent** and the **Emergency Incident State Machine**.

```mermaid
stateDiagram-v2
    [*] --> StandbyArmed: User Signs In & Grants Permissions

    state StandbyArmed {
        [*] --> PassiveListening
        PassiveListening --> KinematicEvaluation: IMU Movement Spike (>1.8g)
        KinematicEvaluation --> PassiveListening: Normal Walking / Running
    }

    StandbyArmed --> AnomalyDetected: Scream Detected (P > 0.80) OR Impact (>3.8g)

    state AnomalyDetected {
        [*] --> CalculateFusionScore
        CalculateFusionScore --> EvaluateContext: ThreatScore >= 0.70
    }

    AnomalyDetected --> StandbyArmed: ThreatScore < 0.35 (False Alarm Suppressed)
    AnomalyDetected --> WarningHaptic: 0.35 <= ThreatScore < 0.70 (Suspicious)
    WarningHaptic --> StandbyArmed: User Dismisses Notice

    AnomalyDetected --> TriageCountdown: ThreatScore >= 0.70 (Severe Crisis)

    state TriageCountdown {
        [*] --> TimerRunning: 15s Countdown Active
        TimerRunning --> UserCancelled: Valid Safe PIN Entered
        TimerRunning --> AutoEscalate: Timer Hits Zero (No Touch/Voice)
    }

    UserCancelled --> StandbyArmed: Log False Alarm Metric
    AutoEscalate --> EmergencyDispatched: Fire Code Red Telemetry

    state EmergencyDispatched {
        [*] --> AttemptWebSocket
        AttemptWebSocket --> RestTransmission: WebSocket Unreachable
        RestTransmission --> GsmSmsFallback: Cellular Data Down
        GsmSmsFallback --> AlertBroadcasting: Dispatched via SMS
        AttemptWebSocket --> AlertBroadcasting: Dispatched via WebSocket
    }

    AlertBroadcasting --> ResponderEnRoute: Admin Acknowledges Alert
    ResponderEnRoute --> IncidentResolved: Authorities Reach Tourist
    IncidentResolved --> StandbyArmed: Admin Closes Ticket
```

---

## 5. Activity Diagram

The activity diagram models the operational business logic executed during every sensing interval.

```mermaid
flowchart TD
    Start([Start Monitoring Loop]) --> ReadSensors[Read IMU, Mic Buffer, and GPS]
    ReadSensors --> CheckFreeFall{Free Fall Observed?\n< 0.45g for > 80ms}

    CheckFreeFall -- Yes --> FreeFallFlag[Set FreeFall Timestamp]
    CheckFreeFall -- No --> CheckImpact{Impact Detected?\nAcc > 3.8g}

    FreeFallFlag --> CheckImpact

    CheckImpact -- Yes --> EvaluatePrePost[Check FreeFall + Impact + Immobility Pattern]
    CheckImpact -- No --> AudioInference[Run TFLite YamNet Acoustic Classifier]

    EvaluatePrePost --> KinematicSevere{Valid High-G Crash\nor Hard Fall?}
    KinematicSevere -- Yes --> RaiseThreatHigh[Kinematic Threat K_t = 1.0]
    KinematicSevere -- No --> AudioInference

    AudioInference --> CheckAcoustic{Scream / Distress\nConfidence > 0.80?}
    CheckAcoustic -- Yes --> RaiseAcoustic[Acoustic Threat A_t = 0.9]
    CheckAcoustic -- No --> CheckKeyword{Duress Keyword\nDetected?}

    CheckKeyword -- Yes --> SetSilentDuress[Set Duress Flag = True]
    CheckKeyword -- No --> FusionCalculation[Compute Total Threat Score T]

    RaiseThreatHigh --> FusionCalculation
    RaiseAcoustic --> FusionCalculation
    SetSilentDuress --> FusionCalculation

    FusionCalculation --> ThreatThreshold{Threat Score T >= 0.70?}

    ThreatThreshold -- No --> GeofenceCheck[Evaluate Haversine Geofence vs Zones]
    ThreatThreshold -- Yes --> CheckSilentMode{Is Silent Duress Active?}

    CheckSilentMode -- Yes --> SilentCapture[Covertly Capture Photos & Audio Stream]
    SilentCapture --> DispatchPacket[Prepare Emergency Payload]

    CheckSilentMode -- No --> ShowCountdownModal[Launch 15-Second Triage Countdown HUD]
    ShowCountdownModal --> AwaitCancel{User enters PIN\nwithin 15s?}

    AwaitCancel -- Yes --> AbortAlert[Abort Emergency & Log Diagnostics]
    AwaitCancel -- No --> DispatchPacket

    DispatchPacket --> InternetAvailable{Internet Active?}
    InternetAvailable -- Yes --> SendCloud[Transmit to FastAPI via WebSocket / REST]
    InternetAvailable -- No --> SendSMS[Send Encoded Emergency SMS via Cellular GSM]

    SendCloud --> AdminHUD[Alert Displayed on First Responder Admin HUD]
    SendSMS --> AdminHUD
    AbortAlert --> LoopEnd([Wait for Next Polling Frame])
    GeofenceCheck --> LoopEnd
    AdminHUD --> LoopEnd
```

---

## 6. Component Diagram

Illustrates the modular packaging and runtime dependencies between edge modules, cloud microservices, and external systems.

```mermaid
flowchart TD
    subgraph ClientPackage["Tourist Mobile Application (Flutter Client)"]
        UIModule["UI / UX Presentation Module\n(Live HUD, Map, Emergency Modal)"]
        SensorsModule["Sensors Daemon Component\n(Geolocator, Sensors Plus, Mic Stream)"]
        AIEdgeModule["On-Device AI Engine\n(TFLite YamNet, Fall Detector, MediaPipe)"]
        LocalCacheModule["Offline Persistence\n(sqflite, Encrypted SharedPreferences)"]
        CommModule["Network Communication Broker\n(Dio HTTP, WebSockets, SMS Plugin)"]

        UIModule <--> AIEdgeModule
        SensorsModule --> AIEdgeModule
        AIEdgeModule --> LocalCacheModule
        AIEdgeModule --> CommModule
        LocalCacheModule <--> CommModule
    end

    subgraph BackendPackage["Guardian-Go Cloud Platform (FastAPI Core)"]
        APIGateway["FastAPI HTTP / WebSocket Router"]
        AuthFilter["Firebase Admin Token Authenticator"]
        GeofenceEngine["Haversine Geofencing Engine"]
        IncidentOrchestrator["Incident Triage & Correlation Service"]
        NotificationBroker["Alert Notification Broker\n(Twilio API, FCM, WebSockets)"]

        APIGateway --> AuthFilter
        AuthFilter --> IncidentOrchestrator
        IncidentOrchestrator --> GeofenceEngine
        IncidentOrchestrator --> NotificationBroker
    end

    subgraph DatabasePackage["Persistence Tier"]
        PostgresDB[("PostgreSQL 16 DB\n(Users, Locations, Zones, Incidents)")]
        RedisCache[("Redis 7 In-Memory Cache\n(Active WebSockets & Live Cache)")]
    end

    subgraph ExternalGateways["External Cloud Services"]
        FirebaseAuth["Firebase Authentication Service"]
        TwilioService["Twilio Telephony & SMS Gateway"]
        OSMTiles["OpenStreetMap Tile Servers"]
    end

    CommModule -->|HTTPS / WSS| APIGateway
    CommModule -->|GSM SMS| TwilioService
    APIGateway --> PostgresDB
    APIGateway --> RedisCache
    AuthFilter -->|Token Verification| FirebaseAuth
    NotificationBroker --> TwilioService
    UIModule -->|Fetch Map Tiles| OSMTiles
```

---

## 7. Deployment Diagram

Depicts the physical execution environment, containerization, protocol boundaries, and hardware nodes.

```mermaid
flowchart TD
    subgraph TouristDevice["📱 Client Physical Node (Android / iOS Device)"]
        MobileOS["Operating System (Android 14+ / iOS 17+)"]
        subgraph MobileAppRuntime["Flutter Application Sandbox"]
            DartVM["Dart Native Runtime + Flutter Engine"]
            TFLiteC["TensorFlow Lite C++ Runtime (Quantized Models)"]
            SQLiteEngine["SQLite Storage Engine (AES-256 Encrypted)"]
        end
        Hardware["Device Hardware: GPS Chip, Tri-Axial IMU, Microphone, Cameras, GSM Modem"]
        Hardware --> MobileAppRuntime
    end

    subgraph CloudInfra["☁️ Cloud Infrastructure (Linux VPS / Google Cloud / AWS)"]
        subgraph DockerEnv["Docker Container Environment"]
            NginxProxy["Nginx Reverse Proxy & SSL Termination\n(Ports 80 / 443)"]
            FastAPIApp["FastAPI ASGI Server (Uvicorn Workers)\n(Port 8000)"]
            NginxProxy --> FastAPIApp
        end

        subgraph DataTier["Managed Database Instances"]
            PGNode[("PostgreSQL 16 + PostGIS\n(Port 5432)")]
            RedisNode[("Redis Cluster\n(Port 6379)")]
        end
        FastAPIApp --> PGNode
        FastAPIApp --> RedisNode
    end

    subgraph AdminWorkstation["💻 First Responder Client (Web Browser)"]
        AdminBrowser["Chrome / Edge / Firefox\n(HTML5, Leaflet.js, WebSockets HUD)"]
    end

    subgraph ThirdPartyCloud["🌐 Third-Party SaaS"]
        FirebaseCloud["Google Firebase Auth Servers"]
        TwilioCloud["Twilio Global Telephony Cloud"]
    end

    %% Network Protocols
    TouristDevice -->|WSS / HTTPS (TLS 1.3)| NginxProxy
    TouristDevice -.->|Cellular GSM SMS / PDU Protocol| TwilioCloud
    TwilioCloud -->|Webhook HTTPS POST| NginxProxy
    AdminWorkstation -->|HTTPS / WSS (Port 443)| NginxProxy
    FastAPIApp -->|HTTPS REST| FirebaseCloud
```

---

## 8. Entity-Relationship (ER) Diagram

Represents the relational data model deployed on the PostgreSQL database, maintaining full referential integrity and data types.

```mermaid
erDiagram
    USERS ||--o{ LOCATION_PINGS : "generates"
    USERS ||--o{ INCIDENT_ALERTS : "triggers"
    USERS ||--o{ USER_HEALTH_PROFILES : "owns"
    ZONES ||--o{ INCIDENT_ALERTS : "cross-references"

    USERS {
        VARCHAR(128) id PK "Firebase UID"
        VARCHAR(255) email "User email address"
        VARCHAR(32) phone_number "Primary mobile contact"
        VARCHAR(128) full_name "Tourist full legal name"
        TIMESTAMP created_at "Registration timestamp"
        BOOLEAN is_active "Account status"
    }

    USER_HEALTH_PROFILES {
        INT id PK "Auto-increment ID"
        VARCHAR(128) user_id FK "Ref USERS(id)"
        VARCHAR(8) blood_group "e.g., O+, A-, B+"
        TEXT known_allergies "e.g., Penicillin, Peanuts"
        TEXT emergency_contact_1 "Primary phone & relationship"
        TEXT emergency_contact_2 "Secondary phone"
        VARCHAR(64) secret_safe_pin "Bcrypt-hashed safe cancel PIN"
    }

    LOCATION_PINGS {
        BIGINT id PK "Unique ping sequence ID"
        VARCHAR(128) user_id FK "Ref USERS(id)"
        DOUBLE_PRECISION latitude "GPS Latitude"
        DOUBLE_PRECISION longitude "GPS Longitude"
        FLOAT speed "Instantaneous speed in km/h"
        FLOAT heading "Compass orientation in degrees"
        FLOAT accuracy "GPS accuracy radius in meters"
        TIMESTAMP timestamp "Ping reception time (UTC)"
    }

    ZONES {
        INT id PK "Zone ID"
        VARCHAR(100) name "e.g., Rohtang Pass Avalanche Zone"
        VARCHAR(32) type "SAFE or DANGER"
        DOUBLE_PRECISION centre_latitude "Center point latitude"
        DOUBLE_PRECISION centre_longitude "Center point longitude"
        DOUBLE_PRECISION radius "Radius in meters"
        VARCHAR(32) risk_severity "LOW, MEDIUM, HIGH, CRITICAL"
        BOOLEAN is_active "Zone active flag"
    }

    INCIDENT_ALERTS {
        BIGINT id PK "Unique Incident Alert ID"
        VARCHAR(128) user_id FK "Ref USERS(id)"
        INT zone_id FK "Optional Ref ZONES(id)"
        DOUBLE_PRECISION latitude "Emergency location latitude"
        DOUBLE_PRECISION longitude "Emergency location longitude"
        VARCHAR(64) threat_category "CRASH, ATTACK, MEDICAL, GEOFENCE"
        FLOAT threat_score "Calculated threat score (0.0 to 1.0)"
        BOOLEAN in_danger_zone "Whether inside danger zone"
        VARCHAR(100) zone_name "Cached name of zone"
        BOOLEAN is_silent "True if silent duress"
        VARCHAR(32) status "ACTIVE, ACKNOWLEDGED, RESOLVED, FALSE_ALARM"
        TEXT evidence_audio_url "URL to encrypted audio snapshot"
        TEXT evidence_snapshot_url "URL to encrypted image snapshot"
        TIMESTAMP timestamp "Incident trigger timestamp"
    }
```

---

## 9. Summary & Verification Matrix

| UML Diagram | Purpose & Architectural Role | Standard Used |
| :--- | :--- | :--- |
| **Use Case Diagram** | Maps external actors (Tourist, Admin, AI Sentinel, Gateways) against functional requirements. | UML 2.5 Use Case Standard |
| **Class Diagram** | Details OOP classes, attributes, methods, inheritance, and interfaces across mobile and cloud. | UML 2.5 Class Model |
| **Sequence Diagrams** | Proves step-by-step temporal event ordering for detection, tracking, and offline GSM failover. | UML 2.5 Interaction Sequence |
| **State Machine Diagram**| Documents deterministic states from idle monitoring to triage countdown and dispatch. | UML 2.5 Statechart Standard |
| **Activity Diagram** | Outlines business logic, sensor evaluation conditions, and decision trees. | UML 2.5 Activity Model |
| **Component Diagram** | Defines software subsystems, module loose coupling, and interface boundaries. | UML 2.5 Component Standard |
| **Deployment Diagram** | Specifies physical client nodes, network protocols (WSS/HTTPS/GSM), and server containers. | UML 2.5 Deployment Spec |
| **Entity-Relationship** | Formalizes database tables, foreign keys, data types, and multiplicities in PostgreSQL. | Crow's Foot Relational Standard |
