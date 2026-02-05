# React Three Next

[![Downloads](https://img.shields.io/npm/dt/create-r3f-app.svg?style=flat&colorA=000000&colorB=000000)](https://www.npmjs.com/package/create-r3f-app) [![Discord Shield](https://img.shields.io/discord/740090768164651008?style=flat&colorA=000000&colorB=000000&label=discord&logo=discord&logoColor=ffffff)](https://discord.gg/ZZjjNvJ)

> A **modern, production-ready starter** combining Next.js 14, React Three Fiber, and Three.js with persistent 3D canvas rendering across page navigations.

![React Three Next Demo](https://user-images.githubusercontent.com/2223602/192515435-a3d2c1bb-b79a-428e-92e5-f44c97a54bf7.jpg)

## Why React Three Next?

This starter solves a critical challenge in 3D web applications: **maintaining a persistent Three.js canvas across page navigations** while seamlessly swapping DOM content. This architecture eliminates expensive WebGL context recreation and enables smooth 3D/2D synchronization.

### Performance Highlights

- **TTL**: ~100ms (Time to interactive)
- **First Load JS**: ~79KB
- **Lighthouse Score**: 100 (Performance, Accessibility, Best Practices, SEO)
- **Canvas Persistence**: No recreation on route changes (~100ms savings per navigation)

### Live Demo

[Interactive Demo](https://react-three-next.vercel.app/)

## Features

✅ **Persistent Canvas** – Canvas survives page navigations, only DOM content swaps  
✅ **Portal Rendering** – 3D components renderable anywhere in the DOM via `<View>`  
✅ **GLSL Shader Support** – Import and use shaders as string templates  
✅ **Next.js 14 App Router** – Modern, file-based routing with App directory  
✅ **PWA Support** – Progressive Web App ready (disabled in dev, enabled in prod)  
✅ **Optimized Bundle** – Preloads assets and caches textures/geometries  
✅ **Scissor Optimization** – Multiple 3D viewports without full canvas redraws  
✅ **TypeScript Ready** – Full TypeScript support via optional build flag  
✅ **Tailwind CSS** – Preconfigured styling framework

## Quick Start

### Installation

```bash
yarn create r3f-app next my-app
# or
npx create-r3f-app next my-app
```

**With TypeScript:**

```bash
yarn create r3f-app next my-app -ts
# or
npx create-r3f-app next my-app -ts
```

**With styled-components:**

```bash
yarn create r3f-app next my-app styled
# or (Tailwind is default)
yarn create r3f-app next my-app tailwind
```

### Development

```bash
cd my-app
yarn dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

## Core Architecture

### The Problem & Solution

Traditional 3D web apps recreate the WebGL context on every page navigation, destroying canvases and expensive 3D state. This starter uses **component portaling** to separate the persistent Canvas renderer from the transient DOM renderer.

```
┌─────────────────────────────────────┐
│  DOM Pages (Next.js App Router)     │
│  <Layout>                           │
│    <Page1>  <Page2>  <Page3>        │
│      ↓        ↓        ↓            │
│   <View>   <View>   <View>          │
└────────────┬────────────────────────┘
             │ tunnel-rat portal
             ↓
┌─────────────────────────────────────┐
│  Persistent Canvas (Three.js)       │
│  <Canvas>                           │
│    <r3f.Out />  (portal outlet)     │
│    <Preload />                      │
└─────────────────────────────────────┘
```

### Key Components

| Component                         | Purpose                                                |
| --------------------------------- | ------------------------------------------------------ |
| `app/layout.jsx`                  | Root layout wrapping entire app in `<Layout>` provider |
| `app/page.jsx`                    | Example home page with `<View>` portals                |
| `src/components/canvas/Scene.jsx` | Persistent Canvas with `<r3f.Out />` outlet            |
| `src/components/canvas/View.jsx`  | DOM-side portal component linking to canvas            |
| `src/helpers/global.js`           | Exports tunnel-rat instance for component portaling    |

## Usage Patterns

### Rendering 3D Components in Pages

3D components must be wrapped with `dynamic()` to avoid SSR and hydration issues:

```jsx
'use client'

import dynamic from 'next/dynamic'

const Dog = dynamic(() => import('@/components/canvas/Examples').then((m) => m.Dog), { ssr: false })
const View = dynamic(() => import('@/components/canvas/View').then((m) => m.View), { ssr: false })

export default function Page() {
  return (
    <View className='h-96 w-full'>
      <Dog scale={2} position={[0, 0, 0]} />
    </View>
  )
}
```

### Creating 3D Components

Export components from `src/components/canvas/Examples.jsx`:

```jsx
export const Dog = (props) => {
  const { scene } = useGLTF('/dog.glb')
  return <primitive object={scene} {...props} />
}
```

### Adding Lighting & Camera

Include the `<Common>` component for default scene setup:

```jsx
import dynamic from 'next/dynamic'
const Common = dynamic(() => import('@/components/canvas/View').then((m) => m.Common), { ssr: false })

export default function Page() {
  return (
    <View className='h-96'>
      <Common color='#ffffff' />
      <YourComponent />
    </View>
  )
}
```

### Using Shaders

Place GLSL files in `src/templates/Shader/glsl/` and import as strings:

```jsx
import fragmentShader from '@/templates/Shader/glsl/shader.frag'
import vertexShader from '@/templates/Shader/glsl/shader.vert'

const material = new THREE.ShaderMaterial({
  fragmentShader,
  vertexShader,
  uniforms: { ... }
})
```

## Available Scripts

| Command           | Purpose                                    |
| ----------------- | ------------------------------------------ |
| `yarn dev`        | Start Next.js dev server (localhost:3000)  |
| `yarn build`      | Production build                           |
| `yarn start`      | Serve production build                     |
| `yarn lint --fix` | ESLint + Prettier code quality audit       |
| `yarn analyze`    | Bundle size analysis with webpack analyzer |

## Stack & Dependencies

### Core Runtime

- **[Next.js 14](https://nextjs.org/)** – React framework with App Router
- **[React 18](https://react.dev/)** – UI library
- **[Three.js](https://threejs.org/)** – 3D graphics library
- **[@react-three/fiber](https://docs.pmndrs.org/react-three-fiber/)** – React renderer for Three.js
- **[@react-three/drei](https://github.com/pmndrs/drei)** – Helpful utilities (cameras, controls, loaders)
- **[tunnel-rat](https://github.com/pmndrs/tunnel-rat)** – Portal system for component rendering separation
- **[Tailwind CSS](https://tailwindcss.com/)** – Utility-first CSS framework

### Build & Development

- **[webpack](https://webpack.js.org/)** – Module bundler (configured via Next.js)
- **[raw-loader + glslify-loader](https://www.npmjs.com/package/raw-loader)** – GLSL shader imports
- **[PostCSS + Autoprefixer](https://postcss.org/)** – CSS transformations
- **[@next/bundle-analyzer](https://www.npmjs.com/package/@next/bundle-analyzer)** – Bundle size analysis
- **[ESLint + Prettier](https://eslint.org/)** – Code quality and formatting

### Progressive Enhancement

- **[@ducanh2912/next-pwa](https://github.com/DuCanhGit/next-pwa)** – PWA support (production only, disabled in dev)

## Configuration Files

| File                 | Purpose                                     |
| -------------------- | ------------------------------------------- |
| `jsconfig.json`      | Path aliases (`@/` prefix)                  |
| `next.config.js`     | Next.js config, webpack loaders for shaders |
| `tailwind.config.js` | Tailwind CSS customization                  |
| `postcss.config.js`  | PostCSS plugins                             |
| `package.json`       | Dependencies and scripts                    |

## Common Patterns & Best Practices

### Import Path Aliases

All code uses `@/` prefix configured in `jsconfig.json`:

```jsx
import { View } from '@/components/canvas/View'
import { r3f } from '@/helpers/global'
import shader from '@/templates/Shader/glsl/shader.frag'
```

### Preventing Hydration Mismatches

Always use `dynamic()` with `ssr: false` for components accessing browser APIs:

```jsx
// ❌ Causes hydration error
import { View } from '@/components/canvas/View'

// ✅ Correct
const View = dynamic(() => import('@/components/canvas/View').then((m) => m.View), { ssr: false })
```

### Canvas Persistence

The Canvas in `src/components/canvas/Scene.jsx` **must never be conditionally rendered**. It persists across all page navigations. Modify it to configure global Three.js state:

```jsx
<Canvas onCreated={(state) => {
  state.gl.toneMapping = THREE.AgXToneMapping
  state.gl.toneMappingExposure = 1.5
}}>
```

### Viewport Scissoring for Performance

The `<View>` component uses `gl.scissor()` to render multiple 3D regions efficiently. Each `<View>` gets its own viewport segment without redrawing the entire canvas.

### Lazy Loading & Code Splitting

Use `Suspense` with `dynamic()` for better initial load performance:

```jsx
<Suspense fallback={<LoadingSpinner />}>
  <Model />
</Suspense>
```

## Troubleshooting

### Canvas not showing

- Ensure `<Scene>` in `Layout.jsx` is **not conditionally rendered**
- Verify `<View>` component wraps your 3D content
- Check browser console for WebGL errors

### 3D components not rendering

- Wrap with `dynamic(..., { ssr: false })`
- Use `<View>` portal component
- Ensure `<Common>` is included for lighting/camera

### Hydration errors

- Use `dynamic(..., { ssr: false })` for any component with browser APIs
- Add `'use client'` directive to page files

### Bundle size issues

- Run `yarn analyze` to identify large dependencies
- Use tree-shaking: avoid importing entire Three.js or drei libraries
- Lazy load 3D components with `dynamic()`

## Contributing

Contributions welcome! To set up a development environment:

```bash
git clone https://github.com/pmndrs/react-three-next
cd react-three-next
yarn install
yarn dev
```

## License

MIT © Renaud Rohlinger

## Credits & Inspiration

- [Poimandres](https://github.com/pmndrs/) – React Three Fiber ecosystem
- [Vercel](https://vercel.com/) – Next.js and deployment platform

---

**Maintainer:** [@onirenaud](https://twitter.com/onirenaud)  
**Community:** [Discord](https://discord.gg/ZZjjNvJ)
