# Graph Report - dragon_anim_studio  (2026-11-24)

## Corpus Check
- cluster-only mode — file stats not available

## Summary
- 22 nodes · 32 edges · 4 communities
- Extraction: 97% EXTRACTED · 3% INFERRED · 0% AMBIGUOUS · INFERRED: 1 edges (avg confidence: 0.85)
- Token cost: 0 input · 0 output

## Community Hubs (Navigation)
- Community 0
- Community 1
- Community 2
- Community 3

## God Nodes (most connected - your core abstractions)
1. `updateDragonProceduralAnimation()` - 6 edges
2. `init()` - 5 edges
3. `animate()` - 3 edges
4. `applyPosePreset()` - 3 edges
5. `loadDragonModel()` - 3 edges
6. `csFloat()` - 2 edges
7. `generateUnityCSharpSnippet()` - 2 edges
8. `computeTrottingLegAngles()` - 2 edges
9. `evaluatePropellerDashStroke()` - 2 edges
10. `rotateBone()` - 2 edges

## Surprising Connections (you probably didn't know these)
- `animate()` --calls--> `updateDragonProceduralAnimation()`  [EXTRACTED]
  studio.js → studio.js  _Bridges community 1 → community 2_
- `init()` --calls--> `loadDragonModel()`  [EXTRACTED]
  studio.js → studio.js  _Bridges community 2 → community 3_

## Import Cycles
- None detected.

## Communities (4 total, 0 thin omitted)

### Community 0 - "Community 0"
Cohesion: 0.22
Nodes (8): animClock, bones, csFloat(), generateUnityCSharpSnippet(), initialPositions, initialRotations, POSE_PRESETS, tuning

### Community 1 - "Community 1"
Cohesion: 0.50
Nodes (5): computeTrottingLegAngles(), evaluatePropellerDashStroke(), updateDragonProceduralAnimation(), rotateBone(), setWingFingers()

### Community 2 - "Community 2"
Cohesion: 0.50
Nodes (4): animate(), init(), onWindowResize(), setupLighting()

### Community 3 - "Community 3"
Cohesion: 0.67
Nodes (3): applyPosePreset(), loadDragonModel(), syncSlider()

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `updateDragonProceduralAnimation()` connect `Community 1` to `Community 0`, `Community 2`?**
  _High betweenness centrality (0.188) - this node is a cross-community bridge._
- **Why does `init()` connect `Community 2` to `Community 0`, `Community 3`?**
  _High betweenness centrality (0.014) - this node is a cross-community bridge._
- **Why does `animate()` connect `Community 2` to `Community 0`, `Community 1`?**
  _High betweenness centrality (0.007) - this node is a cross-community bridge._