# Firefox Session Limiter (works with other browsers)

This guide uses Firefox as the example but the same steps work for any browser. Use the reference table below to find the correct paths for your browser.

| Browser |          Binary             |       Desktop File         |
|---------|-----------------------------|----------------------------|
| Firefox | `/usr/bin/firefox`          | `firefox.desktop`          |
| Chrome  | `/usr/bin/google-chrome`    | `google-chrome.desktop`    |
| Chromium| `/usr/bin/chromium-browser` | `chromium-browser.desktop` |
| Brave   | `/usr/bin/brave-browser`    | `brave-browser.desktop`    |
| Edge    | `/usr/bin/microsoft-edge`   | `microsoft-edge.desktop`   |


---

## How it works

- Firefox is blocked by default
- You set a session length before Firefox opens
- A warning appears 1 minute before the session ends
- Firefox is killed when the timer runs out
- A 5 minute cooldown is enforced before a new session can start
- Closing Firefox manually also triggers the cooldown
- Multiple instances are blocked

---

## Prerequisites

Install zenity for popup dialogs:
```bash
sudo apt install zenity
```

---

## Setup

### Step 1 — Create the wrapper script
```bash
sudo nano /usr/local/bin/firefox-limited
```

Paste the following exactly:
```bash
#!/bin/bash

MINUTES=$1

# Prevent multiple instances
if pgrep -x "firefox" > /dev/null; then
    zenity --error --text="Firefox is already running." --title="Firefox Session"
    exit 1
fi

# Check cooldown
COOLDOWN_FILE="/home/$USER/.firefox_cooldown"
if [ -f "$COOLDOWN_FILE" ]; then
    LAST=$(cat "$COOLDOWN_FILE")
    NOW=$(date +%s)
    ELAPSED=$(( NOW - LAST ))
    REMAINING=$(( 300 - ELAPSED ))
    if [ "$REMAINING" -gt 0 ]; then
        zenity --error --text="Cooldown active. Please wait $REMAINING more seconds before opening Firefox." --title="Cooldown"
        exit 1
    fi
fi

if [ -z "$MINUTES" ]; then
    MINUTES=$(zenity --entry \
        --title="Firefox Session" \
        --text="How many minutes do you want to use Firefox?" \
        --entry-text="10")
    if [ $? -ne 0 ] || [ -z "$MINUTES" ]; then
        exit 0
    fi
fi

if ! [[ "$MINUTES" =~ ^[0-9]+$ ]] || [ "$MINUTES" -le 0 ]; then
    zenity --error --text="Please enter a valid number of minutes." --title="Firefox Session"
    exit 1
fi

SECONDS_LIMIT=$((MINUTES * 60))

zenity --question \
    --title="Firefox Session" \
    --text="Open Firefox for $MINUTES minute(s)?" \
    --ok-label="Yes" --cancel-label="Cancel"
if [ $? -ne 0 ]; then
    exit 0
fi

export MOZ_APP_LAUNCHER=/usr/bin/firefox
/usr/lib/firefox/firefox "${@:2}" &
FF_PID=$!

(
    sleep $SECONDS_LIMIT
    if kill -0 $FF_PID 2>/dev/null; then
        zenity --warning --text="Your $MINUTES minute Firefox session has ended. 5 minute cooldown started." --title="Session Over" &
        kill $FF_PID
        echo "$(date +%s)" > /home/$USER/.firefox_cooldown
    fi
) &

if [ "$MINUTES" -gt 2 ]; then
    (
        sleep $(( SECONDS_LIMIT - 60 ))
        if kill -0 $FF_PID 2>/dev/null; then
            zenity --warning --text="1 minute remaining in your Firefox session." --title="Firefox Session" &
        fi
    ) &
fi

wait $FF_PID

# Apply cooldown on manual close too
echo "$(date +%s)" > /home/$USER/.firefox_cooldown
```

Save with `Ctrl+O` → `Enter` → `Ctrl+X`.

---

### Step 2 — Make the script executable and secure
```bash
sudo chmod +x /usr/local/bin/firefox-limited
sudo chown root:root /usr/local/bin/firefox-limited
```

---

### Step 3 — Redirect the Firefox binary
```bash
sudo mv /usr/bin/firefox /usr/bin/firefox-real
sudo ln -s /usr/local/bin/firefox-limited /usr/bin/firefox
```

---

### Step 4 — Update the desktop shortcut and enable URL passthrough

This single command updates the desktop shortcut and fixes URL passthrough so apps that require web authentication can still open Firefox correctly:
```bash
sudo sed -i 's|Exec=firefox|Exec=/usr/local/bin/firefox-limited 5 %u|g' /usr/share/applications/firefox.desktop && sudo sed -i 's|/usr/lib/firefox/firefox &|/usr/lib/firefox/firefox "${@:2}" \&|g' /usr/local/bin/firefox-limited
```

---

### Step 5 — Test the setup

Start a 1 minute session:
```bash
firefox-limited 1
```

Firefox should open, close automatically after 1 minute, and block reopening for 5 minutes.

---

## Usage

Open Firefox for a set number of minutes:
```bash
firefox-limited 10    # 10 minute session
firefox-limited 30    # 30 minute session
```

Or click the Firefox desktop icon — it will prompt you to enter a session length.

---

## Troubleshooting

If the desktop shortcut stops working or URL passthrough breaks, run this to fix both at once:
```bash
sudo sed -i 's|Exec=.*firefox.*|Exec=/usr/local/bin/firefox-limited 5 %u|g' /usr/share/applications/firefox.desktop && sudo sed -i 's|/usr/lib/firefox/firefox &|/usr/lib/firefox/firefox "${@:2}" \&|g' /usr/local/bin/firefox-limited
```

---

## Limitations

- Requires sudo access to set up
- A user with sudo access can edit or remove the script
- Does not limit other browsers if they are installed
- Install only one browser and apply this method to it
