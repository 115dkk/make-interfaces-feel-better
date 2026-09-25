# Writing: Product Copy

Rules for the words an interface shows (labels, descriptions, helper text, tooltips, empty states, dialogs, toasts, status lines, error messages, landing and login pages) and for the README that introduces the project. Toolkit-agnostic. Parts are adapted from `better-writing` in [jakubkrehel/skills](https://github.com/jakubkrehel/skills) (MIT); the rest comes from copy rewrites in shipped products.

Generated interfaces get copy wrong in four recurring ways, and a review should look for all four first:

1. **Filler.** Sentences that tell users what they already know, repeat what the title or status lamp already says, or reassure them about things they never worried about.
2. **Internals on the screen.** The developer's vocabulary printed as if it were the user's: config key names, process names, registry paths, component names, state-machine terms.
3. **Raw errors.** An exception string, an HTTP body or an error code shown as the message, instead of text that says what happened and what to do.
4. **Marketing voice.** Sentimental taglines, verse-like fragments and self-praise, on product screens and in READMEs alike.

The fix in every case is to write from the user's side of the screen: what they see, what they get, what they should do next.

## Recon the Existing Voice

Before writing or reviewing, read the copy nearby. Note the product's terms, its register (for Korean, see Korean Register below), its capitalization and any style guide or string catalogue that serves as the reference. A local edit does not invent a new voice.

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
- **No reassurance, sentiment or marketing.** No "Don't worry", no "Oops!", no exclamation marks, no "seamless", "effortless" or "powerful". Errors and destructive actions are plain. Marketing voice gets its own section below.
- **No obvious explanations.** "Click Save to save your changes" teaches nothing. Explain only what the reader could not guess.
- **Turn state-machine prose into one instruction.**

| Before | After |
| --- | --- |
| Stop remains available; other mutations stay blocked. | You can still stop the service. |
| Service state is not ready for a primary action | Wait for the service |
| Wait for the current service transition to finish, then refresh. Control Center will not start another mutation from this state. | Wait for the current operation to finish, then refresh the status. |
| Restart required | Reopen apps to see changes |

## No Marketing Voice

Sentimental taglines are the most recognisable mark of generated copy. Every product that uses them sounds like every other one, and so much AI output has leaned on them that readers now take them as a sign that nobody wrote the page. Keep them out of product screens, landing pages, login screens and READMEs alike.

This is the pattern, from a generated game landing page and its login screen:

```text
A SHARED UNIVERSE. YOUR OWN JOURNEY.

작은 시작.
함께 넓혀가는 우주.

나만의 기지를 세우고 함선을 준비하세요.
친구들과 항해의 이야기를 나누며, 더 먼 성계로.

실제 종합 관제 화면: 자원 생산, 시설 건설, 주변 성계와 함대 편성
실제 게임 화면 · 종합 관제
시간과 함께 성장하는 기지
실시간 채팅과 접속자
어디서든 이어가는 항해

COMMANDER ACCESS
다시 오신 것을 환영합니다
계정에 로그인하고 개척을 이어가세요.
```

What makes it slop:

- **A slogan in English capitals on a non-English page,** and a second-person promise ("your own journey") that says nothing about the product.
- **Verbless fragments set out like verse** ("작은 시작." / "함께 넓혀가는 우주."). They look deep and state no fact.
- **Features turned into poetic noun phrases** ("시간과 함께 성장하는 기지", "어디서든 이어가는 항해") instead of what the user can actually do.
- **Captions that insist the screenshot is real** ("실제 게임 화면"). A caption says what the picture shows. If it has to swear the picture is genuine, the page has a different problem.
- **Theme vocabulary spilled onto functional screens.** A login form titled "COMMANDER ACCESS", with a welcome-back line and "continue your pioneering", is a login form in costume.

Write the plain version: what the product is, what the user does in it, concrete facts. The rewrite below only rearranges facts the original already states; use the product's real ones.

| Before | After |
| --- | --- |
| A SHARED UNIVERSE. YOUR OWN JOURNEY. / 작은 시작. 함께 넓혀가는 우주. | 여러 사람이 한 서버에서 함께 하는 우주 전략 게임입니다. 기지를 짓고 자원을 생산해 주변 성계로 함대를 보냅니다. |
| 실제 종합 관제 화면: 자원 생산, 시설 건설, 주변 성계와 함대 편성 | 종합 관제 화면. 자원 생산, 시설 건설, 함대 편성을 한 화면에서 합니다. |
| COMMANDER ACCESS / 다시 오신 것을 환영합니다 / 계정에 로그인하고 개척을 이어가세요. | 로그인 (title, fields, a 로그인 button, nothing else) |

- **A product's fiction may have a voice; its chrome does not.** A game's dialogue and events can be as playful as the game wants. The landing page, login, settings, errors and store description around it are plain.
- **Adjectives about the product itself are claims the reader cannot check.** Replace each with the fact that would have justified it, or delete it.

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

## Korean Register

- **Do not use 해요체 for statements.** Sentences in the UI use 합니다체 ("PC와 아직 연결하지 않았습니다"). Status lines, empty states and short labels use noun phrases ("승인 요청 없음", "확인 중"). One app rewrote its whole catalogue this way: "기다리는 요청이 없어요" became "승인 요청 없음", and "받은 요청을 확인하고 있어요" became "받은 요청을 확인 중입니다".
- **Buttons and menu items take no sentence ending.** "저장", "다시 연결", "서비스 시작", never "저장해요".
- **Instructions end in -세요.** "다시 연결을 눌러 재시도하세요." This is the one 해요체 form Korean interfaces conventionally pair with 합니다체 statements; do not let it spread to statements ("확인했어요"). -십시오 is stiffer and fits only a product that is formal throughout.
- **The only exception is a deliberate character voice,** such as a game's in-world dialogue. Settings, errors and confirmations around it still use 합니다체.
- **READMEs and documentation use either the plain written style (-다) or 합니다체,** one of them for the whole document, never 해요체.

## READMEs Are Documents, Not Advertisements

A README has two readers: someone deciding whether to use the project, and someone who wants to build or change it. Neither is a customer to be sold to. Give them, in roughly this order, what the project is in one or two plain sentences, what it needs (platform, versions, accounts), how to install it, how to use it, what it does not do yet and its known problems, how to build and test it, and the licence.

- **No self-praise.** No bold tagline, no adjectives about the project itself ("powerful", "blazing fast", "elegant", "the easy way"), no manifesto on why the design is clever, no borrowing credibility by naming who else uses a licence or a technique, no scarcity ("first come, first served", "five spots"). A small project written like a famous product's launch page looks foolish. Plain facts read as confidence.
- **Explain the design only as far as the reader needs it to use the project.** "Each thread has four fields: focus, decisions, evidence, notes" is useful. A paragraph on why this is the right way to think about work is not.
- **State limits plainly.** Unsupported platforms, missing features, known bugs. A README that says where the project stops is the one readers trust.
- **A README in another language is written in that language, not transliterated from English.** A Korean README that follows the English one sentence by sentence comes out as predicate-less fragments, English-style dash and colon lists, a space between an English word and its particle (`Claude Code 나` for `Claude Code나`), literal metaphors, and "not A but B" contrasts nobody asked for. Write each language natively. If the owner keeps a writing guide for that language, such as a guide against translationese, it applies to the README in full.
- **One register for the whole document** (see Korean Register above for Korean).

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
| An all-caps English slogan, verbless tagline lines, "your journey" promises, captions calling a screenshot real | Say what the product is and what the user does in it |
| Theme words on a login, settings or error screen ("COMMANDER ACCESS") | Plain names: 로그인, 설정 |
| A README with a bold tagline, adjectives about the project, scarcity or a design manifesto | Delete; keep what it is, requirements, install, use, limits, build, licence |
| Korean statements ending in -어요, -아요 or -해요 outside character dialogue | 합니다체 for sentences, noun phrases for status and labels |

## Reporting

- **Severity.** `HIGH`: the copy misleads, hides how to recover from an error, shows a raw error or internal detail where the user must decide something, or sends the user the wrong way. `MEDIUM`: filler, marketing voice, self-praise in a README, inconsistent terms or register, mechanism-first descriptions, untranslated fragments. `LOW`: isolated wording polish.
- **Verification.** Source is enough: check every label against the action it triggers, every error path for a written message and a stated fix, every description against the question "would the user who needs this setting understand it", and terms against the copy around them. When the product has a string catalogue, check every locale, not only the one you wrote in.
- **Format.** Use the review format in [SKILL.md](SKILL.md), grouped by the rule each finding breaks, with the catalogue key or `path:line` as the location and the before and after text in the row.
