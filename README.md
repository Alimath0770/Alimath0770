<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>غرف الدردشة</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 20px;
        }
        .chat-room {
            border: 1px solid #ccc;
            border-radius: 5px;
            padding: 10px;
            margin-bottom: 20px;
            background-color: white;
        }
        .chat-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .chat-messages {
            max-height: 200px;
            overflow-y: auto;
            margin: 10px 0;
            border: 1px solid #ccc;
            padding: 5px;
        }
        .button {
            padding: 5px 10px;
            font-size: 14px;
            cursor: pointer;
        }
        .input-group {
            margin: 10px 0;
        }
        .background-selector {
            margin: 5px 0;
        }
    </style>
</head>
<body>

<h1>غرف الدردشة</h1>

<div>
    <input id="email" type="email" placeholder="أدخل بريدك الإلكتروني" />
    <input id="roomName" type="text" placeholder="اسم الغرفة" />
    <button class="button" onclick="createRoom()">إنشاء غرفة</button>
</div>

<div id="roomsContainer"></div>

<script>
    const rooms = {};

    function createRoom() {
        const roomName = document.getElementById("roomName").value;
        const email = document.getElementById("email").value;

        if (!email || !roomName) {
            alert("يرجى إدخال بريدك الإلكتروني واسم الغرفة");
            return;
        }

        if (rooms[roomName]) {
            alert("الغرفة موجودة بالفعل");
            return;
        }

        rooms[roomName] = {
            admin: email,
            users: [],
            messages: [],
            background: '#ffffff'
        };

        renderRooms();
        document.getElementById("roomName").value = '';
    }

    function renderRooms() {
        const roomsContainer = document.getElementById("roomsContainer");
        roomsContainer.innerHTML = '';

        for (const roomName in rooms) {
            const room = rooms[roomName];
            const roomDiv = document.createElement("div");
            roomDiv.className = "chat-room";
            roomDiv.style.backgroundColor = room.background;

            const header = document.createElement("div");
            header.className = "chat-header";
            header.innerHTML = `<strong>${roomName}</strong>
                               <button class="button" onclick="deleteRoom('${roomName}')">حذف</button>`;
            roomDiv.appendChild(header);

            const messagesDiv = document.createElement("div");
            messagesDiv.className = "chat-messages";
            messagesDiv.innerHTML = room.messages.map(msg => `<div>${msg}</div>`).join('');
            roomDiv.appendChild(messagesDiv);

            const inputGroup = document.createElement("div");
            inputGroup.className = "input-group";

            const userNameInput = document.createElement("input");
            userNameInput.placeholder = "اسمك";
            userNameInput.id = `user-${roomName}`;
            inputGroup.appendChild(userNameInput);

            const messageInput = document.createElement("input");
            messageInput.placeholder = "رسالتك";
            inputGroup.appendChild(messageInput);

            const sendButton = document.createElement("button");
            sendButton.className = "button";
            sendButton.innerText = "إرسال";
            sendButton.onclick = () => sendMessage(roomName, userNameInput.value, messageInput.value);
            inputGroup.appendChild(sendButton);

            const backgroundInput = document.createElement("input");
            backgroundInput.type = "color";
            backgroundInput.className = "background-selector";
            backgroundInput.onchange = (e) => changeBackground(roomName, e.target.value);
            inputGroup.appendChild(backgroundInput);

            const exitButton = document.createElement("button");
            exitButton.className = "button";
            exitButton.innerText = "خروج";
            exitButton.onclick = () => exitRoom(roomName, userNameInput.value);
            inputGroup.appendChild(exitButton);

            roomDiv.appendChild(inputGroup);
            roomsContainer.appendChild(roomDiv);
        }
    }

    function deleteRoom(roomName) {
        if (confirm("هل أنت متأكد من حذف الغرفة؟")) {
            delete rooms[roomName];
            renderRooms();
        }
    }

    function sendMessage(roomName, userName, message) {
        if (!userName || !message) {
            alert("يرجى إدخال الاسم والرسالة");
            return;
        }

        if (!rooms[roomName].users.includes(userName)) {
            rooms[roomName].users.push(userName);
        }

        rooms[roomName].messages.push(`${userName}: ${message}`);
        renderRooms();
    }

    function changeBackground(roomName, color) {
        rooms[roomName].background = color;
        renderRooms();
    }

    function exitRoom(roomName, userName) {
        const users = rooms[roomName].users;
        const index = users.indexOf(userName);
        if (index > -1) {
            users.splice(index, 1);
            alert(`${userName} خرج من الغرفة.`);
        } else {
            alert("لم تكن في الغرفة.");
        }
        renderRooms();
    }
</script>
</body>
</html>
