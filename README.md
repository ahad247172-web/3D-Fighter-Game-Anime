<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Anime Fighter Arena 3D - Character Select</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Bangers&family=Orbitron:wght@400;700;900&display=swap');
        
        body {
            margin: 0;
            overflow: hidden;
            background: #000;
            font-family: 'Orbitron', sans-serif;
        }
        
        .anime-font {
            font-family: 'Bangers', cursive;
            letter-spacing: 2px;
        }
        
        .arcade-text {
            font-family: 'Orbitron', sans-serif;
            text-shadow: 0 0 20px currentColor;
        }
        
        /* Character Select Styles */
        .char-card {
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            transform-style: preserve-3d;
        }
        
        .char-card:hover {
            transform: translateY(-10px) scale(1.05);
            box-shadow: 0 0 30px currentColor;
        }
        
        .char-card.selected {
            transform: translateY(-20px) scale(1.1);
            box-shadow: 0 0 50px currentColor;
            border-color: #ffd700;
        }
        
        .char-card.locked {
            filter: grayscale(100%);
            opacity: 0.5;
        }
        
        /* Health Bars */
        .health-bar-container {
            background: linear-gradient(180deg, #1a1a1a 0%, #0d0d0d 100%);
            border: 3px solid #333;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.8);
        }
        
        .health-fill {
            transition: width 0.2s cubic-bezier(0.4, 0, 0.2, 1);
            box-shadow: 0 0 10px currentColor;
        }
        
        .special-bar {
            background: linear-gradient(90deg, #ffd700 0%, #ffed4e 50%, #ffd700 100%);
            box-shadow: 0 0 15px #ffd700;
        }
        
        /* Effects */
        .combo-text {
            animation: comboPop 0.5s ease-out;
            text-shadow: 0 0 30px currentColor;
        }
        
        @keyframes comboPop {
            0% { transform: scale(0.5) rotate(-10deg); opacity: 0; }
            50% { transform: scale(1.2) rotate(5deg); }
            100% { transform: scale(1) rotate(0deg); opacity: 1; }
        }
        
        .domain-expansion {
            animation: domainPulse 2s ease-in-out infinite;
        }
        
        @keyframes domainPulse {
            0%, 100% { filter: hue-rotate(0deg) brightness(1); }
            50% { filter: hue-rotate(30deg) brightness(1.3); }
        }
        
        /* Screen transitions */
        .screen {
            position: absolute;
            inset: 0;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.5s;
        }
        
        .screen.active {
            opacity: 1;
            pointer-events: all;
        }
        
        /* Particle effects */
        .particle {
            position: absolute;
            pointer-events: none;
            animation: float 3s ease-in-out infinite;
        }
        
        @keyframes float {
            0%, 100% { transform: translateY(0) rotate(0deg); }
            50% { transform: translateY(-20px) rotate(180deg); }
        }
        
        .control-key {
            background: linear-gradient(180deg, #444 0%, #222 100%);
            border: 2px solid #666;
            box-shadow: 0 4px 0 #111;
            transition: all 0.1s;
        }
        
        .control-key:active {
            transform: translateY(4px);
            box-shadow: 0 0 0 #111;
        }
        
        /* Cursed energy aura */
        .aura {
            position: absolute;
            border-radius: 50%;
            filter: blur(20px);
            animation: auraPulse 2s ease-in-out infinite;
        }
        
        @keyframes auraPulse {
            0%, 100% { transform: scale(1); opacity: 0.6; }
            50% { transform: scale(1.2); opacity: 0.8; }
        }
    </style>
</head>
<body>
    <div id="game-container" class="relative w-full h-screen">
        
        <!-- START SCREEN -->
        <div id="start-screen" class="screen active bg-gradient-to-b from-purple-900 via-black to-black flex flex-col items-center justify-center z-50">
            <div class="absolute inset-0 opacity-20" style="background-image: radial-gradient(circle at 50% 50%, #4c1d95 0%, transparent 70%);"></div>
            
            <h1 class="anime-font text-7xl md:text-9xl text-transparent bg-clip-text bg-gradient-to-r from-purple-400 via-pink-500 to-red-500 mb-4 animate-pulse">
                JUJUTSU ARENA
            </h1>
            <h2 class="arcade-text text-2xl md:text-4xl text-cyan-400 mb-12 tracking-widest">3D ANIME FIGHTER</h2>
            
            <div class="flex gap-8 text-white mb-12 text-center">
                <div class="bg-black/50 p-6 rounded-lg border border-purple-500/30">
                    <h3 class="text-red-400 font-bold mb-2">PLAYER 1</h3>
                    <p class="text-sm">WASD Move | F Punch | G Kick | R Special</p>
                </div>
                <div class="bg-black/50 p-6 rounded-lg border border-blue-500/30">
                    <h3 class="text-blue-400 font-bold mb-2">PLAYER 2</h3>
                    <p class="text-sm">Arrows Move | K Punch | L Kick | P Special</p>
                </div>
            </div>
            
            <button onclick="showCharSelect()" class="anime-font text-4xl px-16 py-6 bg-gradient-to-r from-purple-600 to-pink-600 text-white rounded-full hover:from-purple-500 hover:to-pink-500 transform hover:scale-110 transition-all shadow-2xl border-4 border-yellow-400 animate-bounce">
                START GAME
            </button>
            
            <div class="mt-8 text-gray-400 text-sm">Press ENTER to Start</div>
        </div>

        <!-- CHARACTER SELECT SCREEN -->
        <div id="char-select" class="screen bg-gradient-to-b from-gray-900 via-purple-900 to-black overflow-y-auto">
            <div class="absolute inset-0 opacity-30">
                <div class="absolute top-0 left-0 w-full h-full bg-[url('data:image/svg+xml,%3Csvg width=\'60\' height=\'60\' viewBox=\'0 0 60 60\' xmlns=\'http://www.w3.org/2000/svg\'%3E%3Cg fill=\'none\' fill-rule=\'evenodd\'%3E%3Cg fill=\'%239C92AC\' fill-opacity=\'0.05\'%3E%3Cpath d=\'M36 34v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4h-4v2h4v4h2V6h4V4h-4zM6 34v-4H4v4H0v2h4v4h2v-4h4v-2H6zM6 4V0H4v4H0v2h4v4h2V6h4V4H6z\'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E')]"></div>
            </div>
            
            <div class="relative z-10 container mx-auto px-4 py-8">
                <h1 class="anime-font text-6xl text-center text-transparent bg-clip-text bg-gradient-to-r from-yellow-400 to-orange-500 mb-8">
                    SELECT FIGHTER
                </h1>
                
                <!-- Selection indicators -->
                <div class="flex justify-center gap-32 mb-8">
                    <div id="p1-indicator" class="text-2xl font-bold text-red-500 border-2 border-red-500 px-6 py-2 rounded bg-red-500/20">
                        PLAYER 1: <span id="p1-selected-name">GOJO</span>
                    </div>
                    <div id="p2-indicator" class="text-2xl font-bold text-blue-500 border-2 border-blue-500 px-6 py-2 rounded bg-blue-500/20">
                        PLAYER 2: <span id="p2-selected-name">SUKUNA</span>
                    </div>
                </div>
                
                <!-- Character Grid -->
                <div class="grid grid-cols-2 md:grid-cols-4 lg:grid-cols-6 gap-4 max-w-7xl mx-auto" id="char-grid">
                    <!-- Characters injected by JS -->
                </div>
                
                <div class="text-center mt-12">
                    <button onclick="startFight()" class="anime-font text-3xl px-12 py-4 bg-gradient-to-r from-green-500 to-emerald-600 text-white rounded-lg hover:from-green-400 hover:to-emerald-500 transform hover:scale-105 transition-all shadow-lg disabled:opacity-50 disabled:cursor-not-allowed" id="fight-btn">
                        FIGHT!
                    </button>
                    <p class="text-gray-400 mt-4 text-sm">P1: Use WASD + F to select | P2: Use Arrows + K to select</p>
                </div>
            </div>
        </div>

        <!-- GAME SCREEN -->
        <div id="game-screen" class="screen">
            <canvas id="game-canvas" class="block w-full h-full"></canvas>
            
            <!-- UI Overlay -->
            <div id="game-ui" class="absolute inset-0 pointer-events-none hidden">
                <!-- Health Bars -->
                <div class="absolute top-4 left-4 right-4 flex justify-between items-start">
                    <!-- P1 -->
                    <div class="w-5/12">
                        <div class="flex items-center mb-2">
                            <span class="text-red-500 font-bold text-2xl mr-2 anime-font">P1</span>
                            <span id="p1-char-name" class="text-white text-lg anime-font">GOJO</span>
                        </div>
                        <div class="health-bar-container h-10 rounded-lg overflow-hidden relative skew-x-12 border-2 border-red-500/50">
                            <div id="p1-health" class="health-fill h-full w-full bg-gradient-to-r from-red-600 to-red-400 origin-left"></div>
                            <div class="absolute inset-0 bg-gradient-to-b from-transparent via-white to-transparent opacity-20"></div>
                        </div>
                        <div class="mt-2 h-3 bg-gray-800 rounded-full overflow-hidden border border-yellow-600/50">
                            <div id="p1-special" class="special-bar h-full w-0 transition-all duration-300"></div>
                        </div>
                        <div class="text-yellow-400 text-xs mt-1">CURSED ENERGY</div>
                    </div>
                    
                    <!-- Timer -->
                    <div class="flex flex-col items-center">
                        <div class="text-7xl font-black text-yellow-400 drop-shadow-lg anime-font" id="timer">99</div>
                        <div class="text-white text-sm mt-2 bg-black/50 px-4 py-1 rounded">ROUND <span id="round">1</span></div>
                    </div>
                    
                    <!-- P2 -->
                    <div class="w-5/12 text-right">
                        <div class="flex items-center justify-end mb-2">
                            <span id="p2-char-name" class="text-white text-lg anime-font mr-2">SUKUNA</span>
                            <span class="text-blue-500 font-bold text-2xl anime-font">P2</span>
                        </div>
                        <div class="health-bar-container h-10 rounded-lg overflow-hidden relative skew-x-12 border-2 border-blue-500/50">
                            <div id="p2-health" class="health-fill h-full w-full bg-gradient-to-r from-blue-600 to-blue-400 origin-right" style="transform: scaleX(-1);"></div>
                            <div class="absolute inset-0 bg-gradient-to-b from-transparent via-white to-transparent opacity-20"></div>
                        </div>
                        <div class="mt-2 h-3 bg-gray-800 rounded-full overflow-hidden border border-yellow-600/50">
                            <div id="p2-special" class="special-bar h-full w-0 transition-all duration-300 ml-auto"></div>
                        </div>
                        <div class="text-yellow-400 text-xs mt-1">CURSED ENERGY</div>
                    </div>
                </div>
                
                <!-- Combo Counters -->
                <div id="p1-combo" class="absolute left-1/4 top-32 text-7xl font-black text-red-500 opacity-0 combo-text anime-font">0 HIT</div>
                <div id="p2-combo" class="absolute right-1/4 top-32 text-7xl font-black text-blue-500 opacity-0 combo-text anime-font">0 HIT</div>
                
                <!-- Win Display -->
                <div id="win-display" class="absolute inset-0 flex items-center justify-center hidden bg-black/80">
                    <div class="text-center">
                        <div id="winner-text" class="text-9xl font-black anime-font text-transparent bg-clip-text bg-gradient-to-r from-yellow-400 to-red-600 mb-4 animate-pulse">
                            P1 WINS!
                        </div>
                        <div class="text-2xl text-white mb-8" id="win-reason">K.O.</div>
                        <button onclick="backToSelect()" class="anime-font text-2xl px-8 py-3 bg-purple-600 text-white rounded hover:bg-purple-500 transition pointer-events-auto">
                            REMATCH
                        </button>
                    </div>
                </div>
                
                <!-- Pause Menu -->
                <div id="pause-menu" class="absolute inset-0 flex items-center justify-center bg-black/90 hidden pointer-events-auto">
                    <div class="text-center">
                        <h2 class="anime-font text-6xl text-white mb-8">PAUSED</h2>
                        <button onclick="togglePause()" class="block w-full mb-4 px-8 py-3 bg-white text-black font-bold rounded hover:bg-gray-200">RESUME</button>
                        <button onclick="backToSelect()" class="block w-full px-8 py-3 bg-red-600 text-white rounded hover:bg-red-500">QUIT</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script>
        // Character Database
        const characters = [
            {
                id: 'gojo',
                name: 'GOJO',
                color: 0xffffff,
                eyeColor: 0x00ffff,
                auraColor: 0x00ffff,
                style: 'speed',
                stats: { power: 7, speed: 10, defense: 6, range: 9 },
                desc: 'Limitless & Six Eyes',
                moves: ['Lapse Blue', 'Reversal Red', 'Hollow Purple', 'Unlimited Void']
            },
            {
                id: 'sukuna',
                name: 'SUKUNA',
                color: 0x8b0000,
                eyeColor: 0xff0000,
                auraColor: 0xff0000,
                style: 'power',
                stats: { power: 10, speed: 7, defense: 8, range: 6 },
                desc: 'King of Curses',
                moves: ['Dismantle', 'Cleave', 'Fire Arrow', 'Malevolent Shrine']
            },
            {
                id: 'yuji',
                name: 'YUJI',
                color: 0xff6600,
                eyeColor: 0xffaa00,
                auraColor: 0xff6600,
                style: 'balanced',
                stats: { power: 8, speed: 8, defense: 7, range: 5 },
                desc: 'Black Flash User',
                moves: ['Divergent Fist', 'Black Flash', 'Manji Kick', 'Soul Damage']
            },
            {
                id: 'megumi',
                name: 'MEGUMI',
                color: 0x1a1a2e,
                eyeColor: 0x4a0080,
                auraColor: 0x4a0080,
                style: 'range',
                stats: { power: 6, speed: 7, defense: 7, range: 10 },
                desc: 'Ten Shadows Technique',
                moves: ['Divine Dog', 'Nue', 'Max Elephant', 'Mahoraga']
            },
            {
                id: 'nobara',
                name: 'NOBARA',
                color: 0xff1493,
                eyeColor: 0xff69b4,
                auraColor: 0xff1493,
                style: 'trick',
                stats: { power: 7, speed: 7, defense: 5, range: 8 },
                desc: 'Straw Doll Technique',
                moves: ['Hairpin', 'Resonance', 'Nail Shot', 'Ultimate Resonance']
            },
            {
                id: 'todo',
                name: 'TODO',
                color: 0x8b4513,
                eyeColor: 0xffffff,
                auraColor: 0x8b4513,
                style: 'power',
                stats: { power: 9, speed: 6, defense: 9, range: 4 },
                desc: 'Boogie Woogie',
                moves: ['Clap', 'Boogie Woogie', 'Ultra Shield', 'Brotherhood']
            },
            {
                id: 'mahito',
                name: 'MAHITO',
                color: 0x2d004d,
                eyeColor: 0x9400d3,
                auraColor: 0x9400d3,
                style: 'trick',
                stats: { power: 7, speed: 8, defense: 5, range: 7 },
                desc: 'Idle Transfiguration',
                moves: ['Body Repel', 'Soul Multiplicity', 'Polymorphic', 'Instant Spirit']
            },
            {
                id: 'jogo',
                name: 'JOGO',
                color: 0xff4500,
                eyeColor: 0xffff00,
                auraColor: 0xff4500,
                style: 'range',
                stats: { power: 9, speed: 7, defense: 6, range: 9 },
                desc: 'Volcano Curse',
                moves: ['Ember Insects', 'Volcano', 'Maximum Meteor', 'Domain']
            },
            {
                id: 'nanami',
                name: 'NANAMI',
                color: 0xffd700,
                eyeColor: 0xffffff,
                auraColor: 0xffd700,
                style: 'balanced',
                stats: { power: 7, speed: 7, defense: 8, range: 6 },
                desc: 'Ratio Technique',
                moves: ['Ratio', 'Collapse', 'Overtime', 'Domain']
            },
            {
                id: 'toji',
                name: 'TOJI',
                color: 0x2f4f4f,
                eyeColor: 0x00ff00,
                auraColor: 0x00ff00,
                style: 'speed',
                stats: { power: 9, speed: 10, defense: 4, range: 5 },
                desc: 'Sorcerer Killer',
                moves: ['Chain', 'Playful Cloud', 'Inverted Spear', 'Heavenly Restriction']
            },
            {
                id: 'geto',
                name: 'GETO',
                color: 0x800080,
                eyeColor: 0xffd700,
                auraColor: 0x800080,
                style: 'range',
                stats: { power: 8, speed: 6, defense: 7, range: 9 },
                desc: 'Cursed Spirit Manip',
                moves: ['Summon', 'Absorb', 'Maximum Uzumaki', 'Domain']
            },
            {
                id: 'yuta',
                name: 'YUTA',
                color: 0xffffff,
                eyeColor: 0x000000,
                auraColor: 0x800080,
                style: 'balanced',
                stats: { power: 9, speed: 8, defense: 8, range: 8 },
                desc: 'Special Grade',
                moves: ['Katana', 'Rika', 'Cursed Speech', 'Domain']
            }
        ];

        // Game State
        let gameState = {
            screen: 'start',
            p1Char: characters[0],
            p2Char: characters[1],
            p1Index: 0,
            p2Index: 1,
            round: 1,
            paused: false
        };

        let scene, camera, renderer;
        let p1, p2;
        let clock = new THREE.Clock();
        let gameInterval;
        let villageBackground;
        
        // Input State
        const p1Input = { up: false, down: false, left: false, right: false, punch: false, kick: false, special: false };
        const p2Input = { up: false, down: false, left: false, right: false, punch: false, kick: false, special: false };

        // Initialize Character Select
        function initCharSelect() {
            const grid = document.getElementById('char-grid');
            grid.innerHTML = '';
            
            characters.forEach((char, index) => {
                const card = document.createElement('div');
                card.className = `char-card bg-gray-800 rounded-lg p-4 cursor-pointer border-2 border-gray-600 relative overflow-hidden ${index === 0 ? 'selected' : ''}`;
                card.dataset.index = index;
                card.dataset.id = char.id;
                
                // Generate preview color
                const colorHex = '#' + char.color.toString(16).padStart(6, '0');
                const auraHex = '#' + char.auraColor.toString(16).padStart(6, '0');
                
                card.innerHTML = `
                    <div class="aura absolute inset-0 opacity-30" style="background: radial-gradient(circle, ${auraHex} 0%, transparent 70%);"></div>
                    <div class="relative z-10">
                        <div class="w-full h-32 rounded mb-3 flex items-center justify-center" style="background: linear-gradient(135deg, ${colorHex}22, ${auraHex}44); border: 2px solid ${colorHex};">
                            <div class="text-6xl font-black anime-font" style="color: ${colorHex}; text-shadow: 0 0 20px ${auraHex};">
                                ${char.name[0]}
                            </div>
                        </div>
                        <h3 class="text-xl font-bold text-white anime-font mb-1">${char.name}</h3>
                        <p class="text-xs text-gray-400 mb-2">${char.desc}</p>
                        <div class="grid grid-cols-2 gap-1 text-xs">
                            <div class="text-red-400">PWR: ${'★'.repeat(char.stats.power/2)}</div>
                            <div class="text-blue-400">SPD: ${'★'.repeat(char.stats.speed/2)}</div>
                            <div class="text-green-400">DEF: ${'★'.repeat(char.stats.defense/2)}</div>
                            <div class="text-purple-400">RNG: ${'★'.repeat(char.stats.range/2)}</div>
                        </div>
                    </div>
                `;
                
                card.onclick = () => selectCharacter(1, index);
                grid.appendChild(card);
            });
            
            updateSelectionDisplay();
        }

        function selectCharacter(player, index) {
            if (player === 1) {
                gameState.p1Index = index;
                gameState.p1Char = characters[index];
            } else {
                gameState.p2Index = index;
                gameState.p2Char = characters[index];
            }
            updateSelectionDisplay();
        }

        function updateSelectionDisplay() {
            document.querySelectorAll('.char-card').forEach((card, idx) => {
                card.classList.remove('selected');
                if (idx === gameState.p1Index) card.classList.add('selected');
            });
            
            document.getElementById('p1-selected-name').textContent = gameState.p1Char.name;
            document.getElementById('p2-selected-name').textContent = gameState.p2Char.name;
            
            // Update indicator borders
            document.getElementById('p1-indicator').style.borderColor = '#' + gameState.p1Char.auraColor.toString(16).padStart(6, '0');
            document.getElementById('p2-indicator').style.borderColor = '#' + gameState.p2Char.auraColor.toString(16).padStart(6, '0');
        }

        function showCharSelect() {
            document.getElementById('start-screen').classList.remove('active');
            document.getElementById('char-select').classList.add('active');
            initCharSelect();
        }

        function startFight() {
            document.getElementById('char-select').classList.remove('active');
            document.getElementById('game-screen').classList.add('active');
            document.getElementById('game-ui').classList.remove('hidden');
            initGame();
        }

        function backToSelect() {
            gameState.paused = false;
            document.getElementById('game-screen').classList.remove('active');
            document.getElementById('char-select').classList.add('active');
            document.getElementById('win-display').classList.add('hidden');
            document.getElementById('pause-menu').classList.add('hidden');
        }

        function togglePause() {
            gameState.paused = !gameState.paused;
            document.getElementById('pause-menu').classList.toggle('hidden');
        }

        // Enhanced Fighter Class
        class Fighter {
            constructor(charData, x, isPlayer1) {
                this.data = charData;
                this.isPlayer1 = isPlayer1;
                this.mesh = new THREE.Group();
                this.x = x;
                this.health = 100;
                this.special = 0;
                this.combo = 0;
                this.isAttacking = false;
                this.isBlocking = false;
                this.isHit = false;
                this.isJumping = false;
                this.velocity = new THREE.Vector3();
                this.facing = isPlayer1 ? 1 : -1;
                this.animTime = 0;
                this.currentAnim = 'idle';
                
                this.createCharacter();
                this.mesh.position.set(x, 0, 0);
                this.createAura();
            }

            createCharacter() {
                const color = this.data.color;
                const eyeColor = this.data.eyeColor;
                const auraColor = this.data.auraColor;
                
                const bodyMat = new THREE.MeshPhongMaterial({ 
                    color: color,
                    shininess: 100,
                    specular: 0x444444
                });
                
                const jointMat = new THREE.MeshPhongMaterial({ color: 0x222222 });
                const eyeMat = new THREE.MeshBasicMaterial({ color: eyeColor });
                const glowMat = new THREE.MeshBasicMaterial({ 
                    color: auraColor,
                    transparent: true,
                    opacity: 0.8
                });

                // Unique body proportions based on character
                const height = this.data.id === 'gojo' ? 1.9 : (this.data.id === 'sukuna' ? 1.7 : 1.8);
                
                // Torso
                this.torso = new THREE.Mesh(new THREE.BoxGeometry(0.5, 0.7, 0.3), bodyMat);
                this.torso.position.y = height * 0.6;
                this.torso.castShadow = true;
                this.mesh.add(this.torso);

                // Character-specific chest marking
                if (this.data.id === 'sukuna') {
                    const marking = new THREE.Mesh(
                        new THREE.PlaneGeometry(0.3, 0.4),
                        new THREE.MeshBasicMaterial({ color: 0x000000 })
                    );
                    marking.position.set(0, 0, 0.16);
                    this.torso.add(marking);
                }

                // Head
                this.head = new THREE.Mesh(new THREE.BoxGeometry(0.3, 0.35, 0.3), bodyMat);
                this.head.position.y = 0.55;
                this.torso.add(this.head);

                // Eyes (glowing)
                const leftEye = new THREE.Mesh(new THREE.PlaneGeometry(0.08, 0.08), eyeMat);
                leftEye.position.set(-0.08, 0.05, 0.16);
                this.head.add(leftEye);
                
                const rightEye = new THREE.Mesh(new THREE.PlaneGeometry(0.08, 0.08), eyeMat);
                rightEye.position.set(0.08, 0.05, 0.16);
                this.head.add(rightEye);

                // Hair/Headpiece
                let hairStyle;
                switch(this.data.id) {
                    case 'gojo':
                        hairStyle = new THREE.Mesh(new THREE.BoxGeometry(0.4, 0.2, 0.4), new THREE.MeshPhongMaterial({ color: 0xffffff }));
                        hairStyle.position.y = 0.25;
                        break;
                    case 'sukuna':
                        hairStyle = new THREE.Mesh(new THREE.BoxGeometry(0.35, 0.15, 0.35), new THREE.MeshPhongMaterial({ color: 0xff0000 }));
                        hairStyle.position.y = 0.2;
                        // Extra eyes on forehead
                        const extraEye = new THREE.Mesh(new THREE.PlaneGeometry(0.06, 0.06), eyeMat);
                        extraEye.position.set(0, 0.15, 0.16);
                        this.head.add(extraEye);
                        break;
                    default:
                        hairStyle = new THREE.Mesh(new THREE.BoxGeometry(0.35, 0.15, 0.35), new THREE.MeshPhongMaterial({ color: 0x111111 }));
                        hairStyle.position.y = 0.2;
                }
                this.head.add(hairStyle);

                // Blindfold for Gojo
                if (this.data.id === 'gojo') {
                    const blindfold = new THREE.Mesh(
                        new THREE.BoxGeometry(0.32, 0.1, 0.32),
                        new THREE.MeshPhongMaterial({ color: 0x000000 })
                    );
                    blindfold.position.y = 0.05;
                    this.head.add(blindfold);
                }

                // Shoulders
                this.leftShoulder = new THREE.Mesh(new THREE.SphereGeometry(0.12), jointMat);
                this.leftShoulder.position.set(-0.35, 0.25, 0);
                this.torso.add(this.leftShoulder);

                this.rightShoulder = new THREE.Mesh(new THREE.SphereGeometry(0.12), jointMat);
                this.rightShoulder.position.set(0.35, 0.25, 0);
                this.torso.add(this.rightShoulder);

                // Arms
                this.leftArm = new THREE.Mesh(new THREE.BoxGeometry(0.15, 0.6, 0.15), bodyMat);
                this.leftArm.position.y = -0.3;
                this.leftShoulder.add(this.leftArm);

                this.rightArm = new THREE.Mesh(new THREE.BoxGeometry(0.15, 0.6, 0.15), bodyMat);
                this.rightArm.position.y = -0.3;
                this.rightShoulder.add(this.rightArm);

                // Hands
                const handGeo = new THREE.BoxGeometry(0.2, 0.2, 0.2);
                this.leftHand = new THREE.Mesh(handGeo, jointMat);
                this.leftHand.position.y = -0.4;
                this.leftArm.add(this.leftHand);

                this.rightHand = new THREE.Mesh(handGeo, jointMat);
                this.rightHand.position.y = -0.4;
                this.rightArm.add(this.rightHand);

                // Weapon/Effect for specific characters
                if (this.data.id === 'megumi') {
                    const shadow = new THREE.Mesh(
                        new THREE.CircleGeometry(0.3, 32),
                        new THREE.MeshBasicMaterial({ color: 0x000000, transparent: true, opacity: 0.5 })
                    );
                    shadow.rotation.x = -Math.PI/2;
                    shadow.position.y = -0.5;
                    this.mesh.add(shadow);
                }

                // Hips
                this.hips = new THREE.Mesh(new THREE.BoxGeometry(0.4, 0.25, 0.25), bodyMat);
                this.hips.position.y = height * 0.3;
                this.mesh.add(this.hips);

                // Legs
                this.leftLeg = new THREE.Mesh(new THREE.BoxGeometry(0.18, 0.7, 0.18), bodyMat);
                this.leftLeg.position.set(-0.12, -0.45, 0);
                this.hips.add(this.leftLeg);

                this.rightLeg = new THREE.Mesh(new THREE.BoxGeometry(0.18, 0.7, 0.18), bodyMat);
                this.rightLeg.position.set(0.12, -0.45, 0);
                this.hips.add(this.rightLeg);

                // Feet
                const footGeo = new THREE.BoxGeometry(0.2, 0.12, 0.35);
                this.leftFoot = new THREE.Mesh(footGeo, jointMat);
                this.leftFoot.position.set(0, -0.4, 0.08);
                this.leftLeg.add(this.leftFoot);

                this.rightFoot = new THREE.Mesh(footGeo, jointMat);
                this.rightFoot.position.set(0, -0.4, 0.08);
                this.rightLeg.add(this.rightFoot);

                // Shadow
                const shadowGeo = new THREE.CircleGeometry(0.5, 32);
                const shadowMat = new THREE.MeshBasicMaterial({ 
                    color: 0x000000, 
                    transparent: true, 
                    opacity: 0.4 
                });
                this.shadow = new THREE.Mesh(shadowGeo, shadowMat);
                this.shadow.rotation.x = -Math.PI / 2;
                this.shadow.position.y = 0.01;
                this.mesh.add(this.shadow);
            }

            createAura() {
                // Cursed energy aura
                const auraGeo = new THREE.SphereGeometry(1.5, 32, 32);
                const auraMat = new THREE.MeshBasicMaterial({
                    color: this.data.auraColor,
                    transparent: true,
                    opacity: 0.1,
                    wireframe: true
                });
                this.aura = new THREE.Mesh(auraGeo, auraMat);
                this.aura.position.y = 1;
                this.mesh.add(this.aura);
                
                // Inner glow
                const glowGeo = new THREE.SphereGeometry(1, 32, 32);
                const glowMat = new THREE.MeshBasicMaterial({
                    color: this.data.auraColor,
                    transparent: true,
                    opacity: 0.05
                });
                this.glow = new THREE.Mesh(glowGeo, glowMat);
                this.glow.position.y = 1;
                this.mesh.add(this.glow);
            }

            update(delta, input, opponent) {
                if (this.health <= 0) return;

                this.animTime += delta * 5;
                
                // Update aura
                this.aura.rotation.y += delta;
                this.aura.scale.setScalar(1 + Math.sin(this.animTime) * 0.1);
                
                // Domain Expansion effect when special is full
                if (this.special >= 100) {
                    this.aura.material.opacity = 0.2 + Math.sin(this.animTime * 2) * 0.1;
                }

                this.currentAnim = 'idle';
                
                // Movement with character-specific speed
                const speed = (this.data.stats.speed / 10) * 5;
                const moveX = (input.right ? 1 : 0) - (input.left ? 1 : 0);
                
                if (moveX !== 0) {
                    this.velocity.x = moveX * speed * this.facing;
                    this.currentAnim = 'walk';
                } else {
                    this.velocity.x *= 0.8;
                }

                // Jump
                if (input.up && this.mesh.position.y <= 0.1) {
                    this.velocity.y = 7 + (this.data.stats.speed / 10);
                    this.isJumping = true;
                }

                // Gravity
                this.velocity.y -= 25 * delta;
                
                // Apply movement
                this.mesh.position.x += this.velocity.x * delta;
                this.mesh.position.y += this.velocity.y * delta;
                
                if (this.mesh.position.y < 0) {
                    this.mesh.position.y = 0;
                    this.velocity.y = 0;
                    this.isJumping = false;
                }

                // Boundaries
                this.mesh.position.x = Math.max(-10, Math.min(10, this.mesh.position.x));

                // Face opponent
                if (opponent.mesh.position.x > this.mesh.position.x) {
                    this.facing = 1;
                    this.mesh.rotation.y = 0;
                } else {
                    this.facing = -1;
                    this.mesh.rotation.y = Math.PI;
                }

                // Blocking
                this.isBlocking = input.down && !this.isAttacking;
                if (this.isBlocking) this.currentAnim = 'block';

                // Attacks
                if (!this.isAttacking && !this.isHit) {
                    if (input.punch) this.attack('punch');
                    else if (input.kick) this.attack('kick');
                    else if (input.special && this.special >= 100) this.attack('special');
                }

                if (this.isHit) {
                    this.hitTimer -= delta;
                    if (this.hitTimer <= 0) {
                        this.isHit = false;
                        this.combo = 0;
                    }
                    this.currentAnim = 'hit';
                }

                if (this.isAttacking) {
                    this.attackTimer -= delta;
                    if (this.attackTimer <= 0) {
                        this.isAttacking = false;
                        this.checkHit(opponent);
                    }
                }

                // Build special
                if (this.special < 100) this.special += delta * (2 + this.data.stats.power/10);

                this.animate(delta);
            }

            attack(type) {
                this.isAttacking = true;
                this.attackTimer = type === 'special' ? 1.5 : 0.3;
                this.currentAnim = type;
                this.attackType = type;
                
                if (type === 'special') {
                    this.special = 0;
                    this.createDomainExpansion();
                } else {
                    this.createAttackEffect(type);
                }
            }

            createAttackEffect(type) {
                const color = '#' + this.data.auraColor.toString(16).padStart(6, '0');
                const geo = type === 'kick' ? 
                    new THREE.ConeGeometry(0.3, 0.8, 8) : 
                    new THREE.SphereGeometry(0.3, 16, 16);
                
                const mat = new THREE.MeshBasicMaterial({
                    color: this.data.auraColor,
                    transparent: true,
                    opacity: 0.6
                });
                
                const effect = new THREE.Mesh(geo, mat);
                effect.position.copy(this.rightHand.position);
                effect.position.x += this.mesh.position.x + (this.facing * 0.5);
                effect.position.y += this.mesh.position.y + this.torso.position.y - 0.3;
                
                if (type === 'kick') {
                    effect.rotation.z = this.facing * Math.PI/2;
                }
                
                scene.add(effect);
                
                let scale = 1;
                const animate = () => {
                    scale += 0.2;
                    effect.scale.setScalar(scale);
                    effect.material.opacity -= 0.05;
                    effect.position.x += this.facing * 0.1;
                    
                    if (effect.material.opacity > 0) {
                        requestAnimationFrame(animate);
                    } else {
                        scene.remove(effect);
                    }
                };
                animate();
            }

            createDomainExpansion() {
                // Massive domain effect
                const domainGeo = new THREE.SphereGeometry(5, 64, 64);
                const domainMat = new THREE.MeshBasicMaterial({
                    color: this.data.auraColor,
                    transparent: true,
                    opacity: 0,
                    side: THREE.BackSide,
                    wireframe: true
                });
                
                const domain = new THREE.Mesh(domainGeo, domainMat);
                domain.position.copy(this.mesh.position);
                domain.position.y = 2.5;
                scene.add(domain);
                
                let opacity = 0;
                let scale = 0.1;
                const expand = () => {
                    opacity += 0.02;
                    scale += 0.05;
                    domain.material.opacity = Math.min(0.3, opacity);
                    domain.scale.setScalar(scale);
                    domain.rotation.y += 0.02;
                    
                    if (scale < 2) {
                        requestAnimationFrame(expand);
                    } else {
                        setTimeout(() => {
                            const fade = () => {
                                domain.material.opacity -= 0.02;
                                if (domain.material.opacity > 0) {
                                    requestAnimationFrame(fade);
                                } else {
                                    scene.remove(domain);
                                }
                            };
                            fade();
                        }, 1000);
                    }
                };
                expand();
                
                cameraShake = 0.8;
            }

            checkHit(opponent) {
                const dist = Math.abs(this.mesh.position.x - opponent.mesh.position.x);
                const range = 1 + (this.data.stats.range / 10);
                
                if (dist < range && !opponent.isBlocking && opponent.health > 0) {
                    let damage = this.attackType === 'kick' ? 
                        10 + this.data.stats.power : 
                        (this.attackType === 'special' ? 
                            40 + this.data.stats.power : 
                            7 + this.data.stats.power/2);
                    
                    this.combo++;
                    if (this.combo > 1) {
                        damage *= (1 + this.combo * 0.15);
                        this.showCombo();
                    }

                    opponent.takeDamage(damage, this.attackType === 'special');
                    
                    if (this.attackType === 'special') {
                        cameraShake = 0.6;
                    }
                } else if (dist < range && opponent.isBlocking) {
                    opponent.velocity.x = this.facing * 3;
                    this.createBlockEffect(opponent.mesh.position);
                }
            }

            takeDamage(amount, isSpecial) {
                const actualDamage = this.isBlocking ? amount * 0.3 : amount;
                this.health = Math.max(0, this.health - actualDamage);
                this.isHit = true;
                this.hitTimer = 0.4;
                this.velocity.x = -this.facing * (isSpecial ? 8 : 4);
                this.velocity.y = isSpecial ? 4 : 2;
                
                this.createHitEffect(isSpecial);
                updateHealthBars();
                
                if (this.health <= 0) {
                    endRound(this.isPlayer1 ? 2 : 1);
                }
            }

            createHitEffect(isHeavy) {
                const particleCount = isHeavy ? 20 : 10;
                const color = isHeavy ? 0xff0000 : 0xffffff;
                
                for (let i = 0; i < particleCount; i++) {
                    const geo = new THREE.BoxGeometry(0.08, 0.08, 0.08);
                    const mat = new THREE.MeshBasicMaterial({ color: color });
                    const mesh = new THREE.Mesh(geo, mat);
                    
                    mesh.position.copy(this.torso.position);
                    mesh.position.x += this.mesh.position.x;
                    mesh.position.y += this.mesh.position.y;
                    mesh.userData.velocity = new THREE.Vector3(
                        (Math.random() - 0.5) * 8,
                        Math.random() * 6,
                        (Math.random() - 0.5) * 8
                    );
                    
                    scene.add(mesh);
                    
                    const animate = () => {
                        mesh.position.add(mesh.userData.velocity.clone().multiplyScalar(0.016));
                        mesh.userData.velocity.y -= 0.15;
                        mesh.rotation.x += 0.2;
                        mesh.rotation.y += 0.2;
                        mesh.scale.multiplyScalar(0.95);
                        
                        if (mesh.scale.x > 0.01) requestAnimationFrame(animate);
                        else scene.remove(mesh);
                    };
                    animate();
                }
            }

            createBlockEffect(pos) {
                const geo = new THREE.OctahedronGeometry(0.4);
                const mat = new THREE.MeshBasicMaterial({ 
                    color: 0xffffff,
                    transparent: true,
                    opacity: 0.8,
                    wireframe: true
                });
                const mesh = new THREE.Mesh(geo, mat);
                mesh.position.copy(pos);
                mesh.position.y += 1;
                scene.add(mesh);
                
                let scale = 1;
                const animate = () => {
                    scale += 0.1;
                    mesh.scale.setScalar(scale);
                    mesh.rotation.y += 0.1;
                    mesh.material.opacity -= 0.05;
                    
                    if (mesh.material.opacity > 0) requestAnimationFrame(animate);
                    else scene.remove(mesh);
                };
                animate();
            }

            showCombo() {
                const comboEl = document.getElementById(this.isPlayer1 ? 'p1-combo' : 'p2-combo');
                comboEl.textContent = this.combo + ' HIT' + (this.combo > 1 ? 'S' : '!');
                comboEl.style.opacity = '1';
                comboEl.classList.remove('combo-text');
                void comboEl.offsetWidth;
                comboEl.classList.add('combo-text');
                
                setTimeout(() => {
                    comboEl.style.opacity = '0';
                }, 1500);
            }

            animate(delta) {
                const t = this.animTime;
                const bob = Math.sin(t * 0.5) * 0.03;
                
                this.torso.position.y = (this.data.id === 'gojo' ? 1.14 : 1.08) + bob;
                
                switch(this.currentAnim) {
                    case 'idle':
                        this.leftArm.rotation.x = Math.sin(t * 0.5) * 0.1;
                        this.rightArm.rotation.x = Math.sin(t * 0.5 + Math.PI) * 0.1;
                        this.leftArm.rotation.z = 0.1;
                        this.rightArm.rotation.z = -0.1;
                        this.leftLeg.rotation.x = 0;
                        this.rightLeg.rotation.x = 0;
                        break;
                        
                    case 'walk':
                        const walkSpeed = 2 + (this.data.stats.speed / 5);
                        this.leftLeg.rotation.x = Math.sin(t * walkSpeed) * 0.6;
                        this.rightLeg.rotation.x = Math.sin(t * walkSpeed + Math.PI) * 0.6;
                        this.leftArm.rotation.x = Math.sin(t * walkSpeed + Math.PI) * 0.4;
                        this.rightArm.rotation.x = Math.sin(t * walkSpeed) * 0.4;
                        break;
                        
                    case 'punch':
                        const punchProgress = 1 - (this.attackTimer / 0.3);
                        this.rightArm.rotation.x = -Math.PI/2 * Math.sin(punchProgress * Math.PI);
                        this.rightShoulder.rotation.y = -0.8 * Math.sin(punchProgress * Math.PI);
                        this.torso.rotation.y = this.facing * 0.3 * Math.sin(punchProgress * Math.PI);
                        break;
                        
                    case 'kick':
                        const kickProgress = 1 - (this.attackTimer / 0.3);
                        this.rightLeg.rotation.x = -Math.PI/1.5 * Math.sin(kickProgress * Math.PI);
                        this.rightLeg.rotation.z = 0.2;
                        this.leftArm.rotation.x = -0.5;
                        this.hips.rotation.x = 0.2 * Math.sin(kickProgress * Math.PI);
                        break;
                        
                    case 'hit':
                        this.torso.rotation.z = this.facing * 0.4;
                        this.head.rotation.z = -this.facing * 0.3;
                        this.leftArm.rotation.x = -0.5;
                        this.rightArm.rotation.x = 0.5;
                        break;
                        
                    case 'block':
                        this.leftArm.rotation.x = -Math.PI/2.5;
                        this.rightArm.rotation.x = -Math.PI/2.5;
                        this.leftArm.rotation.z = 0.4;
                        this.rightArm.rotation.z = -0.4;
                        this.torso.rotation.y = this.facing * 0.2;
                        break;
                        
                    case 'special':
                        const specialProgress = 1 - (this.attackTimer / 1.5);
                        this.mesh.position.y = Math.sin(specialProgress * Math.PI) * 3;
                        this.torso.rotation.y = specialProgress * Math.PI * 6;
                        this.leftArm.rotation.x = -Math.PI * 0.8;
                        this.rightArm.rotation.x = -Math.PI * 0.8;
                        this.leftArm.rotation.z = Math.sin(specialProgress * 10) * 0.5;
                        this.rightArm.rotation.z = -Math.sin(specialProgress * 10) * 0.5;
                        
                        // Lightning effect during special
                        if (Math.random() > 0.7) {
                            this.createLightning();
                        }
                        break;
                }
                
                // Reset rotations
                if (!['hit', 'block', 'special'].includes(this.currentAnim)) {
                    this.torso.rotation.z *= 0.9;
                    this.torso.rotation.y *= 0.9;
                    this.head.rotation.z *= 0.9;
                    this.leftShoulder.rotation.y *= 0.9;
                    this.rightShoulder.rotation.y *= 0.9;
                    this.hips.rotation.x *= 0.9;
                    
                    if (this.currentAnim !== 'kick') {
                        this.leftLeg.rotation.z *= 0.9;
                        this.rightLeg.rotation.z *= 0.9;
                    }
                }
            }

            createLightning() {
                const material = new THREE.LineBasicMaterial({ color: this.data.auraColor });
                const points = [];
                let currentPoint = new THREE.Vector3(
                    this.mesh.position.x + (Math.random() - 0.5),
                    this.mesh.position.y + Math.random() * 2,
                    this.mesh.position.z + (Math.random() - 0.5)
                );
                points.push(currentPoint);
                
                for (let i = 0; i < 5; i++) {
                    currentPoint = currentPoint.clone().add(new THREE.Vector3(
                        (Math.random() - 0.5) * 2,
                        -Math.random() * 0.5,
                        (Math.random() - 0.5) * 2
                    ));
                    points.push(currentPoint);
                }
                
                const geometry = new THREE.BufferGeometry().setFromPoints(points);
                const line = new THREE.Line(geometry, material);
                scene.add(line);
                
                setTimeout(() => scene.remove(line), 100);
            }
        }

        // Village Background
        function createVillageBackground() {
            villageBackground = new THREE.Group();
            
            // Ground
            const groundGeo = new THREE.PlaneGeometry(100, 50);
            const groundMat = new THREE.MeshPhongMaterial({ 
                color: 0x2d5016,
                shininess: 10
            });
            const ground = new THREE.Mesh(groundGeo, groundMat);
            ground.rotation.x = -Math.PI / 2;
            ground.position.z = -10;
            ground.receiveShadow = true;
            villageBackground.add(ground);
            
            // Traditional Japanese buildings
            const buildingColors = [0x8b4513, 0x654321, 0x5d4037, 0x4e342e];
            
            for (let i = -5; i <= 5; i++) {
                if (i === 0) continue; // Clear center for fighting
                
                const height = 3 + Math.random() * 4;
                const width = 2 + Math.random() * 2;
                const depth = 2 + Math.random() * 2;
                
                // Main building
                const building = new THREE.Mesh(
                    new THREE.BoxGeometry(width, height, depth),
                    new THREE.MeshPhongMaterial({ color: buildingColors[Math.floor(Math.random() * buildingColors.length)] })
                );
                building.position.set(i * 6, height/2, -8 - Math.random() * 5);
                building.castShadow = true;
                villageBackground.add(building);
                
                // Roof (Japanese style)
                const roofGeo = new THREE.ConeGeometry(width * 0.8, 1.5, 4);
                const roofMat = new THREE.MeshPhongMaterial({ color: 0x2c1810 });
                const roof = new THREE.Mesh(roofGeo, roofMat);
                roof.position.set(i * 6, height + 0.75, -8 - Math.random() * 5);
                roof.rotation.y = Math.PI / 4;
                villageBackground.add(roof);
                
                // Lanterns
                if (Math.random() > 0.5) {
                    const lantern = new THREE.Mesh(
                        new THREE.SphereGeometry(0.3, 16, 16),
                        new THREE.MeshBasicMaterial({ color: 0xffaa00 })
                    );
                    lantern.position.set(i * 6 + width/2, height * 0.6, -8 + depth/2);
                    villageBackground.add(lantern);
                    
                    // Point light for lantern
                    const light = new THREE.PointLight(0xffaa00, 0.5, 5);
                    light.position.copy(lantern.position);
                    villageBackground.add(light);
                }
            }
            
            // Torii gate in background
            const toriiGroup = new THREE.Group();
            const redMat = new THREE.MeshPhongMaterial({ color: 0xcc0000 });
            const blackMat = new THREE.MeshPhongMaterial({ color: 0x000000 });
            
            // Pillars
            const pillar1 = new THREE.Mesh(new THREE.CylinderGeometry(0.3, 0.3, 6), redMat);
            pillar1.position.set(-2, 3, -15);
            toriiGroup.add(pillar1);
            
            const pillar2 = new THREE.Mesh(new THREE.CylinderGeometry(0.3, 0.3, 6), redMat);
            pillar2.position.set(2, 3, -15);
            toriiGroup.add(pillar2);
            
            // Crossbars
            const topBar = new THREE.Mesh(new THREE.BoxGeometry(5, 0.4, 0.4), redMat);
            topBar.position.set(0, 5.5, -15);
            toriiGroup.add(topBar);
            
            const lowerBar = new THREE.Mesh(new THREE.BoxGeometry(4, 0.3, 0.3), redMat);
            lowerBar.position.set(0, 4.5, -15);
            toriiGroup.add(lowerBar);
            
            // Roof
            const roof = new THREE.Mesh(new THREE.BoxGeometry(5.5, 0.2, 0.8), blackMat);
            roof.position.set(0, 5.8, -15);
            toriiGroup.add(roof);
            
            villageBackground.add(toriiGroup);
            
            // Cherry blossom particles
            const particleCount = 100;
            const particles = new THREE.BufferGeometry();
            const positions = new Float32Array(particleCount * 3);
            const velocities = [];
            
            for (let i = 0; i < particleCount; i++) {
                positions[i * 3] = (Math.random() - 0.5) * 40;
                positions[i * 3 + 1] = Math.random() * 10;
                positions[i * 3 + 2] = (Math.random() - 0.5) * 20 - 5;
                
                velocities.push({
                    x: (Math.random() - 0.5) * 0.02,
                    y: -Math.random() * 0.02 - 0.01,
                    z: (Math.random() - 0.5) * 0.02
                });
            }
            
            particles.setAttribute('position', new THREE.BufferAttribute(positions, 3));
            
            const particleMat = new THREE.PointsMaterial({
                color: 0xffb7c5,
                size: 0.15,
                transparent: true,
                opacity: 0.8
            });
            
            const particleSystem = new THREE.Points(particles, particleMat);
            villageBackground.add(particleSystem);
            
            // Animate particles
            villageBackground.userData.particles = particleSystem;
            villageBackground.userData.velocities = velocities;
            
            scene.add(villageBackground);
        }

        function updateVillage() {
            if (!villageBackground) return;
            
            const particles = villageBackground.userData.particles;
            const positions = particles.geometry.attributes.position.array;
            const velocities = villageBackground.userData.velocities;
            
            for (let i = 0; i < velocities.length; i++) {
                positions[i * 3] += velocities[i].x;
                positions[i * 3 + 1] += velocities[i].y;
                positions[i * 3 + 2] += velocities[i].z;
                
                // Reset if too low
                if (positions[i * 3 + 1] < 0) {
                    positions[i * 3 + 1] = 10;
                    positions[i * 3] = (Math.random() - 0.5) * 40;
                }
            }
            
            particles.geometry.attributes.position.needsUpdate = true;
            particles.rotation.y += 0.001;
        }

        // Game Initialization
        let cameraShake = 0;

        function initGame() {
            // Scene setup
            scene = new THREE.Scene();
            
            // Gradient sky
            const canvas = document.createElement('canvas');
            canvas.width = 2;
            canvas.height = 512;
            const context = canvas.getContext('2d');
            const gradient = context.createLinearGradient(0, 0, 0, 512);
            gradient.addColorStop(0, '#1a1a2e');
            gradient.addColorStop(0.5, '#16213e');
            gradient.addColorStop(1, '#0f3460');
            context.fillStyle = gradient;
            context.fillRect(0, 0, 2, 512);
            
            const skyTexture = new THREE.CanvasTexture(canvas);
            scene.background = skyTexture;
            scene.fog = new THREE.Fog(0x1a1a2e, 10, 40);

            // Camera
            camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.set(0, 4, 10);

            // Renderer
            renderer = new THREE.WebGLRenderer({ canvas: document.getElementById('game-canvas'), antialias: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.shadowMap.enabled = true;
            renderer.shadowMap.type = THREE.PCFSoftShadowMap;

            // Lighting
            const ambientLight = new THREE.AmbientLight(0x404060, 0.4);
            scene.add(ambientLight);

            const moonLight = new THREE.DirectionalLight(0xaaccff, 0.5);
            moonLight.position.set(10, 20, 10);
            moonLight.castShadow = true;
            moonLight.shadow.mapSize.width = 2048;
            moonLight.shadow.mapSize.height = 2048;
            scene.add(moonLight);

            // Fighting arena lighting
            const arenaLight = new THREE.SpotLight(0xffaa00, 0.8);
            arenaLight.position.set(0, 15, 5);
            arenaLight.angle = Math.PI / 3;
            arenaLight.penumbra = 0.5;
            arenaLight.castShadow = true;
            scene.add(arenaLight);

            // Village background
            createVillageBackground();

            // Fighting stage
            const stageGeo = new THREE.CylinderGeometry(12, 12, 0.5, 64);
            const stageMat = new THREE.MeshPhongMaterial({ 
                color: 0x3d2817,
                shininess: 50
            });
            const stage = new THREE.Mesh(stageGeo, stageMat);
            stage.position.y = -0.25;
            stage.receiveShadow = true;
            scene.add(stage);

            // Stage pattern
            const patternGeo = new THREE.RingGeometry(6, 11, 64);
            const patternMat = new THREE.MeshPhongMaterial({ 
                color: 0x2a1b0f,
                shininess: 30
            });
            const pattern = new THREE.Mesh(patternGeo, patternMat);
            pattern.rotation.x = -Math.PI / 2;
            pattern.position.y = 0.01;
            pattern.receiveShadow = true;
            scene.add(pattern);

            // Create fighters
            p1 = new Fighter(gameState.p1Char, -4, true);
            scene.add(p1.mesh);

            p2 = new Fighter(gameState.p2Char, 4, false);
            scene.add(p2.mesh);

            // Update UI names
            document.getElementById('p1-char-name').textContent = gameState.p1Char.name;
            document.getElementById('p2-char-name').textContent = gameState.p2Char.name;
            document.getElementById('p1-char-name').style.color = '#' + gameState.p1Char.auraColor.toString(16).padStart(6, '0');
            document.getElementById('p2-char-name').style.color = '#' + gameState.p2Char.auraColor.toString(16).padStart(6, '0');

            // Reset game state
            gameState.round = 1;
            document.getElementById('round').textContent = '1';
            document.getElementById('timer').textContent = '99';
            document.getElementById('win-display').classList.add('hidden');
            
            // Event listeners
            window.addEventListener('keydown', handleKeyDown);
            window.addEventListener('keyup', handleKeyUp);
            window.addEventListener('resize', onWindowResize);

            // Start game loop
            gameState.paused = false;
            clock = new THREE.Clock();
            
            // Timer
            if (gameInterval) clearInterval(gameInterval);
            gameTime = 99;
            gameInterval = setInterval(() => {
                if (!gameState.paused && gameTime > 0) {
                    gameTime--;
                    document.getElementById('timer').textContent = gameTime;
                    if (gameTime === 0) {
                        endRound(p1.health > p2.health ? 1 : (p2.health > p1.health ? 2 : 0));
                    }
                }
            }, 1000);

            gameLoop();
        }

        let gameTime = 99;

        function handleKeyDown(e) {
            if (e.key === 'Escape') {
                togglePause();
                return;
            }
            
            // P1
            if (e.key === 'w' || e.key === 'W') p1Input.up = true;
            if (e.key === 's' || e.key === 'S') p1Input.down = true;
            if (e.key === 'a' || e.key === 'A') p1Input.left = true;
            if (e.key === 'd' || e.key === 'D') p1Input.right = true;
            if (e.key === 'f' || e.key === 'F') p1Input.punch = true;
            if (e.key === 'g' || e.key === 'G') p1Input.kick = true;
            if (e.key === 'r' || e.key === 'R') p1Input.special = true;

            // P2
            if (e.key === 'ArrowUp') p2Input.up = true;
            if (e.key === 'ArrowDown') p2Input.down = true;
            if (e.key === 'ArrowLeft') p2Input.left = true;
            if (e.key === 'ArrowRight') p2Input.right = true;
            if (e.key === 'k' || e.key === 'K') p2Input.punch = true;
            if (e.key === 'l' || e.key === 'L') p2Input.kick = true;
            if (e.key === 'p' || e.key === 'P') p2Input.special = true;
            
            // Character select navigation
            if (document.getElementById('char-select').classList.contains('active')) {
                if (e.key === 'a' || e.key === 'A') navigateCharSelect(1, -1);
                if (e.key === 'd' || e.key === 'D') navigateCharSelect(1, 1);
                if (e.key === 'f' || e.key === 'F') confirmSelection(1);
                
                if (e.key === 'ArrowLeft') navigateCharSelect(2, -1);
                if (e.key === 'ArrowRight') navigateCharSelect(2, 1);
                if (e.key === 'k' || e.key === 'K') confirmSelection(2);
            }
        }

        function navigateCharSelect(player, dir) {
            const currentIdx = player === 1 ? gameState.p1Index : gameState.p2Index;
            let newIdx = currentIdx + dir;
            if (newIdx < 0) newIdx = characters.length - 1;
            if (newIdx >= characters.length) newIdx = 0;
            selectCharacter(player, newIdx);
        }

        function confirmSelection(player) {
            // Visual feedback
            const indicator = document.getElementById(player === 1 ? 'p1-indicator' : 'p2-indicator');
            indicator.classList.add('animate-pulse');
            setTimeout(() => indicator.classList.remove('animate-pulse'), 500);
        }

        function handleKeyUp(e) {
            if (e.key === 'w' || e.key === 'W') p1Input.up = false;
            if (e.key === 's' || e.key === 'S') p1Input.down = false;
            if (e.key === 'a' || e.key === 'A') p1Input.left = false;
            if (e.key === 'd' || e.key === 'D') p1Input.right = false;
            if (e.key === 'f' || e.key === 'F') p1Input.punch = false;
            if (e.key === 'g' || e.key === 'G') p1Input.kick = false;
            if (e.key === 'r' || e.key === 'R') p1Input.special = false;

            if (e.key === 'ArrowUp') p2Input.up = false;
            if (e.key === 'ArrowDown') p2Input.down = false;
            if (e.key === 'ArrowLeft') p2Input.left = false;
            if (e.key === 'ArrowRight') p2Input.right = false;
            if (e.key === 'k' || e.key === 'K') p2Input.punch = false;
            if (e.key === 'l' || e.key === 'L') p2Input.kick = false;
            if (e.key === 'p' || e.key === 'P') p2Input.special = false;
        }

        function onWindowResize() {
            if (!camera || !renderer) return;
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        function updateHealthBars() {
            document.getElementById('p1-health').style.width = p1.health + '%';
            document.getElementById('p2-health').style.width = p2.health + '%';
            document.getElementById('p1-special').style.width = p1.special + '%';
            document.getElementById('p2-special').style.width = p2.special + '%';
        }

        function endRound(winner) {
            gameState.paused = true;
            const winDisplay = document.getElementById('win-display');
            const winnerText = document.getElementById('winner-text');
            const winReason = document.getElementById('win-reason');
            
            winDisplay.classList.remove('hidden');
            
            if (winner === 0) {
                winnerText.textContent = 'DRAW!';
                winReason.textContent = 'TIME UP';
            } else {
                const winnerChar = winner === 1 ? gameState.p1Char : gameState.p2Char;
                winnerText.textContent = winnerChar.name + ' WINS!';
                winnerText.style.background = `linear-gradient(to right, #${winnerChar.auraColor.toString(16).padStart(6, '0')}, #ffd700)`;
                winnerText.style.webkitBackgroundClip = 'text';
                winnerText.style.webkitTextFillColor = 'transparent';
                winReason.textContent = p1.health <= 0 || p2.health <= 0 ? 'K.O.!' : 'TIME UP';
            }
        }

        function gameLoop() {
            if (!gameState.paused) {
                const delta = clock.getDelta();
                
                // Update fighters
                p1.update(delta, p1Input, p2);
                p2.update(delta, p2Input, p1);
                
                // Update village animation
                updateVillage();
                
                // Camera follow
                const midX = (p1.mesh.position.x + p2.mesh.position.x) / 2;
                const dist = Math.abs(p1.mesh.position.x - p2.mesh.position.x);
                const targetX = midX * 0.4;
                const targetZ = 9 + dist * 0.2;
                const targetY = 4 + dist * 0.05;
                
                camera.position.x += (targetX - camera.position.x) * 0.05;
                camera.position.z += (targetZ - camera.position.z) * 0.05;
                camera.position.y += (targetY - camera.position.y) * 0.05;
                camera.lookAt(midX * 0.2, 1.5, 0);
                
                // Camera shake
                if (cameraShake > 0) {
                    camera.position.x += (Math.random() - 0.5) * cameraShake;
                    camera.position.y += (Math.random() - 0.5) * cameraShake;
                    cameraShake *= 0.9;
                    if (cameraShake < 0.01) cameraShake = 0;
                }
                
                renderer.render(scene, camera);
            }
            
            requestAnimationFrame(gameLoop);
        }

        // Initialize
        window.onload = () => {
            document.getElementById('start-screen').classList.add('active');
        };
    </script>
</body>
</html>
