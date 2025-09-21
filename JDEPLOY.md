# jDeploy Setup for LLMS

This project has been configured for jDeploy deployment. jDeploy allows easy distribution of Java desktop applications as native installers and npm packages.

## Overview

The LLMS project is now configured for cross-platform deployment using jDeploy with:
- **Cross-platform JAR**: Includes native libraries for Linux (x64/ARM64), macOS (Intel/Apple Silicon), and Windows (x64)
- **Platform-specific bundles**: Optimized installers for each platform (reduces size from ~90MB to ~30MB per platform)
- **GitHub Actions**: Automated building and publishing workflow
- **Native installers**: Windows MSI, macOS DMG, Linux AppImage

## Quick Start

### Prerequisites
- Node.js installed
- Java 21 installed
- Project builds successfully with `./gradlew :composeApp:buildExecutableJar`

### Local Development
```bash
# Install jDeploy locally
npx @shannah/jdeploy install

# Build the application
npx @shannah/jdeploy build

# Test the built application
npx @shannah/jdeploy run

# Publish to npm (requires npm account)
npx @shannah/jdeploy publish
```

### Automated Deployment
The project includes a GitHub Actions workflow (`.github/workflows/jdeploy.yml`) that automatically:
1. Builds the cross-platform JAR
2. Creates platform-specific installers
3. Publishes to GitHub Releases
4. Optionally creates signed macOS DMG files

## Configuration Files

### package.json
- **name**: `llms` (npm package name)
- **jdeploy.jar**: `composeApp/build/libs/composeApp-1.0.0-all.jar`
- **jdeploy.javaVersion**: `21`
- **jdeploy.platformBundlesEnabled**: `true` (enables platform-specific optimization)
- **buildCommand**: `./gradlew :composeApp:buildExecutableJar`

### Platform Optimization
The following `.jdpignore.*` files optimize bundle sizes by excluding unnecessary native libraries:
- `.jdpignore.linux-x64` - Linux x86_64 bundle
- `.jdpignore.linux-arm64` - Linux ARM64 bundle  
- `.jdpignore.mac-x64` - macOS Intel bundle
- `.jdpignore.mac-arm64` - macOS Apple Silicon bundle
- `.jdpignore.win-x64` - Windows x64 bundle

### Application Icon
- `icon.png` (1024x1024) - Used for desktop app icon and installers

## Build Configuration

### Cross-Platform Dependencies
The `composeApp/build.gradle.kts` now includes all desktop platforms:
```kotlin
implementation(compose.desktop.linux_x64)
implementation(compose.desktop.linux_arm64)  
implementation(compose.desktop.macos_x64)
implementation(compose.desktop.macos_arm64)
implementation(compose.desktop.windows_x64)
```

### Shadow JAR
The Shadow plugin creates a fat JAR with all dependencies included for distribution.

## Distribution

Users can install LLMS in multiple ways:

### npm Global Install
```bash
npm install -g llms
llms
```

### Direct Download
Download platform-specific installers from GitHub Releases:
- Windows: `.msi` installer
- macOS: `.dmg` installer  
- Linux: `.AppImage` portable app

### Web Installer
Visit the jDeploy web installer page for guided installation.

## Supported Platforms

- ✅ Linux x86_64
- ✅ Linux ARM64
- ✅ macOS Intel (x86_64)
- ✅ macOS Apple Silicon (ARM64)
- ✅ Windows x86_64
- ❌ Windows ARM64 (not yet supported by Compose Multiplatform)

## Next Steps

1. **Test the build**: Run `./gradlew :composeApp:buildExecutableJar` to ensure the JAR builds successfully
2. **Test jDeploy locally**: Use `npx @shannah/jdeploy build` to test the complete pipeline
3. **Setup GitHub secrets**: For DMG signing, add macOS developer certificates to repository secrets
4. **Publish**: Use GitHub releases or `npx @shannah/jdeploy publish` to distribute

## Troubleshooting

- **JAR not found**: Verify the JAR path in `package.json` matches build output
- **Missing native libraries**: Ensure all platform dependencies are included in `build.gradle.kts`
- **Large bundle size**: Platform-specific bundles are enabled to optimize size
- **Build failures**: Check Java version compatibility and Gradle dependencies

For more information, see the [jDeploy documentation](https://www.jdeploy.com).