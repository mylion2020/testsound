# AGENTS.md - Uni-app Android Audio Testing Project

## Project Overview
This is a Uni-app project for testing Android native audio features including:
- **ASR (Automatic Speech Recognition)** - via `android-asr` UTS module
- **TTS (Text-to-Speech)** - via `android-utils` UTS module
- Cross-platform mobile app development using Vue.js 3

## Build & Development Commands

### Development Build
```bash
# Run in HBuilderX IDE or use CLI:
# Development mode (hot reload)
npm run dev:app-plus

# Build for production
npm run build:app-plus

# Run on Android device/emulator
npm run dev:app-android

# Run on iOS simulator
npm run dev:app-ios
```

### Platform-Specific Builds
```bash
# Android APK build
npm run build:app-android

# iOS IPA build  
npm run build:app-ios

# Web build
npm run build:h5

# WeChat Mini Program
npm run build:mp-weixin
```

### Testing Commands
```bash
# Run unit tests (if configured)
npm test

# Run specific test file
npm test -- --testPathPattern=test-asr

# Run tests in watch mode
npm run test:watch
```

## Code Style Guidelines

### File Structure
```
testsound/
├── pages/              # Vue page components
│   ├── index/         # Home page
│   ├── test-asr/      # ASR testing page  
│   └── test-tts/      # TTS testing page
├── uni_modules/        # UTS native modules
│   ├── android-asr/   # Speech recognition
│   ├── android-utils/ # TTS and utilities
│   └── android-asr-test/
├── static/            # Static assets
├── manifest.json      # App configuration
└── pages.json        # Page routing
```

### Vue Component Structure
Follow this template for Vue 3 components:
```vue
<template>
  <!-- Template with uni-app components -->
</template>

<script>
  // Import UTS modules at top
  import { AndroidASR } from "@/uni_modules/android-asr"
  
  export default {
    data() {
      return {
        // Reactive state
      }
    },
    onLoad() {
      // Lifecycle hooks
    },
    methods: {
      // Component methods
    }
  }
</script>

<style>
  /* Component styles with rpx units */
</style>
```

### Import Conventions
- **UTS modules**: Use `import { ModuleName } from "@/uni_modules/module-name"`
- **Relative imports**: Use `@/` alias for project root
- **External libraries**: Import at top of script section

### Naming Conventions
- **Files**: kebab-case (`test-asr.vue`, `index.uts`)
- **Components**: PascalCase in templates, kebab-case in file names
- **Variables**: camelCase (`resultText`, `isListening`)
- **Constants**: UPPER_SNAKE_CASE (`MAX_VOLUME`, `DEFAULT_CONFIG`)
- **Methods**: camelCase (`startListening()`, `handleError()`)

### TypeScript/UTS Guidelines
- Use explicit types in UTS files:
  ```uts
  private speechRecognizer: SpeechRecognizer | null = null
  private isListening: boolean = false
  ```
- Define interfaces for complex data structures
- Use union types for nullable values

### Error Handling
- **UTS modules**: Use try-catch with specific error messages
- **Vue components**: Show user-friendly error states
- **Async operations**: Handle promise rejections
- **Permission errors**: Gracefully handle Android permission denials

Example error handling pattern:
```javascript
try {
  await this.asr.startListening()
} catch (error) {
  this.addLog(`Error: ${error.message}`)
  this.status = "Error occurred"
}
```

### State Management
- Use Vue `data()` for component state
- For complex state, consider Pinia (if configured)
- Keep state minimal and focused on UI needs
- Use computed properties for derived state

### Styling Guidelines
- Use `rpx` units for responsive design
- Follow BEM-like class naming: `.result-area__title`
- Keep styles scoped to components
- Use CSS variables for theming

### UTS Module Development
When working with UTS modules:
1. **Platform-specific code**: Place in `utssdk/app-android/` or `utssdk/app-ios/`
2. **Shared interfaces**: Define in `utssdk/interface.uts`
3. **Error types**: Use `UniError` from `io.dcloud.uts`
4. **Callback handling**: Use `UniJSCallback` for JS communication

### Android Native Integration
- **Permissions**: Declare in `manifest.json` under `app-plus.distribute.android.permissions`
- **Native APIs**: Access via UTS imports (`import SpeechRecognizer from "android.speech.SpeechRecognizer"`)
- **Lifecycle**: Handle in `onLoad()` and `onUnload()` hooks
- **Memory**: Clean up native resources in `onUnload()`

### Testing Patterns
- **Unit tests**: Test business logic in isolation
- **Component tests**: Test Vue component behavior
- **Integration tests**: Test UTS module integration
- **Manual testing**: Required for native features

### Git & Version Control
- **Commit messages**: Use conventional commits format
- **Branch naming**: `feature/`, `fix/`, `docs/`, `chore/`
- **Versioning**: Follow semantic versioning for UTS modules

### Performance Considerations
- **Memory**: Destroy UTS instances in `onUnload()`
- **Rendering**: Use `v-if` over `v-show` for conditional heavy components
- **Lists**: Virtualize long lists in scroll views
- **Images**: Optimize static assets in `static/` folder

### Platform-Specific Notes
- **Android**: Minimum API level 21 (Android 5.0)
- **iOS**: Requires Xcode and iOS SDK
- **HarmonyOS**: Supported via UTS modules
- **Web**: Limited native feature support

## Development Workflow
1. **Setup**: Install HBuilderX or configure CLI environment
2. **Development**: Use hot reload during development
3. **Testing**: Test on target platforms (Android/iOS)
4. **Build**: Generate platform-specific packages
5. **Deploy**: Distribute via app stores or internal channels

## Troubleshooting
- **UTS compilation errors**: Check UTS syntax and imports
- **Permission issues**: Verify manifest permissions
- **Native API failures**: Check Android/iOS version compatibility
- **Build failures**: Clean build artifacts and retry

## Agent Instructions
When working on this codebase:
1. Always check `manifest.json` for app configuration
2. Review existing UTS modules for patterns
3. Test changes on target platforms
4. Follow Vue 3 and uni-app conventions
5. Handle platform-specific code appropriately