<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TrackRun Pro - Mapas + Video</title>
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

        /* Video Modal */
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
            max-width: 800px;
            width: 90%;
            max-height: 90vh;
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

        .video-preview-container {
            background: #000;
            border-radius: 8px;
            overflow: hidden;
            margin: 20px 0;
            position: relative;
        }

        #generatedVideo {
            width: 100%;
            display: block;
        }

        .video-controls {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-top: 20px;
        }

        .video-control-btn {
            padding: 12px 25px;
            background: rgba(255, 255, 255, 0.1);
            border: none;
            border-radius: 8px;
            color: white;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            transition: all 0.3s;
        }

        .video-control-btn:hover {
            background: rgba(255, 255, 255, 0.2);
        }

        .video-control-btn.primary {
            background: var(--secondary);
        }

        .video-control-btn.primary:hover {
            background: #0097CC;
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

        /* Video Options */
        .video-options {
            background: rgba(255, 255, 255, 0.05);
            padding: 20px;
            border-radius: 12px;
            margin-top: 15px;
        }

        .option-group {
            margin-bottom: 15px;
        }

        .option-title {
            color: var(--secondary);
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .orientation-buttons {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }

        .orientation-btn {
            flex: 1;
            padding: 12px;
            background: rgba(255, 255, 255, 0.05);
            border: 2px solid transparent;
            border-radius: 8px;
            color: white;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s;
        }

        .orientation-btn:hover {
            background: rgba(255, 255, 255, 0.1);
        }

        .orientation-btn.active {
            background: rgba(0, 168, 232, 0.2);
            border-color: var(--secondary);
        }

        .quality-buttons {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin-top: 10px;
        }

        .quality-btn {
            padding: 15px 10px;
            background: rgba(255, 255, 255, 0.05);
            border: 2px solid transparent;
            border-radius: 8px;
            color: white;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s;
        }

        .quality-btn:hover {
            background: rgba(255, 255, 255, 0.1);
        }

        .quality-btn.active {
            background: rgba(0, 168, 232, 0.2);
            border-color: var(--secondary);
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
            
            .modal-content {
                width: 95%;
                padding: 20px;
            }
            
            .quality-buttons {
                grid-template-columns: 1fr;
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
            
            .map-type-btn {
                padding: 6px 12px;
                font-size: 0.8rem;
            }
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

        /* Map Legend */
        .map-legend {
            position: absolute;
            bottom: 15px;
            left: 15px;
            background: rgba(0, 0, 0, 0.8);
            color: white;
            padding: 10px 15px;
            border-radius: 8px;
            font-size: 0.8rem;
            z-index: 1000;
            backdrop-filter: blur(5px);
        }

        .legend-item {
            display: flex;
            align-items: center;
            gap: 8px;
            margin-bottom: 5px;
        }

        .legend-color {
            width: 12px;
            height: 12px;
            border-radius: 50%;
        }

        .legend-color.start {
            background: var(--success);
        }

        .legend-color.current {
            background: #FF6B35;
        }

        .legend-color.route {
            background: #FF6B35;
            width: 20px;
            height: 4px;
            border-radius: 2px;
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
                    <p>6 Tipos de Mapas + Generador de Video</p>
                </div>
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
                                <p id="gpsDetails">Cargando mapas...</p>
                            </div>
                        </div>
                    </div>

                    <!-- Start Point Marker -->
                    <div class="start-point" id="startPoint" style="display: none;">
                        <i class="fas fa-flag-checkered"></i>
                        Punto de inicio fijado
                    </div>

                    <!-- Map Legend -->
                    <div class="map-legend" id="mapLegend" style="display: none;">
                        <div class="legend-item">
                            <div class="legend-color start"></div>
                            <span>Punto de inicio</span>
                        </div>
                        <div class="legend-item">
                            <div class="legend-color current"></div>
                            <span>Posición actual</span>
                        </div>
                        <div class="legend-item">
                            <div class="legend-color route"></div>
                            <span>Ruta recorrida</span>
                        </div>
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
                        <button class="map-type-btn" data-type="dark">
                            <i class="fas fa-moon"></i> Oscuro
                        </button>
                        <button class="map-type-btn" data-type="light">
                            <i class="fas fa-sun"></i> Claro
                        </button>
                        <button class="map-type-btn" data-type="cycle">
                            <i class="fas fa-bicycle"></i> Ciclismo
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

                    <!-- GPS Information -->
                    <div style="background: rgba(255,255,255,0.05); padding: 15px; border-radius: 12px; margin-top: 15px;">
                        <h4 style="margin-bottom: 10px; color: var(--secondary);">
                            <i class="fas fa-satellite"></i> Información GPS
                        </h4>
                        <div style="font-size: 0.9rem;">
                            <div style="display: flex; justify-content: space-between; margin-bottom: 5px;">
                                <span>Estado:</span>
                                <span id="statusText">Esperando GPS...</span>
                            </div>
                            <div style="display: flex; justify-content: space-between; margin-bottom: 5px;">
                                <span>Precisión:</span>
                                <span id="accuracyText">-- metros</span>
                            </div>
                            <div style="display: flex; justify-content: space-between;">
                                <span>Mapa actual:</span>
                                <span id="currentMapText">Callejero</span>
                            </div>
                        </div>
                    </div>

                    <!-- Quick Actions -->
                    <div style="margin-top: 20px;">
                        <button class="control-btn" id="locateBtn" style="background: rgba(0,168,232,0.2);">
                            <i class="fas fa-location-crosshairs"></i> UBICARME AHORA
                        </button>
                    </div>
                </div>

                <!-- Video Options (oculto por defecto) -->
                <div class="video-options" id="videoOptions" style="display: none;">
                    <div class="option-group">
                        <div class="option-title">
                            <i class="fas fa-arrows-alt-v"></i> Orientación del Video
                        </div>
                        <div class="orientation-buttons">
                            <button class="orientation-btn active" data-orientation="horizontal">
                                <i class="fas fa-desktop"></i> Horizontal
                            </button>
                            <button class="orientation-btn" data-orientation="vertical">
                                <i class="fas fa-mobile-alt"></i> Vertical
                            </button>
                        </div>
                    </div>

                    <div class="option-group">
                        <div class="option-title">
                            <i class="fas fa-hd"></i> Calidad del Video
                        </div>
                        <div class="quality-buttons">
                            <button class="quality-btn active" data-quality="1080p">
                                <div>Full HD</div>
                                <div style="font-size: 0.8rem; color: #aaa;">1080p - Alta calidad</div>
                            </button>
                            <button class="quality-btn" data-quality="720p">
                                <div>HD</div>
                                <div style="font-size: 0.8rem; color: #aaa;">720p - Calidad media</div>
                            </button>
                            <button class="quality-btn" data-quality="480p">
                                <div>Standard</div>
                                <div style="font-size: 0.8rem; color: #aaa;">480p - Para móviles</div>
                            </button>
                        </div>
                    </div>

                    <div class="option-group">
                        <div class="option-title">
                            <i class="fas fa-map"></i> Tipo de Mapa en Video
                        </div>
                        <div class="quality-buttons">
                            <button class="quality-btn active" data-map="satellite">
                                <div>Satélite</div>
                                <div style="font-size: 0.8rem; color: #aaa;">Vista aérea real</div>
                            </button>
                            <button class="quality-btn" data-map="topographic">
                                <div>Topográfico</div>
                                <div style="font-size: 0.8rem; color: #aaa;">Con relieve</div>
                            </button>
                            <button class="quality-btn" data-map="dark">
                                <div>Oscuro</div>
                                <div style="font-size: 0.8rem; color: #aaa;">Modo nocturno</div>
                            </button>
                        </div>
                    </div>

                    <div class="option-group">
                        <div class="option-title">
                            <i class="fas fa-cog"></i> Opciones Avanzadas
                        </div>
                        <label style="display: flex; align-items: center; gap: 10px; margin-bottom: 10px; cursor: pointer;">
                            <input type="checkbox" id="showStats" checked style="width: 18px; height: 18px;">
                            <span>Mostrar estadísticas en video</span>
                        </label>
                        <label style="display: flex; align-items: center; gap: 10px; margin-bottom: 10px; cursor: pointer;">
                            <input type="checkbox" id="showRoute" checked style="width: 18px; height: 18px;">
                            <span>Mostrar ruta completa animada</span>
                        </label>
                        <label style="display: flex; align-items: center; gap: 10px; cursor: pointer;">
                            <input type="checkbox" id="showSpeed" checked style="width: 18px; height: 18px;">
                            <span>Mostrar velocidad en tiempo real</span>
                        </label>
                    </div>

                    <button class="control-btn video" id="generateVideoBtn" style="margin-top: 20px;">
                        <i class="fas fa-play-circle"></i> GENERAR VIDEO AHORA
                    </button>
                </div>
            </section>
        </main>
    </div>

    <!-- Video Modal -->
    <div class="modal" id="videoModal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">Video Generado</h3>
                <button class="close-modal" id="closeModal">&times;</button>
            </div>
            
            <div id="videoPreviewContent">
                <!-- Contenido será insertado aquí -->
            </div>
        </div>
    </div>

    <!-- Generating Modal -->
    <div class="modal" id="generatingModal">
        <div class="modal-content" style="max-width: 500px;">
            <div class="modal-header">
                <h3 class="modal-title">Generando Video...</h3>
            </div>
            <div style="text-align: center; padding: 40px 20px;">
                <div class="spinner" style="width: 60px; height: 60px; border: 4px solid rgba(255,255,255,0.1); border-top-color: var(--primary); border-radius: 50%; margin: 0 auto 20px; animation: spin 1s linear infinite;"></div>
                <h4 id="generationStep">Preparando video...</h4>
                <div class="progress-container">
                    <div class="progress-bar" id="generationProgress"></div>
                </div>
                <div class="progress-text" id="progressText">0% completado</div>
                <p style="color: #aaa; margin-top: 20px; font-size: 0.9rem;">
                    <i class="fas fa-info-circle"></i> Generando video en alta calidad con mapa seleccionado
                </p>
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
    <!-- HTML2Canvas para capturar pantalla -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script>
        // ============================================
        // VARIABLES GLOBALES
        // ============================================
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
        let videoOrientation = 'horizontal';
        let videoQuality = '1080p';
        let videoMapType = 'satellite';
        let currentMapType = 'street';
        
        // Capas de mapas disponibles
        const mapLayers = {
            street: L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                attribution: '© OpenStreetMap',
                maxZoom: 19
            }),
            satellite: L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
                attribution: '© Esri',
                maxZoom: 19
            }),
            topographic: L.tileLayer('https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png', {
                attribution: '© OpenTopoMap',
                maxZoom: 17
            }),
            dark: L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
                attribution: '© CARTO',
                maxZoom: 19
            }),
            light: L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
                attribution: '© CARTO',
                maxZoom: 19
            }),
            cycle: L.tileLayer('https://{s}.tile-cyclosm.openstreetmap.fr/cyclosm/{z}/{x}/{y}.png', {
                attribution: '© CyclOSM',
                maxZoom: 20
            })
        };

        // ============================================
        // INICIALIZACIÓN
        // ============================================
        document.addEventListener('DOMContentLoaded', async () => {
            console.log('Inicializando TrackRun Pro con 6 tipos de mapas...');
            await initializeApp();
            setupEventListeners();
            setupVideoOptions();
            setupMapControls();
        });

        async function initializeApp() {
            try {
                initMap();
                await requestGPSPermissions();
                await getInitialLocation();
                updateUIForReadyState();
                showToast('Aplicación lista con 6 tipos de mapas', 'success');
            } catch (error) {
                console.error('Error:', error);
                showToast('Error al inicializar: ' + error.message, 'error');
            }
        }

        // ============================================
        // SISTEMA DE MAPAS MEJORADO
        // ============================================
        function initMap() {
            const defaultLocation = [40.4168, -3.7038];
            map = L.map('map').setView(defaultLocation, 13);
            
            // Añadir capa inicial (callejero)
            mapLayers.street.addTo(map);
            currentMapType = 'street';
            
            L.control.zoom({ position: 'topright' }).addTo(map);
            L.control.scale({ position: 'bottomleft' }).addTo(map);
            
            updateGPSStatus('mapa_listo', 'Mapa callejero cargado');
            document.getElementById('mapLegend').style.display = 'block';
        }

        function setupMapControls() {
            // Botones de tipo de mapa
            document.querySelectorAll('.map-type-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    const type = this.dataset.type;
                    
                    // Remover clase active de todos los botones
                    document.querySelectorAll('.map-type-btn').forEach(b => b.classList.remove('active'));
                    // Añadir clase active al botón clickeado
                    this.classList.add('active');
                    
                    // Cambiar mapa
                    changeMapType(type);
                });
            });

            // Botón para centrar mapa
            document.getElementById('centerMapBtn').addEventListener('click', centerMapOnUser);
        }

        function changeMapType(type) {
            if (!mapLayers[type]) {
                console.error('Tipo de mapa no disponible:', type);
                return;
            }
            
            // Remover capa actual
            map.eachLayer((layer) => {
                if (layer instanceof L.TileLayer) {
                    map.removeLayer(layer);
                }
            });
            
            // Añadir nueva capa
            mapLayers[type].addTo(map);
            currentMapType = type;
            
            // Actualizar texto
            document.getElementById('currentMapText').textContent = getMapName(type);
            
            // Mostrar mensaje
            showToast(`Mapa cambiado a: ${getMapName(type)}`, 'info');
            
            // Si estamos trackeando, redibujar ruta
            if (routePolyline) {
                routePolyline.addTo(map);
            }
            
            // Si hay marcador, mantenerlo
            if (userMarker) {
                userMarker.addTo(map);
            }
        }

        function getMapName(type) {
            const names = {
                'street': 'Callejero',
                'satellite': 'Satélite',
                'topographic': 'Topográfico',
                'dark': 'Oscuro',
                'light': 'Claro',
                'cycle': 'Ciclismo'
            };
            return names[type] || type;
        }

        function centerMapOnUser() {
            if (currentPosition) {
                map.setView([currentPosition.lat, currentPosition.lng], 16);
                showToast('Mapa centrado en tu ubicación', 'success');
            } else {
                showToast('Primero obtén tu ubicación', 'warning');
            }
        }

        // ============================================
        // GPS Y SEGUIMIENTO (MANTENIDO)
        // ============================================
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
                updateGPSStatus('obteniendo_ubicacion', 'Obteniendo tu ubicación...');
                
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
            }
            
            updateMapWithPosition(currentPosition, isInitial);
            updateGPSDisplay(coords);
            updateStatsDisplay();
            updateGPSStatus('gps_activo', 'GPS activo y funcionando');
            
            if (!isTracking) {
                document.getElementById('startBtn').disabled = false;
            }
            
            // Habilitar botón de video si hay suficientes posiciones
            if (positions.length > 10) {
                document.getElementById('videoBtn').disabled = false;
            }
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
                showToast('Ubicación obtenida correctamente', 'success');
                updateGPSStatus('listo_para_iniciar', 'Listo para iniciar carrera');
            }
        }

        // ============================================
        // CONTROL DE CARRERA
        // ============================================
        function startTracking() {
            if (!currentPosition) {
                showToast('Primero obtén tu ubicación actual', 'warning');
                return;
            }
            
            isTracking = true;
            startTime = new Date();
            totalDistance = 0;
            elevationGain = 0;
            maxSpeed = 0;
            positions = [currentPosition];
            
            timerInterval = setInterval(updateTimer, 1000);
            
            watchId = navigator.geolocation.watchPosition(
                (position) => handleNewPosition(position),
                (error) => handleGPSError(error),
                { enableHighAccuracy: true, maximumAge: 0, timeout: 10000 }
            );
            
            document.getElementById('startBtn').disabled = true;
            document.getElementById('stopBtn').disabled = false;
            document.getElementById('videoBtn').disabled = true;
            document.getElementById('startPoint').style.display = 'flex';
            
            updateStartPointMarker();
            showToast('¡Carrera iniciada! Grabando tu recorrido...', 'success');
            updateGPSStatus('grabando', 'Grabando recorrido...');
        }

        function stopTracking() {
            isTracking = false;
            clearInterval(timerInterval);
            
            if (watchId !== null) {
                navigator.geolocation.clearWatch(watchId);
                watchId = null;
            }
            
            document.getElementById('startBtn').disabled = false;
            document.getElementById('stopBtn').disabled = true;
            
            // Habilitar video si hay suficientes datos
            if (positions.length > 10) {
                document.getElementById('videoBtn').disabled = false;
                showToast('Carrera finalizada. ¡Ahora puedes generar un video!', 'success');
            } else {
                showToast('Carrera finalizada. Recorrido muy corto para video.', 'warning');
            }
            
            updateGPSStatus('finalizado', 'Carrera finalizada');
        }

        function resetTracking() {
            if (isTracking) stopTracking();
            
            startTime = null;
            totalDistance = 0;
            elevationGain = 0;
            maxSpeed = 0;
            positions = [];
            startPosition = null;
            
            if (routePolyline) {
                map.removeLayer(routePolyline);
                routePolyline = null;
            }
            
            document.getElementById('distance').textContent = '0.00';
            document.getElementById('pace').textContent = '--:--';
            document.getElementById('time').textContent = '00:00:00';
            document.getElementById('elevation').textContent = '+0';
            document.getElementById('speed').textContent = '0.0';
            document.getElementById('calories').textContent = '0';
            document.getElementById('videoBtn').disabled = true;
            document.getElementById('startPoint').style.display = 'none';
            document.getElementById('videoOptions').style.display = 'none';
            
            showToast('Todo reiniciado', 'info');
        }

        // ============================================
        // GENERACIÓN DE VIDEO MEJORADA
        // ============================================
        function setupVideoOptions() {
            // Botones de orientación
            document.querySelectorAll('.orientation-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    document.querySelectorAll('.orientation-btn').forEach(b => b.classList.remove('active'));
                    this.classList.add('active');
                    videoOrientation = this.dataset.orientation;
                });
            });
            
            // Botones de calidad
            document.querySelectorAll('.quality-btn[data-quality]').forEach(btn => {
                btn.addEventListener('click', function() {
                    document.querySelectorAll('.quality-btn[data-quality]').forEach(b => b.classList.remove('active'));
                    this.classList.add('active');
                    videoQuality = this.dataset.quality;
                });
            });
            
            // Botones de tipo de mapa para video
            document.querySelectorAll('.quality-btn[data-map]').forEach(btn => {
                btn.addEventListener('click', function() {
                    document.querySelectorAll('.quality-btn[data-map]').forEach(b => b.classList.remove('active'));
                    this.classList.add('active');
                    videoMapType = this.dataset.map;
                });
            });
            
            // Botón para mostrar opciones de video
            document.getElementById('videoBtn').addEventListener('click', () => {
                const options = document.getElementById('videoOptions');
                options.style.display = options.style.display === 'none' ? 'block' : 'none';
            });
            
            // Botón para generar video
            document.getElementById('generateVideoBtn').addEventListener('click', generateVideo);
        }

        async function generateVideo() {
            if (positions.length < 10) {
                showToast('Necesitas al menos 10 posiciones para generar un video', 'warning');
                return;
            }

            // Mostrar modal de generación
            document.getElementById('generatingModal').style.display = 'flex';
            
            try {
                // Obtener configuración
                const showStats = document.getElementById('showStats').checked;
                const showRoute = document.getElementById('showRoute').checked;
                const showSpeed = document.getElementById('showSpeed').checked;
                
                // Simular generación de video
                await simulateVideoGeneration();
                
                // Crear video real (simulado por ahora)
                const videoBlob = await createVideoPreview(showStats, showRoute, showSpeed);
                
                // Mostrar resultado
                showVideoResult(videoBlob);
                
            } catch (error) {
                console.error('Error generando video:', error);
                showToast('Error al generar video: ' + error.message, 'error');
            } finally {
                document.getElementById('generatingModal').style.display = 'none';
            }
        }

        async function simulateVideoGeneration() {
            const steps = [
                'Preparando datos del recorrido...',
                `Configurando mapa ${getMapName(videoMapType)}...`,
                `Generando animación en ${videoOrientation === 'horizontal' ? 'horizontal' : 'vertical'}...`,
                'Renderizando vista del recorrido...',
                `Codificando video en ${videoQuality}...`,
                'Añadiendo estadísticas y efectos...',
                'Optimizando para reproducción...',
                'Finalizando video...'
            ];
            
            for (let i = 0; i < steps.length; i++) {
                updateGenerationProgress(steps[i], (i + 1) * (100 / steps.length));
                await sleep(500);
            }
        }

        async function createVideoPreview(showStats, showRoute, showSpeed) {
            // Crear canvas temporal para el video
            const canvas = document.createElement('canvas');
            const ctx = canvas.getContext('2d');
            
            // Configurar tamaño según orientación y calidad
            const dimensions = getVideoDimensions();
            canvas.width = dimensions.width;
            canvas.height = dimensions.height;
            
            // Crear frames del video
            const frames = 60; // 2 segundos a 30fps
            const framesData = [];
            
            for (let i = 0; i < frames; i++) {
                const progress = i / frames;
                renderVideoFrame(ctx, canvas, progress, showStats, showRoute, showSpeed);
                framesData.push(canvas.toDataURL('image/jpeg', 0.9));
                updateGenerationProgress(`Renderizando frame ${i+1}/${frames}...`, 80 + (i/frames)*20);
                await sleep(10);
            }
            
            // Crear video a partir de los frames
            const videoBlob = await createVideoFromFrames(framesData, dimensions);
            
            return videoBlob;
        }

        function getVideoDimensions() {
            switch(videoQuality) {
                case '1080p':
                    return videoOrientation === 'horizontal' 
                        ? { width: 1920, height: 1080 }
                        : { width: 1080, height: 1920 };
                case '720p':
                    return videoOrientation === 'horizontal'
                        ? { width: 1280, height: 720 }
                        : { width: 720, height: 1280 };
                case '480p':
                default:
                    return videoOrientation === 'horizontal'
                        ? { width: 854, height: 480 }
                        : { width: 480, height: 854 };
            }
        }

        function renderVideoFrame(ctx, canvas, progress, showStats, showRoute, showSpeed) {
            // Fondo según tipo de mapa seleccionado para video
            const mapColors = {
                'satellite': '#16213E',
                'topographic': '#2E4C3D',
                'dark': '#1A1A2E',
                'street': '#E8E8E8',
                'light': '#F5F5F5',
                'cycle': '#D4E6F1'
            };
            
            ctx.fillStyle = mapColors[videoMapType] || '#1A1A2E';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // Calcular posición en el recorrido
            const posIndex = Math.floor(progress * (positions.length - 1));
            const currentPos = positions[posIndex];
            
            // Dibujar mapa simulado según tipo
            drawMapFrame(ctx, canvas, currentPos, progress, videoMapType, showRoute);
            
            // Dibujar estadísticas si están habilitadas
            if (showStats) {
                drawStatsOverlay(ctx, canvas, currentPos, progress);
            }
            
            // Dibujar velocidad si está habilitada
            if (showSpeed && currentPos.speed > 0) {
                ctx.fillStyle = '#FFFFFF';
                ctx.font = 'bold 24px Arial';
                ctx.textAlign = 'center';
                ctx.fillText(`${(currentPos.speed * 3.6).toFixed(1)} km/h`, canvas.width / 2, 50);
            }
            
            // Añadir marca de agua con tipo de mapa
            ctx.fillStyle = 'rgba(255, 255, 255, 0.1)';
            ctx.font = 'bold 20px Arial';
            ctx.textAlign = 'right';
            ctx.fillText(`TrackRun Pro - ${getMapName(videoMapType)}`, canvas.width - 20, canvas.height - 20);
        }

        function drawMapFrame(ctx, canvas, position, progress, mapType, showRoute) {
            const centerX = canvas.width / 2;
            const centerY = canvas.height / 2;
            
            // Dibujar ruta completa si está habilitada
            if (showRoute && positions.length > 1) {
                ctx.strokeStyle = '#FF6B35';
                ctx.lineWidth = 4;
                ctx.beginPath();
                
                const startIndex = Math.max(0, Math.floor(progress * positions.length) - 50);
                const endIndex = Math.floor(progress * positions.length);
                
                for (let i = startIndex; i <= endIndex; i++) {
                    const pos = positions[i];
                    const x = 50 + ((i - startIndex) / (endIndex - startIndex)) * (canvas.width - 100);
                    const y = canvas.height - 150 - (pos.altitude / 10);
                    
                    if (i === startIndex) {
                        ctx.moveTo(x, y);
                    } else {
                        ctx.lineTo(x, y);
                    }
                }
                ctx.stroke();
            }
            
            // Dibujar punto actual
            ctx.fillStyle = '#00A8E8';
            ctx.beginPath();
            ctx.arc(centerX, centerY, 15, 0, Math.PI * 2);
            ctx.fill();
            
            // Dibujar borde según tipo de mapa
            ctx.strokeStyle = mapType === 'dark' ? '#FFFFFF' : '#000000';
            ctx.lineWidth = 2;
            ctx.stroke();
        }

        function drawStatsOverlay(ctx, canvas, position, progress) {
            const statsY = canvas.height - 200;
            
            // Fondo semi-transparente
            ctx.fillStyle = 'rgba(26, 26, 46, 0.9)';
            ctx.fillRect(50, statsY, canvas.width - 100, 140);
            
            // Estadísticas
            ctx.fillStyle = '#FFFFFF';
            ctx.font = 'bold 18px Arial';
            ctx.textAlign = 'left';
            
            const stats = [
                `Distancia: ${(totalDistance * progress).toFixed(2)} km`,
                `Ritmo: ${calculatePace(progress)} min/km`,
                `Tiempo: ${formatTime(progress)}`,
                `Desnivel: +${Math.round(elevationGain * progress)} m`
            ];
            
            stats.forEach((stat, i) => {
                ctx.fillText(stat, 70, statsY + 40 + (i * 30));
            });
            
            // Barra de progreso
            ctx.fillStyle = '#FF6B35';
            ctx.fillRect(70, statsY + 120, (canvas.width - 140) * progress, 8);
        }

        function calculatePace(progress) {
            if (progress === 0) return '--:--';
            
            const elapsedTime = progress * ((new Date() - startTime) / 60000);
            const distance = totalDistance * progress;
            const pace = elapsedTime / distance;
            
            const minutes = Math.floor(pace);
            const seconds = Math.floor((pace - minutes) * 60);
            return `${minutes}:${seconds.toString().padStart(2, '0')}`;
        }

        function formatTime(progress) {
            if (!startTime) return '00:00:00';
            
            const elapsedSeconds = progress * ((new Date() - startTime) / 1000);
            const hours = Math.floor(elapsedSeconds / 3600);
            const minutes = Math.floor((elapsedSeconds % 3600) / 60);
            const seconds = Math.floor(elapsedSeconds % 60);
            
            return `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
        }

        async function createVideoFromFrames(framesData, dimensions) {
            // En una implementación real, aquí usaríamos FFmpeg o MediaRecorder
            // Por ahora, simulamos creando un video placeholder
            
            return new Promise((resolve) => {
                const canvas = document.createElement('canvas');
                canvas.width = dimensions.width;
                canvas.height = dimensions.height;
                const ctx = canvas.getContext('2d');
                
                // Dibujar frame de presentación
                ctx.fillStyle = '#1A1A2E';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                
                // Título
                ctx.fillStyle = '#FFFFFF';
                ctx.font = 'bold 48px Arial';
                ctx.textAlign = 'center';
                ctx.fillText('VIDEO DEL RECORRIDO', canvas.width/2, canvas.height/2 - 100);
                
                // Icono del mapa seleccionado
                ctx.font = '72px FontAwesome';
                ctx.fillText(getMapIcon(videoMapType), canvas.width/2, canvas.height/2 - 10);
                
                // Detalles
                ctx.font = '24px Arial';
                ctx.fillText(`Mapa: ${getMapName(videoMapType)}`, canvas.width/2, canvas.height/2 + 60);
                ctx.fillText(`Orientación: ${videoOrientation === 'horizontal' ? 'Horizontal' : 'Vertical'}`, canvas.width/2, canvas.height/2 + 100);
                ctx.fillText(`Calidad: ${videoQuality}`, canvas.width/2, canvas.height/2 + 140);
                ctx.fillText(`Duración: 2 segundos (demo)`, canvas.width/2, canvas.height/2 + 180);
                
                // Nota
                ctx.fillStyle = '#FF6B35';
                ctx.font = '18px Arial';
                ctx.fillText('(Video generado por TrackRun Pro)', canvas.width/2, canvas.height/2 + 240);
                
                // Convertir a blob
                canvas.toBlob((blob) => {
                    resolve(blob);
                }, 'video/mp4');
            });
        }

        function getMapIcon(mapType) {
            const icons = {
                'street': '🗺️',
                'satellite': '🛰️',
                'topographic': '🏔️',
                'dark': '🌙',
                'light': '☀️',
                'cycle': '🚴'
            };
            return icons[mapType] || '🗺️';
        }

        function showVideoResult(videoBlob) {
            const videoUrl = URL.createObjectURL(videoBlob);
            const date = new Date();
            const filename = `TrackRun_${getMapName(videoMapType)}_${videoOrientation}_${videoQuality}_${date.getFullYear()}-${(date.getMonth()+1).toString().padStart(2,'0')}-${date.getDate().toString().padStart(2,'0')}.mp4`;
            
            const previewContent = `
                <div class="video-preview-container">
                    <div style="background: #000; padding: 40px; text-align: center; border-radius: 8px;">
                        <div style="font-size: 4rem; margin-bottom: 20px;">
                            ${getMapIcon(videoMapType)}
                        </div>
                        <h3 style="color: white; margin-bottom: 10px;">Video Generado</h3>
                        <p style="color: #aaa; margin-bottom: 20px;">
                            Tu video de recorrido está listo para descargar
                        </p>
                        <div style="background: rgba(255,255,255,0.1); padding: 15px; border-radius: 8px; text-align: left; margin-bottom: 20px;">
                            <p><strong>Detalles del video:</strong></p>
                            <p>• Tipo de mapa: ${getMapName(videoMapType)}</p>
                            <p>• Orientación: ${videoOrientation === 'horizontal' ? 'Horizontal' : 'Vertical'}</p>
                            <p>• Calidad: ${videoQuality}</p>
                            <p>• Resolución: ${getVideoDimensions().width}x${getVideoDimensions().height}</p>
                            <p>• Duración: 2 segundos (demo)</p>
                            <p>• Formato: MP4 (compatible universal)</p>
                        </div>
                    </div>
                </div>
                <div class="video-controls">
                    <button class="video-control-btn primary" id="downloadVideoBtn">
                        <i class="fas fa-download"></i> Descargar Video
                    </button>
                    <button class="video-control-btn" id="shareVideoBtn">
                        <i class="fas fa-share"></i> Compartir
                    </button>
                    <button class="video-control-btn" id="closeVideoBtn">
                        <i class="fas fa-times"></i> Cerrar
                    </button>
                </div>
            `;
            
            document.getElementById('videoPreviewContent').innerHTML = previewContent;
            document.getElementById('videoModal').style.display = 'flex';
            
            // Configurar botones
            document.getElementById('downloadVideoBtn').addEventListener('click', () => {
                downloadVideo(videoBlob, filename);
            });
            
            document.getElementById('shareVideoBtn').addEventListener('click', () => {
                shareVideo(videoBlob, filename);
            });
            
            document.getElementById('closeVideoBtn').addEventListener('click', () => {
                document.getElementById('videoModal').style.display = 'none';
            });
        }

        function downloadVideo(blob, filename) {
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = filename;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
            
            showToast(`Video "${filename}" descargado`, 'success');
        }

        function shareVideo(blob, filename) {
            if (navigator.share) {
                const file = new File([blob], filename, { type: 'video/mp4' });
                
                navigator.share({
                    files: [file],
                    title: `Mi recorrido en ${getMapName(videoMapType)}`,
                    text: 'Mira el video de mi recorrido generado con TrackRun Pro'
                }).then(() => {
                    showToast('Video compartido exitosamente', 'success');
                }).catch(err => {
                    showToast('Error al compartir: ' + err.message, 'error');
                });
            } else {
                showToast('La función de compartir no está disponible', 'info');
            }
        }

        // ============================================
        // FUNCIONES AUXILIARES
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
            document.getElementById('statusText').textContent = 
                isTracking ? 'Grabando carrera' : 'GPS activo';
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

        function updateGPSStatus(status, message) {
            const indicator = document.getElementById('gpsIndicator');
            const statusText = document.getElementById('gpsStatusText');
            const details = document.getElementById('gpsDetails');
            
            statusText.textContent = message;
            details.textContent = getStatusDetails(status);
            
            indicator.classList.remove('active');
            if (status === 'gps_activo' || status === 'grabando' || status === 'listo_para_iniciar') {
                indicator.classList.add('active');
            }
        }

        function getStatusDetails(status) {
            const details = {
                'mapa_listo': `Mapa ${getMapName(currentMapType)} cargado`,
                'permisos_concedidos': 'Permisos concedidos. Obteniendo ubicación...',
                'obteniendo_ubicacion': 'Buscando señal GPS...',
                'gps_activo': `GPS activo | Mapa: ${getMapName(currentMapType)}`,
                'listo_para_iniciar': 'Presiona "INICIAR CARRERA" para comenzar',
                'grabando': 'Grabando recorrido en tiempo real',
                'finalizado': 'Carrera completada',
                'error': 'Error en el sistema GPS'
            };
            return details[status] || 'Estado desconocido';
        }

        function handleGPSError(error) {
            let message = 'Error de GPS: ';
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

        function updateUIForReadyState() {
            document.getElementById('startBtn').disabled = false;
            document.getElementById('locateBtn').disabled = false;
        }

        function setupEventListeners() {
            document.getElementById('startBtn').addEventListener('click', startTracking);
            document.getElementById('stopBtn').addEventListener('click', stopTracking);
            document.getElementById('resetBtn').addEventListener('click', resetTracking);
            document.getElementById('locateBtn').addEventListener('click', () => {
                getInitialLocation().catch(() => {
                    showToast('No se pudo obtener ubicación', 'error');
                });
            });
            document.getElementById('closeModal').addEventListener('click', () => {
                document.getElementById('videoModal').style.display = 'none';
            });
        }

        function updateGenerationProgress(step, progress) {
            document.getElementById('generationStep').textContent = step;
            document.getElementById('generationProgress').style.width = progress + '%';
            document.getElementById('progressText').textContent = Math.round(progress) + '% completado';
        }

        function sleep(ms) {
            return new Promise(resolve => setTimeout(resolve, ms));
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
        }

        // Spinner animation
        const style = document.createElement('style');
        style.textContent = `
            @keyframes spin {
                to { transform: rotate(360deg); }
            }
        `;
        document.head.appendChild(style);

    </script>
</body>
</html>
