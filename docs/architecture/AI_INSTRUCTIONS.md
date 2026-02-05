# AI Copilot Instructions for React-Three-Next

## Architecture Overview

This is a **Next.js 14 starter** combining React, Three.js, and React Three Fiber with a critical architectural constraint: **the Canvas persists across page navigations** while DOM content swaps. This eliminates canvas recreation overhead and enables seamless 3D/2D synchronization.

### Key Pattern: Component Portaling with Tunnel-Rat

The architecture uses `tunnel-rat` to portal React Three Fiber components between two separate React renderers:

- **DOM Renderer**: React's standard DOM rendering in the `app/` directory (Next.js App Router)
- **Canvas Renderer**: Three.js Canvas context, persisted in `src/components/canvas/Scene.jsx`

**Flow**: 3D components imported in DOM pages are wrapped with `dynamic(() => ..., { ssr: false })`, then rendered via `<View>` component → tunneled through `r3f` (tunnel-rat instance from `src/helpers/global.js`) → rendered in the persistent Canvas via `<r3f.Out />`.

## Directory Structure & Responsibilities

```
app/                   # Next.js App Router (DOM layer)
  layout.jsx          # Root layout wraps everything in <Layout>
  page.jsx            # Home page with <View> portals to canvas
  blob/page.jsx       # Example additional route

src/
  components/
    canvas/
      View.jsx        # ForwardRef portal component (DOM side)
      Scene.jsx       # Persistent Canvas + <r3f.Out /> outlet
      Examples.jsx    # 3D components (Logo, Dog, Duck, etc.)
    dom/
      Layout.jsx      # Wraps app with Layout provider, loads Scene
  helpers/
    global.js         # Exports tunnel-rat instance `r3f`
    components/
      Three.jsx       # Wrapper exposing <r3f.In> for 3D children
```

## Critical Development Patterns

### 1. Rendering 3D Objects in DOM Pages

Use `dynamic()` with `ssr: false` and `<View>` portal:

```jsx
const Dog = dynamic(() => import('@/components/canvas/Examples').then((m) => m.Dog), { ssr: false })

export default function Page() {
  return (
    <View className='h-96'>
      <Dog scale={2} />
    </View>
  )
}
```

- **Why no SSR**: Canvas/Three.js requires browser APIs (WebGL, window, document)
- **Why dynamic()**: Prevents hydration mismatches
- **Why <View>**: Portals the 3D component through tunnel-rat to the Canvas

### 2. Canvas Initialization

In `Scene.jsx`, the Canvas is initialized once and reused. The `onCreated` callback configures global Three.js state:

```jsx
<Canvas onCreated={(state) => (state.gl.toneMapping = THREE.AgXToneMapping)}>
```

Modifications here affect all 3D content globally across all pages.

### 3. Common 3D Setup

The `<Common>` component provides standard 3D utilities (lighting, camera):

```jsx
export const Common = ({ color }) => (
  <ambientLight />
  <pointLight position={[20, 30, 10]} intensity={3} decay={0.2} />
  <PerspectiveCamera makeDefault fov={40} position={[0, 0, 6]} />
)
```

Include in any `<View>` that needs default lighting/camera.

## Build & Dev Workflow

```bash
yarn dev              # Start Next.js dev server (localhost:3000)
yarn build            # Production build
yarn start            # Serve production build
yarn lint --fix       # ESLint with Prettier
yarn analyze          # Bundle size analysis (ANALYZE=true next build)
```

**Note**: No separate build step for shaders or Three.js—webpack is configured to handle `.glsl` files via `raw-loader` + `glslify-loader`.

## Import Aliases & Path Resolution

All imports use `@/` prefix (configured in `jsconfig.json`):

- `@/components/` → `app/` or `src/`
- `@/helpers/` → `src/helpers/`

Example:

```jsx
import { View } from '@/components/canvas/View'
import { r3f } from '@/helpers/global'
```

## Key Dependencies & Integration Points

- **@react-three/fiber**: React renderer for Three.js (manages Canvas & lifecycle)
- **@react-three/drei**: Helpers (OrbitControls, PerspectiveCamera, Preload, etc.)
- **tunnel-rat**: Portal system enabling 3D/DOM separation
- **three**: 3D library (via drei's Preload for asset optimization)
- **@ducanh2912/next-pwa**: PWA support (dev disabled, prod enabled)
- **Tailwind CSS**: Styling (configured in `tailwind.config.js`)
- **PostCSS/Autoprefixer**: CSS transformations

## Shader Loading

GLSL shaders (`.glsl`, `.vert`, `.frag`) are imported as raw text strings via webpack:

```jsx
import shader from '@/shader.frag' // Returns string
// Use in: new THREE.ShaderMaterial({ fragmentShader: shader })
```

Located in: `src/templates/Shader/glsl/` (examples: `shader.vert`, `shader.frag`)

## Performance Considerations

1. **Canvas Persistence**: Avoids WebGL context recreation on navigation (gains ~100ms TTL)
2. **gl.scissor**: View component uses viewport scissoring to render multiple 3D regions without full canvas redraws
3. **Suspense & lazy loading**: Use for 3D components to prevent blocking renders
4. **Preload all**: `<Preload all />` in Scene.jsx caches textures/geometries loaded via drei

## PWA & Metadata

- PWA manifest at `public/manifest.json`
- Metadata in `app/layout.jsx` via `export const metadata = {...}`
- Robots.txt at `public/robots.txt`

## Common Pitfalls

- **Hydration mismatch**: Always wrap 3D components with `dynamic(..., { ssr: false })`
- **Canvas recreation**: Do not conditionally render `<Scene>` in Layout; it must persist
- **Direct canvas access**: Access canvas state via `useThree()` hook, not DOM manipulation
- **Event propagation**: `Layout` passes `eventSource` to Scene to sync DOM events; don't bypass
- **Styling portaled content**: 3D components inside `<View>` use canvas positioning, not CSS

## When Adding New Features

- **New 3D component**: Add to `src/components/canvas/Examples.jsx`, export, import with `dynamic()` in pages
- **New page with 3D**: Create route in `app/`, use `<View>` + `dynamic()` imports
- **Modify canvas behavior**: Edit `Scene.jsx` or use `useThree()` hook within 3D components
- **Add shader**: Place in `src/templates/Shader/glsl/`, import as string
- **Add styling**: Use Tailwind classes (DOM) or Three.js materials (canvas)

## Lighthouse Targets

- **Performance**: 100 (first load ~79KB JS, TTL ~100ms)
- **Accessibility**: 100
- **Best Practices**: 100
- **SEO**: 100

Avoid breaking these by keeping bundle size controlled (use `yarn analyze` to check) and maintaining semantic HTML.
