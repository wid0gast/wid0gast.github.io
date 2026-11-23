---
layout: page
title: Job Agent
description: A job-application agent I built for my own search. It finds roles, drafts the application, and waits for my OK before sending anything.
img: assets/img/projects/job-agent/ranked.jpg
importance: 1
github: https://github.com/wid0gast/job-agent
---

I built this during my own internship search. The usual advice is to apply to as many places as you can, and doing that by hand means pasting the same resume and the same answers into a hundred forms. I wanted help with the boring parts (finding roles, reading long postings, tailoring a resume, filling in forms) without ending up with a pile of generic applications I'd never have sent myself.

So Job Agent does the legwork and I make the decisions. It runs on my laptop, uses Claude, and the code is [on GitHub](https://github.com/wid0gast/job-agent) if you want to try it.

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/projects/job-agent/ranked.jpg" title="Ranked jobs" alt="Ranked list of jobs, each scored with a one-line reason" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption">
  Every job gets a score and a one-line reason for it. (All screenshots use a made-up candidate.)
</div>

## How it works

It starts by finding jobs. It reads the big community-maintained internship and new-grad lists, then goes looking on the job boards companies actually post to (Greenhouse, Lever, Ashby, Workday and a few others). A lot of roles, especially at startups, never show up on LinkedIn, so this turns up companies I wouldn't have found otherwise. If the same role is posted in two places it only counts once, and anything I've already applied to gets set aside.

Then it sorts them. Keyword matching is free, so every job gets a rough score that way. A small, cheap model looks at the top 120 more carefully. Only the jobs I actually want to pursue get the expensive treatment: the best model reads the whole posting and goes through it one requirement at a time, saying whether my resume covers it and pointing to the line that shows it.

<div class="row mt-3">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="lazy" path="assets/img/projects/job-agent/fit.jpg" title="Fit check" alt="Fit check table: each requirement, whether it is met, and the evidence" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="lazy" path="assets/img/projects/job-agent/answers.jpg" title="Drafted answers" alt="Drafted application answers, each with its source" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption">
  The fit check is allowed to say no. Every drafted answer shows where it came from.
</div>

For a job that looks good, it tailors my resume, reads up on the company, and drafts every answer on the form, each with a note on where it got the information. A second model then reads the finished application the way a hiring manager skimming a stack would, and points out the lines that would make them stop reading.

It also tells me how to apply, not just whether to. Some jobs get "reach out first", with a short email drafted to someone at the company. Others get "apply", or "think twice" if the posting has been open for months or keeps getting reposted, which usually means nobody is really hiring for it.

## Keeping it honest

I'd only use something like this if I could trust everything it sends under my name, and that shaped most of the design.

It can't make things up. Every answer has to come from my resume, my profile, or something I've told it directly. If a tailored resume has a number in it that isn't in my real one, the code throws it out.

It ignores tricks hidden in job pages. Some postings now hide text aimed at AI tools, like "ignore your instructions and…". The model only sees what a person looking at the page would see, and nothing on a page can make it less careful.

Nothing gets sent without me. The assistant can prepare an application and ask me to submit it, but submitting takes a click from me. The submit step itself is plain code that types in exactly what I approved, with no model involved. It also leaves CAPTCHAs and verification codes to me, caps how many applications go out per day and per company, and keeps a record of every step it took for each application, so I can always see why it did something.

<div class="row mt-3">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="lazy" path="assets/img/projects/job-agent/review.jpg" title="Approval queue" alt="An application in the approval queue, with questions only the applicant can answer flagged" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid loading="lazy" path="assets/img/projects/job-agent/assistant.jpg" title="Assistant" alt="Assistant chat checking an application and asking for confirmation before submitting" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption">
  Questions only I can answer get flagged. The chat assistant can do anything the dashboard does, but it can only ask to submit.
</div>

## Some details

It's a Python app with a local web dashboard, plus a chat assistant that uses the same tools, so I can just type "find my five best matches and prepare them". Different jobs go to different models: Haiku for skimming hundreds of job titles, Sonnet for reading resumes and doing research, and Opus for the writing and the fit checks. It runs on a regular Claude subscription or an API key.

A lot of the work went into making the writing sound like a person wrote it. There's a [writing guide](https://github.com/wid0gast/job-agent/blob/main/docs/writing-guide.md) based on what recruiters say they look for, code that catches stock phrases and answers that run too long, and an [eval](https://github.com/wid0gast/job-agent/blob/main/docs/writing-eval.md) on real postings.

Testing an agent that submits real applications is tricky, so the test suite runs the whole flow against fake copies of Greenhouse, Lever, Ashby, and Workday, hidden-text traps included, without calling a model.

I built this as part of an agentic AI workshop. The setup instructions and a longer guide are in the [repo](https://github.com/wid0gast/job-agent).
