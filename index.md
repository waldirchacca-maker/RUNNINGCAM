<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TrackRun Pro - Seguimiento GPS para Carreras</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <link rel="stylesheet" href="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.css" />
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

        .header-controls {
            display: flex;
            gap: 10px;
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
        }

        .map-type-btn.active {
            background: var(--primary);
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

        .gps-status {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 15px;
            background: rgba(255, 255, 255, 0.05);
            border-radius: 12px;
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

        .gps-info {
            flex: 1;
        }

        .gps-info h3 {
            font-size: 1rem;
            margin-bottom: 4px;
        }

        .gps-info p {
            font-size: 0.85rem;
            color: var(--gray);
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

        .control-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none !important;
        }

        .control-btn:not(:disabled):hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
        }

        /* Video Export */
        .video-export {
            background: rgba(26, 26, 46, 0.8);
            backdrop-filter: blur(10px);
            border-radius: var(--border-radius);
            padding: 25px;
            box-shadow: var(--shadow);
            margin-top: 15px;
        }

        .video-quality-selector {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin: 20px 0;
        }

        .quality-option {
            padding: 15px;
            background: rgba(255, 255, 255, 0.05);
            border-radius: 12px;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s ease;
            border: 2px solid transparent;
        }

        .quality-option:hover {
            background: rgba(255, 255, 255, 0.1);
        }

        .quality-option.active {
            background: rgba(0, 168, 232, 0.2);
            border-color: var(--secondary);
        }

        .quality-option i {
            font-size: 1.5rem;
            margin-bottom: 8px;
            color: var(--secondary);
        }

        .export-btn {
            width: 100%;
            padding: 18px;
            background: linear-gradient(135deg, var(--secondary), #0097CC);
            border: none;
            border-radius: 14px;
            color: white;
            font-size: 1.1rem;
            font-weight: 700;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
            transition: all 0.3s ease;
        }

        .export-btn:hover:not(:disabled) {
            transform: translateY(-3px);
            box-shadow: 0 8px 25px rgba(0, 168, 232, 0.3);
        }

        .export-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        /* Session Info */
        .session-info {
            background: rgba(26, 26, 46, 0.8);
            backdrop-filter: blur(10px);
            border-radius: var(--border-radius);
            padding: 25px;
            box-shadow: var(--shadow);
            margin-top: 20px;
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

        /* Footer */
        .app-footer {
            background: rgba(26, 26, 46, 0.95);
            padding: 20px;
            text-align: center;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
            margin-top: 20px;
        }

        .footer-text {
            color: var(--gray);
            font-size: 0.9rem;
            line-height: 1.5;
        }

        /* Animations */
        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.5; }
            100% { opacity: 1; }
        }

        @keyframes slideIn {
            from { transform: translateY(20px); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }

        /* Responsive Design */
        @media (max-width: 768px) {
            .main-content {
                flex-direction: column;
                padding: 15px;
            }
            
            .stats-container {
                grid-template-columns: repeat(2, 1fr);
            }
            
            .map-type-selector {
                flex-wrap: wrap;
                justify-content: center;
            }
            
            .video-quality-selector {
                grid-template-columns: 1fr;
            }
            
            .app-header {
                padding: 12px 15px;
            }
            
            .stat-value {
                font-size: 1.8rem;
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
            
            .logo-text h1 {
                font-size: 1.2rem;
            }
        }

        /* Modal */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
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
            max-height: 80vh;
            overflow-y: auto;
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

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        /* Loading */
        .loading {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 40px;
        }

        .spinner {
            width: 50px;
            height: 50px;
            border: 4px solid rgba(255, 255, 255, 0.1);
            border-radius: 50%;
            border-top-color: var(--primary);
            animation: spin 1s linear infinite;
            margin-bottom: 20px;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
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
                    <p>Seguimiento GPS profesional para atletas</p>
                </div>
            </div>
            <div class="header-controls">
                <button class="icon-btn" id="settingsBtn" title="Configuración">
                    <i class="fas fa-cog"></i>
                </button>
                <button class="icon-btn" id="fullscreenBtn" title="Pantalla completa">
                    <i class="fas fa-expand"></i>
                </button>
            </div>
        </header>

        <!-- Main Content -->
        <main class="main-content">
            <!-- Map Section -->
            <section class="map-section">
                <div class="map-container">
                    <div id="map"></div>
                </div>
                <div class="map-controls">
                    <div class="map-type-selector">
                        <button class="map-type-btn active" data-type="standard">
                            <i class="fas fa-map"></i> Mapa
                        </button>
                        <button class="map-type-btn" data-type="satellite">
                            <i class="fas fa-satellite"></i> Satélite
                        </button>
                        <button class="map-type-btn" data-type="terrain">
                            <i class="fas fa-mountain"></i> Terreno
                        </button>
                        <button class="map-type-btn" data-type="hybrid">
                            <i class="fas fa-layer-group"></i> Híbrido
                        </button>
                    </div>
                    <button class="icon-btn" id="centerMapBtn" title="Centrar en mi ubicación">
                        <i class="fas fa-location-crosshairs"></i>
                    </button>
                </div>
            </section>

            <!-- Stats and Controls Section -->
            <section class="stats-section">
                <!-- GPS Status -->
                <div class="gps-status">
                    <div class="gps-indicator" id="gpsIndicator"></div>
                    <div class="gps-info">
                        <h3 id="gpsStatusText">Buscando señal GPS...</h3>
                        <p id="gpsAccuracy">Precisión: -- metros</p>
                    </div>
                    <div class="gps-data">
                        <div class="detail-item">
                            <div class="detail-label">Altitud inicial</div>
                            <div class="detail-value" id="initialAltitude">-- m</div>
                        </div>
                    </div>
                </div>

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
                            <i class="fas fa-stop-circle"></i> DETENER
                        </button>
                        <button class="control-btn reset" id="resetBtn">
                            <i class="fas fa-redo"></i> REINICIAR
                        </button>
                    </div>
                </div>

                <!-- Video Export -->
                <div class="video-export">
                    <h3><i class="fas fa-video"></i> Exportar Recorrido en Video</h3>
                    <p>Exporta tu entrenamiento como video profesional compatible con todos los dispositivos</p>
                    
                    <div class="video-quality-selector">
                        <div class="quality-option active" data-quality="4k">
                            <i class="fas fa-film"></i>
                            <h4>4K Ultra HD</h4>
                            <p>Máxima calidad (3840x2160)</p>
                        </div>
                        <div class="quality-option" data-quality="1080p">
                            <i class="fas fa-hd"></i>
                            <h4>Full HD</h4>
                            <p>Calidad premium (1920x1080)</p>
                        </div>
                        <div class="quality-option" data-quality="720p">
                            <i class="fas fa-mobile-alt"></i>
                            <h4>HD Móvil</h4>
                            <p>Optimizado para móvil (1280x720)</p>
                        </div>
                    </div>

                    <div class="orientation-selector" style="margin: 15px 0;">
                        <div style="display: flex; gap: 10px; justify-content: center;">
                            <button class="map-type-btn active" id="landscapeBtn">
                                <i class="fas fa-desktop"></i> Horizontal
                            </button>
                            <button class="map-type-btn" id="portraitBtn">
                                <i class="fas fa-mobile-alt"></i> Vertical
                            </button>
                        </div>
                    </div>

                    <button class="export-btn" id="exportBtn" disabled>
                        <i class="fas fa-download"></i> GENERAR Y DESCARGAR VIDEO
                    </button>
                </div>
            </section>
        </main>

        <!-- Session Info -->
        <section class="session-info">
            <h3><i class="fas fa-chart-line"></i> Resumen de Sesión</h3>
            <div class="session-details">
                <div class="detail-item">
                    <div class="detail-label">Velocidad Máxima</div>
                    <div class="detail-value" id="maxSpeed">0.0 km/h</div>
                </div>
                <div class="detail-item">
                    <div class="detail-label">Ritmo Promedio</div>
                    <div class="detail-value" id="avgPace">--:-- min/km</div>
                </div>
                <div class="detail-item">
                    <div class="detail-label">Desnivel Total</div>
                    <div class="detail-value" id="totalElevation">+0 m / -0 m</div>
                </div>
                <div class="detail-item">
                    <div class="detail-label">Calorías/min</div>
                    <div class="detail-value" id="caloriesPerMin">0.0</div>
                </div>
            </div>
        </section>

        <!-- Footer -->
        <footer class="app-footer">
            <div class="footer-text">
                <p>TrackRun Pro v2.0 | GPS en tiempo real con análisis avanzado | Los datos se procesan localmente en tu dispositivo</p>
                <p style="margin-top: 8px; font-size: 0.8rem; color: #555;">
                    <i class="fas fa-shield-alt"></i> Privacidad garantizada - Sin envío de datos a servidores externos
                </p>
            </div>
        </footer>
    </div>

    <!-- Modal for Video Preview -->
    <div class="modal" id="videoModal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">Vista Previa del Video</h3>
                <button class="close-modal" id="closeModal">&times;</button>
            </div>
            <div id="videoPreview">
                <!-- Video preview will be inserted here -->
            </div>
            <div style="text-align: center; margin-top: 20px;">
                <button class="control-btn start" id="downloadVideoBtn">
                    <i class="fas fa-download"></i> DESCARGAR VIDEO
                </button>
            </div>
        </div>
    </div>

    <!-- Toast Notifications -->
    <div class="toast" id="toast" style="display: none;">
        <i class="fas fa-check-circle"></i>
        <span id="toastMessage">Operación completada</span>
    </div>

    <!-- Leaflet and other libraries -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/leaflet-elevation@1.0.0/dist/leaflet-elevation.min.js"></script>
    <script>
        // Variables globales
        let watchId = null;
        let isTracking = false;
        let startTime = null;
        let timerInterval = null;
        let currentRoute = [];
        let totalDistance = 0;
        let totalElevationGain = 0;
        let totalElevationLoss = 0;
        let lastPosition = null;
        let map = null;
        let mapPolyline = null;
        let currentMapLayer = 'standard';
        let initialAltitude = null;
        let maxSpeed = 0;
        let startCoords = null;
        let videoOrientation = 'landscape'; // 'landscape' or 'portrait'
        
        // Configuración de mapas
        const mapLayers = {
            standard: L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                attribution: '© OpenStreetMap'
            }),
            satellite: L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
                attribution: '© Esri'
            }),
            terrain: L.tileLayer('https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png', {
                attribution: '© OpenTopoMap'
            }),
            hybrid: L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
                attribution: '© Esri'
            })
        };

        // Inicializar aplicación
        document.addEventListener('DOMContentLoaded', () => {
            initializeApp();
            setupEventListeners();
            checkGPSPermissions();
        });

        // Inicializar aplicación
        function initializeApp() {
            // Inicializar mapa
            initMap();
            
            // Configurar controles de mapa
            setupMapControls();
            
            // Mostrar estado inicial
            updateGPSStatus(false);
            
            // Inicializar calidad de video
            initVideoQuality();
            
            // Mostrar mensaje de bienvenida
            showToast('TrackRun Pro listo. Conectando con GPS...', 'info');
        }

        // Inicializar mapa
        function initMap() {
            // Usar ubicación por defecto (Madrid) hasta que obtengamos GPS
            const defaultLocation = [40.4168, -3.7038];
            
            // Crear mapa
            map = L.map('map', {
                center: defaultLocation,
                zoom: 13,
                zoomControl: false,
                preferCanvas: true
            });

            // Añadir capa inicial
            mapLayers.standard.addTo(map);
            currentMapLayer = 'standard';

            // Añadir controles
            L.control.zoom({ position: 'topright' }).addTo(map);
            L.control.scale({ position: 'bottomleft' }).addTo(map);

            // Añadir geocoder
            L.Control.geocoder({
                defaultMarkGeocode: false,
                position: 'topleft'
            }).on('markgeocode', function(e) {
                const bbox = e.geocode.bbox;
                map.fitBounds(bbox);
            }).addTo(map);
        }

        // Configurar controles de mapa
        function setupMapControls() {
            // Botones de tipo de mapa
            document.querySelectorAll('.map-type-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    const type = this.dataset.type;
                    
                    // Remover clase active de todos los botones
                    document.querySelectorAll('.map-type-btn').forEach(b => b.classList.remove('active'));
                    // Añadir clase active al botón clickeado
                    this.classList.add('active');
                    
                    // Cambiar capa del mapa
                    changeMapLayer(type);
                });
            });

            // Botón para centrar mapa
            document.getElementById('centerMapBtn').addEventListener('click', centerMapOnUser);
        }

        // Cambiar capa del mapa
        function changeMapLayer(type) {
            // Remover capa actual
            map.eachLayer(layer => {
                if (layer instanceof L.TileLayer) {
                    map.removeLayer(layer);
                }
            });

            // Añadir nueva capa
            mapLayers[type].addTo(map);
            
            // Si es híbrido, añadir capa de etiquetas
            if (type === 'hybrid') {
                L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                    attribution: '© OpenStreetMap',
                    opacity: 0.5
                }).addTo(map);
            }
            
            currentMapLayer = type;
        }

        // Centrar mapa en el usuario
        function centerMapOnUser() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(position => {
                    const lat = position.coords.latitude;
                    const lng = position.coords.longitude;
                    
                    map.setView([lat, lng], 15);
                    showToast('Mapa centrado en tu ubicación', 'success');
                }, error => {
                    showToast('No se pudo obtener la ubicación', 'error');
                });
            }
        }

        // Configurar event listeners
        function setupEventListeners() {
            // Botones de control
            document.getElementById('startBtn').addEventListener('click', startTracking);
            document.getElementById('stopBtn').addEventListener('click', stopTracking);
            document.getElementById('resetBtn').addEventListener('click', resetTracking);
            
            // Botón de exportación
            document.getElementById('exportBtn').addEventListener('click', generateVideo);
            
            // Botones de orientación de video
            document.getElementById('landscapeBtn').addEventListener('click', function() {
                videoOrientation = 'landscape';
                this.classList.add('active');
                document.getElementById('portraitBtn').classList.remove('active');
            });
            
            document.getElementById('portraitBtn').addEventListener('click', function() {
                videoOrientation = 'portrait';
                this.classList.add('active');
                document.getElementById('landscapeBtn').classList.remove('active');
            });
            
            // Botones de configuración
            document.getElementById('fullscreenBtn').addEventListener('click', toggleFullscreen);
            document.getElementById('settingsBtn').addEventListener('click', showSettings);
            
            // Modal
            document.getElementById('closeModal').addEventListener('click', () => {
                document.getElementById('videoModal').style.display = 'none';
            });
        }

        // Inicializar calidad de video
        function initVideoQuality() {
            document.querySelectorAll('.quality-option').forEach(option => {
                option.addEventListener('click', function() {
                    document.querySelectorAll('.quality-option').forEach(opt => opt.classList.remove('active'));
                    this.classList.add('active');
                });
            });
        }

        // Verificar permisos GPS
        function checkGPSPermissions() {
            if (navigator.permissions && navigator.permissions.query) {
                navigator.permissions.query({ name: 'geolocation' }).then(result => {
                    if (result.state === 'granted') {
                        updateGPSStatus(true);
                        centerMapOnUser();
                    } else if (result.state === 'prompt') {
                        updateGPSStatus(false);
                    }
                });
            }
        }

        // Actualizar estado GPS
        function updateGPSStatus(connected) {
            const indicator = document.getElementById('gpsIndicator');
            const statusText = document.getElementById('gpsStatusText');
            
            if (connected) {
                indicator.classList.add('active');
                indicator.style.animation = 'pulse 2s infinite';
                statusText.textContent = 'GPS Conectado';
                statusText.style.color = '#4CAF50';
            } else {
                indicator.classList.remove('active');
                indicator.style.animation = 'pulse 2s infinite';
                statusText.textContent = 'Buscando señal GPS...';
                statusText.style.color = '#FF9800';
            }
        }

        // Iniciar seguimiento
        function startTracking() {
            if (!navigator.geolocation) {
                showToast('Tu navegador no soporta geolocalización', 'error');
                return;
            }

            // Resetear datos anteriores
            resetTrackingData();
            
            isTracking = true;
            startTime = new Date();
            
            // Actualizar UI
            document.getElementById('startBtn').disabled = true;
            document.getElementById('stopBtn').disabled = false;
            document.getElementById('exportBtn').disabled = true;
            
            // Iniciar temporizador
            updateTimer();
            timerInterval = setInterval(updateTimer, 1000);
            
            // Iniciar seguimiento GPS
            watchId = navigator.geolocation.watchPosition(
                position => handleNewPosition(position),
                error => handleGPSError(error),
                {
                    enableHighAccuracy: true,
                    maximumAge: 0,
                    timeout: 10000
                }
            );
            
            showToast('Carrera iniciada - Grabando recorrido', 'success');
        }

        // Manejar nueva posición GPS
        function handleNewPosition(position) {
            updateGPSStatus(true);
            
            const lat = position.coords.latitude;
            const lng = position.coords.longitude;
            const altitude = position.coords.altitude;
            const speed = position.coords.speed;
            const accuracy = position.coords.accuracy;
            
            // Actualizar precisión GPS
            document.getElementById('gpsAccuracy').textContent = `Precisión: ${Math.round(accuracy)} metros`;
            
            // Guardar altitud inicial si es la primera posición
            if (!initialAltitude && altitude) {
                initialAltitude = altitude;
                document.getElementById('initialAltitude').textContent = `${Math.round(altitude)} m`;
            }
            
            // Guardar coordenadas iniciales
            if (!startCoords) {
                startCoords = { lat, lng };
            }
            
            // Crear objeto de posición
            const newPosition = {
                lat,
                lng,
                altitude: altitude || 0,
                speed: speed || 0,
                accuracy,
                timestamp: new Date(position.timestamp)
            };
            
            // Añadir a la ruta
            currentRoute.push(newPosition);
            
            // Calcular estadísticas si tenemos posición anterior
            if (lastPosition) {
                calculateRouteStats(newPosition);
            }
            
            // Actualizar última posición
            lastPosition = newPosition;
            
            // Actualizar UI
            updateStatsDisplay();
            updateMap(newPosition);
            
            // Actualizar velocidad máxima
            if (speed > maxSpeed) {
                maxSpeed = speed;
            }
        }

        // Calcular estadísticas de ruta
        function calculateRouteStats(newPosition) {
            // Calcular distancia
            const distance = calculateDistance(
                lastPosition.lat, lastPosition.lng,
                newPosition.lat, newPosition.lng
            );
            
            totalDistance += distance;
            
            // Calcular desnivel
            if (lastPosition.altitude && newPosition.altitude) {
                const elevationDiff = newPosition.altitude - lastPosition.altitude;
                if (elevationDiff > 0) {
                    totalElevationGain += elevationDiff;
                } else {
                    totalElevationLoss += Math.abs(elevationDiff);
                }
            }
        }

        // Calcular distancia (Fórmula de Haversine)
        function calculateDistance(lat1, lon1, lat2, lon2) {
            const R = 6371; // Radio de la Tierra en km
            const dLat = (lat2 - lat1) * Math.PI / 180;
            const dLon = (lon2 - lon1) * Math.PI / 180;
            const a = 
                Math.sin(dLat/2) * Math.sin(dLat/2) +
                Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) * 
                Math.sin(dLon/2) * Math.sin(dLon/2);
            const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
            return R * c;
        }

        // Actualizar mapa
        function updateMap(position) {
            const lat = position.lat;
            const lng = position.lng;
            
            // Centrar mapa en posición actual
            map.setView([lat, lng], map.getZoom());
            
            // Actualizar ruta en el mapa
            if (currentRoute.length > 1) {
                const latLngs = currentRoute.map(p => [p.lat, p.lng]);
                
                if (mapPolyline) {
                    map.removeLayer(mapPolyline);
                }
                
                mapPolyline = L.polyline(latLngs, {
                    color: '#FF6B35',
                    weight: 4,
                    opacity: 0.8,
                    lineJoin: 'round',
                    lineCap: 'round'
                }).addTo(map);
            }
            
            // Añadir marcador de posición actual
            if (map.getPane('currentPosition')) {
                map.removeLayer(map.getPane('currentPosition'));
            }
            
            L.circleMarker([lat, lng], {
                radius: 8,
                fillColor: '#FF6B35',
                color: '#FFFFFF',
                weight: 3,
                opacity: 1,
                fillOpacity: 0.8
            }).addTo(map).bindTooltip('Tu posición actual', {permanent: false, direction: 'top'});
        }

        // Actualizar temporizador
        function updateTimer() {
            if (!startTime) return;
            
            const now = new Date();
            const elapsedSeconds = Math.floor((now - startTime) / 1000);
            const hours = Math.floor(elapsedSeconds / 3600);
            const minutes = Math.floor((elapsedSeconds % 3600) / 60);
            const seconds = elapsedSeconds % 60;
            
            document.getElementById('time').textContent = 
                `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
        }

        // Actualizar estadísticas en pantalla
        function updateStatsDisplay() {
            // Distancia
            document.getElementById('distance').textContent = totalDistance.toFixed(2);
            
            // Tiempo ya se actualiza en updateTimer()
            
            // Ritmo (Pace) - min/km
            if (totalDistance > 0) {
                const elapsedSeconds = Math.floor((new Date() - startTime) / 1000);
                const paceSeconds = elapsedSeconds / totalDistance;
                const paceMinutes = Math.floor(paceSeconds / 60);
                const paceSecs = Math.floor(paceSeconds % 60);
                document.getElementById('pace').textContent = 
                    `${paceMinutes}:${paceSecs.toString().padStart(2, '0')}`;
                
                // Actualizar ritmo promedio
                document.getElementById('avgPace').textContent = 
                    `${paceMinutes}:${paceSecs.toString().padStart(2, '0')} min/km`;
            }
            
            // Desnivel
            document.getElementById('elevation').textContent = `+${Math.round(totalElevationGain)}`;
            document.getElementById('totalElevation').textContent = 
                `+${Math.round(totalElevationGain)} m / -${Math.round(totalElevationLoss)} m`;
            
            // Velocidad
            const currentSpeed = lastPosition ? (lastPosition.speed * 3.6) : 0; // m/s a km/h
            document.getElementById('speed').textContent = currentSpeed.toFixed(1);
            
            // Velocidad máxima
            document.getElementById('maxSpeed').textContent = `${(maxSpeed * 3.6).toFixed(1)} km/h`;
            
            // Calorías (fórmula simplificada)
            const elapsedMinutes = Math.floor((new Date() - startTime) / 60000);
            const calories = elapsedMinutes * 10; // 10 kcal por minuto aproximado
            document.getElementById('calories').textContent = Math.round(calories);
            document.getElementById('caloriesPerMin').textContent = 
                elapsedMinutes > 0 ? (calories / elapsedMinutes).toFixed(1) : '0.0';
        }

        // Manejar error GPS
        function handleGPSError(error) {
            updateGPSStatus(false);
            
            switch(error.code) {
                case error.PERMISSION_DENIED:
                    showToast('Permiso de ubicación denegado', 'error');
                    break;
                case error.POSITION_UNAVAILABLE:
                    showToast('Señal GPS no disponible', 'error');
                    break;
                case error.TIMEOUT:
                    showToast('Tiempo de espera agotado', 'error');
                    break;
                default:
                    showToast('Error desconocido en GPS', 'error');
                    break;
            }
        }

        // Detener seguimiento
        function stopTracking() {
            isTracking = false;
            
            // Detener temporizador
            clearInterval(timerInterval);
            
            // Detener seguimiento GPS
            if (watchId !== null) {
                navigator.geolocation.clearWatch(watchId);
                watchId = null;
            }
            
            // Actualizar UI
            document.getElementById('startBtn').disabled = false;
            document.getElementById('stopBtn').disabled = true;
            document.getElementById('exportBtn').disabled = false;
            
            showToast('Carrera finalizada - Listo para exportar', 'success');
        }

        // Reiniciar datos de seguimiento
        function resetTrackingData() {
            totalDistance = 0;
            totalElevationGain = 0;
            totalElevationLoss = 0;
            maxSpeed = 0;
            initialAltitude = null;
            startCoords = null;
            currentRoute = [];
            lastPosition = null;
            
            if (mapPolyline) {
                map.removeLayer(mapPolyline);
                mapPolyline = null;
            }
            
            // Resetear UI
            document.getElementById('distance').textContent = '0.00';
            document.getElementById('pace').textContent = '--:--';
            document.getElementById('time').textContent = '00:00:00';
            document.getElementById('elevation').textContent = '+0';
            document.getElementById('speed').textContent = '0.0';
            document.getElementById('calories').textContent = '0';
            document.getElementById('maxSpeed').textContent = '0.0 km/h';
            document.getElementById('avgPace').textContent = '--:-- min/km';
            document.getElementById('totalElevation').textContent = '+0 m / -0 m';
            document.getElementById('caloriesPerMin').textContent = '0.0';
            document.getElementById('initialAltitude').textContent = '-- m';
        }

        // Reiniciar completamente
        function resetTracking() {
            if (isTracking) {
                stopTracking();
            }
            
            resetTrackingData();
            showToast('Datos reiniciados', 'info');
        }

        // Generar video del recorrido
        function generateVideo() {
            if (currentRoute.length < 2) {
                showToast('No hay suficiente recorrido para generar video', 'error');
                return;
            }

            // Obtener calidad seleccionada
            const qualityOption = document.querySelector('.quality-option.active');
            const quality = qualityOption.dataset.quality;
            
            // Mostrar modal de carga
            const exportBtn = document.getElementById('exportBtn');
            exportBtn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> GENERANDO VIDEO...';
            exportBtn.disabled = true;
            
            // Simular generación de video
            setTimeout(() => {
                // Crear vista previa del video
                const videoPreview = document.getElementById('videoPreview');
                videoPreview.innerHTML = `
                    <div style="background: #2D3047; padding: 20px; border-radius: 12px; text-align: center;">
                        <div style="background: #1A1A2E; padding: 15px; border-radius: 8px; margin-bottom: 15px;">
                            <h4 style="color: #FF6B35; margin-bottom: 10px;">Vista previa del video</h4>
                            <div style="background: #000; height: ${videoOrientation === 'landscape' ? '200px' : '350px'}; 
                                 border-radius: 8px; display: flex; align-items: center; justify-content: center;">
                                <i class="fas fa-play-circle" style="font-size: 3rem; color: #FF6B35;"></i>
                            </div>
                        </div>
                        <div style="text-align: left;">
                            <p><i class="fas fa-info-circle" style="color: #00A8E8;"></i> 
                            <strong>Calidad:</strong> ${quality.toUpperCase()}</p>
                            <p><i class="fas fa-video" style="color: #00A8E8;"></i> 
                            <strong>Resolución:</strong> ${getVideoResolution(quality, videoOrientation)}</p>
                            <p><i class="fas fa-clock" style="color: #00A8E8;"></i> 
                            <strong>Duración:</strong> ${document.getElementById('time').textContent}</p>
                            <p><i class="fas fa-compress" style="color: #00A8E8;"></i> 
                            <strong>Tamaño estimado:</strong> ${getFileSize(quality)}</p>
                            <p><i class="fas fa-check-circle" style="color: #4CAF50;"></i> 
                            <strong>Compatible con:</strong> Todos los dispositivos</p>
                        </div>
                    </div>
                `;
                
                // Mostrar modal
                document.getElementById('videoModal').style.display = 'flex';
                
                // Restaurar botón de exportación
                exportBtn.innerHTML = '<i class="fas fa-download"></i> GENERAR Y DESCARGAR VIDEO';
                exportBtn.disabled = false;
                
                // Configurar botón de descarga
                document.getElementById('downloadVideoBtn').onclick = downloadVideo;
                
                showToast('Video generado exitosamente', 'success');
            }, 2000);
        }

        // Obtener resolución de video
        function getVideoResolution(quality, orientation) {
            const resolutions = {
                '4k': { landscape: '3840x2160', portrait: '2160x3840' },
                '1080p': { landscape: '1920x1080', portrait: '1080x1920' },
                '720p': { landscape: '1280x720', portrait: '720x1280' }
            };
            return resolutions[quality][orientation];
        }

        // Obtener tamaño de archivo estimado
        function getFileSize(quality) {
            const sizes = {
                '4k': '150-300 MB',
                '1080p': '50-100 MB',
                '720p': '20-40 MB'
            };
            return sizes[quality];
        }

        // Descargar video
        function downloadVideo() {
            const qualityOption = document.querySelector('.quality-option.active');
            const quality = qualityOption.dataset.quality;
            
            // Crear nombre de archivo
            const date = new Date().toISOString().slice(0, 19).replace(/[:]/g, '-');
            const filename = `TrackRun_${date}_${quality}_${videoOrientation}.mp4`;
            
            // Simular descarga
            showToast(`Descargando video ${quality.toUpperCase()}...`, 'info');
            
            // Simular descarga completa
            setTimeout(() => {
                document.getElementById('videoModal').style.display = 'none';
                showToast('Video descargado exitosamente', 'success');
                
                // Aquí en una implementación real se usaría una librería como:
                // - html2canvas + ffmpeg.js para capturar el mapa y generar video
                // - MediaRecorder API para grabar en tiempo real
                // - Una combinación de Canvas API y WebGL para renderizado avanzado
            }, 1500);
        }

        // Alternar pantalla completa
        function toggleFullscreen() {
            if (!document.fullscreenElement) {
                document.documentElement.requestFullscreen().catch(err => {
                    showToast(`Error al activar pantalla completa: ${err.message}`, 'error');
                });
            } else {
                if (document.exitFullscreen) {
                    document.exitFullscreen();
                }
            }
        }

        // Mostrar configuración
        function showSettings() {
            showToast('Configuración - En desarrollo', 'info');
        }

        // Mostrar notificación toast
        function showToast(message, type = 'info') {
            const toast = document.getElementById('toast');
            const toastMessage = document.getElementById('toastMessage');
            
            // Configurar tipo
            toast.className = 'toast';
            if (type === 'error') toast.classList.add('error');
            if (type === 'warning') toast.classList.add('warning');
            
            // Configurar icono
            const icon = toast.querySelector('i');
            if (type === 'success') icon.className = 'fas fa-check-circle';
            if (type === 'error') icon.className = 'fas fa-exclamation-circle';
            if (type === 'warning') icon.className = 'fas fa-exclamation-triangle';
            if (type === 'info') icon.className = 'fas fa-info-circle';
            
            // Configurar mensaje
            toastMessage.textContent = message;
            
            // Mostrar
            toast.style.display = 'flex';
            
            // Ocultar después de 3 segundos
            setTimeout(() => {
                toast.style.display = 'none';
            }, 3000);
        }

        // Manejar cambios en la orientación
        window.addEventListener('resize', function() {
            if (map) {
                map.invalidateSize();
            }
        });
    </script>
</body>
</html>
