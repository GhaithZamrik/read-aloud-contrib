# Inline playback speed

Automated coverage: `npm test` (including `tests/playback-rate.test.js`).

## Real-voice smoke test

Load the unpacked extension from this checkout in a disposable browser profile.
Use a multi-paragraph article with more than one speech section and a voice
whose synthesized audio is played by the extension (for example Piper).

1. Start reading. The **Speed** number field should appear at the bottom of the
   player, below the reading pane, without a preset dropdown. It must remain
   visible when the reading-controls disclosure is collapsed.
2. Enter a rate such as `1.3`, then move focus out of the field. Playback should
   use that rate. The number-input arrows should also adjust the speed.
3. With Piper, confirm the current sentence does not restart and highlighting
   continues at the same position. Check transitions back to `1` as well.
4. Pause, change speed again, and wait. Playback must remain paused. Resume;
   it should use the new rate at the same position.
5. Stop and restart; reopen the popup; check the same voice's speed in Settings.
   The new value should persist. Select another voice and confirm its speed
   preference remains independent.
6. Change speed as the article advances to the next section. Confirm the current
   section receives the update, not only the completed section.
7. Try the compact toolbar popup and pop-out player in light and dark mode.
   Confirm the localized label is visible, the input is keyboard-accessible,
   and the toolbar has no horizontal clipping.
8. Try invalid/empty input and out-of-range values. They must not reach the
   speech engine. Valid values are between 0.1 and 10; individual voices can
   impose narrower effective speed limits.

## Reader-mode floating speed presets

1. Select reader mode and start reading. The floating bar should show the current
   speed as a button, such as **1×**.
2. Activate it with mouse, Enter, or Space. Verify all eight presets are present:
   0.5×, 0.75×, 1×, 1.25×, 1.5×, 1.75×, 2×, and 3×. The current preset should be
   highlighted and marked pressed for assistive technology; a custom speed
   should still appear on the button without falsely selecting a preset.
3. Choose a preset while playing and while paused. Confirm the same live-speed
   behavior and per-voice persistence as the popup. Paused speech must not resume.
4. Press Escape while presets are open. Only the presets should close, with focus
   returned to the speed button. Clicking outside should dismiss them too.
5. Open display settings and speed presets alternately; they must not overlap.
6. Minimize the reader and repeat the preset selection. Click the underlying page
   to dismiss the presets without expanding or closing the floating bar.
7. Check light/dark themes and a 320px-wide viewport. Both the bar and preset
   panel should fit without clipping.
8. Stop playback or start playback in another tab. The old reader's speed button
   must become hidden, with no stale preset panel remaining open.

## Voices without live-speed support

1. Select a browser-native voice and start reading. Confirm both the popup speed
   field and reader-mode speed button are hidden, not merely disabled.
2. Repeat with a Google built-in/network voice where available. Its live speed
   control must also be hidden even though the voice is cloud-backed.
3. Verify hiding in playing, paused, and loading states, in both full and minimized
   reader mode. If presets were open when the voice changed, they must close.
4. Switch back to an audio-backed voice with live-speed support. The controls
   should return and show that voice's current rate.
5. A stale rate request reaching an unsupported voice must not restart it,
   change its rate, or overwrite its saved per-voice preference.
6. Verify the normal Settings page still lets users configure a native voice's
   speed before starting playback. Unknown/pending voice capabilities should
   keep live controls hidden until support is confirmed.

## Deterministic browser integration scope

Provider-independent integration can substitute a generated silent WAV for
synthesis while keeping the real Speech session, popup controls, extension
messaging, settings storage, and offscreen HTML audio element. Assert that a
speed change modifies `playbackRate` without replacing `src`, resetting
`currentTime`, or resuming paused playback. This verifies audio transport,
**not** live Piper model synthesis; perform the real-voice smoke test separately.
