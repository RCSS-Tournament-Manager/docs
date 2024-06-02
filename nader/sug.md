# Sugesstion

## Backend API

### Team Manager

- /api/team/createNewTeam TeamName
- /api/team/createTeamsFromFile TeamsFile
- /api/team/createTeams Teams
- /api/team/addUserToTeam TeamName UserName
- /api/team/removeUserFromTeam TeamName UserName
- /api/team/deleteTeam TeamName
- /api/team/getTeamUsers TeamName
- /api/team/getAllTeams
- /api/team/getTeam TeamName
- /api/team/changeTeamName TeamName NewTeamName

### User Manager

- /api/user/createNewUser UserName Email Password Role[Admin, Visitor, TeamManager] [TeamName]
- /api/user/createUsersFromFile UsersFile
- /api/user/createUsers Users
- /api/user/deleteUser UserName
- /api/user/getUser UserName
- /api/user/getAllUsers
- /api/user/changeUserRole UserName Role
- /api/user/changeUserPassword UserName Password
- /api/user/changeUserEmail UserName Email
- /api/user/changeUserName UserName NewUserName
- /api/user/changeUserTeam UserName TeamName

### News Manager

- /api/news/createNewNews Title Content ContentType[text,html,markdown,googleDoc, ...] Visibility[public, team, ...] pinned[true, false]
- /api/news/deleteNews NewsId
- /api/news/getNews NewsId
- /api/news/getAllNews
- /api/news/changeNewsTitle NewsId NewTitle
- /api/news/changeNewsContent NewsId NewContent
- /api/news/changeNewsVisibility NewsId NewVisibility
- /api/news/changeNewsPinned NewsId NewPinned

### Email Manager

- /api/email/addEmailSmtp Email
- /api/email/removeEmailSmtp Email
- /api/email/sendEmailToAllUsers Title Content
- /api/email/sendEmailToUsers Title Content Users
- /api/email/sendEmailToTeams Title Content Teams

### Group Manager

- /api/createNewGroup GroupName GroupType[group, steplader, ...] [Teams] ServerConfigName[starter, major, ...] [ServerConfigs]
- /api/deleteGroup GroupName
- /api/getGroup GroupName
- /api/getAllGroups
- /api/AddTeamToGroup GroupName TeamName
- /api/removeTeamFromGroup GroupName TeamName
- /api/changeGroupName GroupName NewGroupName
- /api/changeGroupType GroupName NewGroupType
- /api/changeGroupServerConfig GroupName NewServerConfigName
- /api/createGames GroupName

### Runner Manager

### Game Manager

- /api/createNewGame Teams ServerConfigs groupName [RunngerName] [ServerNumber]
- /api/deleteGame GameId
- /api/getGame GameId
- /api/getAllGames
- /api/endGame GameId Results Status
- /api/stopGame GameId
- /api/prioritizeGame GameId Priority
- /api/reAddGame GameId

### Server Config Manager

- /api/uploadMainServerConfig configFile
- /api/getMainServerConfigs
- /api/addServerConfig ServerConfigName Config
- /api/deleteServerConfig ServerConfigName
- /api/getServerConfig ServerConfigName
- /api/changeServerConfig ServerConfigName Config

## Database Models

### User

- UserName
- Email
- Password
- Role
- > Team

### Team

- TeamName
- > Users
- > UploadTeam

### UploadTeam

- TeamName
- VersionName
- ResultStatus [Pass, Fail, NotChecked]
- > Game
- Error
- OrginalFilePath

### MainServerConfig

- ConfigName
- ConfigValue

### ServerConfig

- ServerConfigName
- Configs (str)

### News

- Title
- Content
- ContentType [text,html,markdown,googleDoc, ...]
- Visibility [public, team, ...]
- pinned [true, false]

### EmailSmtp

- Email

### Runner

- RunnerName
- RunnerIP
- RunnerPort
- RunnerStatus [up, down]
- >> RunnerServers

### RunnerServer

- > Runner
- ServerNumber
- ServerStatus [running, waiting]
- ServerPort

### Group

- GroupName
- GroupType [group, steplader, ...]
- >> Teams
- > ServerConfig
- >> Games
- stepLaderCurrentGame

### Game

- > LeftTeam
- > RightTeam
- > ServerConfig
- > Runner
- > ServerNumber
- > Group
- LeftTeamScore
- RightTeamScore
- LeftTeamPenaltyScore
- RightTeamPenaltyScore
- Status [noStatus, inQueue, running, ended, stopped]
- Priority
- StartTime
- StartedTime
- EndTime
- GameLog
- TeamsLog
