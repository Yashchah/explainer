# Inkline

Code goes in, a two-minute lesson video comes out. It should be easy to learn from and look good enough that people keep watching.

This repo holds a **playable mock** (`mock/index.html`). It is a single self-contained page with no build step. Open it in a browser. It plays EP 01, *Binary search in two minutes*, rendered live from a timeline. Use it as the reference for the real generator.

## Art direction: risograph print

The video looks like an animated riso zine: textured paper, grain that shifts 8–12 times a second, and inks that print slightly out of register. Code videos are usually dark IDE screens, so this look stands out. It also feels made by hand, which keeps attention without fast cuts.

Three inks, each with one meaning across the whole video:

| Ink | Means | Used for |
| --- | --- | --- |
| Blue | code and data | code, values, `lo` / `hi`, structure |
| Pink | look here now | active line, `mid`, current guess, the bug |
| Yellow | found / correct | found card, right answer, karaoke word |

Type: Bricolage Grotesque for display, Atkinson Hyperlegible for captions (designed for low-vision readers), JetBrains Mono for code.

## The seven beats (120 s)

| Time | Beat | Job |
| --- | --- | --- |
| 0:00 | Hook | A concrete, oversized problem: 1,000,000 numbers, one by one vs. 20 looks |
| 0:12 | Idea | A real-world analogy (guess my number) carries the whole algorithm, with no code yet |
| 0:28 | Code | Code types in line by line. Each line gets a margin note in the analogy's words |
| 0:48 | Trace | Real execution: line highlight, inline values, pointers, discarded cells, pause & predict |
| 1:26 | Speed | The payoff: countable dots vs. a bar that runs off the page, then the doubling rule |
| 1:40 | Trap | The most common bug fails on the smallest input, then gets fixed |
| 1:52 | Recap | Three steps in the same words used throughout, plus a hook into the next episode |

## The method (rules every generated video follows)

1. **Stakes before syntax.** Open on a problem the viewer wants solved, never on code.
2. **Concrete before abstract.** An analogy first. The code then only gives names to things the viewer already understands.
3. **Code earns each line.** Never a full-screen code dump. Lines appear one at a time, each tied to the idea.
4. **One focus at a time.** The active line, the moving object and the caption keyword share one ink. Everything else steps back.
5. **Show state.** Variables are objects that move. Discarded data stays visible, greyed out.
6. **Pause and predict.** At least one forced guess per video, with a 3-second timer so the video keeps flowing.
7. **Feel the payoff.** Show why it matters at three sizes, not as a formula.
8. **Teach the bug.** Show one real mistake failing on the smallest input, then fixed.
9. **Motion every 3–6 seconds.** Camera pushes, stamps, wipes. Never a still screen.
10. **Captions are design.** Karaoke word highlighting, with keywords in their ink color.

## How the real generator would work

1. **Parse** the source (tree-sitter) to find functions, loops, branches and changing variables.
2. **Plan beats** with an LLM, using the seven-beat template above and a 120 s budget. It outputs a *lesson spec* (JSON): narration lines, analogy, sample input, trap.
3. **Execute** the code on the sample input under a tracer (`sys.settrace` for Python) and record each line and variable change. The trace drives the trace scene, so the animation can't show something the code doesn't do.
4. **Voice:** TTS per narration line. Real audio durations re-time the spec.
5. **Compose** from scene templates: array stage, number line, race chart, bug card, recap cards, chosen by data shape (array, tree, graph, string, map).
6. **Render deterministically.** Each frame is a pure function of `t` (as in the mock). Playwright steps time frame by frame, captures 1920×1080 at 60 fps, and ffmpeg muxes in the voice track to make an MP4.

The mock already follows the shape of step 6. `render(t)` has no hidden state, and `#t45` in the URL opens the frame at 45 s. Step 6 only needs a capture script that walks `t` from 0 to 120.

## Mock controls

Space or click: play/pause · ←/→: skip 5 s · scrubber has chapter segments · Captions / Voice (browser speech, rough timing) / speed toggles · tap an answer during *Pause & predict*.
