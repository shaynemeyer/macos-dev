# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **documentation-focused repository** for macOS development resources. It contains comprehensive guides, deep dives, and reference materials covering macOS system architecture, APIs, and development patterns.

## Repository Structure

- **`Complete_macOS_Developers_Guide/`** - Comprehensive guide covering macOS architecture, frameworks, file system, process/memory management, graphics, security, and best practices. Includes architecture diagrams (images/).

- **`MacOS-API/`** - Complete guide to the macOS API ecosystem including:
  - Full framework stack overview (SwiftUI, AppKit, Foundation, Core Graphics, Metal, etc.)
  - Application type recommendations (utilities, media apps, creative tools, games, etc.)
  - Framework layer deep-dives with code examples
  - Guidance on choosing the right framework stack

- **`deep-dives/`** - Technical deep-dives on specific topics:
  - `macOS_Hybrid_Process_Model_Deep_Dive.md` - Detailed explanation of Mach tasks vs BSD processes, IPC, threading, and kernel architecture

## Content Architecture

### Key Technical Topics Covered

1. **macOS Architecture**
   - XNU Kernel (Mach + BSD hybrid)
   - Darwin foundation layer
   - System layering (Application Layer → Frameworks → System Services → Kernel)

2. **Framework Ecosystem**
   - UI Frameworks: SwiftUI (modern/declarative) vs AppKit (traditional/imperative)
   - Foundation: Core data types, collections, file system, networking
   - Media/Graphics: Core Graphics, Metal, AVFoundation, Core Image
   - Data Management: Core Data, Combine
   - System Services: Network Framework, Security, FileManager

3. **Process Model**
   - Hybrid Mach task + BSD process architecture
   - Thread management and scheduling
   - Virtual memory system
   - IPC via Mach ports
   - XPC services

4. **Security**
   - App Sandbox and entitlements
   - Code signing and notarization
   - System Integrity Protection (SIP)
   - Privacy permissions (TCC)

5. **Development Patterns**
   - Automatic Reference Counting (ARC)
   - Memory management and optimization
   - Grand Central Dispatch (GCD)
   - Modern Swift concurrency (async/await, actors)

## Working with Documentation Files

### File Format
All documentation is in Markdown format with:
- Comprehensive tables of contents
- Code examples in Swift, Objective-C, and C
- Conceptual diagrams (text-based and PNG images)
- Structured sections with clear hierarchies

### When Making Updates

1. **Maintain Technical Accuracy**: This documentation covers deep system-level concepts. Verify technical details against Apple's official documentation when making changes.

2. **Preserve Structure**: Each guide follows a logical progression from high-level concepts to detailed implementation. Maintain this flow when editing.

3. **Code Examples**: Examples should be:
   - Syntactically correct
   - Runnable or clear pseudo-code
   - Well-commented for clarity
   - Using modern APIs where appropriate

4. **Cross-References**: Documents reference each other and build on concepts. When adding content, consider what foundational knowledge is needed and where else the concept might be relevant.

5. **Consistency**: Use consistent terminology:
   - "macOS" (not "MacOS" or "Mac OS")
   - "Mach task" vs "BSD process" (distinct concepts)
   - Framework names match Apple's capitalization (SwiftUI, AppKit, AVFoundation)

## Repository Purpose

This repository serves as a learning resource and reference guide for:
- Developers new to macOS development
- Experienced developers needing deep system-level understanding
- Anyone building native macOS applications
- Understanding the relationship between different macOS frameworks and layers

The documentation emphasizes understanding not just "how" but "why" - explaining architectural decisions and the reasoning behind macOS's design.
