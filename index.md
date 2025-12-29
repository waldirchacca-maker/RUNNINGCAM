<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TrackRun Pro - Datos Persistentes</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        :root {
            --primary: #FF6B35;
            --primary-dark: #E55A2B;
            --secondary: #00A8E8;
            --accent: #6A0572;
            --success: #4CAF50;
            --warning: #FF9800;
            --danger: #FF5252;
            --dark: #1A1A2E;
            --light: #F8F9FA;
            --gray: #6C757D;
            --card-bg: #FFFFFF;
            --shadow: 0 6px 20px rgba(0, 0, 0, 0.08);
            --border-radius: 16px;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', 'Roboto', 'Helvetica Neue', Arial, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #1A1A2E 0%, #16213E 100%);
            color: var(--light);
            min-height: 100vh;
            overflow-x: hidden;
        }

        .app-container {
            display: flex;
            flex-direction: column;
            min-height: 100vh;
            max-width: 100%;
        }

        /* Header */
        .app-header {
            background: rgba(26, 26, 46, 0.95);
            backdrop-filter: blur(10px);
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            position: sticky;
            top: 0;
            z-index: 1000;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .logo-container {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .logo-icon {
            background: linear-gradient(135deg, var(--primary), var(--accent));
            width: 40px;
            height: 40px;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 20px;
        }

        .logo-text h1 {
            font-size: 1.4rem;
            font-weight: 800;
            background: linear-gradient(to right, var(--primary), var(--secondary));
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
        }

        .logo-text p {
            font-size: 0.75rem;
            color: var(--gray);
        }

        /* Session Status */
        .session-status {
            background: rgba(255, 107, 53, 0.2);
            padding: 8px 15px;
            border-radius: 20px;
            font-size: 0.9rem;
            display: flex;
            align-items: center;
            gap: 8px;
            animation: pulse 2s infinite;
        }

        /* Main Content */
        .main-content {
            display: flex;
            flex: 1;
            padding: 20px;
            gap: 20px;
            flex-wrap: wrap;
        }

        /* Map Container */
        .map-section {
            flex: 2;
            min-width: 300px;
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        .map-container {
            flex: 1;
            background: var(--dark);
            border-radius: var(--border-radius);
            overflow: hidden;
            box-shadow: var(--shadow);
            position: relative;
        }

        #map {
            width: 100%;
            height: 100%;
            min-height: 400px;
        }

        /* Map Controls */
        .map-controls {
            display: flex;
            justify-content: space-between;
            background: rgba(26, 26, 46, 0.8);
            padding: 12px 20px;
            border-radius: var(--border-radius);
            backdrop-filter: blur(10px);
        }

        .map-type-selector {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
        }

        .map-type-btn {
            padding: 8px 16px;
            background: rgba(255, 255, 255, 0.1);
            border: none;
            border-radius: 10px;
            color: white;
            cursor: pointer;
            font-size: 0.9rem;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .map-type-btn.active {
            background: var(--primary);
        }

        .icon-btn {
            width: 40px;
            height: 40px;
            border-radius: 12px;
            background: rgba(255, 255, 255, 0.1);
            border: none;
            color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.2rem;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .icon-btn:hover {
            background: rgba(255, 255, 255, 0.2);
            transform: translateY(-2px);
        }

        /* GPS Overlay */
        .gps-overlay {
            position: absolute;
            top: 15px;
            left: 15px;
            z-index: 1000;
            background: rgba(0, 0, 0, 0.8);
            padding: 15px;
            border-radius: 12px;
            backdrop-filter: blur(5px);
            max-width: 300px;
        }

        .gps-status {
            display: flex;
            align-items: center;
            gap: 12px;
            margin-bottom: 10px;
        }

        .gps-indicator {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            background: var(--danger);
            animation: pulse 2s infinite;
        }

        .gps-indicator.active {
            background: var(--success);
            box-shadow: 0 0 15px var(--success);
        }

        /* Stats Section */
        .stats-section {
            flex: 1;
            min-width: 300px;
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        .stats-container {
            background: rgba(26, 26, 46, 0.8);
            backdrop-filter: blur(10px);
            border-radius: var(--border-radius);
            padding: 25px;
            box-shadow: var(--shadow);
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 20px;
        }

        .stat-card {
            background: rgba(255, 255, 255, 0.05);
            border-radius: 14px;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-align: center;
            transition: transform 0.3s ease;
            border: 1px solid rgba(255, 255, 255, 0.05);
        }

        .stat-card:hover {
            transform: translateY(-5px);
            background: rgba(255, 255, 255, 0.08);
        }

        .stat-card.primary {
            background: linear-gradient(135deg, rgba(255, 107, 53, 0.2), rgba(106, 5, 114, 0.2));
            border: 1px solid rgba(255, 107, 53, 0.3);
        }

        .stat-value {
            font-size: 2.2rem;
            font-weight: 800;
            margin: 8px 0;
            color: white;
        }

        .stat-label {
            font-size: 0.9rem;
            color: var(--gray);
            text-transform: uppercase;
            letter-spacing: 1px;
            font-weight: 600;
        }

        .stat-unit {
            font-size: 0.8rem;
            color: var(--primary);
            margin-top: 4px;
            font-weight: 600;
        }

        /* Control Panel */
        .control-panel {
            background: rgba(26, 26, 46, 0.8);
            backdrop-filter: blur(10px);
            border-radius: var(--border-radius);
            padding: 25px;
            box-shadow: var(--shadow);
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .control-buttons {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .control-btn {
            padding: 18px;
            border: none;
            border-radius: 14px;
            font-size: 1.1rem;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
        }

        .control-btn.start {
            background: linear-gradient(135deg, var(--primary), var(--primary-dark));
            color: white;
        }

        .control-btn.stop {
            background: linear-gradient(135deg, var(--danger), #FF8A80);
            color: white;
        }

        .control-btn.reset {
            background: rgba(255, 255, 255, 0.1);
            color: white;
        }

        .control-btn.video {
            background: linear-gradient(135deg, var(--secondary), #0097CC);
            color: white;
        }

        .control-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none !important;
        }

        .control-btn:not(:disabled):hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
        }

        /* Session Info */
        .session-info {
            background: rgba(26, 26, 46, 0.8);
            backdrop-filter: blur(10px);
            border-radius: var(--border-radius);
            padding: 25px;
            box-shadow: var(--shadow);
            margin-top: 15px;
        }

        .session-details {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-top: 15px;
        }

        .detail-item {
            background: rgba(255, 255, 255, 0.05);
            padding: 15px;
            border-radius: 12px;
        }

        .detail-label {
            font-size: 0.85rem;
            color: var(--gray);
            margin-bottom: 5px;
        }

        .detail-value {
            font-size: 1.2rem;
            font-weight: 700;
            color: white;
        }

        /* Toast Notifications */
        .toast {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background: var(--success);
            color: white;
            padding: 15px 25px;
            border-radius: 12px;
            box-shadow: var(--shadow);
            z-index: 3000;
            animation: slideIn 0.3s ease;
            display: flex;
            align-items: center;
            gap: 12px;
            max-width: 350px;
        }

        .toast.error {
            background: var(--danger);
        }

        .toast.warning {
            background: var(--warning);
        }

        .toast.info {
            background: var(--secondary);
        }

        /* Progress Bar */
        .progress-container {
            width: 100%;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            margin: 20px 0;
            overflow: hidden;
        }

        .progress-bar {
            height: 20px;
            background: linear-gradient(90deg, var(--primary), var(--secondary));
            border-radius: 10px;
            width: 0%;
            transition: width 0.3s ease;
        }

        .progress-text {
            text-align: center;
            margin-top: 10px;
            color: var(--gray);
            font-size: 0.9rem;
        }

        /* Start Point Marker */
        .start-point {
            background: var(--success);
            color: white;
            padding: 8px 15px;
            border-radius: 20px;
            font-size: 0.9rem;
            font-weight: 600;
            display: flex;
            align-items: center;
            gap: 8px;
            position: absolute;
            top: 15px;
            right: 15px;
            z-index: 1000;
        }

        /* Recovery Modal */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.9);
            z-index: 2000;
            align-items: center;
            justify-content: center;
            animation: fadeIn 0.3s ease;
        }

        .modal-content {
            background: var(--dark);
            border-radius: var(--border-radius);
            padding: 30px;
            max-width: 500px;
            width: 90%;
            animation: slideIn 0.4s ease;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .modal-title {
            font-size: 1.5rem;
            font-weight: 700;
            color: white;
        }

        .close-modal {
            background: none;
            border: none;
            color: var(--gray);
            font-size: 1.5rem;
            cursor: pointer;
        }

        /* Animations */
        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.7; }
            100% { opacity: 1; }
        }

        @keyframes slideIn {
            from { transform: translateY(20px); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        /* Responsive */
        @media (max-width: 768px) {
            .main-content {
                flex-direction: column;
                padding: 15px;
            }
            
            .stats-container {
                grid-template-columns: repeat(2, 1fr);
            }
            
            .app-header {
                padding: 12px 15px;
            }
            
            .stat-value {
                font-size: 1.8rem;
            }
            
            .map-type-selector {
                justify-content: center;
            }
        }

        @media (max-width: 480px) {
            .stats-container {
                grid-template-columns: 1fr;
            }
            
            .control-btn {
                padding: 16px;
                font-size: 1rem;
            }
            
            .map-container {
                min-height: 350px;
            }
        }
    </style>
</head>
<body>
    <div class="app-container">
        <!-- Header -->
        <header class="app-header">
            <div class="logo-container">
                <div class="logo-icon">
                    <i class="fas fa-running"></i>
                </div>
                <div class="logo-text">
                    <h1>TrackRun Pro</h1>
                    <p>Datos persistentes - No se pierden al recargar</p>
                </div>
            </div>
            <div class="session-status" id="sessionStatus" style="display: none;">
                <i class="fas fa-history"></i>
                <span>RECUPERANDO SESIÓN ACTIVA...</span>
            </div>
        </header>

        <!-- Main Content -->
        <main class="main-content">
            <!-- Map Section -->
            <section class="map-section">
                <div class="map-container">
                    <div id="map"></div>
                    
                    <!-- GPS Overlay -->
                    <div class="gps-overlay" id="gpsOverlay">
                        <div class="gps-status">
                            <div class="gps-indicator" id="gpsIndicator"></div>
                            <div>
                                <h3 id="gpsStatusText">Inicializando GPS...</h3>
                                <p id="gpsDetails">Cargando datos guardados...</p>
                            </div>
                        </div>
                    </div>

                    <!-- Start Point Marker -->
                    <div class="start-point" id="startPoint" style="display: none;">
                        <i class="fas fa-flag-checkered"></i>
                        <span id="startPointText">Punto de inicio fijado</span>
                    </div>
                </div>

                <!-- Map Controls -->
                <div class="map-controls">
                    <div class="map-type-selector">
                        <button class="map-type-btn active" data-type="street">
                            <i class="fas fa-map"></i> Callejero
                        </button>
                        <button class="map-type-btn" data-type="satellite">
                            <i class="fas fa-satellite"></i> Satélite
                        </button>
                        <button class="map-type-btn" data-type="topographic">
                            <i class="fas fa-mountain"></i> Topográfico
                        </button>
                    </div>
                    <button class="icon-btn" id="centerMapBtn" title="Centrar en mi ubicación">
                        <i class="fas fa-location-crosshairs"></i>
                    </button>
                </div>
            </section>

            <!-- Stats and Controls Section -->
            <section class="stats-section">
                <!-- Stats Cards -->
                <div class="stats-container">
                    <div class="stat-card primary">
                        <div class="stat-label">Distancia</div>
                        <div class="stat-value" id="distance">0.00</div>
                        <div class="stat-unit">kilómetros</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-label">Ritmo (Pace)</div>
                        <div class="stat-value" id="pace">--:--</div>
                        <div class="stat-unit">min/km</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-label">Tiempo</div>
                        <div class="stat-value" id="time">00:00:00</div>
                        <div class="stat-unit">hh:mm:ss</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-label">Desnivel</div>
                        <div class="stat-value" id="elevation">+0</div>
                        <div class="stat-unit">metros</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-label">Velocidad</div>
                        <div class="stat-value" id="speed">0.0</div>
                        <div class="stat-unit">km/h</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-label">Calorías</div>
                        <div class="stat-value" id="calories">0</div>
                        <div class="stat-unit">kcal</div>
                    </div>
                </div>

                <!-- Control Panel -->
                <div class="control-panel">
                    <div class="control-buttons">
                        <button class="control-btn start" id="startBtn">
                            <i class="fas fa-play-circle"></i> INICIAR CARRERA
                        </button>
                        <button class="control-btn stop" id="stopBtn" disabled>
                            <i class="fas fa-stop-circle"></i> DETENER CARRERA
                        </button>
                        <button class="control-btn reset" id="resetBtn">
                            <i class="fas fa-redo"></i> REINICIAR TODO
                        </button>
                        <button class="control-btn video" id="videoBtn" disabled>
                            <i class="fas fa-video"></i> GENERAR VIDEO
                        </button>
                    </div>

                    <!-- Session Info -->
                    <div class="session-info">
                        <h4 style="color: var(--secondary); margin-bottom: 15px;">
                            <i class="fas fa-history"></i> Información de Sesión
                        </h4>
                        <div class="session-details">
                            <div class="detail-item">
                                <div class="detail-label">Estado</div>
                                <div class="detail-value" id="sessionState">Inactiva</div>
                            </div>
                            <div class="detail-item">
                                <div class="detail-label">Iniciada</div>
                                <div class="detail-value" id="sessionStart">--:--:--</div>
                            </div>
                            <div class="detail-item">
                                <div class="detail-label">Posiciones</div>
                                <div class="detail-value" id="sessionPositions">0</div>
                            </div>
                            <div class="detail-item">
                                <div class="detail-label">Último guardado</div>
                                <div class="detail-value" id="lastSave">--:--:--</div>
                            </div>
                        </div>
                        <div style="margin-top: 15px; font-size: 0.8rem; color: var(--gray);">
                            <i class="fas fa-info-circle"></i> Los datos se guardan automáticamente cada 10 segundos
                        </div>
                    </div>

                    <!-- Quick Actions -->
                    <div style="margin-top: 20px;">
                        <button class="control-btn" id="saveSessionBtn" style="background: rgba(76,175,80,0.2);">
                            <i class="fas fa-save"></i> GUARDAR SESIÓN MANUALMENTE
                        </button>
                    </div>
                </div>
            </section>
        </main>
    </div>

    <!-- Recovery Modal -->
    <div class="modal" id="recoveryModal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">
                    <i class="fas fa-history"></i> Sesión Recuperada
                </h3>
            </div>
            <div style="padding: 20px;">
                <div style="background: rgba(255,107,53,0.1); padding: 15px; border-radius: 8px; margin-bottom: 20px;">
                    <h4 style="color: var(--primary); margin-bottom: 10px;">
                        <i class="fas fa-exclamation-triangle"></i> Sesión activa encontrada
                    </h4>
                    <p>Se ha recuperado una sesión de carrera en curso que fue interrumpida.</p>
                </div>
                
                <div class="session-details">
                    <div class="detail-item">
                        <div class="detail-label">Tiempo transcurrido</div>
                        <div class="detail-value" id="recoveryTime">00:00:00</div>
                    </div>
                    <div class="detail-item">
                        <div class="detail-label">Distancia recorrida</div>
                        <div class="detail-value" id="recoveryDistance">0.00 km</div>
                    </div>
                    <div class="detail-item">
                        <div class="detail-label">Posiciones guardadas</div>
                        <div class="detail-value" id="recoveryPositions">0</div>
                    </div>
                </div>
                
                <div style="margin-top: 30px;">
                    <p style="margin-bottom: 15px; color: var(--light);">
                        ¿Qué deseas hacer con esta sesión?
                    </p>
                    <div style="display: flex; flex-direction: column; gap: 10px;">
                        <button class="control-btn start" id="resumeSessionBtn">
                            <i class="fas fa-play"></i> REANUDAR SESIÓN
                        </button>
                        <button class="control-btn" id="saveAndNewBtn" style="background: rgba(0,168,232,0.2);">
                            <i class="fas fa-save"></i> GUARDAR Y COMENZAR NUEVA
                        </button>
                        <button class="control-btn reset" id="discardSessionBtn">
                            <i class="fas fa-trash"></i> DESCARTAR SESIÓN
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Toast Notifications -->
    <div class="toast" id="toast" style="display: none;">
        <i class="fas fa-check-circle"></i>
        <span id="toastMessage">Mensaje</span>
    </div>

    <!-- Leaflet -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        // ============================================
        // VARIABLES GLOBALES Y CONSTANTES
        // ============================================
        const STORAGE_KEYS = {
            ACTIVE_SESSION: 'trackrun_active_session',
            SESSION_DATA: 'trackrun_session_data',
            LAST_SAVE: 'trackrun_last_save',
            SESSION_HISTORY: 'trackrun_session_history'
        };

        const AUTO_SAVE_INTERVAL = 10000; // 10 segundos
        const MAX_POSITIONS = 10000; // Límite de posiciones

        let map = null;
        let userMarker = null;
        let routePolyline = null;
        let watchId = null;
        let isTracking = false;
        let startTime = null;
        let timerInterval = null;
        let startPosition = null;
        let currentPosition = null;
        let positions = [];
        let totalDistance = 0;
        let elevationGain = 0;
        let maxSpeed = 0;
        let autoSaveInterval = null;
        let recoveredSession = null;

        // ============================================
        // INICIALIZACIÓN CON PERSISTENCIA
        // ============================================
        document.addEventListener('DOMContentLoaded', async () => {
            console.log('Inicializando TrackRun Pro con persistencia...');
            
            // Mostrar estado de recuperación
            document.getElementById('sessionStatus').style.display = 'flex';
            
            try {
                // 1. Verificar si hay sesión activa
                await checkForActiveSession();
                
                // 2. Inicializar mapa
                initMap();
                
                // 3. Configurar eventos
                setupEventListeners();
                
                // 4. Inicializar GPS
                await initializeGPS();
                
                document.getElementById('sessionStatus').style.display = 'none';
                
            } catch (error) {
                console.error('Error en inicialización:', error);
                showToast('Error: ' + error.message, 'error');
                document.getElementById('sessionStatus').style.display = 'none';
            }
        });

        // ============================================
        // SISTEMA DE PERSISTENCIA
        // ============================================
        async function checkForActiveSession() {
            const activeSession = localStorage.getItem(STORAGE_KEYS.ACTIVE_SESSION);
            
            if (activeSession === 'true') {
                console.log('Sesión activa encontrada, recuperando datos...');
                
                // Cargar datos de la sesión
                const sessionData = loadSessionData();
                
                if (sessionData && sessionData.positions && sessionData.positions.length > 0) {
                    recoveredSession = sessionData;
                    
                    // Mostrar modal de recuperación
                    showRecoveryModal(sessionData);
                    
                    return true;
                }
            }
            
            return false;
        }

        function loadSessionData() {
            try {
                const data = localStorage.getItem(STORAGE_KEYS.SESSION_DATA);
                if (!data) return null;
                
                const parsed = JSON.parse(data);
                
                // Convertir strings de fecha a objetos Date
                if (parsed.startTime) {
                    parsed.startTime = new Date(parsed.startTime);
                }
                
                if (parsed.lastSave) {
                    parsed.lastSave = new Date(parsed.lastSave);
                }
                
                // Convertir timestamps de posiciones
                if (parsed.positions && parsed.positions.length > 0) {
                    parsed.positions = parsed.positions.map(pos => ({
                        ...pos,
                        timestamp: new Date(pos.timestamp)
                    }));
                }
                
                console.log('Datos de sesión cargados:', parsed);
                return parsed;
                
            } catch (error) {
                console.error('Error al cargar datos de sesión:', error);
                return null;
            }
        }

        function saveSessionData() {
            try {
                const sessionData = {
                    startTime: startTime,
                    positions: positions.slice(-MAX_POSITIONS), // Limitar tamaño
                    totalDistance: totalDistance,
                    elevationGain: elevationGain,
                    maxSpeed: maxSpeed,
                    startPosition: startPosition,
                    currentPosition: currentPosition,
                    isTracking: isTracking,
                    lastSave: new Date()
                };
                
                localStorage.setItem(STORAGE_KEYS.SESSION_DATA, JSON.stringify(sessionData));
                localStorage.setItem(STORAGE_KEYS.LAST_SAVE, new Date().toISOString());
                localStorage.setItem(STORAGE_KEYS.ACTIVE_SESSION, isTracking ? 'true' : 'false');
                
                updateLastSaveDisplay();
                console.log('Sesión guardada:', sessionData.positions.length, 'posiciones');
                
            } catch (error) {
                console.error('Error al guardar sesión:', error);
                showToast('Error al guardar sesión', 'error');
            }
        }

        function clearSessionData() {
            localStorage.removeItem(STORAGE_KEYS.SESSION_DATA);
            localStorage.removeItem(STORAGE_KEYS.ACTIVE_SESSION);
            localStorage.removeItem(STORAGE_KEYS.LAST_SAVE);
        }

        function archiveSession() {
            try {
                const sessionData = loadSessionData();
                if (!sessionData) return;
                
                // Obtener historial actual
                let history = JSON.parse(localStorage.getItem(STORAGE_KEYS.SESSION_HISTORY) || '[]');
                
                // Añadir sesión actual al historial
                history.push({
                    ...sessionData,
                    endTime: new Date(),
                    archivedAt: new Date().toISOString()
                });
                
                // Mantener solo las últimas 10 sesiones
                if (history.length > 10) {
                    history = history.slice(-10);
                }
                
                localStorage.setItem(STORAGE_KEYS.SESSION_HISTORY, JSON.stringify(history));
                console.log('Sesión archivada en historial');
                
            } catch (error) {
                console.error('Error al archivar sesión:', error);
            }
        }

        function updateLastSaveDisplay() {
            const lastSave = localStorage.getItem(STORAGE_KEYS.LAST_SAVE);
            if (lastSave) {
                const time = new Date(lastSave);
                document.getElementById('lastSave').textContent = 
                    time.toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
            }
        }

        // ============================================
        // RECUPERACIÓN DE SESIÓN
        // ============================================
        function showRecoveryModal(sessionData) {
            // Calcular tiempo transcurrido
            const elapsedTime = sessionData.startTime ? 
                new Date() - new Date(sessionData.startTime) : 0;
            
            const hours = Math.floor(elapsedTime / 3600000);
            const minutes = Math.floor((elapsedTime % 3600000) / 60000);
            const seconds = Math.floor((elapsedTime % 60000) / 1000);
            
            // Actualizar información en el modal
            document.getElementById('recoveryTime').textContent = 
                `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
            
            document.getElementById('recoveryDistance').textContent = 
                `${sessionData.totalDistance?.toFixed(2) || '0.00'} km`;
            
            document.getElementById('recoveryPositions').textContent = 
                sessionData.positions?.length || 0;
            
            // Mostrar modal
            document.getElementById('recoveryModal').style.display = 'flex';
            
            // Configurar botones del modal
            document.getElementById('resumeSessionBtn').onclick = () => {
                resumeSession(sessionData);
                document.getElementById('recoveryModal').style.display = 'none';
            };
            
            document.getElementById('saveAndNewBtn').onclick = () => {
                archiveSession();
                clearSessionData();
                document.getElementById('recoveryModal').style.display = 'none';
                initializeNewSession();
            };
            
            document.getElementById('discardSessionBtn').onclick = () => {
                clearSessionData();
                document.getElementById('recoveryModal').style.display = 'none';
                initializeNewSession();
            };
        }

        function resumeSession(sessionData) {
            console.log('Reanudando sesión recuperada...');
            
            // Restaurar datos de la sesión
            startTime = sessionData.startTime;
            positions = sessionData.positions || [];
            totalDistance = sessionData.totalDistance || 0;
            elevationGain = sessionData.elevationGain || 0;
            maxSpeed = sessionData.maxSpeed || 0;
            startPosition = sessionData.startPosition;
            currentPosition = sessionData.currentPosition;
            isTracking = sessionData.isTracking || false;
            
            // Actualizar UI
            updateStatsDisplay();
            updateSessionInfo();
            
            // Si estaba trackeando, reanudar
            if (isTracking && sessionData.isTracking) {
                resumeTracking();
            } else {
                // Solo mostrar datos
                displayRecoveredRoute();
                showToast('Sesión recuperada. Los datos están listos.', 'success');
            }
        }

        function resumeTracking() {
            console.log('Reanudando seguimiento GPS...');
            
            isTracking = true;
            
            // Iniciar temporizador
            updateTimer();
            timerInterval = setInterval(updateTimer, 1000);
            
            // Iniciar seguimiento GPS
            watchId = navigator.geolocation.watchPosition(
                (position) => handleNewPosition(position),
                (error) => handleGPSError(error),
                { enableHighAccuracy: true, maximumAge: 0, timeout: 10000 }
            );
            
            // Iniciar guardado automático
            startAutoSave();
            
            // Actualizar UI
            document.getElementById('startBtn').disabled = true;
            document.getElementById('stopBtn').disabled = false;
            document.getElementById('startPoint').style.display = 'flex';
            
            showToast('¡Sesión reanudada! Seguimiento GPS activado.', 'success');
            updateGPSStatus('grabando', 'Reanudando grabación...');
            
            // Mostrar ruta recuperada
            displayRecoveredRoute();
        }

        function displayRecoveredRoute() {
            if (!map || positions.length === 0) return;
            
            // Dibujar ruta recuperada
            const latLngs = positions.map(p => [p.lat, p.lng]);
            
            if (routePolyline) {
                map.removeLayer(routePolyline);
            }
            
            routePolyline = L.polyline(latLngs, {
                color: '#FF6B35',
                weight: 4,
                opacity: 0.8,
                dashArray: '5, 10' // Línea punteada para indicar recuperación
            }).addTo(map);
            
            // Centrar en la última posición
            if (currentPosition) {
                map.setView([currentPosition.lat, currentPosition.lng], 16);
                
                // Actualizar marcador
                if (!userMarker) {
                    userMarker = L.marker([currentPosition.lat, currentPosition.lng], {
                        icon: L.divIcon({
                            className: 'user-marker',
                            html: '<div style="background: #FF6B35; width: 24px; height: 24px; border-radius: 50%; border: 3px solid white; box-shadow: 0 0 10px rgba(0,0,0,0.5);"></div>',
                            iconSize: [24, 24]
                        })
                    }).addTo(map);
                } else {
                    userMarker.setLatLng([currentPosition.lat, currentPosition.lng]);
                }
            }
            
            // Mostrar punto de inicio
            if (startPosition) {
                L.marker([startPosition.lat, startPosition.lng], {
                    icon: L.divIcon({
                        className: 'start-marker',
                        html: '<div style="background: #4CAF50; width: 20px; height: 20px; border-radius: 50%; border: 3px solid white; box-shadow: 0 0 10px rgba(76,175,80,0.8);"></div>',
                        iconSize: [20, 20]
                    })
                }).addTo(map).bindPopup('Punto de inicio (recuperado)');
                
                document.getElementById('startPoint').style.display = 'flex';
                document.getElementById('startPointText').textContent = 'Punto de inicio (recuperado)';
            }
        }

        // ============================================
        // INICIALIZACIÓN NUEVA SESIÓN
        // ============================================
        function initializeNewSession() {
            // Limpiar variables
            startTime = null;
            positions = [];
            totalDistance = 0;
            elevationGain = 0;
            maxSpeed = 0;
            startPosition = null;
            currentPosition = null;
            isTracking = false;
            
            // Limpiar mapa
            if (routePolyline) {
                map.removeLayer(routePolyline);
                routePolyline = null;
            }
            
            // Actualizar UI
            updateStatsDisplay();
            updateSessionInfo();
            document.getElementById('startPoint').style.display = 'none';
            
            showToast('Listo para nueva sesión', 'info');
        }

        async function initializeGPS() {
            try {
                await requestGPSPermissions();
                await getInitialLocation();
                updateUIForReadyState();
                showToast('GPS listo. Los datos se guardan automáticamente.', 'success');
            } catch (error) {
                console.error('Error GPS:', error);
                showToast('Error GPS: ' + error.message, 'error');
            }
        }

        // ============================================
        // GUARDADO AUTOMÁTICO
        // ============================================
        function startAutoSave() {
            if (autoSaveInterval) {
                clearInterval(autoSaveInterval);
            }
            
            autoSaveInterval = setInterval(() => {
                if (isTracking || positions.length > 0) {
                    saveSessionData();
                    console.log('Guardado automático realizado');
                }
            }, AUTO_SAVE_INTERVAL);
        }

        function stopAutoSave() {
            if (autoSaveInterval) {
                clearInterval(autoSaveInterval);
                autoSaveInterval = null;
            }
        }

        // ============================================
        // FUNCIONES DE MAPA Y GPS
        // ============================================
        function initMap() {
            const defaultLocation = [40.4168, -3.7038];
            map = L.map('map').setView(defaultLocation, 13);
            
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                attribution: '© OpenStreetMap',
                maxZoom: 19
            }).addTo(map);
            
            L.control.zoom({ position: 'topright' }).addTo(map);
            L.control.scale({ position: 'bottomleft' }).addTo(map);
            
            updateGPSStatus('mapa_listo', 'Mapa cargado');
        }

        function requestGPSPermissions() {
            return new Promise((resolve, reject) => {
                if (!navigator.geolocation) {
                    reject(new Error('Tu navegador no soporta geolocalización'));
                    return;
                }
                
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        updateGPSStatus('permisos_concedidos', 'Permisos GPS concedidos');
                        resolve(position);
                    },
                    (error) => {
                        updateGPSStatus('permisos_denegados', 'Permisos no concedidos');
                        reject(new Error('Permisos de ubicación no concedidos'));
                    },
                    { enableHighAccuracy: true, timeout: 10000 }
                );
            });
        }

        function getInitialLocation() {
            return new Promise((resolve, reject) => {
                updateGPSStatus('obteniendo_ubicacion', 'Obteniendo ubicación...');
                
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        handleNewPosition(position, true);
                        resolve(position);
                    },
                    (error) => {
                        updateGPSStatus('error_ubicacion', 'No se pudo obtener ubicación');
                        reject(error);
                    },
                    { enableHighAccuracy: true, timeout: 15000 }
                );
            });
        }

        // ============================================
        // SEGUIMIENTO DE CARRERA CON PERSISTENCIA
        // ============================================
        function startTracking() {
            if (!currentPosition) {
                showToast('Primero obtén tu ubicación actual', 'warning');
                return;
            }
            
            console.log('Iniciando seguimiento con persistencia...');
            
            isTracking = true;
            startTime = new Date();
            totalDistance = 0;
            elevationGain = 0;
            maxSpeed = 0;
            positions = [currentPosition];
            startPosition = { ...currentPosition };
            
            // Iniciar temporizador
            timerInterval = setInterval(updateTimer, 1000);
            
            // Iniciar seguimiento GPS
            watchId = navigator.geolocation.watchPosition(
                (position) => handleNewPosition(position),
                (error) => handleGPSError(error),
                { enableHighAccuracy: true, maximumAge: 0, timeout: 10000 }
            );
            
            // Iniciar guardado automático
            startAutoSave();
            
            // Guardar inmediatamente
            saveSessionData();
            
            // Actualizar UI
            updateUIForTracking();
            
            showToast('¡Carrera iniciada! Los datos se guardan automáticamente.', 'success');
            updateGPSStatus('grabando', 'Grabando con persistencia...');
        }

        function stopTracking() {
            console.log('Deteniendo seguimiento y guardando...');
            
            isTracking = false;
            
            // Detener temporizador
            clearInterval(timerInterval);
            
            // Detener seguimiento GPS
            if (watchId !== null) {
                navigator.geolocation.clearWatch(watchId);
                watchId = null;
            }
            
            // Detener guardado automático
            stopAutoSave();
            
            // Guardar datos finales
            saveSessionData();
            
            // Archivar sesión
            archiveSession();
            
            // Actualizar UI
            updateUIForStopped();
            
            showToast('Carrera finalizada y guardada.', 'success');
            updateGPSStatus('finalizado', 'Sesión guardada');
        }

        function resetTracking() {
            if (isTracking) {
                if (!confirm('¿Estás seguro? Se perderá el progreso no guardado.')) {
                    return;
                }
                stopTracking();
            }
            
            // Limpiar datos persistentes
            clearSessionData();
            
            // Inicializar nueva sesión
            initializeNewSession();
            
            showToast('Todo reiniciado y datos eliminados', 'info');
        }

        function handleNewPosition(position, isInitial = false) {
            const coords = position.coords;
            
            currentPosition = {
                lat: coords.latitude,
                lng: coords.longitude,
                altitude: coords.altitude || 0,
                speed: coords.speed || 0,
                accuracy: coords.accuracy,
                timestamp: new Date(position.timestamp)
            };
            
            if (isInitial) {
                startPosition = { ...currentPosition };
                positions = [currentPosition];
                updateStartPointMarker();
            } else if (isTracking) {
                if (positions.length > 0) {
                    const lastPos = positions[positions.length - 1];
                    const distance = calculateDistance(
                        lastPos.lat, lastPos.lng,
                        currentPosition.lat, currentPosition.lng
                    );
                    totalDistance += distance;
                    
                    if (lastPos.altitude && currentPosition.altitude) {
                        const elevationDiff = currentPosition.altitude - lastPos.altitude;
                        if (elevationDiff > 0) elevationGain += elevationDiff;
                    }
                    
                    if (currentPosition.speed > maxSpeed) {
                        maxSpeed = currentPosition.speed;
                    }
                }
                positions.push(currentPosition);
                
                // Guardar automáticamente cada 10 posiciones
                if (positions.length % 10 === 0) {
                    saveSessionData();
                }
            }
            
            updateMapWithPosition(currentPosition, isInitial);
            updateGPSDisplay(coords);
            updateStatsDisplay();
            updateSessionInfo();
            
            if (!isTracking && !isInitial) {
                document.getElementById('startBtn').disabled = false;
            }
            
            updateGPSStatus('gps_activo', 'GPS activo');
        }

        // ============================================
        // FUNCIONES AUXILIARES
        // ============================================
        function updateUIForTracking() {
            document.getElementById('startBtn').disabled = true;
            document.getElementById('stopBtn').disabled = false;
            document.getElementById('videoBtn').disabled = true;
            document.getElementById('startPoint').style.display = 'flex';
            document.getElementById('sessionState').textContent = 'Activa';
            document.getElementById('sessionState').style.color = '#4CAF50';
        }

        function updateUIForStopped() {
            document.getElementById('startBtn').disabled = false;
            document.getElementById('stopBtn').disabled = true;
            document.getElementById('videoBtn').disabled = positions.length < 10;
            document.getElementById('sessionState').textContent = 'Completada';
            document.getElementById('sessionState').style.color = '#FF6B35';
        }

        function updateUIForReadyState() {
            document.getElementById('startBtn').disabled = false;
            document.getElementById('sessionState').textContent = 'Inactiva';
            document.getElementById('sessionState').style.color = '#6C757D';
        }

        function updateSessionInfo() {
            // Tiempo de inicio
            if (startTime) {
                document.getElementById('sessionStart').textContent = 
                    startTime.toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
            } else {
                document.getElementById('sessionStart').textContent = '--:--';
            }
            
            // Número de posiciones
            document.getElementById('sessionPositions').textContent = positions.length;
            
            // Último guardado
            updateLastSaveDisplay();
        }

        function updateMapWithPosition(position, isInitial = false) {
            const latLng = [position.lat, position.lng];
            map.setView(latLng, 16);
            
            if (!userMarker) {
                userMarker = L.marker(latLng, {
                    icon: L.divIcon({
                        className: 'user-marker',
                        html: '<div style="background: #FF6B35; width: 24px; height: 24px; border-radius: 50%; border: 3px solid white; box-shadow: 0 0 10px rgba(0,0,0,0.5);"></div>',
                        iconSize: [24, 24]
                    })
                }).addTo(map);
                userMarker.bindPopup('Tu ubicación actual').openPopup();
            } else {
                userMarker.setLatLng(latLng);
            }
            
            if (isTracking && positions.length > 1) {
                if (routePolyline) map.removeLayer(routePolyline);
                
                const latLngs = positions.map(p => [p.lat, p.lng]);
                routePolyline = L.polyline(latLngs, {
                    color: '#FF6B35',
                    weight: 4,
                    opacity: 0.8
                }).addTo(map);
            }
            
            if (isInitial) {
                showToast('Ubicación obtenida', 'success');
                updateGPSStatus('listo_para_iniciar', 'Listo para iniciar');
            }
        }

        function updateStartPointMarker() {
            if (startPosition && map) {
                L.marker([startPosition.lat, startPosition.lng], {
                    icon: L.divIcon({
                        className: 'start-marker',
                        html: '<div style="background: #4CAF50; width: 20px; height: 20px; border-radius: 50%; border: 3px solid white; box-shadow: 0 0 10px rgba(76,175,80,0.8);"></div>',
                        iconSize: [20, 20]
                    })
                }).addTo(map).bindPopup('Punto de inicio');
            }
        }

        // ============================================
        // CÁLCULOS Y ESTADÍSTICAS
        // ============================================
        function calculateDistance(lat1, lon1, lat2, lon2) {
            const R = 6371;
            const dLat = (lat2 - lat1) * Math.PI / 180;
            const dLon = (lon2 - lon1) * Math.PI / 180;
            const a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                      Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) * 
                      Math.sin(dLon/2) * Math.sin(dLon/2);
            const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
            return R * c;
        }

        function updateTimer() {
            if (!startTime) return;
            
            const now = new Date();
            const elapsedSeconds = Math.floor((now - startTime) / 1000);
            const hours = Math.floor(elapsedSeconds / 3600);
            const minutes = Math.floor((elapsedSeconds % 3600) / 60);
            const seconds = elapsedSeconds % 60;
            
            document.getElementById('time').textContent = 
                `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
            
            if (totalDistance > 0) {
                const paceSeconds = elapsedSeconds / totalDistance;
                const paceMinutes = Math.floor(paceSeconds / 60);
                const paceSecs = Math.floor(paceSeconds % 60);
                document.getElementById('pace').textContent = 
                    `${paceMinutes}:${paceSecs.toString().padStart(2, '0')}`;
            }
        }

        function updateStatsDisplay() {
            document.getElementById('distance').textContent = totalDistance.toFixed(2);
            document.getElementById('elevation').textContent = `+${Math.round(elevationGain)}`;
            
            const speedKmph = currentPosition ? (currentPosition.speed * 3.6) : 0;
            document.getElementById('speed').textContent = speedKmph.toFixed(1);
            
            if (startTime) {
                const minutes = (new Date() - startTime) / 60000;
                const calories = minutes * 10;
                document.getElementById('calories').textContent = Math.round(calories);
            }
        }

        function updateGPSDisplay(coords) {
            document.getElementById('accuracyText').textContent = 
                `${Math.round(coords.accuracy)} metros`;
        }

        // ============================================
        // MANEJO DE ERRORES
        // ============================================
        function handleGPSError(error) {
            let message = 'Error GPS: ';
            switch(error.code) {
                case error.PERMISSION_DENIED:
                    message += 'Permisos denegados';
                    break;
                case error.POSITION_UNAVAILABLE:
                    message += 'Ubicación no disponible';
                    break;
                case error.TIMEOUT:
                    message += 'Tiempo de espera agotado';
                    break;
                default:
                    message += 'Error desconocido';
                    break;
            }
            showToast(message, 'error');
        }

        // ============================================
        // EVENT LISTENERS
        // ============================================
        function setupEventListeners() {
            // Botones principales
            document.getElementById('startBtn').addEventListener('click', startTracking);
            document.getElementById('stopBtn').addEventListener('click', stopTracking);
            document.getElementById('resetBtn').addEventListener('click', resetTracking);
            
            // Botón de guardado manual
            document.getElementById('saveSessionBtn').addEventListener('click', () => {
                saveSessionData();
                showToast('Sesión guardada manualmente', 'success');
            });
            
            // Botón de centrar mapa
            document.getElementById('centerMapBtn').addEventListener('click', () => {
                if (currentPosition) {
                    map.setView([currentPosition.lat, currentPosition.lng], 16);
                    showToast('Mapa centrado', 'info');
                }
            });
            
            // Guardar antes de cerrar/recargar
            window.addEventListener('beforeunload', (e) => {
                if (isTracking || positions.length > 0) {
                    saveSessionData();
                    
                    // Mensaje de advertencia
                    if (isTracking) {
                        e.preventDefault();
                        e.returnValue = 'Tienes una carrera en curso. ¿Estás seguro de querer salir?';
                    }
                }
            });
            
            // Guardar también cuando la página pierde visibilidad
            document.addEventListener('visibilitychange', () => {
                if (document.hidden && (isTracking || positions.length > 0)) {
                    saveSessionData();
                    console.log('Datos guardados al minimizar la app');
                }
            });
        }

        // ============================================
        // UTILIDADES
        // ============================================
        function updateGPSStatus(status, message) {
            const indicator = document.getElementById('gpsIndicator');
            const statusText = document.getElementById('gpsStatusText');
            const details = document.getElementById('gpsDetails');
            
            statusText.textContent = message;
            
            indicator.classList.remove('active');
            if (status === 'gps_activo' || status === 'grabando' || status === 'listo_para_iniciar') {
                indicator.classList.add('active');
            }
        }

        function showToast(message, type = 'info') {
            const toast = document.getElementById('toast');
            const toastMessage = document.getElementById('toastMessage');
            
            toast.className = 'toast';
            if (type === 'error') toast.classList.add('error');
            if (type === 'warning') toast.classList.add('warning');
            if (type === 'info') toast.classList.add('info');
            
            const icon = toast.querySelector('i');
            icon.className = 
                type === 'success' ? 'fas fa-check-circle' :
                type === 'error' ? 'fas fa-exclamation-circle' :
                type === 'warning' ? 'fas fa-exclamation-triangle' :
                'fas fa-info-circle';
            
            toastMessage.textContent = message;
            toast.style.display = 'flex';
            
            setTimeout(() => {
                toast.style.display = 'none';
            }, 4000);
            
            console.log(`Toast [${type}]: ${message}`);
        }

    </script>
</body>
</html>
