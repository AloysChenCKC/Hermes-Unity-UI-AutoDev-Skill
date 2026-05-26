# Feature Workflows

This document expands each major `Hermes Unity UI AutoDev Skill` capability into a workflow. Each feature has an input contract, execution flow, output contract, governance gate, and failure route.

## 1. Document-To-Blueprint

### Purpose

Convert a non-standard planning document into a structured screen blueprint before any Unity mutation occurs.

### Inputs

- feature document
- target screen id
- known game system or module name
- optional UI reference image
- optional component catalog

### Workflow

```text
planning document
-> extract screens
-> extract functions
-> extract states and transitions
-> extract UI actions and events
-> extract data requirements
-> extract acceptance criteria
-> emit candidate blueprint
-> validate coverage
-> admit blueprint or emit ambiguity ledger
```

### Outputs

- `candidate-screen-blueprint.json`
- `functional-graph.json`
- `event-binding-contract.json`
- `state-transition-contract.json`
- `data-binding-contract.json`
- `interaction-test-contract.json`
- `ambiguity-ledger.json`
- `admitted-screen-blueprint.json` when accepted

### Governance Gate

`blueprint admission gate`

### Failure Route

If the document does not define enough functional truth, the skill emits `blocked_by_blueprint_gap` and allows only visual-only UI reconstruction.

## 2. Asset Name Governance

### Purpose

Normalize loose UI asset names before UI reconstruction and semantic binding.

### Inputs

- component PNG folder
- naming policy
- optional component catalog

### Workflow

```text
scan asset folder
-> parse prefixes
-> tokenize file names
-> infer asset category
-> infer component type hint
-> infer visual role hint
-> infer state hint
-> detect duplicate or weak names
-> propose canonical asset ids
-> emit asset manifest
-> emit rename plan if needed
```

### Prefix Examples

| Prefix | Component Hint |
| --- | --- |
| `icon_` | Image or icon button child |
| `button_` | Button |
| `panel_` | Panel or container |
| `bg_` | Background image |
| `tab_` | Tab button |
| `badge_` | Badge or label marker |
| `frame_` | Border or framed panel |
| `input_` | Input field |
| `text_` | Text style or baked text candidate |

### Outputs

- `asset-manifest.json`
- `asset-normalization-ledger.json`
- `asset-ambiguity-ledger.json`
- `asset-rename-plan.json` when renaming is requested

### Governance Gate

`asset manifest gate`

### Failure Route

Weak names can continue as visual assets, but they cannot be auto-bound to functional components without stronger blueprint, OCR, or positional evidence.

## 3. UI Perception

### Purpose

Read the full-screen UI reference PNG and identify candidate UI nodes, positions, hierarchy, text, and visual component types.

### Inputs

- full-screen UI reference PNG
- screen dimensions
- target platform or resolution profile
- optional asset manifest

### Workflow

```text
load reference PNG
-> detect large containers and panels
-> detect button-like regions
-> detect icon-like regions
-> OCR text regions
-> infer grouping and hierarchy
-> infer sibling order
-> score component type candidates
-> emit perception report
```

### Outputs

- `perception-report.json`
- `visual-node-candidates.json`
- `ocr-report.json`
- `layout-candidate-report.json`
- `layer-candidate-report.json`

### Governance Gate

`perception confidence gate`

### Failure Route

Low-confidence nodes are routed to the ambiguity ledger. They may be emitted as visual placeholders but not admitted as functional UI elements.

## 4. Semantic Binding

### Purpose

Determine what blueprint function each UI component carries.

### Inputs

- admitted screen blueprint
- asset manifest
- perception report
- OCR report
- component catalog

### Workflow

```text
load blueprint function graph
-> load visual node candidates
-> load asset candidates
-> score filename token overlap
-> score OCR text match
-> score component type match
-> score expected position
-> score nearby context
-> score screen scope
-> resolve function binding
-> admit, block, or route ambiguity
```

### Scoring Tiers

| Score | Route |
| --- | --- |
| `>= 0.85` | auto admit |
| `0.65 - 0.85` | ambiguity ledger / confirmation needed |
| `< 0.65` | block functional binding |

### Outputs

- `semantic-binding-report.json`
- `function-binding-map.json`
- `ambiguity-ledger.json`

### Governance Gate

`semantic binding gate`

### Failure Route

The skill may generate a visual-only element but must not generate functional code for a blocked binding.

## 5. Canonical UI IR

### Purpose

Create the governed intermediate representation that Unity generation consumes.

### Inputs

- admitted screen blueprint
- asset manifest
- perception report
- semantic binding report
- layout policy
- responsive profile

### Workflow

```text
join visual node identity
-> bind asset truth
-> bind blueprint function truth
-> compute RectTransform model
-> assign hierarchy and sibling order
-> assign component types
-> assign state variants
-> assign event/data bindings
-> emit canonical UI IR
-> validate schema and identity consistency
```

### Outputs

- `canonical-ui-ir.json`
- `ui-hierarchy-manifest.json`
- `ui-layout-manifest.json`
- `ui-component-registry.json`

### Governance Gate

`UI IR schema gate`

### Failure Route

Identity conflicts, missing function bindings, or invalid layout fields return to semantic binding or perception repair.

## 6. Prefab Patch Planning

### Purpose

Plan Unity Prefab mutations before writing files.

### Inputs

- Canonical UI IR
- current Unity Prefab state if it exists
- manual override ledger
- Unity component mapping policy

### Workflow

```text
load current prefab state
-> compare against Canonical UI IR
-> classify create/update/delete operations
-> detect manual override conflicts
-> attach rollback intent
-> emit patch plan
-> validate patch plan
```

### Outputs

- `prefab-patch-plan.json`
- `manual-override-conflict-report.json`
- `rollback-plan.json`

### Governance Gate

`prefab patch plan gate`

### Failure Route

Manual override conflicts block mutation until resolved or explicitly waived.

## 7. Unity Prefab Build

### Purpose

Generate or patch Unity UI Prefabs through Unity-owned editor automation.

### Inputs

- prefab patch plan
- Canonical UI IR
- Unity project path
- sprite import policy
- TextMeshPro policy

### Workflow

```text
invoke Unity Editor script
-> import or resolve sprites
-> create or patch Canvas root
-> create or patch UI nodes
-> set RectTransform anchors/pivots/positions
-> assign Images, Buttons, TMP_Text, ScrollRect, Mask, LayoutGroup
-> bind event stubs
-> save Prefab
-> emit patch receipt
```

### Outputs

- generated or patched `.prefab`
- `prefab-patch-receipt.json`
- `generated-code-manifest.json` when stubs are created

### Governance Gate

`Unity adapter gate`

### Failure Route

Unity editor failures route to repair. Direct uncontrolled YAML mutation is not allowed unless a dedicated governed YAML adapter is admitted.

## 8. Visual Verification

### Purpose

Check whether the generated Unity UI visually matches the full-screen reference PNG.

### Inputs

- generated Prefab or Scene
- reference PNG
- visual regression profile
- screenshot capture policy

### Workflow

```text
open generated UI in Unity
-> capture screenshot
-> export RectTransform probe
-> compare screenshot with reference PNG
-> detect offscreen elements
-> detect overlap violations
-> detect missing or wrong assets
-> emit visual diff report
-> pass or route repair
```

### Outputs

- `screenshot-generated.png`
- `rect-transform-probe.json`
- `visual-diff-report.json`

### Governance Gate

`visual diff gate`

### Failure Route

Visual failures return to UI IR layout repair, asset mapping repair, or Prefab patch repair depending on the evidence.

## 9. Feature AutoDev

### Purpose

Generate feature binding code after visual and semantic truth are admitted.

### Inputs

- admitted screen blueprint
- Canonical UI IR
- semantic binding report
- project coding policy
- target architecture pattern

### Workflow

```text
read functional graph
-> generate Controller/ViewModel stubs
-> generate event handlers
-> generate state transition skeletons
-> generate data binding placeholders
-> generate interaction tests
-> bind UI events
-> run compile and tests
```

### Outputs

- generated C# files
- `generated-code-manifest.json`
- interaction tests
- compile/test logs

### Governance Gate

`Unity compile gate` and `interaction test gate`

### Failure Route

Compile or test failures route to governed repair. Missing blueprint truth blocks functional code admission.

## 10. Audit And Delta Sync

### Purpose

Preserve evidence, protect manual changes, and make every mutation reviewable.

### Inputs

- all run artifacts
- prior evidence pack
- current Unity state
- manual override ledger

### Workflow

```text
hash inputs
-> compare prior run
-> detect deltas
-> detect manual overrides
-> attach patch receipts
-> attach validation evidence
-> classify waivers
-> emit admission report
```

### Outputs

- `input-hashes.json`
- `manual-override-ledger.json`
- `delta-sync-report.json`
- `repair-report.json`
- `admission-report.md`

### Governance Gate

`admission gate`

### Failure Route

Dirty manual override conflicts, missing evidence, or failed validation prevents admission.

## 11. Repair Loop

### Purpose

Route failures to the smallest safe repair path.

### Workflow

```text
validation failure
-> classify failure source
-> select repair target
-> generate bounded repair packet
-> apply through Hermes executor
-> rerun required gates
-> update evidence
```

### Failure Classes

| Failure | Repair Route |
| --- | --- |
| Missing blueprint function | Document-to-Blueprint |
| Weak asset naming | Asset Name Governance |
| Bad node detection | UI Perception |
| Wrong function binding | Semantic Binding |
| Bad layout | Canonical UI IR |
| Prefab mismatch | Prefab Patch Planning or Unity Prefab Build |
| Visual diff failure | Visual Verification repair |
| Compile failure | Feature AutoDev repair |
| Manual override conflict | Audit and Delta Sync |

## 12. Admission Flow

Final screen admission requires:

```text
blueprint admitted
asset manifest admitted
semantic binding admitted
UI IR valid
Prefab patch receipt present
visual diff pass or governed waiver
Unity compile pass
interaction tests pass or governed waiver
manual override state clean or reconciled
admission report emitted
```

Forbidden claims:

- production release readiness
- full game UI completion
- gameplay feature completion without tests
- complete Unity project closure
- stable v1.2 live unless separately proven
