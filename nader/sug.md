# Sugesstion

```mermaid
erDiagram
    USER {
        int id PK
        string UserName
        string Email
        string PasswordSalt
        string PasswordHash
        enum Role 
        int Team FK
        int photo FK
        boolean isDeleted
        datetime lastLoginAt
        datetime createdAt
        datetime updatedAt
    }
    
    TEAM {
        int id PK
        string name
        string affiliation
        int photo FK
        int managerId FK
        boolean isDeleted
        datetime createdAt
        datetime updatedAt
    }
    
    BINARYUPLOAD {
        int id PK
        int Team FK
        enum resultStatus
        string errorMsg
        int binaryFile FK
        string finishedDockerImageHash
        int finishedDockerImageFile FK
        int createdBy FK
        datetime createdAt
        datetime updatedAt
    }
    
    FILEUPLOAD {
        int id PK
        string fileSaveId
        string fileName
        string fileType
        int fileSize
        boolean isDeleted
        boolean isPublic
        int createdBy FK
        datetime createdAt
    }
    
    SERVERCONFIG {
        int id PK
        string name
        string description
        json value
        datetime createdAt
        datetime updatedAt
    }
    
    NEWS {
        int id PK
        string title
        text content
        text brief
        enum contentType 
        enum visibility 
        boolean pinned
        string link
        int createdBy FK
        datetime createdAt
        datetime updatedAt
    }
    
    RUNNER {
        int id PK
        string name
        string messageBusId
        enum status 
        datetime lastHeartBeat
        enum queueStatus
        json tags
        int[] RunnerServers FK
    }
    
    RUNNERSERVER {
        int id PK
        string ip
        enum ServerStatus 
    }
    
    GROUP {
        int id PK
        string name
        enum type
        int[] Teams FK
        int defaultServerConfig FK
        json stepLadderState
        enum status
        datetime createdAt
        datetime updatedAt
    }

    GROUPTEAM {
        int id PK
        int Group FK
        int Team FK
        enum state
    }

    MATCH {
        int id PK
        int leftTeam FK
        int rightTeam FK
        int ServerConfig FK
        int Runner FK
        int Group FK
        int leftTeamScore
        int rightTeamScore
        int leftTeamPenaltyScore
        int rightTeamPenaltyScore
        enum status
        int priority
        timestamp startTime
        timestamp startedTime
        timestamp endTime
        int gameLog FK
        int teamsLog FK
        datetime createdAt
        datetime updatedAt
    }

    EVENT {
        int id PK
        int createdBy FK
        string operation
        json operationData
        datetime createdAt
    }

    %% relationships
    USER ||--o{ TEAM : "manages"
    USER ||--o{ FILEUPLOAD : "uploads"
    USER ||--o{ BINARYUPLOAD : "creates"
    USER ||--o{ NEWS : "writes"
    USER ||--o{ EVENT : "creates"
    TEAM ||--o{ BINARYUPLOAD : "uses"
    TEAM ||--|| RUNNER : "has"
    TEAM ||--o{ GROUPTEAM : "belongs to"
    TEAM ||--|| MATCH : "plays in"
    FILEUPLOAD ||--o| TEAM : "is used by"
    FILEUPLOAD ||--o| BINARYUPLOAD : "is binaryFile"
    FILEUPLOAD ||--o| MATCH : "is gameLog"
    SERVERCONFIG ||--|| GROUP : "configures"
    SERVERCONFIG ||--|| MATCH : "configures"
    NEWS ||--o{ TEAM : "views"
    NEWS ||--o{ GROUP : "views"
    RUNNER ||--o| MATCH : "runs"
    RUNNERSERVER ||--|| RUNNER : "belongs to"
    GROUP ||--o| GROUPTEAM : "contains"
    GROUP ||--o| MATCH : "has"
    GROUPTEAM ||--|| TEAM : "is part of"
    GROUPTEAM ||--|| GROUP : "includes"
    MATCH ||--o| GROUP : "has"
```

## Backend API

### Team Manager

> Admin
- POST create new team
    url : /api/team

- PATCH update team
    url : /api/team/:id

- DELETE delete team
    url : /api/team/:id
- GET get team
    url : /api/team/:id
- PATCH add user to team
    url : /api/team/:id/addUser

- PATCH remove user from team
    url : /api/team/:id/removeUser

- GET get all teams
    url : /api/teams

- GET get team users
    url : /api/team/:id/users


> TeamManager
- PATCH update team
    url : /api/team/:id

- GET get team
    url : /api/team/:id

- GET get team users
    url : /api/team/:id/users

- PATH add user to team
    url : /api/team/:id/addUser

- PATH remove user from team
    url : /api/team/:id/removeUser

- GET get all teams
    url : /api/teams

> Visitor
- GET get team
    url : /api/team/:id
- GET get all teams
    url : /api/teams
- GET get team users
    url : /api/team/:id/users



### User Manager
> Admin
- POST create new user
    url : /api/user

- PATCH update user
    url : /api/user/:id

- DELETE delete user
    url : /api/user/:id

- GET get user
    url : /api/user/:id

- GET get all users
    url : /api/users


> User him self
- PATCH update user
    url : /api/user/:id
- GET get user
    url : /api/user/:id
- GET get all users
    url : /api/users


### News Manager

> Admin
- POST create new news
    url : /api/news
- DELETE delete news
    url : /api/news/:id
- PATCH update news
    url : /api/news/:id
- GET get news
    url : /api/news/:id
- GET get all news
    url : /api/news

- POST send email to users
    url : /api/news/:id/sendEmailToUsers

> TeamManager
- GET get news
    url : /api/news/:id
- GET get all news
    url : /api/news


### Group Manager

> Admin
- POST create new group
    - **URL:** /api/group
    - **Body:**

    ```json
    {
        "name": "Group Name",
        "type": "group/steplader",
        "defaultServerConfig": 1, // ServerConfig ID
        "stepLadderState": {},
        "status": "running"
    }
    ```

- PATCH update group
    - **URL:** /api/group/:id
    - **Body:** (update fields as necessary)

    ```json
    {
        "name": "Updated Group Name",
        "type": "steplader",
        "defaultServerConfig": 2,
        "stepLadderState": {},
        "status": "ended"
    }
    ```

- DELETE delete group
    - **URL:** /api/group/:id

- GET get group
    - **URL:** /api/group/:id

- GET get all groups
    - **URL:** /api/groups

### Runner Manager

> Admin
- POST create new runner
    - **URL:** /api/runner
    - **Body:**

    ```json
    {
        "name": "Runner Name",
        "messageBusId": "busId",
        "status": "up",
        "queueStatus": "waiting",
        "tags": [],
        "RunnerServers": [1, 2] // RunnerServer IDs
    }
    ```

- PATCH update runner
    - **URL:** /api/runner/:id
    - **Body:** (update fields as necessary)

    ```json
    {
        "name": "Updated Runner Name",
        "messageBusId": "newBusId",
        "status": "down",
        "queueStatus": "running",
        "tags": [],
        "RunnerServers": [3, 4] // RunnerServer IDs
    }
    ```

- DELETE delete runner
    - **URL:** /api/runner/:id

- GET get runner
    - **URL:** /api/runner/:id

- GET get all runners
    - **URL:** /api/runners

### Game Manager

> Admin
- POST create new match
    - **URL:** /api/match
    - **Body:**

    ```json
    {
        "leftTeam": 1, // Team ID
        "rightTeam": 2, // Team ID
        "ServerConfig": 3, // ServerConfig ID
        "Runner": 4, // Runner ID
        "Group": 5, // Group ID
        "leftTeamScore": 0,
        "rightTeamScore": 0,
        "leftTeamPenaltyScore": 0,
        "rightTeamPenaltyScore": 0,
        "status": "inQueue",
        "priority": 1,
        "startTime": "2023-10-01T00:00:00Z",
        "startedTime": "2023-10-01T00:05:00Z",
        "endTime": "2023-10-01T01:00:00Z",
        "gameLog": 6, // FileUpload ID
        "teamsLog": 7 // FileUpload ID
    }
    ```

- PATCH update match
    - **URL:** /api/match/:id
    - **Body:** (update fields as necessary)

    ```json
    {
        "status": "running",
        "leftTeamScore": 1,
        "rightTeamScore": 1,
    }
    ```

- DELETE delete match
    - **URL:** /api/match/:id

- GET get match
    - **URL:** /api/match/:id

- GET get all matches
    - **URL:** /api/matches

### Server Config Manager

> Admin
- POST create new server config
    - **URL:** /api/serverconfig
    - **Body:**

    ```json
    {
        "name": "Server Config Name",
        "description": "Description of server config",
        "value": {}
    }
    ```

- PATCH update server config
    - **URL:** /api/serverconfig/:id
    - **Body:** (update fields as necessary)

    ```json
    {
        "name": "Updated Server Config Name",
        "description": "Updated description",
        "value": {}
    }
    ```

- DELETE delete server config
    - **URL:** /api/serverconfig/:id

- GET get server config
    - **URL:** /api/serverconfig/:id

- GET get all server configs
    - **URL:** /api/serverconfigs



## Database Models

### User

- id
- UserName
- Email
- PasswordSalt
- PasswordHash
- Role[Admin, Visitor, TeamManager, TeamMember]
- > Team
- photo > FileUpload
- isDeleted
- lastLoginAt
- createdAt
- updatedAt

### Team
- id
- name
- affiliation
- photo > FileUpload
- managerId > User
- isDeleted
- createdAt
- updatedAt

### BinaryUpload

- > Team
- resultStatus [Pass, Fail, NotChecked]
- errorMsg
- binaryFile > FileUpload
- finishedDockerImageHash
- finishedDockerImageFile > FileUpload
- createdBy > User
- createdAt
- updatedAt


### FileUpload
- id 
- fileSaveId
- fileName
- fileType
- fileSize
- isDeleted
- isPublic
- createdBy > User
- createdAt

### ServerConfig
- id
- name
- description
- value [json]
- createdAt
- updatedAt

### News
- id
- title
- content
- brief
- contentType [text,html,markdown,googleDoc, ...]
- visibility [public, team, ...]
- pinned [true, false]
- link
- createdBy > User
- createdAt
- updatedAt


### Runner
- id
- name
- messageBusId
- status [up, down, non-responsive]
- lastHeartBeat
- queueStatus [running, waiting, stopped]
- tags [Array]
- > RunnerServers

### RunnerServer
- id
- ip
- ServerStatus [running, waiting]


### Group
- id
- name
- type [group, steplader, ...]
- >> Teams
- defaultServerConfig > ServerConfig
- stepLadderState [json]
- status [noStatus, running, ended, stopped]
- createdAt
- updatedAt

### Group-Team
- id
- > Group
- > Team
- state [lost]

### Match
- id
- leftTeam > Team
- rightTeam > Team
- > ServerConfig
- ?> Runner
- ?> Group
- leftTeamScore
- rightTeamScore
- leftTeamPenaltyScore
- rightTeamPenaltyScore
- status [noStatus,error , inQueue, running, ended, stopped]
- priority
- startTime [timestamp]
- startedTime [timestamp]
- endTime [timestamp]
- gameLog > FileUpload
- teamsLog > FileUpload
- createdAt
- updatedAt


### Events
- id
- createdBy > User
- operation 
- operationData [json]
- createdAt
