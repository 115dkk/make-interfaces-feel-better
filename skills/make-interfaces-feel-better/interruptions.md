# Interruptions, Takeovers and Consent

Rules for any screen that seizes attention and asks for a decision: full-screen
takeovers, secure or protected desktops, modal ceremonies, consent prompts,
pairing and enrolment flows, warnings, and anything that shows a code or a
secret. Toolkit-agnostic.

These screens are judged by a different standard from ordinary UI. An ordinary
screen that is slightly wrong is annoying. A takeover that is slightly wrong is
frightening, and a frightened user either abandons a legitimate action or, worse,
learns that this is normal and complies with the next one that is not.

## Fear Comes From the Frame, Not the Content

Read the composition, not the pixels. A screen becomes threatening when several
of these are true at once, and any one of them alone is usually harmless:

1. The whole display is replaced with no warning, and the familiar environment
   (taskbar, clock, other windows) disappears.
2. No visible way out exists.
3. A countdown runs.
4. A large, high-contrast, non-figurative field owns the visual centre.
5. Nothing on screen names the program responsible.

That list is also, exactly, the design of a ransom note and of a tech-support
scareware lock screen. Those attacks force full-screen, remove the controls,
add urgency cues and mimic a system dialog, and they work on people who are not
naive. A legitimate takeover that assembles the same grammar inherits the same
reaction, and the user cannot tell the difference by looking, because by
construction there is nothing to look at.

**Rule:** when a takeover is unavoidable for real reasons (a protected desktop,
a privileged prompt, a kiosk), break the grammar deliberately. Keep the security
property; remove every other element on the list.

- Do not fix this by making the screen prettier. Softer colours on an
  inescapable countdown is still an inescapable countdown.
- Audit the screen by describing it in one sentence to someone who cannot see
  it. If that sentence could describe malware, redesign the frame.

## Always Leave a Door, and Name It

Perceived control is the single largest lever available on a screen like this.
An identical interruption is experienced as far less threatening when the person
believes they can leave, and the belief does the work whether or not they use
the door. The cost is one button.

- **Every screen that can be waited out must be escapable now.** If the flow
  will end by itself in two minutes, it must also end on demand in one second.
- **Provide both a pointer target and a key**, usually Escape. Frozen people
  reach for the keyboard; distracted people reach for the mouse.
- **Say the exit exists in words**, not only as a control. A sentence like
  "Esc or Cancel stops this at any time" converts an affordance that an anxious
  person may not notice into a promise they have read. This line is doing more
  work than the button next to it.
- **Honour it instantly and visibly.** The window must close and the previous
  environment must return on the same frame the key is pressed. Any lag here
  teaches that the door was decorative.
- **Do not punish the exit.** A person who escapes and is then told the
  operation "failed" learns that leaving broke something. Report a deliberate
  exit as a cancellation, in the same register you would use for success.
- An exit that decides nothing must not be routed through the code path that
  records decisions. Leaving before a question was asked is not an answer to it.

## Say When It Ends, and Give the Ending No Agent

A visible timer measurably degrades the decision it is attached to. Under time
pressure people stop deliberating and fall back on heuristics, defaults and
whatever is emotionally loudest; risk tolerance goes up. Attackers know this and
add countdown clocks to phishing pages on purpose.

If your screen genuinely expires, the user still needs to know. State when it
ends, and nothing else:

- Bad: "Time remaining 01:47", "Expires in 01:47", red digits, growing urgency.
- Also bad: "Closes by itself in 01:47."
- Good: "Ends in 01:47."

The middle form is the trap, and it is the one a careful writer reaches for
first. It sounds reassuring, and it tests well in isolation, but *closes by
itself* gives the agency to the screen: it says this thing will act, on its own
schedule, whatever you do. On a display that has already taken over the machine
that is a quiet second statement that the user is not in control, delivered in a
kind voice. The bare form attributes the ending to nobody, which is the only
framing that adds no threat at all.

The same trap sits in reassurance lines elsewhere on such a screen. "This window
will disappear on its own when you are done" is worse than silence: it answers a
question nobody asked and volunteers that the machine acts unprompted. Cut any
line whose content the reader can already infer; keep only facts they cannot see.

Keep the timer small, muted, below the content, and never style it more urgently
as it runs down. The information is identical; only the threat is removed.

The rare exception is a timer used **against** haste rather than for it: a short
enforced pause before a dangerous confirmation. That timer blocks the action
instead of racing it, and it belongs to a different rule.

## Explain Before You Display, Not After

Put an introduction screen in front of any ceremony that will take over the
display, show a code, or ask for an irreversible approval. It costs one click
and it changes the event from an ambush into a step the person walked into.

A good introduction says four things, in this order:

1. What this is, in one sentence, in the user's words.
2. What they should have ready before continuing (get the phone out *now*, not
   while a clock runs).
3. How to stop, naming both the key and the button.
4. The specific way this ceremony gets abused, and what never to do.

Then a button that names the next screen: "Show the QR code", not "Continue" or
"OK". Naming the consequence removes the surprise that the next screen would
otherwise deliver.

Requiring an explicit action to leave this screen is a feature, not friction.
Warnings that can be dismissed by reflex are dismissed by reflex; warnings that
require a deliberate act are the ones that survive.

## Warnings Are Spent by Repetition

Security warnings habituate faster than almost any other interface element, and
the habituation generalises: people tune out warnings they have never seen
before because they resemble ones they have. A warning shown on every run is
worth nothing by the tenth run.

- **Show a warning where it is rare and relevant.** A ceremony a person performs
  three times in their life is the right place for one. A screen they see daily
  is not.
- **Make the warning look unlike the surrounding chrome** — its own surface,
  its own ink, not merely bold body text. Sameness is what the brain learns to
  skip.
- **Say what to do, not what might happen.** "If someone else asked you to start
  this, stop now" is actionable. "This operation may be unsafe" is noise.
- **Use caution colour, not alarm colour.** Amber reads as attention; red reads
  as failure and adds the fear you are trying to remove. Reserve red for a state
  that has actually gone wrong.
- **Size the warning to its own text.** A band far taller than the words inside
  it reads as a detached decorative box and is skipped like an advertisement.
  Measure the copy and fit the surface to it, in every language.

## Size Every Fixed Box From the Longest Translation

A takeover is usually painted at fixed coordinates, and the copy on it is
usually authored in the shortest language in the catalogue. Those two facts
combine into clipped text that the author never sees, because the author reads
the language the box was measured against.

- **Measure, do not estimate.** Render each label in the face that locale
  actually ships and count the lines it needs inside the real box. A button that
  holds two lines in one language needs three in another, and the difference is
  not visible in the source.
- **Raising the type size re-opens this.** Every band, button and card sized
  before the change has to be re-measured after it. A one-step increase in body
  size is enough to turn a two-line label into a three-line one.
- **Prefer a wider box to a taller one.** Width removes lines; height only
  accommodates them. Widening until the worst case needs one line fewer is
  usually cheaper than the vertical room three lines demand.
- **Where two surfaces overlap, drop rather than clip.** A corner signature that
  a card is painted over should disappear when the room runs out, not be sliced
  by the card edge. Sliced text reads as a rendering fault and costs more trust
  than the missing text would.
- **A gallery only protects what it draws.** A screen absent from the review
  set, or drawn with a forgiving `min-width` where the real control is a fixed
  size, will keep regressing. Draw the real geometry, in the worst locales, and
  assert that no text exceeds its own box.

## Codes and Secrets on Screen

A QR code, a pairing code or a one-time secret is a machine-readable credential
that a person cannot inspect. That is exactly why it is attacked: codes carry
implicit trust, hide their destination until the moment of scanning, and most
people scan without verifying anything.

- **Say what the code does and who may see it.** "Never share this code with
  anyone" belongs on the screen, not only in documentation.
- **Never shrink a functional code for aesthetics.** Its module size is a
  legibility budget shared by a camera at arm's length, a poorly lit room, and
  any automated reader you test with. Measure the minimum that decodes, make it
  a floor in the layout, and let margins be sacrificed before the code is. Size
  was rarely why the screen felt oppressive anyway; isolation was.
- **Lay the escape route out relative to the code, not the other way round.**
  Compute the code's box first and anchor the footer to the container, so a
  short display loses padding rather than dropping the cancel button on top of
  the thing the camera has to read. Test the small sizes; that is where a layout
  that works at 1080p silently collapses.
- **Never put a real credential in a screenshot, a gallery fixture or a design
  mock.** Generate a meaningless code of the same version and module count so
  the geometry is honest, and make the payload a sentence that says it is a
  sample.

## Sign the Screen, Once

An unbranded takeover is anonymous, and anonymous is the same as unaccountable.
Put the product mark and name in one corner, small and quiet.

- **Exactly one, in the leading corner.** A mark repeated in several corners, or
  centred and enlarged, reads as a seal or a watermark, which is the register of
  an official summons and precisely the wrong feeling.
- Keep it in the accent colour at body weight. It is a signature, not a headline.
- Do not imitate the operating system's own dialog styling. Looking like a
  system alert is what scareware does; looking like your own program is what
  distinguishes you from it.

## Checklist

- [ ] Every takeover screen can be left with both a key and a visible control
- [ ] The exit is described in words, not only implied by a button
- [ ] Leaving is reported as a cancellation, never as a failure
- [ ] The timer states when the screen ends, not that it closes itself or how long is left
- [ ] An introduction precedes any ceremony that takes over or shows a code
- [ ] The proceed button names the next screen instead of saying Continue
- [ ] Warnings are rare, visually distinct, actionable, and amber rather than red
- [ ] Warning surfaces are measured to fit their own text in every language
- [ ] Every fixed box is sized from the longest translation, measured rather than estimated
- [ ] Where a surface overlaps another, the covered text is dropped rather than clipped
- [ ] Machine-readable codes carry a do-not-share line and keep a decode floor
- [ ] Layout places the code first and the footer relative to the container
- [ ] Sample codes in galleries and mocks are meaningless by construction
- [ ] The product mark appears exactly once, in one corner, quietly
