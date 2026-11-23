# Graph Report - code-graph  (2026-11-24)

## Corpus Check
- cluster-only mode — file stats not available

## Summary
- 1334 nodes · 3393 edges · 56 communities (42 shown, 14 thin omitted)
- Extraction: 90% EXTRACTED · 10% INFERRED · 0% AMBIGUOUS · INFERRED: 342 edges (avg confidence: 0.86)
- Token cost: 0 input · 0 output

## Graph Freshness
- Built from commit: `b835ead7`
- Run `git rev-parse HEAD` and compare to check if the graph is stale.
- Run `graphify update .` after code changes (no API cost).

## Community Hubs (Navigation)
- Community 0
- Community 1
- Community 2
- Community 3
- Community 4
- Community 5
- Community 6
- Community 7
- Community 8
- Community 9
- Community 10
- Community 11
- Community 12
- Community 13
- Community 14
- Community 15
- Community 16
- Community 17
- Community 18
- Community 19
- Community 20
- Community 21
- Community 22
- Community 23
- Community 24
- Community 26
- Community 29
- Community 32
- Community 33
- Community 34
- Community 36
- Community 37
- Community 39
- Community 41
- Community 42
- Community 43
- Community 44
- Community 45
- Community 46
- Community 47
- Community 48
- Community 49
- Community 50
- Community 51
- Community 52
- Community 53
- Community 54
- Community 55

## God Nodes (most connected - your core abstractions)
1. `UDPPoseReceiver` - 99 edges
2. `DragonFlightController` - 88 edges
3. `VillageZoneController` - 73 edges
4. `InGameFlightHUD` - 63 edges
5. `IPoseProvider` - 63 edges
6. `CalibrationPanelUI` - 53 edges
7. `PythonProcessManager` - 45 edges
8. `BiometricNormalizer` - 44 edges
9. `main()` - 43 edges
10. `GestureStateMachine` - 40 edges

## Surprising Connections (you probably didn't know these)
- `IsCameraCalibrated` --references--> `UDPPoseReceiver`  [EXTRACTED]
  UI/DragonGameSettings.cs → CV_Receiver/UDPPoseReceiver.cs
- `VillageZoneController` --references--> `DragonFlightController`  [EXTRACTED]
  Gameplay/VillageZoneController.cs → Controllers/DragonFlightController.cs
- `VillageZoneController` --references--> `DragonVfxController`  [EXTRACTED]
  Gameplay/VillageZoneController.cs → Controllers/DragonVfxController.cs
- `VillageZoneController` --references--> `IPoseProvider`  [EXTRACTED]
  Gameplay/VillageZoneController.cs → CV_Receiver/IPoseProvider.cs
- `VillageZoneController` --references--> `FinishModalUI`  [EXTRACTED]
  Gameplay/VillageZoneController.cs → UI/FinishModalUI.cs

## Import Cycles
- None detected.

## Communities (56 total, 14 thin omitted)

### Community 0 - "Community 0"
Cohesion: 0.09
Nodes (17): VillageZoneController, ActiveStageIndex, ActiveVillageCount, ClearedVillageCount, IsAnyNarrativeActive, IsFreePlayMode, IsPendingVictory, IsStageFinished (+9 more)

### Community 1 - "Community 1"
Cohesion: 0.14
Nodes (5): CalibrationPanelUI, ActivePoseIndex, CurrentRecentPoseIndices, InGamePauseMode, IsPoseGuideOpen

### Community 2 - "Community 2"
Cohesion: 0.11
Nodes (7): InGameFlightHUD, FinishModal, IsNarrativeIsolated, IsVillageChallengeMode, PauseModal, QuestNarrative, VillageChallenge

### Community 3 - "Community 3"
Cohesion: 0.07
Nodes (24): DragonWingAR.Gameplay, DragonWingAR.Controllers, DragonWingAR.UI, DragonWingAR.CV, GameState, CountdownStart, LevelInit, Paused (+16 more)

### Community 4 - "Community 4"
Cohesion: 0.04
Nodes (49): UDPPoseReceiver, ActiveStateId, BodyPitch, CalibrationProgress, CalibrationStateId, ChestElevation, DashPower, FlapFrequency (+41 more)

### Community 5 - "Community 5"
Cohesion: 0.04
Nodes (47): IPoseProvider, ActiveStateId, BodyPitch, CalibrationProgress, CalibrationStateId, ChestElevation, DashPower, FlapFrequency (+39 more)

### Community 6 - "Community 6"
Cohesion: 0.06
Nodes (18): BiomeAtmosphereController, ActiveZone, ActiveZoneName, AtmosphereProfile, BiomeZoneState, NeutralOcean, PyrewoodCrisis, PyrewoodRestored (+10 more)

### Community 7 - "Community 7"
Cohesion: 0.26
Nodes (3): PoseGuideOverlayUI, HighlightedPoseIndex, IsOpen

### Community 8 - "Community 8"
Cohesion: 0.05
Nodes (38): DragonFlightController, ActiveCommand, ActiveFlightStateName, CanFlyUpClimb, ControlsLocked, CurrentAltitude, currentAltitude, CurrentCameraOffsetZ (+30 more)

### Community 9 - "Community 9"
Cohesion: 0.08
Nodes (16): CameraDeviceInfo, CameraLaunchMode, BackgroundInGameOnly, ExternalPreviewWindow, None, CameraListJsonWrapper, PythonProcessManager, ActiveLaunchMode (+8 more)

### Community 10 - "Community 10"
Cohesion: 0.09
Nodes (12): BootFlowController, BootPhase, Lobby, Splash, StartScreen, CurrentPhase, IsInLobby, SkipBootScreensOnce (+4 more)

### Community 11 - "Community 11"
Cohesion: 0.06
Nodes (17): DragonGameSettings, BgmVolume, HasSavedTurnSensitivity, IsCameraCalibrated, IsFreePlaySelected, IsFreePlayUnlocked, IsStage2UnlockedPlaceholder, IsStage3UnlockedPlaceholder (+9 more)

### Community 12 - "Community 12"
Cohesion: 0.21
Nodes (4): DragonVfxController, IsWaterBreathActive, IsWaterSiphonActive, IsWingtipTrailsActive

### Community 13 - "Community 13"
Cohesion: 0.11
Nodes (8): create_command_socket(), main(), parse_args(), poll_calibration_commands(), HybridPoseTracker, CalibrationState, PlayerCalibrator, test_calibration_step_sequence()

### Community 14 - "Community 14"
Cohesion: 0.13
Nodes (7): DragonWingAnimator, ActiveWingRegime, IsRigReady, WingBoneRig, IsValid, WingPoseTargets, WingSideState

### Community 15 - "Community 15"
Cohesion: 0.11
Nodes (15): DragonBodyAnimator, BoneRig, IsValid, CurrentHoverBobOffsetY, CurrentHoverChestElevationDeg, CurrentJawAngle, CurrentSpineArchDeg, CurrentTailSwayDeg (+7 more)

### Community 16 - "Community 16"
Cohesion: 0.13
Nodes (7): LobbyNavigationManager, ActiveTab, CalibrationPanel, LobbyTab, Calibration, Missions, Settings

### Community 17 - "Community 17"
Cohesion: 0.19
Nodes (7): QuestLineData, QuestNarrativeUI, CurrentAutoNextDuration, CurrentLineIndex, IsPlaying, RemainingLineSeconds, TotalLines

### Community 18 - "Community 18"
Cohesion: 0.22
Nodes (3): SolidCameraDropdown, IsOpen, OptionItem

### Community 19 - "Community 19"
Cohesion: 0.16
Nodes (6): WorldSpaceVillageMarker, CurrentDisplayText, CurrentDistance, IsBillboardVisible, IsMarkerActive, LocationName

### Community 21 - "Community 21"
Cohesion: 0.23
Nodes (5): DragonHeadLookController, CurrentHeadPitchDeg, HeadBoneRig, IsValid, IsRigReady

### Community 22 - "Community 22"
Cohesion: 0.20
Nodes (3): RecentPoseSlotUI, MissionsPanelUI, PoseGuideCardSlot

### Community 23 - "Community 23"
Cohesion: 0.11
Nodes (4): PauseModalUI, CalibrationSubPanel, IsPaused, SettingsPanelUI

### Community 24 - "Community 24"
Cohesion: 0.22
Nodes (5): DefaultFlightInputMapper, CurrentCommand, DragonLocomotionConfig, FlightCommand, IFlightInputProvider

### Community 26 - "Community 26"
Cohesion: 0.12
Nodes (5): test_scenario_8_udp_protocol_integrity(), test_udp_34_float_telemetry_protocol(), main(), PoseUDPSender, VideoUDPSender

### Community 29 - "Community 29"
Cohesion: 0.15
Nodes (5): draw_debug_overlay(), compute_smooth_tilt(), recover_motion_blurred_wrists(), TrackingState, test_clean_cv_preview()

### Community 32 - "Community 32"
Cohesion: 0.29
Nodes (7): ActiveSessionKind, Narrative, None, PyrewoodForest, PyrewoodLake, SolariaChallenge, ZephyriaChallenge

### Community 33 - "Community 33"
Cohesion: 0.18
Nodes (5): VillageChallengeUI, PoseDewRainSprite, PoseFlapSprite, PoseLakeWaterSprite, PoseWaterBreathSprite

### Community 34 - "Community 34"
Cohesion: 0.33
Nodes (6): VisualCameraState, Error, LiveBackground, LiveExternalPreview, Standby, StartingAI

### Community 36 - "Community 36"
Cohesion: 0.24
Nodes (3): GameManager, ElapsedSeconds, Instance

### Community 37 - "Community 37"
Cohesion: 0.13
Nodes (5): CylinderTrigger, VillageId, Pyrewood, Solaria, Zephyria

### Community 39 - "Community 39"
Cohesion: 0.25
Nodes (21): GestureState, GestureStateMachine, make_kpts(), run_all_tests(), test_scenario_10_fly_up_to_normal_flight(), test_scenario_1_chest_raised_no_flap(), test_scenario_2_chest_raised_with_flap(), test_scenario_3_upper_arms_tucked_no_motion() (+13 more)

### Community 41 - "Community 41"
Cohesion: 0.13
Nodes (17): BiometricNormalizer, RobustKeypointTracker, run_all_validations(), test_scenario_1_standing_still(), test_scenario_2_arms_up_down(), test_scenario_3_body_leaning(), test_scenario_5_distance_scale_invariance(), test_scenario_6_out_of_frame_and_recovery() (+9 more)

### Community 42 - "Community 42"
Cohesion: 0.11
Nodes (9): AdaptiveThresholdEngine, get_default_profile(), PlayerBiometricProfile, run_all_phase4_tests(), test_adaptive_flap_thresholding(), test_adaptive_leaning_and_asymmetry_deadzone(), test_adaptive_tuck_dive(), test_comfort_and_fatigue_reduction() (+1 more)

### Community 43 - "Community 43"
Cohesion: 0.11
Nodes (7): ThreadedCamera, benchmark_model(), main(), PoseStabilizer, main(), main(), main()

### Community 44 - "Community 44"
Cohesion: 0.09
Nodes (6): compute_nose_chin_elevation_deg(), MouthTracker, normalize_head_pitch(), AdaptiveOneEuroFilter, run_tests(), _simulate_3d_face_points()

### Community 45 - "Community 45"
Cohesion: 0.16
Nodes (5): enumerate_dshow_cameras(), __init__(), resolve_camera_index(), check_cameras(), list_cameras_json()

### Community 46 - "Community 46"
Cohesion: 0.34
Nodes (10): FlightCommand, run_all_phase3_tests(), SimulatedFlightController, test_flap_to_climb(), test_glide_equilibrium(), test_lean_to_turn_and_bank(), test_propeller_dash(), test_terrain_clearance_and_ceiling() (+2 more)

### Community 47 - "Community 47"
Cohesion: 0.07
Nodes (14): ContinuousLeanClassifier, FlapStrokeClassifier, FlapStrokePhase, FlyDownClassifier, FlyUpClassifier, GentleDescentClassifier, HoverClassifier, _load_cv_locomotion_config() (+6 more)

### Community 48 - "Community 48"
Cohesion: 0.18
Nodes (4): UDPVideoStreamReceiver, Instance, IsVideoConnected, VideoTexture

### Community 49 - "Community 49"
Cohesion: 0.31
Nodes (3): build_kpts_dict(), FlightPhysicsEmulator, run_phase6_tests()

### Community 52 - "Community 52"
Cohesion: 0.40
Nodes (7): simulate_unity_physics(), _base_torso(), _step_frames(), test_flap_and_propeller_gradual_telemetry(), test_fly_up_only_from_y_pose_not_head_pitch(), test_hover_active_vs_slow_glide(), test_locked_6_poses_zero_regression()

## Knowledge Gaps
- **279 isolated node(s):** `Pyrewood`, `Solaria`, `Zephyria`, `ActiveStageIndex`, `ActiveVillageCount` (+274 more)
  These have ≤1 connection - possible missing edges. (Counts symbols only; 471 node(s) total have ≤1 connection when file, concept and rationale nodes are included.)
- **14 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `UDPPoseReceiver` connect `Community 4` to `Community 0`, `Community 1`, `Community 2`, `Community 3`, `Community 5`, `Community 6`, `Community 9`, `Community 11`, `Community 14`, `Community 15`, `Community 16`, `Community 19`, `Community 20`, `Community 21`, `Community 23`, `Community 24`, `Community 30`, `Community 31`, `Community 35`, `Community 38`, `Community 48`?**
  _High betweenness centrality (0.117) - this node is a cross-community bridge._
- **What connects `Pyrewood`, `Solaria`, `Zephyria` to the rest of the system?**
  _279 weakly-connected nodes found - possible missing edges._
- **Should `Community 0` be split into smaller, more focused modules?**
  _Cohesion score 0.09462365591397849 - nodes in this community are weakly interconnected._
- **Why does `VillageZoneController` connect `Community 0` to `Community 32`, `Community 33`, `Community 2`, `Community 3`, `Community 36`, `Community 37`, `Community 6`, `Community 5`, `Community 8`, `Community 11`, `Community 12`, `Community 17`, `Community 19`, `Community 28`?**
  _High betweenness centrality (0.082) - this node is a cross-community bridge._
- **Should `Community 1` be split into smaller, more focused modules?**
  _Cohesion score 0.14022988505747128 - nodes in this community are weakly interconnected._
- **Why does `InGameFlightHUD` connect `Community 2` to `Community 0`, `Community 1`, `Community 3`, `Community 4`, `Community 6`, `Community 8`, `Community 9`, `Community 11`, `Community 15`, `Community 17`, `Community 19`, `Community 20`, `Community 22`, `Community 23`, `Community 28`, `Community 33`, `Community 36`, `Community 37`, `Community 54`?**
  _High betweenness centrality (0.076) - this node is a cross-community bridge._
- **Should `Community 2` be split into smaller, more focused modules?**
  _Cohesion score 0.11290322580645161 - nodes in this community are weakly interconnected._