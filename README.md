# websocket_example

Пример кода:

HTML:
```HTML
<!DOCTYPE html>
<html>
<head>
    <title>WebSocket Пример</title>
</head>
<body>
    <h1>Чат</h1>
    <input type="text" id="messageInput" placeholder="Введите сообщение">
    <button onclick="sendMessage()">Отправить</button>
    <ul id="messages"></ul>

    <script src="websocket.js"></script>
</body>
</html>
```
JavaScript (websocket.js):
```JavaScript
let socket = new WebSocket("ws://localhost:8080");

socket.onopen = function() {
    console.log("Соединение установлено.");
};

socket.onmessage = function(event) {
    let messageList = document.getElementById("messages");
    let newMessage = document.createElement("li");
    newMessage.textContent = event.data;
    messageList.appendChild(newMessage);
};

socket.onclose = function(event) {
    if (event.wasClean) {
        console.log(`Соединение закрыто чисто, код=${event.code} причина=${event.reason}`);
    } else {
        console.log('Соединение прервано');
    }
};

socket.onerror = function(error) {
    console.log(`[Ошибка] ${error.message}`);
};

function sendMessage() {
    let messageInput = document.getElementById("messageInput");
    let message = messageInput.value;
    socket.send(message);
    messageInput.value = '';
}
```
Node.js сервер (server.js):
```Node.js
const WebSocket = require('ws');

const wss = new WebSocket.Server({ port: 8080 });

wss.on('connection', function connection(ws) {
    ws.on('message', function incoming(message) {
        console.log('received: %s', message);
        wss.clients.forEach(function each(client) {
            if (client.readyState === WebSocket.OPEN) {
                client.send(message);
            }
        });
    });

    ws.send('Добро пожаловать в чат!');
});
```
