# YouTube Setup Guide

This guide explains the YouTube upload functionality and how to set it up.

## Overview

The timelapse downloader can automatically upload videos to YouTube with intelligent duplicate detection. It uses the YouTube Data API to check for existing videos by title.

## File Types Explained

### client_secrets.json (OAuth2 Application Configuration)
- Contains your OAuth2 client ID and client secret from Google Cloud Console
- Downloaded from Google Cloud Console when you create OAuth2 credentials
- This file identifies your application to Google
- Should be kept secure but is needed to run the authentication flow
- **This is different from user credentials**

### youtube_credentials.json (User Authentication Tokens)
- Contains your authenticated user tokens (access token, refresh token, etc.)
- Generated automatically after the OAuth2 flow completes
- This file proves you've authorized the application to access your YouTube account
- Should be kept private and not shared
- **This is different from the client secrets**

## Setup Steps

1. **Google Cloud Console Setup**:
   - Go to [Google Cloud Console](https://console.cloud.google.com/)
   - Create a new project or select an existing one
   - Enable the YouTube Data API v3
   - Go to "Credentials" → "Create Credentials" → "OAuth 2.0 Client IDs"
   - Choose "Desktop Application" as the application type
   - Download the credentials file and save it as `client_secrets.json`

2. **Install YouTube Dependencies**:
   ```bash
   pip install -r requirements-youtube.txt
   ```

3. **Authentication Setup**:
   ```bash
   python get_timelapse.py --youtube-auth
   ```
   This will:
   - Open your browser for OAuth authorization
   - Generate `youtube_credentials.json` with your user tokens
   - You only need to do this once (unless tokens expire)

4. **Configure Options** (optional):
   - Edit `config.json` to set:
     - `youtube_privacy_status`: "public", "unlisted", or "private"
     - `youtube_playlist_id`: Playlist ID to add videos to automatically

## Usage

### Basic Upload
```bash
python get_timelapse.py --youtube-upload
```

### Upload with Custom Title Prefix
```bash
python get_timelapse.py --youtube-upload --youtube-title-prefix "My 3D Prints"
```

### Upload to Specific Playlist
```bash
python get_timelapse.py --youtube-upload --youtube-playlist-id "PLxxxxxxxxxxxxxxxxxxxxx"
```

### Keep Files After Upload
```bash
python get_timelapse.py --youtube-upload --keep-after-upload
```

## How Duplicate Detection Works

The system checks your YouTube channel for existing videos with matching titles:
- Compares the title that would be generated for the new video
- If an exact match or partial match is found, the upload is skipped
- This prevents duplicate uploads without needing local file tracking

## Troubleshooting

### "Could not fetch channel videos"
This usually means:
- YouTube API dependencies are not installed (`pip install -r requirements-youtube.txt`)
- Authentication failed or expired (run `--youtube-auth` again)
- API quota exceeded (wait and try again later)

### "Client secrets file not found"
- Make sure `client_secrets.json` exists in the script directory
- Download it from Google Cloud Console if missing

### "Authentication failed"
- Make sure you've enabled YouTube Data API v3 in Google Cloud Console
- Check that `client_secrets.json` is valid and not corrupted
- Try running `--youtube-auth` again

### Videos Being Deleted
- Use `--keep-after-upload` to prevent local file deletion after upload
- The script deletes files by default after successful upload to save disk space