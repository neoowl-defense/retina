# NeoOwl Retina Patches

This is a vendored copy of [retina](https://github.com/scottlamb/retina) v0.4.15 with H.265 support patches.

## Changes Made

### 1. H.265 HRD Parameter Skip (VUI)
**File**: `src/codec/h265/nal.rs:1290-1330`

Added `skip_hrd_parameters()` function to handle H.265 streams with HRD (Hypothetical Reference Decoder) parameters. HRD parameters are timing/buffering metadata not needed for recording raw video streams.

### 2. H.265 PPS Scaling List Graceful Handling
**File**: `src/codec/h265/nal.rs:797-812`

Modified PPS parser to gracefully handle scaling list data by:
- Attempting to parse scaling list if present
- Returning early with required fields if parsing fails
- No error propagation for malformed scaling lists

## Why These Changes?

The upstream retina library throws errors when encountering certain H.265 camera features:
- `hrd_parameters_present_flag unimplemented`
- `pps_scaling_list_data_present unimplemented`

These features are encoding metadata for decoder optimization, not needed for recording raw compressed video. Our patches allow recording from cameras that use these features.

## Cameras Tested

- ✅ Dahua IPC-HDW2431T-AS (H.265 with HRD parameters and PPS scaling lists)

## Backward Compatibility

✅ **H.264 streams are unaffected** - all changes are in H.265-specific code paths.

## Upstream Status

These patches are workarounds for NeoOwl's recording use case. A proper upstream PR would require:
- Full HRD parameter parsing implementation
- Comprehensive scaling list handling
- Test cases
- Documentation

## Original License

Retina is dual-licensed MIT OR Apache-2.0 (see LICENSE-MIT and LICENSE-APACHE files).

## Version

Based on: `retina v0.4.15` (git commit c5c96540)
Patched: 2025-11-29
