# CineSave — Movie Browser & Watchlist (Android, Kotlin)

**Module:** CS309 Mobile Application Development · University of Makeni
**Project (Q51):** Develop a mobile application that enables users to browse, search, and save movies they are interested in watching.
**Student:** Musa Augustine Conteh (ID 10698)

CineSave is a native Android app written in Kotlin with Jetpack Compose. Users browse a catalogue of 32 movies, filter by genre, sort, search by title, open a details page, and save movies to a personal watchlist where they can mark them as watched and attach a short note. Everything is stored on the phone with Room (SQLite), so the app works fully offline — no internet connection, account or API key is needed.

---

## Features

| Feature | Where |
|---|---|
| Browse all movies in a poster grid | Browse tab |
| Filter by genre (All, Action, Animation, Comedy, Crime, Drama, Horror, Romance, Sci-Fi, Thriller, War) | Browse tab – chips |
| Sort by Top rated / Newest first / Title A–Z | Browse tab – sort icon |
| Search movies by title (live, case-insensitive, with input validation) | Search tab |
| View full details: year, runtime, director, genre, rating, overview | Details screen |
| Save / remove a movie from the watchlist (one tap, bookmark icon) | Browse, Search, Details |
| Watchlist with All / To watch / Watched filters and counts | Watchlist tab |
| Mark a saved movie as watched / not watched | Watchlist, Details |
| Add a personal note (max 200 characters) to a saved movie | Details screen |
| Remove with **Undo** snackbar | Watchlist tab |
| Badge on the Watchlist tab showing how many movies are saved | Bottom bar |
| Dark and light theme (follows the phone's setting) | Whole app |

## Tech stack

| Layer | Technology |
|---|---|
| Language | Kotlin 1.9.24 |
| UI | Jetpack Compose (BOM 2024.06.00), Material 3 |
| Architecture | MVVM + Repository, unidirectional data flow |
| State | `StateFlow`, Compose state, `collectAsStateWithLifecycle` |
| Navigation | Navigation Compose 2.7.7 (bottom tabs + details route) |
| Storage | Room 2.6.1 (SQLite) with KSP |
| Async | Kotlin Coroutines & Flow |
| Tests | JUnit 4 (validation and formatting logic) |
| Build | Gradle 8.7 (wrapper), Android Gradle Plugin 8.5.2 |
| Min / target SDK | 26 (Android 8.0) / 34 (Android 14) |

## Requirements

- **Android Studio** Koala (2024.1.1) or newer — includes JDK 17 and the Android SDK
- Android SDK Platform 34 (Android Studio installs it when prompted)
- An Android phone (Android 8.0+) with USB debugging, **or** an emulator (Pixel 6, API 34 recommended)
- Internet connection **for the first build only** (Gradle downloads the libraries); the app itself runs offline

## Setup and run

1. Unzip `CineSave.zip`.
2. Open Android Studio → **File › Open…** → select the `CineSave` folder (the one containing `settings.gradle.kts`).
3. Wait for **Gradle sync** to finish. If asked, accept the prompt to install SDK Platform 34 or to use the bundled JDK 17.
4. Choose a device in the toolbar:
   - Emulator: **Device Manager › Create device › Pixel 6 › API 34**, then start it; or
   - Phone: enable *Developer options › USB debugging* and connect by cable.
5. Click **Run ▶** (Shift+F10). The app installs and opens on the Browse tab.

### Build from the command line (optional)

```bash
# macOS / Linux
./gradlew assembleDebug        # APK at app/build/outputs/apk/debug/app-debug.apk
./gradlew testDebugUnitTest    # run unit tests

# Windows
gradlew.bat assembleDebug
```

### Install the APK on a phone

```bash
adb install app/build/outputs/apk/debug/app-debug.apk
```
Or copy `app-debug.apk` to the phone and open it (allow "Install unknown apps" when asked).

## How to use

1. **Browse** — scroll the grid. Tap a genre chip to filter; tap the sort icon (top right) to change the order.
2. **Save** — tap the bookmark on any poster. It turns amber and the Watchlist badge goes up.
3. **Search** — open the Search tab and type at least 2 characters of a title (e.g. `lion`). Results appear as you type.
4. **Details** — tap any movie to see its full information. Use **Add to watchlist** / **Remove**.
5. **Watchlist** — see everything you saved. Tap the circle to mark as watched; use the filter chips; tap the bin to remove (tap **Undo** to bring it back).
6. **Notes** — on a saved movie's details page, type a note and tap **Save note**.

## Project structure

```
CineSave/
├── build.gradle.kts, settings.gradle.kts, gradle.properties, gradlew(.bat)
└── app/
    ├── build.gradle.kts                    # dependencies and SDK levels
    └── src/
        ├── main/
        │   ├── AndroidManifest.xml
        │   ├── java/com/unimak/cinesave/
        │   │   ├── CineSaveApp.kt          # Application: creates DB + repository, seeds catalogue
        │   │   ├── MainActivity.kt         # single activity, hosts Compose
        │   │   ├── data/
        │   │   │   ├── local/              # Room: entities, DAOs, database, seed movies
        │   │   │   └── repository/         # MovieRepository (single source of truth)
        │   │   ├── ui/
        │   │   │   ├── AppViewModelProvider.kt
        │   │   │   ├── navigation/         # routes, bottom bar, NavHost
        │   │   │   ├── components/         # PosterArt, MovieCard, MovieRow, EmptyState…
        │   │   │   ├── browse/  search/  details/  watchlist/   # screen + ViewModel each
        │   │   │   └── theme/              # colours and typography
        │   │   └── util/Validators.kt      # search/note validation, runtime formatting
        │   └── res/                        # icon, strings, window theme
        └── test/…/util/ValidatorsTest.kt   # unit tests
```

## Database

| Table | Columns |
|---|---|
| `movies` | `id` (PK), `title`, `year`, `genre`, `rating`, `runtimeMinutes`, `director`, `synopsis` |
| `watchlist` | `movieId` (PK, FK → movies.id, cascade delete), `addedAt`, `watched`, `note` |

The 32-movie catalogue is inserted automatically the first time the app starts.

## Troubleshooting

| Problem | Fix |
|---|---|
| "SDK location not found" | Open the project in Android Studio once; it writes `local.properties` automatically. |
| "Unsupported Java" / JDK error | *Settings › Build › Gradle › Gradle JDK* → choose **jbr-17** (Embedded JDK). |
| Gradle sync fails downloading | Check internet connection, then *File › Sync Project with Gradle Files*. |
| Want to reset data | *Settings › Apps › CineSave › Storage › Clear data* (catalogue re-seeds on next launch). |

## Notes

- Ratings in the catalogue are sample values out of 10 for demonstration.
- Posters are generated in-app (colour gradient with the title's initials), so no copyrighted artwork or network access is required.
