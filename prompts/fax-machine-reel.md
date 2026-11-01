# Fax Machine Reel — `/faceless-video` prompt (v2, hardened)

> v1 changes are listed at the bottom. Paste everything inside the code block.

```
/faceless-video Make a 60-second faceless Reel: "How fax machines sent documents through a phone line with zero internet."

HOOK (hard spec):
- Frame 0 (t ≤ 0.10s): first spoken word, fax audio, and the paper-feeding-through-rollers visual all start on the SAME frame. Measured sync offset between the three ≤ 100ms. "Lands within 1.5s" = all three are audible/visible by t = 1.5s; the full line will finish ~4.5–5s.
- Line: "That screech? Two machines about to push a whole page through a phone line — with zero internet."
  (Accuracy note: on real G3 fax the recognisable screech is mainly the handshake; most machines mute the speaker during page data. Do NOT claim the screech IS the document. The CTA pays this off.)
- Mechanism rationale: combines spoken-claim-first (CRT: 68% vs 56% stay vs silent cold open) with audio-forward hook (dial-up: 1,349 views, best raw reach).

MUST: hook flows straight into the reveal. No spoken question beat, no visual hold, no pause between beats 1 and 2 (retention data: hook-to-reveal pauses cause the 4–9s universal drop).
MUST: during beats 2–4 no shot or diagram state holds > 3.0s (target 2–2.5s). Verify with ffmpeg scene detection + per-state animation log.
MUST: closing CTA has continuous motion under the spoken line — never a static hold.
MUST: every visual in beats 2–4 is driven by the signal (amplitude/frequency/bit stream), never decorative loops.

Structure — 7 beats (timestamps are targets, ±0.5s):
1. (0–5s) Hook: line + fax handshake audio + paper feeding, frame-0 synchronous. Bridge: 0.6s screech swell in the clear as the line ends, cutting into beat 2 on the swell.
2. (5–16s) Reveal — the scan: a light bar sweeps the page; each scan line becomes a strip of 1,728 black/white pixels; runs of white/black are counted ("white 412, black 9, white 1,307…") and packed into short codes (Modified Huffman run-length coding) — that's why a mostly-white page sends fast. Codes become a modulated tone stream on the phone line. Cuts every 2–2.5s.
3. (16–30s) Cause/effect — the receiver: incoming tone stream → decoded run lengths → pixels printed line by line on thermal paper. Printer head position and pixel rows MUST be locked to the visible incoming waveform (same timecode), not animated independently.
4. (30–40s) Callback — the handshake (the hook sound, explained): split-screen of two machines. Show in order: caller's 1100 Hz beep (CNG) → receiver's 2100 Hz answer tone (CED) → fast 300 bps chatter where they trade capabilities (page size, resolution, top speed) → a training burst to test the line → agreed speed locks in (e.g. 9,600 or 14,400 bps). Each stage labelled on screen in #00FF41. Tones play in the clear (VO pauses ≥ 1.5s total across this beat — the audio IS the content, not dead air).
5. (~38–40s) On-screen overlay "Comment FAX 👇" — enters while beat 4 visuals are still moving, stays up ≥ 3s.
6. (40–52s) Resolution: fully printed received page slides in beside the blank sheet from frame 0 — before/after contrast, plus one stat: "~1 page per minute over a voice line, standardised in 1980 (ITU T.4/T.30)."
7. (52–60s) CTA: paper still visibly printing underneath. Line: "Comment FAX and I'll decode exactly what that screech was saying." Final frame loops cleanly back to the frame-0 rollers for rewatch.

Title: "How Fax Machines Sent Documents Through a Phone Line With Zero Internet"
- MUST contain zero "#" characters. Run a literal character check on the final title string and print the result in the report.
- No hashtags anywhere in title. (Caption hashtags: none unless I ask.)

Audience: tech-curious, zero telecom/signal-processing knowledge. Every term (handshake, modulation, run-length) gets a plain-English gloss within the same sentence.
Accent colour: #00FF41. Tone: punchy, confident, nerdy-enthusiastic.
Visual style: editing-desk aesthetic; reactive tone-to-pixel visualisation.
Voice: ElevenLabs if available, else Kokoro — report which. ElevenLabs budget = ONE generation call for the full script; if a retake is needed, STOP AND ASK.
Captions: word-synced, high-contrast, inside the 9:16 safe zone (clear of top 14% and bottom 20%, right-side 12% UI rail).

Sound design:
- Fax audio: synthesise from spec (CNG 1100 Hz 0.5s on / 3s off; CED 2100 Hz ~3s; V.21 FSK 1650/1850 Hz; training burst) OR use a CC0/public-domain recording — state which and the licence. No uncleared audio.
- Mix: beats 1 & 4 — fax audio integrated loudness within 6 LU of VO; side-chain ducking capped at 4 dB; EQ-carve 1.6–2.2 kHz out of the VO bed music (not out of the fax tones). Music bed ≥ 10 LU under VO everywhere. Final master −14 LUFS integrated, −1 dBTP.

Research: 3–5 real sources (prefer ITU-T T.30, T.4, V.21/V.17 specs; a manufacturer or university explainer). Cite in the report. Mark [uncertain] on any disputed spec (e.g. typical page time, exact handshake durations, speaker-mute behaviour across models).

STOP AND ASK before: overwriting an existing output file; installing a new dependency; any paid API beyond the single voice call.

DONE WHEN, verified by the blind-review subagent with measurements, not opinion:
1. Hook: VO onset, fax audio onset, paper visual onset all ≤ 0.10s and within 100ms of each other.
2. Zero question beat; no silence/hold > 0.4s between beats 1→2.
3. All 7 beats present at roughly the stated timestamps.
4. Beats 2–4: max shot/diagram-state duration ≤ 3.0s (list the longest three).
5. CTA: motion present on every frame of 52–60s (frame-diff > 0 throughout).
6. Fax audio within 6 LU of VO in beats 1 and 4 (report measured values).
7. Title "#" count = 0.
8. No claim that the audible screech is the page data.

Report: file path, voice engine, runtime, measured values for 1/4/6, sources, [uncertain] flags, explicit hashtag-free title confirmation.
```

---

## What changed from v1 and why

| # | v1 issue | Risk | Fix |
|---|---|---|---|
| 1 | Hook line (~17 words) assigned to a 0–2s beat | Physically impossible at ~3.2 words/s (≈5s). Agent either rushes VO (unintelligible) or truncates beat 2 | Beat 1 = 0–5s; "lands" redefined as frame-0 onset of all three channels |
| 2 | "This screech… was a document traveling" | Factually wrong: the audible sound is mostly the T.30 handshake; most machines mute during page data. Telecom-literate commenters will dunk on it, eroding credibility in a nerd niche | Hook reframed to "two machines about to push a page"; CTA pays off the decode |
| 3 | Fax tone "prominent" + VO simultaneous, no mix spec | V.21/CED tones sit at 1.6–2.1 kHz — dead centre of speech intelligibility. Either VO gets masked or the agent buries the tone | Loudness target (≤6 LU gap), duck cap, EQ carve on music, VO pauses in beat 4 |
| 4 | "Each line converted into an audio tone pattern" | Oversimplified; skips the actual nerd payoff | Added Modified Huffman run-length coding — the "why mostly-white pages send fast" insight is the shareable fact |
| 5 | "Comment FAX" must land "before any attention-drop window" at ~40s | Your measured drop window is 4–9s; the clause is unverifiable as written | Anchored to visuals still moving + ≥3s on screen |
| 6 | Done-criteria subjective ("audibly prominent") | Blind reviewer can't fail anything objectively | Every criterion now has a measurement (onset ms, LU, frame-diff, scene-detect) |
| 7 | "One voice call" ambiguous on retakes | Silent budget overrun or a degraded single take | Retake = STOP AND ASK |
| 8 | Real fax recording unspecified | Copyright strike / Content ID mute kills the audio-forward hook | Synthesise from spec or CC0 only, licence reported |
| 9 | No loop ending | Leaves rewatch rate on the table | Final frame loops to frame-0 rollers |

## Open strategic questions (decide before render)

- **Hook-mechanism attribution is n=1 each.** CRT (68% vs 56%) and dial-up (1,349 views) are single data points; stacking them assumes additive effects. Log this video's 3s-hold and 4–9s curve against both so you can actually attribute.
- **"Comment FAX" CTA requires a DM automation (ManyChat etc.).** If none is wired, the keyword CTA drives comments but no fulfilment — set it up or change the CTA to a follow/save ask.
- **Series consistency:** dial-up → CRT → fax is becoming a "dead tech, live physics" franchise. Lock the #00FF41 editing-desk template as a reusable asset to cut per-video production cost.
