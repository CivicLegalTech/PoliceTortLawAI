# Police TortLaw AI v2.1

A Windows desktop workspace for local document review, civil-liability research, and speech-assisted rebuttal review. Uses IBM Granite 4.0 Micro through a local llama.cpp-compatible server. Original application code is MIT licensed.

## Download and start

Open [Releases](https://github.com/GreyAreaHacks/PoliceTortLawAI/releases). Download **both** `PoliceTortLawAI-v2.1-Windows-App.zip` and `PoliceTortLawAI-v2.1-Granite-Model.zip`.

1. Extract both ZIPs into the **same destination**, such as `Downloads/TortLawRelease`. Both contain a `PoliceTortLawAI` folder; merge that folder when prompted. Do not extract each ZIP into a separate automatically named folder.
2. Check that `PoliceTortLawAI/models/granite-4.0-micro-Q4_K_M.gguf` exists beside the `application`, `python`, and `runtime` folders.
3. Open `PoliceTortLawAI/Install.cmd` to create a separate installation and desktop shortcut. Or run `Start Police TortLaw AI.cmd` in the extracted folder for portable use.
4. Wait for Granite to load, then use the GUI. Closing this GUI stops the server that this launcher started.

The Windows package includes Python, dependencies, Vosk English speech model, and CUDA runtime libraries. No separate Ollama or CUDA Toolkit installation is required. A compatible **NVIDIA display driver** is required. The ZIP installer is not a newly signed MSI/EXE. Keep Windows protections enabled. Allow at least 12 GB free space for download, extraction, and a separate installation.

## GPU compatibility

**Tested:** Windows x64, NVIDIA GTX 1650 with 4 GB VRAM, driver 621.14. Granite inference and the local server connection passed on that machine. The launcher uses 24 GPU layers, 4,096 context tokens, and one server slot: this is **partial GPU offload**, not a guarantee of 100% GPU utilization. Speech recognition runs on CPU.

Other NVIDIA GPUs require a CUDA-compatible driver and a runtime built for their architecture; they have not been validated with this release. More VRAM can permit more layers or context. Do not assume every newer or older GPU works with this bundled CUDA 12 runtime. AMD, Intel, and Apple GPUs are not supported by this particular Windows CUDA bundle. Source users can separately configure a suitable llama.cpp backend.

For out-of-memory errors, close other GPU-heavy apps, reduce `-ngl` (GPU layers) or `-c` (context) in `launch.py`, and retry. A port-in-use message means port 18080 is occupied; close the earlier instance. See `logs/server.log` for launch failures. Do not open the server to the public internet.

## Source code and development

Download and extract **`PoliceTortLawAI-v2.1-Source.zip`** in this repository or the release assets. It contains the application, workflows, templates, tests, public research summaries, and installer source. The source archive excludes Python binaries, vendor packages, model weights, runtime binaries, personal databases, and logs. The GitHub-generated source archive alone contains the publishing files and this source ZIP; extract the included source ZIP to work on the application.

Install Python 3.11 x64 with Tkinter, then from the extracted source folder:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

Download `vosk-model-small-en-us-0.15` from the [official Vosk model page](https://alphacephei.com/vosk/models) and extract into `models/vosk-model-small-en-us-0.15` for speech recognition.

Download the Q4_K_M GGUF from [IBM Granite's official model repository](https://huggingface.co/ibm-granite/granite-4.0-micro-GGUF). For a separate server, obtain a Windows x64 CUDA build and matching CUDA DLL package **from the same release and CUDA version** on [official llama.cpp releases](https://github.com/ggml-org/llama.cpp/releases). Extract both into one runtime folder. Asset names can change; do not mix CPU builds, ARM64 builds, CUDA versions, or release revisions. Follow [upstream build instructions](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md) when a compatible prebuilt package is unavailable.

Example server command (adjust paths):

```powershell
.\runtime\llama-server.exe -m .\models\granite-4.0-micro-Q4_K_M.gguf -a local --host 127.0.0.1 --port 8080 -c 4096 -ngl 24 -np 1 -b 256 -ub 128
```

In a second terminal, from the extracted source folder:

```powershell
$env:TORTLAW_AI_ENDPOINT = 'http://127.0.0.1:8080'
.\.venv\Scripts\python.exe app\screenshot_gui.py
```

The source GUI writes data to `user-data` beside the application. Optional BGE embeddings need separate packages and weights; the default search uses SQLite FTS5. The `installer` folder contains build/restore scripts that expect the complete binary bundle layout, not just the source archive.

Run tests with `.\.venv\Scripts\python.exe -m unittest discover -s tests`.

## Voice and rebuttal review

Open **Voice (Mic)**, select a microphone, and click **Start Listening**. Listening is explicit, not automatic. Activity refreshes every 400 milliseconds; that is not the time required to analyze a statement. Finalized statements are reviewed against imported source excerpts, with automatic review throttled to at least eight seconds between starts. Low recognition confidence or insufficient evidence should return a request for context. Quotes are checked against excerpts, and a second model pass challenges potential contradictions. A possible-rebuttal alert is a suggestion for human review, not a legal ruling or calibrated probability. Raw audio is not saved by the application.

## Scope and verification

The working GUI, local Granite inference with CUDA, PDF/DOCX imports, speech model loading, and evidence-gating tests were checked on one Windows laptop. There has been no independent security audit, broad GPU certification, or legal validation of generated output. Case summaries are preliminary research aids; verify original opinions, citations, jurisdiction, and subsequent history. OCR is not included. This software does not determine whether a claim or rebuttal is legally valid and is not a substitute for professional advice.

Public downloads contain no personal document library, microphone recordings, account keys, or runtime logs. Back up `application/user-data` and original documents separately after use. Model inference uses localhost; downloading dependencies/models requires internet access.

## Licenses and credits

See **LICENSE.txt** (MIT) and **LICENSE-SCOPE.txt** for original code. Third-party packages retain their own licenses, included in their distribution folders. IBM Granite 4.0 Micro and the included small English Vosk model are Apache-2.0 licensed. The binary package includes llama.cpp libraries distributed with Ollama 0.40.16, Python 3.11, and NVIDIA CUDA dependencies; those are not relicensed under the application's MIT license.

Granite GGUF SHA-256: `97c417dcc0534b0737c74016fb2af083cb17c3b51eaac621192d23961b7024eb`. Model revision: `ec48475f0c811d812fbfb61975717a9c36eeb652`. Release file hashes are in `SHA256SUMS.txt`.
