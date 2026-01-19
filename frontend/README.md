# 📱 Prueba Técnica - Frontend React Native Developer

---

## 🎯 Objetivo

Desarrollar una aplicación móvil simple en **React Native** que permita buscar libros y ver sus detalles utilizando la API pública de **Open Library**.

Esta prueba evaluará tus conocimientos en:
- React Native + Expo + TypeScript
- React Query para manejo de estado asíncrono
- Arquitectura de código limpia y escalable
- Consumo de APIs REST
- Componentes reutilizables

---

## 🎨 Diseño (Figma) — MUY IMPORTANTE
Tendrás un diseño en Figma que debes replicar **1:1**:

**Figma:** https://www.figma.com/design/gy4waC251AzgB5WtYPcgLe/Frontend-Prueba

### Requisitos de UI (Pixel Perfect)
- La UI debe verse **igual** al Figma:
  - Tipografías (tamaño, peso, line-height si aplica)
  - Espaciados (padding/margins)
  - Bordes/radius, sombras (si existen)
  - Colores y jerarquía visual
  - Estados (loading/empty/error) tal como se muestran en el diseño
- Se valorará que uses **tokens/constantes** (colors, spacing, typography) para mantener consistencia.
- Respeta safe areas y comportamiento nativo (teclado, scroll, etc.).
- No buscamos “algo parecido”: buscamos **pixel perfect**.

> Tip: añade screenshots comparables (mismo device) para demostrar fidelidad al Figma.

---

## 🛠️ Stack Técnico Requerido

- ⚛️ **React Native** con **Expo**
- 📘 **TypeScript**
- 🔄 **@tanstack/react-query** (manejo de estado de servidor)
- 🧭 **React Navigation** (navegación entre pantallas)
- 📦 **axios** (llamadas HTTP)

---

## 📋 Funcionalidades Requeridas

### 1) Pantalla de Búsqueda 🔍
**Requisitos funcionales**
- Input de búsqueda de libros
- Lista de resultados mostrando:
  - Título
  - Autor(es)
  - Año de publicación
- Tap en un libro → navega a detalle
- Estados:
  - Loading (ActivityIndicator o el componente que indique Figma)
  - Empty (“No se encontraron libros” o el texto del Figma)
  - Error (mensaje de error)

**Requisitos de diseño**
- Debe ser **igual** a Figma: layout, tipografías, colores, espaciados, estilos del item, etc.
- La lista debe respetar paddings, separadores y alturas del diseño.

**API a usar:**
```
GET https://openlibrary.org/search.json?q={query}&limit=10
```

**Ejemplo de respuesta:**
```json
{
  "docs": [
    {
      "key": "/works/OL27448W",
      "title": "JavaScript: The Good Parts",
      "author_name": ["Douglas Crockford"],
      "first_publish_year": 2008,
      "cover_i": 8739161,
      "number_of_pages_median": 176
    }
  ]
}
```

---

### **2. Pantalla de Detalle** 📖

**Requisitos:**
- Mostrar información del libro:
  - Cover/portada del libro (con placeholder si no existe)
  - Título
  - Autor(es)
  - Año de publicación
  - Número de páginas (si está disponible)
- Botón para volver a la búsqueda (puedes usar el header nativo)

**APIs a usar:**
```
Detalle: GET https://openlibrary.org/works/{work_id}.json
Cover:   https://covers.openlibrary.org/b/id/{cover_id}-M.jpg
```

> **Nota:** El `work_id` se extrae del campo `key`. Por ejemplo: `"/works/OL27448W"` → `"OL27448W"`

---

## 🚀 Setup Inicial

### **1. Crear el proyecto:**
```bash
npx create-expo-app@latest bookfinder -t blank-typescript
cd bookfinder
```

### **2. Instalar dependencias:**
```bash
npm install @tanstack/react-query @react-navigation/native @react-navigation/native-stack axios
npx expo install react-native-screens react-native-safe-area-context
```

### **3. Estructura de carpetas sugerida:**
```
src/
├── api/
│   └── books.ts              # Funciones para llamadas API
├── hooks/
│   ├── useSearchBooks.ts     # Hook con React Query para búsqueda
│   └── useBookDetails.ts     # Hook con React Query para detalle
├── screens/
│   ├── SearchScreen.tsx      # Pantalla de búsqueda
│   └── DetailScreen.tsx      # Pantalla de detalle
├── components/
│   ├── BookCard.tsx          # Card para mostrar libro en lista
│   └── CustomImage.tsx       # Componente Image con manejo de errores
├── types/
│   └── book.ts               # Interfaces TypeScript
└── navigation/
    └── AppNavigator.tsx      # Configuración de navegación
```

## 📦 Entregables

### **1. Repositorio en GitHub**
- Código completo del proyecto
- Commits con mensajes descriptivos
- `.gitignore` configurado (ignorar `node_modules`, `.expo`, etc.)

### **2. README.md en tu repo con:**

```markdown
# BookFinder

## Instalación
\`\`\`bash
npm install
\`\`\`

## Ejecución
\`\`\`bash
npx expo start
\`\`\`

## Decisiones Técnicas
[Explica brevemente tus decisiones: por qué estructuraste así, 
qué desafíos encontraste, cómo los resolviste]

## Screenshots
[Opcional: agrega capturas o GIF]
```

### **3. Envía el link del repositorio**

---

## 💡 Consejos

1. **Empieza simple**: Primero haz que funcione, luego mejora
2. **Lee la documentación**: 
   - [React Query](https://tanstack.com/query/latest)
   - [Open Library API](https://openlibrary.org/developers/api)
3. **Prueba en Expo Go**: Descarga la app en tu celular para probar
4. **Enfócate en React Query**: Es lo más importante de esta prueba
5. **Tipado fuerte**: Evita `any`, define interfaces claras
6. **Commits frecuentes**: Muestra tu proceso de trabajo

---

## 📚 Recursos Útiles

### **Open Library API:**
- Docs: https://openlibrary.org/developers/api
- Búsqueda: `https://openlibrary.org/search.json?q=the+lord+of+the+rings`
- Detalle: `https://openlibrary.org/works/OL27448W.json`
- Cover: `https://covers.openlibrary.org/b/id/8739161-M.jpg`

### **Documentación:**
- [React Query](https://tanstack.com/query/latest/docs/framework/react/overview)
- [React Navigation](https://reactnavigation.org/docs/getting-started)
- [Expo](https://docs.expo.dev/)

---

## ❓ Preguntas Frecuentes

**¿Puedo usar librerías adicionales?**
- Sí, pero justifícalo en el README. Preferimos ver el código nativo de React Native.

**¿Necesito hacer tests?**
- No es necesario para esta prueba.

**¿Necesito implementar persistencia/favoritos?**
- No, enfócate en la funcionalidad básica.

**¿Puedo consultar documentación?**
- ¡Por supuesto! Es parte del trabajo real.

---

## 🚀 ¡Buena suerte!

Recuerda: **Calidad sobre cantidad**. Es mejor una app simple pero bien hecha que una compleja con bugs.

Si tienes dudas durante la prueba, anótalas en el README y explica cómo las resolviste o por qué tomaste ciertas decisiones.

