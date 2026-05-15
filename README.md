# 70MAI DashCam Viewer

A simple macOS app written in SwiftUI, AVKit, and MapKit.

## Features

- Select the root folder of the SD card. The app automatically uses `Normal/Front` and `Normal/Back`.
- Backward compatible: If you select a folder that directly contains `Front` and `Back`, that also works.
- Finds matching MP4 pairs with the format:
  - `NOyyyymmdd-hhmmss-nnnnnnF.MP4` in `Front`
  - `NOyyyymmdd-hhmmss-nnnnnnB.MP4` in `Back`
- Displays the Front video on the left.
- Displays the Back video in a smaller size at the top right.
- Displays a MapKit map below the Back video and can draw the selected GPS route from the dashcam log.
- Start, pause, stop, previous, and next video pair.
- Audio can be turned on/off with the **Audio** toggle. Audio is off by default for more stable dashcam playback.
- Automatically continues to the next video pair when both the Front and Back videos in the current pair have ended.

## Usage

1. Open `70MAI DashCam Viewer.xcodeproj` in Xcode.
2. Select the target `70MAI DashCam Viewer` and press Run.
3. Click **Select SD Card…** and choose the root folder of the SD card. The app automatically opens `Normal/Front` and `Normal/Back`.
4. Select a video pair and click **Start**.
5. When a video pair has finished, the next matching pair starts automatically. After the last pair, playback stops.

## Requirements

- macOS 14.1 or newer.

## Robust Playback

This version prepares both `AVPlayerItem`s and waits for `.readyToPlay` before playback starts. It also attempts to resume playback during brief AVPlayer stalls. If a video fails during preparation or playback, the error is shown in the status bar, and the app automatically skips to the next video pair if one exists.

## Map / Route Display

The map is passive during video playback. It is only updated when you select a different route in the dropdown menu. The selected route is drawn as a polyline with start and end markers.

## Stable Video Display Without macOS Image Analysis

This version uses a custom `StableAVPlayerView` based on `AVPlayerView` instead of SwiftUI `VideoPlayer`.
`allowsVideoFrameAnalysis` is disabled, so macOS does not attempt Live Text / visual analysis of the video frame during playback.
In addition, there is a small watchdog that attempts to restart playback if a player briefly stops without a normal end or error message.

## GPS Routes

If the SD card’s root folder contains `GPSData000001.txt`, the app reads routes from the file. The line `$V02` marks the start of a new route. The app uses the first four comma-separated fields: epoch timestamp, GPS status, latitude, and longitude. The camera’s epoch value is corrected by +8 hours to UTC, and times are then displayed as Danish local time (`Europe/Copenhagen`). The routes are shown in the dropdown menu above the map, and the selected route is drawn on the map. There is a fallback to the earlier incorrect filename `GPSDate000001.txt` if a test file still has that name.

## Settings

This version now includes a separate **Settings…** window.

Here you can change:

- **Timestamp offset** in hours. The default is `8`, meaning the GPS file’s epoch value + 8 hours before display.
- **Time zone** as an IANA ID, for example `Europe/Copenhagen`.

Press **Recalculate** to reload `GPSData000001.txt` with the new settings. The videos do not need to be scanned again.

## Filtering Videos by Selected Route

When you select a route in the dropdown menu, the app filters the video list to the video pairs where the video start time from the filename is greater than or equal to the route start time and less than or equal to the route end time. If you select **All videos**, the filtering is removed again.

### Settings

The **Settings…** button opens a separate window where you can change the camera’s timestamp offset and select or enter an IANA time zone, for example `Europe/Copenhagen`. Press **Recalculate** to reload `GPSData000001.txt` and update the routes.