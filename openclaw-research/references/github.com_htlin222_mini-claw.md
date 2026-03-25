# GitHub - htlin222/mini-claw: minimalism alternative of OpenClaw · GitHub

**URL:** https://github.com/htlin222/mini-claw

---

Skip to content
Navigation Menu
Platform
Solutions
Resources
Open Source
Enterprise
Pricing
Sign in
Sign up
htlin222
/
mini-claw
Public
Notifications
Fork 15
 Star 72
Code
Issues
Pull requests
1
Actions
Projects
Security
Insights
htlin222/mini-claw
 main
1 Branch
0 Tags
Code
Folders and files
Name	Last commit message	Last commit date

Latest commit
htlin222
Add Playwright CLI for Pi Agent automation commands
6cdd991
 · 
History
5 Commits


skills/playwright
	
Add Playwright CLI for Pi Agent automation commands
	


src
	
Fix /new session reset and add streaming status updates
	


.env.example
	
Add improvements: rate limiting, file attachments, configurable timeouts
	


.gitignore
	
Add comprehensive unit test suite with vitest
	


CLAUDE.md
	
Add improvements: rate limiting, file attachments, configurable timeouts
	


Makefile
	
Add improvements: rate limiting, file attachments, configurable timeouts
	


README.md
	
Update README with new features
	


eslint.config.js
	
Add improvements: rate limiting, file attachments, configurable timeouts
	


package.json
	
Add improvements: rate limiting, file attachments, configurable timeouts
	


pnpm-lock.yaml
	
Add improvements: rate limiting, file attachments, configurable timeouts
	


tsconfig.json
	
Add comprehensive unit test suite with vitest
	


vitest.config.ts
	
Add comprehensive unit test suite with vitest
	
Repository files navigation
README
Mini-Claw

Lightweight Telegram bot for persistent AI conversations using Pi coding agent.

A minimalist alternative to OpenClaw - use your Claude Pro/Max or ChatGPT Plus subscription directly in Telegram, no API costs.

Features
Persistent Sessions - Conversations are saved and auto-compacted
Workspace Navigation - Change directories with /cd, run shell commands with /shell
Session Management - Archive, switch, and clean up old sessions
File Attachments - Automatically sends files created by Pi (PDF, images, documents)
Rate Limiting - Prevents message spam (configurable cooldown)
Access Control - Optional allowlist for authorized users
Typing Indicators - Shows activity while AI is processing
Architecture
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  Telegram   │────►│  Mini-Claw  │────►│  Pi Agent   │
│   (User)    │◄────│   (Bot)     │◄────│  (Session)  │
└─────────────┘     └─────────────┘     └─────────────┘
                           │
                           ▼
                    ~/.mini-claw/
                    └── sessions/
                        └── telegram-<chat_id>.jsonl

Quick Start
Prerequisites
Node.js 22+
pnpm
Pi coding agent installed globally
Installation
# Clone and install
git clone https://github.com/yourusername/mini-claw
cd mini-claw
pnpm install

# Login to AI provider (Claude or ChatGPT)
pi /login

# Configure bot token
cp .env.example .env
# Edit .env with your TELEGRAM_BOT_TOKEN

# Start the bot
pnpm start
Using Make
make install    # Install dependencies
make login      # Authenticate with AI provider
make dev        # Development mode (watch)
make start      # Production mode
make test       # Run tests
Bot Commands
Command	Description
/start	Welcome message
/help	Show all commands
/pwd	Show current working directory
/cd <path>	Change working directory
/home	Go to home directory
/shell <cmd>	Run shell command directly
/session	List and manage sessions
/new	Start fresh session (archives old)
/status	Show bot status
Configuration
# Required
TELEGRAM_BOT_TOKEN=your_bot_token

# Optional
MINI_CLAW_WORKSPACE=/path/to/workspace    # Default: ~/mini-claw-workspace
MINI_CLAW_SESSION_DIR=~/.mini-claw/sessions
PI_THINKING_LEVEL=low                      # low | medium | high
ALLOWED_USERS=123456,789012                # Comma-separated user IDs

# Rate limiting & timeouts (milliseconds)
RATE_LIMIT_COOLDOWN_MS=5000                # Default: 5 seconds
PI_TIMEOUT_MS=300000                       # Default: 5 minutes
SHELL_TIMEOUT_MS=60000                     # Default: 60 seconds

# Web search (optional)
BRAVE_API_KEY=your_brave_api_key           # For Pi web search skill
Deployment
systemd (Linux)
make install-service
systemctl --user start mini-claw
systemctl --user enable mini-claw
pm2
pnpm build
pm2 start dist/index.js --name mini-claw
pm2 save
tmux
tmux new -s mini-claw
pnpm start
# Ctrl+B, D to detach
Development
# Run in watch mode
pnpm dev

# Type checking
pnpm typecheck

# Run tests
pnpm test

# Run tests with coverage
pnpm test:coverage
Test Coverage
Module	Coverage
config.ts	100%
sessions.ts	100%
workspace.ts	100%
pi-runner.ts	100%
Tech Stack
Runtime: Node.js 22+, TypeScript
Telegram: grammY
AI: Pi coding agent
Testing: Vitest
Troubleshooting
"Pi not authenticated"
pi /login
"Session file locked"

Check for running Pi processes:

ps aux | grep pi
License

MIT

About

minimalism alternative of OpenClaw

Resources
 Readme
 Activity
Stars
 72 stars
Watchers
 1 watching
Forks
 15 forks
Report repository


Releases
No releases published


Packages
No packages published



Contributors
2
htlin222 Hsieh-Ting Lin (林協霆)
claude Claude


Languages
TypeScript
90.3%
 
JavaScript
6.1%
 
Makefile
3.6%
Footer
© 2026 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
Status
Community
Docs
Contact
Manage cookies
Do not share my personal information