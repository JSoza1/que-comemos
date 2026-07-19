# 🍽️ ¿Qué comemos hoy? - Proyecto Despensa & Recetario Reactivo

Este proyecto es una aplicación web responsiva (Single Page Application) autocontenida y optimizada para celulares. Su objetivo es ayudar al usuario a gestionar los ingredientes que tiene en casa y determinar de forma reactiva qué comidas puede preparar en tiempo real según la disponibilidad actual.

---

## 📌 Arquitectura del Proyecto

Para mantener el proyecto extremadamente ágil, portable y fácil de desplegar en dispositivos locales sin dependencias de backend, toda la aplicación se encuentra encapsulada en **un solo archivo**:
* 📄 **[index.html](file:///c:/Users/Joneh/Documents/Dev%204/QueComemos/index.html)**: Contiene la estructura HTML5 semántica, la hoja de estilos CSS3 en el `<head>` y toda la lógica JavaScript de control de estado en el bloque `<script>` final.

### Stack Tecnológico
* **Estructura:** HTML5 Semántico.
* **Estilo:** CSS3 customizado con variables nativas para el sistema de diseño (modo oscuro, gradientes, sombras).
* **Interactividad y Reactividad:** Vanilla JavaScript (ES6+).
* **Iconografía:** FontAwesome 6 (vía CDN en línea).
* **Tipografía:** Google Fonts (`Outfit`).

---

## 📊 Modelo de Datos (State)

El estado global de la aplicación se gestiona en memoria mediante variables reactivas y se sincroniza en tiempo real con `localStorage` para garantizar la persistencia del usuario.

### 1. Ingredientes
Cada ingrediente sigue la siguiente interfaz:
```typescript
interface Ingredient {
  id: string;        // Identificador único (timestamp al crear)
  name: string;      // Nombre visible del ingrediente (ej. "Huevos")
  available: boolean; // Indica si el usuario dispone del ingrediente hoy (true/false)
  category: string;  // Categoría: "verduras" | "heladera" | "frezzer" | "alacena" (se muestra como "Despensa")
}
```

### 2. Comidas (Recetas)
Cada comida sigue la siguiente interfaz:
```typescript
interface Recipe {
  id: string;              // Identificador único (ej. "r1" o timestamp)
  name: string;            // Nombre de la comida (ej. "Pasta con Tomate")
  ingredients: string[];   // Lista de IDs de ingredientes requeridos
  preparation: string;     // Instrucciones paso a paso de preparación
}
```

---

## ⚙️ Mecánicas Clave y Lógica Reactiva

### 1. Sistema de 3 Estados para las Comidas
Cuando el listado de comidas se renderiza en la función `renderRecipes()`, la aplicación analiza los ingredientes requeridos y calcula cuántos están disponibles en la despensa actual. Dependiendo del resultado, se le asigna una clase visual a la tarjeta:

| Estado de Ingredientes | Clase CSS | Estilo Visual | Etiqueta |
| :--- | :--- | :--- | :--- |
| **100% de ingredientes disponibles** | `.can-cook` | Borde izquierdo verde | `¡Disponible!` (Verde) |
| **Algunos disponibles, faltan otros** | `.some-missing` | Borde izquierdo amarillo | `Faltan X` (Amarillo) |
| **0% de ingredientes disponibles** | `.all-missing` | Borde izquierdo rojo | `Falta todo` (Rojo) |

### 2. Cuadrícula de Categorías (2x2) y Creación Contextual
* La sección de ingredientes se organiza mediante una cuadrícula de 2x2 pestañas: **Verduras, Heladera, Freezer y Despensa**.
* Al seleccionar un cuadrante, se activa el listado específico de ingredientes.
* **Formulario dinámico:** El input para ingresar ingredientes se dibuja al final del listado activo. Al dar de alta un ingrediente, se salta el selector de categorías clásico y se asigna **automáticamente** a la pestaña en la que se encuentra posicionado el usuario.

### 3. Persistencia de Estado (`localStorage`)
* **`loadState()`**: Se ejecuta inmediatamente al cargar la página. Si existen datos almacenados, reconstruye el inventario y las comidas. En caso contrario, se inicializa con datos de demostración predefinidos (`DEFAULT_INGREDIENTS` y `DEFAULT_RECIPES`).
* **`saveState()`**: Guarda el estado serializado del inventario de ingredientes, las comidas y el último cuadrante de categoría activo para que la recarga de página no borre el progreso.

### 4. Animaciones y Modales Custom
* **Mensajes Flotantes (Toasts):** Centrados vertical y horizontalmente en el medio de la pantalla con una animación pop-in en escala para una visibilidad premium en teléfonos móviles.
* **Ventana de Detalles:** Popup interactivo que muestra la preparación y deshabilita el posicionamiento absoluto de los badges para evitar superposiciones con los botones de cierre.
* **Eliminación Segura:** Popup personalizado que reemplaza las alertas del navegador para mantener una estética consistente.

---

## 🛠️ Directrices para el Mantenimiento del Código (Futuros Modelos de IA / Desarrolladores)

Si vas a modificar o agregar características a este repositorio, ten en cuenta las siguientes reglas de diseño:

1. **Mantener el Archivo Único:** No separes el CSS ni el JS a archivos externos a menos que el usuario lo solicite expresamente. La portabilidad local y el Live Server dependen del archivo único.
2. **Priorización Móvil (Mobile First):** La aplicación se utiliza principalmente en smartphones.
   * Evita añadir cajas excesivamente anchas o márgenes laterales rígidos.
   * **Prevención de Zoom en iOS:** Todos los campos de entrada de texto (`input[type="text"]`, `textarea`, `select`) deben tener un tamaño de fuente de **al menos `16px`** en su estilo base para evitar que Safari aplique un auto-zoom molesto al hacer foco.
3. **Guardado Coherente:** Asegúrate de llamar a la función `saveState()` al finalizar cualquier operación que altere los arreglos globales de `ingredients` o `recipes` (ej. crear, eliminar, o modificar disponibilidad).
4. **Nomenclatura Consistente:**
   * Utiliza la palabra **"Comida"** o **"Plato"** en los textos dirigidos al usuario en vez de "Recetas" para el título de registro.
   * Utiliza **"Mis ingredientes"** para referirse al panel del inventario y **"Despensa"** para referirse a la categoría Alacena.
5. **Iconografía Gratuita:** Antes de usar una clase de FontAwesome, verifica que pertenezca a la versión gratuita de FontAwesome 6 (los iconos Premium o Pro no se renderizarán y dejarán vacía la sección).

---

## 🚀 Propuesta de Evolución Tecnológica

Actualmente, la aplicación es una Single Page Application (SPA) monolítica y autocontenida que depende exclusivamente de `localStorage` para la persistencia. Aunque esta arquitectura es ideal para portabilidad y simplicidad local, presenta limitaciones críticas al escalar (pérdida de datos si se borra la caché del navegador, inaccesibilidad entre diferentes dispositivos y falta de cuentas de usuario).

A continuación se detalla cómo estructurar una migración completa hacia un stack moderno compuesto por **Next.js (App Router)**, **TypeScript**, **Tailwind CSS** y una **Base de Datos**.

### 1. Incorporación de una Base de Datos
**Limitación actual:** `localStorage` restringe los datos al dispositivo y navegador específico. Si el usuario limpia el historial o cambia de teléfono, pierde su despensa e historial de comidas.
**Solución propuesta:** Migrar a una base de datos centralizada (ej. PostgreSQL o SQLite usando Prisma/Drizzle ORM, o un Backend-as-a-Service como Supabase/InsForge).

* **Modelo Relacional Propuesto:**
  * **Tabla `users`:** Para autenticación (registro, login) y vinculación de datos personales.
  * **Tabla `ingredients`:** Con columnas `id` (UUID), `name`, `category`, `available` (boolean) y `user_id` (relación de clave foránea 1:N con `users`).
  * **Tabla `recipes` (Comidas):** Con columnas `id` (UUID), `name`, `preparation` (text) y `user_id` (opcional, para recetas creadas por el usuario).
  * **Tabla `recipe_ingredients`:** Tabla intermedia para la relación N:M (muchos a muchos) entre recetas e ingredientes, especificando el `recipe_id` y el `ingredient_id`.
* **Beneficios:**
  * **Sincronización en la nube:** Acceso desde múltiples dispositivos de manera instantánea.
  * **Autenticación (Auth):** Registro seguro para múltiples usuarios (usando NextAuth/Auth.js o la autenticación nativa de un BaaS).
  * **Colaboración:** Posibilidad de compartir la despensa y recetario familiar/del hogar entre varios miembros.
  * **Búsquedas complejas:** Consultas eficientes en el servidor para traer solo las recetas viables según la despensa actual.

### 2. Migración a Next.js (App Router)
**Limitación actual:** Todo el código HTML, CSS y JS reside en un único archivo. Al crecer, el archivo se vuelve inmanejable y no permite optimización de SEO, rutas limpias ni renderizado del lado del servidor (SSR).
**Solución propuesta:** Estructurar el proyecto bajo el framework de React Next.js.

* **Arquitectura de Carpetas Propuesta:**
  * `app/layout.tsx`: Layout global de la app, importando tipografías de Google de forma local y optimizada (`next/font/google`).
  * `app/page.tsx`: Dashboard principal del usuario (mis ingredientes y comidas disponibles).
  * `app/recipes/[id]/page.tsx`: Páginas dinámicas dedicadas a mostrar la preparación detallada de cada receta, facilitando compartir la receta por enlace directo y mejorando el SEO.
  * `components/`: Componentes React modulares y reutilizables (ej. `<PantryCategory />`, `<RecipeCard />`, `<Toast />`, `<ConfirmationModal />`).
* **Beneficios:**
  * **React Server Components (RSC):** Renderizado inicial rápido en el servidor trayendo las recetas por defecto directamente desde la base de datos sin spinners de carga.
  * **Server Actions:** Gestión interactiva y segura de formularios (creación de ingredientes, actualización de estados) sin necesidad de crear complejas endpoints de API tradicionales.
  * **Rendimiento superior:** División de código automática (Code Splitting) que carga solo el JS requerido por cada página visitada.

### 3. Migración a TypeScript (TS)
**Limitación actual:** Vanilla JavaScript no cuenta con validación estática de tipos. Un error ortográfico en una propiedad (ej. `available` escrito como `avalaible`) solo se detectará al ejecutar la aplicación, lo que genera bugs difíciles de rastrear.
**Solución propuesta:** Implementar tipado estático estricto.

* **Definición de Interfaces Clave:**
  ```typescript
  // types/index.ts
  export type CategoryType = 'verduras' | 'heladera' | 'freezer' | 'alacena';

  export interface Ingredient {
    id: string;
    name: string;
    available: boolean;
    category: CategoryType;
  }

  export interface Recipe {
    id: string;
    name: string;
    ingredients: string[]; // Array de IDs de ingredientes
    preparation: string;
  }
  ```
* **Beneficios:**
  * **Autocompletado e IntelliSense:** Mayor rapidez al programar gracias a la sugerencia interactiva de propiedades y métodos de los modelos en el editor de código.
  * **Detección temprana de errores:** Errores de tipo o de datos nulos se detectan en tiempo de compilación/edición y no en producción.
  * **Refactorización guiada:** Cambiar el nombre de un campo alertará automáticamente sobre todas las dependencias afectadas a lo largo del código.

### 4. Implementación de Tailwind CSS
**Limitación actual:** La hoja de estilos CSS actual es extensa (más de 300 líneas), propensa a duplicados y lenta de mantener en caso de querer rediseñar componentes.
**Solución propuesta:** Reemplazar el archivo CSS nativo por clases de utilidad optimizadas de Tailwind CSS.

* **Ejemplo Práctico de Migración (Tarjeta de Ingrediente):**
  * *CSS actual en `index.html`:*
    ```css
    .pantry-item {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding: 0.75rem 1rem;
        background-color: var(--bg-card);
        border: 1px solid var(--border-color);
        border-radius: 10px;
        transition: all 0.3s ease;
    }
    ```
  * *Equivalente en Tailwind CSS (React JSX):*
    ```tsx
    <div className="flex items-center justify-between p-3 bg-card border border-border rounded-xl transition-all duration-300 hover:border-slate-500 hover:translate-x-0.5">
      {/* Contenido de la tarjeta */}
    </div>
    ```
* **Beneficios:**
  * **Sistema de Diseño Unificado:** Configuración centralizada de fuentes, paletas de colores y espaciados mediante `tailwind.config.js`, eliminando la necesidad de variables CSS manuales.
  * **CSS ultra-optimizado:** El compilador de Tailwind analiza el código y genera un archivo CSS minúsculo conteniendo exclusivamente las clases utilizadas en el proyecto.
  * **Diseño Responsivo Intuitivo:** Creación simplificada de layouts fluidos usando prefijos como `md:grid-cols-2` o `lg:flex-row` directo en el HTML.
  * **Soporte nativo de Modo Oscuro:** Transición fluida de temas utilizando la clase modificadora `dark:`.

