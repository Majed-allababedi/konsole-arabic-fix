# Fix disconnected Arabic letters in Konsole

[العربية](README.md)

Arabic looked fine everywhere on my desktop except Konsole, where some letters appeared disconnected. It happened both inside and outside Hermes, so the problem wasn't limited to that application.

I tried changing the Word mode settings first, but that made no difference. What worked for me was switching the profile font to DejaVu Sans Mono and opening a new Konsole window. It's not my favourite-looking font, but the letters join correctly now, so I kept it.

I'm sharing the settings in case you run into the same problem.

## My setup

- Fedora Linux 44 with KDE.
- Konsole `26.08.1-1.fc44.x86_64`.
- UTF-8 was already configured and Arabic fonts were installed.

## Change the font

1. Open Konsole and go to Settings → Edit Current Profile.
2. Under Appearance, select DejaVu Sans Mono. I used size 10; choose a size that suits you.
3. Save the settings and open a completely new Konsole window, not just another tab.
4. Try this text:

```sh
printf 'اللغة العربية جميلة
السلام عليكم ورحمة الله وبركاته
'
```

If you use Hermes, start it in the new window after checking that the letters join correctly.

## If the font isn't installed

Check which font the system selects:

```sh
fc-match 'DejaVu Sans Mono:lang=ar' -f '%{family}: %{file}\n'
```

The result should name DejaVu Sans Mono. If it shows another font, the system may be using a substitute.

On Fedora, you can install it with:

```sh
sudo dnf install dejavu-sans-mono-fonts
```

## Edit the profile file instead

Konsole profile files are normally stored here:

```text
~/.local/share/konsole/*.profile
```

Make sure you're editing the profile you actually use, and back it up first. Add or update this entry in its `[Appearance]` section:

```ini
Font=DejaVu Sans Mono,10,-1,5,50,0,0,0,0,0
```

Don't replace the whole file or add a duplicate `[Appearance]` section. Keep your colors, transparency, and other settings. I've included a [small example](examples/arabic-font.profile) to show the relevant entries.

## Word mode settings

These were enabled when the font change worked:

```ini
WordMode=true
WordModeAttr=true
```

Enabling them alone didn't solve the problem. I left them enabled after changing the font, so these are the settings I actually tested. I haven't checked whether disabling them gives the same result.

`WordModeAttr` uses the same color and formatting for a whole word, which can affect highlighting within words. If changing the font is enough on your machine, there's no reason to change every profile setting.

## Notes from my experience

I didn't need to change the system language or the fonts used by other applications. The issue was how Konsole displayed the text, not the Arabic text itself.

To undo the change, select your previous font in the profile settings or restore your backup, then open a new window. If the letters are still disconnected, check the active profile and the actual font before making more changes.

Some Arabic letters, such as ا, د, and ر, naturally don't join the following letter. This issue concerns missing joins where the letters should connect.

This worked on the setup listed above. I can't promise the same settings will fix every Arabic rendering problem in every terminal.

## Useful reference

[KDE's documentation on text layout](https://docs.kde.org/trunk_kf6/en/konsole/konsole/complex-text-rendering.html)
