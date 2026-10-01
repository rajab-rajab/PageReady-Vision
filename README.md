# PageReady Vision

PageReady Vision is an agentic document-quality gate. It inspects an uploaded page, uses OpenCV evidence to choose a corrective or escalation tool, verifies any correction, and emits an auditable trace.

## Golden path

1. Analyze a document's visual evidence.
2. If the page has a confident, recoverable small skew, call `auto_correct`.
3. Re-analyze the corrected output with `verify_corrected_page`.
4. Approve only when verification succeeds; otherwise route it to human review.

The first milestone deliberately limits automatic correction to small skew. Blur, content near a frame edge, low contrast, conflicting evidence, and failed correction are fail-safe routes to human review or rescan.

## Local development

Use a working Python 3.11+ installation, then install the package and tests:

```powershell
.\.venv\Scripts\python.exe -m pip install -e ".[dev]"
.\.venv\Scripts\python.exe -m pytest -q
```

Run an image through the local golden path:

```powershell
.\.venv\Scripts\python.exe -m pageready.cli analyze .\example.png --output-dir .\runs
```

Run the local full-stack console in two terminals:

```powershell
.\.venv\Scripts\python.exe -m uvicorn pageready.api:app --host 127.0.0.1 --port 8000
```

```powershell
Set-Location frontend
npm install
npm run dev
```

The Vite development server proxies `/api` to FastAPI. The local session resets on browser refresh by design.

## Docker and benchmark evidence

Build and run the full-stack container locally:

```powershell
docker build -t pageready-vision:local .
docker run --rm -p 8000:8000 pageready-vision:local
```

Build an ARM64-compatible image when preparing an AWS Graviton run:

```powershell
docker buildx build --platform linux/arm64 -t pageready-vision:arm64 --load .
```

Capture a local benchmark artifact:

```powershell
.\.venv\Scripts\python.exe scripts\benchmark.py --runs 20 --output runs\benchmark.json
```

The resulting JSON documents operating-system, architecture, Python/OpenCV versions, fixture outcomes, median/p95 timing, and a comparison placeholder. It is **not** AWS Graviton or COOL evidence unless the same command runs on documented AWS ARM64/Graviton hardware with the intended runtime.

## Deployment note

The source uses stable OpenCV APIs so it can be tested locally. The competition deployment must run its core image workload with OpenCV 5 and, for the COOL track, verify the COOL runtime on AWS Graviton before claiming a benchmark result. Do not label a local development wheel, an ARM64-compatible container, or a local benchmark as COOL.
