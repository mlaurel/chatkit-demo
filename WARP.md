# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a ChatKit starter template built with Next.js that integrates with OpenAI's ChatKit web component to create conversational AI interfaces. The project demonstrates how to bootstrap a ChatKit application with minimal configuration while providing a foundation for more complex implementations.

## Common Development Commands

### Setup and Installation
```bash
npm install
cp .env.example .env.local
```

### Development
```bash
# Start development server (default: http://localhost:3000)
npm run dev

# Build for production
npm run build

# Start production server
npm start

# Run linter
npm run lint
```

## Architecture Overview

### Core Components Architecture
- **App.tsx**: Root application component that manages global state and event handlers
- **ChatKitPanel.tsx**: Main ChatKit integration component that handles session management, theming, error states, and client tool integrations
- **ErrorOverlay.tsx**: Centralized error display component with retry functionality
- **useColorScheme.ts**: Hook for managing light/dark theme preferences with system integration and localStorage persistence

### API Layer
- **app/api/create-session/route.ts**: Edge runtime API route that creates ChatKit sessions by proxying requests to OpenAI's ChatKit API, handles user session management via cookies, and provides comprehensive error handling

### Configuration System
- **lib/config.ts**: Central configuration hub for workflow ID, session endpoints, starter prompts, and UI text
- Environment variables managed through `.env.local` (OPENAI_API_KEY, NEXT_PUBLIC_CHATKIT_WORKFLOW_ID, optional CHATKIT_API_BASE)

### Key Features
- **Session Management**: Automatic user ID generation and cookie-based session persistence (30-day expiration)
- **Client Tool Integration**: Built-in support for theme switching (`switch_theme`) and fact recording (`record_fact`) client tools
- **Error Handling**: Multi-layered error handling for script loading, session creation, and API integration failures
- **Theme System**: Dynamic light/dark mode with system preference detection and user override capability
- **Edge Runtime**: API routes optimized for Edge runtime deployment

## Development Guidelines

### Environment Configuration
1. Copy `.env.example` to `.env.local`
2. Set `OPENAI_API_KEY` with a ChatKit-enabled API key
3. Set `NEXT_PUBLIC_CHATKIT_WORKFLOW_ID` with your workflow ID from Agent Builder
4. Optionally set `CHATKIT_API_BASE` for custom API endpoints

### Client Tool Development
When adding new client tools, implement them in the `onClientTool` handler in `ChatKitPanel.tsx`. Follow the existing pattern:
```typescript
if (invocation.name === "your_tool_name") {
  // Handle tool logic
  return { success: true }; // or { success: false }
}
```

### Theme Customization
- Modify theme settings in the `useChatKit` hook configuration within `ChatKitPanel.tsx`
- Color scheme logic is centralized in `useColorScheme.ts` hook
- Tailwind classes handle the UI theming throughout the application

### Error Handling Patterns
- API errors are extracted using `extractUpstreamError()` utility
- Client-side errors use the `ErrorOverlay` component with optional retry functionality
- Development mode includes extensive console logging for debugging

### ChatKit Integration Points
- Script loading managed through Next.js `Script` component in `layout.tsx`
- Session initialization handled in `getClientSecret` callback
- Event handlers for `onResponseStart`, `onResponseEnd`, `onThreadChange`, and `onError`

## File Structure Notes

### Key Paths
- `app/api/create-session/route.ts` - Session creation endpoint
- `components/ChatKitPanel.tsx` - Main ChatKit integration
- `lib/config.ts` - Central configuration
- `hooks/useColorScheme.ts` - Theme management

### TypeScript Configuration
- Uses `@/*` path alias for clean imports
- Configured for Next.js with strict TypeScript settings
- Target ES2017 with modern module resolution

## Deployment Considerations

### Environment Variables
- `OPENAI_API_KEY`: Required for ChatKit API access
- `NEXT_PUBLIC_CHATKIT_WORKFLOW_ID`: Workflow ID from OpenAI Agent Builder
- `CHATKIT_API_BASE`: Optional custom API base URL (defaults to https://api.openai.com)

### Security
- API key never exposed to client-side code
- Session cookies are HttpOnly with SameSite=Lax
- Secure flag enabled in production
- Edge runtime for optimal performance and security