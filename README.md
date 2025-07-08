# 🚀 Aprende Next.js: Construyendo una Pokédex

Este tutorial te enseñará los fundamentos de **Next.js** construyendo una aplicación práctica: una Pokédex que consume la PokeAPI. Al final tendrás una aplicación funcional y comprenderás los conceptos clave de Next.js.

## 🎯 ¿Qué aprenderás?

- ✅ Crear y configurar un proyecto Next.js desde cero
- ✅ Entender la estructura de carpetas y el App Router
- ✅ Diferencia entre Server Components y Client Components
- ✅ Sistema de rutas dinámicas
- ✅ Consumo de APIs externas
- ✅ Creación de API Routes propias
- ✅ Uso de componentes con ShadCN/UI
- ✅ Mejores prácticas de estructura y organización

## 📋 Requisitos previos

- Node.js (versión 18 o superior)
- Conocimientos básicos de React
- Conocimientos básicos de TypeScript (opcional pero recomendado)

---

## 🛠 Paso 1: Creación del proyecto

Primero, vamos a crear nuestro proyecto Next.js:

```bash
npx create-next-app@latest pokedex-nextjs
cd pokedex-nextjs
```

**Configuración recomendada:**
- ✅ TypeScript: **Yes**
- ✅ ESLint: **Yes** 
- ✅ Tailwind CSS: **Yes**
- ✅ `src/` directory: **Yes**
- ✅ App Router: **Yes** (recommended)
- ❌ Turbopack: **No**
- ❌ Import alias: **No**

Inicia el servidor de desarrollo:
```bash
npm run dev
```

Visita `http://localhost:3000` para ver tu aplicación funcionando.

---

## 📁 Paso 2: Entendiendo la estructura del proyecto

Next.js 13+ usa el **App Router** con esta estructura:

```
pokedex-nextjs/
├── src/                    # 📁 Código fuente
│   ├── app/                # 🎯 Corazón de la aplicación
│   │   ├── page.tsx        # 🏠 Página principal (/)
│   │   ├── layout.tsx      # 📋 Layout global
│   │   ├── globals.css     # 🎨 Estilos globales
│   │   ├── pokemons/       # 📂 Rutas de pokémons
│   │   │   ├── page.tsx    # 📄 Lista (/pokemons)
│   │   │   └── [name]/     # 🔗 Ruta dinámica
│   │   │       └── page.tsx # 📄 Detalle (/pokemons/pikachu)
│   │   └── api/            # 🔌 API Routes
│   │       └── pokemons/   
│   │           └── route.ts # 🛠 Endpoint propio
│   ├── components/         # 🧩 Componentes reutilizables
│   └── lib/                # 🔧 Utilidades y funciones
├── public/                 # 📁 Archivos estáticos
└── ...
```

### 🔍 Conceptos clave:

- **`src/`**: Directorio fuente que contiene todo el código
- **`app/`**: Cada carpeta representa una ruta
- **`page.tsx`**: Define una página accesible por URL
- **`layout.tsx`**: Envuelve páginas con elementos comunes
- **`[name]/`**: Rutas dinámicas (parámetros variables)

---

## 🎨 Paso 3: Configurar ShadCN/UI

ShadCN/UI nos dará componentes bonitos y funcionales:

```bash
npx shadcn@latest init
```

**Configuración recomendada:**
- Style: **Neutral**

Instala algunos componentes que usaremos:

```bash
npx shadcn@latest add card button badge input
```

---

## 🏠 Paso 4: Crear la página principal (Landing)

Actualiza `src/app/page.tsx` para crear una landing atractiva:

```tsx
// src/app/page.tsx
import Link from 'next/link'
import { Button } from '../components/ui/button'

export default function HomePage() {
  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-500 to-purple-600">
      <div className="container mx-auto px-4 py-16 text-center text-white">
        <h1 className="text-6xl font-bold mb-6">
          🔴 Pokédex
        </h1>
        <p className="text-xl mb-8 max-w-2xl mx-auto">
          Explora el mundo Pokémon con nuestra Pokédex interactiva. 
          Descubre información detallada sobre tus Pokémon favoritos.
        </p>
        <div className="space-y-4">
          <Link href="/pokemons">
            <Button size="lg" variant="secondary" className="text-lg px-8 py-4">
              🚀 Explorar Pokémons
            </Button>
          </Link>
        </div>
        
        <div className="mt-16 grid md:grid-cols-3 gap-8 text-left">
          <div className="bg-white/10 backdrop-blur rounded-lg p-6">
            <h3 className="text-2xl font-semibold mb-3">⚡ Server Components</h3>
            <p>Renderizado del lado del servidor para mejor SEO y rendimiento</p>
          </div>
          <div className="bg-white/10 backdrop-blur rounded-lg p-6">
            <h3 className="text-2xl font-semibold mb-3">🔄 App Router</h3>
            <p>Sistema de rutas moderno con layouts y páginas anidadas</p>
          </div>
          <div className="bg-white/10 backdrop-blur rounded-lg p-6">
            <h3 className="text-2xl font-semibold mb-3">🛠 API Routes</h3>
            <p>Backend y frontend en un solo proyecto con API Routes</p>
          </div>
        </div>
      </div>
    </div>
  )
}
```

---

## 📄 Paso 5: Crear componente PokemonCard reutilizable

Antes de crear la página de pokémons, vamos a crear un componente reutilizable que nos ayudará a mostrar cada pokémon de manera elegante.

Crea `src/components/PokemonCard.tsx`:

```tsx
// src/components/PokemonCard.tsx
import Link from 'next/link'
import Image from 'next/image'
import { Card, CardContent, CardHeader, CardTitle } from './ui/card'

interface PokemonCardProps {
  name: string
  url: string
}

export function PokemonCard({ name, url }: PokemonCardProps) {
  // Extraer ID del URL para mostrar imagen
  const pokemonId = url.split('/').filter(Boolean).pop()
  const imageUrl = `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokemonId}.png`
  
  return (
    <Link href={`/pokemons/${name}`}>
      <Card className="hover:shadow-lg transition-all duration-200 hover:scale-105 cursor-pointer">
        <CardHeader className="pb-2">
          <CardTitle className="capitalize text-center text-lg">
            {name}
          </CardTitle>
        </CardHeader>
        <CardContent className="text-center">
          <Image 
            src={imageUrl}
            alt={name}
            width={80}
            height={80}
            className="w-20 h-20 mx-auto mb-2"
          />
          <p className="text-sm text-gray-500">#{pokemonId}</p>
        </CardContent>
      </Card>
    </Link>
  )
}
```

### 🔍 Conceptos del componente:

- **Props con TypeScript**: Interfaz clara para las propiedades
- **Extracción de ID**: Manipulación de strings para obtener el ID del pokémon
- **Imagen dinámica**: URL construida dinámicamente usando el ID
- **Hover effects**: Efectos visuales con Tailwind CSS
- **lazy loading**: Optimización para imágenes

---

## 🛠 Paso 6: API Routes (Backend en Next.js)

Antes de crear las páginas, vamos a crear nuestras **propias API Routes** para manejar los datos. Esto es una mejor práctica porque:

- ✅ Separamos la lógica de backend del frontend
- ✅ Podemos agregar validación, cache y transformaciones
- ✅ Mejor control de errores
- ✅ Más escalable para aplicaciones reales

### 📄 API para lista de pokémons

Crea `src/app/api/pokemons/route.tsx`:

```tsx
// src/app/api/pokemons/route.tsx
import { NextRequest, NextResponse } from 'next/server'

// 🔍 Tipos para las respuestas de la API de Pokemon
interface PokemonApiListItem {
  name: string
  url: string
}

interface PokemonApiListResponse {
  count: number
  next: string | null
  previous: string | null
  results: PokemonApiListItem[]
}

interface PokemonType {
  type: {
    name: string
    url: string
  }
  slot: number
}

interface PokemonDetailResponse {
  id: number
  name: string
  sprites: {
    front_default: string
  }
  types: PokemonType[]
}

// 🔍 Tipos para nuestras respuestas
interface PokemonListItem {
  name: string
  url: string
  id: number
  image: string
  types: string[]
}

interface ApiResponse {
  pokemons: PokemonListItem[]
  total: number
  search?: string
}

// 📦 GET - Obtener lista de pokémons (con búsqueda)
export async function GET(request: NextRequest) {
  const { searchParams } = new URL(request.url)
  const limit = searchParams.get('limit') || '20'
  const search = searchParams.get('search') || ''
  
  try {
    // Obtener lista básica
    const res = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=${limit}`, {
      cache: 'force-cache'
    })
    
    if (!res.ok) {
      throw new Error('Error al obtener pokémons')
    }
    
    const data: PokemonApiListResponse = await res.json()
    
    // Enriquecer con detalles para búsqueda
    const enrichedPokemons = await Promise.all(
      data.results.map(async (pokemon: PokemonApiListItem) => {
        const detailRes = await fetch(pokemon.url, { cache: 'force-cache' })
        const detail: PokemonDetailResponse = await detailRes.json()
        
        return {
          name: detail.name,
          url: pokemon.url,
          id: detail.id,
          image: detail.sprites.front_default,
          types: detail.types.map((t: PokemonType) => t.type.name)
        }
      })
    )
    
    // Filtrar por búsqueda si existe
    const filteredPokemons = search
      ? enrichedPokemons.filter(p => 
          p.name.toLowerCase().includes(search.toLowerCase())
        )
      : enrichedPokemons
    
    const response: ApiResponse = {
      pokemons: filteredPokemons,
      total: data.count,
      search
    }
    
    return NextResponse.json(response)
    
  } catch (error) {
    console.error('Error:', error)
    return NextResponse.json(
      { error: 'Error al cargar pokémons' },
      { status: 500 }
    )
  }
}
```

### 📄 API para detalle de pokémon

Crea `src/app/api/pokemons/[name]/route.ts`:

```tsx
// src/app/api/pokemons/[name]/route.ts
import { NextRequest, NextResponse } from 'next/server'

// 🔍 Tipos para el pokémon detallado
interface PokemonDetail {
  id: number
  name: string
  height: number
  weight: number
  sprites: {
    front_default: string
    back_default: string
  }
  types: Array<{
    type: { name: string }
  }>
  stats: Array<{
    base_stat: number
    stat: { name: string }
  }>
}

interface RouteParams {
  params: Promise<{ name: string }>
}

// 📦 GET - Obtener un pokémon específico
export async function GET(request: NextRequest, { params }: RouteParams) {
  try {
    const { name } = await params
    
    const res = await fetch(`https://pokeapi.co/api/v2/pokemon/${name.toLowerCase()}`, {
      cache: 'force-cache'
    })
    
    if (!res.ok) {
      return NextResponse.json(
        { error: 'Pokémon no encontrado' },
        { status: 404 }
      )
    }
    
    const pokemon: PokemonDetail = await res.json()
    
    return NextResponse.json(pokemon)
    
  } catch (error) {
    console.error('Error:', error)
    return NextResponse.json(
      { error: 'Error al cargar pokémon' },
      { status: 500 }
    )
  }
}
```

### 🔍 Conceptos importantes:

- **API Routes**: Backend integrado en Next.js
- **NextResponse**: Para crear respuestas HTTP
- **Manejo de errores**: Try/catch con respuestas HTTP apropiadas
- **Cache**: Optimización en el servidor
- **Transformación de datos**: Adaptamos la respuesta externa a nuestra API
- **Tipado fuerte**: Interfaces específicas para cada tipo de dato (evitar `any`)
- **TypeScript en APIs**: Tipado tanto para requests como responses

---

## 📄 Paso 7: Lista de Pokémons (Server Component)

Ahora que tenemos nuestras APIs, vamos a crear la página que **consume nuestras propias APIs** (no directamente PokeAPI).

Crea la página de lista en `src/app/pokemons/page.tsx`:

```tsx
// src/app/pokemons/page.tsx
import { Badge } from '../../components/ui/badge'
import { PokemonCard } from '../../components/PokemonCard'
import { SearchPokemon } from '../../components/SearchPokemon'

// 🔍 Tipos para TypeScript
interface Pokemon {
  name: string
  url: string
}

interface ApiResponse {
  pokemons: Pokemon[]
  total: number
}

// 🌐 Función para obtener datos desde NUESTRA API
async function getPokemons(): Promise<ApiResponse> {
  const res = await fetch(`${process.env.NEXT_PUBLIC_BASE_URL || 'http://localhost:3000'}/api/pokemons`, {
    cache: 'force-cache'
  })
  
  if (!res.ok) {
    throw new Error('Error al cargar pokémons')
  }
  
  return res.json()
}

// 🧩 Server Component (por defecto en app/)
export default async function PokemonsPage() {
  const data = await getPokemons()
  
  return (
    <div className="container mx-auto px-4 py-8">
      <div className="flex items-center justify-between mb-8">
        <h1 className="text-4xl font-bold">Pokédex</h1>
        <Badge variant="secondary" className="text-lg px-4 py-2">
          {data.total} Pokémons total
        </Badge>
      </div>

      {/* Componente de búsqueda */}
      <div className="mb-8">
        <SearchPokemon />
      </div>
      
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
        {data.pokemons.map((pokemon) => (
          <PokemonCard 
            key={pokemon.name} 
            name={pokemon.name} 
            url={pokemon.url} 
          />
        ))}
      </div>
    </div>
  )
}
```

### 🔍 Conceptos importantes:

- **Server Component**: Se ejecuta en el servidor, perfecto para fetch de datos
- **API propia**: Llamamos a `/api/pokemons` en lugar de PokeAPI directamente
- **BASE_URL**: Variable de entorno para diferentes ambientes
- **Separación de responsabilidades**: La página solo se preocupa por mostrar datos
- **Búsqueda integrada**: Incluimos SearchPokemon para mejor UX

---

## 📱 Paso 8: Página de detalle (Rutas dinámicas)

Ahora vamos a crear la página de detalle que consume **nuestra API** `/api/pokemons/[name]`.

Crea `src/app/pokemons/[name]/page.tsx`:

```tsx
// src/app/pokemons/[name]/page.tsx
import { notFound } from 'next/navigation'
import Link from 'next/link'
import Image from 'next/image'
import { Button } from '../../../components/ui/button'
import { Card, CardContent, CardHeader, CardTitle } from '../../../components/ui/card'
import { Badge } from '../../../components/ui/badge'

// 🔍 Tipos para el Pokémon detallado
interface PokemonDetail {
  id: number
  name: string
  height: number
  weight: number
  sprites: {
    front_default: string
    back_default: string
  }
  types: Array<{
    type: { name: string }
  }>
  stats: Array<{
    base_stat: number
    stat: { name: string }
  }>
}

// 🌐 Función para obtener un Pokémon desde NUESTRA API
async function getPokemon(name: string): Promise<PokemonDetail | null> {
  try {
    const res = await fetch(`${process.env.NEXT_PUBLIC_BASE_URL || 'http://localhost:3000'}/api/pokemons/${name}`, {
      cache: 'force-cache'
    })
    
    if (!res.ok) {
      return null
    }
    
    return res.json()
  } catch {
    return null
  }
}

// 🎯 Props que recibe la página (incluye parámetros de ruta)
interface PageProps {
  params: Promise<{ name: string }>
}

export default async function PokemonDetailPage({ params }: PageProps) {
  // 🔥 IMPORTANTE: En Next.js 15, params es asíncrono y debe ser awaited
  const { name } = await params
  const pokemon = await getPokemon(name)
  
  // Si no existe el Pokémon, mostrar 404
  if (!pokemon) {
    notFound()
  }
  
  return (
    <div className="container mx-auto px-4 py-8">
      <Link href="/pokemons">
        <Button variant="outline" className="mb-6">
          ← Volver a la lista
        </Button>
      </Link>
      
      <div className="grid md:grid-cols-2 gap-8">
        {/* Información básica */}
        <Card>
          <CardHeader>
            <CardTitle className="text-3xl capitalize flex items-center gap-4">
              {pokemon.name}
              <Badge>#{pokemon.id}</Badge>
            </CardTitle>
          </CardHeader>
          <CardContent className="space-y-4">
            <div className="flex justify-center space-x-4">
              <Image 
                src={pokemon.sprites.front_default} 
                alt={`${pokemon.name} frontal`}
                width={128}
                height={128}
                className="w-32 h-32"
              />
              <Image 
                src={pokemon.sprites.back_default} 
                alt={`${pokemon.name} trasero`}
                width={128}
                height={128}
                className="w-32 h-32"
              />
            </div>
            
            <div className="grid grid-cols-2 gap-4 text-center">
              <div>
                <p className="text-sm text-gray-500">Altura</p>
                <p className="text-2xl font-bold">{pokemon.height / 10} m</p>
              </div>
              <div>
                <p className="text-sm text-gray-500">Peso</p>
                <p className="text-2xl font-bold">{pokemon.weight / 10} kg</p>
              </div>
            </div>
            
            <div>
              <p className="text-sm text-gray-500 mb-2">Tipos</p>
              <div className="flex gap-2">
                {pokemon.types.map((type) => (
                  <Badge key={type.type.name} variant="secondary">
                    {type.type.name}
                  </Badge>
                ))}
              </div>
            </div>
          </CardContent>
        </Card>
        
        {/* Estadísticas */}
        <Card>
          <CardHeader>
            <CardTitle>Estadísticas Base</CardTitle>
          </CardHeader>
          <CardContent>
            <div className="space-y-3">
              {pokemon.stats.map((stat) => (
                <div key={stat.stat.name}>
                  <div className="flex justify-between mb-1">
                    <span className="capitalize">{stat.stat.name}</span>
                    <span className="font-bold">{stat.base_stat}</span>
                  </div>
                  <div className="w-full bg-gray-200 rounded-full h-2">
                    <div 
                      className="bg-blue-500 h-2 rounded-full"
                      style={{ width: `${Math.min(stat.base_stat / 2, 100)}%` }}
                    />
                  </div>
                </div>
              ))}
            </div>
          </CardContent>
        </Card>
      </div>
    </div>
  )
}
```

### 🔍 Conceptos importantes:

- **[name]**: Carpeta con corchetes = ruta dinámica
- **params**: Acceso a parámetros de la URL (Promise en Next.js 15)
- **await params**: ⚠️ **CRUCIAL**: En Next.js 15, params es una Promise que debe ser awaited
- **Promise<{ name: string }>**: Tipado correcto para params en Next.js 15
- **notFound()**: Función de Next.js para mostrar página 404
- **Manejo de errores**: Try/catch para APIs externas

---





## 🎮 Paso 9: Client Component interactivo (SearchPokemon)

En el Paso 7 ya integramos el componente `SearchPokemon` en la página de pokémons. Ahora vamos a ver cómo está implementado este componente que permite buscar pokémons usando **nuestra API**.

El componente `src/components/SearchPokemon.tsx` ya está creado con este código:

```tsx
// src/components/SearchPokemon.tsx
'use client' // 🎯 Marca como Client Component

import { useState } from 'react'
import Image from 'next/image'
import { Button } from './ui/button'
import { Input } from './ui/input'
import { Card, CardContent, CardHeader, CardTitle } from './ui/card'

interface SearchResult {
  name: string
  id: number
  image: string
  types: string[]
}

export function SearchPokemon() {
  const [search, setSearch] = useState('')
  const [results, setResults] = useState<SearchResult[]>([])
  const [loading, setLoading] = useState(false)
  
  const handleSearch = async () => {
    if (!search.trim()) return
    
    setLoading(true)
    try {
      const res = await fetch(`/api/pokemons?search=${search}&limit=10`)
      const data = await res.json()
      setResults(data.pokemons)
    } catch (error) {
      console.error('Error:', error)
    } finally {
      setLoading(false)
    }
  }
  
  return (
    <Card className="w-full max-w-2xl mx-auto">
      <CardHeader>
        <CardTitle>🔍 Buscar Pokémon</CardTitle>
      </CardHeader>
      <CardContent className="space-y-4">
        <div className="flex gap-2">
          <Input
            placeholder="Busca un Pokémon..."
            value={search}
            onChange={(e) => setSearch(e.target.value)}
            onKeyPress={(e) => e.key === 'Enter' && handleSearch()}
          />
          <Button onClick={handleSearch} disabled={loading}>
            {loading ? '🔄' : '🔍'}
          </Button>
        </div>
        
        {results.length > 0 && (
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            {results.map((pokemon) => (
              <div key={pokemon.id} className="flex items-center gap-3 p-3 border rounded">
                <Image src={pokemon.image} alt={pokemon.name} width={48} height={48} className="w-12 h-12" />
                <div>
                  <p className="font-semibold capitalize">{pokemon.name}</p>
                  <p className="text-sm text-gray-500">
                    {pokemon.types.join(', ')}
                  </p>
                </div>
              </div>
            ))}
          </div>
        )}
      </CardContent>
    </Card>
  )
}
```

### 🔍 Diferencias clave:

- **'use client'**: Necesario para hooks como useState
- **Client Component**: Se ejecuta en el navegador
- **Server Component**: Se ejecuta en el servidor (por defecto)
- **API propia**: Usa `/api/pokemons` con parámetros de búsqueda
- **Arquitectura separada**: Frontend consume backend propio

---

## 📄 Paso 9.5: Paginación de la lista de Pokémons

Una funcionalidad esencial para cualquier lista larga es la **paginación**. Vamos a implementar una paginación completa que incluya:

- ✅ Navegación por páginas
- ✅ Información de página actual
- ✅ Botones Anterior/Siguiente
- ✅ Navegación directa a páginas específicas
- ✅ Soporte en la API con `offset` y `limit`

### 🧩 Crear componente de paginación

Primero, creamos un componente reutilizable en `src/components/Pagination.tsx`:

```tsx
// src/components/Pagination.tsx
import Link from 'next/link'
import { Button } from './ui/button'

interface PaginationProps {
  currentPage: number
  totalPages: number
  baseUrl: string // URL base para la paginación (ej: "/pokemons")
}

export function Pagination({ currentPage, totalPages, baseUrl }: PaginationProps) {
  // Calcular páginas a mostrar (máximo 5 páginas)
  const getPageNumbers = () => {
    const pages = []
    const maxPages = 5
    
    let startPage = Math.max(1, currentPage - Math.floor(maxPages / 2))
    const endPage = Math.min(totalPages, startPage + maxPages - 1)
    
    // Ajustar si estamos cerca del final
    if (endPage - startPage + 1 < maxPages) {
      startPage = Math.max(1, endPage - maxPages + 1)
    }
    
    for (let i = startPage; i <= endPage; i++) {
      pages.push(i)
    }
    
    return pages
  }

  const pageNumbers = getPageNumbers()

  return (
    <div className="flex items-center justify-center space-x-2 py-8">
      {/* Botón anterior */}
      <Link href={currentPage > 1 ? `${baseUrl}?page=${currentPage - 1}` : '#'}>
        <Button 
          variant={currentPage > 1 ? "outline" : "ghost"} 
          disabled={currentPage <= 1}
          className="disabled:opacity-50 disabled:cursor-not-allowed"
        >
          ← Anterior
        </Button>
      </Link>

      {/* Primera página */}
      {pageNumbers[0] > 1 && (
        <>
          <Link href={`${baseUrl}?page=1`}>
            <Button variant="outline">1</Button>
          </Link>
          {pageNumbers[0] > 2 && <span className="px-2">...</span>}
        </>
      )}

      {/* Páginas numeradas */}
      {pageNumbers.map((page) => (
        <Link key={page} href={`${baseUrl}?page=${page}`}>
          <Button 
            variant={page === currentPage ? "default" : "outline"}
            className={page === currentPage ? "bg-blue-600" : ""}
          >
            {page}
          </Button>
        </Link>
      ))}

      {/* Última página */}
      {pageNumbers[pageNumbers.length - 1] < totalPages && (
        <>
          {pageNumbers[pageNumbers.length - 1] < totalPages - 1 && (
            <span className="px-2">...</span>
          )}
          <Link href={`${baseUrl}?page=${totalPages}`}>
            <Button variant="outline">{totalPages}</Button>
          </Link>
        </>
      )}

      {/* Botón siguiente */}
      <Link href={currentPage < totalPages ? `${baseUrl}?page=${currentPage + 1}` : '#'}>
        <Button 
          variant={currentPage < totalPages ? "outline" : "ghost"} 
          disabled={currentPage >= totalPages}
          className="disabled:opacity-50 disabled:cursor-not-allowed"
        >
          Siguiente →
        </Button>
      </Link>
    </div>
  )
}
```

### 🔍 Conceptos del componente de paginación:

- **Lógica de páginas visibles**: Muestra máximo 5 páginas, centradas en la página actual
- **Elipsis (`...`)**: Indica páginas omitidas cuando hay muchas páginas
- **Estados disabled**: Botones deshabilitados cuando no se puede navegar
- **Link optimizado**: Usa `<Link>` de Next.js para navegación rápida
- **Responsive**: Funciona bien en móviles y desktop

### 🛠 Actualizar API para soportar paginación

Modifica `src/app/api/pokemons/route.tsx` para soportar `offset` y devolver información de paginación:

```tsx
// src/app/api/pokemons/route.tsx
// Actualizar interface ApiResponse
interface ApiResponse {
  pokemons: PokemonListItem[]
  total: number
  search?: string
  pagination: {
    currentPage: number
    totalPages: number
    hasNext: boolean
    hasPrevious: boolean
    limit: number
    offset: number
  }
}

// 📦 GET - Obtener lista de pokémons (con búsqueda y paginación)
export async function GET(request: NextRequest) {
  const { searchParams } = new URL(request.url)
  const limit = parseInt(searchParams.get('limit') || '20')
  const page = parseInt(searchParams.get('page') || '1')
  const search = searchParams.get('search') || ''
  
  // Calcular offset basado en la página
  const offset = (page - 1) * limit
  
  try {
    // Obtener lista básica con offset
    const res = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=${limit}&offset=${offset}`, {
      cache: 'force-cache'
    })
    
    // ... resto del código igual ...
    
    // Calcular información de paginación
    const totalPages = Math.ceil(data.count / limit)
    
    const response: ApiResponse = {
      pokemons: filteredPokemons,
      total: data.count,
      search,
      pagination: {
        currentPage: page,
        totalPages,
        hasNext: page < totalPages,
        hasPrevious: page > 1,
        limit,
        offset
      }
    }
    
    return NextResponse.json(response)
  }
  // ... manejo de errores igual ...
}
```

### 📄 Actualizar página de pokémons

Modifica `src/app/pokemons/page.tsx` para usar `searchParams` y mostrar la paginación:

```tsx
// src/app/pokemons/page.tsx
import { Badge } from '../../components/ui/badge'
import { PokemonCard } from '../../components/PokemonCard'
import { SearchPokemon } from '../../components/SearchPokemon'
import { Pagination } from '../../components/Pagination'

// Actualizar interface ApiResponse
interface ApiResponse {
  pokemons: Pokemon[]
  total: number
  pagination: {
    currentPage: number
    totalPages: number
    hasNext: boolean
    hasPrevious: boolean
    limit: number
    offset: number
  }
}

// Actualizar función para incluir página
async function getPokemons(page: number = 1): Promise<ApiResponse> {
  const res = await fetch(`${process.env.NEXT_PUBLIC_BASE_URL || 'http://localhost:3000'}/api/pokemons?page=${page}`, {
    cache: 'force-cache'
  })
  
  if (!res.ok) {
    throw new Error('Error al cargar pokémons')
  }
  
  return res.json()
}

// Agregar searchParams a la página
interface PageProps {
  searchParams: Promise<{ page?: string }>
}

export default async function PokemonsPage({ searchParams }: PageProps) {
  const { page } = await searchParams
  const currentPage = parseInt(page || '1')
  const data = await getPokemons(currentPage)
  
  return (
    <div className="container mx-auto px-4 py-8">
      <div className="flex items-center justify-between mb-8">
        <h1 className="text-4xl font-bold">Pokédex</h1>
        <Badge variant="secondary" className="text-lg px-4 py-2">
          {data.total} Pokémons total
        </Badge>
      </div>

      {/* Componente de búsqueda */}
      <div className="mb-8">
        <SearchPokemon />
      </div>
      
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
        {data.pokemons.map((pokemon) => (
          <PokemonCard 
            key={pokemon.name} 
            name={pokemon.name} 
            url={pokemon.url} 
          />
        ))}
      </div>

      {/* Componente de paginación */}
      <Pagination
        currentPage={data.pagination.currentPage}
        totalPages={data.pagination.totalPages}
        baseUrl="/pokemons"
      />
    </div>
  )
}
```

### 🔍 Conceptos importantes de paginación:

- **searchParams**: Props de Next.js 15 que requiere `await` para acceder a parámetros de consulta
- **Offset calculation**: `(page - 1) * limit` para calcular desde qué pokémon empezar
- **URL con parámetros**: `/pokemons?page=2` navega a la página 2
- **Cache strategy**: Mantenemos cache para mejorar rendimiento
- **Server Component**: La paginación funciona del lado del servidor, mejor SEO

### ✨ Funcionalidades de la paginación:

- **📄 20 pokémons por página**: Carga rápida y navegación fluida
- **🔢 Números de página**: Navegación directa a cualquier página
- **⬅️➡️ Botones anterior/siguiente**: Navegación secuencial
- **💫 Indicador visual**: Página actual resaltada
- **📱 Responsive**: Funciona perfectamente en móviles
- **⚡ Navegación rápida**: Sin recarga de página completa
- **🔄 Cache inteligente**: Páginas visitadas se cargan más rápido

### 🎯 Resultado:

Ahora puedes navegar por **todos los 1000+ pokémons** de la PokeAPI:

- `http://localhost:3000/pokemons` - Primera página (pokémons 1-20)
- `http://localhost:3000/pokemons?page=2` - Segunda página (pokémons 21-40)
- `http://localhost:3000/pokemons?page=50` - Página 50 (pokémons 981-1000)

---

## 🚀 Paso 10: Optimizaciones y mejores prácticas

### 📁 Crear utilidades en `src/lib/pokemon.ts`:

```tsx
// src/lib/pokemon.ts
export interface Pokemon {
  name: string
  url: string
}

export interface PokemonDetail {
  id: number
  name: string
  height: number
  weight: number
  sprites: { front_default: string; back_default: string }
  types: Array<{ type: { name: string } }>
  stats: Array<{ base_stat: number; stat: { name: string } }>
}

export async function fetchPokemon(name: string): Promise<PokemonDetail | null> {
  try {
    const res = await fetch(`https://pokeapi.co/api/v2/pokemon/${name.toLowerCase()}`, {
      next: { revalidate: 3600 } // Revalidar cada hora
    })
    return res.ok ? res.json() : null
  } catch {
    return null
  }
}

export async function fetchPokemonList(limit = 20): Promise<{ results: Pokemon[]; count: number }> {
  const res = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=${limit}`, {
    next: { revalidate: 3600 }
  })
  return res.json()
}
```

### 🖼️ Optimización de imágenes con `next/image`

Para mejor rendimiento y optimización automática, actualizamos todas las imágenes para usar el componente `Image` de Next.js:

```tsx
// ❌ Antes: elemento img básico
<img src={imageUrl} alt={name} className="w-20 h-20" />

// ✅ Después: componente Image optimizado
import Image from 'next/image'
<Image src={imageUrl} alt={name} width={80} height={80} className="w-20 h-20" />
```

**Configuración en `next.config.ts`:**
```ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'raw.githubusercontent.com',
        port: '',
        pathname: '/PokeAPI/sprites/master/sprites/pokemon/**',
      },
    ],
  },
};

export default nextConfig;
```

**Beneficios del componente Image:**
- ✅ **Optimización automática**: Redimensiona y comprime imágenes
- ✅ **Lazy loading**: Carga imágenes solo cuando son visibles
- ✅ **WebP automático**: Convierte a formatos modernos si el navegador lo soporta
- ✅ **LCP mejorado**: Mejora Core Web Vitals automáticamente
- ✅ **Placeholder**: Evita layout shift durante la carga

### 🎨 Actualizar layout global en `src/app/layout.tsx`:

```tsx
// src/app/layout.tsx
import type { Metadata } from 'next'
import { Inter } from 'next/font/google'
import Link from 'next/link'
import './globals.css'

const inter = Inter({ subsets: ['latin'] })

export const metadata: Metadata = {
  title: 'Pokédex Next.js',
  description: 'Aprende Next.js construyendo una Pokédex',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="es">
      <body className={inter.className}>
        <nav className="border-b border-gray-200 bg-white/95 backdrop-blur sticky top-0 z-50">
          <div className="container mx-auto px-4 py-3">
            <div className="flex items-center justify-between">
              <h1 className="text-xl font-bold">🔴 Pokédex</h1>
              <div className="space-x-4">
                <Link href="/" className="hover:text-blue-600">Inicio</Link>
                <Link href="/pokemons" className="hover:text-blue-600">Pokémons</Link>
              </div>
            </div>
          </div>
        </nav>
        {children}
      </body>
    </html>
  )
}
```

---

## 🎉 ¡Felicidades! Has completado tu Pokédex

### 📚 Resumen de lo que has aprendido:

1. **✅ Estructura de Next.js**: App Router, páginas, layouts
2. **✅ Server vs Client Components**: Cuándo usar cada uno
3. **✅ Rutas dinámicas**: `[name]` para parámetros variables  
4. **✅ API Routes**: Backend integrado en Next.js
5. **✅ Optimizaciones**: Cache, revalidación, lazy loading
6. **✅ Componentes**: Reutilización y props con TypeScript
7. **✅ UI moderna**: ShadCN/UI + Tailwind CSS
8. **✅ Arquitectura limpia**: Separación entre frontend y backend

### 🏗️ Mejores prácticas implementadas:

- **API propia**: Las páginas consumen nuestras APIs, no directamente APIs externas
- **Separación de responsabilidades**: Frontend para UI, API Routes para lógica de negocio
- **Componentización**: Componentes reutilizables desde el principio
- **Tipado fuerte**: TypeScript en todo el proyecto con interfaces específicas (sin `any`)
- **Navegación optimizada**: Uso de `<Link>` de Next.js para navegación interna
- **Imágenes optimizadas**: Componente `<Image>` de Next.js para mejor rendimiento
- **Paginación eficiente**: Navegación por páginas con URLs amigables
- **Linting estricto**: ESLint configurado para Next.js y TypeScript
- **Manejo de errores**: Control de errores tanto en cliente como servidor
- **Cache estratégico**: Optimización de rendimiento
- **Código limpio**: Sin duplicación ni refactorización innecesaria

### 🎯 Ventajas del código limpio y tipado:

- **Detección temprana de errores**: TypeScript + ESLint previenen errores comunes
- **Mejor IntelliSense**: Autocompletado y sugerencias más precisas
- **Navegación más rápida**: Componentes `<Link>` mejoran la experiencia del usuario
- **Mantenibilidad**: Código más fácil de entender y modificar
- **Escalabilidad**: Estructura preparada para crecer

### ⚠️ Cambios importantes en Next.js 15:

En Next.js 15, varios APIs que antes eran síncronos ahora son asíncronos:

- **`params`** en páginas y layouts
- **`searchParams`** en páginas  
- **`cookies()`**, **`headers()`**, **`draftMode()`**

**Antes (Next.js 14):**
```tsx
interface PageProps {
  params: { slug: string }
}
export default function Page({ params }: PageProps) {
  const { slug } = params  // ❌ Error en Next.js 15
}
```

**Ahora (Next.js 15):**
```tsx
interface PageProps {
  params: Promise<{ slug: string }>
}
export default async function Page({ params }: PageProps) {
  const { slug } = await params  // ✅ Correcto
}
```

### 🚀 Próximos pasos sugeridos:

- 🗄️ Añadir una base de datos (SQLite/PostgreSQL)
- 🔐 Implementar autenticación
- 📱 Hacer la app PWA (Progressive Web App)
- 🎨 Añadir animaciones con Framer Motion
- 📊 Implementar filtros avanzados
- 🌙 Modo oscuro
- 📈 Analytics y métricas

### 🧪 Probar tu aplicación:

**Comandos de desarrollo:**
```bash
npm run dev          # Servidor de desarrollo
npm run build        # Build para producción
npm run start        # Servidor de producción
npm run lint         # Verificar errores de linting
npm run lint -- --fix # Corregir errores de linting automáticamente
```

**APIs creadas:**
- `http://localhost:3000/api/pokemons` - Lista de pokémons
- `http://localhost:3000/api/pokemons/pikachu` - Detalle de un pokémon
- `http://localhost:3000/api/pokemons?search=pika` - Búsqueda

**Páginas funcionales:**
- `http://localhost:3000/` - Landing page
- `http://localhost:3000/pokemons` - Lista de pokémons con búsqueda integrada
- `http://localhost:3000/pokemons/pikachu` - Detalle de pokémon

### 🔧 Troubleshooting (Errores comunes):

**Error: `params should be awaited before using its properties`**
```bash
Error: Route "/pokemons/[name]" used `params.name`. `params` should be awaited before using its properties.
```

**Solución:** Await el objeto params antes de acceder a sus propiedades:

**Error: TypeScript `any` types**
```bash
Error: Unexpected any. Specify a different type.  @typescript-eslint/no-explicit-any
```

**¿Por qué es importante?** El tipo `any` elimina los beneficios de TypeScript y puede causar errores en tiempo de ejecución.

**Solución:** Definir interfaces específicas para los tipos de datos:
```tsx
// ❌ Incorrecto
const pokemon: any = await res.json()

// ✅ Correcto
interface PokemonApiListItem {
  name: string
  url: string
}
const pokemon: PokemonApiListItem = await res.json()
```

**Error: HTML anchor tags en Next.js**
```bash
Error: Do not use an `<a>` element to navigate to `/`. Use `<Link />` from `next/link` instead.
```

**¿Por qué es importante?** Los `<a>` tags causan navegación completa de página, mientras que `<Link>` permite navegación del lado del cliente (más rápida).

**Solución:** Usar componentes Link para navegación interna:
```tsx
// ❌ Incorrecto
<a href="/pokemons">Pokémons</a>

// ✅ Correcto
import Link from 'next/link'
<Link href="/pokemons">Pokémons</Link>
```
```tsx
// ❌ Incorrecto
interface PageProps {
  params: { name: string }
}
export default async function Page({ params }: PageProps) {
  const name = params.name
}

// ✅ Correcto  
interface PageProps {
  params: Promise<{ name: string }>
}
export default async function Page({ params }: PageProps) {
  const { name } = await params
}
```

**Comando automático para corregir:**
```bash
npx @next/codemod@canary next-async-request-api .
```

**Nota:** Si usas `generateMetadata`, también necesita await:
```tsx
// ✅ Correcto
interface PageProps {
  params: Promise<{ name: string }>
}
export async function generateMetadata({ params }: PageProps) {
  const { name } = await params
  return { title: `Pokémon ${name}` }
}
```

### 🔗 Recursos útiles:

- [Documentación oficial de Next.js](https://nextjs.org/docs)
- [ShadCN/UI Components](https://ui.shadcn.com)
- [PokeAPI Documentation](https://pokeapi.co)
- [Tailwind CSS](https://tailwindcss.com)

---

**¡Tu Pokédex está lista! 🎮 Ahora tienes una base sólida para construir aplicaciones más complejas con Next.js.**