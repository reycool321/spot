# Spot / Petcam

Hermes-assisted remote desktop prototype with an Omarchy desktop-pet interface.
Windows uploads screen frames to Linux HQ and polls for input commands. Hermes
or a user chooses actions; the scripts handle transport and execution.

## Included implementation

- Python receiver: frames, MJPEG, health, client geometry, control queue, move results.
- Brain daemon: feed health, pet state/speech, command-file dispatch, local Hermes
  history inspection, targeting geometry, and action journal.
- Driver CLI: screenshot, movement, clicks, typing, shortcuts, and navigation.
- Native Windows sender: screenshots and remote input.
- Optional ffmpeg sender/bootstrap, viewer, mock feed, and synthetic loot fixtures.

Clicking precision remains under investigation. Progressive learning and an
independent learned-routine runner are not implemented. Separate script processes
do not make the current Hermes-assisted workflow autonomous.

## Setup

Install/configure Tailscale on both machines. The receiver has no application-level
authentication: bind only to the intended private/tailnet address and limit network
access. Do not expose it publicly. Use only authorized machines.

Python 3 is required on HQ. Install Pillow for image operations and the mock feed:

```bash
python3 -m pip install -r requirements.txt
python3 receiver.py YOUR_HQ_TAILSCALE_IP 9101
```

In another HQ terminal in this directory:

```bash
export PETCAM_STATE=http://YOUR_HQ_TAILSCALE_IP:9101/state
export PETCAM_LATEST=http://YOUR_HQ_TAILSCALE_IP:9101/latest.jpg
export PETCAM_CONTROL=http://YOUR_HQ_TAILSCALE_IP:9101/control
export PETCAM_CLIENT=http://YOUR_HQ_TAILSCALE_IP:9101/client
export PETCAM_BASE=http://YOUR_HQ_TAILSCALE_IP:9101
export PETCAM_PLACEHOLDER_IP=YOUR_HQ_TAILSCALE_IP
python3 brain.py
```

Pet rendering uses the existing Omarchy pet CLI; mpv/Hyprland are needed for the
provided HQ viewer. Environment settings must also be applied in any driver/viewer
terminal. Service definitions from the original machine are not included.

On Windows, review the sender first, then use a fresh Windows PowerShell window:

```powershell
$env:PETCAM_TARGET = 'http://YOUR_HQ_TAILSCALE_IP:9101/frame'
& .\petcam-send-native.ps1
```

Ctrl+C or closing the sender window stops capture and command polling. Restart in
a fresh window after changing embedded C#. Example 192.0.2.x addresses in optional
scripts are documentation placeholders: configure them before use. Local Python
defaults use loopback. Optional installers can install software; review them first.

## First checks

```bash
python3 drive.py state
python3 drive.py shot
python3 drive.py geometry
python3 drive.py move 640 360
```

Use actual JPEG feed coordinates, not coordinates from a resized chat preview.
Confirm movement before clicking. Do not type until the intended input is focused.
Cursor diagnostics require the matching sender/backend revision; an unavailable
result is not proof of successful movement. A changed frame is not proof of task
success. Commands and logs can contain private text and are excluded from Git.

## Current limitations

- Command/result correlation, focus, geometry changes, DPI, and replay protection
  need further validation.
- General mouse/keyboard control can operate logged-in applications.
- The supplied eval checks fixture parsing; it is not a comprehensive test of
  actual memory learning or remote action correctness.
- No model API key is bundled; Hermes/model configuration is external.

## Publication preparation

Personal network addresses were replaced, runtime data/backups/screenshots and the
unredacted historical PDF were excluded, and this README reflects the current
prototype. The original archive is unchanged. See VALIDATION.md for checks.

The archive's README declared MIT, but no license file was included. Confirm the
copyright holder and add the intended license before treating that declaration as
a complete license grant.
