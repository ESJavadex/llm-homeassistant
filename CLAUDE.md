# Claude Code Instructions

## Repository Context

**IMPORTANT: This is PRODUCTION code.**

This repository contains production-level code that is actively used. When making changes:

- Exercise extra caution and thoroughly test all modifications
- Prioritize stability and backward compatibility
- Avoid breaking changes unless absolutely necessary
- Consider the impact on existing users and deployments
- Follow best practices for security and error handling
- Document all significant changes clearly

## Project Overview

**LLM Homeassistant** - A Home Assistant custom integration that enables LLM-powered conversations with your smart home. Supports OpenAI and Azure OpenAI APIs.

### Key Features
- Multi-turn conversation support with persistent history
- Function/tool calling for Home Assistant services
- Context-aware prompts with dynamic sensor data
- Support for OpenAI and Azure OpenAI
- Image querying capabilities
- Custom function definitions (script, template, REST, scrape, SQLite, composite)

## Project Structure

```
custom_components/llm_homeassistant/
├── __init__.py          # Main agent logic (OpenAIAgent class)
├── const.py             # Constants and default prompt template
├── config_flow.py       # Configuration UI flow
├── services.py          # HA services (query_image, clear_history)
├── services.yaml        # Service descriptions
├── helpers.py           # Function executors and utilities
├── exceptions.py        # Custom exceptions
├── manifest.json        # Integration metadata and version
├── strings.json         # UI strings
└── translations/        # Localization files
```

## Architecture

### Conversation Flow
1. `async_process()` receives user input from Home Assistant
2. Retrieves/creates conversation history from `hass.data[DOMAIN][DATA_CHAT_LOGS]`
3. Generates system message with dynamic context (entities, weather, sensors, etc.)
4. Sends full message history to LLM API via `query()`
5. Handles function/tool calls recursively if needed
6. Saves updated history and returns response

### Key Components

**OpenAIAgent** (`__init__.py`)
- Main conversation agent extending `conversation.AbstractConversationAgent`
- Manages conversation history in `hass.data` for persistence
- Handles both legacy function calls and modern tool calls

**Function Executors** (`helpers.py`)
- `native`: Built-in functions (execute_service, get_history, get_energy, etc.)
- `script`: Execute Home Assistant scripts
- `template`: Jinja2 template execution
- `rest`: HTTP REST API calls
- `scrape`: Web scraping
- `composite`: Chained function execution
- `sqlite`: SQLite database queries

**System Prompt** (`const.py`)
- Dynamic Jinja2 template with:
  - Temporal context (time, day, sunrise/sunset)
  - Environmental data (weather, temperature, humidity sensors)
  - Entity states grouped by area
  - Battery levels, security sensors
  - Available scenes and automations

## Development Guidelines

### When Adding Features
1. Update `const.py` for new constants
2. Modify `__init__.py` for agent logic
3. Add services in `services.py` and `services.yaml`
4. Update `CHANGELOG.md` with changes
5. Bump version in `manifest.json`

### Conversation History
- Stored in: `hass.data[DOMAIN][DATA_CHAT_LOGS][conversation_id]`
- Format: List of message dicts with `role` and `content`
- Persists across agent reloads
- Each conversation is independent by `conversation_id`

### Logging
- Use `_LOGGER.info()` for important flow events
- Use `_LOGGER.debug()` for detailed debugging
- Use `_LOGGER.error()` for errors with context

### Testing Changes
1. Reload integration: Settings → Devices & Services → LLM Homeassistant → Reload
2. Check logs: Settings → System → Logs
3. Test conversation in HA Assist

## Version History
- **1.2.0**: Multi-turn conversation support, clear_history service
- **1.1.0**: Context-aware prompts, get_history function, dynamic sensors
- **1.0.5**: Initial Nabu personality, area-aware prompts
