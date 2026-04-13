# Video Extend: SVI Integration User Guide

## Overview

The SVI (Stable Video Integration) LoRA system enhances SwarmUI's Video Extend feature, enabling longer videos (60+ seconds) with improved character consistency and smoother transitions. This guide covers installation, configuration, and best practices for using SVI LoRAs with Video Extend.

---

## What Are SVI LoRAs?

SVI LoRAs are specialized Low-Rank Adaptation models designed specifically for the Wan 2.2 video generation pipeline. They work in pairs:

| LoRA Type | Applied To | Purpose |
|-----------|------------|---------|
| **SVI High LoRA** | Base Video Extend Model (high-noise stage) | Maintains character consistency across long video sequences |
| **SVI Low LoRA** | Swap Model (low-noise stage) | Ensures smooth continuity between generation stages |

### Why Use SVI LoRAs?

- **Extended Length**: Generate videos 60+ seconds long without quality degradation
- **Character Consistency**: Maintain consistent characters across extended sequences
- **Smoother Transitions**: Reduced visible jumps between video segments
- **Professional Results**: Higher quality output suitable for longer content

---

## Installation & Setup

### 1. Download SVI LoRA Files

SVI LoRAs are typically available as paired `.safetensors` files:

```
SVI_CharacterName_High.safetensors  # For the base model
SVI_CharacterName_Low.safetensors   # For the swap model
```

### 2. Install LoRA Files

Place the downloaded SVI LoRA files in your SwarmUI LoRA folder:

```
Models/LoRA/SVI_CharacterName_High.safetensors
Models/LoRA/SVI_CharacterName_Low.safetensors
```

> **Important**: Only `.safetensors` format is supported. Ensure files are correctly named and placed in the `LoRA` subfolder.

### 3. Configure Your Models

SVI LoRAs work with Wan 2.2 model pairs:
- **Wan 2.2 HIGH** (Image-to-Video base model)
- **Wan 2.2 LOW** (Swap model for low-noise stage)

Make sure both models are selected in the Video Extend tab before enabling SVI LoRAs.

---

## Parameter Reference

### Video Extend SVI High LoRA

| Setting | Description |
|---------|-------------|
| **Default** | `""` (disabled) |
| **Purpose** | Apply SVI High-Noise LoRA to the base Video Extend Model |
| **When to Use** | For videos 60+ seconds requiring character consistency |
| **Compatible Models** | Wan 2.2 HIGH model (base stage) |

**Example Usage:**
```
SVI_CharacterX_High.safetensors
SVI_Wan_High_v1.safetensors
```

### Video Extend SVI Low LoRA

| Setting | Description |
|---------|-------------|
| **Default** | `""` (disabled) |
| **Purpose** | Apply SVI Low-Noise LoRA to the Video Extend Swap Model |
| **When to Use** | Must be paired with SVI High LoRA |
| **Compatible Models** | Wan 2.2 LOW model (swap stage) |

**Important:** SVI Low LoRA should match the High LoRA variant. For example:
- If using `SVI_CharacterX_High.safetensors`, use `SVI_CharacterX_Low.safetensors`

### Video Extend Auto-Chain

| Setting | Description |
|---------|-------------|
| **Default** | `false` (disabled) |
| **Purpose** | Automatically chain multiple 5-second segments |
| **When to Use** | For long-form content without manual `<extend:N>` blocks |

**Examples:**
- **40-second video**: Enable Auto-Chain, set Total Duration to 40
- **60 seconds** = ~12 segments (5 sec each)
- **30 seconds** = ~6 segments (5 sec each)

### Video Extend Total Duration (seconds)

| Setting | Description |
|---------|-------------|
| **Default** | `40` |
| **Range** | 20-120 seconds |
| **Purpose** | Target total video duration when Auto-Chain is enabled |
| **Calculation** | Segments = Total Duration / 5 seconds |

**Examples:**
- 20 seconds = 4 segments
- 40 seconds = 8 segments
- 60 seconds = 12 segments
- 120 seconds = 24 segments

### Video Extend Frame Overlap

| Setting | Description |
|---------|-------------|
| **Default** | `25` |
| **Range** | 1-128 frames |
| **Purpose** | Number of frames repeated between segments for continuity |

**Recommendations with SVI LoRAs:**
- **9 frames**: Minimum, may show visible transitions
- **16 frames**: Better continuity, moderate VRAM usage
- **25-30 frames**: RECOMMENDED for SVI LoRAs, smoothest transitions
- **Valid frame counts for Wan**: Multiple of 4 plus 1 (5, 9, 13, 17, 21, 25, 29, ...)

---

## Example Workflows

### Workflow 1: 60-Second Character Consistent Video

**Goal**: Generate a 60-second video with consistent character appearance

**Setup:**
```
✓ Video Extend Model: Wan 2.2 HIGH
✓ Video Extend Swap Model: Wan 2.2 LOW
✓ SVI High LoRA: SVI_MyCharacter_High.safetensors
✓ SVI Low LoRA: SVI_MyCharacter_Low.safetensors
✓ Auto-Chain: Enabled
✓ Total Duration: 60
✓ Frame Overlap: 25
```

**Steps:**
1. Enter your character description in the prompt
2. Configure settings as shown above
3. Click Generate
4. System will automatically create 12 segments (5 seconds each)

### Workflow 2: Extended 120-Second Video with SVI LoRAs

**Goal**: Create a 2-minute video with maximum quality

**Setup:**
```
✓ Video Extend Model: Wan 2.2 HIGH
✓ Video Extend Swap Model: Wan 2.2 LOW
✓ SVI High LoRA: SVI_Environment_High.safetensors
✓ SVI Low LoRA: SVI_Environment_Low.safetensors
✓ Auto-Chain: Enabled
✓ Total Duration: 120
✓ Frame Overlap: 28
```

**Steps:**
1. Plan your scene transitions in the prompt
2. Configure settings as shown above
3. Enable output of intermediate images if you want to review segments
4. Click Generate (will take longer for 24 segments)

### Workflow 3: Short Video Without SVI LoRAs

**Goal**: Create a simple 20-second extension without SVI LoRAs

**Setup:**
```
✓ Video Extend Model: Wan 2.2 HIGH
✓ Video Extend Swap Model: Wan 2.2 LOW
✓ SVI High LoRA: (leave empty)
✓ SVI Low LoRA: (leave empty)
✓ Auto-Chain: Enabled
✓ Total Duration: 20
✓ Frame Overlap: 25
```

---

## Troubleshooting

### Error: "SVI High LoRA 'filename.safetensors' not found in Models/LoRA folder"

**Cause:** The specified LoRA file doesn't exist in the LoRA folder.

**Solutions:**
1. Verify the file exists at `Models/LoRA/filename.safetensors`
2. Check for typos in the LoRA filename
3. Ensure the file has `.safetensors` extension
4. Reload the LoRA list in SwarmUI if files were added while the application was running

### Error: "SVI Low LoRA 'filename.safetensors' not found in Models/LoRA folder"

**Cause:** The specified Low LoRA file doesn't exist.

**Solutions:**
1. Verify the matching Low LoRA file exists
2. Ensure you're using a complete High/Low pair from the same variant
3. Check file naming matches the High LoRA (e.g., `MyCharacter_High` and `MyCharacter_Low`)

### Warning: "SVI High LoRA is specified but no SVI Low LoRA is set"

**Cause:** Only the High LoRA is selected without the matching Low LoRA.

**Solution:** Add the corresponding Low LoRA file for best continuity results.

### Warning: "SVI LoRAs are designed for Wan 2.2 HIGH/LOW model pairs"

**Cause:** SVI LoRAs are being used with a model other than Wan 2.2.

**Solution:** SVI LoRAs are specifically trained for Wan 2.2 models. Use with Wan 2.2 HIGH and LOW models for optimal results. Other models may produce unpredictable output.

### Warning: "SVI LoRAs are enabled but Frame Overlap is set to X (below recommended 25)"

**Cause:** Frame overlap is too low for SVI LoRA usage.

**Solution:** Increase Frame Overlap to at least 25 frames (or higher, up to 28-30) for smoother transitions with SVI LoRAs.

### Warning: "Auto-Chain is enabled with a very short Total Duration"

**Cause:** Total Duration is set below 20 seconds with Auto-Chain enabled.

**Solution:** For better results with Auto-Chain, use 20+ seconds. For very short videos (under 20 seconds), consider disabling Auto-Chain and using manual `<extend:N>` syntax.

### Issue: Visible transitions between segments

**Possible Causes:**
1. Frame Overlap is too low
2. SVI LoRAs not being used for long videos
3. Incompatible model pairing

**Solutions:**
1. Increase Frame Overlap to 25-30 frames
2. Use matching SVI High/Low LoRA pairs for videos 60+ seconds
3. Verify you're using Wan 2.2 HIGH and LOW models

### Issue: Inconsistent character appearance

**Possible Causes:**
1. SVI LoRAs not enabled for long videos
2. Frame Overlap too low
3. Segment count too high without SVI LoRAs

**Solutions:**
1. Enable both SVI High and Low LoRAs for videos 60+ seconds
2. Increase Frame Overlap to 25+ frames
3. Use appropriate SVI LoRA variant for your character type

---

## FAQ

### Q: Do I need SVI LoRAs for all Video Extend videos?

**A:** No. SVI LoRAs are primarily beneficial for:
- Videos 60+ seconds in length
- Videos requiring character consistency across segments
- Professional/production-quality outputs

For short extensions (under 60 seconds), you can use Video Extend without SVI LoRAs.

### Q: Can I use only SVI High LoRA without the Low LoRA?

**A:** Technically yes, but it's not recommended. SVI LoRAs are designed as a pair:
- High LoRA affects the high-noise (base model) stage
- Low LoRA affects the low-noise (swap model) stage
- Using only one may cause discontinuities between generation stages

### Q: What happens if I use mismatched SVI LoRA pairs?

**A:** Using mismatched pairs (e.g., `CharacterA_High` with `CharacterB_Low`) may cause:
- Inconsistent character appearance
- Visual artifacts between stages
- Reduced continuity

Always use matching High/Low pairs from the same variant.

### Q: Can I use SVI LoRAs with models other than Wan 2.2?

**A:** SVI LoRAs are specifically trained for Wan 2.2 models. Using them with other models may produce unpredictable or suboptimal results. You will receive a warning if you try.

### Q: How does Auto-Chain differ from manual `<extend:N>` syntax?

**A:** 
- **Auto-Chain**: Automatically calculates segments based on Total Duration, no manual syntax needed
- **Manual `<extend:N>`**: Requires explicit `<extend:frames>` blocks in your prompt

Use Auto-Chain for convenience, manual syntax for fine-grained control over each segment.

### Q: What's the maximum video length I can create?

**A:** With Auto-Chain enabled and proper settings, you can create videos up to 120 seconds (24 segments × 5 seconds). For longer videos, consider running multiple generations and stitching them together.

### Q: Do SVI LoRAs work with Image-to-Video or Text-to-Video?

**A:** SVI LoRAs work with Image-to-Video workflow (using Video Extend). They are applied to the extension generation process, not the initial image generation.

---

## Best Practices Summary

| Scenario | Recommended Settings |
|----------|---------------------|
| **Short video (under 20 seconds)** | Auto-Chain: Disabled, Manual extend blocks |
| **Medium video (20-60 seconds)** | Auto-Chain: Enabled, Total Duration: 20-60, Frame Overlap: 25 |
| **Long video (60+ seconds)** | SVI LoRAs: Enabled, Auto-Chain: Enabled, Total Duration: 60-120, Frame Overlap: 25-30 |
| **Maximum character consistency** | Matching SVI High/Low LoRAs + Frame Overlap 28-30 + Wan 2.2 models |

### Quick Checklist Before Generation

- [ ] SVI LoRA files placed in `Models/LoRA/` folder
- [ ] SVI High and Low LoRA pair are from the same variant
- [ ] Video Extend Model set to Wan 2.2 HIGH
- [ ] Video Extend Swap Model set to Wan 2.2 LOW
- [ ] Frame Overlap set to 25+ for SVI LoRA usage
- [ ] Total Duration matches desired video length
- [ ] Auto-Chain enabled for videos over 20 seconds

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | Initial Release | Initial SVI integration with Video Extend |

---

## Additional Resources

- [Video Extend Overview](../Video Model Support.md)
- [Model Support Guide](../Model Support.md)
- [SwarmUI Basic Usage](../Basic Usage.md)

---

For issues or questions, refer to the [Troubleshooting section](#troubleshooting) or check the SwarmUI community forums.