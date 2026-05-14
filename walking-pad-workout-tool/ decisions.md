## Product Decision Log: Walking Pad Workout Tool

Built by: Jessica Parker  
Type: Personal tool / public artifact  
Live: https://claude.ai/public/artifacts/f4637fa1-86b8-499b-a683-68ba5047c889  
Status: Shipped. In active use. Iteration backlog exists.

---

## What It Is

A browser-based workout tracker built for use on a walking pad treadmill desk. Tracks a full weekly structure across 5 days (push, lower, pull, full body, active recovery), exercise details with equipment swap options, a live countdown timer with phase-by-phase cues, and a numbers view with calorie and step estimates.

Single HTML file. No backend. No login. No dependencies beyond two Google Fonts.

The user is me.

---

## Why I Built It

The workout programming I wanted to follow existed as a series of individual videos, not a structured routine. Good content, wrong format. To use it I had to make a set of decisions before every session: which days, which videos, what to repeat, how to sequence it. That pre-session decision load is exactly where routines break down.

I could have written a workout journal. Instead I built a tool that eliminated every decision except getting on the pad. The routine runs itself. The timer advances. The cues appear. Nothing requires attention except the movement.

That is the actual product requirement. Not a fitness tracker. A decision eliminator.

---

## Constraint-Driven Decisions

These are the decisions that shaped the build. Each one was a real choice with a real reason.

**Single HTML file, no backend**

The tool needs to open in one click. Any friction breaks the use case. I am on a treadmill. I cannot troubleshoot a login screen or wait for a loading state.

No state persistence was a deliberate call. The workout structure does not change session to session. What matters is the timer and the cue, not a history log. Removing persistence removed an entire category of complexity with no user value.

**Timer drives the experience, not a checklist**

A checklist requires decisions at each step. Read the next item, process it, advance, confirm. On a treadmill those are small costs that add up to cognitive load I was trying to eliminate.

A countdown timer requires nothing. It runs. When it ends, something changes on screen. No tapping through pages, no searching for what comes next. The phase transitions are automated. The only time I touch the screen is to pause between sets, which is on the iteration backlog.

**Equipment toggle built in, not a separate view**

The original build had dumbbells only. I added resistance bands as an inline toggle on each exercise after the first week of real use.

The alternative was two separate workout plans. That would have required choosing between them before starting, which reintroduces a pre-session decision. One tap, same timer, different movement. Zero decision cost.

**Light theme**

Started dark. My default assumption is that dark screens are easier to see. Testing a lighter version produced better visibility in practice and a feel that was less heavy for a workout context. Kept it on both counts.

**No sets or reps tracking**

Timed circuits eliminate the counting problem. You do the movement until the timer says to stop. No tracking required, no additional burden on the workout.

Weight tracking is on the iteration backlog. Seeing progression over time is motivating. That is a different requirement from counting reps in the moment, and it belongs in a later version with session memory.

**Belt-off as a first-class concept**

Lower body day includes exercises that cannot be done while walking. This is not a footnote. It is a named phase type in the data model with its own color code and explicit cues: stop the pad, complete the movement, restart the belt.

Treating this as a distinct phase type rather than an inline note was a safety and usability decision. If you are walking at speed and miss a small text note, you are doing a floor exercise on a moving surface. The color distinction and explicit cue make it structurally impossible to miss.

---

## What Broke in Actual Use

Two things surfaced immediately after the first real workout.

**Set transitions**. When the timer advances to the next exercise, there is no buffer. The new exercise appears and the timer starts. If you need to change weights or slow the belt, you are already behind. The pause button works but requires touching the screen mid-workout. The fix is a built-in transition phase, probably configurable because the right duration depends on belt speed and the next movement.

**Next-exercise visibility**. When the current set ends and the next one appears, you read it and then get the equipment. Those seconds are inside the work period. Showing the next exercise in the final seconds of the current set means equipment is ready before the transition, not after.

**Numbers tab**. The calorie and step estimates are based on inputs I hardcoded for myself during the build. The data is useful when it is your data. The current version is a static display that only works for me. The correct version is an entry form where the user inputs their stats and gets back their numbers.

---

## What I Would Build Next and Why

**10-second transition buffer between sets** 
Keeps the session hands-free. Duration should be a setting, not a fixed value, because the right transition time varies by belt speed and exercise type.

**Next exercise preview in final seconds** 
Changes the experience from reading and reacting to preparing and executing. Equipment is ready before the timer changes.

**Editable Numbers tab**  
An input form for height, weight, pace, and session duration makes the estimates functional for any user, not just me.

**Weight tracking across sessions**  
A simple log of weight used per exercise over time. Progression is motivating. Requires session memory, which was correctly out of scope for the initial build.

---

## What This Is Actually About

Every decision in this build started from the same place: what does this user actually need to do the thing, and what gets in the way of that.

The user was me, which made the requirements unusually clear. I knew where the friction was. I knew which decisions were costing me the routine. I knew what the failure mode looked like.

The process was the same one I use professionally. Start with what is actually broken. Map the real workflow. Build to match how the user operates, not how you think they should. Ship something that works, observe what breaks, iterate.

The tool is small. The diagnostic thinking behind it is the same thinking I bring to a prior authorization platform or a HIPAA compliance workflow. The domain changes. The process does not.
