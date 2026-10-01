# Fix disconnected Arabic letters in Konsole

[الشرح بالعربية](README.md)

A tested workaround for partially disconnected Arabic letters in **Konsole**, while Arabic renders normally elsewhere on the desktop.

## The working fix

Set the active Konsole profile's font to **DejaVu Sans Mono**, then open a **completely new Konsole window**. A separate test window displayed connected Arabic, and the user confirmed the result.

This is a workaround verified for the case below, not a universal fix for every Arabic rendering issue or terminal emulator. It does not change system-wide fonts.

## Tested environment

- Fedora Linux 44 with the KDE desktop.
- Konsole: `26.08.1-1.fc44.x86_64`.
- UTF-8 was already configured and Arabic fonts were installed.
- The issue affected Konsole both inside and outside Hermes; it was not specific to Hermes.

## Recommended: change the font in the GUI

1. Open Konsole.
2. Choose **Settings → Edit Current Profile**.
3. On the **Appearance** page, select **DejaVu Sans Mono** and a suitable size. The test used size 10.
4. Save the profile.
5. Open a completely new Konsole window. Do not rely on a new tab in the existing Konsole process for verification.
6. Run:

```sh
printf 'اللغة العربية جميلة
السلام عليكم ورحمة الله وبركاته
'
```

If you use Hermes, start it in the new window after confirming that the letters join correctly.

## Check that the font is installed

```sh
fc-match 'DejaVu Sans Mono:lang=ar' -f '%{family}: %{file}\n'
```

Check that the result actually names **DejaVu Sans Mono**. Fontconfig can return a substitute when the requested font is unavailable.

On Fedora, install it if needed:

```sh
sudo dnf install dejavu-sans-mono-fonts
```

## Manual profile edit

Profile files are normally located at:

```text
~/.local/share/konsole/*.profile
```

Identify the profile you actually use in the Konsole UI and back it up before editing. Add or update this entry in its existing `[Appearance]` section; do not create a duplicate section:

```ini
Font=DejaVu Sans Mono,10,-1,5,50,0,0,0,0,0
```

Do not replace your complete profile with the example: that could remove your colors, transparency, or startup commands. [examples/arabic-font.profile](examples/arabic-font.profile) is an illustrative snippet, not an automatic installer.

## What about Word mode?

The following settings were enabled during diagnosis:

```ini
WordMode=true
WordModeAttr=true
```

Changing these settings alone **did not fix the issue**, according to the user's test. They remained enabled in the successful font test, so this repository does not claim that disabling them produces the same result. Selecting **DejaVu Sans Mono** was the change that produced the successful independent-window result.

KDE documents that Word mode renders words as a unit, while the whole-word attributes option defers color, bold, and italic changes until the end of the word. That can affect highlighting within words; do not enable it unnecessarily if the font change alone works for you.

## Undo and troubleshooting

- Select the previous font in the profile settings, or restore your profile backup, then open a new window.
- Switching the system language to Arabic is not required for this case: UTF-8 was already working.
- Do not reverse the text or convert it into Arabic Presentation Forms for this workaround; the change is at the terminal-rendering level.
- If the problem persists, verify the active profile and the font returned by `fc-match`. Include your Konsole version and a screenshot of a non-sensitive test string when reporting the issue.
- Some Arabic letters naturally do not join the following letter, such as ا, د, and ر. This issue concerns missing connections where joining is expected.

## Reference

[KDE Konsole — Complex Text Layout](https://docs.kde.org/trunk_kf6/en/konsole/konsole/complex-text-rendering.html)

No personal profile contents, credentials, or screenshots of user sessions are included.
