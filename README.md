
<p align="center">
  <img src="logo.png" alt="CollectivAI Logo" width="260" />
</p>

# CollectiVAI Control Center (macOS)

> **Status:** Architecture & UI only – all integrations are running in **Safe Mode** (no real shell commands executed yet).  
> This app is the **central cockpit** for a local AI + Security + Crypto ecosystem on a **MacBook Pro M2 Max**.

---

## ✨ Overview

The **CollectiVAI Control Center** is a SwiftUI macOS app that acts as a  
**single pane of glass** for a complex local ecosystem:

- Local AI (Ollama, PrivateGPT, agents)
- Crypto & Nodes (Bitcoin Core, Geth, Lightning, Bittensor/TAO)
- Security / Blue Team tools (Tor, dnscrypt-proxy, firewall, SwiftBar)
- Hardware & Infra (UTM VMs, TAO server, WiFi hardware, etc.)
- Wallet overview (BTC / ETH / TAO & more)
- Alerts, logs, profiles and Safe Mode

At the current stage, the app focuses on:

- **Architecture**
- **UX / Structure**
- **Enterprise-style dashboard feeling**

No real system commands are executed yet – everything is still **simulated**  
and prepared for future live integration.

---

## 🧱 Current Features (Architecture Level)

### 1. Root Navigation

- `CollectivAIControlCenterApp`
  - Sets up the main environment:
    - `AppState`
    - `CollectivAISettings` (profiles, Safe Mode, paths)
    - `SystemHealthModel` (dummy system health)
    - `WalletCenterModel` (dummy wallets)
    - `CollectivAlertCenterModel` (central alert bus)
  - Renders:
    - `WelcomeView` (entry screen)
    - `MainControlCenterView` (primary dashboard)

### 2. Main Control Center

`MainControlCenterView` contains:

- **Sidebar** with sections:
  - `Overview`
  - `AI & Agents`
  - `Crypto & Nodes`
  - `Security / Blue Team`
  - `Stack / Inventory`
  - `TAO / Hardware`
  - `Dev & GitHub`
  - `Settings`
- **Header bar** with:
  - Active section name
  - **Profile badge** (`Lab`, `Stealth`, `Default`, …)
  - **Safe Mode badge** (Safe Mode / Live Mode)
  - Global **“Refresh Snapshot”** button  
    → currently only creates a **dummy alert** via `CollectivAlertCenterModel`.

- **Content area** with panel routing:
  - `OverviewPanel`
  - `AIAgentsPanel` (placeholder)
  - `CryptoNodesPanel`
  - `SecurityPanel` (placeholder)
  - `Stack / Inventory` (placeholder)
  - `TAO / Hardware` (placeholder)
  - `Dev & GitHub` (placeholder)
  - `SettingsView`

### 3. Settings & Profiles

`CollectivAISettings` (ObservableObject):

- Stores:
  - Active profile (**Default / Lab / Stealth / Custom**)
  - **Safe Mode** state
  - Important paths:
    - Bitcoin datadir
    - Geth datadir
    - Lightning datadir
    - TAO / UTM base dir
    - AI base dirs (Ollama, PrivateGPT), etc.
- Provides:
  - `activeProfileDisplayName`
  - Short profile summaries for the UI
- All panels read from this model → central configuration hub.

### 4. System Health (Dummy)

`SystemHealthModel`:

- Holds **simulated** health data:
  - Global score (e.g. `92%`)
  - AI load level (Low/Medium/High)
  - Node counts
- Used in:
  - `OverviewPanel` metrics and snapshot rows
- Later: will be fed by real shell-based monitors.

### 5. Wallet Center (Architecture)

`WalletCenterModel`:

- Holds placeholder wallet entries for:
  - BTC
  - ETH
  - TAO
  - (future chains)
- Prepared panel: `WalletsPanel` (not yet fully wired into sidebar in this repo snapshot)
- Goal: central view for local node-based balances & external wallets.

### 6. Alerts & Logs

`CollectivAlertCenterModel`:

- Central **alert bus** for the entire app.
- API roughly:
  ```swift
  func addAlert(level: AppAlert.Level, source: String, message: String)
  ```
- Used by:
  - `MainControlCenterView` (Refresh button)
  - `CryptoNodesPanel` (Quick Actions)
- Future: dedicated `AlertsPanel` to visualize events/log stream.

### 7. Crypto & Nodes Panel (Dummy)

`CryptoNodesPanel`:

- Shows **descriptive cards** for:
  - Bitcoin Core
  - Ethereum Geth
  - Lightning (LND/CLN)
  - Bittensor TAO
- Reads paths from `CollectivAISettings`.
- Has **Quick Actions** (all simulated):
  - `Simulate Start All Nodes`
  - `Simulate BTC Sync Monitor`
  - `Simulate Ethereum Suite Check`
- Each action only sends an **Alert** to `CollectivAlertCenterModel`  
  → no shell commands, fully **Safe Mode ready**.

---

## 📁 Project Structure (high-level)

Suggested folder layout:

```text
CollectivAIControlCenter/
├─ App/
│  ├─ CollectivAIControlCenterApp.swift
│  ├─ AppState.swift
│  └─ RootView.swift
│
├─ Models/
│  ├─ CollectivAISettings.swift
│  ├─ SystemHealthModel.swift
│  ├─ WalletCenterModel.swift
│  └─ CollectivAlertCenterModel.swift
│
├─ Views/
│  ├─ MainControlCenterView.swift
│  ├─ WelcomeView.swift
│  ├─ SettingsView.swift
│  ├─ Panels/
│  │  ├─ OverviewPanel.swift        (oder in MainControlCenterView.swift)
│  │  ├─ AIAgentsPanel.swift
│  │  ├─ CryptoNodesPanel.swift
│  │  ├─ SecurityPanel.swift
│  │  ├─ WalletsPanel.swift
│  │  └─ AlertsPanel.swift          (planned)
│
└─ Resources/
   ├─ Assets.xcassets
   └─ logo.png
```

*(Names can differ slightly depending on your current repo,  
but conceptually this is the structure.)*

---

## 🛠 Getting Started (Xcode)

**Requirements:**

- macOS 15.x (Sequoia or similar)
- Xcode 16+ (SwiftUI + Swift Concurrency)
- Apple Silicon (M2 Max) – but project should also run on Intel for UI only

**Build steps:**

1. Clone the repository:
   ```bash
   git clone https://github.com/collectiv-ai/collectivai-control-center.git
   cd collectivai-control-center
   ```
2. Open the project in Xcode:
   ```bash
   open CollectivAIControlCenter.xcodeproj
   ```
3. Select the **CollectivAIControlCenter** app target.
4. Run on **My Mac (Designed for macOS)**.

Since everything currently runs in **Safe Mode / Dummy Mode**,  
you don’t need any external services to just explore the UI.

---

## 🚦 Safe Mode vs. Live Mode (Future)

Right now the app is **intentionally not executing** shell commands.

- **Safe Mode (current default):**
  - Only simulates actions.
  - Writes alerts/log entries.
  - No interaction with `zsh`, Homebrew, nodes, etc.

- **Future Live Mode (planned):**
  - Controlled via `CollectivAISettings.safeModeEnabled`.
  - Will allow:
    - Shell-based status checks
    - Start/stop scripts
    - Deep audits of AI, nodes, security stack

The architecture is ready – but real integration happens **later**,  
once all security checks and flow are fully defined.

---

## 📌 Roadmap

Short version of the planned next steps:

- **Playbooks Panel**
  - Morning Routine (AI + Nodes + Security snapshot)
  - Deep Audit Routine
  - Stealth Routine (minimal surface, Tor/VPN focus)
- **Alerts & Logs Panel**
  - Timeline of events from all panels
  - Filter by level (info/warn/error)
  - Export to file
- **AI & Agents Panel**
  - Integration previews for:
    - Ollama models
    - PrivateGPT instances
    - Local agents / workers
- **Security / Blue Team Panel**
  - Visual representation of:
    - Tor + dnscrypt status
    - Firewall (LuLu) state (simulated)
    - SwiftBar plugin status
- **Wallets & Balances**
  - Basic views approximating:
    - BTC / Lightning
    - ETH / ERC-20
    - TAO
  - Later: direct node queries once Live Mode is enabled.

---

## 🤝 Contributing / Ideas

This project is currently focused on the **local ecosystem** of one power-user  
(MacBook Pro M2 Max + nodes + VMs + hardware).

If you have ideas for:

- better UX for complex local stacks,
- safer Live Mode execution,
- or general improvements for operator dashboards,

feel free to open issues or propose concepts.

---

## License

TBD – project is currently in an **experimental** state.  
Check this repository for updates on licensing.
