# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is the **ALTSYNC 开发团队** (ALTSYNC Development Team) rule repository. It contains development standards, workflows, and guidelines for building products with the following tech stack:

- **Backend**: Java 11, Spring Boot, MyBatis-Plus, MySQL 5.7
- **Admin Frontend**: Vue 2.6, Node.js 18, Yarn, SCSS
- **Mobile App**: Flutter (use FVM for version management)

## Project Architecture

### Backend Structure
The backend follows a modular architecture with four core modules:

1. **Common**: Data models and structures (no business logic)
2. **Service**: Business logic, utilities, and Spring Boot components
3. **Admin**: Management APIs and controllers
4. **User**: User-facing APIs and controllers

**Dependencies**: Service → Common; Admin/User → Service + Common

### Flutter Frontend Structure
- `lib/common`: Global styles (colors, fonts, themes)
- `lib/network`: API communication functions
- `lib/pages`: Application pages/views
- `lib/service`: Business logic and state management
- `lib/utils`: Utility functions
- `lib/widgets`: Reusable UI components

## Git Workflow

### Branch Naming Convention
```
Format: [type]/[description]
Types: feature, bugfix, update, refactor
Examples: feature/userpage, bugfix/page_not_found, update/to-java-21
```

### Development Cycle
1. Create branch from `main`: `git checkout -b feature/my-feature`
   - Always sync: `git pull origin main` before creating branch
2. Develop with single theme per branch
3. Push and create Pull Request
4. Core member reviews and merges PR

### Commit Message Format
For direct commits (core members only, emergency fixes only):
- Updates requiring release: `feat: description (release)` or `fix: description (alpha)`
- Non-release updates: `feat: description` or `fix: description`

### Direct Commit Policy
- Only core members can directly commit
- Only emergency fixes allowed
- App endpoints must follow PR format when requiring builds

## Code Style Conventions

### Java
- **IDE**: IntelliJ IDEA preferred
- **Indentation**: 4 spaces, K&R brace style
- **Variables**: Use `var` for local types, `final` for constants
- **Annotations**: Use Lombok (`@Data`, `@Slf4j`), Spring Boot annotations
- **Database**: Avoid N+1 queries; use XML for joins over MyBatis-Plus Join
- **Controllers**: Use `@RestController`, `@GetMapping`/`@PostMapping`, wrap returns in `CommonResult`
- **Method signatures**: Must declare `throws` for potential exceptions
- **Nesting**: Early returns preferred over deep nesting

### JavaScript/Node.js
- **IDE**: IntelliJ WebStorm preferred
- **Variables**: Never use `var`; use `let` (mutable) and `const` (immutable)
- **Indentation**: 4 spaces
- **Formatting**: ESLint allowed; avoid Prettier
- **Functions**: Use named functions, arrow functions only for callbacks/anonymous
- **Avoid**: "creative" JavaScript patterns, dynamic property assignment after function definition

### Flutter/Dart
- **IDE**: Android Studio preferred
- **Version Management**: Must use FVM
- **Indentation**: 2 spaces
- **Text**: Use `FastWidget.text()` for unified font
- **Colors**: Use `CommonStyles` theme colors only
- **Page Skeleton**: Use `AppScaffold` (supports background, loading, error handling)
- **Refresh/Load**: Use EasyRefresh with styles from `common_list.dart`
- **Empty States**: Use `EmptyWidget`
- **Routing**: Use GetX (`Get.toReloadable()`, `Get.offReloadable()`)
- **Logging**: Use `Logger` from `util.dart`, not `print`
- **Caching**: Use `SharedPreferences` via `DataCacheService`
- **Images**: Use `AppWidget.getNetworkImage` (includes caching, error handling)
- **Responsive**: Use ScreenUtil with design size 375×800
- **Spacing**: Use `AppMargin`/`AppPadding` from `common_styles.dart`
- **Border Radius**: Use `AppBorderRadius`/`AppRadius` from `common_styles.dart`

### General Rules (All Languages)
- **Naming**:
  - Classes: PascalCase
  - Functions/Methods: camelCase, verb-first
  - Constants: UPPER_SNAKE_CASE
  - Variables: meaningful names, avoid single letters
- **Function length**: Max 30-40 lines
- **Parameter count**: Max 3 parameters, encapsulate if more
- **Error handling**: Log errors with stack traces
- **Security**: Never hardcode sensitive data (passwords, API keys)

## Design System

### Color Palette
- **Primary**: `#F3C60E` (Yellow) - Brand recognition, primary buttons, active states
- **Accent**: `#FE7001` (Orange) - Secondary buttons, hover states, alerts
- **Background**: `#F5F5F5` (Light gray) - Canvas background
- **Component**: `#FFFFFF` (White) - Component backgrounds
- **Text**: `#333333` (Primary), `#666666` (Secondary), `#999999` (Hint)

### Typography
- **Font**: 思源黑体 (Noto Sans CJK SC / Source Han Sans SC)
- **Primary text**: 16px / 24px line height
- **Headings**: H1 (32px), H2 (30px), H3 (26px), H4 (24px)
- **Buttons**: Large (18px), Regular (16px), Small (14px)

### Spacing & Layout
- **Grid system**: 2px or 4px alignment
- **Border radius**: 16px standard
- **Page margins**: 12px horizontal/vertical (mobile), 12px/16px (tablet/desktop)
- **Container padding**: 12px or 16px

## Deployment

### Backend
- Use Docker Compose
- Config in `./compose` folder
- Run with `./up.sh`
- Database init files in `./compose/sql_init`

## Team Communication Guidelines

### Core Principles
- **Transparency**: Report problems publicly in group chat, never hide issues
- **Interns**: Must ask questions in group chat, never private message
- **Daily Standup**: 1:00 PM daily, 15 minutes max
- **Task handoff**: When unable to continue, document: current state, code locations, unresolved issues, next steps

### Terminology Clarifications
- Classes and structs are treated as the same concept
- Methods are called "functions" or "member functions"
- Use "field" or "property" for class members with getters/setters
- Database-related classes are called "models"
- HTTP response results use "Res" naming, not "ResultVo"
- Avoid naming conventions like POJO, BO, VO

### Git Notes for Direct Commits
Only core members may directly commit, and only for emergency fixes. When direct commits are necessary:
- For build releases: Follow PR format (`feat: add login (release)` or `fix: bug (alpha)`)
- For non-releases: Simple format (`feat: add login`)
