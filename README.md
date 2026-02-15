# IP Tracking (Vue 3 + Vite)

A minimal, modern IP tracking app built with Vue 3 and Vite. It uses public geolocation data from [ipapi.co](https://ipapi.co/), maps rendered by [Leaflet](https://leafletjs.com/) and tiles from [OpenStreetMap](https://www.openstreetmap.org/).

## Features

- Search IP address (IPv4/IPv6) and view details
- “My IP” quick action
- Interactive map with marker and radius
- Minimalist, responsive UI (desktop & mobile)
- Rate-limit handling (shows a modal for HTTP 429)

## Tech Stack

- Vue 3.5
- Vue Router 5
- Vite 7
- Tailwind CSS 3
- Leaflet

## Requirements

- Node.js 18+ and npm
- Recommended: [VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=johnsoncodehk.volar) (disable Vetur)

## Getting Started

Install dependencies:

```sh
npm install
```

Start development server:

```sh
npm run dev
```

Build for production:

```sh
npm run build
```

Preview production build locally:

```sh
npm run preview
```

## Configuration

- Vite base is set to a subpath for GitHub Pages deployment. Adjust `base` in [vite.config.js](file:///Users/pantera/project/node/ip-tracking-vue/vite.config.js) if deploying under a different path.

## Deployment

GitHub Pages: https://bagoespantera.github.io/ip-tracking-vue/

## Screenshots

Home
![Home Screenshot](https://github.com/BagoesPantera/ip-tracking-vue/blob/main/screenshots/HomeView.png?raw=true)
