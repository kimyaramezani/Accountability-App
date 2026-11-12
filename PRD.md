# Accountability App PRD

Nov 11, 2026 · Kimya

## Overview

An iOS app where a small friend group sets personal weekly goals, the app checks them automatically from Apple Health and Screen Time, and the group gets roasted or celebrated every week based on the results.

**Problem.** Accountability between friends works, but it runs on group texts and self-reporting. People forget to post, nobody wants to be the one keeping score, and "I worked out, trust me" is easy to fudge. The effort of tracking kills the habit before the habit sticks.

**Vision.** The app does the scorekeeping. You set a goal once ("4 workouts a week", "under 1 hour of social media a day"), connect your data, and forget about it. The app watches your progress, nudges you mid-week, and posts the verdict to the group: a playful roast if you fell short, a celebration if you hit it.

**Audience.** v1 is built for one friend group (roughly 3 to 10 people, all on iPhone), distributed through TestFlight. It should be designed so it could open to the public later, but nothing in v1 depends on that.

**v1 in one line.** Personal goals, evaluated weekly, verified automatically where possible (workouts, activity, sleep, screen time) with manual check-ins for everything else, and a group feed with roasts and celebrations.

## Goals, non-goals and success metrics

**Goals**

- Make tracking effortless: most goals verify themselves with zero daily input.
- Make the weekly result social and fun, so people come back for the group, not just the stats.
- Support different goal types (fitness, sleep, screen time, anything custom) in one consistent model.
- Keep the stakes playful: a miss should sting a little and get a laugh, never feel cruel.

**Non-goals for v1**

- Public sign-up, discovery or matching with strangers.
- Android or web clients.
- Real money stakes, betting or payments.
- Coaching, workout plans or health advice.
- Leaderboards that rank people against each other on raw numbers (the comparison is you vs. your own goal).

**Success metrics (friend-group scale)**

| Metric | Target after 8 weeks |
| --- | --- |
| Members still active (opened the app in the last week) | 80% or more of the group |
| Goals verified automatically, not manually | 70% or more |
| Weekly goal hit rate | Rising vs. the first 2 weeks |
| Reactions or comments per weekly results post | At least 1 per member |
| "This roast went too far" reports | Near zero |

## Users and user stories

There is one kind of user, a group member. One member creates the group and invites the rest; after that everyone has the same permissions.

**Setting up**

- As a member, I can join my friends' group from an invite link, signing in with Apple in one tap.
- As a member, I can set one or more personal weekly goals and pick how each is verified.
- As a member, I can grant Apple Health and Screen Time access once and never think about it again.

**During the week**

- As a member, I can see my progress toward each goal without doing anything (e.g. "2 of 4 workouts").
- As a member, I get a nudge when I'm at risk of missing a goal while there's still time to fix it.
- As a member, I can check off a manual goal in one tap, optionally with a photo as proof.
- As a member, I can see how my friends are tracking, so I can hype them up or start the trash talk early.

**At the end of the week**

- As a member, I see a weekly results post: who hit, who missed, with a roast or celebration for each.
- As a member, I can react to and comment on the results post.
- As a member, I can challenge a friend's manual check-in if I don't believe it.
- As a member, I can mark a week as excused (sick, traveling) a limited number of times so I'm not roasted for real life.

**Over time**

- As a member, I can see my streaks and history for each goal.
- As a member, I can change a goal, with changes taking effect the following week so nobody edits their way out of a miss.

## Goal types and automatic verification

Every goal is a rule the app can score once a week: a metric, a target, and how many days or sessions count. Three of the four v1 goal types verify themselves; manual goals rely on the honor system plus friends who can call you out.

**Goal model.** Each goal has a type, a metric, a target (e.g. 30 min), a frequency (e.g. 4 times per week, or 5 of 7 days), and a verification source. Weeks run Monday to Sunday. Goal edits take effect the next Monday.

| Goal type | Example goals | Verified by | How it works | Watch-outs |
| --- | --- | --- | --- | --- |
| Workouts and activity | 4 workouts of 30+ min a week; 8,000 steps on 5 of 7 days; 150 exercise minutes a week | Apple Health (HealthKit), or a direct app connection | Reads workouts, steps and exercise minutes. Covers Apple Watch plus any app that writes to Health (Strava, Peloton, Garmin, Whoop and others). Background updates keep progress current. | Some apps don't sync to Health, or sync late. Health data can't be read while the phone is locked, so updates may lag until the phone is opened. |
| Sleep | 7+ hours on 5 of 7 nights; in bed by 11:30 pm on weeknights | Apple Health (sleep analysis), or a direct app connection | Reads sleep sessions from Apple Watch, Oura, Eight Sleep and other apps that write to Health. | Needs a sleep tracker. Without one, sleep falls back to manual. Naps and split nights need a clear rule. |
| Screen time | Under 1 hr of social media on 5 of 7 days; under 3 hrs total daily | Apple Screen Time API (FamilyControls + DeviceActivity) | The user picks apps or categories once. The app sets a daily limit; iOS alerts the app's extension if the limit is crossed, which marks that day as missed. No alert by midnight means the day passed. | Pass/fail only: outside the EU, iOS does not let third-party apps read actual minutes used, so the app can't say "you used 1 hr 42 min." Tracks iPhone use only. Requires Apple's Family Controls distribution entitlement. |
| Custom (manual) | Read 20 min 5x a week; no alcohol on weeknights; meditate daily | Self check-in, optional photo | One-tap check-in per day or session, with an optional photo shown in the group feed. Friends can challenge a check-in. | Honor system. Keep challenges playful, not a trial. |

**Unverifiable data.** If a data source is disconnected or permission is revoked mid-week, the goal shows as "unverified" rather than missed, and the member can switch it to manual check-in for that week. The group sees the label, which is its own gentle accountability.

### Connecting any health or fitness app

Members should be able to use whatever app or device they already track with. Apple Health is the hub for v1 because most popular apps already send their data to it and reading it is free; direct connections to individual apps fill the gaps.

Apple Health captures what goals need (workouts, steps, exercise minutes, sleep) from almost every major app. What it misses is each brand's own scores, like WHOOP Recovery or Garmin Body Battery.

| App or device | How the data gets in | What Apple Health doesn't carry |
| --- | --- | --- |
| Apple Watch, iPhone | Apple Health | Nothing |
| Strava, Peloton, Nike Run Club and most gym apps | Apple Health, once the member turns on Health sync in that app | App-only stats (kudos, Peloton output) |
| Garmin | Apple Health; the Garmin Connect app must be opened for data to sync | Body Battery, Training Readiness, HRV Status; workouts arrive without GPS routes |
| Oura | Apple Health | Readiness, Sleep Score, Activity Score, HRV |
| WHOOP | Apple Health | Recovery, Strain, Sleep Performance, HRV |
| Fitbit / Google Health | Apple Health, via the Google Health app (added Aug 2026) | Some data, such as HRV |
| Any app that doesn't write to Apple Health | Direct connection (below), or manual check-in | Everything, until connected |

**Direct connections (v1.1).** For an app that doesn't sync to Apple Health, or a goal built on a brand score ("WHOOP Recovery in the green 4 days a week"), the app connects straight to that service. The member signs in to the service once, and the backend receives new data automatically. Build only the ones the group actually uses. Strava, Oura, WHOOP and Fitbit offer public developer APIs; Garmin requires applying to its developer program.

**Aggregator option.** Services like Terra connect hundreds of apps through one integration, but plans start at $399 a month, too much for one friend group. Revisit if the app opens to the public.

**Rules**

- Each goal uses one data source, chosen at setup, so the same workout is never counted twice (e.g. a Peloton ride logged by both Peloton and Apple Watch).
- Goal setup lists the member's available sources and shows a "last synced" time on each goal.
- If a member's app isn't supported yet, the goal falls back to manual check-in until it is.

## Weekly cycle, roasts and celebrations

The week is the product's heartbeat: quiet tracking Monday to Saturday, a nudge when you're slipping, and a group reckoning Monday morning.

**The weekly loop**

1. **Monday:** new week starts. Goal edits from last week take effect. Everyone's progress resets to zero.
2. **During the week:** progress updates automatically in the background. Members see their own and friends' progress bars.
3. **At-risk nudge:** when a goal becomes hard to hit (e.g. 0 of 4 workouts by Thursday, or 2 screen-time misses already), the member gets a private push notification.
4. **Sunday 9 pm:** a "last call" reminder for anyone still short. Manual check-ins close at Sunday 11:59 pm.
5. **Monday ~8 am:** the app scores the week and posts **The Weekly Reckoning** to the group feed. Late-syncing Health data gets a grace window until then.
6. **Reactions:** members react, comment and pile on. Then the loop starts over.

**Roasts (on a miss)**

- Each member who missed a goal gets a short, funny line in the results post, e.g. "Jordan's running shoes filed a missing persons report. 1 of 4 workouts."
- Roasts poke at the miss, never the person: no comments on bodies, weight, appearance or anything outside the goal.
- Each member sets their own roast intensity: **Mild** (gentle teasing), **Medium** (default), or **Spicy**. Your setting controls how you get roasted.
- Repeat misses escalate the joke (week 3 in a row gets a callback to weeks 1 and 2), which keeps it fresh.
- Optional: friends can add their own roast in the comments, and the best one gets pinned.

**Celebrations (on a hit)**

- Hits get a hype line and a small visual moment (confetti, a badge).
- Streaks earn milestones (3, 5, 10, 25 weeks) shown on your profile.
- A "Clean sweep" badge when everyone in the group hits every goal that week.
- Comebacks are celebrated: hitting a goal after missing it gets extra hype.

**Keeping it kind**

- **Excused weeks:** each member gets 4 excused weeks per year (sick, traveling, injured). Excused goals show a neutral "out this week" label.
- **Report a roast:** any member can flag a line as too far. It's hidden and the generator is tuned down for that person.
- Roasts go only to the group, never to outside apps unless the member chooses to share.

**Roast generation.** v1 can start with a hand-written bank of templates per goal type and intensity, filled with the member's name and numbers. A later version can use an AI model to write fresher lines, with the same rules as guardrails.

**Notifications summary**

| Notification | Who gets it | When |
| --- | --- | --- |
| At-risk nudge | That member only | When a goal becomes hard to hit, at most once a day |
| Last call | Members still short | Sunday 9 pm |
| Weekly Reckoning is up | Whole group | Monday ~8 am |
| Friend hit a goal early | Whole group (optional) | When it happens |
| Check-in challenged | That member | When a friend challenges |

## Key screens and flows

v1 needs six core screens, reached from a three-tab bar: **Group**, **My Goals**, **Profile**.

**Onboarding flow**

1. Open invite link, then Sign in with Apple.
2. Pick a display name and photo.
3. Create your first goal (type, target, frequency).
4. Grant the permission that goal needs (Health or Screen Time), with a plain-language screen explaining exactly what is read and why.
5. Set roast intensity (Mild / Medium / Spicy).
6. Land on the Group tab.

**Screens**

| Screen | What it shows | Main actions |
| --- | --- | --- |
| Group home | This week's progress for every member, one row per person with a ring per goal; days left in the week | Tap a friend to see their goals; send a hype or a heckle |
| Weekly Reckoning | Last week's results post: hits, misses, roasts, celebrations, streaks | React, comment, pin a friend's roast, report a roast |
| My Goals | Each goal's progress this week, data source and sync status | Manual check-in, add or edit a goal, mark week excused |
| Goal setup | Goal type picker, target and frequency, data source, app/category picker for screen time | Save (takes effect now if it's a new goal, next Monday if editing) |
| Goal history | Week-by-week hit/miss for one goal, current and best streak | None (read only) |
| Profile and settings | Badges, streaks, roast intensity, connected data, excused weeks left, notifications | Change settings, disconnect data, leave group |

**Home screen widget (nice to have).** A small widget showing your rings for the week and days left, so you see progress without opening the app.

## Technical approach, data and privacy

The phone does the verifying and the server only sees scores: each iPhone reads its own Health and Screen Time data, turns it into progress numbers, and syncs just those numbers to a small backend that runs the weekly scoring and notifications.

**Architecture**

| Part | Choice for v1 | Role |
| --- | --- | --- |
| iOS app | Swift + SwiftUI, iOS 17+ | All screens, goal setup, local progress calculation |
| Health reader | HealthKit observer queries with background delivery | Wakes the app when new workouts, steps or sleep land in Health; recomputes progress |
| Screen Time extension | DeviceActivityMonitor app extension + App Group storage | Gets the "limit crossed" event from iOS and records that day as missed |
| Backend | Supabase or Firebase (pick one; both have free tiers fine for one friend group) | Accounts, groups, goals, weekly progress, feed posts, reactions |
| Scheduled jobs | Server cron | Monday scoring, the Weekly Reckoning post, nudge checks |
| Push | Apple Push Notification service, sent from the backend | Nudges, last call, results |
| Sign-in | Sign in with Apple | One-tap accounts, no passwords |
| Distribution | TestFlight | Install for the friend group without an App Store launch |

**What leaves the phone.** Only derived results: e.g. "3 of 4 workouts, Tue/Thu/Sat", "sleep goal met 5 of 7 nights", "screen-time limit crossed on Wednesday". Raw health records, heart rate, sleep stages and app usage details never leave the device. Direct app connections are the exception: that data goes from the service to the backend, so the backend keeps only the fields a goal needs and deletes the rest. Workout type and duration can be shared if the member opts in (it makes the roasts better).

**Data model (core objects).** User, Group, Membership (with roast intensity, excused weeks left), Goal (type, metric, target, frequency, source, effective-from week), WeeklyProgress (goal, week, daily values, status), Check-in (with optional photo), FeedPost (weekly results, lines per member), Reaction / Comment, Challenge.

**Platform requirements**

- Apple Developer Program membership (needed for HealthKit, TestFlight and push).
- HealthKit capability and clear permission strings for every data type read.
- Family Controls distribution entitlement, requested from Apple, before Screen Time goals work outside development builds. Approval timing is not guaranteed, so screen-time goals should be built so the rest of the app can ship without them.
- Apple's rules bar using HealthKit data for advertising or selling it; this app does neither.

## Risks, open questions and milestones

The biggest risk is Screen Time: it's pass/fail only and gated by an Apple entitlement, so it ships last and the app must stand on its own without it.

**Risks**

| Risk | Impact | Mitigation |
| --- | --- | --- |
| Family Controls entitlement delayed or denied | No automatic screen-time goals | Apply early; ship workouts, sleep and manual first; screen time falls back to manual |
| Screen Time limit alerts don't fire reliably (developers have reported this) | Wrong pass/fail calls | Show "unverified" when the extension hasn't reported; let members dispute a result |
| Health data syncs late from third-party apps | Someone gets roasted for a workout they did | Grace window until Monday 8 am scoring; allow a correction that edits the post |
| Roasts land badly | Hurt feelings, people leave | Per-person intensity, report button, strict "goal not person" rules, excused weeks |
| A friend doesn't have an iPhone | They can't join | Out of scope for v1; note as a future decision |
| Interest fades after the novelty | Group stops checking | Streaks, escalating roast callbacks, clean-sweep badges; review after 8 weeks |

**Open questions**

- [ ] Should a member's roast intensity be their own choice, or voted on by the group?
- [ ] Should friends see exact numbers (e.g. "5,200 steps") or only progress toward the goal?
- [ ] Should there be shared group challenges later (everyone hits 100 workouts together this month)?
- [ ] Templates only for roasts in v1, or use an AI model from the start?
- [ ] Which week boundary and time zone if friends live in different time zones?
- [ ] Supabase or Firebase for the backend?

**Milestones**

1. **Foundation:** Sign in with Apple, groups and invites, manual goals and check-ins, group home. Friends start using it on TestFlight right away.
2. **Auto-verification:** HealthKit workouts, activity and sleep with background updates. Apply for the Family Controls entitlement in parallel.
3. **The fun part:** Weekly Reckoning post, roasts and celebrations, reactions, streaks, nudges and push notifications.
4. **Screen time:** DeviceActivity-based screen-time goals once the entitlement is approved.
5. **Polish:** widget, badges, challenges on check-ins, excused weeks, roast reporting, and direct connections to any app a friend uses that doesn't sync to Apple Health.

## Sources

- [Apple Developer Forums: Screen Time usage data access outside the EU](https://developer.apple.com/forums/thread/849296)
- [Apple Developer Forums: Screen Time data stays on device (Apple reply)](https://developer.apple.com/forums/thread/834794)
- [MacRumors: Fitbit data can finally sync to Apple Health](https://www.macrumors.com/2026/08/03/fitbit-apple-health-syncing/)
- [Sensai: What Oura, WHOOP and Garmin sync to Apple Health](https://www.sensai.fit/blog/wearable-apple-health-integration-what-syncs-oura-whoop-garmin)
- [Terra: unified health data API](https://www.tryterra.co/)
