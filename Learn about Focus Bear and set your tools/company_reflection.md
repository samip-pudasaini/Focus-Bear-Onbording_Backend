# Focus Bear Reflection

# Part 1: Company Reflection

## Why do you think Focus Bear was created?

Our founder and CEO, Jeremy Nagel, was diagnosed with ADHD late in life. He had tried every app and every system. So he built the tool he actually needed: something that starts the morning, blocks the rabbit holes before you fall in, and forgives you when you slip.

Focus Bear is built by ADHD brains. It reduces decisions, removes the willpower tax, and guides you one step at a time instead of handing you a blank to-do list.

## What problem is Focus Bear solving?

Focus Bear helps people with ADHD who struggle to stay focused, manage distractions, and stick to routines. It gives users tools to reduce digital distractions, build structured routines, and stay on task without relying on willpower or motivation.

## Why do you think this mission is important?

The mission is very personal to me. I have been observing myself for signs of ADHD.

ADHD is more about executive dysfunction than hyperactivity or a lack of focus. When I am torn between many tasks, or worried about events later in the day, I get choice paralysis. I then cannot do anything, because I cannot judge how much time each task needs.

Focus Bear tackles this with set focus times, blocking, and routines that are harder to skip. That makes it easier to start working. I have experienced this myself.

## How does Focus Bear's work align with your personal values or interests?

I self-diagnosed with ADHD. It took me years of self-doubt and feeling worthless before I found this explanation. If I had known earlier, my studies would not have been affected, as I was doing well. It would also have done wonders for my mental health.

From my experience with the app and my own patterns, I need flexibility to work, such as listening to music. My cousin, who also has ADHD, needs a more restrictive system.

Focus Bear supports both. Users can block distracting apps, and they also get flexibility through the timer and the task-alignment indicator. This is personally relevant to me and matches my values.

## Personal connection

I know the challenges of a neurodivergent mind well. I have struggled to reply to messages and to start a task weeks before the due date. I then rush to start and finish it under the pressure and chaos of the deadline. I can relate to the challenges Focus Bear aims to solve.

## Specific life example

With ADHD, the hard part is getting started. Taking a break afterwards is also hard. When there is no clear line between work and rest, I shut down.

For example, I might study for 2-3 hours until my brain is overwhelmed and needs rest. But the guilt of not studying makes it hard to rest. If I do rest, I may not go back to studying.

A clear signal for when to work and when to rest would stop me from feeling overwhelmed. It would let me do both properly. This is the outcome I want.

---

# Part 2: First-Time User Experience

**Device and OS:** Windows, on a portable laptop (ASUS TUF A15)

## What I did

1. Signed up using Google, Apple, or email.
2. Set up distraction blocking.
   ![Blocking distraction options](distraction_blocking.png)
3. Step one: chose my purpose.
   ![Asks your purpose](step_1.png)
4. Chose whether to use AI to block distractions. The app states that the AI never records our information, browsing stays private, and the feature is optional.
5. Allowed website blocking. I chose what to block and what to allow, for example YouTube Music or Spotify.
   ![Allows blocking of websites](block_website.png)
   ![Websites that are not blocked](website_allowed.png)
6. Scheduled blocking, for example during work hours.
   ![Scheduled blocking](block_schedule.png)
7. Chose a strictness level that suits my preference.
8. Set up habits.

## Pain points and confusing parts

### 1. Brain Dump has no way to save notes for later

I can type into the Brain Dump, but there is no option to save the text as a note. I can manually add notes that I already planned to keep. But thoughts that appear while my mind wanders during a focus session cannot be saved. The only way to keep them is to copy the text somewhere else.

The text is kept while I work, but it is not saved as a separate note like the ones shown in the image.

![Brain Dump](brain_dump.png)

**Suggestion:** Add a "Save as note" button to the Brain Dump.

### 2. The TOP score is hard to understand

When I add a task, I can enter a "perspiration" value, but the app does not explain what it means. A higher TOP score should mean a higher priority, but I cannot tell how the inputs affect it.

For example: if perspiration is 2 weeks and the due date is tomorrow, does that mean I have already worked on the task for 2 weeks? I could not tell.

![TOP score confusion](TOP_score.png)

**Suggestion:** Add a short explanation or tooltip beside "perspiration" and the TOP score. It should include one worked example.

### 3. Micro breaks need habits before they work

Even after I enabled micro breaks, I had to add habits before I could use the feature. Sometimes I just want to do nothing or take a free-form break. I would expect that to be the default when no micro break activities are set up.

![Micro break option](micro_breaks.png)

**Suggestion:** Default to a free-form break when no micro break habits exist.

### 4. Focus Music does not support music with lyrics or linked services

Some people, including me, need music with lyrics, or classical music, to work well. It would help if the app could link to Spotify or YouTube Music. The user could then keep control over what kind of music plays.

![Focus Music](Focus_music.png)

**Suggestion:** Allow linking Spotify or YouTube Music for focus sessions.

### 5. No way to add long-term goals on the Motivation page

Under "Your goals" on the Motivation page, I could not find a way to add long-term goals. There is no option in that window. When no goals have been set yet, the window should offer a way to add one.

![Motivation goals](Your_goals.png)

**Suggestion:** Show an "Add a goal" button when the goals list is empty.

## Improvements to the onboarding program

Overall, I think the onboarding is good. Two things would make it clearer:

- **Proof of completed tasks:** Explain where to include screenshots so the review bot does not flag them.
- **Clean Code milestone:** Put issues #67 and #73 in separate files instead of one `clean_code.md`. The bot can only read up to a limit of characters or lines, so one long file is harder to review.

## Bug logged in team.focusbear.io

**Bug summary:** Duplicate manual entries with the same start time and end time.

**Outcome:** The team reviewed it and decided it was not an actual issue to manage.

![Bug report](image.png)

---

# Part 3: Getting to Know the Focus Bear Product (Backend Developer Intern)

## 4 things I learned

### 1. The app decides what counts as a task

- **What I learned:** In a focus session, Focus Bear checks each site that I open. It decides whether the site matches my current objective and shows an alignment score. It uses AI plus my block and allow lists. It prompts me if I try to open a blocked website.
- **Why it matters to my backend role:** The app needs a reliable way to compare a user's stated objective with their activity. It also needs to store and apply each user's block and allow lists. I have not seen how this works inside the code, so I want to learn how the alignment check is built.

### 2. Morning and evening routines are not optional

- **What I learned:** I cannot skip the habit routine and go straight into my day. The app blocks the rest of my computer until I complete the routine or press the skip button.
- **Why it matters to my backend role:** The backend likely needs to record which habits are required, which can be skipped, and whether each one was completed. That is more complex than it looks from the outside.

### 3. The app remembers my patterns over time

- **What I learned:** At the end of the day, I get a report on my focus sessions, breaks, and habit participation.
- **Why it matters to my backend role:** The app must log each user's activity during the day so it can be summarised later. This needs careful data storage and querying.

### 4. Settings should be the same on every device

- **What I learned:** Focus Bear is available on more than one device. I have only tested it on my Windows laptop, so this is my assumption about how it works.
- **Why it matters to my backend role:** If settings, streaks, and block lists are shared across devices, the backend must keep them in sync. It would also need to handle changes made on one device while another is offline.

---

# Part 4: Reflection

## What did I learn that I did not know before?

Many of the features that make the app supportive rather than restrictive are hidden behind the interface. The app is not only a set of nice UI elements, such as blocking distracting websites. It also makes decisions all the time, such as judging whether an activity is useful for my current objective.

## Did I spot anything in the help centre that seems out of date or inconsistent?

The key features and tutorials are consistent with what I saw. The help articles use images from macOS. I use Windows, and I did not see any differences in the steps.