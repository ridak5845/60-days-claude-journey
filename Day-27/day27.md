# 60 Days of Claude AI Challenge 

# Day 27/60 — Prior Authorization Story Simulator

**Challenge: Build a Prior Authorization Story Simulator — learn healthcare workflows through interactive conversations.**

What I built: A single-file, self-contained HTML chat simulator that walks through a complete Prior Authorization (PA) journey as an 8-scene branching conversation between a patient (Rahul) and a healthcare operations specialist (Priya) — diagnosis → insurance roadblock → explanation → review → denial → appeal → approval → takeaways. Every scene ends in a 2-option choice that changes the dialogue that follows.

##  Screenshots

#### Start screen
The story opens with a short framing line before any dialogue begins — nothing auto-plays until the learner chooses to start.

![screenshot](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-27/Start%20Screen.png)

####  Interactive story conversation with Rahul and Priya

![Screenshot](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-27/Scene-1.png)

![Screenshot](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-27/Scene-2.jpeg)

![Screenshot](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-27/Scene-3.png)


####  Insurance review and denial workflow

![Screenshot](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-27/Scene-4.jpeg)

![Screenshot](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-27/Scene-5.png)

#### Appeal process and final approval

![Screenshot](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-27/Scene-6.png)

![Screenshot](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-27/Scene-7.png)

#### Patient and healthcare system takeaways screen

![Screenshot](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-27/Scene-8.png)

#### Generated HTML File

[HTML FILE](https://github.com/ridak5845/60-days-claude-journey/blob/main/Day-27/prior_authorization_story_simulator.html)

**The simulator presents an interactive, choice-driven story that explains the Prior Authorization process through conversations between a patient and a healthcare operations specialist.**


### Key Takeaways from the Prior Authorization Journey

- Prior Authorization (PA) is a process insurers use to verify that a treatment is medically necessary before approving coverage.
- The workflow follows **Provider → Prior Authorization Request → Payer**, with the payer reviewing the submitted clinical information.
- Insurance reviewers evaluate eligibility, clinical documentation, ICD-10 diagnosis codes, and step therapy history before making a decision.
- A PA denial is not always final; missing documentation can often be addressed through a formal appeal.
- A strong appeal includes updated clinical records, step therapy documentation, and a Letter of Medical Necessity.
- Once approved, the Prior Authorization remains on file for future refills as long as the treatment and insurance plan remain unchanged.


### My Learnings

-Building this simulator helped me understand that Prior Authorization is more than an insurance approval step—it is a structured healthcare workflow involving collaboration between providers and payers.

-One of my biggest learnings was that many PA denials happen because of incomplete documentation rather than the treatment itself being inappropriate. A well-prepared appeal with the required clinical evidence can often lead to approval.

-This project also gave me a better understanding of how healthcare operations balance patient care, clinical documentation, and insurance requirements while aiming to reduce treatment delays.
