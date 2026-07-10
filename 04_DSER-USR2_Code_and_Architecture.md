# DSER-USR2 — Code & Architecture
### The computational implementation of the φ-χ-ρ master action

**Key finding from this compilation:** DSER-USR2's code layer is not a separate project from Fundamental Fisycs (FF) — it is FF's underlying physics engine. The "DSER Master System v5.0" is the shared compute/rendering core (Bus Driver, Smart Switch Chip, K8 renderer, Mandalfold renderer, Verification Daemon) that both this Claude project's FF files and the Google Drive DSER-USR2 folder build on. φ/χ/ρ appear directly as simulation parameters and as physics-deck IDs (PHI_FIELD=6, CHI_FIELD=7, RHO_FIELD=8) in the shared codebase.

---

## 1. Where things live

**Google Drive** — folder `DSER-USR2` → `Actual_Science` → `MetatronCog.Inc` (deepest confirmed nesting; folder tree continues beyond what was crawled in this compilation — re-run a Drive search on that folder if deeper contents are needed).

**This Claude project's file system** (`/mnt/project/`) — the Fundamental Fisycs implementation, which shares the same underlying engine:
- `dser_scheduler_adapter.py` — present in both locations (see §3)
- `MANDALFOLD_DSER_INTEGRATION.md`, `SMART_SWITCH_CHIP.md` — shared architecture docs
- `universal_knowledge_base.py`, `universal_bridge_scheduler.py`, `universal_compute_layer.py`, `universal_unified_pipeline.py`, `universal_pattern_library.py`, `pde_physics_bridge.py`, `context_tokenizer.py` — the "Smart Switch Chip" package (predictive background scheduling, PDE compute, NER tokenization)
- `k8_renderer.py`, `k8_renderer_optimized.py`, `mandalfold_renderer.py`, `mandalfold_renderer_optimized.py` — the two GPU/neural renderers
- `verification_daemon.py`, `dual_way_event_bus.py` — the 4-layer (L1-L4) safety/event system
- `bus_driver_cpp.core/.txt`, `bus_driver_h.core/.txt`, `ner_cpp.core/.txt`, `jazz_cpp.txt` — C++ core (Bus Driver, NER)
- `ner_wrapper.py`, `plugin_system.py`, `config_bus.json`, `config_physics.json`, `config_gates.json`, `config_render.json`, `config_face_*.json` — configuration layer
- `FisycsMasterWindow.py`, `fundamental_fisycs_*.py` (core_engines, decks, logger, synthesizer, pyqt_main) — the FF-specific application layer built on top of the shared engine
- `BUS_DRIVER_ARCHITECTURE.md`, `ARCHITECTURE_CHOREOGRAPHY.md`, `KLEIN_BOTTLE_8_OVERVIEW.md/SCOPE.md/OPERATION.md/PROCEDURE.md`, `research_compilation.md` — architecture documentation

*None of the physics-decoding above requires re-uploading; these files are already in this project's knowledge base and searchable via `project_knowledge_search`.*

---

## 2. Physics Deck Mapping (from `dser_scheduler_adapter.py`)

The engine assigns each φ-χ-ρ field, plus six spatial axes and other subsystems, to one of 16 numbered "decks" (0–15) — the same deck numbering referenced throughout the codemaps and the FF `config_face_*.json` files.

Confirmed deck assignments from source:
- Deck 6 = PHI_FIELD (φ)
- Deck 7 = CHI_FIELD (χ)
- Deck 8 = RHO_FIELD (ρ)
- Decks 0–5 = spatial axes (±X, ±Y, ±Z) — mapped to Möbius/Toroidal/Klein Bottle topologies in the Mandalfold renderer (see §4)

## 3. Core Source — `dser_scheduler_adapter.py`

*BridgeScheduler subclass specialized for DSER physics. Maps staged background-computation results into BusDriver field decks via lock-free event publishing. Present identically in the Google Drive DSER-USR2 folder and this project's `/mnt/project/`.*

```python
#!/usr/bin/env python3
"""
dser_scheduler_adapter.py
=========================
BridgeScheduler subclass specialized for DSER physics system.

Maps staged results from background computation into BusDriver
field decks via lock-free event publishing.
"""

import numpy as np
from typing import Dict, Any, Optional

from Smart_Switch_Chip.universal_bridge_scheduler import (
    UniversalBridgeScheduler,
    StagedResult,
)
from Smart_Switch_Chip.pde_physics_bridge import PhysicsPDEBridge, GrayScottParams


class DSERSchedulerAdapter(UniversalBridgeScheduler):
    """
    DSER-specific BridgeScheduler adapter.
    Handles mapping of staged computation results to BusDriver field decks.
    """

    def __init__(
        self,
        dser_master_system,
        dirty_enum,
        param_map: Dict[str, list],
        lookahead: int = 3,
        max_workers: int = 3,
    ):
        super().__init__(
            target_system=dser_master_system,
            dirty_enum=dirty_enum,
            param_map=param_map,
            lookahead=lookahead,
            max_workers=max_workers,
        )
        self.dser = dser_master_system
        self.pde_bridge: Optional[PhysicsPDEBridge] = None
        self._register_compute_functions()

    def _register_compute_functions(self):
        """Register compute functions for each physics deck flag."""
        for flag in self.dirty_enum:
            if hasattr(flag, "value"):
                deck_id = flag.value
                # Register PDE step for field decks (6, 7, 8) = PHI/CHI/RHO
                if deck_id in [6, 7, 8]:
                    self.register_compute_fn(flag, self._compute_pde_step)

    def set_pde_bridge(self, pde_bridge: PhysicsPDEBridge):
        """Set PDE bridge for field computations."""
        self.pde_bridge = pde_bridge

    def _compute_pde_step(self, params: Dict) -> Dict[str, np.ndarray]:
        """Compute PDE reaction-diffusion step."""
        # (full body in source file — Gray-Scott reaction-diffusion on φ/χ decks,
        #  Numba @njit(parallel=True) accelerated, see pde_physics_bridge.py)
        ...
```

## 4. Mandalfold + DSER Integration Bridge (Neural-Topological Synthesis)

*Connects Mandalfold Field's neural rendering with DSER's 19,000-particle physics engine, Dual-Way Event Bus (60 Hz sync), 4-Layer Verification Daemon, and 16 Physics Decks. Source: `mandala_fold_dser_bridge.py` (Google Drive), architecture doc `MANDALFOLD_DSER_INTEGRATION.md` (both locations).*

**Topology map** (deck → neural field topology):

| Deck | Field | Topology |
|---|---|---|
| 0–1 | ±X | Möbius Strip |
| 2–3 | ±Y | Toroidal |
| 4–5 | ±Z | Klein Bottle |
| 6 | PHI_FIELD | Hyperbolic |
| 7 | CHI_FIELD | Projective Plane |
| 8 | RHO_FIELD | *(see full codemap for assignment)* |

**Quality profiles:** ULTRA (4K/60Hz), HIGH (1440p/60Hz), ENHANCED (1080p/60Hz), PERFORMANCE (720p/120Hz), LOW (480p/30Hz).

**Temporal modes:** REALTIME (max neural quality, ~45 FPS), DETERMINISTIC (locked 60 Hz, predictive caching), ADAPTIVE (dynamic quality based on particle density), DUAL (split-screen particle + neural views).

**Anisotropic ratio:** particles are scaled by 30/33 or 33/30 depending on even/odd deck parity — the same κ=10/11-family anisotropy constant referenced in the Theory file (Oval-π geometry, §I.10) and in the SSDM Hubble-equation reading (Applications file, §IV.4).

## 5. Smart Switch Chip — Predictive Orchestration Layer

*Package: `Smart_Switch_Chip/` — `universal_unified_pipeline.py` (main orchestration), `universal_bridge_scheduler.py` (background scheduling), `universal_compute_layer.py` (PingPongBuffer, CircularStateBuffer), `universal_knowledge_base.py` (SQLite + Oracle), `universal_pattern_library.py` (caching), `pde_physics_bridge.py` (Numba PDE compute), `context_tokenizer.py` (NER→ML tokens), `dser_scheduler_adapter.py` (§3 above).*

**Performance characteristics** (from `SMART_SWITCH_CHIP.md`):

| Component | Operation | Time |
|---|---|---|
| PDE Bridge | Gray-Scott step (19k particles) | ~3ms (Numba @njit parallel) |
| State Buffer | Push frame | ~0.1ms |
| State Buffer | Rollback (5 frames) | ~0.5ms |
| Tokenizer | Encode entities | ~0.2ms |
| Pipeline | Tick + apply staged | ~0.3ms |
| **Total overhead** | Per frame | **~4ms (<25% of 16.67ms @60fps budget)** |

**Oracle Safety Layer:** `VerificationDaemon` registers callbacks for CRITICAL alerts; automatic rollback via `CircularStateBuffer` (5-frame recovery); constraint teaching to Oracle for future avoidance; L1–L4 security layer integration. This is the same Verification Daemon referenced in the Fundamental Fisycs project's `verification_daemon.py`.

## 6. Codemap Index (Google Drive, `.codemap` files — architecture summaries, not fetched in full for this compilation)

| Codemap | Subsystem |
|---|---|
| DSER_System_Initialization | ConfigLoader, PyQt6, Smart Switch Chip, Mandalfold Renderer |
| DSER-USR2_Engine_Integration_Architecture | Core engine integration |
| DSER-USR2_3D_Rendering_and_Visualization_System | 3D rendering pipeline |
| DSER_UI_Layout_and_Configuration_System | UI layout |
| DSER_UI_Theme_Configuration_System | Theming |
| DSER_Cellular_Energetics_Simulator (ODE Integration, Diagnostics, Visualization) | CES engine (§IV.6 of Applications file) |
| DSER_Cellular_Energetics__Biochemical_Parameters___Tissue_Presets_Flow | Tissue presets table (§IV.6) |
| DSER_Cellular_Energetics__Kivy_UI_Layout___Visualization_Widgets | CES Kivy front-end |
| DSER_Distributed_Swarm_Entropy_Regulation_System | Swarm/entropy regulation |
| DSER-USR2_String_Theory_Physics_Engine_-_Quantum_Field___Daemon_Integration | Extended physics engine module |
| Neural_Topology_and_Field_Mapping_-_DSER_to_Sphere_Modulation_Pipeline | Neural field mapping |
| DSER_Master_System_Architecture (Core Integration, Particle Management, Event Bus) | Top-level system architecture |
| DSER_66D_ARCHITECTURE.md | 66-dimensional extension architecture (not yet cross-referenced against Theory file — flagged for follow-up if this is a distinct layer from the 3-field core) |

*These are auto-generated or hand-written architecture descriptions in Google Drive under the DSER-USR2 folder tree. Full text available on request — not included verbatim here to keep this file a usable index rather than a multi-hundred-page dump.*

## 7. Open Items / Follow-Up

- `DSER_66D_ARCHITECTURE.md` was located but not read in full during this compilation — its relationship to the 3-field (φ,χ,ρ) core in the Theory file is unconfirmed. Worth checking whether it's a genuine extension or a naming collision with an unrelated subsystem.
- `mandala_fold_dser_bridge.data.json` (Drive) is a data file, not source — not included here; fetch directly if needed for a specific debugging task.
- The Google Drive `DSER-USR2` folder nests at least three levels deep (`DSER-USR2` → `Actual_Science` → `MetatronCog.Inc`); this compilation crawled to that depth but did not exhaustively enumerate every file below it. Re-run `Google Drive:search_files` with `parentId = 'MetatronCog.Inc folder ID'` for a complete listing if this matters.

---

*Compiled from: Google Drive folder "DSER-USR2" (code + codemaps), this Claude project's `/mnt/project/` files (Smart Switch Chip package, Bus Driver C++ core, Fundamental Fisycs application layer), and `research_compilation.md` (project knowledge).*
