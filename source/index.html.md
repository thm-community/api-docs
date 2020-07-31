---
title: THM API

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - <a href='https://github.com/thm-community'>By the THM community, for the THM community.</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Introduction

This is a collection of API endpoint related notes gathered from various [contributors on TryHackMe](https://github.com/thm-community). The documentation below should only be used as a reference to build awesome apps. As the information is reliant on community contributors, some things may not be up to date. 

Endpoints change and sometimes it's easier to update bot code then it is to update my own reference for it. If an endpoint or an example doesn't work as expected, I will not be troubleshooting. Thanks! -CMNatic


<aside class="notice">Please don't ask for help with <code>x,y,z</code></aside>


# Authentication


Using routes such as <code>/api/room/room/answer</code> require both a session (CSRF token) and THM account.

# TryHackMe Education
As it stands, you only require API keys for THM Education. These endpoints are somewhat detailed [here](https://docs.tryhackme.com/docs/api-docs/introduction) provide a lot more functionality designed for teachers i.e. controlling student accounts. This programme is invite only.

# Site Statistics
You can query the site for statistics that were previously displayed on the dashboard. This includes:

- User count
- Public room count
- Cloneable rooms count

#### HTTP Request
`GET https://tryhackme.com/api/getstats`

```json
{
    "publicRooms": 219,
    "totalUsers": 110017,
    "cloneableRooms": 1190
}
```
# Querying User Info
- query profile i.e. avatar, points and rank
- query a users rank
- list all badges a user has
- list public rooms they have created
- list all public rooms completed by user
- view activity (streak & heatmap objects)

### Query Profile:

#### HTTP Request
`GET https://tryhackme.com/api/user/{username}`

Parameter | Required | Description
--------- | ------- | -----------
username | true | You must provide a valid username (https://tryhackme.com/p/username)

```json
{
    "userRank": 44,
    "points": 27679,
    "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/a28268c068c06e8c9369329d39c9de22.jpg"
}
```

### Only retrieve a users rank:
`GET https://tryhackme.com/api/usersRank/{username}`

Parameter | Required | Description
--------- | ------- | -----------
username | true | You must provide a valid username (https://tryhackme.com/p/username)

```json
{
    "userRank": 44
}
```

### List all badges a user has:
`GET https://tryhackme.com/api/get-badges/{username}` 

Parameter | Required | Description
--------- | ------- | -----------
username | true | You must provide a valid username (https://tryhackme.com/p/username)

### List all rooms created (public):
This seems to list by user count (popularity) highest -> lowest

`GET https://tryhackme.com/api/created-rooms/{username}`

Parameter | Required | Description
--------- | ------- | -----------
username | true | You must provide a valid username (https://tryhackme.com/p/username)
success  | false | This key appears but I can't figure out its use at the moment

```json
{
    "success": true,
    "rooms": [
        {
            "public": true,
            "image": "https://tryhackme-images.s3.amazonaws.com/room-icons/24de724519d5a2f65efc6ae06fc2bbd9.png",
            "title": "Google Dorking",
            "description": "Explaining how Search Engines work and leveraging them into finding hidden content!",
            "code": "googledorking",
            "users": 8457,
            "tags": [
                "osint",
                "google dorking",
                "dork",
                "beginner",
                "web app",
                "research",
                "researching"
            ],
            "clonedRooms": [],
            "difficulty": "easy",
            "completedUsersNo": 6129,
            "upVotes": 395,
            "type": "walkthrough"
        },
```
### List all rooms completed by User (public):
This appears to list in order of first completed -> latest completed. Although I'm not exactly sure.

`GET http://tryhackme.com/api/all-completed-rooms/{username}`

Parameter | Required | Description
--------- | ------- | -----------
username | true | You must provide a valid username (https://tryhackme.com/p/username)

```json
[
    {
        "image": "https://tryhackme-images.s3.amazonaws.com/room-icons/fafc074a97207f99929f2ee28bea87ac.jpeg",
        "title": "Crack the hash",
        "description": "Cracking hashes challenges",
        "code": "crackthehash",
        "users": 7058,
        "tags": [
            "hash",
            "hashcat",
            "johntheripper",
            "cracking"
        ],
        "type": "challenge",
        "difficulty": "easy"
    },
```

### List recent activity (tasks)
This is how streaks and the activity heatmap is counted, each instance has a YY/MM/DD object.

Heatmap actions:
- deploy-downloadable (material)
- deploy-vm (instance)
- answer-question

Streaks:
- answer-question

`GET http://tryhackme.com/api/get-activity-events/{username}`

Parameter | Required | Description
--------- | ------- | -----------
username | true | You must provide a valid username (https://tryhackme.com/p/username)

```json
        {
            "_id": {
                "year": "2020",
                "month": "01",
                "day": "25",
                "action": "deploy-vm"
            },
            "events": 5
        },
```

# Rooms
The following endpoints require the `room_code` to be provided. This code is the identifier within the url in your browser. For example, the room `https://tryhackme.com/room/blue` will have the `room_code` of `blue`.
- list details of a room 
- list tasks and questions of a public room (free-only unless authenticated)
- list the last 6 rooms released
- list the highest scoring users within specified amount
- list hacktivities (all public rooms)

### List details of a Room:
`GET http://tryhackme.com/api/room/{room_code}`

Parameter | Required | Description
--------- | ------- | -----------
room_code | true | You must provide a valid room code.

```json
{
    "success": true,
    "owner": "DarkStar7471",
    "ownerAvatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/DarkStar7471.png",
    "title": "Blue",
    "description": "Deploy & hack into a Windows machine, leveraging common misconfigurations issues.",
    "image": "https://tryhackme-images.s3.amazonaws.com/room-icons/7717bbc69c486931e503a74f3192a4d8.gif",
    "created": "2019-03-17T23:39:23.554Z",
    "users": 25061,
    "banned": [
        "M3LVS"
    ],
    "type": "walkthrough",
    "public": true,
    "cloneable": false,
    "code": 4,
    "oneTimeFee": 0,
    "difficulty": "easy",
    "freeToUse": true,
    "ctf": false,
    "sponsor": {
        "enabled": false,
        "name": "",
        "file": ""
    },
    "tags": [
        "windows",
        "eternal blue",
        " MS17-010",
        " CVE2017-0144",
        " CVE-2017-0145",
        " CVE-2017-0146",
        "CVE-2017-0147",
        "CVE-2017-0148",
        "SMB",
        "CVE-2017-0143",
        "video"
    ],
    "ipType": "private",
    "simpleRoom": true,
    "writeups": [
        {
            "username": "DarkStar7471",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/DarkStar7471.png",
            "title": "Official Write-up",
            "link": "https://github.com/DarkStar7471/CTF-Win7/blob/master/README.md",
            "posted": "2019-03-22T03:18:29.299Z"
        },
        {
            "username": "DarkStar7471",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/DarkStar7471.png",
            "title": "Official Write-Up",
            "link": "https://github.com/DarkStar7471/CTF-Write-Ups-And-Items/tree/master/TryHackMe/Blue-Win7",
            "posted": "2019-04-28T20:24:04.998Z"
        },
        {
            "username": "MrSeth6797",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/MrSeth6797.jpeg",
            "title": "Blue - Write-up",
            "link": "https://floreaiulianpfa.com/blue-tryhackme/",
            "posted": "2019-07-31T06:53:08.352Z"
        },
        {
            "username": "sckull",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/3e93f56841fa1e4c49cfd6d053e91878.png",
            "title": "Blue - Solucion",
            "link": "https://sckull.github.io/posts/blue-thm/",
            "posted": "2019-09-06T05:32:14.897Z"
        },
        {
            "username": "wuggles",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/wuggles.png",
            "title": "Blue - Walkthrough",
            "link": "https://unicornsec.com/home/tryhackme-blue-t6red",
            "posted": "2019-11-11T01:59:14.440Z"
        },
        {
            "username": "crhystamil",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/f4c502a07b5ba5f4eb452c32b0690120.jpg",
            "title": "Windows Blue ",
            "link": "https://bit.ly/38XoNwk",
            "posted": "2020-03-15T04:05:59.104Z"
        },
        {
            "username": "ctaner",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/8302e5bfcab6fb45c772562966aedad1.png",
            "title": "CTF: TRYHackMe Blue Makinesi Çözümü",
            "link": "https://www.siberguvenlik.web.tr/index.php/2020/04/10/ctf-tryhackme-blue-makinesi-cozumu/",
            "posted": "2020-04-09T21:47:17.483Z"
        },
        {
            "username": "tareq",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/0d20b93812a60f072cbcf2ac64b271a6.png",
            "title": "فيديو عربي - Blue@tryhackme",
            "link": "https://www.youtube.com/watch?v=nX8k5ZIoraE",
            "posted": "2020-04-11T14:41:21.854Z"
        },
        {
            "username": "c0gnitiveFl0w",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/7143694c971e0691c37343719dc22190.png",
            "title": "Blue- Walkthough",
            "link": "https://breakbeforemake.tech/13656/tryhackme-blue",
            "posted": "2020-04-12T18:57:05.340Z"
        },
        {
            "username": "m4sterph0enix",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/63d5591eb7cf3340882f5271611a5ac5.png",
            "title": "Blue Walkthrough",
            "link": "https://www.hacktoday.io/t/blue-walkthrough-oscp-preparation/2875",
            "posted": "2020-04-22T20:17:24.399Z"
        },
        {
            "username": "parasarora06",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/9a4c560c619ebd09defeceb368acae4b.jpeg",
            "title": "Blue Walkthrough",
            "link": "https://parasarora06.blogspot.com/2020/05/tryhackme-blue-walkthrough_2.html",
            "posted": "2020-05-02T11:04:26.444Z"
        },
        {
            "username": "TheMayor",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/6e628f271621c081e52d36ca9c8a869d.png",
            "title": "Blue Walkthrough - No Metasploit Version",
            "link": "https://www.cybersecpadawan.com/2020/05/tryhackme-blue-eternalblue-exploitation.html",
            "posted": "2020-05-07T05:54:24.704Z"
        },
        {
            "username": "MrAnarchy",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/3c8ac54ec4e1f773a6105b6600b59926.jpg",
            "title": "Erebus - Walkthrough",
            "link": "https://erebus.vip/2020/05/20/thm-blue/",
            "posted": "2020-05-23T13:02:12.340Z"
        },
        {
            "username": "Akshay12",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/8c458528207a916665b7f72244fcbd2a.jpg",
            "title": "Blue Walkthrough",
            "link": "https://securityhackerctf.blogspot.com/2020/05/blue-walkthrough-tryhackme.html",
            "posted": "2020-05-30T16:35:51.906Z"
        },
        {
            "username": "hackadvisermx",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/01bfabfcd9c191990d0c2040274a9554.jpg",
            "title": "[SP] Video - Solución - Blue",
            "link": "https://youtu.be/OvMUVGPdSY4",
            "posted": "2020-05-31T04:37:39.667Z"
        },
        {
            "username": "R0101",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/0afc270d7ba7fe695be6694857e882b8.png",
            "title": "Blue CTF Write-up",
            "link": "https://bit.ly/2A7Yat1",
            "posted": "2020-06-06T14:01:57.977Z"
        }
    ],
    "screenee": {
        "active": false
    },
    "headerImage": "https://i.imgur.com/GosxHyQ.jpg",
    "locked": false,
    "checked": {
        "status": "accepted",
        "comments": []
    },
    "forumLink": "https://tryhackme.com/thread/5e1e4a4ee06ddb0f142757b8",
    "views": 180073,
    "scoreType": 0,
    "video": {
        "active": true,
        "free": false
    },
    "completedUsersNo": 10483
}
```

### List Tasks and Questions of a public room:
`GET http://tryhackme.com/api/tasks/{room_code}`

Remember that it will only return the values of free rooms unless you are authenticated and a subscriber. 

The answer is not returned either from my testing (different endpoint?!)


Parameter | Required | Description
--------- | ------- | -----------
room_code | true | You must provide a valid room code.

```json
{
    "success": true,
    "data": [
        {
            "taskTitle": "Recon",
            "taskDesc": "<p style=\"text-align:center;\">Scan and learn what exploit this machine is vulnerable to. Please note that this machine does not respond to ping (ICMP) and may take a few minutes to boot up. <b>This room is not meant to be a boot2root CTF, rather, this is an educational series for complete beginners. Professionals will likely get very little out of this room beyond basic practice as the process here is meant to be beginner-focused. </b></p><p style=\"text-align:center;\"><i>Link to Ice, the sequel to Blue: <a href=\"https://tryhackme.com/room/ice\">Link</a></i></p><p style=\"text-align:center;\"><i>You can check out the third box in this series, Blaster, here: <a href=\"https://tryhackme.com/room/blaster\" target=\"_blank\">Link</a></i></p><p style=\"text-align:center;\">-----------------------------------------</p><p style=\"text-align:center;\"><br /></p><p style=\"margin-bottom:0px;text-align:center;\">The virtual machine used in this room (Blue) can be downloaded for offline usage from <a href=\"https://darkstar7471.com/resources.html\">https://darkstar7471.com/resources.html</a><a href=\"https://darkstar7471.com/resources.html\"></a></p><p style=\"margin-bottom:0px;text-align:center;\"><br /></p><p style=\"margin-bottom:1rem;text-align:center;\"><i>Enjoy the room! For future rooms and write-ups, follow <a href=\"https://twitter.com/darkstar7471\">@darkstar7471</a> on Twitter.</i></p>",
            "taskType": "THM",
            "taskNo": 1,
            "taskCreated": "2019-03-19T21:54:54.022Z",
            "taskDeadline": null,
            "tasksInfo": [],
            "uploadId": "5c8ec51594a5767fa67f7060",
            "questions": [
                {
                    "questionNo": 1,
                    "question": "<p>Scan the machine. (If you are unsure how to tackle this, I recommend checking out the room <a href=\"https://tryhackme.com/room/rpnmap\">RP: Nmap</a>)</p>",
                    "hint": "Command: nmap -sV -vv --script vuln TARGET_IP"
                },
                {
                    "questionNo": 2,
                    "question": "<p>How many ports are open with a port number under 1000?</p>",
                    "hint": "Near the top of the nmap output: PORT STATE SERVICE "
                },
                {
                    "questionNo": 3,
                    "question": "<p>What is this machine vulnerable to? (Answer in the form of: ms??-???, ex: ms08-067)</p>",
                    "hint": "Revealed by the ShadowBrokers, exploits an issue within SMBv1"
                }
            ]
        },
```
### List the last 6 rooms released:
`GET http://tryhackme.com/api/new-rooms`

These rooms can be either walkthrough or challenge type. I could not query this endpoint to return by type. I don't know why you can only return up to 6 of the latest rooms either.

The answer is not returned either from my testing (different endpoint?!)

Also, this endpoint has been subject to change quite frequently.


```json
[
    {
        "image": "https://tryhackme-images.s3.amazonaws.com/room-icons/fd7c6efd1cf9dbb6866c76382fd82397.png",
        "title": "Dave's Blog",
        "description": "My friend Dave made his own blog! ",
        "code": "davesblog",
        "users": 185,
        "tags": [
            "security",
            "nodejs",
            "nosql",
            "binexp",
            "bof",
            "buffer overflow",
            "weekly challenge"
        ],
        "type": "challenge",
        "difficulty": "hard",
        "oneTimeFee": 0,
        "completedUsers": [
            "szymex73",
            "0day",
            "nella17",
            "0X10001",
            "onurshin",
            "necross",
            "allFun",
            "legndery",
            "Zippyrippy",
            "7s26simon"
        ],
        "upVotes": 11,
        "created": "2020-06-26T20:14:28.661Z",
        "published": "2020-06-26T19:04:02.946Z",
        "creator": "jammy"
    },
```

### List highest scoring users within range:
`GET http://tryhackme.com/api/getgraphdata/{user_count}/{room_code}`

This is quite useful for generating your own leaderboards for example. It's quite illogical in the sense that you have to provide user_count THEN room_code.

You also receieve the user info and the points they scored for each question answered in the provided room_code as well timestamps.

Note the `level` object correlates to their profile level i.e. "level":3 = 0x3 where `rankLevel` correlates to their **position in the room - not THM-wide ranking**

Parameter | Required | Description
--------- | ------- | -----------
user_count | true    | Specify the amount of users you want to gather data for (i.e. 3, 10). Query response time is decided by this!!!
room_code | true | You must provide a valid room code.

```json
[
    {
        "name": "lemalealpha",
        "level": 3,
        "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/lemalealpha.jpg",
        "data": [
            "",
            0,
            [
                "Task 1 - Question 1",
                30
            ],
            [
                "Task 1 - Question 2",
                30
            ],
            [
                "Task 1 - Question 3",
                30
            ],
            [
                "Task 2 - Question 1",
                30
            ],
            [
                "Task 2 - Question 2",
                30
            ],
            [
                "Task 2 - Question 3",
                30
            ],
            [
                "Task 2 - Question 4",
                30
            ],
            [
                "Task 2 - Question 5",
                30
            ],
            [
                "Task 3 - Question 1",
                30
            ],
            [
                "Task 3 - Question 2",
                30
            ],
            [
                "Task 3 - Question 3",
                30
            ],
            [
                "Task 3 - Question 4",
                30
            ],
            [
                "Task 3 - Question 5",
                30
            ],
            [
                "Task 3 - Question 6",
                30
            ],
            [
                "Task 3 - Question 7",
                30
            ],
            [
                "Task 3 - Question 8",
                30
            ],
            [
                "Task 4 - Question 1",
                30
            ],
            [
                "Task 4 - Question 2",
                30
            ],
            [
                "Task 5 - Question 1",
                30
            ],
            [
                "Task 5 - Question 2",
                30
            ],
            [
                "Task 5 - Question 3",
                30
            ]
        ],
        "taskData": [
            {
                "taskNo": 1,
                "questionNo": 1,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-21T21:03:01.544Z"
            },
            {
                "taskNo": 1,
                "questionNo": 2,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-22T21:50:05.033Z"
            },
            {
                "taskNo": 1,
                "questionNo": 3,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T06:32:47.496Z"
            },
            {
                "taskNo": 2,
                "questionNo": 1,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T06:33:05.343Z"
            },
            {
                "taskNo": 2,
                "questionNo": 2,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T06:35:01.974Z"
            },
            {
                "taskNo": 2,
                "questionNo": 3,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T06:37:30.127Z"
            },
            {
                "taskNo": 2,
                "questionNo": 4,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T06:36:40.351Z"
            },
            {
                "taskNo": 2,
                "questionNo": 5,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T06:37:07.056Z"
            },
            {
                "taskNo": 3,
                "questionNo": 1,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T14:07:15.651Z"
            },
            {
                "taskNo": 3,
                "questionNo": 2,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T14:08:17.960Z"
            },
            {
                "taskNo": 3,
                "questionNo": 3,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T14:08:35.090Z"
            },
            {
                "taskNo": 3,
                "questionNo": 4,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T14:09:11.954Z"
            },
            {
                "taskNo": 3,
                "questionNo": 5,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T14:09:22.696Z"
            },
            {
                "taskNo": 3,
                "questionNo": 6,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T14:09:35.194Z"
            },
            {
                "taskNo": 3,
                "questionNo": 7,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T14:09:36.739Z"
            },
            {
                "taskNo": 3,
                "questionNo": 8,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-23T14:09:37.788Z"
            },
            {
                "taskNo": 4,
                "questionNo": 1,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-26T21:11:28.546Z"
            },
            {
                "taskNo": 4,
                "questionNo": 2,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-26T21:34:33.836Z"
            },
            {
                "taskNo": 5,
                "questionNo": 1,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-27T13:12:50.543Z"
            },
            {
                "taskNo": 5,
                "questionNo": 2,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-27T14:00:44.195Z"
            },
            {
                "taskNo": 5,
                "questionNo": 3,
                "correct": true,
                "score": 30,
                "timeCorrect": "2019-03-27T13:18:44.387Z"
            }
        ],
        "totalScore": 630,
        "rankNo": 1
```

### List hackitvities:
This is every public room on the site. This is one of the endpoints that you can do most querying to narrow your results for.

As of 29/06/2020 it looks like it may of changed and I haven't had a chance to figure out the new params more then:

type : room type
difficulty : easy , medium , hard

For reference, my previous notes: 

`GET http://tryhackme.com/api/hacktivites/{query}/{order_by}/{difficulty}/{type}`

Parameter | Required | Description
--------- | ------- | -----------
query |  false    | Search by tags or content of room descriptions
order_by | false | Order by `most-popular` (upvotes), `most-users` (most users), `newest` (newest)
difficulty | false | easy, medium, hard
type | false | walkthrough, challenge

##### I.E. challenge rooms (as of 29.06.2020)

`GET http://tryhackme.com/api/hacktivities?difficulty=easy`

```json
[
    {
        "image": "https://tryhackme-images.s3.amazonaws.com/room-icons/fafc074a97207f99929f2ee28bea87ac.jpeg",
        "title": "Crack the hash",
        "description": "Cracking hashes challenges",
        "code": "crackthehash",
        "users": 7059,
        "tags": [
            "hash",
            "hashcat",
            "johntheripper",
            "cracking"
        ],
        "type": "challenge",
        "difficulty": "easy",
        "userCompleted": false,
        "upVotes": 198,
        "date": "2018-11-18T20:45:53.545Z",
        "freeToUse": true,
        "creator": "ben"
    },
    {
        "image": "https://tryhackme-images.s3.amazonaws.com/room-icons/81eb59b8dc0fc1289ab597ba0efd2e50.png",
        "title": "OWASP Juice Shop",
        "description": "This machine uses the OWASP Juice Shop vulnerable web application to learn how to identify and exploit common web application vulnerabilities. This room has been designed for beginners, but can be completed by anyone.",
        "code": "juiceshop",
        "users": 6477,
        "tags": [
            "security",
            "webapplication",
            "owasp",
            "beginner"
        ],
        "type": "challenge",
        "difficulty": "easy",
        "userCompleted": false,
        "upVotes": 92,
        "date": "2018-12-02T17:10:51.638Z",
        "freeToUse": true,
        "creator": "ashu"
    },
    {
```

# KoTh
- get machine pool list
- get info about a game
- list 5 most recent koth games

(thanks to szymex73 for this one)

### Get machine pool list:
`GET http://tryhackme.com/games/koth/get/machine-pool`

```json
[
    {
        "title": "Carnage",
        "os": "linux"
    },
    {
        "title": "Lion",
        "os": "linux"
    },
    {
        "title": "Hackers",
        "os": "linux"
    },
    {
        "title": "Fortune",
        "os": "linux"
```

### Get info about a game:
`GET http://tryhackme.com/games/koth/data/2743`

Parameter | Required | Description
--------- | ------- | -----------
game_id |  true    | You must provide a game id

```json
{
    "success": true,
    "id": 2743,
    "startTime": "2020-05-15T13:43:00.980Z",
    "startTimeRemaining": -3913585.075,
    "finishTime": "2020-05-15T14:43:00.117Z",
    "finishTimeRemaining": -3909985.938,
    "status": "complete",
    "king": {
        "found": false,
        "changes": 1
    },
    "chartData": {
        "series": [
            {
                "name": "cmnatic",
                "currentPoints": 20,
                "data": [
                    20
                ]
            },
            {
                "name": "notcmnatic",
                "currentPoints": 0,
                "data": [
                    0
                ]
            }
        ],
        "labels": [
            "2020-05-15T13:53:18.268Z"
        ],
        "firstHacked": "2020-05-15T13:53:18.268Z"
    },
    "tableData": [
        {
            "username": "cmnatic",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/a28268c068c06e8c9369329d39c9de22.jpg",
            "level": 13,
            "country": "gb",
            "score": 20,
            "flags": 1,
            "kingTime": 0,
            "rank": 1
        },
        {
            "username": "notcmnatic",
            "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/5353534f405df9de8d852946f7c8c3ae.png",
            "level": 1,
            "country": "gb",
            "score": 0,
            "flags": 0,
            "kingTime": 0,
            "rank": 2
        }
    ],

    "box": {
        "title": "Food",
        "os": "linux"
    },
    "flagNo": 8,
    "resets": 0,
    "gameType": "private",
    "createdBy": {
        "success": true,
        "userId": "5de96d9ca744773ea7ef8c00",
        "username": "cmnatic",
        "avatar": "https://tryhackme-images.s3.amazonaws.com/user-avatars/a28268c068c06e8c9369329d39c9de22.jpg"
    }
}
```

### List 5 most recent games
`GET http://tryhackme.com/games/koth/recent/games`

The json output is the same format as the above but just x5 over so I'm not going to repost.


