# Passa-Bola-Arduino
https://wokwi.com/projects/442261819600491521

Código Node-Red
[
  {
    "id": "tab_passabola",
    "type": "ui_tab",
    "name": "Passa a Bola",
    "icon": "dashboard",
    "order": 1
  },
  {
    "id": "grp_saude",
    "type": "ui_group",
    "z": "",
    "name": "Saúde em Tempo Real",
    "tab": "tab_passabola",
    "order": 1,
    "disp": true,
    "width": "12",
    "collapse": false
  },
  {
    "id": "broker_mosq",
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
    "id": "mqtt_in_data",
    "type": "mqtt in",
    "z": "flow1",
    "name": "DHT Data",
    "topic": "sensor/dht/Arthur",
    "qos": "0",
    "datatype": "auto",
    "broker": "broker_mosq",
    "nl": false,
    "rap": true,
    "rh": 0,
    "x": 160,
    "y": 120,
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
    "z": "flow1",
    "name": "JSON parse",
    "property": "payload",
    "action": "",
    "pretty": false,
    "x": 350,
    "y": 120,
    "wires": [
      [
        "chg_temp",
        "chg_bpm",
        "chg_spo2",
        "fn_status"
      ]
    ]
  },
  {
    "id": "chg_temp",
    "type": "change",
    "z": "flow1",
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
    "z": "flow1",
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
    "x": 535,
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
    "z": "flow1",
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
    "z": "flow1",
    "name": "Avalia STATUS",
    "func": "// Espera payload: {Temperatura,BPM,SpO2,status}\nvar t = Number(msg.payload.Temperatura);\nvar bpm = Number(msg.payload.BPM);\nvar s = Number(msg.payload.SpO2);\n\nlet status = msg.payload.status || \"OK\"; // o ESP já manda, mas garantimos\n\n// Recalcula se quiser garantir lógica local\nfunction nivelTemp(x){ if(isNaN(x)) return 1; if(x>38.5) return 2; if(x>37.5) return 1; return 0; }\nfunction nivelBpm(x){ if(x>150||x<40) return 2; if(x>140||x<50) return 1; if(x>=60&&x<=100) return 0; return 1; }\nfunction nivelSpO2(x){ if(x<90) return 2; if(x<=94) return 1; return 0; }\n\nconst nivel = Math.max(nivelTemp(t), Math.max(nivelBpm(bpm), nivelSpO2(s)));\nif(nivel===0) status = \"OK\"; else if(nivel===1) status = \"ALERTA\"; else status = \"CRITICO\";\n\nreturn { payload: status };\n",
    "outputs": 1,
    "noerr": 0,
    "initialize": "",
    "finalize": "",
    "libs": [],
    "x": 540,
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
    "z": "flow1",
    "name": "Temperatura (°C)",
    "group": "grp_saude",
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
      "#ca3838",
      "#ffC107",
      "#3acc3a"
    ],
    "seg1": "37.5",
    "seg2": "38.5",
    "className": ""
  },
  {
    "id": "g_bpm",
    "type": "ui_gauge",
    "z": "flow1",
    "name": "BPM",
    "group": "grp_saude",
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
      "#ca3838",
      "#ffC107",
      "#3acc3a"
    ],
    "seg1": "50",
    "seg2": "140",
    "className": ""
  },
  {
    "id": "g_spo2",
    "type": "ui_gauge",
    "z": "flow1",
    "name": "SpO₂",
    "group": "grp_saude",
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
      "#ffC107",
      "#3acc3a"
    ],
    "seg1": "90",
    "seg2": "94",
    "className": ""
  },
  {
    "id": "txt_status",
    "type": "ui_text",
    "z": "flow1",
    "group": "grp_saude",
    "order": 4,
    "width": "12",
    "height": "1",
    "name": "STATUS",
    "label": "STATUS",
    "format": "{{msg.payload}}",
    "layout": "row-spread",
    "className": ""
  },
  {
    "id": "chart_all",
    "type": "ui_chart",
    "z": "flow1",
    "name": "Sinais (Tempo)",
    "group": "grp_saude",
    "order": 5,
    "width": "12",
    "height": "6",
    "label": "Temperatura / BPM / SpO₂",
    "chartType": "line",
    "legend": "true",
    "xformat": "HH:mm:ss",
    "interpolate": "linear",
    "nodata": "",
    "dot": false,
    "ymin": "0",
    "ymax": "",
    "removeOlder": "5",
    "removeOlderPoints": "",
    "removeOlderUnit": "minute",
    "cutout": 0,
    "useOneColor": false,
    "colors": [
      "#1f77b4",
      "#ff7f0e",
      "#2ca02c",
      "#d62728",
      "#9467bd",
      "#8c564b",
      "#e377c2",
      "#7f7f7f",
      "#bcbd22",
      "#17becf"
    ],
    "outputs": 1,
    "useDifferentColor": false,
    "className": ""
  },
  {
    "id": "dbg_raw",
    "type": "debug",
    "z": "flow1",
    "name": "DEBUG raw",
    "active": false,
    "tosidebar": true,
    "console": false,
    "tostatus": false,
    "complete": "payload",
    "targetType": "msg",
    "statusVal": "",
    "statusType": "auto",
    "x": 350,
    "y": 80,
    "wires": []
  },
  {
    "id": "ui_switch",
    "type": "ui_switch",
    "z": "flow1",
    "name": "LED Verde (CMD)",
    "label": "LED Verde (CMD)",
    "group": "grp_saude",
    "order": 6,
    "width": "4",
    "height": "1",
    "passthru": true,
    "decouple": "false",
    "topic": "",
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
    "x": 190,
    "y": 260,
    "wires": [
      [
        "mqtt_out_cmd"
      ]
    ]
  },
  {
    "id": "mqtt_out_cmd",
    "type": "mqtt out",
    "z": "flow1",
    "name": "CMD LED",
    "topic": "led/control/Arthur",
    "qos": "",
    "retain": "",
    "respTopic": "",
    "contentType": "",
    "userProps": "",
    "correl": "",
    "expiry": "",
    "broker": "broker_mosq",
    "x": 400,
    "y": 260,
    "wires": []
  }
]
