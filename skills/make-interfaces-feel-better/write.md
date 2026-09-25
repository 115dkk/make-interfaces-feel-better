# Writing: Product Copy

Rules for the words an interface shows: labels, descriptions, helper text, tooltips, empty states, dialogs, toasts, status lines and error messages. Toolkit-agnostic. Parts are adapted from `better-writing` in [jakubkrehel/skills](https://github.com/jakubkrehel/skills) (MIT); the rest comes from copy rewrites in shipped products.

Generated interfaces get copy wrong in three recurring ways, and a review should look for all three first:

1. **Filler.** Sentences that tell users what they already know, repeat what the title or status lamp already says, or reassure them about things they never worried about.
2. **Internals on the screen.** The developer's vocabulary printed as if it were the user's: config key names, process names, registry paths, component names, state-machine terms.
3. **Raw errors.** An exception string, an HTTP body or an error code shown as the message, instead of text that says what happened and what to do.

The fix in all three cases is to write from the user's side of the screen: what they see, what they get, what they should do next.

## Recon the Existing Voice

Before writing or reviewing, read the copy nearby. Note the product's terms, its register (formal or casual, and in Korean which politeness level), its capitalization and any style guide or string catalogue that serves as the reference. A local edit does not invent a new voice.

Keep terms consistent. If it is "Archive" in the menu, it is not "Move to storage" in the toast. If the product says "apps", no string says "processes".

## Say What the User Gets, Not How It Works

A setting, option or button is named and described by its effect on the user, not by the mechanism behind it. The mechanism is true, and it is useless to someone who does not already know it.

| Before (mechanism) | After (effect) |
| --- | --- |
| Choose the gamma method used for alpha blending. | Choose how the brightness of letter edges is adjusted. |
| Configure subpixel order, filters, and per-channel tuning. | Reduce coloured edges and adjust text to suit your screen. |
| Use Windows font linking information to supply missing characters. | Use another font for characters missing from the chosen font. |
| Provide this device to ASIO applications | Use in ASIO apps |
| Hook child processes | Apply to apps opened by other apps |

- **A description says what changes and, when it helps, when to change it.** "Choose how fonts are read. Try the other option if a font displays incorrectly." The second sentence is what makes the setting usable.
- **Options get names, never numbers.** `Mode 0` to `Mode 6` becomes `Automatic`, `Sharp pixels, no smoothing`, `Natural spacing`, and so on. If you cannot name an option, the user cannot choose it.
- **A setting describes its ON state.** "Send read receipts", not "Don't send read receipts".
- **Standard platform phrases are not automatically right.** Pick the phrase that says what the action does on this screen. A Korean "실행 취소" for undo made users think of launching a program; "수정 취소" said what it does.
- **The label and its styling agree.** A play icon and a filled primary style mean "this starts something". A button that only designates or selects must not wear them, or users read it as a second start button.

## Keep Internals Off the Screen

Anything only the developer needs goes in the log, a details panel or the documentation, never in the main copy. Watch for:

- config and core setting names (`UseMapping`, `ClipBoxFix`), enum values, ids, JSON keys
- registry keys and paths (`HKCU Run`), file formats, manifest hashes, payloads
- process names, PIDs, bitness ("the 32-bit preview process"), injection, hooking, helper, broker, loader
- state-machine and verification vocabulary: transition, mutation, verified state, primary action, evidence, token, revision
- raw command lines and raw file contents echoed back as a summary
- build slugs and runtime names (`x64-avx2`, the name of the UI toolkit or language the app was ported from)

| Before | After |
| --- | --- |
| Stores the CacheMaxFaces core setting in the profile. | Set how many fonts can be kept ready for reuse. Higher values may use more memory. |
| Uses the per-user HKCU Run entry. Windows requests UAC approval only when system application is first prepared. | Open Control Center in the tray when you sign in to Windows. |
| The core files are present, but the 32-bit preview process has not started yet. | The preview is not ready. Choose Reconnect to try again. |
| Started process {pid} with MacLoader. | Opened the app with MacType (PID {pid}). |

- **Technical words the user knows and looks for stay.** Units (Hz, dB, ms), terms of the domain the user works in (BAR, UEFI, ASIO for an audio tool) and product names are content, not internals. The test is whether the reader of this screen uses the word themselves.
- **Display names are metadata; ids stay.** Rename what the user sees ("EQ APO XT (AVX2)" instead of "EqualizerAPO-XT x64-avx2") without touching the identifier underneath.

## Cut Filler

Every sentence must do work for the person reading it. Delete the ones that do not.

- **Do not say what is already shown.** If the card header names the unit, the body does not repeat the name as a label. A red lamp does not need the word "Rejected" beside it. Say each status once, in one place: the lamp or badge names the state, one line gives the cause.
- **Do not print fake readouts.** Tick marks with no control pointing at them, counts and statistics the user cannot act on, and theoretical curves beside a measured one are decoration pretending to be information.
- **Do not defend the product to the user.** Sentences about what the app will not do ("Control Center will not terminate a process owned by another session", "Complete profile contents are never logged", "this is not shown as normal system-wide application") answer questions nobody asked. If a limit matters to the user, state it as what they can do.
- **No reassurance, sentiment or marketing.** No "Don't worry", no "Oops!", no exclamation marks, no "seamless", "effortless" or "powerful". Warm tone is for success and onboarding at most; errors and destructive actions are plain.
- **No obvious explanations.** "Click Save to save your changes" teaches nothing. Explain only what the reader could not guess.
- **Turn state-machine prose into one instruction.**

| Before | After |
| --- | --- |
| Stop remains available; other mutations stay blocked. | You can still stop the service. |
| Service state is not ready for a primary action | Wait for the service |
| Wait for the current service transition to finish, then refresh. Control Center will not start another mutation from this state. | Wait for the current operation to finish, then refresh the status. |
| Restart required | Reopen apps to see changes |

## Errors Are Instructions

An error message says what happened in the user's terms and what to do next, next to the thing that failed. The raw error goes to the log.

| Before | After |
| --- | --- |
| `Error: EACCES: permission denied, open 'C:\...\config.json'` | Could not save settings. Close other apps using this file and try again. |
| That password is too short | Choose a password with at least 8 characters |
| Oops! Something went wrong. | Unable to save. Check your connection and try again. |
| MacTray.exe does not match the verified installation path. | MacTray.exe is running from an unexpected location. Check it in Task Manager and close it yourself, then check again. |

- **Never render an exception, `error.message`, an HTTP body, an error code or a stack trace as the message.** Map each failure you can identify to a written, localized message (coded errors with catalogue entries). Keep the raw text reachable through a details disclosure, a copy button or the log, for the bug report.
- **But the real reason must reach the screen.** Swallowing a specific failure into "Something went wrong", or dropping it so the screen waits forever, is the opposite mistake. If the system told you why, tell the user in their terms.
- **Name the right failure and send the user the right way.** A failure in the phone's own connection must not tell the user to check the PC app. A failure after a successful open must not be labelled a refusal.
- **Phrase fixes positively and early.** "Use only letters", not "Don't use numbers or symbols", shown before the mistake when possible. When the same error keeps firing, change the interaction rather than the wording.
- **Status must be honest.** Do not show "connected" during a grace period when the connection is gone; show "reconnecting". Do not flash "unknown" because one read was missed; keep the last known value briefly and say when it is stale.

## When the App Acts, Say So

When the app does something on the user's behalf (applies a default, starts a service, changes a file), disclose it three times: before the click, in a success message that names what happened, and in a specific message if it fails. Do not replace disclosure with refusal or with an extra modal.

- **A confirmation is one dialog with one factual line about the consequence.** No "I understand" checkboxes, no essays to acknowledge, no disabled navigation behind a lock banner.
- **Buttons start with a verb and repeat the consequence.** "Delete this project?" offers `Delete project` and `Cancel`, never a bare `Yes` and `No` on a consequential action.
- **Respect a refusal.** Ask for elevation or permission once; if the user declines, say what is unavailable and how to get it later, and stop asking.

## Short, Conditional Help

- **A tooltip gives the item's name and what choosing it does,** in one or two sentences, with the trade-off in plain words: "Removing the buffer reduces latency, but sound may play without EQ."
- **Show help and dependent options only when they apply.** A firewall paragraph appears only when the device is actually disconnected; a sub-option appears only when its parent is on (hidden, not merely disabled).
- **Empty states point forward:** what this place is, how to fill it, one action. But when the view's own state already says why it is empty, do not add a hint that restates it.
- **Placeholders are examples, not labels.** Every field keeps a visible label.

## Every Language, Fully

- **Every locale gets every string.** A Korean screen with an English lump in the middle ("Peaking" as a card title, `PK`/`DEV` badges) is untranslated, not "technical". Units and product names are the deliberate exceptions.
- **Plain words in every language.** Prefer the word a non-expert uses ("프리앰프" over the dictionary term "전치 증폭"); keep the domain's own tokens intact.
- **Never build a sentence from fragments around a variable.** Use a full templated string with proper plural forms, because word order changes per language.
- **Text must fit without shrinking.** When a string does not fit, rewrite it shorter or give it room; do not drop the font size (see [typography.md](typography.md)) and do not add `nowrap` or fixed widths to shared components, because the longest locale will overflow them.
- **If the product ships a subset font, new copy must fit the subset.** Reword rather than silently falling back to another font.

## Before You Finish

| Pattern to search for | What to do |
| --- | --- |
| A description containing a config key or "Stores the ... setting" | Rewrite as the effect on the user |
| Option labels `Mode 1`, `Level 2`, `Option A` | Name each option by what it does |
| Strings containing process, PID, registry, HKCU, inject, hook, payload, manifest, helper, broker, verified, mutation, transition | Move to the log or details, or rewrite in the user's terms |
| `error.message`, `String(err)`, `e.what()`, `QString::fromStdString(ex...)` reaching a label or toast | Map to a written message; keep the raw text in details or the log |
| "Oops", "Something went wrong" with nothing after it, exclamation marks in errors | State the cause and the next action |
| "will not", "never", "remains blocked", "is not shown as" in a description | Delete, or turn into what the user can do |
| A body label repeating the header, a word repeating the lamp | Say it once |
| An English word inside a non-English screen that is not a unit or a name | Translate it |
| Two words for one concept across screens | Pick one |

## Reporting

- **Severity.** `HIGH`: the copy misleads, hides how to recover from an error, shows a raw error or internal detail where the user must decide something, or sends the user the wrong way. `MEDIUM`: filler, inconsistent terms, mechanism-first descriptions, untranslated fragments. `LOW`: isolated wording polish.
- **Verification.** Source is enough: check every label against the action it triggers, every error path for a written message and a stated fix, every description against the question "would the user who needs this setting understand it", and terms against the copy around them. When the product has a string catalogue, check every locale, not only the one you wrote in.
- **Format.** Use the review format in [SKILL.md](SKILL.md), grouped by the rule each finding breaks, with the catalogue key or `path:line` as the location and the before and after text in the row.
