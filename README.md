<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mindful Detox Break</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            height: 100vh;
            overflow: hidden;
            touch-action: none;
            -webkit-user-select: none;
            user-select: none;
        }
        
        .container {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            text-align: center;
            padding: 20px;
        }
        
        .timer {
            font-size: 4rem;
            font-weight: bold;
            margin: 2rem 0;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }
        
        .message {
            font-size: 1.5rem;
            margin-bottom: 2rem;
            max-width: 500px;
        }
        
        .breathing-circle {
            width: 200px;
            height: 200px;
            border-radius: 50%;
            background: rgba(255,255,255,0.2);
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 2rem 0;
            animation: breathe 8s infinite ease-in-out;
        }
        
        @keyframes breathe {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.2); }
        }
        
        .tip {
            background: rgba(255,255,255,0.1);
            padding: 1rem;
            border-radius: 10px;
            margin: 1rem 0;
            max-width: 400px;
        }
        
        .exit-warning {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.9);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }
        
        .warning-content {
            background: white;
            color: black;
            padding: 2rem;
            border-radius: 15px;
            text-align: center;
            max-width: 300px;
        }
        
        .hidden {
            display: none;
        }
        
        .setup-screen {
            background: white;
            color: #333;
            padding: 2rem;
            border-radius: 15px;
            max-width: 400px;
        }
        
        input, button {
            padding: 12px;
            margin: 10px;
            border: none;
            border-radius: 8px;
            font-size: 1rem;
        }
        
        button {
            background: #667eea;
            color: white;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div id="setupScreen" class="container setup-screen">
        <h2>🌿 Digital Detox Break</h2>
        <p>Set your mindful break duration:</p>
        <input type="number" id="minutesInput" min="1" max="30" value="2" placeholder="Minutes">
        <button onclick="startDetox()">Start Detox</button>
        <p style="font-size: 0.9rem; margin-top: 1rem; color: #666;">
            Note: You can still exit using phone buttons. This is for mindfulness, not restriction.
        </p>
    </div>

    <div id="detoxScreen" class="container hidden">
        <div class="timer" id="timer">02:00</div>
        <div class="message" id="message">Breathe deeply and be present</div>
        
        <div class="breathing-circle">
            <span style="font-size: 1.2rem;">Breathe</span>
        </div>
        
        <div class="tip" id="tip">
            Focus on your breathing. Inhale slowly, exhale completely.
        </div>
        
        <div style="margin-top: 2rem;">
            <button onclick="showExitWarning()" style="background: #e74c3c;">Emergency Exit</button>
        </div>
    </div>

    <div id="exitWarning" class="exit-warning">
        <div class="warning-content">
            <h3>Are you sure?</h3>
            <p>Completing your detox break can help reset your focus.</p>
            <button onclick="continueDetox()" style="background: #27ae60;">Continue Detox</button>
            <button onclick="endDetox()" style="background: #e74c3c;">Exit Now</button>
        </div>
    </div>

    <script>
        let detoxTimer;
        let totalSeconds = 120;
        let isRunning = false;

        // Prevent some common gestures
        document.addEventListener('touchmove', function(e) {
            if (isRunning) {
                e.preventDefault();
            }
        }, { passive: false });

        document.addEventListener('contextmenu', function(e) {
            if (isRunning) {
                e.preventDefault();
            }
        });

        function startDetox() {
            const minutes = parseInt(document.getElementById('minutesInput').value) || 2;
            totalSeconds = minutes * 60;
            
            document.getElementById('setupScreen').classList.add('hidden');
            document.getElementById('detoxScreen').classList.remove('hidden');
            
            // Request fullscreen
            if (document.documentElement.requestFullscreen) {
                document.documentElement.requestFullscreen();
            }
            
            startTimer();
            isRunning = true;
        }

        function startTimer() {
            updateTimerDisplay();
            
            detoxTimer = setInterval(() => {
                totalSeconds--;
                updateTimerDisplay();
                
                if (totalSeconds <= 0) {
                    endDetoxSuccess();
                }
            }, 1000);
        }

        function updateTimerDisplay() {
            const minutes = Math.floor(totalSeconds / 60);
            const seconds = totalSeconds % 60;
            document.getElementById('timer').textContent = 
                `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
            
            // Update messages based on time left
            updateMessages();
        }

        function updateMessages() {
            const messages = [
                "Breathe deeply and be present",
                "Notice the sensations in your body",
                "Let thoughts come and go like clouds",
                "You are exactly where you need to be",
                "This moment is perfect as it is"
            ];
            
            const tips = [
                "Focus on your breathing. Inhale slowly, exhale completely.",
                "Scan your body from head to toe. Notice any tension.",
                "Listen to the sounds around you without judgment.",
                "Feel the air on your skin. Notice the temperature.",
                "Observe your thoughts without getting caught in them."
            ];
            
            const index = Math.floor(totalSeconds / 30) % messages.length;
            document.getElementById('message').textContent = messages[index];
            document.getElementById('tip').textContent = tips[index];
        }

        function showExitWarning() {
            document.getElementById('exitWarning').style.display = 'flex';
        }

        function continueDetox() {
            document.getElementById('exitWarning').style.display = 'none';
        }

        function endDetox() {
            clearInterval(detoxTimer);
            isRunning = false;
            
            if (document.exitFullscreen) {
                document.exitFullscreen();
            }
            
            // Return to setup
            document.getElementById('exitWarning').style.display = 'none';
            document.getElementById('detoxScreen').classList.add('hidden');
            document.getElementById('setupScreen').classList.remove('hidden');
        }

        function endDetoxSuccess() {
            clearInterval(detoxTimer);
            isRunning = false;
            
            if (document.exitFullscreen) {
                document.exitFullscreen();
            }
            
            alert('🎉 Great job! Your detox break is complete. Hope you feel refreshed!');
            document.getElementById('detoxScreen').classList.add('hidden');
            document.getElementById('setupScreen').classList.remove('hidden');
        }

        // Handle fullscreen change
        document.addEventListener('fullscreenchange', function() {
            if (!document.fullscreenElement && isRunning) {
                showExitWarning();
            }
        });

        // Prevent back button
        history.pushState(null, null, location.href);
        window.onpopstate = function(event) {
            history.go(1);
            if (isRunning) {
                showExitWarning();
            }
        };
    </script>
</body>
</html>
