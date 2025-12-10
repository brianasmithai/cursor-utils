# Cursor Utils

A collection of useful rules, commands, snippets, and templates for [Cursor](https://cursor.com).

![Version](https://img.shields.io/badge/version-1.0.0-blue)

## Overview

This repository serves as a central place to store and share reusable Cursor configurations and code artifacts that can be applied across a variety of projects.

## Structure

```
cursor-utils/
├── rules/          # Core Cursor rules applicable across projects
├── commands/       # Custom Cursor commands
├── snippets/       # Reusable code snippets and examples
└── templates/      # Cursor settings templates
```

## Usage

### Rules

Rules are placed in your project's `.cursor/rules/` directory. To use a rule from this collection:

1. Copy the desired `.mdc` file from `rules/` to your project's `.cursor/rules/` directory
2. The rule will automatically be applied based on its configuration

### Commands

Commands can be added to Cursor via the command palette or settings. Each command file includes installation instructions in its header.

### Snippets

Code snippets are organized for easy copy-paste into your projects. Each snippet includes context about its use case and any dependencies.

### Templates

Settings templates provide pre-configured Cursor settings for specific workflows or project types. Copy the relevant settings to your Cursor configuration.

## Contributing

Contributions are welcome! When adding new content:

- **Rules**: Include a clear description and specify which file types/contexts the rule applies to
- **Commands**: Document what the command does and any prerequisites
- **Snippets**: Add comments explaining the use case and any required dependencies
- **Templates**: Describe the intended workflow or project type

## License

MIT License - see [LICENSE](LICENSE) for details.
