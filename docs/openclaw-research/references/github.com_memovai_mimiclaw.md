# GitHub - memovai/mimiclaw: MimiClaw: Run OpenClaw on a $5 chip. No OS(Linux). No Node.js. No Mac mini. No Raspberry Pi. No VPS. Hardware agents OS. · GitHub

**URL:** https://github.com/memovai/mimiclaw

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
memovai
/
mimiclaw
Public
Notifications
Fork 650
 Star 4.6k
Code
Issues
42
Pull requests
43
Discussions
Actions
Projects
Security
Insights
memovai/mimiclaw
 main
19 Branches
2 Tags
Code
Folders and files
Name	Last commit message	Last commit date

Latest commit
crispyberry
Merge pull request #152 from IRONICBo/feat/release-spiffs
bb10ea0
 · 
History
214 Commits


.github
	
feat: include spiffs.bin in release and switch console to USB-JTAG
	


assets
	
chore: remove display and rgb source trees
	


docs
	
feat: link onboarding guide in docs
	


main
	
Merge pull request #150 from wjc1207/fix/feishu-md
	


scripts
	
chore: macOS setup skips installed tools and brew failures
	


skills/deploy
	
feat: add deploy skill with build/flash guide and helper scripts
	


spiffs_data
	
feat: add built-in gpio-control skill for switch confirmation
	


.gitignore
	
chore: add feishu config defaults
	


CMakeLists.txt
	
feat: add skill verification CLI commands and preflash SPIFFS image
	


CONTRIBUTING.md
	
chore: move CONTRIBUTE.md to repo root as CONTRIBUTING.md
	


LICENSE
	
docs: add MIT license
	


README.md
	
Merge pull request #136 from memovai/docs/wifi-onboarding-ap-guide
	


README_CN.md
	
feat: add onboarding guide links
	


README_JA.md
	
feat: add onboarding guide links
	


partitions.csv
	
feat: add sdkconfig defaults, partition table, and global config
	


sdkconfig.defaults
	
fix: increase SPIFFS_OBJ_NAME_LEN to 64 for long session filenames
	


sdkconfig.defaults.esp32s3
	
feat: include spiffs.bin in release and switch console to USB-JTAG
	
Repository files navigation
README
Contributing
MIT license
MimiClaw: Pocket AI Assistant on a $5 Chip

   

English | 中文 | 日本語

The world's first AI assistant(OpenClaw) on a $5 chip. No Linux. No Node.js. Just pure C

MimiClaw turns a tiny ESP32-S3 board into a personal AI assistant. Plug it into USB power, connect to WiFi, and talk to it through Telegram — it handles any task you throw at it and evolves over time with local memory — all on a chip the size of a thumb.

Meet MimiClaw
Tiny — No Linux, no Node.js, no bloat — just pure C
Handy — Message it from Telegram, it handles the rest
Loyal — Learns from memory, remembers across reboots
Energetic — USB power, 0.5 W, runs 24/7
Lovable — One ESP32-S3 board, $5, nothing else
How It Works

You send a message on Telegram. The ESP32-S3 picks it up over WiFi, feeds it into an agent loop — the LLM thinks, calls tools, reads memory — and sends the reply back. Supports both Anthropic (Claude) and OpenAI (GPT) as providers, switchable at runtime. Everything runs on a single $5 chip with all your data stored locally on flash.

Quick Start
What You Need
An ESP32-S3 dev board with 16 MB flash and 8 MB PSRAM (e.g. Xiaozhi AI board, ~$10)
A USB Type-C cable
A Telegram bot token — talk to @BotFather on Telegram to create one
An Anthropic API key — from console.anthropic.com, or an OpenAI API key — from platform.openai.com
Install
# You need ESP-IDF v5.5+ installed first:
# https://docs.espressif.com/projects/esp-idf/en/v5.5.2/esp32s3/get-started/

git clone https://github.com/memovai/mimiclaw.git
cd mimiclaw

idf.py set-target esp32s3
Ubuntu Install
macOS Install
Configure

MimiClaw uses a two-layer config system: build-time defaults in mimi_secrets.h, with runtime overrides via the serial CLI. CLI values are stored in NVS flash and take priority over build-time values.

cp main/mimi_secrets.h.example main/mimi_secrets.h

Edit main/mimi_secrets.h:

#define MIMI_SECRET_WIFI_SSID       "YourWiFiName"
#define MIMI_SECRET_WIFI_PASS       "YourWiFiPassword"
#define MIMI_SECRET_TG_TOKEN        "123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11"
#define MIMI_SECRET_API_KEY         "sk-ant-api03-xxxxx"
#define MIMI_SECRET_MODEL_PROVIDER  "anthropic"     // "anthropic" or "openai"
#define MIMI_SECRET_SEARCH_KEY      ""              // optional: Brave Search API key
#define MIMI_SECRET_TAVILY_KEY      ""              // optional: Tavily API key (preferred)
#define MIMI_SECRET_PROXY_HOST      ""              // optional: e.g. "10.0.0.1"
#define MIMI_SECRET_PROXY_PORT      ""              // optional: e.g. "7897"

Then build and flash:

# Clean build (required after any mimi_secrets.h change)
idf.py fullclean && idf.py build

# Find your serial port
ls /dev/cu.usb*          # macOS
ls /dev/ttyACM*          # Linux

# Flash and monitor (replace PORT with your port)
# USB adapter: likely /dev/cu.usbmodem11401 (macOS) or /dev/ttyACM0 (Linux)
idf.py -p PORT flash monitor

Important: Plug into the correct USB port! Most ESP32-S3 boards have two USB-C ports. You must use the one labeled USB (native USB Serial/JTAG), not the one labeled COM (external UART bridge). Plugging into the wrong port will cause flash/monitor failures.

Show reference photo
CLI Commands (via UART/COM port)

Connect via serial to configure or debug. Config commands let you change settings without recompiling — just plug in a USB cable anywhere.

Runtime config (saved to NVS, overrides build-time defaults):

mimi> wifi_set MySSID MyPassword   # change WiFi network
mimi> set_tg_token 123456:ABC...   # change Telegram bot token
mimi> set_api_key sk-ant-api03-... # change API key (Anthropic or OpenAI)
mimi> set_model_provider openai    # switch provider (anthropic|openai)
mimi> set_model gpt-4o             # change LLM model
mimi> set_proxy 127.0.0.1 7897  # set HTTP proxy
mimi> clear_proxy                  # remove proxy
mimi> set_search_key BSA...        # set Brave Search API key
mimi> set_tavily_key tvly-...      # set Tavily API key (preferred)
mimi> config_show                  # show all config (masked)
mimi> config_reset                 # clear NVS, revert to build-time defaults


Debug & maintenance:

mimi> wifi_status              # am I connected?
mimi> memory_read              # see what the bot remembers
mimi> memory_write "content"   # write to MEMORY.md
mimi> heap_info                # how much RAM is free?
mimi> session_list             # list all chat sessions
mimi> session_clear 12345      # wipe a conversation
mimi> heartbeat_trigger           # manually trigger a heartbeat check
mimi> cron_start                  # start cron scheduler now
mimi> restart                     # reboot

USB (JTAG) vs UART: Which Port for What

Most ESP32-S3 dev boards expose two USB-C ports:

Port	Use for
USB (JTAG)	idf.py flash, JTAG debugging
COM (UART)	REPL CLI, serial console

REPL requires the UART (COM) port. The USB (JTAG) port does not support interactive REPL input.

Port details & recommended workflow
Memory

MimiClaw stores everything as plain text files you can read and edit:

File	What it is
SOUL.md	The bot's personality — edit this to change how it behaves
USER.md	Info about you — name, preferences, language
MEMORY.md	Long-term memory — things the bot should always remember
HEARTBEAT.md	Task list the bot checks periodically and acts on autonomously
cron.json	Scheduled jobs — recurring or one-shot tasks created by the AI
2026-02-05.md	Daily notes — what happened today
tg_12345.jsonl	Chat history — your conversation with the bot
Tools

MimiClaw supports tool calling for both Anthropic and OpenAI — the LLM can call tools during a conversation and loop until the task is done (ReAct pattern).

Tool	Description
web_search	Search the web via Tavily (preferred) or Brave for current information
get_current_time	Fetch current date/time via HTTP and set the system clock
cron_add	Schedule a recurring or one-shot task (the LLM creates cron jobs on its own)
cron_list	List all scheduled cron jobs
cron_remove	Remove a cron job by ID

To enable web search, set a Tavily API key via MIMI_SECRET_TAVILY_KEY (preferred), or a Brave Search API key via MIMI_SECRET_SEARCH_KEY in mimi_secrets.h.

Cron Tasks

MimiClaw has a built-in cron scheduler that lets the AI schedule its own tasks. The LLM can create recurring jobs ("every N seconds") or one-shot jobs ("at unix timestamp") via the cron_add tool. When a job fires, its message is injected into the agent loop — so the AI wakes up, processes the task, and responds.

Jobs are persisted to SPIFFS (cron.json) and survive reboots. Example use cases: daily summaries, periodic reminders, scheduled check-ins.

Heartbeat

The heartbeat service periodically reads HEARTBEAT.md from SPIFFS and checks for actionable tasks. If uncompleted items are found (anything that isn't an empty line, a header, or a checked - [x] box), it sends a prompt to the agent loop so the AI can act on them autonomously.

This turns MimiClaw into a proactive assistant — write tasks to HEARTBEAT.md and the bot will pick them up on the next heartbeat cycle (default: every 30 minutes).

Also Included
WebSocket gateway on port 18789 — connect from your LAN with any WebSocket client
OTA updates — flash new firmware over WiFi, no USB needed
Dual-core — network I/O and AI processing run on separate CPU cores
HTTP proxy — CONNECT tunnel support for restricted networks
Multi-provider — supports both Anthropic (Claude) and OpenAI (GPT), switchable at runtime
Cron scheduler — the AI can schedule its own recurring and one-shot tasks, persisted across reboots
Heartbeat — periodically checks a task file and prompts the AI to act autonomously
Tool use — ReAct agent loop with tool calling for both providers
For Developers

Technical details live in the docs/ folder:

docs/ARCHITECTURE.md — system design, module map, task layout, memory budget, protocols, flash partitions
docs/TODO.md — feature gap tracker and roadmap
docs/WIFI_ONBOARDING_AP.md — how the local MimiClaw-XXXX onboarding/admin AP flow works
docs/tool-setup/ — configuration guides for external service integrations (Tavily, etc.)
Contributing

Please read CONTRIBUTING.md before opening issues or pull requests.

Contributors

Thanks to everyone who has contributed to MimiClaw.

License

MIT

Acknowledgments

Inspired by OpenClaw and Nanobot. MimiClaw reimplements the core AI agent architecture for embedded hardware — no Linux, no server, just a $5 chip.

Star History
About

MimiClaw: Run OpenClaw on a $5 chip. No OS(Linux). No Node.js. No Mac mini. No Raspberry Pi. No VPS. Hardware agents OS.

mimiclaw.io
Topics
ai memory assistant edge-ai-agents clawdbot openclaw
Resources
 Readme
License
 MIT license
Contributing
 Contributing
 Activity
 Custom properties
Stars
 4.6k stars
Watchers
 41 watching
Forks
 650 forks
Report repository


Releases 2
v0.1.1
Latest
+ 1 release


Packages
No packages published



Contributors
7


Languages
C
96.6%
 
Shell
3.0%
 
CMake
0.4%
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