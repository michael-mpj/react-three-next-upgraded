# Contributing to React Three Next

Thank you for your interest in contributing! We welcome contributions of all kinds, including bug reports, feature requests, and code improvements.

## Getting Started

### Prerequisites

- Node.js 22 or higher
- npm 10 or higher (or yarn 4+)
- Git

### Development Setup

1. **Fork the repository**

   ```bash
   # Navigate to the repo on GitHub and click "Fork"
   ```

2. **Clone your fork**

   ```bash
   git clone https://github.com/your-username/react-three-next-upgraded.git
   cd react-three-next-upgraded
   ```

3. **Add upstream remote**

   ```bash
   git remote add upstream https://github.com/michael-mpj/react-three-next-upgraded.git
   ```

4. **Install dependencies**

   ```bash
   npm install
   ```

5. **Start the development server**

   ```bash
   npm run dev
   ```

   The app will be available at `http://localhost:3000`

## Development Workflow

### Creating a Feature Branch

```bash
# Update main from upstream
git fetch upstream main
git checkout main
git merge upstream/main

# Create a feature branch
git checkout -b feature/your-feature-name
```

### Making Changes

- Follow the existing code style and patterns documented in `.github/copilot-instructions.md`
- Keep commits atomic and descriptive
- Test your changes thoroughly

### Linting & Code Quality

```bash
# Run linter
npm run lint

# Fix linting issues
npm run lint -- --fix

# Check TypeScript types
npx tsc --noEmit

# Build for production
npm run build

# Analyze bundle size
npm run analyze
```

## Commit Message Convention

Use clear, descriptive commit messages:

```
type(scope): description

[optional body]

[optional footer]
```

### Types

- `feat`: A new feature
- `fix`: A bug fix
- `docs`: Documentation only
- `style`: Code style changes (formatting, missing semicolons, etc.)
- `refactor`: Code refactoring without feature changes
- `perf`: Performance improvements
- `test`: Adding or updating tests
- `chore`: Build process, dependencies, or tooling changes

### Examples

```
feat(canvas): add post-processing effects
fix(shader): correct fragment shader precision
docs(readme): update installation instructions
chore(deps): upgrade tailwindcss to 3.4.19
```

## Pull Request Process

1. **Push to your fork**

   ```bash
   git push origin feature/your-feature-name
   ```

2. **Create a Pull Request**
   - Go to the repository and click "New Pull Request"
   - Select your fork and feature branch
   - Write a clear PR title and description
   - Link any related issues

3. **PR Description Template**

   ```markdown
   ## Description

   Brief description of what your PR does

   ## Related Issues

   Fixes #123

   ## Type of Change

   - [ ] Bug fix
   - [ ] New feature
   - [ ] Breaking change
   - [ ] Documentation

   ## Testing

   Describe how you tested these changes

   ## Checklist

   - [ ] My code follows the project's code style
   - [ ] I've run `npm run lint` and fixed any issues
   - [ ] I've tested this locally
   - [ ] My changes don't introduce new warnings
   ```

4. **CI Checks**
   - Ensure all GitHub Actions checks pass
   - Address any failures before requesting review

5. **Code Review**
   - Respond to reviewer feedback
   - Push fixes as new commits (don't force push during review)

## Reporting Bugs

Use [GitHub Issues](https://github.com/michael-mpj/react-three-next-upgraded/issues) to report bugs.

**Bug Report Template:**

```markdown
## Description

Clear description of the bug

## Steps to Reproduce

1. ...
2. ...
3. ...

## Expected Behavior

What should happen

## Actual Behavior

What actually happens

## Environment

- OS: [e.g. macOS 14.0]
- Node: [e.g. 22.0]
- npm: [e.g. 10.0]

## Additional Context

Screenshots, code snippets, or other relevant information
```

## Suggesting Enhancements

Use [GitHub Discussions](https://github.com/michael-mpj/react-three-next-upgraded/discussions) or Issues to suggest improvements.

**Feature Request Template:**

```markdown
## Description

Clear description of the feature

## Motivation

Why would this be useful?

## Proposed Solution

How should this work?

## Alternatives Considered

Other approaches you've thought about
```

## Code Style Guidelines

- Use **2 spaces** for indentation
- Use **semicolons** at the end of statements
- Use **single quotes** for strings (unless template literals are needed)
- Use **camelCase** for variables and functions
- Use **PascalCase** for components and classes
- Keep lines under 100 characters when possible
- Add comments for complex logic

### 3D Component Patterns

When adding new 3D components:

1. **Add to `src/components/canvas/Examples.jsx`**

   ```jsx
   export const MyComponent = (props) => {
     return <group {...props}>{/* Your Three.js elements */}</group>
   }
   ```

2. **Use in pages with dynamic + View**

   ```jsx
   import dynamic from 'next/dynamic'

   const MyComponent = dynamic(() => import('@/components/canvas/Examples').then((m) => m.MyComponent), { ssr: false })

   export default function Page() {
     return (
       <View>
         <MyComponent />
       </View>
     )
   }
   ```

3. **Follow Canvas Persistence Rules**
   - Never conditionally render `<Scene>` in Layout
   - Use scissor testing via `<View>` for multiple 3D regions
   - Access canvas state via `useThree()` hook

See `.github/copilot-instructions.md` for complete architecture patterns.

## Project Architecture

This project uses a unique architecture combining:

- **Next.js 16** with App Router
- **React Three Fiber** for 3D rendering
- **Tunnel-Rat** for component portaling between DOM and Canvas

The Canvas persists across page navigations, reducing overhead and enabling seamless 3D/2D integration. See `.github/copilot-instructions.md` for detailed architecture documentation.

## Resources

- [Next.js Documentation](https://nextjs.org/docs)
- [React Three Fiber Documentation](https://docs.pmnd.rs/react-three-fiber/)
- [Three.js Documentation](https://threejs.org/docs/)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [GitHub Docs](https://docs.github.com/)

## Questions or Need Help?

- Check existing [Issues](https://github.com/michael-mpj/react-three-next-upgraded/issues)
- Start a [Discussion](https://github.com/michael-mpj/react-three-next-upgraded/discussions)
- Review `.github/copilot-instructions.md` for architecture patterns

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

Thank you for contributing to making this project better! 🚀
