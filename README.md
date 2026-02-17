# phira-mp-server (C++20 Implementation)

A complete, feature-rich multiplayer game server implementation in C++20, providing full compatibility with the phira-mp protocol and extending it with a powerful Lua plugin system and RESTful HTTP API.

**中文说明**：这是一个用C++20实现的多人在线游戏服务器，完全兼容phira-mp协议，并扩展了强大的Lua插件系统和RESTful HTTP API。

## 📋 Features / 功能特性

### Core Server / 核心服务器
- **Binary Protocol Support** - Full implementation of the phira-mp binary command protocol with 16 command types
- **Multi-threaded Architecture** - Efficient session handling with dedicated connection threads
- **Room Management** - Complete room system with creation, joining, and management capabilities
- **User Session Management** - Robust user connection handling with UUID-based identification

### Plugin System / 插件系统
- **Lua 5.4 Integration** - Dynamic plugin loading with Lua scripting support
- **Event Hooks** - Comprehensive hook system (`on_enable`, `on_disable`, `on_user_join`, `on_before_command`, etc.)
- **Plugin API** - Full Lua API exposed through global `phira` table for server manipulation
- **Hot Reload** - Plugins can be enabled/disabled at runtime

### HTTP API / HTTP接口
- **RESTful Endpoints** - Complete HTTP API on port 61234 (configurable)
- **Public Endpoints** - `/room`, `/stats` for client applications
- **Replay System** - `/replay/*` endpoints for replay authentication and retrieval
- **Admin Interface** - Full administrative control via HTTP (`/admin/*`)
- **CORS Support** - Cross-origin requests enabled for web clients

### Replay System / 回放系统
- **Recording & Storage** - Automatic replay recording during gameplay
- **File-based Storage** - Replays stored in `replays/` directory as binary files
- **Metadata Management** - Replay information tracking (player, song, timestamp, size)
- **HTTP Access** - Replays accessible via authenticated HTTP endpoints

### Admin & Management / 管理员功能
- **Token-based Authentication** - Simple admin token system (awaiting HSN integration)
- **Server Configuration** - Dynamic configuration via HTTP API
- **Room Controls** - Room creation, banning, and management
- **User Management** - User banning, disconnection, and monitoring
- **Broadcast System** - Server-wide message broadcasting

### Interactive CLI / 交互式命令行界面
- **Real-time Command Input** - Interactive console for server administration
- **Server Status Monitoring** - View connected users, active rooms, and server stats
- **Room Management** - List, disband, and configure rooms directly
- **User Management** - Kick, ban, unban, and view user details
- **Plugin Control** - Hot-reload plugins without restarting server
- **Broadcast Messaging** - Send messages to all rooms or specific rooms

## 🚀 Quick Start / 快速开始

### Prerequisites / 环境要求
- **g++ 13+** with C++20 support
- **Lua 5.4** development libraries
- **libuuid** for UUID generation
- **pthread** for threading support

**Ubuntu/Debian:**
```bash
sudo apt-get update
sudo apt-get install -y g++ make liblua5.4-dev uuid-dev
```

**CentOS/RHEL:**
```bash
sudo yum install -y gcc-c++ make lua-devel libuuid-devel
```

### Building / 编译
```bash
# Clone the repository (if not already)
git clone <repository-url>
cd cpp-phira-mp

# Build the server
make clean
make
```

This will produce the `phira-mp-server` binary.

### Running / 运行
```bash
# Start with default port (12346 for game protocol, 61234 for HTTP)
./phira-mp-server

# Start with custom game port
./phira-mp-server --port 8080

# Start in background
nohup ./phira-mp-server > server.log 2>&1 &
```

The server will:
1. Load configuration from `server_config.yml` (if exists)
2. Scan and load plugins from `plugins/` directory
3. Start game server on specified port (default: 12346)
4. Start HTTP API server on port 61234
5. Start interactive CLI for console administration
6. Begin accepting connections

### Interactive CLI Usage / 交互式CLI使用

When you start the server, you'll see a CLI prompt appear:
```
=== Phira MP Server CLI ===
Type 'help' for available commands
==============================

> 
```

#### Available Commands / 可用命令

**General Commands:**
- `help`, `?` - Show help message
- `status`, `info` - Show server status
- `stop`, `shutdown` - Show shutdown instructions

**Room Management:**
- `list`, `rooms` - List all active rooms
- `disband <roomId>` - Disband a room
- `maxusers <roomId> <count>` - Set room max users (1-64)
- `roomcreation <on|off|status>` - Control room creation

**User Management:**
- `users` - List all online users
- `user <userId>` - Show user details
- `kick <userId>` - Kick a user from the server
- `ban <userId>` - Ban a user from the server
- `unban <userId>` - Unban a user
- `banlist` - Show banned users list

**Communication:**
- `broadcast <msg>` - Broadcast message to all rooms
- `say <msg>` - Alias for broadcast
- `roomsay <roomId> <msg>` - Send message to specific room

**Server Management:**
- `reload` - Reload all plugins
- `replay <on|off|status>` - Control replay recording

#### Command Examples / 命令示例

```bash
# View server status
> status

# List all active rooms
> list

# List all online users
> users

# Kick a user
> kick 12345

# Broadcast server message
> broadcast Server will restart in 10 minutes

# Send message to specific room
> roomsay room1 Please follow the game rules

# Disband a room
> disband room1

# Reload all plugins
> reload

# Control replay recording
> replay on
> replay status
```

**Note**: For advanced commands (contest mode, IP blacklist management, etc.), use the HTTP API on port 61234.

## ⚙️ Configuration / 配置

### Server Configuration / 服务器配置
Create `server_config.yml` in the working directory:

```yaml
# Game server port
port: 12346

# HTTP API server port
http_port: 61234

# Admin token for API access (simplified, awaiting HSN integration)
admin_token: "your-secure-admin-token-here"

# Replay system enabled
replay_enabled: true

# Room creation enabled
room_creation_enabled: true

# Monitor IDs (game-specific)
monitors:
  - 2
  - 42
```

If no configuration file is found, default values will be used.

### Plugin Configuration / 插件配置
Each plugin in the `plugins/` directory requires:
- `plugin.json` - Plugin metadata (id, name, version, author, enabled flag)
- `init.lua` - Main plugin script

Example `plugin.json`:
```json
{
    "id": "my-plugin",
    "name": "My Plugin",
    "version": "1.0.0",
    "description": "Plugin description",
    "author": "Your Name",
    "enabled": true,
    "dependencies": []
}
```

## 🔌 Plugin System / 插件系统

### Available Plugins / 内置插件
The server comes with 5 built-in plugins:

1. **http-admin-api** - HTTP Admin API endpoints
2. **replay-recorder** - Game replay recording and management
3. **admin-commands** - Administrative command system
4. **advanced-room-management** - Enhanced room controls and features
5. **virtual-room** - Virtual room creation and management

### Plugin Development / 插件开发
Plugins are written in Lua and have access to the server through the `phira` global table.

**For complete plugin development documentation, see [PLUGIN_DEVELOPMENT.md](PLUGIN_DEVELOPMENT.md)**

The plugin system provides:
- **40+ Server Management APIs** - Full control over users, rooms, messages, bans, contests, and server state
- **Comprehensive Event Hooks** - Real-time notifications for user joins/leaves, room creation/destruction, kicks, bans, and command filtering
- **HTTP Route Registration** - Extend the HTTP API with custom endpoints
- **Thread-Safe Operations** - All APIs are properly synchronized for concurrent access

#### New Event Hooks (v2.0+)
- `on_user_kick(user, room, reason)` - When a user is kicked
- `on_user_ban(user, reason, duration)` - When a user is banned  
- `on_user_unban(user_id)` - When a user is unbanned
- `on_room_create(room)` - When a room is created
- `on_room_destroy(room)` - When a room is destroyed

#### New Management APIs (v2.0+)
```lua
-- User management
phira.kick_user(user_id, preserve_room)
phira.ban_user(user_id)
phira.unban_user(user_id)
phira.is_user_banned(user_id)
phira.get_banned_users()

-- Room management  
phira.disband_room(room_id)
phira.set_max_users(room_id, max_users)
phira.get_room_max_users(room_id)

-- Messaging
phira.broadcast_message(message)
phira.roomsay_message(room_id, message)

-- Server control
phira.shutdown_server()
phira.reload_plugins()

-- Status queries
phira.get_connected_user_count()
phira.get_active_room_count()
phira.get_room_list()

-- IP blacklist management
phira.add_ip_to_blacklist(ip, is_admin)
phira.remove_ip_from_blacklist(ip, is_admin)
phira.is_ip_banned(ip)

-- Contest management
phira.enable_contest(room_id, manual_start, auto_disband)
phira.disable_contest(room_id)
phira.start_contest(room_id, force)
```

See the complete API reference in [PLUGIN_DEVELOPMENT.md](PLUGIN_DEVELOPMENT.md) for detailed documentation and examples.

## 🌐 HTTP API Reference / HTTP接口文档

### Public Endpoints / 公共端点

#### `GET /room`
Get list of all available rooms.

**Response:**
```json
{
    "rooms": [
        {
            "id": "room-uuid",
            "name": "Room Name",
            "players": 3,
            "maxPlayers": 8,
            "status": "waiting"
        }
    ],
    "total": 1
}
```

#### `GET /stats`
Get server statistics.

**Response:**
```json
{
    "users": 5,
    "sessions": 5,
    "rooms": 2,
    "uptime": 3600,
    "version": "1.0.0"
}
```

### Replay Endpoints / 回放端点

#### `POST /replay/auth`
Authenticate for replay access (stub implementation).

**Request:**
```json
{
    "token": "user-token"
}
```

**Response:**
```json
{
    "ok": true,
    "userId": 12345,
    "charts": [],
    "sessionToken": "mock_session_token",
    "expiresAt": 1678886400
}
```

#### `GET /replay/download`
Download a replay file (requires authentication).

**Query Parameters:**
- `id` - Replay ID
- `token` - Session token

#### `POST /replay/delete`
Delete a replay (stub implementation).

### Admin Endpoints / 管理员端点
All admin endpoints require authentication via `admin_token` parameter (query string or request body).

#### Authentication Methods / 认证方式
1. **Query Parameter**: `?admin_token=your-token`
2. **Request Body**: `{"admin_token": "your-token"}`

#### Available Admin Endpoints / 可用管理端点

**Configuration Management:**
- `GET /admin/replay/config` - Get replay configuration
- `POST /admin/replay/config` - Update replay configuration
- `GET /admin/room-creation/config` - Get room creation config
- `POST /admin/room-creation/config` - Update room creation config

**Room Management:**
- `GET /admin/rooms` - List all rooms with details
- `POST /admin/ban/room` - Ban a room
- `POST /admin/rooms/max_users` - Set room max users
- `POST /admin/rooms/disband` - Disband a room
- `POST /admin/rooms/chat` - Send room chat message

**User Management:**
- `GET /admin/users/info` - Get user information
- `POST /admin/ban/user` - Ban a user
- `POST /admin/users/disconnect` - Disconnect a user
- `POST /admin/users/move` - Move user to different room

**Server Controls:**
- `POST /admin/broadcast` - Broadcast message to all users
- `GET /admin/ip-blacklist` - Get IP blacklist
- `POST /admin/ip-blacklist/remove` - Remove IP from blacklist
- `POST /admin/ip-blacklist/clear` - Clear IP blacklist
- `GET /admin/log-rate` - Get log rate configuration

**OTP Endpoints (Simplified - Awaiting HSN Integration):**
- `POST /admin/otp/request` - Request OTP (returns dummy session)
- `POST /admin/otp/verify` - Verify OTP (accepts "123456" for testing)

## 🔐 Authentication & Security / 认证与安全

### Current Implementation / 当前实现
The current authentication system is simplified and awaits integration with the HSN (HyperSynapseNetwork) unified user system.

**Admin Authentication:**
- Single `admin_token` in configuration
- Token passed via query parameter or request body
- No OTP/IP banning in current simplified version

**Future HSN Integration:**
- Unified user accounts across services
- OTP-based admin authentication
- IP-based rate limiting and banning
- Session management

### Security Notes / 安全说明
1. **Production Use**: The current simplified auth is for development/testing only
2. **Token Security**: Keep `admin_token` secure and rotate regularly
3. **Network Security**: Run behind firewall/reverse proxy in production
4. **HTTPS**: For production, use HTTPS termination at reverse proxy

## 📊 Replay System / 回放系统

### How It Works / 工作原理
1. **Recording**: During gameplay, the server records game events
2. **Storage**: Replays are saved as binary files in `replays/` directory
3. **Metadata**: Replay information stored in server memory for quick access
4. **Retrieval**: Replays accessible via authenticated HTTP endpoints

### File Structure / 文件结构
```
replays/
├── replay_1234567890_1678886400.bin
├── replay_1234567891_1678886500.bin
└── ...
```

### Replay Information / 回放信息
Each replay includes:
- Unique replay ID
- Player name
- Song ID
- Creation timestamp
- File size
- Binary game data

## 🛠️ Development / 开发

### Building from Source / 从源码构建
```bash
# Clone repository
git clone <repository-url>
cd cpp-phira-mp

# Install dependencies (Ubuntu/Debian example)
sudo apt-get install -y g++ make liblua5.4-dev uuid-dev

# Build
make

# Run tests (if available)
make test
```

### Code Structure / 代码结构
```
cpp-phira-mp/
├── include/              # Header files
│   ├── server.h         # Server core definitions
│   ├── session.h        # Session management
│   ├── room.h           # Room system
│   ├── commands.h       # Binary protocol commands
│   ├── http_server.h    # HTTP server
│   ├── lua_bindings.h   # Lua API bindings
│   └── ...
├── src/                 # Source files
│   ├── server.cpp       # Server implementation
│   ├── session.cpp      # Session handling
│   ├── http_server.cpp  # HTTP API implementation
│   ├── lua_bindings.cpp # Lua integration
│   └── ...
├── plugins/             # Lua plugins
│   ├── http-admin-api/
│   ├── replay-recorder/
│   ├── admin-commands/
│   ├── advanced-room-management/
│   └── virtual-room/
├── replays/             # Replay storage
├── locales/             # Localization files
├── Makefile             # Build configuration
├── server_config.yml    # Server configuration
└── README.md            # This file
```

### Extending the Server / 扩展服务器

#### Adding New Commands / 添加新命令
1. Define command in `include/commands.h`
2. Implement handling in `src/session.cpp`
3. Add plugin hooks if needed

#### Adding HTTP Endpoints / 添加HTTP端点
1. Add route registration in `src/http_server.cpp`
2. Implement handler function
3. Test with curl or HTTP client

#### Creating New Plugins / 创建新插件
1. Create directory in `plugins/`
2. Add `plugin.json` with metadata
3. Write `init.lua` with plugin logic
4. Enable in configuration

## 🐛 Troubleshooting / 故障排除

### Common Issues / 常见问题

**Server won't start:**
- Check port availability: `sudo lsof -i :12346`
- Verify dependencies: `ldd phira-mp-server`
- Check permissions: `chmod +x phira-mp-server`

**Plugins not loading:**
- Verify plugin directory structure
- Check `plugin.json` syntax
- Enable plugin in configuration
- Check Lua version compatibility

**HTTP API inaccessible:**
- Verify HTTP server is running on port 61234
- Check firewall rules
- Test locally: `curl http://localhost:61234/stats`

**High memory usage:**
- Check for memory leaks in plugins
- Monitor with `top` or `htop`
- Adjust connection limits if needed

### Logging / 日志
The server outputs logs to stdout. Important events include:
- Server startup and shutdown
- User connections/disconnections
- Room creation/deletion
- Plugin loading/enabling
- HTTP request processing

For production, redirect logs to a file:
```bash
./phira-mp-server > /var/log/phira-server.log 2>&1 &
```

## 🤝 Contributing / 贡献指南

### Development Process / 开发流程
1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request

### Code Standards / 代码标准
- **C++20** with modern practices
- **RAII** for resource management
- **const-correctness** where applicable
- **Meaningful naming** for variables/functions
- **Comments** for complex logic

### Testing / 测试
- Test new features thoroughly
- Verify backward compatibility
- Test with multiple simultaneous clients
- Validate HTTP API responses

## 📄 License / 许可证

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

```
MIT License

Copyright (c) 2026 HyperSynapseNetwork

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## 🙏 Acknowledgments / 致谢

- **Original phira-mp** - For the protocol specification and inspiration
- **Lua Community** - For the powerful scripting language
- **Open Source Contributors** - For various libraries and tools used
- **HyperSynapseNetwork** - For project sponsorship and development
- **tphira-mp** - provided a large amount of reference English for this project
- **jphira-mp** - provided a large amount of reference English for this project



---

**Note**: This server is under active development. Features and APIs may change as development progresses. Always check the documentation for your specific version.

**注意**: 本服务器正在积极开发中。功能和API可能会随着开发进展而变化。请始终查阅您特定版本的文档。