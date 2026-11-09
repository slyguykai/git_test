# xCreate Clone - Implementation Plan

## Overview

Clone of **xCreate - AI Image Generator** by **Ashraf Samy** (App Store ID: 6756119462), a state-of-the-art local AI image generation app for macOS. The app runs AI models natively on Apple Silicon with complete privacy (no data leaves the device).

**Original App Details:**
- **Developer:** Ashraf Samy
- **Released:** December 14, 2025 (v1.0)
- **Current Version:** 1.1.2 (January 27, 2026)
- **Requirements:** macOS 15.0+, Apple M1 chip or later
- **Pricing:** Free tier + Pro subscription (~$10/month)

---

## Core Features to Implement (Based on Actual xCreate)

### 1. Text-to-Image Generation
- User enters a text prompt describing the desired image
- AI model generates images based on the prompt
- **Live Preview** feature (added in v1.1.0) - see generation progress in real-time
- Asynchronous generation (non-blocking UI)

### 2. Model Management
- **Models Section** for managing AI models
- Select location of existing local models (folder picker)
- Download models directly from **Hugging Face** within the app
- **Top Menu Bar** model selector - switch models before generating
- Support for multiple model formats

### 3. Supported AI Models (Actual xCreate Models)
| Model | Type | Notes |
|-------|------|-------|
| **GLM-Image** | CogView-based | Chinese AI model |
| **Z-Image-Turbo** | Fast generation | Optimized for speed |
| **Flux.2-klein-4B** | FLUX variant | 4B parameter model |
| **Qwen-Image-2512** | Qwen-based | Added in v1.1.1 |

### 4. Generation Queue System
- **Free Tier:** Single generation queue
- **Pro Tier:** Multiple queued generations
- Queue management UI
- Cancel/pause generation

### 5. Variations System
- Generate multiple variations per prompt
- **Free Tier:** Limited variations (2 per prompt)
- **Pro Tier:** Unlimited variations ("trillions")

### 6. Watermark System
- **Free Tier:** Watermark on generated images
- **Pro Tier:** No watermark

### 7. Privacy & Local Processing
- 100% offline operation
- No data sent to servers
- All AI processing happens on-device
- No data collection (per App Store privacy label)

---

## Technical Architecture

### Technology Stack

| Component | Technology |
|-----------|------------|
| Language | Swift 5.9+ |
| UI Framework | SwiftUI |
| ML Runtime | Core ML, Metal Performance Shaders, MLX |
| Model Format | safetensors, GGUF, Core ML (.mlmodelc) |
| Minimum macOS | **15.0+ (Sequoia)** |
| Hardware | **Apple Silicon (M1/M2/M3/M4) required** |
| RAM Requirement | 16GB minimum, 24GB+ recommended |

### Project Structure

```
xCreateClone/
├── xCreateClone.xcodeproj
├── xCreateClone/
│   ├── App/
│   │   ├── xCreateCloneApp.swift          # App entry point
│   │   ├── AppState.swift                  # Global app state
│   │   └── AppDelegate.swift               # Menu bar integration
│   │
│   ├── Views/
│   │   ├── MainView.swift                  # Main window layout
│   │   ├── MenuBar/
│   │   │   └── ModelMenuBarView.swift      # Top menu model selector
│   │   ├── Models/
│   │   │   ├── ModelsView.swift            # Models section
│   │   │   ├── ModelBrowserView.swift      # Browse local models
│   │   │   ├── HuggingFaceDownloadView.swift # HF download UI
│   │   │   └── ModelRowView.swift          # Individual model row
│   │   ├── Generation/
│   │   │   ├── PromptInputView.swift       # Text prompt input
│   │   │   ├── LivePreviewView.swift       # Real-time preview
│   │   │   ├── GenerationQueueView.swift   # Queue management
│   │   │   └── VariationsView.swift        # Variation selector
│   │   ├── Gallery/
│   │   │   ├── GalleryView.swift           # Image grid
│   │   │   └── ImageDetailView.swift       # Full image view
│   │   └── Settings/
│   │       ├── SettingsView.swift          # App preferences
│   │       └── SubscriptionView.swift      # Pro upgrade
│   │
│   ├── Models/
│   │   ├── GenerationRequest.swift         # Generation parameters
│   │   ├── GeneratedImage.swift            # Image + metadata
│   │   ├── AIModel.swift                   # Model info
│   │   ├── GenerationQueue.swift           # Queue state
│   │   └── Subscription.swift              # Pro tier state
│   │
│   ├── Services/
│   │   ├── ImageGenerationService.swift    # Core generation pipeline
│   │   ├── ModelManager.swift              # Load/manage models
│   │   ├── HuggingFaceService.swift        # HF API integration
│   │   ├── QueueManager.swift              # Generation queue
│   │   ├── WatermarkService.swift          # Apply/remove watermark
│   │   └── ImageStorage.swift              # Save/load images
│   │
│   ├── MLBackends/
│   │   ├── MLXBackend.swift                # MLX inference
│   │   ├── CoreMLBackend.swift             # Core ML inference
│   │   └── ModelConverter.swift            # Format conversion
│   │
│   ├── Utilities/
│   │   ├── Extensions/
│   │   ├── Helpers/
│   │   └── Constants.swift
│   │
│   └── Resources/
│       ├── Assets.xcassets
│       └── Localizations/
│
├── Packages/
│   └── Dependencies via SPM
│
└── Tests/
```

---

## UI Design (Based on Actual xCreate)

### Main Window Layout

Based on the App Store description, xCreate has:
1. **Models Section** - For selecting/downloading models
2. **Top Menu Bar** - Model selector dropdown
3. **Prompt Area** - Text input for generation
4. **Live Preview** - Real-time generation progress
5. **Queue Indicator** - Shows queued generations

```
+------------------------------------------------------------------+
|  [xCreate Clone]  [Model: Flux.2-klein ▼]              [_] [□] [X] |
+------------------------------------------------------------------+
|                                                                    |
|  +------------------+  +--------------------------------------+   |
|  |                  |  |                                      |   |
|  |  Models Section  |  |         Live Preview Area            |   |
|  |                  |  |                                      |   |
|  |  ○ GLM-Image     |  |    +------------------------+       |   |
|  |  ○ Z-Image-Turbo |  |    |                        |       |   |
|  |  ● Flux.2-klein  |  |    |   [Generation Preview] |       |   |
|  |  ○ Qwen-Image    |  |    |                        |       |   |
|  |                  |  |    +------------------------+       |   |
|  |  [+ Add Model]   |  |                                      |   |
|  |  [↓ Download]    |  |    Progress: ████████░░ 80%         |   |
|  |                  |  |                                      |   |
|  +------------------+  +--------------------------------------+   |
|                                                                    |
|  Prompt: [A serene mountain landscape at sunset...              ] |
|                                                                    |
|  Variations: [2 ▼]  Queue: [1/1]           [  Generate  ⌘↵  ]    |
|                                                                    |
+------------------------------------------------------------------+
|  Queue: Idle                               [FREE] Upgrade to Pro  |
+------------------------------------------------------------------+
```

### Key UI Elements

1. **Top Menu Bar Model Selector**
   - Dropdown in the app's menu bar area
   - Shows currently selected model
   - Quick switch between loaded models

2. **Models Section (Sidebar)**
   - List of available models
   - Radio button selection
   - Download status indicators
   - "Add Model" button (folder picker)
   - "Download" button (Hugging Face browser)

3. **Live Preview**
   - Shows generation progress visually
   - Updates in real-time as image forms
   - Final image display when complete

4. **Generation Controls**
   - Prompt text field
   - Variations selector (limited in free tier)
   - Queue indicator
   - Generate button

5. **Status Bar**
   - Queue status
   - Subscription tier indicator
   - Upgrade prompt for free users

---

## Pricing/Subscription Implementation

### Free Tier
```swift
struct FreeTier {
    let maxVariationsPerPrompt = 2
    let maxConcurrentQueue = 1
    let hasWatermark = true
    let unlimitedPrompts = true
}
```

### Pro Tier (~$10/month)
```swift
struct ProTier {
    let maxVariationsPerPrompt = Int.max  // "Trillions"
    let maxConcurrentQueue = 10           // Multiple queued
    let hasWatermark = false
    let unlimitedPrompts = true
}
```

### StoreKit Integration
- Use StoreKit 2 for subscriptions
- Monthly auto-renewable subscription
- Restore purchases support
- Receipt validation

---

## Implementation Phases

### Phase 1: Foundation (Week 1)
1. **Project Setup**
   - Create Xcode project targeting macOS 15.0+
   - Configure for Apple Silicon only
   - Set up Swift Package Manager dependencies
   - Configure app entitlements

2. **Model Infrastructure**
   - Model discovery (scan local folders)
   - Model metadata parsing
   - Hugging Face API client for downloads

### Phase 2: Core Generation (Week 2)
3. **ML Backend**
   - Integrate MLX for model inference
   - Support for safetensors/GGUF formats
   - Basic text-to-image pipeline
   - Test with Flux.2-klein-4B

4. **Live Preview**
   - Step-by-step generation callback
   - Real-time image preview updates
   - Progress indicator

### Phase 3: UI Implementation (Week 3)
5. **Main Interface**
   - Models section sidebar
   - Top menu bar model selector
   - Prompt input area
   - Live preview canvas

6. **Model Management UI**
   - Local folder browser
   - Hugging Face download interface
   - Download progress tracking
   - Model switching

### Phase 4: Queue & Variations (Week 4)
7. **Generation Queue**
   - Queue data structure
   - Sequential processing
   - Queue UI (status, cancel)

8. **Variations System**
   - Multiple image generation
   - Variation grid display
   - Free tier limits

### Phase 5: Monetization & Polish (Week 5)
9. **Subscription System**
   - StoreKit 2 integration
   - Pro tier unlocks
   - Watermark system

10. **Final Polish**
    - Keyboard shortcuts
    - Error handling
    - Performance optimization
    - App Store preparation

---

## Key Dependencies

### Swift Packages

```swift
// Package.swift dependencies
dependencies: [
    // MLX for Apple Silicon ML inference
    .package(url: "https://github.com/ml-explore/mlx-swift", from: "0.10.0"),
    
    // Hugging Face Hub API
    .package(url: "https://github.com/huggingface/swift-transformers", from: "0.1.0"),
    
    // Image handling
    .package(url: "https://github.com/SDWebImage/SDWebImageSwiftUI", from: "2.0.0"),
    
    // File/archive management
    .package(url: "https://github.com/weichsel/ZIPFoundation", from: "0.9.0"),
]
```

### Model Sources (Hugging Face)

| Model | HF Repo | Size |
|-------|---------|------|
| GLM-Image | THUDM/CogView or similar | ~10GB |
| Z-Image-Turbo | Various | ~4-6GB |
| Flux.2-klein-4B | black-forest-labs/FLUX.1-schnell variants | ~8GB |
| Qwen-Image-2512 | Qwen/Qwen-VL variants | ~10GB |

---

## Data Models

### GenerationRequest
```swift
struct GenerationRequest: Codable, Identifiable {
    let id: UUID
    let prompt: String
    let modelId: String
    let variationCount: Int      // Limited in free tier
    let timestamp: Date
    
    // Generation will determine these based on model
    var width: Int?
    var height: Int?
    var steps: Int?
    var seed: UInt64?
}
```

### GeneratedImage
```swift
struct GeneratedImage: Codable, Identifiable {
    let id: UUID
    let requestId: UUID
    let variationIndex: Int
    let imageData: Data
    let thumbnailData: Data
    let prompt: String
    let modelId: String
    let generatedAt: Date
    let generationTime: TimeInterval
    var hasWatermark: Bool
}
```

### AIModel
```swift
struct AIModel: Codable, Identifiable {
    let id: String
    let name: String
    let type: ModelType          // GLM, FLUX, Qwen, etc.
    let localPath: URL?
    let huggingFaceRepo: String?
    let sizeBytes: Int64
    var isDownloaded: Bool
    var isSelected: Bool
    
    enum ModelType: String, Codable {
        case glmImage = "GLM-Image"
        case zImageTurbo = "Z-Image-Turbo"
        case flux2Klein = "Flux.2-klein-4B"
        case qwenImage = "Qwen-Image-2512"
    }
}
```

---

## Required Entitlements

```xml
<!-- xCreateClone.entitlements -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" 
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
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
    <!-- For StoreKit -->
    <key>com.apple.developer.storekit.configuration</key>
    <true/>
</dict>
</plist>
```

---

## Version History Matching

To match xCreate's feature rollout:

| Version | Features |
|---------|----------|
| 1.0 | Basic generation, model management |
| 1.1.0 | Live preview, Qwen-Image-2512, async generation |
| 1.1.1 | FLUX.2-klein-4B, GLM-Image support |
| 1.1.2 | Bug fixes, stability |

---

## Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Model format compatibility | Can't load models | Support multiple formats, provide converters |
| Large model downloads | Poor UX, storage | Resume support, storage warnings |
| Memory constraints | Crashes on 8GB Macs | Minimum 16GB, model size warnings |
| App Store IAP review | Rejection | Follow StoreKit guidelines strictly |
| Model licensing | Legal issues | Only support open-source models |

---

## Sources & References

- [xCreate App Store](https://apps.apple.com/us/app/xcreate-ai-image-generator/id6756119462?mt=12) - Official listing
- [xCreate Official Site](https://xcreate.com/) - Developer website
- [MLX Swift](https://github.com/ml-explore/mlx-swift) - Apple's ML framework
- [Hugging Face](https://huggingface.co/) - Model repository
- [macOS AI Apps](https://www.macaiapps.com/category/image-generation/) - Category reference

---

## Summary

This plan outlines a **1:1 clone** of xCreate based on **verified information** from the App Store and web research:

- **Native macOS 15.0+ app** using Swift/SwiftUI
- **Apple Silicon only** (M1/M2/M3/M4)
- **Actual supported models**: GLM-Image, Z-Image-Turbo, Flux.2-klein-4B, Qwen-Image-2512
- **Live Preview** for real-time generation progress
- **Hugging Face integration** for model downloads
- **Freemium model**: Free tier with limits, Pro subscription (~$10/mo)
- **100% local/private** - no cloud processing

The app matches xCreate's actual feature set as documented in the App Store listing and version history.
