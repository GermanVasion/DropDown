# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

DropDown is an iOS UIKit library (Swift 5) providing a Material Design-inspired drop down menu as a replacement for `UIPickerView`. It supports CocoaPods, Carthage, and Swift Package Manager. The library is iOS-only (`#if os(iOS)` guards throughout).

## Build Commands

```bash
# Build the framework
xcodebuild build -scheme DropDown -destination 'platform=iOS Simulator,name=iPhone 16'

# Build the demo app
xcodebuild build -scheme Demo -destination 'platform=iOS Simulator,name=iPhone 16'

# Run tests
xcodebuild test -scheme DropDown -destination 'platform=iOS Simulator,name=iPhone 16'
```

## Architecture

The library has a small, focused codebase with two main layers:

**Core (`DropDown/src/`):**
- `DropDown.swift` — The main class. A `UIView` subclass that manages the entire drop down lifecycle: layout computation, show/hide animations, UITableView data source/delegate, keyboard awareness, and multi-selection support. It adds itself to the app's visible `UIWindow` when shown.
- `DropDownCell.swift` — Base `UITableViewCell` subclass with an `optionLabel` IBOutlet. Subclass this for custom cells via XIB.
- `DropDown+Appearance.swift` — `setupDefaultAppearance()` helper using UIKit's `UIAppearance` proxy.

**Helpers (`DropDown/helpers/`):**
- `DPDConstants.swift` — All default values (colors, fonts, dimensions, animation params) in the `DPDConstant` struct.
- `DPDKeyboardListener.swift` — Singleton that tracks keyboard visibility/frame for layout adjustments.
- `DPDUIView+Extension.swift` — Internal extensions for Auto Layout helpers and `UIWindow.visibleWindow()`.

**Resources (`DropDown/resources/`):**
- `DropDownCell.xib` — Default cell XIB. When using SPM, loaded via `Bundle.module`; otherwise via `Bundle(for: DropDownCell.self)`.

**Key design patterns:**
- The `AnchorView` protocol allows attaching to either `UIView` or `UIBarButtonItem`.
- Layout is computed dynamically in `computeLayout()` which determines direction (top/bottom/any), position, and visible height based on available screen space and keyboard state.
- Appearance properties use `@objc dynamic` for UIAppearance compatibility and trigger `reloadAllComponents()` on change.
- Only one `DropDown` can be visible at a time (tracked via `DropDown.VisibleDropDown`).

## Custom Cells

To use custom cells: subclass `DropDownCell`, create a XIB linking `optionLabel`, set `dropDown.cellNib`, and configure via `customCellConfiguration` closure. See `Demo/MyCell.swift` and `Demo/MyCell.xib` for a working example.
