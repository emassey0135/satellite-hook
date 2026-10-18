# The Satellite hook

The part of [Satellite](https://github.com/emassey0135/satellite-setup) that works inside Orbit's own apps on the
Orbit Speak and Orbit Player, as an [LSPosed](https://github.com/LSPosed/LSPosed) module. It lets Orbit's screen reader
and Backtalk share the Orbit, gives Backtalk the Orbit's keys as a braille display, and fixes bugs in Orbit's apps.

To set Satellite up, use [Satellite Setup](https://github.com/emassey0135/satellite-setup), which explains the whole
project.

## In Orbit's screen reader

- **Apps that speak for themselves.** Orbit's screen reader reads every app that isn't Orbit's own, even one that
  speaks through Orbit's screen reader itself. With the hook, Orbit leaves alone an app that says it speaks for itself,
  as it does Orbit's own apps (see below).
- **Backtalk's apps.** While Backtalk reads an app with the Orbit's keys, Orbit's screen reader stays quiet in it, and
  the keypad, including the command chords Orbit otherwise keeps for itself, goes to Backtalk. In Orbit's own apps,
  Orbit's screen reader works as before. On the Orbit Player, Backtalk also gets HOME and the 0 key, which Orbit
  otherwise never passes on.
- **The Orbit's keys as a braille display.** The hook provides Backtalk with a braille display through
  [Backtalk's braille display API](https://github.com/trypsynth/backtalk/blob/master/docs/braille-display-api.md): the
  Speak's braille keys type and run Backtalk's commands, and the Player's phone keypad is a braille keyboard, with the
  left column as dots 1, 2, 3 and 7, the right column as dots 4, 5, 6 and 8, and the middle column as the space bar.
- **Remote mode from any app.** The user guide's remote mode shortcuts work in any app (Space with Left Arrow and dot
  1, 2 or 3 for Bluetooth channels 1 to 3, Space with Right Arrow and dot 4 or 5 for channels 4 and 5, Space with Right
  Arrow and dot 6 for USB), plus Space with Right Arrow and dot 8 for USB braille HID. Leaving remote mode returns to
  the app you were in, rather than the home screen.

The hook finds what it needs in Orbit's screen reader by the text it logs (with
[DexKit](https://github.com/LuckyPray/DexKit)), not by its obfuscated names, so most Orbit updates don't affect it. It
reports in LSPosed's log whether it found everything, which setup checks.

## Fixes in Orbit's apps

- **Alarm clock:** daily and weekly alarms ring at their time, rather than late or not at all, and Dismiss stops an
  alarm, rather than letting it ring again as if snoozed.
- **Updater:** after an update that replaces the screen reader, the device restarts, as the updater means it to.

## For app developers: speaking for yourself

An Android app that speaks through Orbit's screen reader itself, as Orbit's own apps do, can tell the hook so with this
in its manifest's `<application>`:

```xml
<meta-data android:name="orbit.self_voicing" android:value="true" />
```

Orbit's screen reader then leaves the app's window alone, and the Satellite Launcher shows it beside Orbit's own apps
rather than in the Android Apps folder.

## Building

The hook builds with Gradle and JDK 21: `./gradlew assembleDebug`. LSPosed loads it into Orbit's screen reader, alarm
clock and updater (`com.orbit.screenreader`, `com.orbit.alarmclock`, `com.orbit.updater`). GitHub Actions builds each
push, signs it with Satellite's release key, and publishes it as the "dev" pre-release; each version tag is published
as a release, which setup installs.

## License

The Satellite hook is under the [Apache License 2.0](LICENSE).
