    <!DOCTYPE html>
    <html lang="es">
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guía de Debloat mediante ADB para MIUI/HyperOS</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

      body {
            background: #0a0e14;
            font-family: 'Fira Code', 'Cascadia Code', 'Consolas', 'Courier New', monospace;
            padding: 30px 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .terminal {
            max-width: 1100px;
            width: 100%;
            background: #11181f;
            border-radius: 16px;
            padding: 35px 40px 45px 40px;
            box-shadow: 0 20px 50px rgba(0, 0, 0, 0.85), inset 0 1px 0 rgba(255, 255, 255, 0.04);
            border: 1px solid #1e2a34;
            color: #c8d6e0;
        }

        /* ===== ENCABEZADO ===== */
        .terminal-header {
            display: flex;
            align-items: center;
            gap: 14px;
            padding-bottom: 20px;
            border-bottom: 2px solid #1e2a34;
            margin-bottom: 30px;
            flex-wrap: wrap;
        }

        .dot-group {
            display: flex;
            gap: 8px;
        }

        .dot {
            width: 14px;
            height: 14px;
            border-radius: 50%;
            display: inline-block;
        }

        .dot.red { background: #ff5f57; }
        .dot.yellow { background: #ffbd2e; }
        .dot.green { background: #28c840; }

        .terminal-title {
            font-size: 18px;
            color: #6d8a9e;
            letter-spacing: 0.5px;
            font-weight: 400;
        }

        .terminal-title span {
            color: #d4e6f0;
            font-weight: 600;
        }

        .badge {
            margin-left: auto;
            background: #1d2b38;
            padding: 5px 16px;
            border-radius: 30px;
            font-size: 12px;
            color: #7a9bb3;
            border: 1px solid #2a3b4a;
            letter-spacing: 0.5px;
            text-transform: uppercase;
        }

        /* ===== TÍTULOS PRINCIPALES ===== */
        .main-title {
            font-size: 26px;
            font-weight: 700;
            color: #eaf3fa;
            padding: 12px 0 6px 0;
            letter-spacing: 0.5px;
            border-bottom: 2px solid #2a4050;
            margin-bottom: 18px;
        }

        .main-title .highlight {
            color: #54b67a;
        }

        .section-title {
            font-size: 20px;
            font-weight: 600;
            color: #d9e9f5;
            padding: 18px 0 8px 0;
            margin-top: 10px;
            border-bottom: 1px dashed #1e2a34;
            margin-bottom: 14px;
        }

        .section-title .icon {
            color: #3c8db0;
            margin-right: 12px;
        }

        .sub-section-title {
            font-size: 17px;
            font-weight: 500;
            color: #b3cdde;
            padding: 14px 0 6px 0;
            margin-top: 6px;
        }

        .sub-section-title .step {
            color: #f1c40f;
            font-weight: 700;
            margin-right: 8px;
        }

        /* ===== TEXTO GENERAL ===== */
        .text-block {
            padding: 6px 0 4px 0;
            line-height: 1.7;
            font-size: 15px;
            color: #c8d6e0;
        }

        .text-block strong {
            color: #d4e6f0;
            font-weight: 600;
        }

        .text-block .highlight-text {
            color: #f1c40f;
        }

        .text-block .path {
            color: #7ab8d4;
            background: #0d141c;
            padding: 1px 10px;
            border-radius: 4px;
            font-size: 14px;
        }

        .text-block .cmd-inline {
            color: #bfd7e6;
            background: #0d141c;
            padding: 1px 10px;
            border-radius: 4px;
            font-size: 14px;
        }

        .text-block .success-text {
            color: #52c47a;
        }

        .text-block .warning-text {
            color: #f0b34b;
        }

        .text-block .muted-text {
            color: #6d8a9e;
        }

        /* ===== LISTAS ===== */
        .step-list {
            padding-left: 20px;
            margin: 8px 0 10px 0;
            list-style: none;
        }

        .step-list li {
            padding: 4px 0 4px 28px;
            position: relative;
            line-height: 1.7;
            font-size: 15px;
            color: #c8d6e0;
        }

        .step-list li::before {
            content: "▸";
            position: absolute;
            left: 4px;
            color: #3c8db0;
            font-weight: 700;
        }

        .step-list li strong {
            color: #d4e6f0;
        }

        .step-list li .highlight-text {
            color: #f1c40f;
        }

        .step-list li .path {
            color: #7ab8d4;
            background: #0d141c;
            padding: 0 8px;
            border-radius: 3px;
            font-size: 14px;
        }

        /* ===== BLOQUES DE COMANDO ===== */
        .cmd-block {
            background: #0d141c;
            padding: 16px 22px;
            border-radius: 8px;
            margin: 10px 0 12px 0;
            border-left: 4px solid #2f7a9e;
            overflow-x: auto;
            white-space: pre-wrap;
            word-break: break-all;
            color: #bfd7e6;
            font-size: 14px;
            line-height: 1.8;
        }

        .cmd-block .prompt {
            color: #54b67a;
            font-weight: 500;
        }

        .cmd-block .cmd {
            color: #e8f0f7;
        }

        .cmd-block .cmd-highlight {
            color: #f1c40f;
        }

        .cmd-block .comment-cmd {
            color: #5b7c91;
            font-style: italic;
        }

        /* ===== NOTAS ===== */
        .note-box {
            background: #101b24;
            border-radius: 8px;
            padding: 16px 22px;
            margin: 16px 0 10px 0;
            border-left: 4px solid #f0b34b;
            font-size: 14px;
            color: #b8cfdf;
            line-height: 1.7;
        }

        .note-box strong {
            color: #f5cd7a;
        }

        .note-box .cmd-inline {
            color: #d7e8f5;
            background: #1b2935;
            padding: 2px 12px;
            border-radius: 4px;
            font-size: 13px;
        }

        .note-box .success-text {
            color: #52c47a;
        }

        .note-box .warning-text {
            color: #f0b34b;
        }

        .note-box .muted-text {
            color: #6d8a9e;
        }

        /* ===== FICHA TÉCNICA ===== */
        .tech-specs {
            background: #0d141c;
            border-radius: 8px;
            padding: 16px 22px;
            margin: 10px 0 16px 0;
            border: 1px solid #1e2a34;
        }

        .tech-specs .spec-line {
            padding: 3px 0;
            font-size: 14px;
            color: #aac2d4;
        }

        .tech-specs .spec-line .label {
            color: #6d8a9e;
            font-weight: 400;
        }

        .tech-specs .spec-line .value {
            color: #d4e6f0;
            font-weight: 500;
        }

        .tech-specs .spec-line .value-highlight {
            color: #f1c40f;
        }

        /* ===== TABLA DE RIESGOS ===== */
        .risk-table {
            width: 100%;
            border-collapse: collapse;
            margin: 12px 0 8px 0;
            font-size: 14px;
        }

        .risk-table th {
            text-align: left;
            padding: 10px 12px;
            background: #1a2630;
            color: #b3cdde;
            font-weight: 600;
            border-bottom: 2px solid #2a4050;
        }

        .risk-table td {
            padding: 8px 12px;
            border-bottom: 1px solid #1a2630;
            color: #c8d6e0;
        }

        .risk-table .pkg {
            color: #bfd7e6;
            font-weight: 500;
        }

        .risk-table .risk-low {
            color: #52c47a;
        }

        .risk-table .risk-medium {
            color: #f0b34b;
        }

        .risk-table .risk-high {
            color: #e06c6c;
        }

        .risk-table .desc {
            color: #8aaec2;
            font-style: italic;
        }

        /* ===== DESCRIPCIONES DE PAQUETES ===== */
        .pkg-desc {
            padding: 4px 0 4px 24px;
            font-size: 14px;
            color: #8aaec2;
            line-height: 1.6;
        }

        .pkg-desc .pkg-name {
            color: #bfd7e6;
            font-weight: 500;
        }

        .pkg-desc .pkg-info {
            color: #6d8a9e;
            font-style: italic;
        }

        /* ===== DIVISORES ===== */
        .divider {
            border: 0;
            height: 1px;
            background: linear-gradient(90deg, #1e2a34, #3a4d5e, #1e2a34);
            margin: 28px 0;
        }

        .divider-light {
            border: 0;
            height: 1px;
            background: #1a2630;
            margin: 18px 0;
        }

        /* ===== FOOTER ===== */
        .terminal-footer {
            display: flex;
            justify-content: space-between;
            flex-wrap: wrap;
            gap: 10px;
            padding-top: 20px;
            margin-top: 10px;
            border-top: 1px solid #1e2a34;
            font-size: 13px;
            color: #4f6f84;
        }

        .terminal-footer .footer-left {
            display: flex;
            gap: 20px;
            flex-wrap: wrap;
        }

        .terminal-footer .footer-right {
            color: #5b7c91;
        }

        /* ===== SOLUCIÓN DE PROBLEMAS ===== */
        .troubleshoot-item {
            padding: 8px 0 8px 28px;
            position: relative;
            line-height: 1.7;
            font-size: 15px;
            color: #c8d6e0;
        }

        .troubleshoot-item::before {
            content: "⚠";
            position: absolute;
            left: 0;
            color: #f0b34b;
            font-weight: 700;
        }

        .troubleshoot-item .cmd-inline {
            color: #bfd7e6;
            background: #0d141c;
            padding: 1px 10px;
            border-radius: 4px;
            font-size: 14px;
        }

        .troubleshoot-item .success-text {
            color: #52c47a;
        }

        .troubleshoot-item .warning-text {
            color: #f0b34b;
        }

        /* ===== RESPONSIVE ===== */
        @media print {
            body {
                background: #0a0e14;
                padding: 0;
            }

            .terminal {
                box-shadow: none;
                border: 1px solid #1e2a34;
                border-radius: 10px;
                padding: 28px 32px 35px 32px;
                max-width: 100%;
            }

            .badge {
                background: #1d2b38;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }

            .dot {
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }

            .cmd-block {
                background: #0d141c;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }

            .note-box {
                background: #101b24;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }

            .tech-specs {
                background: #0d141c;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }

            .text-block .path,
            .text-block .cmd-inline,
            .step-list li .path {
                background: #0d141c;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }

            .main-title {
                border-bottom-color: #2a4050;
            }

            .section-title {
                border-bottom-color: #1e2a34;
            }

            .divider {
                background: linear-gradient(90deg, #1e2a34, #3a4d5e, #1e2a34);
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }

            .note-box .cmd-inline {
                background: #1b2935;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }

            .troubleshoot-item .cmd-inline {
                background: #0d141c;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }

            .risk-table th {
                background: #1a2630;
                -webkit-print-color-adjust: exact;
                print-color-adjust: exact;
            }
        }

        @media (max-width: 700px) {
            body { padding: 12px; }
            .terminal { padding: 20px 16px 28px 16px; }
            .main-title { font-size: 20px; }
            .section-title { font-size: 17px; }
            .terminal-header { gap: 10px; }
            .badge { margin-left: 0; }
            .cmd-block { font-size: 13px; padding: 14px 16px; }
            .text-block { font-size: 14px; }
            .step-list li { font-size: 14px; }
            .terminal-footer { flex-direction: column; gap: 6px; }
            .tech-specs .spec-line { font-size: 13px; }
            .troubleshoot-item { font-size: 14px; }
            .risk-table { font-size: 13px; }
            .risk-table th, .risk-table td { padding: 6px 8px; }
        }

        /* ===== SCROLLBAR ===== */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #0d141c;
        }
        ::-webkit-scrollbar-thumb {
            background: #2c4050;
            border-radius: 8px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #3a5268;
        }
    </style>
</head>
<body>

<div class="terminal">

    <!-- ===== HEADER ===== -->
    <div class="terminal-header">
        <div class="dot-group">
            <span class="dot red"></span>
            <span class="dot yellow"></span>
            <span class="dot green"></span>
        </div>
        <div class="terminal-title">
            <span></span> DEBLOAT · ADB
        </div>
        <div class="badge">MIUI / HyperOS</div>
    </div>

    <!-- ===== TÍTULO PRINCIPAL ===== -->
    <div class="main-title">
        Guía de Optimización y Desinstalación de Paquetes <span class="highlight">mediante ADB</span>
    </div>
    <div class="text-block" style="font-size:15px; color:#8aaec2; padding-bottom:10px;">
        Procedimiento para reducir aplicaciones preinstaladas, servicios opcionales y componentes publicitarios en dispositivos Xiaomi y Poco con MIUI o HyperOS, utilizando las herramientas de depuración de Android.
    </div>

    <hr class="divider">

    <!-- ===== ADVERTENCIAS Y CONSIDERACIONES ===== -->
    <div class="section-title"><span class="icon">⚠️</span> Consideraciones Importantes</div>

    <div class="note-box" style="border-left-color:#3c8db0; margin-bottom:12px;">
        <strong>📌 Sobre el método utilizado</strong>
        <br><br>
        Los comandos de esta guía utilizan:
        <br>
        <span class="cmd-inline">pm uninstall -k --user 0</span>
        <br><br>
        Este método <strong>no elimina físicamente</strong> los archivos del sistema ni modifica la partición del sistema operativo. Las aplicaciones se desinstalan únicamente para el usuario actual y pueden restaurarse posteriormente mediante ADB. No requiere acceso root.
        <br><br>
        No todos los paquetes están presentes en todas las versiones de MIUI o HyperOS. Algunos comandos pueden devolver errores sin que ello represente un problema.
    </div>

    <div class="note-box" style="border-left-color:#e06c6c; margin-bottom:12px;">
        <strong>⚠️ Advertencia de riesgos</strong>
        <br><br>
        Aunque los paquetes incluidos en esta guía han sido seleccionados por su bajo impacto en el funcionamiento general del sistema, la desinstalación de componentes del fabricante puede ocasionar comportamientos inesperados en determinadas versiones de firmware.
        <br><br>
        Se recomienda realizar una copia de seguridad antes de comenzar.
    </div>

    <div class="note-box" style="border-left-color:#6d8a9e; margin-bottom:8px;">
        <strong>🔄 Comportamiento de las desinstalaciones</strong>
        <br><br>
        Las aplicaciones eliminadas mediante <span class="cmd-inline">pm uninstall -k --user 0</span> pueden reaparecer después de:
        <br><br>
        • Restablecimiento de fábrica.<br>
        • Actualizaciones mayores del sistema (ej. MIUI → HyperOS).<br>
        • Creación de un nuevo perfil de usuario.
        <br><br>
        Esto no indica un error; es el comportamiento esperado del método.
    </div>

    <hr class="divider">

    <!-- ===== SECCIÓN 1: REQUISITOS ===== -->
    <div class="section-title"><span class="icon">🛠️</span> Requisitos Previos y Configuración Inicial</div>

    <div class="text-block">
        Antes de ejecutar los comandos, es necesario preparar el entorno en la computadora y habilitar el acceso en el teléfono.
    </div>

    <!-- Paso 1 -->
    <div class="sub-section-title"><span class="step">Paso 1</span> Configurar las Platform Tools en la PC</div>
    <div class="text-block">
        Para que la computadora pueda comunicarse con el teléfono, se necesitan las herramientas oficiales de depuración:
    </div>
    <ul class="step-list">
        <li>Descargar el archivo comprimido de <strong>SDK Platform-Tools para Windows</strong> desde el sitio web de Android Developers.</li>
        <li>Extraer el contenido del archivo <span class="highlight-text">.zip</span> en una ruta accesible (por ejemplo: <span class="path">C:\Users\TuUsuario\Desktop\Programas\platform-tools</span>).</li>
        <li>Abrir la carpeta <span class="highlight-text">platform-tools</span>.</li>
        <li>Hacer clic en la <strong>barra de direcciones</strong> de Windows, escribir <span class="highlight-text">cmd</span> y presionar <strong>Enter</strong>. Se abrirá la terminal de comandos en esa ubicación.</li>
    </ul>

    <!-- Paso 2 -->
    <div class="sub-section-title" style="margin-top:16px;"><span class="step">Paso 2</span> Activar la Depuración USB en el Celular</div>
    <div class="text-block">
        Este paso habilita al teléfono para aceptar comandos ADB desde la computadora:
    </div>
    <ul class="step-list">
        <li>En el dispositivo, ingresar a <strong>Ajustes</strong> → <strong>Sobre el teléfono</strong> y presionar <strong>7 veces</strong> sobre <strong>Versión de OS</strong> (o versión de MIUI/HyperOS) hasta que aparezca el mensaje: <span class="highlight-text">«Ahora eres desarrollador»</span>.</li>
        <li>Volver al menú principal de <strong>Ajustes</strong> y navegar hasta <strong>Ajustes adicionales</strong> → <strong>Opciones de desarrollador</strong>.</li>
        <li>Activar el interruptor de <strong>Depuración USB</strong> (USB Debugging).</li>
        <li>En dispositivos Xiaomi/Poco, activar también <strong>Depuración USB (Ajustes de seguridad)</strong>. El sistema mostrará advertencias; aceptar para confirmar.</li>
    </ul>

    <!-- Paso 3 -->
    <div class="sub-section-title" style="margin-top:16px;"><span class="step">Paso 3</span> Verificación de Conexión</div>
    <ul class="step-list">
        <li>Conectar el teléfono a la computadora con un cable USB.</li>
        <li>En la terminal, ejecutar:</li>
    </ul>
    <div class="cmd-block">
        <span class="prompt">C:\platform-tools&gt;</span> <span class="cmd">adb devices</span>
    </div>
    <ul class="step-list">
        <li>En el teléfono, aparecerá un diálogo solicitando permitir la depuración USB. Seleccionar <strong>"Permitir siempre desde esta computadora"</strong> y presionar <strong>Aceptar</strong>.</li>
        <li>Ejecutar nuevamente <span class="highlight-text">adb devices</span>. Si la conexión es correcta, se mostrará el número de serie del dispositivo seguido de <span class="highlight-text">device</span>.</li>
    </ul>
    <div class="text-block" style="padding-top:10px;">
        Para acceder al entorno del teléfono, ejecutar:
    </div>
    <div class="cmd-block">
        <span class="prompt">C:\platform-tools&gt;</span> <span class="cmd">adb shell</span>
    </div>
    <div class="text-block">
        El indicador cambiará al nombre en clave del procesador (ej: <span class="highlight-text">klee:/ $</span>), confirmando que se está operando dentro del sistema del dispositivo.
    </div>

    <hr class="divider">

    <!-- ===== SECCIÓN 2: GESTIÓN DE PAQUETES ===== -->
    <div class="section-title"><span class="icon">📦</span> Gestión de Aplicaciones del Sistema mediante ADB</div>

    <!-- Dispositivo de referencia -->
    <div class="sub-section-title">Dispositivo de Referencia</div>
    <div class="tech-specs">
        <div class="spec-line"><span class="label">Modelo:</span> <span class="value">Xiaomi Poco X8 Pro (2511FPC34G)</span></div>
        <div class="spec-line"><span class="label">Firmware:</span> <span class="value">HyperOS v3.0.303.0</span> <span class="label">(Placa:</span> <span class="value-highlight">klee</span><span class="label">)</span></div>
        <div class="spec-line"><span class="label">Objetivo:</span> <span class="value">Reducir aplicaciones preinstaladas, componentes publicitarios y servicios de telemetría sin modificar la partición del sistema ni requerir acceso root.</span></div>
    </div>

    <!-- 1. Fluidez Visual -->
    <div class="sub-section-title" style="margin-top:18px;">1. Ajuste de Animaciones del Sistema</div>
    <div class="text-block">
        Los siguientes comandos reducen la escala de las animaciones del sistema a <strong>0.5x</strong>, lo que puede disminuir el tiempo de renderizado de las transiciones:
    </div>
    <div class="cmd-block">
        <span class="prompt">klee:/ $</span> <span class="cmd">settings put global window_animation_scale 0.5</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">settings put global transition_animation_scale 0.5</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">settings put global animator_duration_scale 0.5</span>
    </div>
    <div class="text-block success-text">✔ Animaciones configuradas a 0.5x</div>

    <hr class="divider-light">

    <!-- 2. Publicidad -->
    <div class="sub-section-title">2. Componentes de Publicidad y Telemetría</div>
    <div class="text-block">
        Paquetes utilizados por Xiaomi para mostrar contenido promocional y recopilar datos de uso:
    </div>
    <div class="cmd-block">
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.miui.msa.global</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.miui.analytics</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.xiaomi.mipicks</span>
    </div>
    <div class="pkg-desc">
        <span class="pkg-name">msa.global</span> <span class="pkg-info">→ Servicio utilizado para mostrar recomendaciones y contenido promocional</span>
        <br>
        <span class="pkg-name">analytics</span> <span class="pkg-info">→ Módulo de recopilación de datos de uso y telemetría</span>
        <br>
        <span class="pkg-name">mipicks</span> <span class="pkg-info">→ Tienda de aplicaciones alternativa que puede mostrar notificaciones y recomendaciones</span>
    </div>

    <hr class="divider-light">

    <!-- 3. Multimedia -->
    <div class="sub-section-title">3. Aplicaciones Multimedia Opcionales</div>
    <div class="text-block">
        Aplicaciones multimedia preinstaladas por el fabricante:
    </div>
    <div class="cmd-block">
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.miui.videoplayer</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.miui.player</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.miui.weather.global</span>
    </div>
    <div class="pkg-desc">
        <span class="pkg-name">videoplayer</span> <span class="pkg-info">→ Reproductor Mi Video</span>
        <br>
        <span class="pkg-name">player</span> <span class="pkg-info">→ Reproductor Mi Música</span>
        <br>
        <span class="pkg-name">weather.global</span> <span class="pkg-info">→ Aplicación de clima (puede no estar presente en todos los firmwares)</span>
    </div>

    <hr class="divider-light">

    <!-- 4. Servicios de Facebook -->
    <div class="sub-section-title">4. Servicios de Facebook Preinstalados</div>
    <div class="text-block">
        Componentes de Facebook que pueden ejecutarse en segundo plano y consumir recursos del sistema, incluso si las aplicaciones principales de Facebook no están instaladas:
    </div>
    <div class="cmd-block">
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.facebook.appmanager</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.facebook.services</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.facebook.system</span>
    </div>
    <div class="text-block muted-text">✔ Puede reducir la cantidad de procesos en segundo plano.</div>

    <hr class="divider-light">

    <!-- 5. Servicios Adicionales -->
    <div class="sub-section-title">5. Servicios de Soporte Opcionales</div>
    <div class="text-block">
        Componentes del sistema que no son esenciales para el funcionamiento diario:
    </div>
    <div class="cmd-block">
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.miui.extraphoto</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.miui.bugreport</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.miui.miservice</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.miui.hybrid</span>
        <br>
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.android.browser</span>
    </div>
    <div class="pkg-desc">
        <span class="pkg-name">extraphoto</span> <span class="pkg-info">→ Procesamiento adicional de imágenes por IA en la galería</span>
        <br>
        <span class="pkg-name">bugreport</span> <span class="pkg-info">→ Herramienta de reporte de errores</span>
        <br>
        <span class="pkg-name">miservice</span> <span class="pkg-info">→ Servicio de feedback y soporte de Xiaomi</span>
        <br>
        <span class="pkg-name">hybrid</span> <span class="pkg-info">→ Navegador híbrido (puede no estar presente en todos los firmwares)</span>
        <br>
        <span class="pkg-name">android.browser</span> <span class="pkg-info">→ Navegador web (puede no estar presente en todos los firmwares)</span>
    </div>

    <hr class="divider-light">

    <!-- 6. Motores de Búsqueda -->
    <div class="sub-section-title">6. Desactivación de Componentes de Búsqueda</div>
    <div class="text-block">
        Para deshabilitar la barra de búsqueda en el cajón de aplicaciones y la pantalla lateral izquierda:
    </div>

    <div style="padding:8px 0 4px 0;"><strong style="color:#b3cdde;">Buscador del cajón de aplicaciones:</strong></div>
    <div class="cmd-block">
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.mi.appfinder</span>
    </div>

    <div style="padding:12px 0 4px 0;"><strong style="color:#b3cdde;">Pantalla lateral izquierda (App Vault):</strong></div>
    <div class="cmd-block">
        <span class="prompt">klee:/ $</span> <span class="cmd">pm uninstall -k --user 0 com.mi.globalminusscreen</span>
    </div>

    <!-- ===== TABLA DE RIESGOS ===== -->
    <hr class="divider-light">

    <div class="sub-section-title">Evaluación de Riesgos por Paquete</div>
    <div class="text-block" style="padding-bottom:8px;">
        Los siguientes niveles de riesgo se basan en la experiencia de usuarios y en la función esperada de cada componente. El impacto real puede variar según la versión de firmware y la región.
    </div>

    <table class="risk-table">
        <thead>
            <tr>
                <th>Paquete</th>
                <th>Riesgo</th>
                <th>Descripción</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td class="pkg">com.miui.msa.global</td>
                <td class="risk-low">Bajo</td>
                <td class="desc">Servicio de publicidad; su ausencia no afecta funciones básicas.</td>
            </tr>
            <tr>
                <td class="pkg">com.miui.analytics</td>
                <td class="risk-low">Bajo</td>
                <td class="desc">Telemetría; su ausencia no afecta el rendimiento general.</td>
            </tr>
            <tr>
                <td class="pkg">com.xiaomi.mipicks</td>
                <td class="risk-low">Bajo</td>
                <td class="desc">Tienda alternativa; su ausencia no afecta la tienda principal.</td>
            </tr>
            <tr>
                <td class="pkg">com.miui.videoplayer</td>
                <td class="risk-low">Bajo</td>
                <td class="desc">Reproductor de video; puede reemplazarse por otra app.</td>
            </tr>
            <tr>
                <td class="pkg">com.miui.player</td>
                <td class="risk-low">Bajo</td>
                <td class="desc">Reproductor de música; puede reemplazarse.</td>
            </tr>
            <tr>
                <td class="pkg">com.facebook.*</td>
                <td class="risk-low">Bajo</td>
                <td class="desc">Servicios de Facebook; no afectan funciones del sistema.</td>
            </tr>
            <tr>
                <td class="pkg">com.miui.extraphoto</td>
                <td class="risk-medium">Medio</td>
                <td class="desc">Puede desactivar algunas funciones de IA en la galería.</td>
            </tr>
            <tr>
                <td class="pkg">com.mi.globalminusscreen</td>
                <td class="risk-medium">Medio</td>
                <td class="desc">Puede eliminar widgets y acceso rápido en la pantalla lateral.</td>
            </tr>
            <tr>
                <td class="pkg">com.android.browser</td>
                <td class="risk-medium">Medio</td>
                <td class="desc">Puede afectar enlaces abiertos por defecto; se puede reemplazar.</td>
            </tr>
            <tr>
                <td class="pkg">com.miui.bugreport</td>
                <td class="risk-low">Bajo</td>
                <td class="desc">Herramienta de reporte de errores; su ausencia no es crítica.</td>
            </tr>
            <tr>
                <td class="pkg">com.miui.miservice</td>
                <td class="risk-low">Bajo</td>
                <td class="desc">Servicio de soporte; no afecta funciones diarias.</td>
            </tr>
            <tr>
                <td class="pkg">com.mi.appfinder</td>
                <td class="risk-medium">Medio</td>
                <td class="desc">Elimina la búsqueda en el cajón de apps; puede afectar la experiencia de algunos usuarios.</td>
            </tr>
        </tbody>
    </table>

    <hr class="divider">

    <!-- ===== RECUPERACIÓN ===== -->
    <div class="section-title"><span class="icon">🔄</span> Mecanismo de Restauración</div>
    <div class="note-box" style="border-left-color:#3c8db0;">
        <strong>⏪ Restaurar un paquete desinstalado</strong> sin necesidad de reiniciar el teléfono de fábrica:
        <br><br>
        <span class="cmd-inline">cmd package install-existing NOMBRE_DE_LA_APP</span>
        <br><br>
        <span class="muted-text">Ejemplo:</span> <span class="cmd-inline">cmd package install-existing com.miui.msa.global</span>
    </div>

    <!-- ===== SOLUCIÓN DE PROBLEMAS ===== -->
    <hr class="divider">

    <div class="section-title"><span class="icon">🔧</span> Solución de Problemas Comunes</div>

    <div class="sub-section-title">Mensaje "Failure [not installed for 0]"</div>
    <div class="text-block">
        Es normal que algunos paquetes devuelvan este mensaje. Puede deberse a:
    </div>
    <ul class="step-list">
        <li>El paquete no está presente en el firmware regional.</li>
        <li>Fue desinstalado previamente.</li>
        <li>No está activo en el perfil de usuario por defecto.</li>
    </ul>
    <div class="text-block success-text" style="padding-top:6px;">✔ El proceso continúa sin afectar el resto de la desinstalación.</div>

    <div class="divider-light"></div>

    <div class="sub-section-title">No aparece el diálogo de depuración USB</div>
    <ul class="step-list">
        <li>Verificar que <strong>ambas</strong> opciones de depuración USB estén activadas.</li>
        <li>Probar con otro cable USB (preferentemente original).</li>
        <li>Reiniciar el servicio ADB: <span class="highlight-text">adb kill-server</span> y luego <span class="highlight-text">adb start-server</span>.</li>
    </ul>

    <div class="divider-light"></div>

    <div class="sub-section-title">"adb devices" no muestra el dispositivo</div>
    <ul class="step-list">
        <li>Verificar la conexión física del cable.</li>
        <li>Probar con otro puerto USB en la computadora.</li>
        <li>En <strong>Opciones de desarrollador</strong>, desactivar y reactivar <strong>Depuración USB</strong>.</li>
        <li>Reiniciar el teléfono y la computadora.</li>
    </ul>

    <div class="divider-light"></div>

    <div class="sub-section-title">Restaurar todas las aplicaciones desinstaladas</div>
    <ul class="step-list">
        <li>El método más sencillo es realizar un <strong>restablecimiento de fábrica</strong> desde los ajustes del dispositivo.</li>
        <li>Alternativamente, se pueden restaurar paquetes individuales con el comando mencionado en la sección de recuperación.</li>
    </ul>

    <!-- ===== FOOTER ===== -->
    <hr class="divider">

    <div class="terminal-footer">
        <div class="footer-left">
            <span>Guía de Debloat · ADB</span>
            <span style="color:#3c8db0;">MIUI / HyperOS</span>
        </div>
        <div class="footer-right">
            ⚠️ Bajo responsabilidad del usuario · Realizar copia de seguridad
        </div>
    </div>

</div>

</body>
</html>
