# Audiobooks player

A small web app for listening to audiobooks stored in Google Drive, made to be added to the iPhone home screen. Hebrew, right to left.

- Reads the Drive folder `ספרים מושמעים`: every subfolder is a book, its audio files (sorted by name) are the chapters. Loose audio files in the folder are one-chapter books.
- Continue from the last position, or start over. Progress is kept on the device.
- Book art: `cover.jpg` / `cover.png` in the book folder (or its only image) is shown in the library, the player and on the lock screen, and kept on the device. Books without one get art drawn from the title.
- A chapter is downloaded to the device before it plays (Drive does not accept a token in a media URL), and the next chapter is fetched ahead, so playback goes on without network and moves to the next chapter while the phone is locked.
- Lock screen and headphone controls through the Media Session API. Speed, 15/30 second skips, a short rewind after a long pause.
- Sleep timer: 15 to 60 minutes, or the end of the chapter (the next chapter then waits, ready).
- Save a whole book on the device before a flight, with its size shown; remove it when done.
- `book.json` in a book folder (written by Book Translator) gives full chapter titles and lengths before anything is downloaded.
- Sharing: every folder named `ספרים מושמעים` the listener can see counts, including ones shared with them. To share, share your folder in Drive and add the listener as a test user of the OAuth app.

Books come from Book Translator ("שלח ל-Google Drive" in its audiobook step), or from any folder of audio files.

## Files

- `index.html`: the whole app (HTML, CSS, JS).
- `sw.js`: caches the app shell so it opens offline. Chapters live in a separate cache the page manages.
- `manifest.webmanifest`, `icons/`: home screen install.
- `demo/` (not in git): local test library. `index.html?demo` reads `demo/library.json` and skips Google sign-in.

Run locally: `python3 -m http.server 8766`, then open `http://localhost:8766/?demo`.

## Google sign-in setup (one time)

The app uses the OAuth implicit redirect flow with the `drive.readonly` scope; it works inside a home screen web app, where popups do not. The client ID is public by design and lives in `index.html` (`CLIENT_ID`).

1. [Google Cloud Console](https://console.cloud.google.com/): pick a project (or create one).
2. APIs & Services, Library: enable **Google Drive API**.
3. Google Auth Platform, Get started: app name, support email, audience **External**. Then Audience, Test users: add your Google account. The app stays in Testing mode; only listed test users can sign in, and Google shows an "unverified app" warning once.
4. Clients, Create client, type **Web application**:
   - Authorized JavaScript origins: `https://ramishaked.github.io`, `http://localhost:8766`
   - Authorized redirect URIs: `https://ramishaked.github.io/audiobooks-player/`, `http://localhost:8766/`
5. Put the client ID in `CLIENT_ID` in `index.html`.
