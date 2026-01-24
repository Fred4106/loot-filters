# CLAUDE.md - Loot Filters Plugin

## Project Overview

Loot Filters is a RuneLite plugin (OSRS game client extension) written in Java. It's an enhanced version of the built-in Ground Items plugin with support for scriptable "loot filters" using a custom language (RS2F) that allows fine-grained control over how ground items are displayed.

## Tech Stack

- **Language**: Java 11+
- **Build System**: Gradle 8.10 (use wrapper `./gradlew`)
- **DI Framework**: Google Guice (via RuneLite)
- **JSON**: Google GSON
- **Code Generation**: Lombok
- **Testing**: JUnit 4.12
- **HTTP Client**: OkHttp3

## Build Commands

```bash
# Build project (compile + test)
./gradlew build

# Compile only
./gradlew classes

# Create distributable JAR with all dependencies
./gradlew shadowJar
# Output: build/libs/loot-filters-plugin-<version>-all.jar

# Run tests
./gradlew test

# Clean build artifacts
./gradlew clean
```

## Directory Structure

```
src/main/java/com/lootfilters/
├── LootFiltersPlugin.java    # Main plugin entry point
├── LootFiltersConfig.java    # Configuration interface
├── LootFiltersOverlay.java   # Game world rendering
├── LootFiltersPanel.java     # Side panel UI
├── LootFilter.java           # Filter model & compilation
├── FilterRule.java           # Individual filter rules
├── DisplayConfig.java        # Display configuration
├── ast/                      # Abstract Syntax Tree for RS2F
│   └── leaf/                 # Condition implementations
├── lang/                     # RS2F custom language
│   ├── Lexer.java            # Tokenization
│   ├── Parser.java           # Parsing
│   └── Preprocessor.java     # Macro preprocessing
├── model/                    # Data models & enums
├── migration/                # Version migrations
└── util/                     # Utility classes

src/main/resources/com/lootfilters/
├── scripts/preamble.rs2f     # Filter language preamble
└── icons/                    # UI icons (PNG)

src/test/java/com/lootfilters/
├── LootFiltersPluginTest.java
└── debug/                    # Debug plugin for testing
```

## Key Files to Read First

1. `LootFiltersPlugin.java` - Plugin lifecycle, event handling
2. `LootFilter.java` - Filter compilation from RS2F sources
3. `lang/Lexer.java` - RS2F tokenizer
4. `lang/Parser.java` - RS2F parser
5. `FilterRule.java` - Filter rule evaluation
6. `LootFiltersOverlay.java` - Rendering logic

## Code Patterns

### Event Subscription (RuneLite pattern)
```java
@Subscribe
public void onItemSpawned(ItemSpawned event) { ... }
```

### Dependency Injection
```java
@Inject private Client client;
@Inject private ConfigManager configManager;
```

### Configuration Provision
```java
@Provides
LootFiltersConfig provideConfig(ConfigManager configManager) { ... }
```

### Filter Compilation & Evaluation
```java
LootFilter filter = LootFilter.fromSources(sourceMap);
DisplayConfig display = filter.findMatch(plugin, item);
```

## RS2F Filter Language

The plugin uses a custom DSL (RS2F) for defining filters:

```
#define COLOR_NAME "value"   // Macros

rule {
  if (condition) {
    property = value;        // Display property assignment
  }
}
```

Conditions include: ItemId, ItemName, ItemValue, ItemQuantity, Area, Ownership, and more (see `ast/leaf/` directory).

## Configuration

Config is managed via RuneLite's ConfigManager with group "loot-filters". Key sections:
- General, Hotkey, Display Settings, Global Filters, Ownership Filter, Item Value Rules, Advanced

## Plugin Manifest

Located in `runelite-plugin.properties`:
- Entry point: `com.lootfilters.LootFiltersPlugin`
- Conflicts with built-in "Ground Items" plugin

## Testing

Run the test class `LootFiltersPluginTest` which loads both the main plugin and debug plugin into RuneLite for integration testing.

## External Resources

- GitHub: https://github.com/riktenx/loot-filters
- User Guide: https://github.com/riktenx/loot-filters/blob/userguide/README.md
- Web Filter Editor: https://filterscape.xyz/
- Discord: https://discord.gg/ESbA28wPnt
