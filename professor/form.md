# form creation - Google App Script
```
Generate a complete Google Apps Script (FormApp) code to automatically create a Google Quiz.

Requirements:
- Quiz title: "<TITLE>"
- Use Google Apps Script (FormApp only)
- Put everything in a single file (Code.gs)
- Enable quiz mode with auto-grading
- Each question should be MCQ with marks
- Collect real Google account email addresses (no manual email question)
- Limit to 1 response per user
- Use only supported methods (do NOT use deprecated APIs like setRequireLogin)
- Use item.createChoice(), not FormApp.createChoice()

Form Structure:
1. Personal information section:
   - Full Name (required)
   - Date of Birth (required)
   - Department (dropdown: CSE, ECE, EEE, IT, MECH)

2. Quiz section:
   - Create 10 MCQs relevant to the quiz title
   - Each question has 4 options
   - Exactly one correct answer
   - 1 mark per question

Technical Notes:
- Use:
  - form.setCollectEmail(true)
  - form.setLimitOneResponsePerUser(true)
  - form.setIsQuiz(true)
- Define quiz data in a JSON object above the main function
- Log the Edit URL and Public URL at the end
- Ensure the script runs without errors on a college Google Workspace account

Output:
- Only the final, ready-to-run Apps Script code
- No explanations unless asked

```
```
/**************** FORM DATA ****************/
const formData = {
  title: "CIA Triad Assessment",
  isQuiz: true,

  personalInfo: [
    { type: "short", text: "Full Name", required: true },
    { type: "date", text: "Date of Birth", required: true },
    {
      type: "dropdown",
      text: "Department",
      options: ["CSE", "ECE", "EEE", "IT", "MECH"],
      required: true
    }
  ],

  questions: [
    {
      text: "What does CIA stand for in information security?",
      options: [
        "Confidentiality, Integrity, Availability",
        "Control, Inspection, Authorization",
        "Confidentiality, Information, Access",
        "Central Intelligence Agency"
      ],
      correct: 0,
      points: 1
    },
    {
      text: "Which CIA principle ensures data is not altered improperly?",
      options: ["Confidentiality", "Integrity", "Availability", "Authentication"],
      correct: 1,
      points: 1
    },
    {
      text: "Availability mainly ensures:",
      options: [
        "Data secrecy",
        "System uptime and accessibility",
        "User authentication",
        "Encryption"
      ],
      correct: 1,
      points: 1
    },
    {
      text: "Which control best supports confidentiality?",
      options: ["Encryption", "Backups", "Load balancing", "Redundancy"],
      correct: 0,
      points: 1
    },
    {
      text: "Checksums and hashes help ensure:",
      options: ["Availability", "Integrity", "Confidentiality", "Authorization"],
      correct: 1,
      points: 1
    },
    {
      text: "Which is a threat to availability?",
      options: ["Phishing", "DDoS attack", "Data leakage", "Weak password"],
      correct: 1,
      points: 1
    },
    {
      text: "Least privilege supports which CIA principle most?",
      options: ["Integrity", "Availability", "Confidentiality", "Redundancy"],
      correct: 2,
      points: 1
    },
    {
      text: "Which CIA principle is affected by server failure?",
      options: ["Integrity", "Confidentiality", "Availability", "Authentication"],
      correct: 2,
      points: 1
    },
    {
      text: "Digital signatures mainly ensure:",
      options: [
        "Integrity and authenticity",
        "Availability",
        "Confidentiality",
        "Redundancy"
      ],
      correct: 0,
      points: 1
    },
    {
      text: "Backups primarily help with:",
      options: ["Confidentiality", "Integrity", "Availability", "Authorization"],
      correct: 2,
      points: 1
    }
  ]
};

/**************** MAIN FUNCTION ****************/
function createCIATriadQuiz() {

  // Create the form
  const form = FormApp.create(formData.title);

  /******** RESPONSE SETTINGS (IMPORTANT) ********/

  // 1. Collect real Google account email (VERIFIED)
  form.setCollectEmail(true);

  // 2. Force Google sign-in
  form.setRequireLogin(true);

  // 3. Limit to one response per user
  form.setLimitOneResponsePerUser(true);

  // 4. Enable Quiz mode
  if (formData.isQuiz) {
    form.setIsQuiz(true);
  }

  /******** PERSONAL INFORMATION ********/
  formData.personalInfo.forEach(q => {
    let item;

    if (q.type === "short") {
      item = form.addTextItem();
    }
    else if (q.type === "date") {
      item = form.addDateItem();
    }
    else if (q.type === "dropdown") {
      item = form.addListItem();
      item.setChoices(q.options.map(o => item.createChoice(o)));
    }

    item.setTitle(q.text);
    item.setRequired(q.required || false);
  });

  // Section break
  form.addPageBreakItem().setTitle("CIA Triad MCQs");

  /******** MCQs WITH MARKS ********/
  formData.questions.forEach(q => {
    const item = form.addMultipleChoiceItem();
    item.setTitle(q.text);
    item.setPoints(q.points);

    const choices = q.options.map((opt, i) =>
      item.createChoice(opt, i === q.correct)
    );

    item.setChoices(choices);
    item.setRequired(true);
  });

  // Log URLs
  Logger.log("Form Edit URL: " + form.getEditUrl());
  Logger.log("Form Public URL: " + form.getPublishedUrl());
}

```