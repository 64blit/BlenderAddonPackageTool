# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the WebCrafter 2.0 Blender Add-on Development Framework - a comprehensive framework for developing multiple Blender addons in a single workspace. The framework supports both legacy addons and Blender 4.2+ extensions.

## Common Development Commands

### Testing an addon with hot-reload
```bash
python test.py                    # Test the default active addon (from main.py)
python test.py webcrafter_ai         # Test a specific addon
python test.py --disable_watch    # Test without hot-reload
```

### Creating a new addon
```bash
python create.py                  # Create addon with default name
python create.py my_new_addon     # Create addon with specific name
```

### Packaging an addon for release
```bash
python release.py                 # Package default addon as zip
python release.py webcrafter_ai --is_extension  # Package as Blender 4.2+ extension
python release.py --with_version  # Include version in filename
python release.py --with_timestamp # Include timestamp in filename
```

### Running lint/typecheck
Currently, no standard lint or typecheck commands are defined. If the user provides them, suggest adding them to this file for future reference.

### PyCharm Configuration

**IMPORTANT:** Do NOT run main.py directly - it's just a configuration file.

To run Blender from PyCharm:
1. Create a new Run Configuration in PyCharm
2. Set the script path to: `test.py` (not main.py)
3. Set working directory to the project root
4. Optional arguments:
   - `webcrafter` - to test the webcrafter addon specifically
   - `--disable_watch` - to disable hot-reload during development

Example PyCharm run configurations:
- Script: `test.py`, Args: `webcrafter` (test webcrafter addon with hot-reload)
- Script: `test.py`, Args: `sample_addon --disable_watch` (test sample addon without hot-reload)

## High-Level Architecture

### Project Structure
- **addons/** - Contains all addon projects. Each addon is a self-contained directory
- **common/** - Shared utilities used across all addons:
  - **class_loader/** - Auto-registration system for Blender classes
  - **i18n/** - Internationalization support
  - **io/** - File management utilities
  - **types/** - Framework base classes (ExpandableUi, decorators)
- **wheels/** - Python wheel dependencies
- **workflows/** - GitHub Actions for automated releases

### Key Framework Features

1. **Automatic Class Registration**: The framework automatically discovers and registers all Blender classes (Operators, Panels, PropertyGroups, etc.) in the correct dependency order. No manual registration needed.

2. **Hot-Reload Development**: When using `test.py`, changes to addon code are automatically reflected in Blender without restart.

3. **Multi-Addon Support**: Develop multiple addons in one workspace with shared utilities. The framework only packages necessary dependencies.

4. **Extension Support**: Can package addons as either legacy format or Blender 4.2+ extensions. The framework automatically converts absolute imports to relative imports for extensions.

5. **Built-in I18n**: Translation support through dictionary-based system in `addon/i18n/dictionary.py`.

6. **UI Extension**: Use `ExpandableUi` base class to extend existing Blender panels and menus.

### Configuration

Configuration is managed through:
1. **main.py** - Default configuration values
2. **config.ini** (optional) - Override main.py settings without modifying code

Key settings:
- `ACTIVE_ADDON` - Default addon for commands
- `BLENDER_EXE_PATH` - Path to Blender executable
- `IS_EXTENSION` - Whether to package as extension
- `DEFAULT_RELEASE_DIR` - Where packaged addons are saved

### Addon Development Guidelines

1. Each addon must have a `config.py` with `__addon_name__` defined
2. Place all registrable classes in directories with `__init__.py`
3. For extensions, use `__addon_name__` as `bl_idname` for preferences
4. Use declarative property definitions in `__init__.py`
5. Add translations to `i18n/dictionary.py`
6. Use `@reg_order` decorator to control registration order

### Working with Modified Files

Current git status shows:
- Modified: `create.py`
- Untracked: `addons/help/` and `addons/webcrafter/`

These appear to be work in progress. The framework is currently configured for "sample_addon" but can be switched to work with "webcrafter" or "help" addons.