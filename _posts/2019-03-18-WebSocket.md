---
layout: default
title:  "Websocket"
date:   2019-03-18 09:25:00
comments: true
categories: other
---

WebSocket is a computer communications protocol, providing full-duplex communication channels over a single TCP connection.  
WebSocket provides a persistent connection between a client and server that both parties can use to start sending data at any time.  

Differ from other solutions for solving allow servers to push data to the client,
WebSocket provides low latency and less complexity.

#### protocol vs  

| protocol | position in OSI | port | communication | efficiency | latency |
| ---- | -----| ---- | ---- | ---- | ---- |
| WebSocket(ws/wss) | 7| 80 443 | full-duplex | good | low |
| Http | 7| 80 443 | not sure | not sure | not sure |


#### protocol handshake 
1. client sends a WebSocket handshake request  
2. server returns a WebSocket handshake response  
3. using **Upgrade** to switch protocol from Http to WebSocket

