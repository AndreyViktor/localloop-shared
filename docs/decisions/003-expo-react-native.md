# ADR-003: Expo (React Native) for mobile

## Status
Accepted

## Date
2024-03-18

## Context
LocalLoop needs a mobile app for iOS and Android. The team is a solo developer. We needed:
- A single codebase for both platforms
- Good support for geolocation, push notifications, secure storage, and deep links
- Fast iteration without native toolchain complexity

## Decision
We use **Expo SDK 55 (React Native)** with the managed workflow.

Expo provides `expo-location`, `expo-secure-store`, `expo-linking`, and Expo Push Notifications — covering all of LocalLoop's native needs. The managed workflow avoids Xcode/Android Studio complexity for the MVP phase.

If a native module is needed that Expo doesn't support, we can eject to the bare workflow at that point without discarding the existing code.

## Consequences

### Positive
- Single codebase for iOS + Android
- Expo SDK covers all required native capabilities (location, secure storage, push)
- Fast OTA updates via Expo Updates (when needed)
- No native toolchain required during development

### Negative
- Managed workflow imposes some constraints on native modules
- Expo SDK updates can be breaking — must pin version carefully

### Neutral
- Deep link scheme configured: `localloop://`

## Alternatives considered

| Option | Why rejected |
|--------|-------------|
| React Native CLI (bare) | More complexity for solo dev at MVP stage; can migrate later if needed |
| Flutter | Team has React/TypeScript experience; no Flutter expertise |
| Native iOS + Android | Too slow for solo dev at MVP stage |
