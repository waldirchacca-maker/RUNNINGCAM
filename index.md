<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TrackRun Pro - GPS Funcional</title>
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

        .control-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none !important;
        }

        .control-btn:not(:disabled):hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
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

        /* GPS Debug */
        .gps-debug {
            position: absolute;
            bottom: 15px;
            left: 15px;
            background: rgba(0, 0, 0, 0.8);
            color: white;
            padding: 10px 15px;
            border-radius: 8px;
            font-size: 0.8rem;
            z-index: 1000;
            max-width: 250px;
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
                    <p>GPS Funcional - Inicia tu carrera ahora</p>
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
                                <p id="gpsDetails">Esperando permisos...</p>
                            </div>
                        </div>
                        <div id="gpsInstructions" style="font-size: 0.9rem; color: #aaa; margin-top: 10px;">
                            <p><i class="fas fa-info-circle"></i> Acepta los permisos de ubicación para comenzar</p>
                        </div>
                    </div>

                    <!-- Start Point Marker -->
                    <div class="start-point" id="startPoint" style="display: none;">
                        <i class="fas fa-flag-checkered"></i>
                        Punto de inicio fijado
                    </div>
                </div>

                <!-- GPS Debug (visible solo durante desarrollo) -->
                <div class="gps-debug" id="gpsDebug" style="display: none;">
                    <div><strong>Estado GPS:</strong> <span id="debugStatus">Inactivo</span></div>
                    <div><strong>Precisión:</strong> <span id="debugAccuracy">--</span></div>
                    <div><strong>Última posición:</strong> <span id="debugPosition">--</span></div>
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
                                <span>Altitud:</span>
                                <span id="altitudeText">-- metros</span>
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
            </section>
        </main>
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
        // VARIABLES GLOBALES SIMPLIFICADAS
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
        let initialAltitude = null;

        // ============================================
        // INICIALIZACIÓN DE LA APLICACIÓN
        // ============================================
        document.addEventListener('DOMContentLoaded', async () => {
            console.log('Inicializando aplicación...');
            await initializeApp();
            setupEventListeners();
        });

        // ============================================
        // FUNCIÓN PRINCIPAL DE INICIALIZACIÓN
        // ============================================
        async function initializeApp() {
            try {
                // 1. Inicializar mapa inmediatamente
                initMap();
                
                // 2. Solicitar permisos GPS explícitamente
                await requestGPSPermissions();
                
                // 3. Obtener ubicación inicial
                await getInitialLocation();
                
                // 4. Configurar interfaz
                updateUIForReadyState();
                
                showToast('Aplicación lista. Puedes iniciar tu carrera.', 'success');
                
            } catch (error) {
                console.error('Error en inicialización:', error);
                showToast(`Error: ${error.message}`, 'error');
                showManualLocationPrompt();
            }
        }

        // ============================================
        // 1. INICIALIZAR MAPA (SIN GPS)
        // ============================================
        function initMap() {
            try {
                // Usar ubicación por defecto (Madrid)
                const defaultLocation = [40.4168, -3.7038];
                const defaultZoom = 13;
                
                map = L.map('map').setView(defaultLocation, defaultZoom);
                
                // Añadir capa base
                L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                    attribution: '© OpenStreetMap',
                    maxZoom: 19
                }).addTo(map);
                
                // Añadir controles
                L.control.zoom({ position: 'topright' }).addTo(map);
                L.control.scale({ position: 'bottomleft' }).addTo(map);
                
                console.log('Mapa inicializado correctamente');
                updateGPSStatus('mapa_listo', 'Mapa listo. Esperando GPS...');
                
            } catch (error) {
                console.error('Error al inicializar mapa:', error);
                showToast('Error al cargar el mapa', 'error');
            }
        }

        // ============================================
        // 2. SOLICITAR PERMISOS GPS
        // ============================================
        function requestGPSPermissions() {
            return new Promise((resolve, reject) => {
                if (!navigator.geolocation) {
                    reject(new Error('Tu navegador no soporta geolocalización'));
                    return;
                }
                
                console.log('Solicitando permisos GPS...');
                updateGPSStatus('solicitando_permisos', 'Solicitando permisos de ubicación...');
                
                // Intentar obtener ubicación para solicitar permisos
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        console.log('Permisos GPS concedidos');
                        updateGPSStatus('permisos_concedidos', 'Permisos GPS concedidos');
                        resolve(position);
                    },
                    (error) => {
                        console.warn('Permisos GPS no concedidos:', error.message);
                        updateGPSStatus('permisos_denegados', 'Permisos de ubicación no concedidos');
                        reject(new Error('Permisos de ubicación no concedidos'));
                    },
                    {
                        enableHighAccuracy: true,
                        timeout: 10000,
                        maximumAge: 0
                    }
                );
            });
        }

        // ============================================
        // 3. OBTENER UBICACIÓN INICIAL
        // ============================================
        function getInitialLocation() {
            return new Promise((resolve, reject) => {
                console.log('Obteniendo ubicación inicial...');
                updateGPSStatus('obteniendo_ubicacion', 'Obteniendo tu ubicación...');
                
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        console.log('Ubicación obtenida:', position.coords);
                        handleNewPosition(position, true);
                        resolve(position);
                    },
                    (error) => {
                        console.warn('No se pudo obtener ubicación:', error.message);
                        updateGPSStatus('error_ubicacion', 'No se pudo obtener ubicación');
                        reject(error);
                    },
                    {
                        enableHighAccuracy: true,
                        timeout: 15000,
                        maximumAge: 0
                    }
                );
            });
        }

        // ============================================
        // 4. MANEJAR NUEVA POSICIÓN GPS
        // ============================================
        function handleNewPosition(position, isInitial = false) {
            const coords = position.coords;
            console.log('Nueva posición:', coords);
            
            // Actualizar posición actual
            currentPosition = {
                lat: coords.latitude,
                lng: coords.longitude,
                altitude: coords.altitude || 0,
                speed: coords.speed || 0,
                accuracy: coords.accuracy,
                timestamp: new Date(position.timestamp)
            };
            
            // Si es la primera posición, establecer como punto de inicio
            if (isInitial) {
                startPosition = { ...currentPosition };
                positions = [currentPosition];
                updateStartPointMarker();
            } else if (isTracking) {
                // Calcular distancia desde la última posición
                if (positions.length > 0) {
                    const lastPos = positions[positions.length - 1];
                    const distance = calculateDistance(
                        lastPos.lat, lastPos.lng,
                        currentPosition.lat, currentPosition.lng
                    );
                    totalDistance += distance;
                    
                    // Calcular desnivel
                    if (lastPos.altitude && currentPosition.altitude) {
                        const elevationDiff = currentPosition.altitude - lastPos.altitude;
                        if (elevationDiff > 0) {
                            elevationGain += elevationDiff;
                        }
                    }
                    
                    // Actualizar velocidad máxima
                    if (currentPosition.speed > maxSpeed) {
                        maxSpeed = currentPosition.speed;
                    }
                }
                
                positions.push(currentPosition);
            }
            
            // Actualizar interfaz
            updateMapWithPosition(currentPosition, isInitial);
            updateGPSDisplay(coords);
            updateStatsDisplay();
            
            // Mostrar estado GPS
            updateGPSStatus('gps_activo', 'GPS activo y funcionando');
            
            // Habilitar botón de inicio si no estamos trackeando
            if (!isTracking) {
                document.getElementById('startBtn').disabled = false;
            }
        }

        // ============================================
        // 5. ACTUALIZAR MAPA CON POSICIÓN
        // ============================================
        function updateMapWithPosition(position, isInitial = false) {
            const latLng = [position.lat, position.lng];
            
            // Centrar mapa en la posición
            map.setView(latLng, 16);
            
            // Actualizar o crear marcador
            if (!userMarker) {
                userMarker = L.marker(latLng, {
                    icon: L.divIcon({
                        className: 'user-marker',
                        html: '<div style="background: #FF6B35; width: 24px; height: 24px; border-radius: 50%; border: 3px solid white; box-shadow: 0 0 10px rgba(0,0,0,0.5);"></div>',
                        iconSize: [24, 24]
                    })
                }).addTo(map);
                
                userMarker.bindPopup('<b>Tu ubicación actual</b><br>Precisión: ' + Math.round(position.accuracy) + 'm').openPopup();
            } else {
                userMarker.setLatLng(latLng);
            }
            
            // Dibujar ruta si estamos trackeando
            if (isTracking && positions.length > 1) {
                if (routePolyline) {
                    map.removeLayer(routePolyline);
                }
                
                const latLngs = positions.map(p => [p.lat, p.lng]);
                routePolyline = L.polyline(latLngs, {
                    color: '#FF6B35',
                    weight: 4,
                    opacity: 0.8,
                    lineJoin: 'round'
                }).addTo(map);
            }
            
            // Si es posición inicial, mostrar mensaje
            if (isInitial) {
                showToast('Ubicación obtenida correctamente', 'success');
                updateGPSStatus('listo_para_iniciar', 'Listo para iniciar carrera');
            }
        }

        // ============================================
        // 6. INICIAR SEGUIMIENTO DE CARRERA
        // ============================================
        function startTracking() {
            if (!currentPosition) {
                showToast('Primero obtén tu ubicación actual', 'warning');
                return;
            }
            
            console.log('Iniciando seguimiento de carrera...');
            
            // Reiniciar variables de carrera
            isTracking = true;
            startTime = new Date();
            totalDistance = 0;
            elevationGain = 0;
            maxSpeed = 0;
            positions = [currentPosition];
            
            // Configurar intervalo del temporizador
            timerInterval = setInterval(updateTimer, 1000);
            
            // Iniciar seguimiento GPS continuo
            watchId = navigator.geolocation.watchPosition(
                (position) => handleNewPosition(position),
                (error) => handleGPSError(error),
                {
                    enableHighAccuracy: true,
                    maximumAge: 0,
                    timeout: 10000
                }
            );
            
            // Actualizar interfaz
            document.getElementById('startBtn').disabled = true;
            document.getElementById('stopBtn').disabled = false;
            document.getElementById('startPoint').style.display = 'flex';
            
            // Mostrar punto de inicio en el mapa
            updateStartPointMarker();
            
            showToast('¡Carrera iniciada! Grabando tu recorrido...', 'success');
            updateGPSStatus('grabando', 'Grabando recorrido...');
        }

        // ============================================
        // 7. DETENER SEGUIMIENTO
        // ============================================
        function stopTracking() {
            console.log('Deteniendo seguimiento...');
            
            isTracking = false;
            
            // Detener temporizador
            clearInterval(timerInterval);
            
            // Detener seguimiento GPS
            if (watchId !== null) {
                navigator.geolocation.clearWatch(watchId);
                watchId = null;
            }
            
            // Actualizar interfaz
            document.getElementById('startBtn').disabled = false;
            document.getElementById('stopBtn').disabled = true;
            
            showToast('Carrera finalizada', 'info');
            updateGPSStatus('finalizado', 'Carrera finalizada');
        }

        // ============================================
        // 8. REINICIAR TODO
        // ============================================
        function resetTracking() {
            console.log('Reiniciando todo...');
            
            // Detener si está activo
            if (isTracking) {
                stopTracking();
            }
            
            // Reiniciar variables
            startTime = null;
            totalDistance = 0;
            elevationGain = 0;
            maxSpeed = 0;
            positions = [];
            startPosition = null;
            
            // Limpiar mapa
            if (routePolyline) {
                map.removeLayer(routePolyline);
                routePolyline = null;
            }
            
            // Reiniciar estadísticas
            document.getElementById('distance').textContent = '0.00';
            document.getElementById('pace').textContent = '--:--';
            document.getElementById('time').textContent = '00:00:00';
            document.getElementById('elevation').textContent = '+0';
            document.getElementById('speed').textContent = '0.0';
            document.getElementById('calories').textContent = '0';
            
            // Ocultar punto de inicio
            document.getElementById('startPoint').style.display = 'none';
            
            showToast('Todo reiniciado', 'info');
        }

        // ============================================
        // 9. FUNCIONES AUXILIARES
        // ============================================

        // Calcular distancia entre dos puntos
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
            
            // Actualizar ritmo
            if (totalDistance > 0) {
                const paceSeconds = elapsedSeconds / totalDistance;
                const paceMinutes = Math.floor(paceSeconds / 60);
                const paceSecs = Math.floor(paceSeconds % 60);
                document.getElementById('pace').textContent = 
                    `${paceMinutes}:${paceSecs.toString().padStart(2, '0')}`;
            }
        }

        // Actualizar estadísticas
        function updateStatsDisplay() {
            // Distancia
            document.getElementById('distance').textContent = totalDistance.toFixed(2);
            
            // Desnivel
            document.getElementById('elevation').textContent = `+${Math.round(elevationGain)}`;
            
            // Velocidad
            const speedKmph = currentPosition ? (currentPosition.speed * 3.6) : 0;
            document.getElementById('speed').textContent = speedKmph.toFixed(1);
            
            // Calorías (estimación simple)
            if (startTime) {
                const minutes = (new Date() - startTime) / 60000;
                const calories = minutes * 10; // 10 kcal por minuto
                document.getElementById('calories').textContent = Math.round(calories);
            }
        }

        // Actualizar display GPS
        function updateGPSDisplay(coords) {
            document.getElementById('accuracyText').textContent = 
                `${Math.round(coords.accuracy)} metros`;
            
            document.getElementById('altitudeText').textContent = 
                coords.altitude ? `${Math.round(coords.altitude)} metros` : '-- metros';
            
            document.getElementById('statusText').textContent = 
                isTracking ? 'Grabando carrera' : 'GPS activo';
        }

        // Actualizar marcador de punto de inicio
        function updateStartPointMarker() {
            if (startPosition && userMarker) {
                // Crear marcador de inicio
                const startMarker = L.marker([startPosition.lat, startPosition.lng], {
                    icon: L.divIcon({
                        className: 'start-marker',
                        html: '<div style="background: #4CAF50; width: 20px; height: 20px; border-radius: 50%; border: 3px solid white; box-shadow: 0 0 10px rgba(76,175,80,0.8);"></div>',
                        iconSize: [20, 20]
                    })
                }).addTo(map);
                
                startMarker.bindPopup('<b>Punto de inicio</b><br>Haz clic aquí para centrar').on('click', () => {
                    map.setView([startPosition.lat, startPosition.lng], 16);
                });
            }
        }

        // Manejar errores GPS
        function handleGPSError(error) {
            console.error('Error GPS:', error);
            
            let message = 'Error de GPS: ';
            switch(error.code) {
                case error.PERMISSION_DENIED:
                    message += 'Permisos denegados. Por favor, habilita la ubicación en tu navegador.';
                    updateGPSStatus('permisos_denegados', 'Permisos denegados');
                    break;
                case error.POSITION_UNAVAILABLE:
                    message += 'Ubicación no disponible.';
                    updateGPSStatus('no_disponible', 'GPS no disponible');
                    break;
                case error.TIMEOUT:
                    message += 'Tiempo de espera agotado.';
                    updateGPSStatus('timeout', 'Timeout GPS');
                    break;
                default:
                    message += 'Error desconocido.';
                    updateGPSStatus('error', 'Error GPS');
                    break;
            }
            
            showToast(message, 'error');
        }

        // Actualizar estado GPS en overlay
        function updateGPSStatus(status, message) {
            const indicator = document.getElementById('gpsIndicator');
            const statusText = document.getElementById('gpsStatusText');
            const details = document.getElementById('gpsDetails');
            
            statusText.textContent = message;
            details.textContent = getStatusDetails(status);
            
            // Actualizar indicador visual
            indicator.classList.remove('active');
            if (status === 'gps_activo' || status === 'grabando' || status === 'listo_para_iniciar') {
                indicator.classList.add('active');
            }
        }

        // Obtener detalles del estado
        function getStatusDetails(status) {
            const details = {
                'solicitando_permisos': 'Por favor, acepta los permisos en la ventana emergente...',
                'permisos_concedidos': 'Permisos concedidos. Obteniendo ubicación...',
                'permisos_denegados': 'Habilita la ubicación en configuración del navegador',
                'obteniendo_ubicacion': 'Buscando señal GPS...',
                'gps_activo': 'Señal GPS estable',
                'listo_para_iniciar': 'Presiona "INICIAR CARRERA" para comenzar',
                'grabando': 'Grabando tu recorrido en tiempo real',
                'finalizado': 'Carrera completada',
                'error': 'Error en el sistema GPS'
            };
            
            return details[status] || 'Estado desconocido';
        }

        // Mostrar prompt manual de ubicación
        function showManualLocationPrompt() {
            const instructions = document.getElementById('gpsInstructions');
            instructions.innerHTML = `
                <p><i class="fas fa-exclamation-triangle" style="color: #FF9800;"></i> 
                <strong>No se pudo obtener ubicación automática</strong></p>
                <p style="margin-top: 8px;">Por favor:</p>
                <ol style="margin-left: 20px; margin-top: 5px;">
                    <li>Acepta los permisos de ubicación</li>
                    <li>Haz clic en "UBICARME AHORA"</li>
                    <li>Asegúrate de tener GPS activado</li>
                </ol>
            `;
        }

        // Actualizar UI para estado listo
        function updateUIForReadyState() {
            document.getElementById('startBtn').disabled = false;
            document.getElementById('locateBtn').disabled = false;
        }

        // ============================================
        // CONFIGURACIÓN DE EVENT LISTENERS
        // ============================================
        function setupEventListeners() {
            // Botones principales
            document.getElementById('startBtn').addEventListener('click', startTracking);
            document.getElementById('stopBtn').addEventListener('click', stopTracking);
            document.getElementById('resetBtn').addEventListener('click', resetTracking);
            
            // Botón de ubicación manual
            document.getElementById('locateBtn').addEventListener('click', () => {
                getInitialLocation().catch(() => {
                    showToast('No se pudo obtener ubicación. Revisa los permisos.', 'error');
                });
            });
            
            // Permitir clic en mapa para debugging
            map.on('click', (e) => {
                console.log('Clic en mapa:', e.latlng);
            });
            
            // Mostrar coordenadas al mover el mouse
            map.on('mousemove', (e) => {
                // Solo para debugging
                if (document.getElementById('gpsDebug').style.display === 'block') {
                    document.getElementById('debugPosition').textContent = 
                        `${e.latlng.lat.toFixed(6)}, ${e.latlng.lng.toFixed(6)}`;
                }
            });
        }

        // ============================================
        // FUNCIÓN DE TOAST NOTIFICATIONS
        // ============================================
        function showToast(message, type = 'info') {
            const toast = document.getElementById('toast');
            const toastMessage = document.getElementById('toastMessage');
            
            // Configurar tipo
            toast.className = 'toast';
            if (type === 'error') toast.classList.add('error');
            if (type === 'warning') toast.classList.add('warning');
            if (type === 'info') toast.classList.add('info');
            
            // Configurar icono
            const icon = toast.querySelector('i');
            icon.className = 
                type === 'success' ? 'fas fa-check-circle' :
                type === 'error' ? 'fas fa-exclamation-circle' :
                type === 'warning' ? 'fas fa-exclamation-triangle' :
                'fas fa-info-circle';
            
            // Configurar mensaje
            toastMessage.textContent = message;
            
            // Mostrar
            toast.style.display = 'flex';
            
            // Ocultar después de 4 segundos
            setTimeout(() => {
                toast.style.display = 'none';
            }, 4000);
            
            // Log en consola
            console.log(`Toast [${type}]: ${message}`);
        }

        // ============================================
        // FUNCIÓN PARA DEBUG (OPCIONAL)
        // ============================================
        function toggleDebug() {
            const debug = document.getElementById('gpsDebug');
            debug.style.display = debug.style.display === 'none' ? 'block' : 'none';
        }

        // Activar debug con doble clic en logo
        document.querySelector('.logo-container').addEventListener('dblclick', toggleDebug);

    </script>
</body>
</html>
