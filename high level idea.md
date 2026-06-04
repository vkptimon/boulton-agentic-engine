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
- We will have 4 agents.
  - Ingestion Agent: Will ingest the content of the resume and store it as vector embeddings for retrieval later
  - ATS Score Calculating Agent: Will calculate the score/matching percentage of the resume against the pasted JD
  - Content Boosting Agent: Will recommend the contents which would increase the ATS Score of the resume
    - It should only touch "Work Experience", "Projects" and "Technical Skills" sections and shouldn't touch the rest
  - Final PDF Generating Agent: After the recommend changes are selected, it'll create a new pdf out of it with a defined name
- We will upload the resume as a latex file, so it would be better to make changes and generate PDF again
- Or if the readability of PDF is better than Latex, then we will go with PDF then
- For the frontend UI, we will keep it minimal in terms of the input box or the overall layout of the website
- But we have to bring in 2 things
  - Latex editor embedded into it, so if any of the suggestions provided by the AI are not to the user's liking, then they could manually edit them
  - We have to show diffs of the changes on top of the central master resume in color coded way
  - Highlight in green for a newly added bullet point or line; Red Strikethrough for a bullet point/line that's removed; Highlight in blue for a line that's edited

## Tech Stack
- For the Agent, Python is the obvious choice, with LangGraph as the framework
- For the Backend part, gluing the agent and frontend, i'm thinking of Go/Java
- For the Frontend, something lean and performant like Vue.js or Solid.js
- The main agenda of choosing a structured stack is to become good at them by practice and application

## Main Agenda of the Project
- To build a tool, assisting me in faster and better suiting my resume towards a given job application
- To learn the internal workings and implementing RAG, Agentic workflows
- To implement and get a deeper look at microservice architecture
- Become proficient in basic UI building
