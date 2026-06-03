# 03rd June

- The high level plan is to generate an agentic system that can help identify the pitfalls of the master resume.
- Dividing all the responsibilities among different agents, each agent having a single task/action performing atmost.

## High Level Flow
- We will paste the JD in a textbox/input place in the GUI
- The master resume will always be maintained in the profile section
  - It can be changed from time but we don't have to upload it everytime
- It will first try to calculate the matching percentage and show it to us in a color-coded way
- If the matching percentage is less than 75%, it'll suggest us to make changes in our resume
- Upon clicking on the `boost resume` button, we will be shown a loader and after a while it will show some suggestions
- Points that need to be hidden will be striked off, but that need a little tinkering will be highlighted in blue
- If the changes are satisfactory, we will click on the "Approve" button
- We do also have a "Retry" button, with only one additional retry
- And also a "Reject" button to completely discard the suggested changes
- It will create a new resume with the approved changes, if any, with the filename being the master-resume-day-month-year.pdf

## Technical Implementation
