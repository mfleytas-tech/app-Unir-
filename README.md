<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Plataforma de Curso Flask</title>
    <!-- Carga de Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Configuración de Fuentes y Estilos -->
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f9fc;
            min-height: 100vh;
        }
        .scroll-container {
            max-height: 70vh;
            overflow-y: auto;
        }
        /* Estilos para el botón de envío y el estado */
        .submit-btn {
            transition: all 0.2s;
        }
        .submitted {
            background-color: #3b82f6; /* Azul de Tailwind */
        }
        .scored {
            background-color: #10b981; /* Verde esmeralda de Tailwind */
        }
    </style>
</head>
<body>

    <div id="app" class="min-h-screen flex flex-col">
        <!-- Encabezado y Barra de Usuario -->
        <header class="bg-indigo-600 shadow-lg p-4 text-white">
            <div class="max-w-7xl mx-auto flex justify-between items-center">
                <h1 class="text-2xl font-bold">Introducción a Python y Flask</h1>
                <div class="text-sm bg-indigo-700 p-2 rounded-full flex items-center shadow-md">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-5 h-5 mr-2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 1 1-7.5 0 3.75 3.75 0 0 1 7.5 0ZM4.501 20.25a.75.75 0 0 0 .15-.029 7.576 7.576 0 0 1 14.698 0 .75.75 0 0 0 .15.029h.001A.75.75 0 0 0 21 19.5c0-4.66-3.84-8.472-8.5-8.472S4 14.84 4 19.5a.75.75 0 0 0 .501.75Z" />
                    </svg>
                    <span id="user-id-display">Cargando usuario...</span>
                </div>
            </div>
        </header>

        <!-- Contenido Principal (Dashboard y Módulos) -->
        <main class="flex-grow max-w-7xl mx-auto w-full p-4 md:p-8 grid grid-cols-1 lg:grid-cols-4 gap-6">

            <!-- Columna de Navegación/Dashboard (lg:col-span-1) -->
            <div class="lg:col-span-1 bg-white p-6 rounded-xl shadow-lg h-fit">
                <h2 class="text-xl font-semibold mb-4 text-indigo-700">Módulos del Curso (6 Semanas)</h2>
                <div id="module-nav" class="space-y-3">
                    <!-- Los botones de los módulos se renderizarán aquí -->
                </div>
                <div id="overall-progress" class="mt-6 pt-4 border-t border-gray-200">
                    <h3 class="font-medium text-gray-700 mb-2">Progreso General</h3>
                    <div class="w-full bg-gray-200 rounded-full h-2.5">
                        <div id="progress-bar" class="bg-green-500 h-2.5 rounded-full" style="width: 0%"></div>
                    </div>
                    <p id="progress-text" class="text-sm mt-1 text-gray-500">0% completado</p>
                    <div id="final-grade" class="mt-4 p-3 bg-yellow-100 border-l-4 border-yellow-500 text-yellow-800 rounded-lg hidden">
                        **Nota Final Estimada: <span id="grade-value" class="font-bold"></span>%**
                    </div>
                </div>
            </div>

            <!-- Columna de Contenido del Módulo (lg:col-span-3) -->
            <div class="lg:col-span-3 bg-white p-6 rounded-xl shadow-lg">
                <div id="loading-indicator" class="text-center p-10">
                    <div class="animate-spin rounded-full h-12 w-12 border-b-2 border-indigo-500 mx-auto mb-4"></div>
                    <p class="text-gray-600">Autenticando y cargando el curso...</p>
                </div>

                <div id="module-content" class="hidden">
                    <h2 id="module-title" class="text-2xl font-bold text-gray-800 mb-2"></h2>
                    <p id="module-description" class="text-gray-600 mb-6 border-b pb-4 border-gray-200"></p>

                    <!-- Estructura del Temario -->
                    <div id="module-apartados" class="mb-8">
                        <h3 class="text-xl font-semibold text-indigo-700 mb-3">Contenido Semanal</h3>
                        <!-- Los apartados se renderizarán aquí -->
                    </div>

                    <!-- Actividades del Módulo -->
                    <div id="module-activities">
                        <h3 class="text-xl font-semibold text-red-600 mb-4">Actividades y Evaluación</h3>
                        <!-- Las actividades se renderizarán aquí -->
                    </div>
                </div>

                <!-- Modales para Quiz y Submission (Opcional, se pueden usar secciones dentro del contenido) -->
                <div id="quiz-area" class="mt-6 p-6 border border-yellow-300 bg-yellow-50 rounded-lg hidden">
                    <h4 class="text-lg font-bold text-yellow-800 mb-4">Cuestionario Semanal: Evaluación de Comprensión</h4>
                    <p class="mb-4 text-sm text-gray-700">Responde las siguientes 10 preguntas para evaluar tu comprensión del módulo. ¡Suerte!</p>
                    <div id="quiz-questions" class="space-y-4">
                        <!-- Preguntas del Quiz (simuladas) -->
                    </div>
                    <button onclick="submitQuiz()" class="mt-6 w-full py-2 px-4 bg-yellow-500 text-white font-semibold rounded-lg shadow-md hover:bg-yellow-600 transition duration-300">
                        Enviar Quiz y Ver Puntuación
                    </button>
                    <div id="quiz-result" class="mt-4 p-3 bg-white border border-yellow-500 text-yellow-700 rounded-lg font-bold hidden"></div>
                </div>

                <div id="submission-area" class="mt-6 p-6 border border-blue-300 bg-blue-50 rounded-lg hidden">
                    <h4 id="submission-title" class="text-lg font-bold text-blue-800 mb-4">Entrega de Laboratorio/Proyecto</h4>
                    <label for="submission-content" class="block text-sm font-medium text-gray-700 mb-2">Pega tu código, enlace al repositorio (GitHub) o URL de despliegue:</label>
                    <textarea id="submission-content" rows="6" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 p-3" placeholder="Por ejemplo: 'Mi repositorio está en https://github.com/.../lab4'"></textarea>
                    <button id="submit-button" onclick="submitActivity()" class="mt-4 w-full py-2 px-4 bg-blue-500 text-white font-semibold rounded-lg shadow-md hover:bg-blue-600 transition duration-300">
                        Enviar Entrega
                    </button>
                    <div id="submission-status" class="mt-4 p-3 bg-white border text-gray-700 rounded-lg hidden"></div>
                </div>

                <div id="grade-display-area" class="mt-6 p-6 border border-green-300 bg-green-50 rounded-lg hidden">
                    <h4 class="text-lg font-bold text-green-800 mb-4">Tu Calificación</h4>
                    <p class="text-green-700">Tu entrega ha sido calificada. **Nota:** <span id="activity-score" class="text-xl font-extrabold"></span>%</p>
                    <p class="text-sm mt-2 text-gray-600">Comentarios del Profesor (simulado): "Excelente uso de los modelos ORM. Asegúrate de incluir la validación de formularios en la próxima entrega."</p>
                </div>
            </div>
        </main>
    </div>

    <!-- Firebase SDKs -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, setDoc, getDoc, onSnapshot, collection, query, where, updateDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { setLogLevel } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Establecer nivel de log para depuración de Firestore
        setLogLevel('debug');

        // Variables globales (proporcionadas por el entorno de Canvas)
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : null;
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        let db, auth;
        let userId = null;
        let courseStructure = null;
        let userProgress = {};
        let currentModuleIndex = 0;
        let isAuthReady = false;

        // --- Estructura Base del Curso (Hardcodeada según la planificación) ---
        const COURSE_ID = 'flask_intro_v1';
        const BASE_COURSE_STRUCTURE = {
            title: "Introducción al Desarrollo Web con Python y Flask",
            weeks: [
                {
                    week: 1, title: "Fundamentos de Python para Desarrollo Web",
                    apartados: ["Repaso de estructuras de control y tipos de datos.", "Entorno virtual y `pip`.", "Introducción a la programación orientada a objetos (POO) en Python."],
                    activities: [{ id: 'lab1', type: 'lab', name: 'Laboratorio 1: Configuración de Entorno e POO', weight: 10 }]
                },
                {
                    week: 2, title: "Introducción a Flask y Protocolo HTTP",
                    apartados: ["Arquitectura web y protocolo HTTP.", "Instalación y configuración de un proyecto Flask.", "Definición de rutas (`@app.route`) y funciones de vista."],
                    activities: [
                        { id: 'quiz1', type: 'quiz', name: 'Cuestionario 1: HTTP y Rutas', weight: 5 },
                        { id: 'lab2', type: 'lab', name: 'Laboratorio 2: Creación de Vistas Básicas', weight: 10 }
                    ]
                },
                {
                    week: 3, title: "Renderizado de Contenido y Jinja",
                    apartados: ["Introducción al motor de plantillas Jinja2.", "Uso de variables, estructuras de control y herencia de plantillas.", "Estilización básica con Tailwind CSS (Integración)."],
                    activities: [
                        { id: 'lab3', type: 'lab', name: 'Laboratorio 3: Integrando Formularios y Jinja', weight: 10 }
                    ]
                },
                {
                    week: 4, title: "Bases de Datos con SQLAlchemy",
                    apartados: ["Conceptos básicos de bases de datos relacionales (Modelo ORM).", "Configuración de SQLAlchemy con Flask.", "Definición de modelos de datos y migración inicial."],
                    activities: [
                        { id: 'quiz2', type: 'quiz', name: 'Cuestionario 2: SQL, NoSQL y ORM', weight: 5 },
                        { id: 'lab4', type: 'lab', name: 'Laboratorio 4: Definición de Modelos ORM', weight: 10 }
                    ]
                },
                {
                    week: 5, title: "Implementación de Funcionalidades CRUD",
                    apartados: ["Implementación de la función Create (Formularios).", "Implementación de Read (Consulta y listado de datos).", "Implementación de Update y Delete."],
                    activities: [
                        { id: 'lab5', type: 'lab', name: 'Laboratorio 5: CRUD Completo (Parte I)', weight: 10 }
                    ]
                },
                {
                    week: 6, title: "Seguridad, Despliegue y Prácticas Avanzadas",
                    apartados: ["Manejo de sesiones y autenticación de usuarios.", "Prevención de vulnerabilidades de seguridad comunes.", "Introducción al despliegue (Heroku o Railway).", "Siguientes pasos (APIs, Blueprints)."],
                    activities: [
                        { id: 'quiz3', type: 'quiz', name: 'Cuestionario 3: Seguridad y Despliegue', weight: 5 },
                        { id: 'final_project', type: 'project', name: 'Proyecto Final: Aplicación Web CRUD', weight: 35 }
                    ]
                }
            ],
            // 10 preguntas simuladas para todos los quizzes (por simplicidad)
            quiz_questions: [
                { q: "¿Qué protocolo utiliza la web para la comunicación?", options: ["FTP", "SMTP", "HTTP", "DNS"], correct: 2 },
                { q: "¿Cuál es el gestor de dependencias de Python estándar?", options: ["npm", "composer", "pip", "yarn"], correct: 2 },
                { q: "¿Qué significa el acrónimo CRUD?", options: ["Code, Run, Upload, Download", "Create, Read, Update, Delete", "Compile, Route, Unit, Deploy", "Client, Router, URL, Database"], correct: 1 },
                { q: "¿Qué motor de plantillas utiliza Flask?", options: ["Blade", "Handlebars", "Jinja2", "EJS"], correct: 2 },
                { q: "¿Qué método HTTP se usa para enviar datos de formulario que modifican el servidor?", options: ["GET", "PUT", "DELETE", "POST"], correct: 3 },
                { q: "¿Qué es un ORM?", options: ["Online Resource Manager", "Object-Relational Mapping", "Optimized Route Module", "Output Render Method"], correct: 1 },
                { q: "¿Cuál es una vulnerabilidad común de seguridad web?", options: ["CSRF", "TCP/IP", "DNS resolution", "CSS injection"], correct: 0 },
                { q: "¿Qué es un 'entorno virtual' en Python?", options: ["Una máquina virtual", "Un servidor web", "Un directorio aislado para dependencias del proyecto", "Una red privada"], correct: 2 },
                { q: "¿Qué capa maneja Flask?", options: ["Front-end", "Back-end", "Database", "Networking"], correct: 1 },
                { q: "¿Qué librería se usa comúnmente en Flask para interactuar con bases de datos relacionales?", options: ["Django ORM", "Requests", "SQLAlchemy", "Panda"], correct: 2 }
            ]
        };

        // --- Funciones de Utilidad (para TTS, requeridas por la plataforma) ---
        function base64ToArrayBuffer(base64) {
            const binaryString = atob(base64);
            const len = binaryString.length;
            const bytes = new Uint8Array(len);
            for (let i = 0; i < len; i++) {
                bytes[i] = binaryString.charCodeAt(i);
            }
            return bytes.buffer;
        }

        function pcmToWav(pcm16, sampleRate = 24000) {
            const numChannels = 1;
            const bytesPerSample = 2; // Int16
            const blockAlign = numChannels * bytesPerSample;
            const byteRate = sampleRate * blockAlign;
            const dataSize = pcm16.byteLength;

            const buffer = new ArrayBuffer(44 + dataSize);
            const view = new DataView(buffer);

            // RIFF chunk
            writeString(view, 0, 'RIFF');
            view.setUint32(4, 36 + dataSize, true);
            writeString(view, 8, 'WAVE');

            // FMT chunk
            writeString(view, 12, 'fmt ');
            view.setUint33(16, 16, true); // Sub-chunk size
            view.setUint16(20, 1, true);  // Audio format (1 = PCM)
            view.setUint16(22, numChannels, true);
            view.setUint33(24, sampleRate, true);
            view.setUint33(28, byteRate, true);
            view.setUint16(32, blockAlign, true);
            view.setUint16(34, 16, true); // Bits per sample

            // DATA chunk
            writeString(view, 36, 'data');
            view.setUint33(40, dataSize, true);

            // Write PCM data
            for (let i = 0; i < pcm16.length; i++) {
                view.setInt16(44 + i * 2, pcm16[i], true);
            }

            return new Blob([buffer], { type: 'audio/wav' });

            function writeString(view, offset, string) {
                for (let i = 0; i < string.length; i++) {
                    view.setUint8(offset + i, string.charCodeAt(i));
                }
            }
        }

        // --- Funciones de Firestore ---

        /**
         * Inicializa la estructura del curso en la colección pública si no existe.
         */
        async function initializeCourseStructure() {
            const courseDocRef = doc(db, 'artifacts', appId, 'public', 'data', 'course_structure', COURSE_ID);
            try {
                const docSnap = await getDoc(courseDocRef);
                if (!docSnap.exists()) {
                    console.log("Inicializando estructura del curso...");
                    await setDoc(courseDocRef, BASE_COURSE_STRUCTURE);
                    courseStructure = BASE_COURSE_STRUCTURE;
                } else {
                    courseStructure = docSnap.data();
                }
            } catch (error) {
                console.error("Error al inicializar la estructura del curso:", error);
            }
        }

        /**
         * Escucha los cambios en el progreso del usuario.
         */
        function setupProgressListener() {
            if (!userId) return;

            const progressDocRef = doc(db, 'artifacts', appId, 'users', userId, 'course_progress', COURSE_ID);

            onSnapshot(progressDocRef, (docSnap) => {
                if (docSnap.exists()) {
                    userProgress = docSnap.data().activities || {};
                } else {
                    userProgress = {};
                }
                console.log("Progreso actualizado:", userProgress);
                renderModuleNavigation();
                renderModuleContent(currentModuleIndex);
                updateOverallProgress();
            }, (error) => {
                console.error("Error al escuchar el progreso del usuario:", error);
            });
        }

        /**
         * Actualiza el progreso general del curso.
         */
        function updateOverallProgress() {
            const totalActivities = courseStructure.weeks.flatMap(w => w.activities).length;
            let completedActivities = 0;
            let totalWeight = 0;
            let earnedPoints = 0;

            courseStructure.weeks.forEach(week => {
                week.activities.forEach(activity => {
                    totalWeight += activity.weight;
                    const status = userProgress[activity.id]?.status || 'pending';
                    const score = userProgress[activity.id]?.score || 0;

                    if (status === 'scored' || (status === 'submitted' && activity.type === 'quiz')) {
                        completedActivities++;
                        earnedPoints += score * activity.weight / 100; // Asume score es de 0 a 100
                    }
                });
            });

            // Cálculo del progreso visual (cuantas actividades se han entregado/completado)
            const submissionCount = Object.values(userProgress).filter(a => a.status === 'submitted' || a.status === 'scored').length;
            const visualProgress = Math.round((submissionCount / totalActivities) * 100);

            // Cálculo de la nota final (Ponderada)
            const finalGradePercentage = totalWeight > 0 ? Math.round((earnedPoints / totalWeight) * 100) : 0;

            document.getElementById('progress-bar').style.width = `${visualProgress}%`;
            document.getElementById('progress-text').textContent = `${visualProgress}% completado (${submissionCount} de ${totalActivities} actividades entregadas).`;
            
            const finalGradeEl = document.getElementById('final-grade');
            document.getElementById('grade-value').textContent = finalGradePercentage;

            // Mostrar nota final si hay un porcentaje significativo de avance
            if (visualProgress > 70) {
                finalGradeEl.classList.remove('hidden');
            } else {
                finalGradeEl.classList.add('hidden');
            }
        }


        /**
         * Guarda el progreso de una actividad.
         * @param {string} activityId ID de la actividad (e.g., 'quiz1', 'lab3')
         * @param {string} status Estado de la actividad ('submitted', 'scored')
         * @param {string} submission Contenido de la entrega (código, enlace)
         * @param {number} score Puntuación (solo para quiz o si el profesor califica)
         */
        async function saveActivityProgress(activityId, status, submission = null, score = null) {
            if (!userId) {
                console.error("Usuario no autenticado.");
                return;
            }

            const progressDocRef = doc(db, 'artifacts', appId, 'users', userId, 'course_progress', COURSE_ID);

            const updateData = {
                [`activities.${activityId}.status`]: status,
                [`activities.${activityId}.timestamp`]: new Date().toISOString()
            };

            if (submission !== null) {
                updateData[`activities.${activityId}.submission`] = submission;
            }
            if (score !== null) {
                updateData[`activities.${activityId}.score`] = score;
            }

            try {
                // Actualiza el documento de progreso del usuario. Si no existe, lo crea.
                await setDoc(progressDocRef, { activities: { [activityId]: updateData } }, { merge: true });
                console.log(`Progreso de actividad ${activityId} guardado con éxito.`);
            } catch (error) {
                console.error("Error al guardar el progreso de la actividad:", error);
            }
        }

        // --- Funciones de Renderizado ---

        /**
         * Renderiza la navegación lateral de los módulos.
         */
        function renderModuleNavigation() {
            const navEl = document.getElementById('module-nav');
            navEl.innerHTML = '';
            if (!courseStructure) return;

            courseStructure.weeks.forEach((module, index) => {
                const isActive = index === currentModuleIndex;
                const progressIndicator = module.activities.every(act => userProgress[act.id]?.status === 'scored' || (act.type === 'quiz' && userProgress[act.id]?.status === 'submitted')) ? '✅' : '⏳';

                const buttonClass = isActive
                    ? 'bg-indigo-500 text-white shadow-md'
                    : 'bg-gray-100 text-gray-700 hover:bg-gray-200';

                const button = `
                    <button onclick="changeModule(${index})" class="w-full text-left p-3 rounded-lg font-medium transition duration-200 ${buttonClass} flex justify-between items-center">
                        <span class="truncate">${module.week}. ${module.title}</span>
                        <span class="text-sm ml-2">${progressIndicator}</span>
                    </button>
                `;
                navEl.insertAdjacentHTML('beforeend', button);
            });
        }

        /**
         * Renderiza el contenido del módulo seleccionado.
         */
        function renderModuleContent(index) {
            const module = courseStructure.weeks[index];
            if (!module) return;

            // 1. Ocultar/Mostrar áreas específicas
            document.getElementById('quiz-area').classList.add('hidden');
            document.getElementById('submission-area').classList.add('hidden');
            document.getElementById('grade-display-area').classList.add('hidden');

            // 2. Título y descripción
            document.getElementById('module-title').textContent = `Semana ${module.week}: ${module.title}`;
            document.getElementById('module-description').textContent = `Esta semana cubre: ${module.apartados.join(' | ')}`;

            // 3. Apartados
            const apartadosEl = document.getElementById('module-apartados');
            apartadosEl.innerHTML = `
                <h3 class="text-xl font-semibold text-indigo-700 mb-3">Contenido Semanal</h3>
                <ul class="list-disc list-inside space-y-2 pl-4">
                    ${module.apartados.map(a => `<li class="text-gray-700">${a}</li>`).join('')}
                </ul>
            `;

            // 4. Actividades
            const activitiesEl = document.getElementById('module-activities');
            activitiesEl.innerHTML = `
                <h3 class="text-xl font-semibold text-red-600 mb-4">Actividades y Evaluación</h3>
            `;

            module.activities.forEach(activity => {
                const progress = userProgress[activity.id] || { status: 'pending', score: 0 };
                const isQuiz = activity.type === 'quiz';
                const buttonColor = progress.status === 'scored' ? 'bg-green-500 hover:bg-green-600' : 
                                    progress.status === 'submitted' ? 'bg-blue-500 hover:bg-blue-600' :
                                    'bg-red-500 hover:bg-600';
                
                let actionText = isQuiz ? 'Tomar Quiz' : 'Subir Entrega';
                if (progress.status === 'submitted') actionText = 'Pendiente de Calificación';
                if (progress.status === 'scored') actionText = `Calificado: ${progress.score}%`;

                const activityCard = `
                    <div class="p-4 mb-4 border rounded-lg shadow-sm flex flex-col md:flex-row justify-between items-start md:items-center">
                        <div>
                            <p class="font-medium text-lg text-gray-900">${activity.name}</p>
                            <p class="text-sm text-gray-500">Peso: ${activity.weight}% | Tipo: ${isQuiz ? 'Cuestionario' : activity.type === 'lab' ? 'Laboratorio' : 'Proyecto'}</p>
                        </div>
                        <button 
                            onclick="openActivity('${activity.id}', '${activity.name}', ${isQuiz})"
                            class="mt-3 md:mt-0 py-2 px-4 rounded-lg text-white font-semibold transition duration-300 ${buttonColor} ${progress.status === 'scored' || progress.status === 'submitted' ? 'cursor-default' : ''}">
                            ${actionText}
                        </button>
                    </div>
                `;
                activitiesEl.insertAdjacentHTML('beforeend', activityCard);
            });

            document.getElementById('loading-indicator').classList.add('hidden');
            document.getElementById('module-content').classList.remove('hidden');
        }

        // --- Funciones de Interacción del Estudiante ---

        /**
         * Cambia el módulo activo.
         * @param {number} index Índice del módulo (0-5)
         */
        window.changeModule = (index) => {
            currentModuleIndex = index;
            renderModuleNavigation();
            renderModuleContent(index);
        };

        let currentActivityId = null;

        /**
         * Abre la interfaz para una actividad (Quiz o Submission).
         * @param {string} activityId ID de la actividad
         * @param {string} activityName Nombre de la actividad
         * @param {boolean} isQuiz Indica si es un Quiz
         */
        window.openActivity = (activityId, activityName, isQuiz) => {
            currentActivityId = activityId;

            // 1. Ocultar todas las áreas
            document.getElementById('quiz-area').classList.add('hidden');
            document.getElementById('submission-area').classList.add('hidden');
            document.getElementById('grade-display-area').classList.add('hidden');

            const progress = userProgress[activityId];

            if (progress && progress.status === 'scored') {
                 // Si ya está calificado, mostrar nota
                document.getElementById('activity-score').textContent = progress.score;
                document.getElementById('grade-display-area').classList.remove('hidden');
            } else if (isQuiz) {
                // Mostrar Quiz
                renderQuizQuestions();
                document.getElementById('quiz-area').classList.remove('hidden');
                document.getElementById('quiz-result').classList.add('hidden');
            } else {
                // Mostrar Submission (Lab/Project)
                document.getElementById('submission-title').textContent = `Entrega: ${activityName}`;
                document.getElementById('submission-area').classList.remove('hidden');
                document.getElementById('submission-status').classList.add('hidden');
                document.getElementById('submission-content').value = progress?.submission || '';

                if (progress && progress.status === 'submitted') {
                    document.getElementById('submission-status').textContent = 'Estado: Entregado. Pendiente de calificación.';
                    document.getElementById('submission-status').classList.remove('hidden');
                    document.getElementById('submit-button').textContent = 'Actualizar Entrega';
                    document.getElementById('submit-button').classList.remove('bg-blue-500');
                    document.getElementById('submit-button').classList.add('bg-gray-500');
                } else {
                    document.getElementById('submit-button').textContent = 'Enviar Entrega';
                    document.getElementById('submit-button').classList.remove('bg-gray-500');
                    document.getElementById('submit-button').classList.add('bg-blue-500');
                }
            }
        };

        /**
         * Renderiza las preguntas del quiz (simuladas).
         */
        function renderQuizQuestions() {
            const questionsEl = document.getElementById('quiz-questions');
            questionsEl.innerHTML = '';
            
            courseStructure.quiz_questions.slice(0, 10).forEach((q, index) => {
                const questionHtml = `
                    <div class="p-3 bg-white border border-yellow-200 rounded-lg">
                        <p class="font-semibold text-gray-800 mb-2">P${index + 1}: ${q.q}</p>
                        <div class="space-y-1">
                            ${q.options.map((opt, optIndex) => `
                                <label class="flex items-center text-sm text-gray-700">
                                    <input type="radio" name="q${index}" value="${optIndex}" class="text-yellow-500 focus:ring-yellow-500">
                                    <span class="ml-2">${opt}</span>
                                </label>
                            `).join('')}
                        </div>
                    </div>
                `;
                questionsEl.insertAdjacentHTML('beforeend', questionHtml);
            });
        }

        /**
         * Maneja el envío del Quiz.
         */
        window.submitQuiz = () => {
            let correctAnswers = 0;
            const quizQuestions = courseStructure.quiz_questions.slice(0, 10);

            quizQuestions.forEach((q, index) => {
                const selected = document.querySelector(`input[name="q${index}"]:checked`);
                if (selected && parseInt(selected.value) === q.correct) {
                    correctAnswers++;
                }
            });

            const score = Math.round((correctAnswers / quizQuestions.length) * 100);
            const resultEl = document.getElementById('quiz-result');

            resultEl.textContent = `Resultado: Acertaste ${correctAnswers} de ${quizQuestions.length}. Tu puntuación es ${score}%.`;
            resultEl.classList.remove('hidden');

            // Guardar el progreso (status 'scored' para quizzes, ya que se autocalifican)
            saveActivityProgress(currentActivityId, 'scored', 'Quiz Autocalificado', score);

            // Cerrar la interfaz del quiz
            setTimeout(() => {
                document.getElementById('quiz-area').classList.add('hidden');
                renderModuleContent(currentModuleIndex);
            }, 3000);
        };

        /**
         * Maneja el envío de Laboratorios y Proyectos.
         */
        window.submitActivity = () => {
            const submissionContent = document.getElementById('submission-content').value.trim();
            const submitBtn = document.getElementById('submit-button');
            const statusEl = document.getElementById('submission-status');

            if (!submissionContent) {
                statusEl.textContent = 'Error: Debes pegar el contenido o un enlace.';
                statusEl.classList.remove('hidden');
                statusEl.classList.add('bg-red-100', 'border-red-300');
                statusEl.classList.remove('bg-white', 'border-gray-300');
                return;
            }

            // Deshabilitar botón
            submitBtn.disabled = true;
            submitBtn.textContent = 'Enviando...';
            submitBtn.classList.add('opacity-50');

            // Guardar el progreso (status 'submitted')
            saveActivityProgress(currentActivityId, 'submitted', submissionContent).then(() => {
                statusEl.textContent = '¡Entrega enviada! Pendiente de calificación por el profesor.';
                statusEl.classList.remove('hidden', 'bg-red-100', 'border-red-300');
                statusEl.classList.add('bg-blue-100', 'border-blue-300');
                
                // Actualizar UI
                submitBtn.disabled = false;
                submitBtn.textContent = 'Actualizar Entrega';
                submitBtn.classList.remove('opacity-50', 'bg-blue-500');
                submitBtn.classList.add('bg-gray-500');
                
                // Re-renderizar contenido del módulo para actualizar el estado del botón
                renderModuleContent(currentModuleIndex);
            }).catch(e => {
                console.error(e);
                statusEl.textContent = 'Error al enviar. Intenta de nuevo.';
                submitBtn.disabled = false;
                submitBtn.textContent = 'Error de Envío';
            });
        };

        // --- Inicialización ---

        /**
         * Inicializa la aplicación y la autenticación.
         */
        async function initApp() {
            if (!firebaseConfig) {
                document.getElementById('loading-indicator').innerHTML = '<p class="text-red-600">ERROR: Configuración de Firebase no disponible. No se puede iniciar la aplicación.</p>';
                return;
            }

            try {
                const app = initializeApp(firebaseConfig);
                db = getFirestore(app);
                auth = getAuth(app);

                // 1. Autenticación (con token personalizado o anónima)
                if (initialAuthToken) {
                    await signInWithCustomToken(auth, initialAuthToken);
                } else {
                    await signInAnonymously(auth);
                }

                // 2. Establecer el listener de estado de autenticación
                onAuthStateChanged(auth, async (user) => {
                    if (user) {
                        userId = user.uid;
                        document.getElementById('user-id-display').textContent = `Usuario ID: ${userId.substring(0, 8)}...`;
                        isAuthReady = true;

                        // 3. Inicializar la estructura del curso (pública)
                        await initializeCourseStructure();

                        // 4. Iniciar la escucha del progreso del usuario (privada)
                        setupProgressListener();

                        // 5. Renderizar el curso
                        renderModuleNavigation();
                        renderModuleContent(currentModuleIndex);
                    } else {
                        // Si por alguna razón la autenticación falla, mostrar error
                        document.getElementById('loading-indicator').innerHTML = '<p class="text-red-600">Error de autenticación. Por favor, recarga la página.</p>';
                        isAuthReady = true; // Permite que el control de flujo continúe
                    }
                });

            } catch (error) {
                console.error("Error grave durante la inicialización de Firebase:", error);
                document.getElementById('loading-indicator').innerHTML = `<p class="text-red-600">ERROR de Inicialización: ${error.message}</p>`;
            }
        }

        window.onload = initApp;

    </script>
</body>
</html>
