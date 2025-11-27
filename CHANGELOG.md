# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.0] - 2025-11-27

### Added

#### Multi-Turn Conversation Support
- **Persistent conversation history**: Messages are now stored in `hass.data` instead of agent instance
  - History persists across agent reloads
  - Each conversation maintains independent context via `conversation_id`
  - Full message history sent to LLM for proper context awareness
- **clear_history service**: New service to reset conversation state
  - Clear all conversations: `service: llm_homeassistant.clear_history`
  - Clear specific conversation: `service: llm_homeassistant.clear_history` with `conversation_id`

### Improved
- Added detailed logging for conversation flow debugging
- Better handling of conversation_id for multi-turn support

---

## [1.1.0] - 2025-01-25

### Added

#### Default Functions
- **get_history**: Now available as a default function alongside execute_services
  - Retrieves historical data of specified entities
  - Automatically formats timestamps to local time
  - Accepts entity_ids, start_time, and end_time parameters

#### Comprehensive Context-Aware Prompt
- **Temporal Context**: Day of week, time period (morning/afternoon/night), sunrise/sunset times
- **Environmental Conditions**:
  - Weather integration (temperature, humidity, conditions)
  - All temperature sensors with area information
  - All humidity sensors with area information
- **Presence & Modes**:
  - Person entities tracking (home/away status)
  - House mode detection (auto-discovers input_select modes)
  - Special modes tracking (guest mode, vacation mode, etc.)
- **Battery Monitoring**:
  - Highlights batteries below 20% with warning
  - Shows batteries below 50%
  - Fully dynamic for any battery-powered device
- **Security Sensors**:
  - Smoke detector status with area
  - Door/window sensors with open/closed state and area
  - Motion and occupancy sensors with detection state and area
- **Energy Consumption**:
  - Active power usage monitoring (shows devices >10W)
  - Includes area information for context
- **Scenes & Automations**:
  - Lists all available scenes
  - Shows count and names of active automations
- **Area Organization**:
  - All devices grouped by area
  - Current area detection
  - Complete area list with IDs and names

#### Dynamic Device Attributes
- Automatic attribute extraction based on device type:
  - **Lights**: brightness, color_mode, rgb_color
  - **Media Players**: source, volume_level, media_title
  - **Climate**: temperature, current_temperature, hvac_mode
  - **Cameras**: motion_detection
  - **Sensors**: unit_of_measurement
- Attributes only included if they exist on the device
- Fully scalable with regex pattern matching

### Improved
- **Error Handling**: Conversation history now preserved even when errors occur
  - Saves partial message history on OpenAIError
  - Saves partial message history on HomeAssistantError
  - Appends assistant function call messages to maintain context
- **Prompt Optimization**: Removed redundant rules, streamlined communication style

### Technical Details
- All context sections are 100% dynamic and scalable
- Auto-discovery of entities by device_class
- Conditional rendering based on entity existence
- No hardcoded entity IDs - adapts to any Home Assistant setup
- Smart filtering (batteries <50%, power >10W) to reduce noise

## [1.0.5] - Previous Release

- Nabu Spanish home assistant personality with enhanced config
- Area-aware prompt with device organization
- Custom Nabu personality for Javi and Gabriela's home
- Execute_services as default function

---

[1.2.0]: https://github.com/esjavadex/llm-homeassistant/releases/tag/v1.2.0
[1.1.0]: https://github.com/esjavadex/llm-homeassistant/releases/tag/v1.1.0
[1.0.5]: https://github.com/esjavadex/llm-homeassistant/releases/tag/v1.0.5
