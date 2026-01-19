# 📱 Prueba Técnica - Frontend React Native Developer

## ⏱️ Tiempo estimado: 1-2 horas

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

## 🛠️ Stack Técnico Requerido

- ⚛️ **React Native** con **Expo**
- 📘 **TypeScript**
- 🔄 **@tanstack/react-query** (manejo de estado de servidor)
- 🧭 **React Navigation** (navegación entre pantallas)
- 📦 **axios** (llamadas HTTP)

---

## 📋 Funcionalidades Requeridas

### **1. Pantalla de Búsqueda** 🔍

**Requisitos:**
- Input de búsqueda de libros
- Lista de resultados mostrando:
  - Título del libro
  - Autor(es)
  - Año de publicación
- Al hacer tap en un libro → navegar a pantalla de detalle
- Estados visuales:
  - **Loading**: Mostrar `ActivityIndicator` mientras carga
  - **Empty**: Mensaje "No se encontraron libros" cuando no hay resultados
  - **Error**: Mensaje de error si falla la petición

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

---

## 📐 Guía de Implementación

### **1. Configurar React Query**

Crea el `QueryClient` y envuelve tu app:

```typescript
// App.tsx (o equivalente)
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000,  // 5 minutos
      gcTime: 10 * 60 * 1000,    // 10 minutos
      retry: false,
    },
  },
});

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      {/* Tu navegación aquí */}
    </QueryClientProvider>
  );
}
```

---

### **2. Tipos TypeScript**

Define las interfaces en `src/types/book.ts`:

```typescript
export interface Book {
  key: string;
  title: string;
  author_name?: string[];
  first_publish_year?: number;
  cover_i?: number;
  number_of_pages_median?: number;
}

export interface BookDetails {
  title: string;
  description?: string | { value: string };
  covers?: number[];
  number_of_pages?: number;
  first_publish_date?: string;
}

export interface SearchResponse {
  docs: Book[];
  numFound: number;
}
```

---

### **3. API Functions**

Crea las funciones en `src/api/books.ts`:

```typescript
import axios from 'axios';
import type { SearchResponse, BookDetails } from '../types/book';

const BASE_URL = 'https://openlibrary.org';

export async function searchBooks(query: string): Promise<SearchResponse> {
  const response = await axios.get(`${BASE_URL}/search.json`, {
    params: { q: query, limit: 10 },
  });
  return response.data;
}

export async function getBookDetails(workId: string): Promise<BookDetails> {
  const response = await axios.get(`${BASE_URL}/works/${workId}.json`);
  return response.data;
}

export function getCoverUrl(coverId?: number): string | null {
  if (!coverId) return null;
  return `https://covers.openlibrary.org/b/id/${coverId}-M.jpg`;
}
```

---

### **4. Hooks con React Query**

**`src/hooks/useSearchBooks.ts`:**
```typescript
import { useQuery } from '@tanstack/react-query';
import { searchBooks } from '../api/books';

export function useSearchBooks(query: string) {
  return useQuery({
    queryKey: ['books', query],
    queryFn: () => searchBooks(query),
    enabled: query.length > 2, // Solo buscar con más de 2 caracteres
  });
}
```

**`src/hooks/useBookDetails.ts`:**
```typescript
import { useQuery } from '@tanstack/react-query';
import { getBookDetails } from '../api/books';

export function useBookDetails(workId: string) {
  return useQuery({
    queryKey: ['book', workId],
    queryFn: () => getBookDetails(workId),
  });
}
```

---

### **5. Componentes**

**`src/components/BookCard.tsx`:**
```typescript
import React from 'react';
import { Pressable, Text, StyleSheet } from 'react-native';
import type { Book } from '../types/book';

interface BookCardProps {
  book: Book;
  onPress: () => void;
}

export function BookCard({ book, onPress }: BookCardProps) {
  const author = book.author_name?.[0] || 'Autor desconocido';
  
  return (
    <Pressable style={styles.card} onPress={onPress}>
      <Text style={styles.title}>{book.title}</Text>
      <Text style={styles.author}>{author}</Text>
      {book.first_publish_year && (
        <Text style={styles.year}>{book.first_publish_year}</Text>
      )}
    </Pressable>
  );
}

const styles = StyleSheet.create({
  card: {
    backgroundColor: '#1E1E1E',
    padding: 16,
    borderRadius: 8,
    marginBottom: 12,
  },
  title: {
    color: '#FFFFFF',
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 4,
  },
  author: {
    color: '#B0B0B0',
    fontSize: 14,
    marginBottom: 2,
  },
  year: {
    color: '#808080',
    fontSize: 12,
  },
});
```

---

### **6. Navegación**

Define los tipos de navegación:

```typescript
export type RootStackParamList = {
  Search: undefined;
  Detail: { workId: string; title: string };
};
```

Configura el Stack Navigator en `src/navigation/AppNavigator.tsx`

---

### **7. Pantallas**

**SearchScreen:**
- `TextInput` para búsqueda
- `FlatList` con `BookCard` para los resultados
- Usar `useSearchBooks(searchQuery)`
- Manejar `isLoading`, `isError`, `data`

**DetailScreen:**
- Obtener `workId` de `route.params`
- Usar `useBookDetails(workId)`
- Mostrar imagen del cover
- Mostrar información del libro

---

## ✅ Criterios de Evaluación

| Criterio | Peso | Qué evaluamos |
|----------|------|---------------|
| **React Query** | 40% | Configuración correcta, manejo de cache, estados (loading/error/success) |
| **TypeScript** | 25% | Interfaces bien definidas, tipado fuerte, sin `any` |
| **Código Limpio** | 20% | Componentes reutilizables, nombres claros, buena estructura |
| **Funcionalidad** | 15% | La app funciona correctamente end-to-end |

---

## 🎁 Puntos Extra (Opcionales)

Si terminas antes del tiempo, puedes agregar:

- ⚡ **Debounce** en la búsqueda (esperar 500ms antes de buscar)
- 🎨 **Estilos más elaborados** y UI pulida
- 📱 **FlatList optimizado** con `windowSize`, `removeClippedSubviews`
- 🔄 **Pull to refresh** en la lista de búsqueda
- ⚠️ **Manejo de errores mejorado** con mensajes específicos
- 🖼️ **Skeleton loaders** en vez de `ActivityIndicator`

---

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

## ⏱️ Timeline Sugerido

- **0-15 min:** Setup proyecto + navegación básica
- **15-45 min:** Pantalla de búsqueda con React Query
- **45-75 min:** Pantalla de detalle
- **75-90 min:** Componentes, tipos, refactoring
- **90-120 min:** README + últimos detalles

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

**¿Debo hacer dark mode?**
- No es necesario, un tema único está bien.

**¿Puedo consultar documentación?**
- ¡Por supuesto! Es parte del trabajo real.

---

## 🚀 ¡Buena suerte!

Recuerda: **Calidad sobre cantidad**. Es mejor una app simple pero bien hecha que una compleja con bugs.

Si tienes dudas durante la prueba, anótalas en el README y explica cómo las resolviste o por qué tomaste ciertas decisiones.

---

**Tiempo de entrega:** [Especificar fecha límite]

**Contacto para dudas:** [Tu email o canal de comunicación]

