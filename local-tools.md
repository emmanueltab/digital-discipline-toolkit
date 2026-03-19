# Local Tools

Every tool you run locally is one less reason to open a browser. This section covers setting up CLI and desktop tools that replace common browser habits.

---

## Claude Desktop (AI without a browser)

The official Claude Desktop app is only available for Windows and Mac. On Linux, use the unofficial port which works well on Debian-based systems.

### Install
```bash
wget -O- https://raw.githubusercontent.com/emsi/claude-desktop/refs/heads/main/install-claude-desktop.sh | bash
```

### Launch
```bash
claude-desktop --disable-gpu --no-sandbox
```

Note: The `--disable-gpu` and `--no-sandbox` flags are required on most Linux systems to prevent a blank loading screen.

### Log in

A window will open prompting you to log in with your Anthropic account. Use the same credentials you use on claude.ai.

---

## cmus (Terminal Music Player)

cmus is a lightweight terminal music player. Pair it with a local music library to eliminate the need for YouTube or Spotify in the browser.

### Install
```bash
sudo apt install cmus
```

### Launch
```bash
cmus
```

### Add your music folder

Inside cmus type:
```
:add ~/Music
```

### Basic controls

| Key | Action |
|-----|--------|
| `5` | Browse library |
| `Enter` | Play selected |
| `c` | Pause / Resume |
| `b` | Next track |
| `z` | Previous track |
| `q` | Quit |

---

## yt-dlp (Download Audio and Video Locally)

yt-dlp lets you download audio from YouTube and other platforms so you can listen locally without a browser.

### Install
```bash
sudo curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp
sudo chmod a+rx /usr/local/bin/yt-dlp
```

Also install ffmpeg for audio conversion:
```bash
sudo apt install ffmpeg -y
```

### Download a single track as MP3
```bash
yt-dlp --extract-audio --audio-format mp3 -o "~/Music/%(title)s.%(ext)s" "URL"
```

### Download multiple tracks from a list

Create a text file with one URL per line:
```bash
nano ~/Music/urls.txt
```

Then download all at once:
```bash
yt-dlp --extract-audio --audio-format mp3 -o "~/Music/%(title)s.%(ext)s" -a ~/Music/urls.txt
```

### Organize into folders
```bash
mkdir -p ~/Music/folder-name
yt-dlp --extract-audio --audio-format mp3 -o "~/Music/folder-name/%(title)s.%(ext)s" "URL"
```

---

## gh CLI (GitHub without a browser)

The GitHub CLI lets you manage repositories, issues, and pull requests entirely from the terminal.

### Install
```bash
sudo apt install gh
```

### Authenticate
```bash
gh auth login
```

### Common commands
```bash
gh repo create repo-name    # create a new repo
gh repo list                # list your repos
gh repo view repo-name      # view repo details
gh repo clone repo-name     # clone a repo locally
gh issue create             # create an issue
gh pr create                # create a pull request
```

### Basic git workflow
```bash
git add .
git commit -m "description of change"
git push
```

---

## Coming Soon

- `neomutt` — terminal email client
- `calcurse` — terminal calendar
- `newsboat` — terminal RSS reader
