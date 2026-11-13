# flutter-rtl-audit

Finds the right-to-left bugs that break a Flutter app in Arabic, Hebrew, Persian and Urdu, and fixes the safe ones automatically.

It works two ways:

- as a plain Dart script you run from the terminal or in CI,
- as an agent skill, so any coding agent that supports the Agent Skills format can run it and fix things for you.

## Run it

```bash
git clone https://github.com/TaheBenSaid/flutter-rtl-audit.git
dart flutter-rtl-audit/scripts/rtl_audit.dart lib
```

```
Flutter RTL audit: lib
17 findings in 1 files, 8 fixable automatically

Fixable with --fix
  lib/inbox_page.dart:16  EdgeInsets.only(left:)  ->  EdgeInsetsDirectional.only(start:)
  lib/inbox_page.dart:35  Alignment.centerLeft  ->  AlignmentDirectional.centerStart
  lib/inbox_page.dart:46  Positioned(left:)  ->  PositionedDirectional(start:)
  ...

Needs a decision
  lib/inbox_page.dart:17  Directional asset 'assets/icons/arrow_back.png' won't flip in RTL. Add matchTextDirection: true
  lib/inbox_page.dart:19  Count glued to an English plural ('$count messages'). Arabic has 6 plural forms, move it to an ARB plural
  lib/inbox_page.dart:63  Phone field without textDirection. Add textDirection: TextDirection.ltr so digits stay in order
  ...
```

Add `--fix` to apply the safe changes, then run `flutter analyze`.

No dependencies, nothing to add to your `pubspec.yaml`.

## What it checks

| Fixed automatically | Flagged for you to decide |
| --- | --- |
| `EdgeInsets.only(left:/right:)` | Arrow and chevron assets that won't flip |
| `EdgeInsets.fromLTRB` | `'$count items'` and `count == 1 ? ...` plurals |
| `Alignment.centerLeft` and friends | Phone fields without `TextDirection.ltr` |
| `Positioned` and `AnimatedPositioned` with `left:/right:` | `DateFormat` without a locale |
| `TextAlign.left/right` | `children: [...].reversed` |
| `BorderRadius.only(topLeft:)`, `BorderRadius.horizontal` | Gradients using `Alignment.topLeft` |
| `Border(left:/right:)` | |

Comments, strings and generated files (`*.g.dart`, `*.freezed.dart`, `app_localizations*.dart`) are skipped.

## Use it as a skill

Clone it straight into your agent's skills folder, for example:

```bash
git clone https://github.com/TaheBenSaid/flutter-rtl-audit.git ~/.claude/skills/flutter-rtl-audit
```

Or into the skills folder inside one project. Then ask something like "check this app for RTL bugs" or "make this screen work in Arabic". The skill runs the scanner, applies the safe fixes, runs `flutter analyze`, and walks through the rest with you: plurals in ARB files, which icons should flip, phone numbers inside Arabic text.

## Use it in CI

The script exits with code 1 when it finds something, so you can fail a pipeline on new RTL bugs. Copy `rtl_audit.dart` into your repo, for example under `tools/`, and add a step:

```yaml
- run: dart tools/rtl_audit.dart lib
```

Try it on the sample screen in [`example`](example).

## More tools

This is part of a series of small Flutter tools, each tied to a problem I write about. The full list is in [flutter-skills](https://github.com/TaheBenSaid/flutter-skills).

## License

MIT
