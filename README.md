# 70MAI DashCam Viewer

Et simpelt macOS-program skrevet i SwiftUI, AVKit og MapKit.

## Funktioner

- Vælg SD-kortets rodmappe. Appen bruger automatisk `Normal/Front` og `Normal/Back`.
- Bagudkompatibelt: Hvis du vælger en mappe, der direkte indeholder `Front` og `Back`, virker det også.
- Finder matchende MP4-par med formatet:
  - `NOyyyymmdd-hhmmss-nnnnnnF.MP4` i `Front`
  - `NOyyyymmdd-hhmmss-nnnnnnB.MP4` i `Back`
- Viser Front-videoen til venstre.
- Viser Back-videoen i mindre størrelse øverst til højre.
- Viser et MapKit-kort under Back-videoen og kan tegne valgt GPS-rute fra dashcam-loggen.
- Start, pause, stop, forrige og næste videopar.
- Lyd kan slås til/fra med kontakten **Lyd**. Lyd er slået fra som standard for mere stabil dashcam-afspilning.
- Fortsætter automatisk med næste videopar, når både Front- og Back-videoen i det aktuelle par er slut.

## Brug

1. Åbn `70MAI DashCam Viewer.xcodeproj` i Xcode.
2. Vælg target `70MAI DashCam Viewer` og tryk Run.
3. Klik **Vælg SD-kort…** og vælg SD-kortets rodmappe. Appen åbner automatisk `Normal/Front` og `Normal/Back`.
4. Vælg et videopar og klik **Start**.
5. Når et videopar er færdigt, starter næste matchende par automatisk. Efter sidste par stopper afspilningen.

## Krav

- macOS 13 eller nyere.
- Xcode 15 eller nyere anbefales.

## Robust afspilning

Denne version forbereder begge `AVPlayerItem`s og venter på `.readyToPlay`, før afspilning startes. Den forsøger også at genoptage afspilning ved korte AVPlayer-stalls. Hvis en video fejler under forberedelse eller afspilning, vises fejlen i statuslinjen, og appen springer automatisk videre til næste videopar, hvis der findes et.

## Kort / rutevisning

Kortet er passivt under videoafspilning. Det opdateres kun, når du vælger en anden rute i dropdown-menuen. Den valgte rute tegnes som en polyline med start- og slutmarkør.

## Stabil video-visning uden macOS billedanalyse

Denne version bruger en egen `StableAVPlayerView` baseret på `AVPlayerView` i stedet for SwiftUI `VideoPlayer`.
`allowsVideoFrameAnalysis` er slået fra, så macOS ikke forsøger Live Text / visuel analyse af videobilledet under afspilning.
Derudover er der en lille watchdog, som forsøger at genstarte afspilningen hvis en spiller stopper kortvarigt uden normal slut-/fejlbesked.


## GPS-ruter

Hvis SD-kortets rodmappe indeholder `GPSData000001.txt`, læser appen ruter fra filen. Linjen `$V02` markerer start på en ny rute. Appen bruger de første fire kommaseparerede felter: epoch-timestamp, GPS-status, latitude og longitude. Kameraets epoch-værdi korrigeres med +8 timer til UTC, og tider vises derefter som dansk lokal tid (`Europe/Copenhagen`). Ruterne vises i dropdown-menuen over kortet, og den valgte rute tegnes på kortet. Der er fallback til det tidligere fejlnavn `GPSDate000001.txt`, hvis en testfil stadig hedder sådan.

## Indstillinger

Versionen indeholder nu et separat vindue **Indstillinger…**.

Her kan du ændre:

- **Timestamp-offset** i timer. Standard er `8`, dvs. GPS-filens epoch-værdi + 8 timer før visning.
- **Tidszone** som IANA-id, fx `Europe/Copenhagen`.

Tryk **Genberegn** for at genlæse `GPSData000001.txt` med de nye indstillinger. Videoerne behøver ikke at blive scannet igen.

## Filtrering af videoer efter valgt rute

Når du vælger en rute i dropdown-menuen, filtrerer appen videolisten til de videopar, hvor videoens starttid fra filnavnet er større end eller lig med rutens starttid og mindre end eller lig med rutens sluttid. Vælger du **Alle videoer**, fjernes filtreringen igen.

### Indstillinger

Knappen **Indstillinger…** åbner et separat vindue, hvor du kan ændre kameraets timestamp-offset og vælge eller skrive en IANA-tidszone, fx `Europe/Copenhagen`. Tryk **Genberegn** for at genlæse GPSData000001.txt og opdatere ruterne.
