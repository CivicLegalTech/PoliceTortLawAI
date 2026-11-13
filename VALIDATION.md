# Release validation

The release was exercised on one Windows GTX 1650 4 GB laptop. Local Granite GPU inference, GUI loading, document import and the microphone path were checked during setup.

Public source test run: 9 passed, 1 skipped (optional NumPy embeddings dependency absent), 1 failed (the mock localhost health request did not return ready in the restricted publishing environment). The latter is unresolved; do not read this as a clean full-suite pass. The standalone packaged server had already returned a real model response.

Windows temporary SQLite cleanup in tests now invokes garbage collection before removing the temporary directory. Optional embedding tests skip when NumPy is unavailable. These are test-only adjustments; the runtime application is unchanged.

No broad hardware testing, legal-output certification, or independent security audit has been performed.
