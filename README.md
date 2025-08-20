<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Corazón Interactivo</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background: linear-gradient(45deg, #1a1a2e, #16213e, #0f3460);
            font-family: 'Georgia', serif;
            overflow: hidden;
            position: relative;
        }

        .sparkles {
            position: absolute;
            width: 100%;
            height: 100%;
            pointer-events: none;
        }

        .sparkle {
            position: absolute;
            color: rgba(255, 255, 255, 0.8);
            font-size: 1.2em;
            animation: twinkle 2s infinite;
        }

        .heart-container {
            position: relative;
            display: flex;
            flex-direction: column;
            align-items: center;
            cursor: pointer;
            user-select: none;
        }

        .heart {
            font-size: 8em;
            transition: all 0.4s cubic-bezier(0.68, -0.55, 0.265, 1.55);
            filter: drop-shadow(0 0 20px currentColor);
            animation: gentlePulse 3s infinite;
            position: relative;
            z-index: 2;
        }

        .heart.clicked {
            transform: scale(1.3);
            animation: heartBurst 0.6s ease-out;
        }

        .heart.red { color: #ff1744; }
        .heart.pink { color: #ff4081; }
        .heart.purple { color: #e91e63; }
        .heart.blue { color: #2196f3; }
        .heart.gold { color: #ffd700; }
        .heart.green { color: #4caf50; }
        .heart.orange { color: #ff5722; }
        .heart.cyan { color: #00bcd4; }

        .love-phrase {
            margin-top: 40px;
            font-size: 1.5em;
            color: #ffffff;
            text-align: center;
            max-width: 600px;
            padding: 20px;
            border-radius: 15px;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            opacity: 0;
            transform: translateY(30px);
            transition: all 0.6s ease;
            line-height: 1.6;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }

        .love-phrase.show {
            opacity: 1;
            transform: translateY(0);
        }

        .counter {
            margin-top: 20px;
            font-size: 1.1em;
            color: rgba(255, 255, 255, 0.7);
            text-align: center;
        }

        .instruction {
            position: absolute;
            top: 50px;
            font-size: 1.2em;
            color: rgba(255, 255, 255, 0.8);
            text-align: center;
            animation: fadeInOut 4s infinite;
        }

        .heart-particles {
            position: absolute;
            pointer-events: none;
        }

        .particle {
            position: absolute;
            font-size: 1.5em;
            animation: floatAway 2s ease-out forwards;
            opacity: 0;
        }

        .color-indicator {
            position: absolute;
            bottom: 50px;
            display: flex;
            gap: 15px;
            align-items: center;
        }

        .color-dot {
            width: 20px;
            height: 20px;
            border-radius: 50%;
            border: 2px solid rgba(255, 255, 255, 0.3);
            transition: all 0.3s ease;
            cursor: pointer;
        }

        .color-dot.active {
            transform: scale(1.3);
            border-color: rgba(255, 255, 255, 0.8);
            box-shadow: 0 0 15px currentColor;
        }

        .color-dot.red { background-color: #ff1744; }
        .color-dot.pink { background-color: #ff4081; }
        .color-dot.purple { background-color: #e91e63; }
        .color-dot.blue { background-color: #2196f3; }
        .color-dot.gold { background-color: #ffd700; }
        .color-dot.green { background-color: #4caf50; }
        .color-dot.orange { background-color: #ff5722; }
        .color-dot.cyan { background-color: #00bcd4; }

        @keyframes gentlePulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }

        @keyframes heartBurst {
            0% { transform: scale(1); }
            30% { transform: scale(1.4); }
            60% { transform: scale(1.2); }
            100% { transform: scale(1.1); }
        }

        @keyframes twinkle {
            0%, 100% { 
                opacity: 0; 
                transform: scale(0.5) rotate(0deg);
            }
            50% { 
                opacity: 1; 
                transform: scale(1) rotate(180deg);
            }
        }

        @keyframes fadeInOut {
            0%, 100% { opacity: 0.6; }
            50% { opacity: 1; }
        }

        @keyframes floatAway {
            0% {
                opacity: 1;
                transform: translate(0, 0) scale(1) rotate(0deg);
            }
            100% {
                opacity: 0;
                transform: translate(var(--x), var(--y)) scale(0.3) rotate(360deg);
            }
        }

        @media (max-width: 768px) {
            .heart {
                font-size: 6em;
            }
            
            .love-phrase {
                font-size: 1.3em;
                margin-top: 30px;
                padding: 15px;
                max-width: 90%;
            }
            
            .instruction {
                font-size: 1em;
                top: 30px;
                padding: 0 20px;
            }
        }
    </style>
</head>
<body>
    <div class="sparkles" id="sparkles"></div>
    
    <div class="instruction">
        💖 TATIANA TE AMO 💖
    </div>

    <div class="heart-container" onclick="clickHeart()">
        <div class="heart red" id="heart">💖</div>
        <div class="heart-particles" id="particles"></div>
    </div>

    <div class="love-phrase" id="lovePhrase">
        Haz clic en el corazón para ver una frase de amor
    </div>

    <div class="counter" id="counter">
        Clics de amor: <span id="clickCount">0</span>
    </div>

    <div class="color-indicator">
        <div class="color-dot red active" onclick="changeHeartColor('red')"></div>
        <div class="color-dot pink" onclick="changeHeartColor('pink')"></div>
        <div class="color-dot purple" onclick="changeHeartColor('purple')"></div>
        <div class="color-dot blue" onclick="changeHeartColor('blue')"></div>
        <div class="color-dot gold" onclick="changeHeartColor('gold')"></div>
        <div class="color-dot green" onclick="changeHeartColor('green')"></div>
        <div class="color-dot orange" onclick="changeHeartColor('orange')"></div>
        <div class="color-dot cyan" onclick="changeHeartColor('cyan')"></div>
    </div>

    <script>
        const lovePhrases = [
            "Eres la melodía que hace que mi corazón baile 💕",
            "En cada latido de mi corazón susurro tu nombre ❤️",
            "Contigo, cada día es un nuevo verso de amor 🌹",
            "Eres mi estrella favorita en el cielo nocturno ⭐",
            "Tu sonrisa es el sol que ilumina mi universo ☀️",
            "Amor, eres mi refugio en cualquier tormenta 🏠",
            "Cada beso tuyo es una promesa de eternidad 💋",
            "Eres el sueño que nunca quiero despertar 🌙",
            "Mi amor por ti crece como las flores en primavera 🌸",
            "Contigo encontré mi hogar, mi paz, mi todo 🏡",
            "Eres la razón por la que creo en los cuentos de hadas ✨",
            "Tu amor es la llave que abrió mi corazón 🔑",
            "Eres mi persona favorita en todo el mundo 🌍",
            "Cada momento contigo es un regalo precioso 🎁",
            "Tu risa es la música más hermosa que conozco 🎵",
            "Eres mi complemento perfecto, mi alma gemela 👫",
            "Con tu amor, hasta lo imposible se vuelve posible 🦋",
            "Eres mi inspiración, mi musa, mi todo 🎨",
            "Tu amor me hace sentir invencible 💪",
            "💍 CONTIGO ME QUIERO CASAR 💍"
        ];

        const heartColors = ['red', 'pink', 'purple', 'blue', 'gold', 'green', 'orange', 'cyan'];
        const heartEmojis = ['💖', '💕', '💗', '💙', '💛', '💚', '🧡', '💎'];
        
        let clickCount = 0;
        let currentColorIndex = 0;
        let currentPhraseIndex = 0;

        function clickHeart() {
            clickCount++;
            document.getElementById('clickCount').textContent = clickCount;
            
            const heart = document.getElementById('heart');
            const phrase = document.getElementById('lovePhrase');
            
            // Efecto de clic
            heart.classList.add('clicked');
            setTimeout(() => {
                heart.classList.remove('clicked');
            }, 600);
            
            // Cambiar color automáticamente
            currentColorIndex = (currentColorIndex + 1) % heartColors.length;
            const newColor = heartColors[currentColorIndex];
            
            heart.className = `heart ${newColor}`;
            heart.textContent = heartEmojis[currentColorIndex];
            
            // Actualizar indicador de color
            updateColorIndicator(newColor);
            
            // Mostrar nueva frase
            currentPhraseIndex = (currentPhraseIndex + 1) % lovePhrases.length;
            
            phrase.classList.remove('show');
            
            setTimeout(() => {
                phrase.textContent = lovePhrases[currentPhraseIndex];
                phrase.classList.add('show');
            }, 300);
            
            // Crear partículas
            createParticles();
            
            // Crear estrellas
            createSparkles();
        }

        function changeHeartColor(color) {
            const heart = document.getElementById('heart');
            const colorIndex = heartColors.indexOf(color);
            
            currentColorIndex = colorIndex;
            heart.className = `heart ${color}`;
            heart.textContent = heartEmojis[colorIndex];
            
            updateColorIndicator(color);
            
            // Efecto visual
            heart.style.transform = 'scale(1.2)';
            setTimeout(() => {
                heart.style.transform = 'scale(1)';
            }, 200);
        }

        function updateColorIndicator(activeColor) {
            const dots = document.querySelectorAll('.color-dot');
            dots.forEach(dot => {
                dot.classList.remove('active');
                if (dot.classList.contains(activeColor)) {
                    dot.classList.add('active');
                }
            });
        }

        function createParticles() {
            const container = document.getElementById('particles');
            const colors = ['💕', '💖', '💗', '✨', '🌟', '💫', '💝', '💘'];
            
            for (let i = 0; i < 8; i++) {
                const particle = document.createElement('div');
                particle.className = 'particle';
                particle.textContent = colors[Math.floor(Math.random() * colors.length)];
                
                const angle = (i / 8) * 2 * Math.PI;
                const distance = 100 + Math.random() * 50;
                const x = Math.cos(angle) * distance;
                const y = Math.sin(angle) * distance;
                
                particle.style.setProperty('--x', x + 'px');
                particle.style.setProperty('--y', y + 'px');
                
                container.appendChild(particle);
                
                setTimeout(() => {
                    if (container.contains(particle)) {
                        container.removeChild(particle);
                    }
                }, 2000);
            }
        }

        function createSparkles() {
            const container = document.getElementById('sparkles');
            
            for (let i = 0; i < 5; i++) {
                setTimeout(() => {
                    const sparkle = document.createElement('div');
                    sparkle.className = 'sparkle';
                    sparkle.textContent = ['✨', '⭐', '🌟', '💫'][Math.floor(Math.random() * 4)];
                    sparkle.style.left = Math.random() * 100 + '%';
                    sparkle.style.top = Math.random() * 100 + '%';
                    sparkle.style.animationDelay = Math.random() * 2 + 's';
                    
                    container.appendChild(sparkle);
                    
                    setTimeout(() => {
                        if (container.contains(sparkle)) {
                            container.removeChild(sparkle);
                        }
                    }, 2000);
                }, i * 100);
            }
        }

        // Mostrar la primera frase al cargar
        setTimeout(() => {
            document.getElementById('lovePhrase').classList.add('show');
        }, 1000);

        // Crear estrellas de fondo ocasionalmente
        setInterval(() => {
            if (Math.random() > 0.7) {
                createSparkles();
            }
        }, 3000);

        // Animación suave del corazón
        setInterval(() => {
            const heart = document.getElementById('heart');
            if (!heart.classList.contains('clicked')) {
                heart.style.transform = 'scale(' + (1 + Math.sin(Date.now() / 1000) * 0.05) + ')';
            }
        }, 50);
    </script>
</body>
</html>
