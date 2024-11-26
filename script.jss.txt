// Replace with your actual Google Sheet ID and API Key
const sheetId = "2PACX-1vRKV_MzdBl0cQgtABJYqw-HmNsbrRIYkLUbmj0KC4UeYU2yv79mnMM7oZAy0F01NUnR37oI_ZL_OExS"; //
const apiKey = "AIzaSyDIcklZ8oZtYUSZsbvarvPnPEMITpZMIuQ";   // Replace with your API Key
const sheetRange = "Sheet1!A2:F";       // Sheet range for the questions and answers

// Function to fetch trivia data from Google Sheets
async function fetchTriviaData() {
  const url = `https://sheets.googleapis.com/v4/spreadsheets/${sheetId}/values/${sheetRange}?key=${apiKey}`;

  try {
    const response = await fetch(url);
    const data = await response.json();
    if (data.values) {
      const triviaQuestions = data.values.map(row => {
        return {
          question: row[0],
          answers: [row[1], row[2], row[3], row[4]],
          correctAnswer: row[5]
        };
      });
      startQuiz(triviaQuestions);
    }
  } catch (error) {
    console.error("Error fetching trivia data: ", error);
  }
}

// Initialize the trivia quiz
function startQuiz(triviaQuestions) {
  let currentQuestionIndex = 0;

  // Display the current question and its answers
  function displayQuestion() {
    const questionData = triviaQuestions[currentQuestionIndex];
    const questionElement = document.getElementById("question");
    questionElement.textContent = questionData.question;

    const answersContainer = document.getElementById("answers");
    answersContainer.innerHTML = ''; // Clear previous answers
    questionData.answers.forEach(answer => {
      const answerButton = document.createElement("button");
      answerButton.textContent = answer;
      answerButton.onclick = () => checkAnswer(answer);
      answersContainer.appendChild(answerButton);
    });

    document.getElementById("next-btn").disabled = true;
  }

  // Check if the selected answer is correct
  function checkAnswer(selectedAnswer) {
    const questionData = triviaQuestions[currentQuestionIndex];
    if (selectedAnswer === questionData.correctAnswer) {
      alert("Correct!");
    } else {
      alert("Wrong! The correct answer was: " + questionData.correctAnswer);
    }

    document.getElementById("next-btn").disabled = false;
  }

  // Move to the next question
  function nextQuestion() {
    currentQuestionIndex++;
    if (currentQuestionIndex < triviaQuestions.length) {
      displayQuestion();
    } else {
      alert("You've completed the quiz!");
      currentQuestionIndex = 0;
      displayQuestion();
    }
  }

  // Bind the next button
  document.getElementById("next-btn").addEventListener("click", nextQuestion);

  // Load the first question
  displayQuestion();
}

// Fetch the trivia data and start the quiz
fetchTriviaData();
