# Dashboard de Benchmark de Productos Bancarios - Bankaool

Este proyecto es la migración modular y escalable a **React** + **Vite** del antiguo dashboard estático de inteligencia competitiva de servicios financieros de Bankaool. 

Centraliza la oferta de productos financieros de la banca mexicana, permitiendo búsquedas rápidas, comparación dinámica de campos por institución, visualización del estado de los datos (vigente, nuevo, descontinuado), trazabilidad de las fuentes de captura y un sistema de inicio de sesión seguro con **Amazon Cognito**.

---

## 🛠️ Tecnologías

- **React** (v19) - Librería principal para la interfaz de usuario.
- **Vite** (v8) - Herramienta de compilación y servidor de desarrollo.
- **pnpm** (v11) - Gestor de dependencias y paquetes.
- **Lucide React** - Set de iconos vectoriales consistentes y ligeros.

---

## 📂 Estructura del Proyecto

```bash 
├── src/
│   ├── components/          # Vistas modulares y elementos de interfaz
│   │   ├── Topbar.jsx       # Header superior con buscador, tema y exportador
│   │   ├── Sidebar.jsx      # Barra de categorías y años con contadores
│   │   ├── ControlsBar.jsx  # Filtros de segmento, familia, marca y estados
│   │   ├── InicioView.jsx   # Pantalla de bienvenida con KPIs globales
│   │   ├── TablaView.jsx    # Matriz comparativa principal con celdas stateful
│   │   ├── CampoView.jsx    # Comparación de un solo campo entre bancos
│   │   ├── ResumenView.jsx  # Indicadores ejecutivos y matriz de cobertura
│   │   ├── FuentesView.jsx  # Log y estado de las URLs capturadas con IA
│   │   ├── GlosarioView.jsx # Glosario de términos y leyenda de colores
│   │   └── LoginView.jsx    # Pantalla de inicio de sesión moderna y segura
│   ├── data/
│   │   ├── db.json          # Base de datos local descomprimida (cat, prod, campos)
│   │   └── logos.json       # Diccionario de logotipos de instituciones en Base64
│   ├── utils/
│   │   ├── helpers.jsx      # Constantes compartidas y avatar
│   │   └── auth.js          # Cliente Cognito ligero para tokens JWT y retos
│   ├── App.jsx              # Gestor de estado global y enrutador de pestañas
│   ├── index.css            # Hoja de estilos con variables de color y temas (Oscuro/Claro)
│   └── main.jsx             # Punto de entrada de React
├── index.html               # Archivo base HTML
├── package.json             # Manifiesto del proyecto y scripts
└── vite.config.js           # Configuración de Vite
```

---

## 🚀 Instalación y Desarrollo

Sigue estos pasos para ejecutar la aplicación en tu máquina local:

### 1. Instalar dependencias
Usa `pnpm` para instalar todas las dependencias del proyecto:
```bash
pnpm install
```

### 2. Iniciar el servidor de desarrollo
Corre el servidor local de Vite:
```bash
pnpm dev
```
La aplicación estará disponible en [http://localhost:5173/](http://localhost:5173/).

### 3. Compilar para producción
Genera el paquete optimizado y minificado listo para desplegar:
```bash
pnpm run build
```

---

## 🔄 Conexión con el Backend (API) y Cognito

La aplicación está completamente integrada con el backend de microservicios Serverless a través de llamadas de red asíncronas (`fetch`) segurizadas mediante tokens de Cognito.

### Variables de Entorno

Debes configurar el archivo `.env.development` o `.env.production` en la raíz del frontend basándote en la plantilla de `.env.example`:

| Variable | Descripción | Valor Ejemplo |
| :--- | :--- | :--- |
| `VITE_API_URL` | URL base del backend de la API (Serverless Offline o AWS) | `http://localhost:3000` |
| `VITE_LOCAL_TEST_MODE` | Omite Cognito únicamente durante `vite dev`; usar solo con `core-service/local_server.py` | `false` |
| `VITE_COGNITO_USER_POOL_ID` | ID del Pool de Usuarios de Amazon Cognito | `us-east-1_xxxxxxxxx` |
| `VITE_COGNITO_WEB_CLIENT_ID` | ID de Cliente Web público de Cognito (tipo SPA) | `xxxxxxxxxxxxxxxxxxxxxxxxxx` |
| `VITE_COGNITO_REGION` | Región del pool de Cognito | `us-east-1` |

Al iniciar el servidor en local (`pnpm dev`), la aplicación forzará el inicio de sesión a través de Cognito antes de dar acceso al dashboard y adjuntará el ID Token bajo la cabecera `Authorization: Bearer <TOKEN>` en cada consulta al backend.

### Prueba efímera sin MongoDB ni Cognito

Crea `.env.local`:

```dotenv
VITE_API_URL=http://127.0.0.1:3000
VITE_LOCAL_TEST_MODE=true
```

Después inicia `core-service/local_server.py` y ejecuta `pnpm dev`. La aplicación abre directamente la tabla local de BBVA. En **2026 → Cuentas**, el botón **Obtener datos** aparece en grande cuando la categoría está vacía; si ya hay datos, la barra de filtros muestra **Actualizar datos**. Ambos descargan y analizan Libretón básica de BBVA, muestran el estado de ejecución y abren la tabla actualizada con sus alertas de revisión. Las categorías sin soporte muestran **Extracción aún no disponible**. La omisión de Cognito está condicionada tanto a esta variable como al modo de desarrollo de Vite, por lo que no se activa en una compilación de producción.

### Historial por año

Las pestañas **2025** y **2026** muestran mediciones independientes. Los registros importados sin `anio` pertenecen a 2025; las capturas del scraper se guardan en 2026 sin reemplazar la base anterior. Una nueva ejecución actualiza la medición 2026 del producto, no crea otra columna duplicada. Los productos agregados y las revisiones manuales se guardan en el año seleccionado. La selección de año se conserva al recargar la página.

Ejecuta `npm test` para verificar las pestañas de año.
