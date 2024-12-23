<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Car Wash Queue Management</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #1a2a6c 0%, #253b80 50%, #182848 100%);
            color: white;
            min-height: 100vh;
            padding: 10px;
        }

        .container {
            max-width: 100%;
            margin: 0 auto;
            padding: 15px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.3);
            backdrop-filter: blur(4px);
        }

        h1 {
            font-size: clamp(1.5rem, 4vw, 2rem);
            text-align: center;
            color: #ffffff;
            text-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
            margin-bottom: 15px;
        }

        h2 {
            font-size: clamp(1.2rem, 3vw, 1.5rem);
            margin-bottom: 15px;
        }

        .clock {
            font-size: clamp(1.5rem, 5vw, 2rem);
            text-align: center;
            margin: 15px auto;
            font-family: 'Digital', monospace;
            text-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
            background: rgba(0, 0, 0, 0.3);
            padding: 10px;
            border-radius: 10px;
            display: inline-block;
            min-width: 200px;
        }

        .timer-container {
            text-align: center;
            margin-bottom: 15px;
        }

        .warning {
            color: #ff4444;
            font-weight: bold;
            text-align: center;
            margin: 15px 0;
            text-shadow: 0 0 10px rgba(255, 0, 0, 0.3);
            font-size: clamp(1rem, 3vw, 1.2rem);
            padding: 0 10px;
        }

        .support {
            text-align: center;
            font-size: clamp(0.8rem, 2.5vw, 0.9rem);
            margin-bottom: 15px;
            background: rgba(255, 255, 255, 0.1);
            padding: 15px;
            border-radius: 10px;
        }

        #adminButton {
            width: 100%;
            max-width: 300px;
            margin: 15px auto;
            padding: 12px;
            background: linear-gradient(145deg, #1e3c72 0%, #2a5298 100%);
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: clamp(1rem, 2.5vw, 1.1rem);
            font-weight: bold;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
            transition: all 0.3s ease;
            text-transform: uppercase;
            display: block;
        }

        .queue-item {
            display: flex;
            flex-direction: column;
            margin: 15px 0;
            padding: 15px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
            gap: 10px;
        }

        .queue-info {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: clamp(0.9rem, 2.5vw, 1rem);
        }

        .progress-container {
            width: 100%;
            height: 25px;
            background: rgba(0, 0, 0, 0.3);
            border-radius: 15px;
            padding: 3px;
            box-shadow: inset 0 2px 5px rgba(0, 0, 0, 0.2);
        }

        .progress-bar {
            height: 100%;
            background: linear-gradient(90deg, #00d2ff 0%, #3a7bd5 100%);
            border-radius: 12px;
            transition: width 0.5s ease-in-out;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
        }

        .input-group {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin-bottom: 15px;
        }

        input {
            width: 100%;
            padding: 12px;
            border: none;
            border-radius: 5px;
            background: rgba(255, 255, 255, 0.9);
            box-shadow: inset 0 2px 5px rgba(0, 0, 0, 0.1);
            font-size: 16px;
        }

        button {
            padding: 12px;
            background: linear-gradient(145deg, #1e3c72 0%, #2a5298 100%);
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
            transition: all 0.3s ease;
            font-size: 16px;
            width: 100%;
        }

        .modal {
            display: none;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: linear-gradient(135deg, #1a2a6c 0%, #253b80 100%);
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
            z-index: 1000;
            width: 90%;
            max-width: 400px;
        }

        .modal input {
            margin-bottom: 10px;
        }

        .overlay {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0, 0, 0, 0.7);
            backdrop-filter: blur(5px);
            z-index: 999;
        }

        @media (min-width: 768px) {
            .container {
                max-width: 800px;
                padding: 20px;
            }

            .queue-item {
                flex-direction: row;
                align-items: center;
            }

            .progress-container {
                margin: 0 15px;
            }

            .input-group {
                flex-direction: row;
            }

            button {
                width: auto;
            }
        }

        @media (hover: hover) {
            #adminButton:hover,
            button:hover {
                transform: translateY(-2px);
                box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
            }

            #adminButton:active,
            button:active {
                transform: translateY(1px);
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Car Wash Queue Status</h1>
        
        <div class="timer-container">
            <div class="clock" id="clock">00:00:00</div>
        </div>

        <p class="warning">ONLY STAFF ALLOWED TO MONITOR QUEUES</p>
        
        <p class="support">
            IT Support: Melato Tatu<br>
            Phone: 073 229 6540<br>
            Email: tatumelato@gmail.com
        </p>

        <button id="adminButton">Staff Access</button>

        <div id="queueDisplay"></div>

        <div id="adminPanel">
            <h2>Queue Management</h2>
            <div class="input-group">
                <input type="text" id="clientName" placeholder="Client Name">
                <input type="text" id="carDetails" placeholder="Car Details">
                <button onclick="addToQueue()">Add to Queue</button>
            </div>
            <div id="adminQueueDisplay"></div>
        </div>
    </div>

    <div class="overlay" id="overlay"></div>
    <div class="modal" id="passwordModal">
        <h3>Enter Staff Password</h3>
        <input type="password" id="passwordInput">
        <button onclick="checkPassword()">Submit</button>
    </div>

    <script>
        const PASSWORD = 'MCW2024';
        let queue = [];
        const WASH_TIME = 20;

        function updateClock() {
            const now = new Date();
            const hours = String(now.getHours()).padStart(2, '0');
            const minutes = String(now.getMinutes()).padStart(2, '0');
            const seconds = String(now.getSeconds()).padStart(2, '0');
            document.getElementById('clock').textContent = `${hours}:${minutes}:${seconds}`;
        }

        setInterval(updateClock, 1000);
        updateClock();

        function updateQueue() {
            const queueDisplay = document.getElementById('queueDisplay');
            const adminQueueDisplay = document.getElementById('adminQueueDisplay');
            queueDisplay.innerHTML = '';
            adminQueueDisplay.innerHTML = '';

            queue.forEach((client, index) => {
                const progress = Math.min(100, (client.progress / WASH_TIME) * 100);
                const estimatedTime = WASH_TIME * (index + 1) - client.progress;

                // Public display
                const itemDiv = document.createElement('div');
                itemDiv.className = 'queue-item';
                itemDiv.innerHTML = `
                    <div class="queue-info">
                        <span>${client.name} - ${client.car}</span>
                        <span>${estimatedTime} min</span>
                    </div>
                    <div class="progress-container">
                        <div class="progress-bar" style="width: ${progress}%"></div>
                    </div>
                `;
                queueDisplay.appendChild(itemDiv);

                // Admin display
                const adminItemDiv = document.createElement('div');
                adminItemDiv.className = 'queue-item';
                adminItemDiv.innerHTML = `
                    <div class="queue-info">
                        <span>${client.name} - ${client.car}</span>
                        <span>${estimatedTime} min</span>
                    </div>
                    <div class="progress-container">
                        <div class="progress-bar" style="width: ${progress}%"></div>
                    </div>
                    <button onclick="removeFromQueue(${index})">Remove</button>
                `;
                adminQueueDisplay.appendChild(adminItemDiv);
            });
        }

        function addToQueue() {
            const name = document.getElementById('clientName').value;
            const car = document.getElementById('carDetails').value;
            if (name && car) {
                queue.push({ name, car, progress: 0 });
                document.getElementById('clientName').value = '';
                document.getElementById('carDetails').value = '';
                updateQueue();
            }
        }

        function removeFromQueue(index) {
            queue.splice(index, 1);
            updateQueue();
        }

        function checkPassword() {
            const password = document.getElementById('passwordInput').value;
            if (password === PASSWORD) {
                document.getElementById('adminPanel').style.display = 'block';
                document.getElementById('passwordModal').style.display = 'none';
                document.getElementById('overlay').style.display = 'none';
            } else {
                alert('Incorrect password');
            }
            document.getElementById('passwordInput').value = '';
        }

        document.getElementById('adminButton').addEventListener('click', () => {
            document.getElementById('passwordModal').style.display = 'block';
            document.getElementById('overlay').style.display = 'block';
        });

        setInterval(() => {
            if (queue.length > 0) {
                queue[0].progress++;
                if (queue[0].progress >= WASH_TIME) {
                    queue.shift();
                }
                updateQueue();
            }
        }, 60000);

        updateQueue();
    </script>
</body>
</html>
