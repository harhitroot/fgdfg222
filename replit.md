# Overview

This is a Telegram bot that acts as a bridge between Telegram messages and CLI scripts. The bot receives messages from Telegram users, executes corresponding CLI commands, and sends the output back to Telegram. It includes sophisticated monitoring capabilities for network speed tracking during downloads/uploads and comprehensive error handling to ensure reliable operation.

# User Preferences

Preferred communication style: Simple, everyday language.

# System Architecture

## Core Bot Architecture
- **Main Bot Class**: `TelegramBot` in `bot.js` serves as the central orchestrator, managing the Telegram API client and coordinating all bot functionality
- **Modular Component Design**: The architecture follows a separation of concerns pattern with specialized modules for different responsibilities
- **Event-Driven Processing**: Uses Telegram API event handlers to process incoming messages and execute corresponding CLI commands

## Message Processing Pipeline
- **Rate Limiting**: `RateLimiter` class implements a 5-second delay between messages to prevent Telegram API rate limit violations
- **Duplicate Prevention**: Message tracking system using Sets and timestamps to prevent spam and duplicate outputs
- **Buffer Management**: Message buffering system to queue and process messages sequentially

## Monitoring and Analytics
- **Speed Monitoring**: `SpeedMonitor` class tracks real-time network usage during CLI operations by reading from `/sys/class/net/` interface statistics
- **Performance Tracking**: Calculates download/upload speeds and provides periodic updates every 2 minutes during active operations
- **Process Management**: Active process tracking using Maps to monitor running CLI operations

## Error Handling and Resilience
- **Comprehensive Error Handler**: `ErrorHandler` class wraps all event handlers and implements retry logic with exponential backoff
- **Connection Recovery**: Automatic reconnection handling for Telegram API disconnections
- **Process Error Handling**: Specific handling for system errors like the `-122` write error to prevent bot crashes

## Keep-Alive and Monitoring
- **HTTP Server**: `KeepAlive` class runs an Express server on port 5000 for uptime monitoring and health checks
- **Status Endpoints**: Multiple endpoints (`/`, `/health`, `/status`) provide different levels of system information
- **Uptime Tracking**: Tracks bot uptime, request counts, and system resource usage

## CLI Integration
- **Process Spawning**: Uses Node.js `child_process.spawn()` to execute CLI commands
- **Output Streaming**: Real-time capture and forwarding of CLI stdout/stderr to Telegram
- **Process Lifecycle Management**: Tracks active processes and handles termination gracefully

# External Dependencies

## Telegram API Integration
- **telegram**: Primary library for Telegram Bot API interaction and session management
- **StringSession**: Handles persistent Telegram session storage via environment variables

## Web Server
- **express**: Lightweight HTTP server for health checks and uptime monitoring endpoints

## System Integration
- **Node.js Built-ins**: 
  - `child_process` for CLI command execution
  - `fs` for file system operations and network interface monitoring
  - `http` for web server functionality

## Environment Configuration
- **API_ID**: Telegram API identifier
- **API_HASH**: Telegram API hash key  
- **SESSION_STRING**: Persistent Telegram session data

## Network Monitoring
- **System Files**: Reads network statistics from `/sys/class/net/` for real-time speed calculation
- **Interface Detection**: Automatically detects available network interfaces (eth0, wlan0, ens33, etc.)