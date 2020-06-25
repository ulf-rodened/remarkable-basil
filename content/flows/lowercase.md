---
title: "Example flow lowercase"
layout: page
---

```

[
    {
        "id": "52729e06.f9885",
        "type": "change",
        "z": "8e2b72bf.d1704",
        "name": "lower case",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "$string($lowercase(msg.payload))",
                "tot": "jsonata"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 410,
        "y": 200,
        "wires": [
            [
                "75040c1a.8f69a4"
            ]
        ]
    },
    {
        "id": "27b57703.4c1908",
        "type": "inject",
        "z": "8e2b72bf.d1704",
        "name": "",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "x": 120,
        "y": 200,
        "wires": [
            [
                "c10a1527.b584d8"
            ]
        ]
    },
    {
        "id": "75040c1a.8f69a4",
        "type": "debug",
        "z": "8e2b72bf.d1704",
        "name": "",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "false",
        "x": 580,
        "y": 200,
        "wires": []
    },
    {
        "id": "c10a1527.b584d8",
        "type": "change",
        "z": "8e2b72bf.d1704",
        "name": "string",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "1. LOW-CODE PROGRAMMING FOR EVENT-DRIVEN APPLICATIONS",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 250,
        "y": 200,
        "wires": [
            [
                "52729e06.f9885",
                "9a7d6957.beaee8"
            ]
        ]
    },
    {
        "id": "f0dbf289.ba871",
        "type": "comment",
        "z": "8e2b72bf.d1704",
        "name": "Change string to lower case",
        "info": "",
        "x": 320,
        "y": 80,
        "wires": []
    },
    {
        "id": "9a7d6957.beaee8",
        "type": "debug",
        "z": "8e2b72bf.d1704",
        "name": "",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "false",
        "x": 410,
        "y": 140,
        "wires": []
    }
]

```
