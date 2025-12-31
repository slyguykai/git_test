# xCreate Clone - Implementation Plan

## Overview

Clone of **xCreate - AI Image Generator**, a state-of-the-art local AI image generation app for macOS. The app runs Stable Diffusion and FLUX models natively on Apple Silicon with complete privacy (no data leaves the device).

---

## Core Features to Implement

### 1. Text-to-Image Generation
- User enters a text prompt describing the desired image
- AI model generates images based on the prompt
- Support for positive and negative prompts
- Real-time generation progress indicator

### 2. Image-to-Image Transformation
- Upload/drag-drop existing image as starting point
- Apply AI transformations based on text prompts
- Control denoising strength/influence of original image

### 3. Inpainting & Outpainting
- Select areas of image to regenerate (inpainting)
- Extend images beyond original boundaries (outpainting)
- Brush tool for mask creation

### 4. Model Management
- Download models directly within app
- Switch between different models (SD 1.5, SDXL, FLUX)
- Custom model import (safetensors, ckpt)
- Model memory offloading for large models

### 5. Advanced Generation Settings
- **Steps**: Number of diffusion steps (1-150)
- **Guidance Scale (CFG)**: How closely to follow prompt (1-30)
- **Seed**: Reproducible generation with specific seeds
- **Scheduler**: Different sampling methods (Euler, DPM++, etc.)
- **Image Size**: Width/height customization
- **Batch Size**: Generate multiple images at once

### 6. Image Gallery & History
- View all generated images
- Metadata stored with each image (prompt, settings, model)
- Favorite/organize images
- Export in various formats (PNG, JPEG, WebP)

### 7. Privacy & Local Processing
- 100% offline operation
- No data sent to servers
- All models run locally via Core ML

---

## Technical Architecture

### Technology Stack

| Component | Technology |
|-----------|------------|
| Language | Swift 5.9+ |
| UI Framework | SwiftUI |
| ML Runtime | Core ML, Metal Performance Shaders |
| Model Format | Core ML (.mlmodelc), converted from PyTorch |
| Diffusion Library | Apple's ml-stable-diffusion or swift-diffusers |
| Minimum macOS | 13.1+ (Ventura) |
| Hardware | Apple Silicon (M1/M2/M3/M4) required |
| RAM Requirement | 16GB minimum, 24GB+ for FLUX |

### Project Structure

```
xCreateClone/
├── xCreateClone.xcodeproj
├── xCreateClone/
│   ├── App/
│   │   ├── xCreateCloneApp.swift          # App entry point
│   │   └── AppState.swift                  # Global app state
│   │
│   ├── Views/
│   │   ├── MainView.swift                  # Main window layout
│   │   ├── Sidebar/
│   │   │   ├── SidebarView.swift           # Navigation sidebar
│   │   │   └── ModelSelectorView.swift     # Model picker
│   │   ├── Generation/
│   │   │   ├── PromptInputView.swift       # Text prompt input
│   │   │   ├── GenerationSettingsView.swift # Advanced settings
│   │   │   ├── ImagePreviewView.swift      # Generated image display
│   │   │   └── ProgressView.swift          # Generation progress
│   │   ├── Gallery/
│   │   │   ├── GalleryView.swift           # Image grid
│   │   │   └── ImageDetailView.swift       # Full image view
│   │   ├── ImageEditor/
│   │   │   ├── InpaintingView.swift        # Inpainting canvas
│   │   │   └── BrushToolView.swift         # Mask painting
│   │   └── Settings/
│   │       ├── SettingsView.swift          # App preferences
│   │       └── ModelManagerView.swift      # Model downloads
│   │
│   ├── Models/
│   │   ├── GenerationRequest.swift         # Generation parameters
│   │   ├── GeneratedImage.swift            # Image + metadata
│   │   ├── AIModel.swift                   # Model info
│   │   └── AppSettings.swift               # User preferences
│   │
│   ├── Services/
│   │   ├── DiffusionService.swift          # Core ML pipeline
│   │   ├── ModelManager.swift              # Download/load models
│   │   ├── ImageStorage.swift              # Save/load images
│   │   └── MetadataService.swift           # Image metadata
│   │
│   ├── Utilities/
│   │   ├── Extensions/
│   │   ├── Helpers/
│   │   └── Constants.swift
│   │
│   └── Resources/
│       ├── Assets.xcassets
│       └── Models/                          # Bundled/downloaded models
│
├── Packages/
│   └── Dependencies via SPM
│
└── Tests/
```

---

## Implementation Phases

### Phase 1: Foundation (Week 1-2)
1. **Project Setup**
   - Create Xcode project with SwiftUI lifecycle
   - Configure for macOS 13.1+ and Apple Silicon
   - Set up Swift Package Manager dependencies
   - Configure app entitlements (file access, network)

2. **Core ML Integration**
   - Integrate Apple's ml-stable-diffusion Swift package
   - Set up model loading infrastructure
   - Implement basic text-to-image pipeline
   - Test with SD 1.5 model

### Phase 2: Core UI (Week 2-3)
3. **Main Interface**
   - Three-column layout (sidebar, canvas, settings)
   - Prompt input with positive/negative fields
   - Generate button with keyboard shortcut
   - Image preview area with zoom/pan

4. **Generation Settings Panel**
   - Steps slider (1-150)
   - Guidance scale slider (1-30)
   - Seed input (random/manual)
   - Scheduler dropdown
   - Image dimensions picker
   - Batch size selector

### Phase 3: Model System (Week 3-4)
5. **Model Management**
   - Model browser with thumbnails
   - Download manager with progress
   - Model switching without restart
   - Custom model import
   - Memory optimization (model offloading)

6. **Support Multiple Model Types**
   - Stable Diffusion 1.5
   - Stable Diffusion 2.1
   - SDXL
   - FLUX (if RAM permits)

### Phase 4: Advanced Features (Week 4-5)
7. **Image-to-Image**
   - Image input via drag-drop
   - Denoising strength control
   - Reference image preview

8. **Inpainting/Outpainting**
   - Brush tool for mask painting
   - Eraser and fill tools
   - Outpaint direction controls
   - Mask visualization

### Phase 5: Gallery & Polish (Week 5-6)
9. **Image Gallery**
   - Grid view of generated images
   - Metadata display
   - Search/filter by prompt
   - Favorite system
   - Batch export

10. **Final Polish**
    - Keyboard shortcuts
    - Menu bar integration
    - Dark/light mode
    - Performance optimization
    - Error handling & recovery

---

## Key Dependencies

### Swift Packages

```swift
// Package.swift dependencies
dependencies: [
    // Apple's Stable Diffusion implementation
    .package(url: "https://github.com/apple/ml-stable-diffusion", from: "1.0.0"),

    // Or Hugging Face's Swift Diffusers
    .package(url: "https://github.com/huggingface/swift-transformers", from: "0.1.0"),

    // Image handling
    .package(url: "https://github.com/SDWebImage/SDWebImageSwiftUI", from: "2.0.0"),

    // File management
    .package(url: "https://github.com/weichsel/ZIPFoundation", from: "0.9.0"),
]
```

### Model Sources

| Model | Size | Source |
|-------|------|--------|
| SD 1.5 | ~5GB | Hugging Face (Core ML converted) |
| SD 2.1 | ~5GB | Hugging Face |
| SDXL | ~12GB | Hugging Face |
| FLUX Dev | ~13.6GB | Hugging Face |

---

## UI Design Specifications

### Main Window Layout

```
+------------------------------------------------------------------+
|  [App Menu]                                          [_] [□] [X] |
+------------------------------------------------------------------+
|        |                                    |                    |
| Models |       Image Preview               |   Generation       |
|  [v]   |                                    |   Settings         |
|        |   +------------------------+      |                    |
| SD 1.5 |   |                        |      | Steps: [====] 30   |
| SD 2.1 |   |                        |      | CFG:   [===] 7.5   |
| SDXL   |   |    Generated Image     |      | Seed:  [Random]    |
| FLUX   |   |                        |      | Size:  512x512     |
|        |   |                        |      | Scheduler: [Euler] |
+--------+   +------------------------+      | Batch: 1           |
|        |                                    |                    |
| Recent |   Prompt:                          +--------------------+
| Images |   [Beautiful sunset over ocean... ]                    |
|        |                                                         |
| [img1] |   Negative:                                            |
| [img2] |   [blurry, low quality, deformed...]                   |
| [img3] |                                                         |
|        |   [       Generate (⌘G)        ]                       |
+--------+---------------------------------------------------------+
```

### Color Scheme

- **Dark Mode** (default):
  - Background: #1E1E1E
  - Surface: #2D2D2D
  - Accent: #007AFF (macOS blue)
  - Text: #FFFFFF

- **Light Mode**:
  - Background: #F5F5F5
  - Surface: #FFFFFF
  - Accent: #007AFF
  - Text: #000000

---

## Data Models

### GenerationRequest
```swift
struct GenerationRequest: Codable {
    let id: UUID
    let prompt: String
    let negativePrompt: String?
    let steps: Int              // 1-150
    let guidanceScale: Double   // 1.0-30.0
    let seed: UInt32?           // nil = random
    let width: Int              // 256-2048
    let height: Int             // 256-2048
    let scheduler: Scheduler
    let modelId: String
    let batchSize: Int          // 1-4
    let inputImage: Data?       // For img2img
    let mask: Data?             // For inpainting
    let denoisingStrength: Double? // 0.0-1.0
}
```

### GeneratedImage
```swift
struct GeneratedImage: Codable, Identifiable {
    let id: UUID
    let imageData: Data
    let thumbnailData: Data
    let request: GenerationRequest
    let generatedAt: Date
    let generationTime: TimeInterval
    var isFavorite: Bool
    var tags: [String]
}
```

---

## Performance Considerations

### Memory Management
- Load only one model at a time
- Implement model unloading when switching
- Use memory-mapped files for large models
- Stream model chunks for FLUX

### Generation Optimization
- Use Metal Performance Shaders
- Enable ANE (Apple Neural Engine) when available
- Implement generation queue for batch processing
- Show progress per step

### Expected Performance (M1/M2/M3)

| Model | Resolution | Steps | Time |
|-------|------------|-------|------|
| SD 1.5 | 512x512 | 30 | ~10-15s |
| SDXL | 1024x1024 | 30 | ~45-60s |
| FLUX | 704x704 | 25 | ~5-6min |

---

## Required Entitlements

```xml
<!-- xCreateClone.entitlements -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "...">
<plist version="1.0">
<dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.security.files.user-selected.read-write</key>
    <true/>
    <key>com.apple.security.files.downloads.read-write</key>
    <true/>
    <key>com.apple.security.network.client</key>
    <true/>
</dict>
</plist>
```

---

## Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Large model downloads | Poor UX | Resume support, progress indication |
| Memory constraints | Crashes | Model size warnings, graceful degradation |
| Slow generation | User frustration | Progress per step, cancel support |
| Core ML compatibility | Limited models | Focus on well-tested conversions |
| App Store rejection | Distribution issues | Offer direct download alternative |

---

## Testing Strategy

### Unit Tests
- Model loading/unloading
- Parameter validation
- Image storage/retrieval

### Integration Tests
- Full generation pipeline
- Model switching
- Gallery operations

### Performance Tests
- Memory usage monitoring
- Generation time benchmarks
- Cold/warm start times

### Manual Testing
- Various prompt types
- Edge cases (very long prompts, special characters)
- Hardware configurations (different M-series chips)

---

## Future Enhancements (Post-MVP)

1. **ControlNet Support** - Guide generation with pose/edge detection
2. **LoRA Support** - Fine-tuned style models
3. **Upscaling** - AI-powered image upscaling
4. **Animation** - Generate image sequences
5. **Plugins** - Extensible architecture
6. **Cloud Sync** - iCloud gallery sync (optional)
7. **iOS Companion** - iPhone/iPad version

---

## Sources & References

- [xCreate Official](https://xcreate.com/) - Original app
- [Apple ml-stable-diffusion](https://github.com/apple/ml-stable-diffusion) - Core ML implementation
- [Swift Diffusers (Hugging Face)](https://huggingface.co/blog/fast-mac-diffusers) - Alternative Swift implementation
- [DiffusionBee](https://diffusionbee.com/) - Reference macOS app
- [FLUX on Mac Guide](https://stable-diffusion-art.com/flux-mac/) - FLUX model setup
- [Core ML Stable Diffusion Tutorial](https://www.createwithswift.com/generating-images-with-stable-diffusion-on-apple-silicon-with-core-ml/)

---

## Summary

This plan outlines a 1:1 clone of xCreate with:
- **Native macOS app** using Swift/SwiftUI
- **Local AI generation** via Core ML (100% private)
- **Multiple model support** (SD 1.5, SDXL, FLUX)
- **Full feature set**: text-to-image, img2img, inpainting
- **Professional UI** with gallery and settings

The app prioritizes privacy, performance on Apple Silicon, and a polished user experience matching or exceeding the original xCreate.
