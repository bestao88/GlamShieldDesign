## GlamShieldDesign

# Insect Capture and Monitoring Mobile Application Development Specification Document

## 1 Project Overview

- Project Name: Insect Capture and Monitoring Mobile Application
- Target Platforms: iOS and Android + Web-based Backend
- Development Languages: C#, Python, etc. mainstream, aggregated platform development
- Backend: Django / Flask



## 2 Technical Architecture

### 2.1. Frontend Architecture

- iOS and Android: MVVM aggregated platform development
- Shared business logic: Consider using Kotlin Multiplatform

### 2.2. Backend Architecture

- Language: Python ~~or C#~~
- Framework: Django / Flask
- Database: ~~PostgreSQL~~ / MySQL
- ORM: SQLAlchemy (Python) 
- File Storage: Amazon S3

### 2.3. API Design

- RESTful design principles
- Use JWT for authentication
- API version control: Use /api/v1/ in URL



## 3 Data Models 
(All tables need to add logical deletion field bool, creator and last modifier UUID, creation and last modification time Timestamp)

### 3.1 User Group Table (UserGroup)

- ugid: auto-increase
- ugname: String
- ugdiscription: String

### 3.2 Access Rights Table (AccessRight)

- arid: Auto-Increase
- arname: String
- arpage: String
- arfunction: String
- argroupid: Integer (FK: accessrightgroup)

### 3.3 Enterprise and Organization (organization)

- OrgID: Auto-Increase
- OrgParentID: Integer (FK: organization)
- OrgName: String
- OrgAddress: String
- location: GeoJSON Point
- OrgAdmin: Integer (FK: User)
- OrgComments: String

### 3.4 User (User)

- uid: UUID
- uname: String
- email: String
- password_hash: String
- OrgID: UUID (FK: Organization)
- ugroup: ugid (FK: UserGroup)
- Title: String
- Department: String
- Mobile: String

### 3.5 Group Access Table (GroupAccess)

- ugid: UUID (FK: UserGroup)
- arid: UUID (FK: AccessRight)
- gacomments: String

### 3.6 Monitoring Area Definition Table (Repository)

- repid: UUID
- OrgID: UUID (FK: Organization)
- repname: String
- repmap: String (path save for picture)
- uid: UUID (FK: User)

### 3.7 Trap Type (TrapType)

- ttID: UUID
- ttName: String
- ttComments: String

### 3.8 Trap (Trap)

- TrapID: UUID
- OrgID: UUID (FK: organization)
- uid: UUID (FK: User)
- repid: UUID (FK: Repository)
- location: GeoJSON Point
- qr_code: String
- ttID: UUID (FK: TrapType)
- install_at: Datetime
- invalid_at: Datetime
- inactive_at: Datetime
- status: Enum (active, inactive)

### 3.9 Bug Registration Table (Bugs)

- bgid: UUID
- bg_ParentID: UUID (FK: bugs)
- bgName: String
- bgNameEn: String
- bgNameLatin: String
- bgDemageLevel: (FK: DemageLevel)
- uid: UUID (FK: users)

### 3.10 Pest Control Suggestions (PestControl)

- pcid: UUID
- bgid: UUID (FK: bugs)
- pcExpert: String
- pcExpertTitle: String
- pcExpertOrg: String
- pcPreventSug: String
- pcKillSug: String
- pcComments: String
- uid: UUID (FK: users)

### 3.11 Bug Image Table (BugImages)

- biid: UUID
- bgid: UUID (FK: bugs)
- biPath: String
- uid: UUID (FK: users)

### 3.12 Inspection Definition Main Table (routing)

- rtid: UUID
- uid: UUID (FK: users)
- rtfromdate: date
- rtenddate: date
- rtcomments: String
- rtid: UUID (FK: users)

### 3.13 Inspection Definition Sub-table (routingreg)

- Rtrid: UUID
- rtid: UUID (FK: routing)
- repid: UUID (FK: Repository)
- rtcomments: String

### 3.14 Inspection Record Table (RoutingRec)

- rrid: UUID
- uid: UUID (FK: users)
- rrdatetime: datetime
- rr_url: string (storing inspection photos)
- rr_qr: string (storing inspection QR code)
- rr_exception: bool
- rr_discription: string

### 3.15 Capture Main Record (Capture)

- cid: UUID
- trap_id: UUID (FK: Trap)
- user_id: UUID (FK: User)
- Cap_Location: String
- species: String
- count: Integer
- cap_date: Datetime
- image_url: String
- notes: Text
- auditor_id: UUID (FK: User)
- auditor_comments: String

### 3.16 Capture Sub-record (CaptureDetails)

- cdID: UUID
- cid: UUID (FK: CaptureImages)
- ccid: UUID
- found_id: UUID (FK: User)
- bgid: UUID (FK: bugs)
- count: Integer
- bsID: UUID (FK: bugStatus)
- etid: UUID (FK: BugEecotype)
- lsid: UUID (FK: BugLifeStage)
- bsid: UUID (FK: bugSize)
- sfid: UUID (FK: surface)
- cdDiscription: String
- capture_date: Datetime
- image_url: String
- notes: Text
- auditor_id: UUID (FK: User)
- auditor_comments: String

### 3.17 Capture Photos (CaptureImages)

- Ciid: UUID
- Image_url: String
- Image_Discription: String
- created_at: Timestamp
- updated_at: Timestamp



## 4 API Endpoints

### 4.1 User Authentication

- POST /api/v1/auth/register
- POST /api/v1/auth/login
- POST /api/v1/auth/logout
- GET /api/v1/auth/user

### 4.2 Trap Management

- GET /api/v1/traps
- POST /api/v1/traps
- GET /api/v1/traps/:id
- PUT /api/v1/traps/:id
- DELETE /api/v1/traps/:id

### 4.3 Capture Records

- GET /api/v1/captures
- POST /api/v1/captures
- GET /api/v1/captures/:id
- PUT /api/v1/captures/:id
- DELETE /api/v1/captures/:id



## 5 User Interface Specifications

### 5.1 Color Scheme

- Primary Color: #007AFF
- Background Color: #FFFFFF
- Secondary Color: #F2F2F7
- Warning Color: #FF3B30
- Success Color: #34C759

### 5.2 Fonts

- iOS: San Francisco
- Android: Roboto
- Font Sizes:
  - Heading: 20sp
  - Subheading: 18sp
  - Body: 16sp
  - Caption: 14sp

### 5.3 Icons

- Use SF Symbols (iOS) and Material Icons (Android)
- Unified icon size: 24x24dp

### 5.4 Layout

- Use Constraint Layout

- Margins: 16dp

- Element spacing: 8dp

  

## 6 Functional Module Development Specifications:

### <u>6.A Backend Fucitonal Modual</u>

### 6.1 Organization Registration

- Register organization when user selects organization during registration
- Allow users to set up hierarchical management of organizations (tree display)
- First-time registration administrator defaults to current registrant
- Organization registration allows administrator to change all information
- Summary and detailed query of historical inspection/pest records for each organization



### 6.2 User Registration Module

- Implement local token storage
- Use Keychain (iOS) and EncryptedSharedPreferences (Android) to store sensitive information
- Implement auto-login functionality
- New registered users who enter an existing organization code must be reviewed and granted permissions by the administrator before use
- Summary and detailed query of historical inspection, inspection, and pest discovery records for each user

### 6.3 Organization User Permission Management

- Organization user administrator sets function permissions for newly registered users
- Custom permission groups can be defined for easy use by users with the same type of permissions
- Specific permissions can be modified arbitrarily, and can also be modified individually after inheriting from a group
- Users can be activated and deactivated, but not deleted

### 6.4 Trap Management:

- Users can manually enter or scan QR code for new trap registration
- Can register trap location directly during registration or supplement later
- Can scan trap QR code to query/modify trap information at any time
- Location identification method is pixel position on map image (simultaneously record percentage position from leftmost and topmost)
- User clicks on map image position to automatically obtain location, stored in JSON
- Summary and detailed query of historical pest records for each trap

### 6.5 Map Module Management:

- ~~Use MapKit (iOS) and Google Maps SDK (Android)~~
- Users can upload images within specified size and resolution range
- Uploaded images can be logically deleted, not allowed to modify
- Record image length, width, size, and resolution
- Support image zoom and pan
- For map images with traps, mark valid trap locations with green dots; for traps about to expire (within one month), mark with red

### 6.6 Pest Management:

- Can register newly discovered pests individually
- Pests need tree-structured hierarchical management
- Can enter prevention and elimination measures for pests at the time of registration
- Registrant can modify pest-related information, can only logically delete

### 6.7 Inspection Management

- Can set inspection tasks for designated employees
- Designated employees will receive reminders, one day before work and 15 minutes before
- Inspection employees can select designated inspection area map for trap-by-trap inspection, scanning QR codes and taking photos
- Each trap inspection needs to indicate if there are any abnormalities, if abnormal, need to fill in abnormal content
- Can inspect according to map, each completed inspection marked with a green checkmark on the original marker, abnormal ones marked with a red X
- Uninspected traps should have prompts. Area completion needs confirmation, forced completion possible for areas with uninspected traps but reasons must be provided.

### 6.8 Pest Discovery and Registration

- Conduct photography first, then registration on the pest discovery page
- Provide registration description for each photo
- If a new pest is discovered, can register new pest; if it's an already registered pest, can select the discovered pest
- For pest damage levels, can provide corresponding level reminders and color indicators based on the level
- Discoverer or organization administrator can modify new pest discovery information
- For multiple pests found in the same trap, can take photos and register separately

### 6.9 Camera Module

- Use AVFoundation (iOS) and CameraX (Android)
- Support auto-focus and flash control
- Implement image compression to optimize upload

### 6.10 QR Code Scanning

- Use AVFoundation (iOS) and ML Kit (Android) for QR code scanning
- Support scanning from photo album

### 6.11 Offline Data Synchronization

- Use Core Data (iOS) and Room (Android) for local data storage
- Implement background synchronization mechanism
- Handle synchronization conflicts

### <u>6.B Frontend Fucitonal Modual</u>





## 7 Performance Optimization

### 7.1 Image Loading

- Use Kingfisher (iOS) and Glide (Android) for image loading and caching
- Implement lazy loading for images

### 7.2 List Optimization

- Use UICollectionView (iOS) and RecyclerView (Android)
- Implement view recycling and reuse

### 7.3 Network Requests

- Use Alamofire (iOS) and Retrofit (Android) for network requests
- Implement request caching and failure retry mechanism



## 8 Security Measures

### 8.1 Data Encryption

- Use AES-256 to encrypt sensitive data
- Implement secure key management

### 8.2 Network Security

- Use SSL Pinning to prevent man-in-the-middle attacks
- Implement request signature mechanism

### 8.3 Code Obfuscation

- Use ProGuard (Android) for code obfuscation
- Use compile-time obfuscation (iOS)




## 9 Unit Testing

### 9.1 Unit Tests
- Use XCTest (iOS) and JUnit (Android)
- Target code coverage: 80%

### 9.2 UI Tests
- Use XCUITest (iOS) and Espresso (Android)
- Cover all key user flows

### 9.3 Integration Tests
- Use Fastlane for automated testing
- Configure CI/CD pipeline (e.g., Jenkins or GitLab CI)



## 10 Documentation Requirements

### 10.1 Code Documentation
- Use Jazzy (iOS) and Dokka (Android) to generate API documentation
- Follow standard comment formats

### 10.2 README
- Include project setup instructions
- List of dependencies
- Build and run instructions

### 10.3 Changelog
- Record changes for each version
- Follow semantic versioning conventions



## 11 Version Control
- Use Git for version control
- Follow Git Flow workflow
- Use meaningful commit messages, adhering to conventional commit standards



## 12 Delivery Standards

- Source code (including frontend and backend)
- Database schema and migration scripts
- API documentation
- User manual
- Deployment documentation
- Test reports
- Performance benchmark reports



## UI Design

### Web Management

![Login](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Login.png)

![Table](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Table.png)

![Trap detail](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Trap%20detail.png)

![trap management](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/trap%20management.png)

![View More](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/View%20More.png)

![Add a trap](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Add%20a%20trap-20240922220722344.png)

![Dashboard](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Dashboard.png)

![Data Management - Add Pest](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Data%20Management%20-%20Add%20Pest.png)

![Data Management - Edit Location](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Data%20Management%20-%20Edit%20Location.png)

![Data Management - Edit Pest](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Data%20Management%20-%20Edit%20Pest.png)

![Data Management - Location](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Data%20Management%20-%20Location.png)

![Data Management - Pest Type](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Data%20Management%20-%20Pest%20Type.png)

![Forms](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Forms.png)

![Image view](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Image%20view.png)

![Data Management - Add Location](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Data%20Management%20-%20Add%20Location.png)

![Member Profile1](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Member%20Profile1.png)

![Analysis1](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Analysis1.png)

![Member1](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Member1.png)





### Mobile APP

![Main page](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Main page2.png)

![Sign Up Page](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Sign%20Up%20Page1.png)

![Camera View](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Camera%20View.png)

![Data Picker](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Data%20Picker.png)

![QR Reader](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/QR%20Reader.png)


![Text field](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Text%20field.png)


![Name Picker](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Name%20Picker.png)

![QR Reader - add new trap](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/QR%20Reader%20-%20add%20new%20trap.png)

#### <u>Incidental Capture</u>

![Incidental Capture](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Incidental%20Capture-20240922222115497.png)

![IC - Data Preview](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/IC%20-%20Data%20Preview.png)

![IC - Rep map](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/IC%20-%20Rep%20map.png)

![IC- Pest Data](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/IC-%20Pest%20Data.png)

![IC - Submitted](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/IC%20-%20Submitted.png)

#### <u>Rountine Inspection</u>

![Rountine Inspection](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Rountine%20Inspection.png)

![RI - Deleted](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/RI%20-%20Deleted.png)

![RI - Move Trap Location](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/RI%20-%20Move%20Trap%20Location.png)

![RI - New Trap map](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/RI%20-%20New%20Trap%20map.png)

![RI - Added Copy](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/RI%20-%20Added%20Copy.png)

![RI - Move trap map](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/RI%20-%20Move%20trap%20map.png)

![RI - New Trap Location](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/RI%20-%20New%20Trap%20Location.png)

![RI - Trap Data](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/RI%20-%20Trap%20Data.png)

![RI - Trap Info](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/RI%20-%20Trap%20Info.png)

![RI - Added](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/RI%20-%20Added.png)

![RI - Trap Info Copy](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/RI%20-%20Trap%20Info%20Copy.png)

![RI - Delete Info](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/RI%20-%20Delete%20info.png)

#### <u>Active Monitoring</u>

![Active Monitoring](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/Active%20Monitoring.png)

![AM - Pest Data](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/AM%20-%20Pest%20Data.png)

![AM - Submitted](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/AM%20-%20Submitted.png)

![AM - Rep map](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/AM%20-%20Rep%20map.png)

![AM - Data Preview](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/AM - Data Preview.png)

### Mobile APP - Flows

![SIPM](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/SIPM.png)

### Web Management - Flows

![web management](https://raw.githubusercontent.com/liangyimingcom/storage/master/PicGo/web%20management.png)

## 14 Other



