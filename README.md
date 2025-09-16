# Passa-Bola-Arduino
https://wokwi.com/projects/442277443782837249

Código Node-Red:



[
    {
        "id": "flow_passabola",
        "type": "tab",
        "label": "Passa a Bola",
        "disabled": false,
        "info": ""
    },
    {
        "id": "mqtt_in_data",
        "type": "mqtt in",
        "z": "flow_passabola",
        "name": "DHT Data",
        "topic": "sensor/dht/Arthur",
        "qos": "0",
        "datatype": "auto",
        "broker": "mqtt_broker_mosq",
        "nl": false,
        "rap": true,
        "rh": 0,
        "inputs": 0,
        "x": 120,
        "y": 140,
        "wires": [
            [
                "json_parse",
                "dbg_raw"
            ]
        ]
    },
    {
        "id": "json_parse",
        "type": "json",
        "z": "flow_passabola",
        "name": "JSON parse",
        "property": "payload",
        "action": "",
        "pretty": false,
        "x": 330,
        "y": 140,
        "wires": [
            [
                "chg_temp",
                "chg_bpm",
                "chg_spo2",
                "fn_status",
                "91c544df461b4914"
            ]
        ]
    },
    {
        "id": "chg_temp",
        "type": "change",
        "z": "flow_passabola",
        "name": "→ Temp",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.Temperatura",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "Temperatura",
                "tot": "str"
            }
        ],
        "x": 540,
        "y": 80,
        "wires": [
            [
                "g_temp",
                "chart_all"
            ]
        ]
    },
    {
        "id": "chg_bpm",
        "type": "change",
        "z": "flow_passabola",
        "name": "→ BPM",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.BPM",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "BPM",
                "tot": "str"
            }
        ],
        "x": 540,
        "y": 120,
        "wires": [
            [
                "g_bpm",
                "chart_all"
            ]
        ]
    },
    {
        "id": "chg_spo2",
        "type": "change",
        "z": "flow_passabola",
        "name": "→ SpO2",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "payload.SpO2",
                "tot": "jsonata"
            },
            {
                "t": "set",
                "p": "topic",
                "pt": "msg",
                "to": "SpO2",
                "tot": "str"
            }
        ],
        "x": 540,
        "y": 160,
        "wires": [
            [
                "g_spo2",
                "chart_all"
            ]
        ]
    },
    {
        "id": "fn_status",
        "type": "function",
        "z": "flow_passabola",
        "name": "Avalia STATUS",
        "func": "// Espera payload: {Temperatura,BPM,SpO2,status}\nvar t = Number(msg.payload.Temperatura);\nvar bpm = Number(msg.payload.BPM);\nvar s = Number(msg.payload.SpO2);\n\nlet status = msg.payload.status || 'OK'; // o ESP já manda, mas garantimos\n\n// Recalcula localmente (opcional)\nfunction nivelTemp(x){ if(isNaN(x)) return 1; if(x>38.5) return 2; if(x>37.5) return 1; return 0; }\nfunction nivelBpm(x){ if(x>150||x<40) return 2; if(x>140||x<50) return 1; if(x>=60&&x<=100) return 0; return 1; }\nfunction nivelSpO2(x){ if(x<90) return 2; if(x<=94) return 1; return 0; }\n\nconst nivel = Math.max(nivelTemp(t), Math.max(nivelBpm(bpm), nivelSpO2(s)));\nif(nivel===0) status = 'OK';\nelse if(nivel===1) status = 'ALERTA';\nelse status = 'CRITICO';\n\nreturn { payload: status };",
        "outputs": 1,
        "timeout": "",
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 560,
        "y": 200,
        "wires": [
            [
                "txt_status"
            ]
        ]
    },
    {
        "id": "g_temp",
        "type": "ui_gauge",
        "z": "flow_passabola",
        "name": "Temperatura (°C)",
        "group": "ui_group_saude",
        "order": 1,
        "width": "4",
        "height": "4",
        "gtype": "gage",
        "title": "Temperatura (°C)",
        "label": "°C",
        "format": "{{value}}",
        "min": 34,
        "max": 41,
        "colors": [
            "#3acc3a",
            "#ffc107",
            "#ca3838"
        ],
        "seg1": "37.5",
        "seg2": "38.5",
        "className": "",
        "x": 950,
        "y": 160,
        "wires": []
    },
    {
        "id": "g_bpm",
        "type": "ui_gauge",
        "z": "flow_passabola",
        "name": "BPM",
        "group": "ui_group_saude",
        "order": 2,
        "width": "4",
        "height": "4",
        "gtype": "gage",
        "title": "Batimentos (BPM)",
        "label": "BPM",
        "format": "{{value}}",
        "min": 40,
        "max": 180,
        "colors": [
            "#3acc3a",
            "#ffc107",
            "#ea1a1a"
        ],
        "seg1": "50",
        "seg2": "140",
        "diff": false,
        "className": "",
        "x": 910,
        "y": 100,
        "wires": []
    },
    {
        "id": "g_spo2",
        "type": "ui_gauge",
        "z": "flow_passabola",
        "name": "SpO₂",
        "group": "ui_group_saude",
        "order": 3,
        "width": "4",
        "height": "4",
        "gtype": "gage",
        "title": "Oxigenação (SpO₂)",
        "label": "%",
        "format": "{{value}}",
        "min": 80,
        "max": 100,
        "colors": [
            "#ca3838",
            "#ffc107",
            "#3acc3a"
        ],
        "seg1": "90",
        "seg2": "94",
        "diff": false,
        "className": "",
        "x": 910,
        "y": 40,
        "wires": []
    },
    {
        "id": "chart_all",
        "type": "ui_chart",
        "z": "flow_passabola",
        "name": "Sinais (Tempo)",
        "group": "ui_group_saude",
        "order": 5,
        "width": "12",
        "height": "6",
        "label": "Temperatura / BPM / SpO₂",
        "chartType": "horizontalBar",
        "legend": "true",
        "xformat": "HH:mm:ss",
        "interpolate": "linear",
        "nodata": "",
        "dot": true,
        "ymin": "0",
        "ymax": "",
        "removeOlder": "5",
        "removeOlderPoints": "",
        "removeOlderUnit": "minute",
        "cutout": 0,
        "useOneColor": false,
        "useUTC": false,
        "colors": [
            "#1f77b4",
            "#ff7f0e",
            "#2ca02c",
            "#d62728",
            "#9467bd",
            "#8c564b",
            "#e377c2",
            "#7f7f7f",
            "#bcbd22"
        ],
        "outputs": 1,
        "useDifferentColor": false,
        "className": "",
        "x": 940,
        "y": 220,
        "wires": [
            []
        ]
    },
    {
        "id": "mqtt_in_status",
        "type": "mqtt in",
        "z": "flow_passabola",
        "name": "Status (LWT)",
        "topic": "sensor/dht/status/Arthur",
        "qos": "0",
        "datatype": "auto",
        "broker": "mqtt_broker_mosq",
        "nl": false,
        "rap": true,
        "rh": 0,
        "inputs": 0,
        "x": 130,
        "y": 340,
        "wires": [
            [
                "txt_conn"
            ]
        ]
    },
    {
        "id": "txt_conn",
        "type": "ui_text",
        "z": "flow_passabola",
        "group": "ui_group_saude",
        "order": 0,
        "width": "12",
        "height": "1",
        "name": "Dispositivo",
        "label": "Dispositivo",
        "format": "{{msg.payload}}",
        "layout": "row-spread",
        "className": "",
        "style": false,
        "font": "",
        "fontSize": "",
        "color": "#000000",
        "x": 930,
        "y": 340,
        "wires": []
    },
    {
        "id": "ui_switch_led",
        "type": "ui_switch",
        "z": "flow_passabola",
        "name": "LED Verde (CMD) ",
        "label": "LED Verde (CMD) ",
        "tooltip": "",
        "group": "ui_group_saude",
        "order": 6,
        "width": "4",
        "height": "1",
        "passthru": true,
        "decouple": "false",
        "topic": "",
        "topicType": "str",
        "style": "",
        "onvalue": "1",
        "onvalueType": "str",
        "onicon": "",
        "oncolor": "",
        "offvalue": "0",
        "offvalueType": "str",
        "officon": "",
        "offcolor": "",
        "animate": false,
        "className": "",
        "x": 130,
        "y": 420,
        "wires": [
            [
                "mqtt_out_cmd"
            ]
        ]
    },
    {
        "id": "mqtt_out_cmd",
        "type": "mqtt out",
        "z": "flow_passabola",
        "name": "CMD LED ",
        "topic": "led/control/Arthur",
        "qos": "",
        "retain": "",
        "respTopic": "",
        "contentType": "",
        "userProps": "",
        "correl": "",
        "expiry": "",
        "broker": "mqtt_broker_mosq",
        "x": 420,
        "y": 420,
        "wires": []
    },
    {
        "id": "dbg_raw",
        "type": "debug",
        "z": "flow_passabola",
        "name": "DEBUG raw",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "payload",
        "targetType": "msg",
        "statusVal": "",
        "statusType": "auto",
        "x": 330,
        "y": 100,
        "wires": []
    },
    {
        "id": "txt_status",
        "type": "ui_text",
        "z": "flow_passabola",
        "group": "ui_group_saude",
        "order": 4,
        "width": "12",
        "height": "1",
        "name": "STATUS",
        "label": "STATUS",
        "format": "{{msg.payload}}",
        "layout": "row-spread",
        "className": "",
        "style": false,
        "font": "",
        "fontSize": "",
        "color": "#000000",
        "x": 920,
        "y": 280,
        "wires": []
    },
    {
        "id": "91c544df461b4914",
        "type": "function",
        "z": "flow_passabola",
        "name": "Para Chart (3 séries)",
        "func": "const t   = Number(msg.payload.Temperatura ?? msg.payload.temperatura);\nconst bpm = Number(msg.payload.BPM         ?? msg.payload.bpm);\nconst s   = Number(msg.payload.SpO2        ?? msg.payload.spo2);\nif (isNaN(t) || isNaN(bpm) || isNaN(s)) return null;\nreturn [\n  { topic: 'Temperatura', payload: t },\n  { topic: 'BPM',         payload: bpm },\n  { topic: 'SpO2',        payload: s }\n];\n",
        "outputs": 1,
        "timeout": "",
        "noerr": 0,
        "initialize": "",
        "finalize": "",
        "libs": [],
        "x": 580,
        "y": 240,
        "wires": [
            [
                "chart_all"
            ]
        ]
    },
    {
        "id": "d03a8133812cf32c",
        "type": "inject",
        "z": "flow_passabola",
        "name": "Inject de teste",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "",
        "payload": "{\"Temperatura\":31.8,\"BPM\":85,\"SpO2\":97,\"status\":\"OK\"}",
        "payloadType": "str",
        "x": 130,
        "y": 220,
        "wires": [
            [
                "json_parse"
            ]
        ]
    },
    {
        "id": "mqtt_broker_mosq",
        "type": "mqtt-broker",
        "name": "Mosquitto Public",
        "broker": "test.mosquitto.org",
        "port": "1883",
        "clientid": "",
        "usetls": false,
        "protocolVersion": "4",
        "keepalive": "60",
        "cleansession": true,
        "birthTopic": "",
        "birthQos": "0",
        "birthPayload": "",
        "closeTopic": "",
        "closePayload": "",
        "willTopic": "",
        "willQos": "0",
        "willPayload": ""
    },
    {
        "id": "ui_group_saude",
        "type": "ui_group",
        "name": "Saúde em Tempo Real",
        "tab": "ui_tab_passabola",
        "order": 1,
        "disp": true,
        "width": "12",
        "collapse": false
    },
    {
        "id": "ui_tab_passabola",
        "type": "ui_tab",
        "name": "Passa a Bola",
        "icon": "dashboard",
        "order": 1
    },
    {
        "id": "a1a4c76c4e8a815e",
        "type": "global-config",
        "env": [],
        "modules": {
            "node-red-dashboard": "3.6.6"
        }
    }
]
