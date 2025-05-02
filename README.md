# VISIOSCAN AI - MVP Repository

A consolidated monorepo containing four core modules for real‑time radiology AI:

1. **Inference Engine** – Real‑time pathology detection on CXR & Head CT (Python, PyTorch, CUDA)
2. **Radiologist Web UI** – OHIF extension inside PACS (React, MVVM)
3. **Patient & Trainee Portal** – Bilingual 3D explainer (Next.js SSR, CDN‑first)
4. **DICOM Router & Gateway** – Pushes studies to GPU box (Go, Orthanc plugin, failsafe buffering)

---

## 1. Inference Engine

*(See `visioscan_mvp_inference/` in previous sections.)*

---

## 2. Radiologist Web UI

*(See `visioscan_webui/` in previous sections.)*

---

## 3. Patient & Trainee Portal

*(See `visioscan_portal/` in previous sections.)*

---

## 4. DICOM Router & Gateway

An event‑driven Go service that receives HTTP hooks from an Orthanc plugin, buffers failures on disk, and reliably dispatches new studies to the GPU inference engine.

```
visioscan_router/
├── README.md
├── go.mod
├── cmd/
│   └── router/
│       └── main.go
└── pkg/
    ├── orthanc_plugin/
    │   ├── plugin.cpp
    │   └── CMakeLists.txt
    ├── buffer/
    │   └── buffer.go
    └── dispatcher/
        └── dispatcher.go
```

--- README.md ---
```markdown
# VISIOSCAN AI - DICOM Router & Gateway

An event‑driven DICOM router and gateway service:

- **Event-driven**: Gin HTTP server handling `/hooks/study` posts
- **Orthanc plugin**: C++ plugin forwards `OnStoredInstance` events
- **Failsafe buffering**: Disk-backed JSON queue for retrying failures
- **Reliable dispatch**: Worker retries buffered events every 30s

## Requirements
- Go 1.19+
- Orthanc ≥1.7 with plugin support
- CMake ≥3.10, libcurl, JsonCpp
- GPU inference engine endpoint (e.g. `http://gpu-engine:8000/process`)

## Build Orthanc Plugin
```bash
cd pkg/orthanc_plugin
mkdir build && cd build
cmake .. && make
sudo cp VisioScanRouterPlugin.so /usr/lib/orthanc/plugins/
```

## Build Go Router
```bash
cd cmd/router
go build -o visioscan-router .
```

## Run Router
```bash
./visioscan-router
```
