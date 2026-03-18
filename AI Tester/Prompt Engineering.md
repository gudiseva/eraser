<p><a target="_blank" href="https://app.eraser.io/workspace/ZYXrOK8MpdU5rBIHgaXt" id="edit-in-eraser-github-link"><img alt="Edit in Eraser" src="https://firebasestorage.googleapis.com/v0/b/second-petal-295822.appspot.com/o/images%2Fgithub%2FOpen%20in%20Eraser.svg?alt=media&amp;token=968381c8-a7e7-472a-8ed6-4a6626da5501"></a></p>

- Telling of the LLM, the right thing is called Prompting


To get the best result from LLM, the following are required:

1. Prompt Engineering
2. RAG
3. Fine Tuning


## Prompting
- refers to the practice of providing the specific instructions or input to an AI model to obtain desired responses.
> Prompt Engineering = Communication Skill

- You are **NOT** asking AI.  You are **INSTRUCTING** AI.


### Core Principals
1. Be Specific
2. Provide Context
3. Define Output Format (desired output)
4. Set Constraints - use only the document (PRD) provided




## 7 Step formula for effective Prompt Engineering
1. Define the Goal
2. Gather the Context
3. Choose Prompting Strategy
4. Structure the Prompt
5. Add Constraints
6. Test and Iterate
7. Document and Reuse




### Step 1: Define the Goal
Ask yourself:

- What exactly do I need?
- What will I do with the output?
- What does success look like?


> Plan -> Generate (AI) - BLAST - RULE 1 - (95% Plan, 5% Action)



### Step 2: Gather Context
- [ ] Collect all relevant information:
- [ ] PRD / Requirements document
- [ ] API documentation
- [ ] Screenshots / UI mockups
- [ ] Error logs
- [ ] Previous test cases
- [ ] Constraints / Limitations
- [ ] JIRA ID, Storeis, Epic, BRD, Confluence, Wireframes, Figma design, Architecture Diagrams, Miro Reports, etc.
**Rule**: More context = Better output



### Step 3: Choose Prompting Strategy


| Situation | Strategy |  |
| ----- | ----- | ----- |
| Simple, standard task | Zero-Shot | No Example |
| Custom format needed | Few-Shot | 2-3 Examples |
| Complex analysis | Chain-of-Thought | Ask him to think about it |
| Domain expertise needed | Role-Based | Act as QA |


## Step 4: Structure the Prompt
Use a framework (**RICE POT** recommended):

```
ROLE: [EXPERTISE]
Instructions: [Purpose]
CONTEXT: [Background info]
EXPECTED: [Success criteria]
PARAMETERS: [Constraints]
OUTPUT: [Format]
TASK: [Specific instruction]
```


#### Task: Write a Selenium Cde for the Sales Force Login
> RICE POT to create the Selenium Code for the Salesforce Login



#### R
#### I
[XXX] <- It is a direct instruction to the LLM

[Critical]

[Mandatory]

[Output]

[Don't]

[Generate]

[Don't Use]



#### C - Context


#### E - Example


#### P - Parameters


#### O - Output


#### T - Tone


---

## Quick Checklist for Good Prompt
- [ ] Goal is clearly defined
- [ ] All relevant context is included
- [ ] Approprite strategy is chosen
- [ ] Prompt is well-structured
- [ ] Anti-hallucination constraints added
- [ ] Output format specified
- [ ] Task instruction is specific


## Common Mistakes to Avoid
| Mistake | Impact | Fix |
| ----- | ----- | ----- |
| Skipping context | Poor, generic output | Gather all docs first |
| No constraints | Hallucinations | Add anti-hallucination rules |
| Vague task | Inconsistent results | Be specific |
| No format | Unstructured output | Specify format |
| No iteration | Suboptimal results | Test and refine |


## Prompt Frameworks: STAR, CLEAR, CRISP
- These are small frameworks that are simple and can be used anywhere.


### 1. CRAP / CRISP Framework
C - Context

R - Role

A - Action

I - Instruction

P - Parameters (optional)



### 2. RACE
R - Role

A - Action

C - Context

E - Expectation

- Used for Bug creation


### 3. COAST
C - Context

O - Objective

A - Action

S - Style

T - Tone



### 4. CLASSIC "Role Model Prompting"
"You are X, behave like Y, produce Z."

Feels like assigning an actor their script.



## Checklist: Is Your Prompt Complete?
- [ ] Role defined
- [ ] Context is provided
- [ ] Task is specifi and quantified
- [ ] Constraints prevent hallucinations, conditions
- [ ] Output format specified
- [ ] Terminology defined, if needed


## Prompts Templates (templates/)
**Purpose**: Ready to use prompt templates for test case generation

**Chapter**: 2 - Prompt Engineering



### Template 1: Basic Test Case Generation (RTCFR)
ROLE - You are a Senior QA Engineer.

TASK - Generate [NUMBER] test cases for [FEATURE].

CONSTRAINTS

- Use ONLY the provided requirements <- This will ensure that it will not do hallucination
- Do NOT assume undocumented behaviour
- If information is missing, state "Not specified"


FORMAT:

| Test ID | Description | Pre-conditions | Steps | Expected Result | Priority |



<!--- Eraser file: https://app.eraser.io/workspace/ZYXrOK8MpdU5rBIHgaXt --->