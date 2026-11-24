# NEXA AI — Full-Stack Personal AI Agent & Android Mobile Application

NEXA AI is a personal AI assistant capable of understanding and executing requests naturally in **Thanglish**, **Tamil**, and **English**. It connects conversational AI intelligence with real-world actions: scheduling exact native Android alarms, managing persistent reminders, synchronizing Google Calendar events, monitoring YouTube channels for new uploads, running background automations, and storing long-term personal memory.

---

## 🚀 Key Features

### 1. Conversational AI Agent with Thanglish & Tamil Understanding
- **Multi-Action Intent Parsing**: Handles compound sentences in one prompt:
  > *"Naalaiku 6 AM alarm vai, 9 AM assignment remind pannu, evening 7 PM project work schedule pannu."*
- **18 Structured Tool Integrations**:
  1. `create_alarm`
  2. `create_reminder`
  3. `create_task`
  4. `update_task`
  5. `delete_task`
  6. `create_calendar_event`
  7. `search_youtube_videos`
  8. `get_latest_channel_videos`
  9. `create_recurring_schedule`
  10. `create_automation`
  11. `update_automation`
  12. `save_memory`
  13. `search_memory`
  14. `create_note`
  15. `search_web`
  16. `get_daily_schedule`
  17. `get_upcoming_reminders`
  18. `get_productivity_summary`
- **Voice Assistant**: Integrated Speech-to-Text (STT) for Tamil (`ta-IN`) and Indian English/Thanglish (`en-IN`), with Text-to-Speech (TTS) audio playback.

### 2. Real Android Alarm & Reminder System
- Exact alarms scheduled via Capacitor Local Notifications & AlarmManager.
- Android 12+ / 13+ exact alarm permissions: `SCHEDULE_EXACT_ALARM`, `USE_EXACT_ALARM`, `WAKE_LOCK`.
- High-importance notification channels (`nexa-alarms`, `nexa-reminders`, `nexa-youtube`, `nexa-automations`).
- Action buttons: **Snooze (5 min)** and **Dismiss** directly on the heads-up notification.
- Persistent alarm reschedule on device reboot (`RECEIVE_BOOT_COMPLETED`).

### 3. YouTube Intelligence
- Add channels by URL, handle (`@tamil-tech`), or Channel ID (`UC...`).
- Zero-quota public RSS feed checking + YouTube Data API v3 integration.
- Background monitoring via Supabase Edge Function `youtube-monitor`.
- Deduplication of video IDs to ensure notifications are only sent once.

### 4. Supabase Backend Architecture
- 16 PostgreSQL tables with Row Level Security (RLS) enforcing `auth.uid() = user_id`:
  - `profiles`, `conversations`, `messages`, `tasks`, `reminders`, `alarms`, `calendar_events`, `youtube_channels`, `youtube_videos`, `automations`, `automation_logs`, `memories`, `notes`, `activity_logs`, `device_tokens`, `notification_preferences`.
- Secure Edge Functions:
  - `gemini-assistant`: Secure AI function calling without exposing API keys in client code.
  - `youtube-monitor`: Background channel checker and FCM push notifier.
  - `automation-runner`: Scheduled execution engine for routines.

---

## 🛠 Project Structure

```
nexa-ai-project/
├── android/                   # Native Android Capacitor Project
│   ├── app/
│   │   ├── build.gradle       # App ID: com.mohan.nexaai
│   │   └── src/main/
│   │       ├── AndroidManifest.xml # Exact alarms, permissions, boot receivers
│   │       └── assets/public/ # Compiled web bundle
├── dist/                      # Production web build
├── src/
│   ├── components/            # UI components (Icon, Stopwatch, UI toggles)
│   ├── context/               # Global AppContext & notification initialization
│   ├── data/                  # Navigation & seed data
│   ├── layouts/               # Main responsive layout (Desktop drawer + Mobile bottom nav)
│   ├── pages/                 # 10 Application modules:
│   │   ├── Home.jsx           # Daily AI briefing, stats, quick voice search
│   │   ├── Assistant.jsx      # Gemini AI chat, multi-action preview cards, STT/TTS
│   │   ├── Tasks.jsx          # Tasks CRUD, priorities, recurrence, filters
│   │   ├── Alarms.jsx         # Real exact alarms, timers, snooze/dismiss
│   │   ├── Calendar.jsx       # Month/Week/Day calendar & Google OAuth sync
│   │   ├── YouTube.jsx        # Channel monitor, latest uploads, RSS polling
│   │   ├── Automations.jsx    # Automation routines, triggers, execution history
│   │   ├── Memory.jsx         # Personal memory, notes, preferences
│   │   ├── Activity.jsx       # Real-time action audit trail
│   │   └── Settings.jsx       # Supabase auth, notifications, AI settings
│   ├── services/              # Integration services:
│   │   ├── aiService.js       # Gemini Edge Function client & Thanglish parser
│   │   ├── alarmService.js    # Exact native alarms & sound engine
│   │   ├── reminderService.js # Local & scheduled notifications
│   │   ├── taskService.js     # Task synchronization
│   │   ├── youtubeService.js  # YouTube RSS & Data API resolver
│   │   ├── calendarService.js # Google Calendar API & OAuth sync
│   │   ├── automationService.js# Automation runner trigger
│   │   ├── voiceService.js    # STT & TTS speech engine
│   │   └── supabaseClient.js  # Supabase client & Auth handlers
│   ├── App.jsx
│   └── main.jsx
├── supabase/
│   ├── functions/
│   │   ├── gemini-assistant/  # Secure Gemini 1.5 Edge Function
│   │   ├── youtube-monitor/   # YouTube poller Edge Function
│   │   └── automation-runner/ # Automation scheduler Edge Function
│   └── migrations/
│       └── 20261107000000_nexa_core_schema.sql # 16 Tables with RLS & Realtime
├── capacitor.config.json      # Capacitor configuration
├── package.json
└── vite.config.js
```

---

## 📲 How to Build the Android APK

### Step 1: Requirements
- **Java Development Kit (JDK 17 or 21)**
- **Android Studio** (or Android SDK Command-line Tools)

### Step 2: Build Web Assets & Sync with Capacitor
```bash
npm run build
npx cap sync
```

### Step 3: Compile APK via Command Line
Open a terminal in the project directory:
```bash
cd android
./gradlew assembleDebug
```
The compiled debug APK will be located at:
`android/app/build/outputs/apk/debug/app-debug.apk`

### Step 4: Or Build via Android Studio GUI
1. Open **Android Studio**.
2. Select **Open an Existing Project** and browse to the `android/` directory.
3. Wait for Gradle to finish syncing dependencies.
4. Click **Build > Build Bundle(s) / APK(s) > Build APK(s)**.
5. Transfer the generated APK to your Android device via USB, Google Drive, or `adb install app-debug.apk`.

---

## ☁️ How to Deploy the Supabase Backend

### 1. Apply Database Migrations
In your Supabase Dashboard:
1. Navigate to the **SQL Editor**.
2. Copy and paste the contents of `supabase/migrations/20261107000000_nexa_core_schema.sql`.
3. Click **Run**. All 16 tables, triggers, and RLS policies will be created.

### 2. Deploy Edge Functions & Secrets
Using the Supabase CLI:
```bash
# Set secrets
supabase secrets set GEMINI_API_KEY=your_gemini_api_key
supabase secrets set YOUTUBE_API_KEY=your_youtube_api_key
supabase secrets set FCM_SERVER_KEY=your_fcm_key

# Deploy edge functions
supabase functions deploy gemini-assistant
supabase functions deploy youtube-monitor
supabase functions deploy automation-runner
```

### 3. Connect Frontend to Supabase
Copy `.env.example` to `.env`:
```bash
VITE_SUPABASE_URL=https://your-project-ref.supabase.co
VITE_SUPABASE_ANON_KEY=your-supabase-anon-key
```

---

## 🧪 Testing Checklist

- [x] **Vite Web Build**: Tested and compiled with 0 errors (`dist/` created).
- [x] **Thanglish & Multi-Action Parser**: Tested compound inputs (*"Naalaiku 6 AM alarm vai, 9 AM assignment remind pannu"*).
- [x] **Native Android Manifest**: Configured with `SCHEDULE_EXACT_ALARM`, `USE_EXACT_ALARM`, `POST_NOTIFICATIONS`, `RECEIVE_BOOT_COMPLETED`, and `WAKE_LOCK`.
- [x] **Capacitor Android Project**: Generated, synced, and verified with all 6 native plugins.
- [x] **Database Schema**: 16 tables with RLS, foreign keys, timestamps, indexes, and realtime publications.
- [x] **Zero-Quota YouTube RSS**: Fallback engine ensures monitored channels work out of the box.
