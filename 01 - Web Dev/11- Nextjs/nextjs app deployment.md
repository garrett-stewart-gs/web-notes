## How to Deploy a Nextjs App

### Build vs Export
  - Build is for Dynamic websites, export is for Static websites
  - 9/10 times, use build, not export.

#### Build
  - Build (SSR) → outputs .next/ (or .next/standalone):
  - Supports SSR, API routes, server actions, ISR, middleware.
  - You run a Node process: node server.js (standalone) or next start.

