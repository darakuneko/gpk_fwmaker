# GPK FWMaker

**QMK/Vial Firmware Build API** - Containerized keyboard firmware build service

[\![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=flat&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[\![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)](https://www.docker.com/)
[\![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=node.js&logoColor=white)](https://nodejs.org/)
[\![QMK](https://img.shields.io/badge/QMK-1e88e5?style=flat&logo=qmk&logoColor=white)](https://qmk.fm/)
[\![Vial](https://img.shields.io/badge/Vial-purple?style=flat)](https://get.vial.today/)
[\![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/darakuneko/gpk_fwmaker)

🌐 English | [🇯🇵 日本語](README.ja.md)

## Overview

GPK FWMaker is a containerized service that provides a REST API for generating QMK/Vial keyboard firmware. It automates the entire process from keyboard files to firmware compilation.

### Key Features

- **QMK/Vial Firmware Build** - Automated building via API
- **File Conversion** - KLE JSON → QMK/Vial, Vial → C keymap
- **Keyboard File Generation** - Automatic project file creation
- **Vial Unique ID Generation** - ID generation for Vial projects
- **Full Docker Support** - Ready-to-use containerized environment

### Current Status

- **Complete TypeScript Migration** - Enhanced type safety and code quality
- **Vitest Testing Environment** - Comprehensive API testing setup
- **Docker Environment Verified** - Production-ready deployment
- **Quality Management System** - Automated checks and test execution

---

## Quick Start

### Prerequisites

- **Docker Desktop** installed  
  Download: https://www.docker.com

### 1. Download Project

```bash
# Clone from GitHub
git clone https://github.com/darakuneko/gpk_fwmaker.git
cd gpk_fwmaker

# Or download ZIP
# https://github.com/darakuneko/gpk_fwmaker/archive/refs/heads/main.zip
```

### 2. Build and Start Docker Environment

```bash
# Build Docker image
docker compose build

# Start services
docker compose up -d
```

### 3. Verify Installation

```bash
# Check service status
curl http://127.0.0.1:3123/
# → Returns "GPK FWMaker\!"

# Get QMK tags list
curl http://127.0.0.1:3123/tags/qmk
# → Returns ["0.29.4", "0.29.3", ...]
```

### 4. Firmware Build Examples

```bash
# QMK firmware build
curl -X POST -H "Content-Type: application/json" \
  -d '{"kb": "reviung/reviung41", "km": "default", "tag": "0.19.3"}' \
  http://127.0.0.1:3123/build/qmk

# Vial firmware build
curl -X POST -H "Content-Type: application/json" \
  -d '{"kb": "reviung/reviung41", "km": "vial"}' \
  http://127.0.0.1:3123/build/vial
```

---

## File Management

### GPKFW Directory

After startup, a `GPKFW` folder is automatically created in your home directory:

```
GPKFW/
├── keyboards/     # Keyboard files location
├── firmware/      # Generated firmware files
└── generated/     # Auto-generated files
```

**Examples**:
- Windows: `C:\\Users\\[username]\\GPKFW`
- macOS: `/Users/[username]/GPKFW`
- Linux: `/home/[username]/GPKFW`

---

## API Reference

**Base URL**: `http://127.0.0.1:3123`

### Firmware Build

#### QMK Firmware Build
```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"kb": "reviung/reviung41", "km": "default", "tag": "0.19.3"}' \
  http://127.0.0.1:3123/build/qmk
```
- **kb** (required): Keyboard name
- **km** (required): Keymap name
- **tag** (required): QMK version tag

#### Vial Firmware Build
```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"kb": "reviung/reviung41", "km": "vial"}' \
  http://127.0.0.1:3123/build/vial
```
- **kb** (required): Keyboard name
- **km** (required): Keymap name
- **commit** (optional): Vial commit specification

#### Custom Firmware Build
```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"fw": "custom", "kb": "my_keyboard", "km": "default"}' \
  http://127.0.0.1:3123/build/custom
```

### Information Retrieval

#### Get QMK Tags List
```bash
curl http://127.0.0.1:3123/tags/qmk
# → ["0.29.4", "0.29.3", "0.29.2", ...]
```

#### Get Keyboard Lists
```bash
# QMK keyboards list
curl http://127.0.0.1:3123/list/qmk

# Vial keyboards list
curl http://127.0.0.1:3123/list/vial
```

#### Generate Vial Unique ID
```bash
curl http://127.0.0.1:3123/generate/vial/id
# → "0x12345678"
```

### File Conversion

#### KLE JSON → QMK/Vial Conversion
```javascript
const formData = new FormData()
formData.append('kle', kleJsonFile)
formData.append('params', JSON.stringify({
  kb: 'my_keyboard',
  mcu: 'atmega32u4',
  user: 'username',
  vid: '0xFEED',
  pid: '0x0001',
  option: 1,  // 0: QMK, 1: Vial, 2: via.json only
  rows: 'GP0,GP1,GP2,GP3',
  cols: 'GP4,GP5,GP6,GP7'
}))

fetch('http://127.0.0.1:3123/convert/kle/qmk', {
  method: 'POST',
  body: formData
})
```

#### info.json + KLE → via.json Conversion
```javascript
const formData = new FormData()
formData.append('info', infoJsonFile)
formData.append('kle', kleJsonFile)

fetch('http://127.0.0.1:3123/convert/via/json', {
  method: 'POST',
  body: formData
})
```

#### Vial JSON → C keymap Conversion
```javascript
const formData = new FormData()
formData.append('vial', vialJsonFile)

fetch('http://127.0.0.1:3123/convert/vial/json', {
  method: 'POST',
  body: formData
})
```

### Management Functions

#### Repository Updates
```bash
# Update QMK repository
curl http://127.0.0.1:3123/update/repository/qmk

# Update Vial repository
curl http://127.0.0.1:3123/update/repository/vial
```

#### Generate QMK Keyboard Files
```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"kb": "my_keyboard", "mcu": "atmega32u4", "layout": "60_ansi", "user": "username"}' \
  http://127.0.0.1:3123/generate/qmk/file
```

---

## Development Environment

### Local Development

```bash
cd server

# Install dependencies
npm install

# TypeScript type checking
npm run type-check

# Run tests
npm run test

# Linting
npm run lint:ts

# Build
npm run build

# Start development server
npm run dev
```

### Quality Checks

```bash
# Comprehensive quality check (type check + linting)
npm run check

# Run tests
npm run test

# Run tests with coverage
npm run test:coverage
```

### Testing Environment

```bash
# Unit tests
npm run test:unit

# API tests
npm run test:api

# Watch mode
npm run test:watch

# UI dashboard
npm run test:ui
```

---

## Architecture

### Project Structure

```
gpk_fwmaker/
├── Dockerfile              # Docker configuration
├── compose.yml              # Docker Compose configuration
├── server/                  # Node.js API server
│   ├── src/
│   │   ├── app.ts             # Express main application
│   │   ├── command.ts         # QMK/Vial command execution
│   │   └── vial2c/
│   │       └── vial2c.ts      # Vial→C keymap conversion
│   ├── tests/                 # Test suite
│   ├── dist/                  # Build output
│   └── tsconfig.json          # TypeScript configuration
└── firmware-scripts/        # Python conversion scripts
```

### Technology Stack

- **Backend**: Node.js + Express + TypeScript
- **Scripts**: Python + Flask
- **Testing**: Vitest + SuperTest
- **Container**: Docker + Docker Compose
- **Build Tools**: TypeScript Compiler
- **Quality Management**: ESLint + TypeScript

---

## Troubleshooting

### Common Issues

#### Build Errors
```bash
error: branch 'x.x.x' not found.
```
**Solution**: Update repository
```bash
curl http://127.0.0.1:3123/update/repository/qmk
```

#### Docker Startup Failure
**Solution**: Ensure Docker Desktop is running

#### Port Conflicts
**Solution**: Verify port 3123 is not in use
```bash
lsof -i :3123
```

### Support

- [Issues](https://github.com/darakuneko/gpk_fwmaker/issues)

---

## Related Projects

### Detailed Guide
**DeepWiki - GPK FWMaker**  
https://deepwiki.com/darakuneko/gpk_fwmaker

### GUI Version
**GPK FWBuilder**  
https://github.com/darakuneko/gpk_fwbuilder

### Reference Project
**Firmware Scripts** by zykrah  
https://github.com/zykrah/firmware-scripts

### Official Documentation
- [QMK Firmware](https://docs.qmk.fm)
- [Vial](https://get.vial.today/docs/porting-to-via.html)

---

## Support & Contributing

### Developer Support

**Buy me a coffee**  
[Amazon Wishlist](https://www.amazon.co.jp/hz/wishlist/ls/66VQJTRHISQT) | [Ko-fi](https://ko-fi.com/darakuneko)

### How to Contribute

1. Fork this repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Create Pull Request

---

## License

This project is released under the [MIT License](LICENSE).

---

<div align="center">

**GPK FWMaker - Making QMK/Vial firmware generation easier**

Made with ❤️ by [darakuneko](https://github.com/darakuneko)

</div>
