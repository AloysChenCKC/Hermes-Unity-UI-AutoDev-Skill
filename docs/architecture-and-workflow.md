# Hermes Unity UI AutoDev Skill Architecture And Workflow

## 1. Document Role

This document defines the intended architecture, workflow, execution boundaries, governance kernel, and implementation approach for `Hermes Unity UI AutoDev Skill`.

It is a project architecture document, not an implementation completion claim.

Current state:

```text
architecture-design / pre-live-implementation / no Unity runtime closure claimed
```

The skill is expected to be generated or derived only after `aegisfabric pipeline v1.2` is repaired into a stable Hermes mother skill, synced back to blueprint authority, and used to generate a fresh `v1.2 live` executor.

## 2. Strategic Goal

The goal is to build a governed skill that can turn game UI design assets and feature intent into real Unity UI implementation artifacts.

Input:

- feature document or formal blueprint
- full-screen UI reference PNG
- UI component PNG assets
- loose or partially standardized asset naming
- Unity target project
- target screen and feature scope

Output:

- admitted screen blueprint
- normalized asset manifest
- canonical UI IR
- prefab patch plan
- generated or patched Unity Prefab
- generated feature binding code
- validation logs
- screenshot and layout evidence
- audit ledger
- admission verdict

## 3. Non-Negotiable Execution Boundary

The LLM is not the executor.

```text
LLM role:
- understand the user request
- organize task content
- prepare candidate intent packets
- explain evidence and ambiguity
- dispatch work into Hermes

Hermes role:
- own governed execution
- write files
- rename or normalize assets
- generate final manifests
- build Prefabs
- generate source files
- repair source and Prefab state
- run Unity validation
- emit evidence
- decide admission
```

If the LLM directly mutates Unity files, writes final Prefabs, repairs code, or silently renames assets outside Hermes, the governance model is bypassed and the skill becomes cosmetic. This architecture explicitly rejects that mode.

## 4. Relationship To `aegisfabric pipeline v1.2`

The intended parent-child hierarchy is:

```text
current aegisfabric pipeline v1.2
  -> repaired generic Hermes mother skill
  -> blueprint-synced v1.2 authority
  -> regenerated v1.2 live executor
  -> generated child skill: Hermes Unity UI AutoDev Skill
```

### 4.1 Current v1.2 Must Not Be Overclaimed

The current `v1.2` was originally developed by an LLM reading a blueprint. That means many flows may be missing, incomplete, or non-standard.

Therefore, the correct sequence is:

1. repair current `v1.2` source and execution behavior
2. synchronize those repairs back into the `v1.2` blueprint
3. use repaired current `v1.2` to generate a fresh `v1.2 live`
4. verify `v1.2 live` as a governed executor
5. derive this Unity child skill from `v1.2 live`

### 4.2 Mother Skill And Child Skill Separation

`v1.2 live` should stay generic. It should not be converted directly into a Unity-specific skill.

It should provide:

- intent-to-blueprint compilation
- blueprint-to-workflow compilation
- workflow-to-action packet compilation
- governed filesystem mutation
- action-to-evidence capture
- proof, replay, receipt, and admission surfaces
- repair, rollback, and hard-fail routing

`Hermes Unity UI AutoDev Skill` should provide Unity-specific domain capability:

- UI image perception
- asset name canonicalization
- component matching
- semantic binding to feature blueprint
- Unity Prefab generation
- Unity Editor script execution
- screenshot and RectTransform validation
- Unity compile/test/interaction gates

## 5. Functional Architecture

```text
Hermes Unity UI AutoDev Skill
|- skill shell
|- governance kernel
|- document-to-blueprint compiler
|- asset name canonicalizer
|- UI perception engine
|- semantic binding resolver
|- canonical UI IR compiler
|- prefab patch planner
|- Unity adapter
|- feature AutoDev generator
|- validation runner
|- audit and delta-sync engine
`- evidence pack emitter
```

For a feature-by-feature execution view, see [feature-workflows.md](feature-workflows.md). That document expands each capability into inputs, execution steps, outputs, governance gates, and failure routing.

## 6. Module Contracts

### 6.1 Skill Shell

Responsibility:

- expose the skill entrypoint
- require task packets instead of free-form direct mutation
- route all execution through the governance kernel
- load domain policies and schema versions

Inputs:

- user task packet
- target Unity project path
- target screen id
- reference image path
- component asset folder
- feature document or blueprint

Outputs:

- run id
- execution plan
- evidence root
- final admission verdict

### 6.2 Governance Kernel

Responsibility:

- own all admission decisions
- enforce write boundaries
- enforce LLM/Hermes execution separation
- classify failures as repair, rollback, rebuild, or hard fail
- preserve audit trails

Required gates:

| Gate | Purpose |
| --- | --- |
| intake gate | verifies task packet completeness |
| blueprint admission gate | verifies feature document was compiled into usable blueprint |
| asset manifest gate | verifies asset naming and metadata are canonicalized |
| perception confidence gate | blocks low-confidence UI recognition |
| semantic binding gate | verifies UI nodes carry declared functions |
| prefab patch plan gate | blocks uncontrolled Prefab mutation |
| visual diff gate | compares generated Unity screenshot with reference PNG |
| Unity compile gate | requires successful C# compile |
| interaction test gate | verifies declared interactions |
| delta sync gate | protects manual changes |
| admission gate | declares pass, blocked, waived, or rollback |

### 6.3 Document-To-Blueprint Compiler

Purpose:

Convert planning documents into candidate blueprints before any UI generation or source mutation begins.

Input may be non-standard:

- planning document
- gameplay UI note
- product requirement
- rough feature description
- screen flow description

Output:

- candidate screen blueprint
- functional graph
- event binding contract
- state transition contract
- data binding contract
- interaction test contract
- ambiguity ledger

The candidate blueprint must pass governance admission before it becomes the functional truth.

Example functional node:

```json
{
  "screen_id": "reward_screen",
  "function_id": "reward.claim",
  "component_type": "Button",
  "text_candidates": ["Claim", "领取", "领取奖励"],
  "asset_keywords": ["reward", "claim", "button"],
  "expected_position": "bottom_center",
  "event": "onClick",
  "handler": "RewardController.OnClaim",
  "state_transitions": [
    ["claimable", "claimed"]
  ],
  "expected_result": [
    "show_reward_result_panel",
    "increase_coin_balance",
    "disable_claim_button"
  ]
}
```

### 6.4 Asset Name Canonicalizer

Purpose:

Normalize imperfect UI asset naming before UI reconstruction.

The user may provide names such as:

```text
icon_coin_gold.png
icon_close_white.png
button_green_long.png
button_reward_claim.png
panel_shop_bg.png
bg_reward_main.png
```

The canonicalizer converts them into a structured manifest:

```json
{
  "asset_id": "button.reward.claim",
  "source_file": "button_reward_claim.png",
  "raw_name": "button_reward_claim",
  "category": "button",
  "tokens": ["reward", "claim"],
  "component_hint": "Button",
  "visual_role_hint": "primary_action",
  "state_hint": "normal",
  "scale_policy": "nine_slice_or_fixed",
  "confidence": 0.82
}
```

Required behaviors:

- parse known prefixes: `icon_`, `button_`, `panel_`, `bg_`, `tab_`, `badge_`, `frame_`, `input_`, `text_`
- infer category and component hints
- detect duplicate or near-duplicate names
- detect generic names that are too weak for semantic binding
- generate rename suggestions
- never silently rename source assets without a governed patch plan

### 6.5 UI Perception Engine

Purpose:

Read the full-screen UI reference PNG as visual truth and produce visual node candidates.

Detection surfaces:

- bounding boxes
- OCR text
- icon-like regions
- button-like regions
- panels and containers
- list or scroll regions
- visual hierarchy
- sibling order
- layer candidates
- background and overlay regions

Output:

```json
{
  "visual_node_id": "detected.node.042",
  "bbox": [420, 860, 240, 72],
  "text": "Claim",
  "visual_type_candidates": [
    {"type": "Button", "score": 0.77},
    {"type": "Image", "score": 0.41}
  ],
  "nearby_tokens": ["reward", "coin"],
  "position_hint": "bottom_center",
  "perception_confidence": 0.81
}
```

### 6.6 Semantic Binding Resolver

Purpose:

Determine what function each UI component carries.

It must not rely on a single signal or pure LLM guessing. It scores multiple evidence sources:

| Signal | Example |
| --- | --- |
| asset prefix | `button_` suggests Button |
| filename tokens | `reward`, `claim`, `shop`, `close` |
| OCR text | `Claim`, `领取`, `Buy` |
| visual role | primary action, icon button, tab |
| position | top right close button, bottom center confirm button |
| nearby context | reward icon near claim button |
| screen scope | current screen is reward screen |
| blueprint function graph | expected `reward.claim` action |
| state assets | normal, pressed, disabled, selected |

Confidence routing:

```text
score >= 0.85: auto admit
0.65 <= score < 0.85: ambiguity ledger, requires confirmation or stronger blueprint evidence
score < 0.65: block function binding, allow visual-only placeholder if policy permits
```

Output:

```json
{
  "ui_node_id": "reward_screen.claim_button",
  "matched_function_id": "reward.claim",
  "matched_asset": "button.reward.claim",
  "component_type": "Button",
  "event": "onClick",
  "handler": "RewardController.OnClaim",
  "binding_confidence": 0.91,
  "admission": "auto_pass"
}
```

### 6.7 Canonical UI IR Compiler

Purpose:

Create the authority object consumed by Unity generation. Unity builders must not consume raw PNGs or raw filenames directly.

The Canonical UI IR joins:

- visual node identity
- blueprint functional identity
- component type
- asset reference
- RectTransform geometry
- hierarchy and sibling order
- style and state variants
- event binding
- data binding
- audit evidence

Example:

```json
{
  "ui_node_id": "reward_screen.claim_button",
  "component_type": "Button",
  "asset_ref": "button.reward.claim",
  "rect": {
    "x": 420,
    "y": 860,
    "w": 240,
    "h": 72
  },
  "unity": {
    "component": "UnityEngine.UI.Button",
    "text_component": "TextMeshProUGUI",
    "onClick": "RewardController.OnClaim"
  },
  "audit": {
    "source_image": "reward_screen.png",
    "source_asset": "button_reward_claim.png",
    "blueprint_function": "reward.claim",
    "confidence": 0.91
  }
}
```

### 6.8 Prefab Patch Planner

Purpose:

Plan Unity Prefab changes before writing them.

Patch plan categories:

- create Canvas root
- create UI node
- update RectTransform
- assign Sprite
- assign TextMeshPro text
- add or update Button
- add ScrollRect, Mask, Image, LayoutGroup
- bind handler stub
- preserve manual override
- delete obsolete generated node

The planner must emit rollback data before mutation.

### 6.9 Unity Adapter

Purpose:

Perform Unity mutations and validation through Unity-owned mechanisms.

Preferred implementation:

- Unity Editor C# scripts read Canonical UI IR
- Editor scripts create or patch Prefabs
- Editor scripts set RectTransform, anchors, pivots, sibling order, components, sprites, and TextMeshPro
- Editor scripts save Prefab assets
- batchmode commands run compile and tests
- probes export hierarchy, component, RectTransform, and screenshot evidence

Avoid direct uncontrolled `.prefab` YAML edits unless there is a dedicated governed YAML mutation adapter.

### 6.10 Feature AutoDev Generator

Purpose:

Generate or patch feature code only after visual and semantic binding has enough evidence.

Outputs:

- Controller stubs
- ViewModel stubs
- event handler methods
- state transition skeletons
- data binding placeholders
- interaction tests
- screen flow hooks

Example generated intent:

```text
RewardController.OnClaim
RewardViewModel.claimState
RewardScreenBinding.BindClaimButton
RewardScreenTests.ClickClaimButton_DisablesClaimButton_AndShowsResult
```

### 6.11 Validation Runner

Required validations:

- asset manifest validation
- blueprint admission validation
- UI IR schema validation
- prefab patch plan validation
- Unity compile validation
- prefab existence validation
- hierarchy and RectTransform probe validation
- screenshot visual diff validation
- interaction test validation
- Console error scan
- manual override audit validation

### 6.12 Audit And Delta-Sync Engine

Purpose:

Protect human edits and make every generated change reviewable.

Required ledgers:

- asset normalization ledger
- ambiguity ledger
- semantic binding ledger
- prefab patch ledger
- manual override ledger
- visual diff report
- interaction test report
- repair report
- admission report

## 7. End-To-End Workflow Modes

### 7.1 Standard Blueprint Mode

Used when a formal blueprint already exists.

```text
blueprint + reference PNG + assets + Unity project
-> blueprint validation
-> asset canonicalization
-> perception
-> semantic binding
-> UI IR
-> Prefab build
-> visual validation
-> feature code generation
-> runtime validation
-> admission
```

### 7.2 Feature Document Mode

Used when planning provides only a functional document.

```text
feature document
-> candidate blueprint
-> blueprint audit
-> admitted blueprint
-> standard blueprint mode
```

The generated blueprint is not authority until it passes admission.

### 7.3 Visual-Only Mode

Used when no valid functional blueprint exists.

Allowed output:

- visual-only Prefab
- placeholder components
- blocked function binding report

Forbidden output:

- completed functional code claim
- event binding admission
- gameplay feature completion claim

### 7.4 Iterative Screen Loop

The intended production loop is:

```text
screen 1 reference PNG + assets + doc
-> generate and validate screen 1
-> audit and admit
-> screen 2 reference PNG + assets + doc
-> generate and validate screen 2
-> audit and admit
-> repeat until all UI screens are covered
```

Shared components, style tokens, naming improvements, and binding patterns are accumulated across screens.

### 7.5 Modification And Audit Loop

When a screen changes:

```text
new reference PNG
-> delta detection
-> asset changes
-> blueprint changes
-> prefab patch plan
-> manual override conflict check
-> governed patch
-> visual and runtime revalidation
-> audit report
```

The system must not silently overwrite manual edits.

## 8. Evidence Pack Structure

Recommended evidence root:

```text
.hermes/evidence/<screen_id>/<run_id>/
|- task-packet.json
|- input-hashes.json
|- document-blueprint-compile-report.json
|- admitted-screen-blueprint.json
|- asset-manifest.json
|- asset-normalization-ledger.json
|- perception-report.json
|- semantic-binding-report.json
|- ambiguity-ledger.json
|- canonical-ui-ir.json
|- prefab-patch-plan.json
|- prefab-patch-receipt.json
|- rect-transform-probe.json
|- screenshot-reference.png
|- screenshot-generated.png
|- visual-diff-report.json
|- generated-code-manifest.json
|- unity-compile.log
|- editmode-tests.log
|- playmode-tests.log
|- console-error-scan.json
|- manual-override-ledger.json
|- repair-report.json
`- admission-report.md
```

## 9. Key Data Contracts

### 9.1 Task Packet

```json
{
  "task_type": "build_unity_ui_screen",
  "screen_id": "reward_screen",
  "reference_image": "Assets/UIReferences/reward_screen.png",
  "asset_folder": "Assets/UIRaw/reward",
  "feature_document": "Docs/reward_feature.md",
  "unity_project_root": "/path/to/unity/project",
  "requested_outputs": [
    "admitted_blueprint",
    "prefab",
    "feature_binding_code",
    "visual_validation",
    "runtime_validation",
    "audit_report"
  ]
}
```

### 9.2 Admission Report

```json
{
  "screen_id": "reward_screen",
  "run_id": "2026-05-26T09-00-00Z",
  "visual_state": "pass",
  "function_binding_state": "pass",
  "unity_compile_state": "pass",
  "interaction_test_state": "pass",
  "manual_override_state": "clean",
  "admission": "pass",
  "closure_claim": "screen_implementation_admitted",
  "production_release_claim": "forbidden"
}
```

## 10. Governance Verdicts

Allowed verdicts:

- `pass`
- `pass_with_waiver`
- `blocked`
- `repair_required`
- `rollback_required`
- `hard_fail`

Forbidden overclaims:

- production release readiness
- full game UI completion
- gameplay feature completion without tests
- complete Unity project closure
- v1.2 mother skill stability unless separately proven

## 11. Implementation Roadmap

### Phase 0: Repository Bootstrap

Deliver:

- project README
- architecture and workflow document
- initial schema placeholders
- example task packet

### Phase 1: v1.2 Mother Skill Readiness

Deliver in parent system:

- current `v1.2` repair inventory
- source repair closure
- blueprint sync closure
- fresh `v1.2 live` generation
- live executor replay evidence

### Phase 2: Child Skill Skeleton

Deliver:

- `SKILL.md`
- task packet schema
- governance policy schema
- evidence pack schema
- Canonical UI IR schema
- Prefab patch plan schema

### Phase 3: Asset And Blueprint Compiler

Deliver:

- asset name canonicalizer
- document-to-blueprint compiler
- blueprint admission validator
- semantic binding scoring model

### Phase 4: Unity Prefab Builder

Deliver:

- Unity Editor script bridge
- Prefab builder
- sprite importer rules
- TextMeshPro binding
- RectTransform probe
- screenshot capture

### Phase 5: Visual And Runtime Closed Loop

Deliver:

- visual diff gate
- Unity compile gate
- interaction tests
- Console error scan
- repair loop
- admission report

## 12. Success Criteria

The skill becomes useful when it can complete this loop for one screen:

```text
feature document + full-screen reference PNG + component assets
-> admitted blueprint
-> normalized asset manifest
-> generated Prefab
-> generated binding code
-> Unity screenshot matches reference within threshold
-> compile/tests pass
-> audit report emitted
```

It becomes production-worthy only after repeated screen loops prove:

- stable component reuse
- robust naming normalization
- predictable semantic binding
- safe manual override preservation
- reliable visual diff repair
- repeatable Unity validation
- no uncontrolled LLM file mutation

## 13. Final Architecture Statement

`Hermes Unity UI AutoDev Skill` is a governed child skill generated from a repaired and live-ready Hermes mother skill. It combines UI reconstruction and automatic feature development into one evidence-driven workflow.

Its central promise is:

```text
from feature intent and prepared UI visuals
to verified Unity Prefab and functional code
through governed execution, validation, audit, and repair
```

The skill is only valid if Hermes remains the execution authority and the LLM remains the task organizer.
