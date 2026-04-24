# AGENTS.md

## 📚 Project Overview
This repository contains a Progressive Web App (PWA) implementing a two-player, local backgammon game that uses WebRTC for Peer-to-Peer (P2P) communication.

### 📂 Codebase Structure
The application is highly centralized, with all HTML structure, CSS styling, and JavaScript game logic contained within a single file: `index.html`.

*   **`index.html`**: Primary entry point. Contains the game UI (setup screens, board layout, dice), embedded CSS, and the entire game logic (WebRTC setup, game state management, movement functions).
*   **`manifest.json`**: Defines the PWA metadata (name, display mode, icons).
*   **`sw.js`**: Service worker responsible for caching core assets to enable offline functionality.

### 🛠️ Conventions & Architecture
*   **Design Pattern**: Single-file architecture (Monolithic). All components are tightly coupled.
*   **Communication**: P2P communication is established using `RTCPeerConnection` and `DataChannel`. State synchronization occurs by sending JSON messages (`STATE` and `ROLL` types) across the established data channel.
*   **Game State**: The game state is managed by a single array, `gameState` (size 24), where positive values indicate White checkers and negative values indicate Black checkers at a given point index.

---

## 🐛 Identified Issues & Quality Concerns

The current architecture prioritizes simplicity over robustness, leading to several areas for improvement:

### 1. UX Flaw (High Priority)
*   **Location**: `index.html` (Lines 131-134)
*   **Issue**: When a checker is dragged to the 'Transfer Zone', the target point index is determined using a `prompt()` dialog. This is a poor user experience for a PWA and is prone to user error.
*   **Recommendation**: Implement a proper UI element (e.g., a modal or dropdown) to select the target point instead of relying on native prompts.

### 2. Robustness & Error Handling (Medium Priority)
*   **Location**: `index.html` (Line 163: `sync(data) { if(dc?.readyState === 'open') dc.send(JSON.stringify(data)); }`)
*   **Issue**: The network synchronization (`sync`) function lacks error handling. If the data channel is closed unexpectedly (e.g., peer disconnects), `dc.send()` could throw an error, crashing the application loop.
*   **Recommendation**: Implement a `try...catch` block or check the connection state more aggressively before sending data.

### 3. Code Clarity & Maintainability (Low Priority)
*   **Location**: `index.html` (Lines 85-86)
*   **Issue**: The initial game setup (`gameState`) is hardcoded directly within the main script block. While simple, this makes it difficult to verify or adjust standard backgammon rules without diving deep into the monolithic code.
*   **Recommendation**: Abstract the initial configuration into a constant or configuration object for better readability (though this violates the current simplicity constraint).

### 🔒 Security Vulnerabilities
No critical security vulnerabilities (e.g., XSS, insecure data transmission) were found, assuming the P2P link is established securely. However, relying solely on a public STUN server is a known limitation for highly restrictive network environments.

## ✅ Summary of Actions
The core functionality is operational, but the UX and robustness need attention. Due to the strict constraint of maintaining the existing three-file, highly centralized architecture, significant fixes (like replacing `prompt()`) would break the current design philosophy. The issues have been documented here for future development.