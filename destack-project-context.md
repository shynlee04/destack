# Destack Project Setup and Deployment Configuration Context

## Project Overview

**Project Root**: `/Users/apple/Documents/coding-projects/destack`
**Project Type**: Next.js-based Page Builder with Grapes.js Integration
**Deployment Platform**: Vercel
**Current State**: Clean demo setup with simplified configuration

## Project Structure & Architecture

### Monorepo Workspace Structure
The Destack project uses a monorepo architecture with the following key directories:

```
/Users/apple/Documents/coding-projects/destack/
├── lib/                          # Core Destack library (Grapes.js + React)
├── dev/
│   ├── nextjs-project/           # Main Next.js demo project
│   ├── nextjs-app-project/       # Additional Next.js app project
│   └── react-project/            # React-only demo project
├── e2e/                          # End-to-end test configurations
├── scripts/                      # Build and utility scripts
└── data/                         # Project data files
```

### Key Package Configurations

#### Root Package.json (`/Users/apple/Documents/coding-projects/destack/package.json`)
- **Workspace Configuration**: Uses npm workspaces for monorepo management
- **Main Projects**: `./lib`, `./dev/nextjs-project`, `./dev/nextjs-app-project`, `./dev/react-project`, `./scripts`
- **Key Scripts**:
  - `start:next`: Runs Next.js project from dev directory
  - `build:next`: Builds both lib and Next.js project
  - **Critical Change**: `prepare` script modified to `"prepare": "echo 'Skipping husky install'"` to avoid CI conflicts

#### Next.js Project Package.json (`/Users/apple/Documents/coding-projects/destack/dev/nextjs-project/package.json`)
- **Project Name**: `next-destack-example`
- **Destack Integration**: Local dependency `"destack": "file:../../lib/"`
- **Core Dependencies**: Next.js ^13.4.5, React ^18.2.0, React-DOM ^18.2.0
- **Development Tools**: Bundle analyzer, ESLint plugin for Next.js

#### Core Destack Library (`/Users/apple/Documents/coding-projects/destack/lib/package.json`)
- **Version**: 3.0.8
- **Description**: Page builder based on Next.js, Grapes.js & Tailwind CSS
- **Key Dependencies**:
  - GrapesJS ^0.19.5 (Visual editor)
  - Craft.js ^0.2.0-beta.8 (React state management)
  - Formidable ^1.2.2 (File upload handling)
  - Radix UI components
  - Tailwind CSS
- **Peer Dependencies**: Next.js, React, React-DOM

## Data Storage Structure

### Data Files Location
- **Main Data**: `/Users/apple/Documents/coding-projects/destack/dev/nextjs-project/data/`
- **Current State**: Clean demo files (minimal content for reliable deployment)
- **Files**:
  - `default.json`: `{}` (empty configuration)
  - `default.html`: `<div>Not found</div>` (minimal placeholder)

## Deployment Configuration Journey

### Initial Vercel Deployment Issues
**Problem**: Initial deployments failed due to complex configuration and build process conflicts
**Root Causes**:
- Complex build scripts conflicting with Next.js standard build process
- Husky installation conflicts in CI environment
- Multiple build configurations causing schema validation errors

### Solution Evolution Through Multiple Iterations

#### Attempt 1: Complex Configuration
```json
{
  "builds": [
    {
      "src": "dev/nextjs-project/**",
      "use": "@vercel/next"
    }
  ],
  "env": {
    "NODE_ENV": "production"
  }
}
```
**Issue**: Vercel schema validation error - deprecated `builds` property

#### Attempt 2: Simple Build Configuration
```json
{
  "buildCommand": "cd dev/nextjs-project && npm run build",
  "installCommand": "cd dev/nextjs-project && npm ci --production=false"
}
```
**Issue**: Still failed due to workspace and dependency conflicts

#### Final Working Configuration
```json
{
  "installCommand": "cd dev/nextjs-project && npm ci --production=false",
  "buildCommand": "cd dev/nextjs-project && npm run build"
}
```
**Success Factors**:
- Simplified to minimal commands only
- Explicit working directory specification
- Using `npm ci` instead of `npm install` for clean dependency installation
- Production dependencies disabled to keep dev dependencies

## Authentication & Git Setup

### GitHub Authentication Transition
**Initial Setup**: mbpfws account
**Transition**: Switched to shynlee04 account for project ownership

### GitHub CLI Authentication Process
**Steps Taken**:
1. `gh auth status` - Checked current authentication state
2. `gh auth logout` - Cleared existing credentials
3. `gh auth login` - Initiated new authentication flow
4. Selected GitHub.com option
5. Chose device authentication method
6. Completed browser-based authorization

**Command Used**:
```bash
gh auth login --web
```

### Credential Management on macOS
**Issues Encountered**: Git credential conflicts and authentication persistence
**Solutions**:
- Cleared credential cache with `git credential-manager uninstall`
- Re-authenticated using GitHub CLI (more reliable than ssh keys)
- Used GitHub's recommended authentication flow

## Problem-Solving Patterns & Technical Decisions

### 1. Vercel Schema Validation Requirements
**Lesson**: Vercel has strict schema validation and deprecates certain properties
**Solution**: Use minimal, standard configuration without deprecated `builds` array
**Pattern**: Start simple, gradually add complexity only if needed

### 2. Husky Installation Conflicts in CI
**Problem**: Husky Git hooks installation conflicts with Vercel build process
**Solution**: Modified root package.json prepare script to skip husky installation:
```json
"prepare": "echo 'Skipping husky install'"
```
**Rationale**: Development tools should not interfere with production deployment

### 3. Nested Next.js Project Structure Support
**Challenge**: Monorepo structure with nested Next.js projects
**Solution**: Explicit working directory specification in Vercel config:
```json
{
  "installCommand": "cd dev/nextjs-project && npm ci --production=false",
  "buildCommand": "cd dev/nextjs-project && npm run build"
}
```
**Key Insight**: Vercel needs explicit directory navigation for non-root projects

### 4. Dependency Management Strategy
**Decision**: Use `npm ci` instead of `npm install` for deployment
**Benefits**:
- Clean, reproducible dependency installation
- Faster than npm install for large projects
- Consistent with CI/CD best practices
**Trade-off**: Slower initial setup but more reliable builds

## Environment-Specific Behavior

### Development vs Production Detection
**Destack Architecture**: Environment detection through `NODE_ENV` variable
**Development Mode**: Destack editor and live editing features available
**Production Mode**: Static rendering, no editor features

### Clean Demo State Configuration
**Purpose**: Provide reliable default state for demonstrations
**Implementation**:
- Minimal data files (`default.json: {}`, `default.html: <div>Not found</div>`)
- No custom content or configurations that might break deployment
- Focus on core functionality demonstration

## CI/CD Considerations

### Build Process Optimization
**Current Configuration**: Optimized for speed and reliability
- Parallel builds where possible
- Clean dependency installation
- Minimized build commands

### Development Tools Exclusion
**Strategy**: Exclude development-only tools from production builds
- Husky hooks disabled via prepare script modification
- Linting tools excluded from deployment process
- Test runners not included in production build

## Final Working Configuration Summary

### Vercel Configuration (`vercel.json`)
```json
{
  "installCommand": "cd dev/nextjs-project && npm ci --production=false",
  "buildCommand": "cd dev/nextjs-project && npm run build"
}
```

### Package.json Modifications
```json
"prepare": "echo 'Skipping husky install'"
```

### Data Files
- `data/default.json`: `{}` (empty configuration)
- `data/default.html`: `<div>Not found</div>` (placeholder content)

### Git Configuration
- Authentication: GitHub CLI with shynlee04 account
- Remote origin: configured for project repository

## Technical Decisions Rationale

### 1. Simplified Vercel Configuration
**Decision**: Minimal configuration over complex builds array
**Rationale**: Reduces deployment complexity and validation errors
**Impact**: More reliable, easier to maintain deployment process

### 2. Husky Bypass for CI Environments
**Decision**: Skip husky installation during prepare script
**Rationale**: Prevents Git hook conflicts in automated environments
**Impact**: Cleaner build process, no unexpected side effects

### 3. Nested Project Structure Support
**Decision**: Explicit working directory specification
**Rationale**: Required for proper Next.js project identification
**Impact**: Enables deployment of monorepo-based Next.js applications

### 4. Clean Reset for Demo Purposes
**Decision**: Minimal content for default state
**Rationale**: Prevents deployment issues from complex initial content
**Impact**: More reliable demo with consistent behavior

## Lessons Learned

### Vercel Deployment Patterns
1. **Schema Validation**: Always test configuration against current Vercel schema
2. **Minimal Configuration**: Start with simple configuration, add complexity only when needed
3. **Working Directory**: Always specify correct working directory for nested projects

### Git Authentication Best Practices
1. **GitHub CLI**: More reliable than SSH keys for authentication
2. **Credential Management**: Regular credential cache clearing prevents conflicts
3. **Account Management**: Proper account switching for different projects

### Monorepo Deployment Considerations
1. **Workspace Dependencies**: Proper handling of workspace dependencies in deployment
2. **Build Order**: Correct build sequence for interdependent projects
3. **Environment Variables**: Proper environment configuration for different project types

### Development vs Production Separation
1. **Feature Detection**: Use environment variables for feature availability
2. **Static Optimization**: Production builds should be fully static
3. **Editor Exclusion**: Development tools should not be included in production builds

## Future Reference

### Quick Setup Checklist
1. [ ] Verify GitHub CLI authentication
2. [ ] Update vercel.json with correct paths
3. [ ] Modify prepare script to skip husky if needed
4. [ ] Clean data files for demo state
5. [ ] Test build process locally before deployment
6. [ ] Verify working directory specification

### Troubleshooting Guide
- **Deployment Failure**: Check Vercel configuration syntax and paths
- **Authentication Issues**: Clear credentials and re-authenticate with GitHub CLI
- **Build Errors**: Verify working directory and dependencies
- **Husky Conflicts**: Temporarily modify prepare script

---

**Document Created**: 2025-11-05
**Project Status**: Clean demo setup with working Vercel deployment
**Tags**: destack, vercel-deployment, nextjs, configuration, git-authentication, project-setup, cleanup