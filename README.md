# QCM_physique_4eme
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>QCM Physique - 4e</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: url('https://upload.wikimedia.org/wikipedia/commons/thumb/5/55/Chemical_structure_template.svg/2560px-Chemical_structure_template.svg.png') repeat;
      background-color: #2c3e50;
      color: #fff;
    }
    .container {
      max-width: 700px;
      margin: 40px auto;
      background-color: rgba(255, 255, 255, 0.1);
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.3);
      position: relative;
      text-align: center;
    }
    .question {
      margin-bottom: 30px;
    }
    .question h2 {
      font-size: 1.3em;
    }
    .option {
      display: block;
      margin: 10px auto;
      padding: 10px;
      background: rgba(255,255,255,0.2);
      border-radius: 8px;
      cursor: pointer;
      transition: background 0.3s;
      max-width: 400px;
    }
    .option:hover {
      background: rgba(255,255,255,0.4);
    }
    .correct {
      background-color: #2ecc71 !important;
    }
    .incorrect {
      background-color: #e74c3c !important;
      text-decoration: line-through;
    }
    .summary, .resultsPage {
      background-color: rgba(255,255,255,0.15);
      padding: 15px;
      border-radius: 10px;
      margin-top: 20px;
    }
    .summary h3, .resultsPage h3 {
      color: #f1c40f;
    }
    .hidden {
      display: none;
    }
    #timer {
      position: absolute;
      top: 15px;
      right: 20px;
      font-size: 1.2em;
      font-weight: bold;
      color: #f39c12;
    }
    #to-summary {
      position: fixed;
      bottom: 20px;
      right: 20px;
      background-color: #3498db;
      border: none;
      border-radius: 50%;
      width: 50px;
      height: 50px;
      font-size: 1.5em;
      color: white;
      cursor: pointer;
      display: none;
    }
    .feedback {
      text-align: center;
      margin-top: 30px;
    }
    .feedback img {
      max-width: 150px;
      animation: bounce 1s infinite;
    }
    .resultsPage img {
      max-width: 200px;
    }
    .recap-button {
      margin-top: 20px;
      background: #27ae60;
      color: white;
      padding: 10px 20px;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      font-size: 1em;
    }
    @keyframes bounce {
      0%, 100% { transform: translateY(0); }
      50% { transform: translateY(-10px); }
    }
  </style>
</head>
<body>

<div class="container">
  <div id="timer">30s</div>
  <div id="quiz"></div>
  <div id="results" class="hidden"></div>
  <div id="summary" class="hidden"></div>
</div>

<button id="to-summary" onclick="showSummary()">➡</button>

<script>
const questions = [
  { question: "Lors d’un changement d’état, la masse d’un corps :", options: ["diminue", "augmente", "reste la même"], answer: 2 },
  { question: "Le passage de l’état solide à l’état liquide s’appelle :", options: ["la condensation", "la fusion", "la vaporisation"], answer: 1 },
  { question: "Lorsqu’un liquide se transforme en gaz sans ébullition, on parle de :", options: ["sublimation", "évaporation", "solidification"], answer: 1 },
  { question: "Le passage direct de l’état solide à l’état gazeux s’appelle :", options: ["sublimation", "liquéfaction", "condensation"], answer: 0 },
  { question: "Quand de l’eau passe de l’état liquide à l’état solide, elle subit :", options: ["la fusion", "la solidification", "la vaporisation"], answer: 1 },
  { question: "Pendant un changement d’état, la température :", options: ["augmente", "reste constante", "diminue"], answer: 1 },
  { question: "Le passage de l’état gazeux à l’état liquide se nomme :", options: ["la condensation", "la sublimation", "la fusion"], answer: 0 },
  { question: "Lorsque l’on chauffe de l’eau à 100°C sous pression atmosphérique normale, elle :", options: ["se solidifie", "bout", "gèle"], answer: 1 },
  { question: "L’air est principalement composé de :", options: ["dioxygène et azote", "dioxyde de carbone et vapeur d’eau", "hydrogène et hélium"], answer: 0 },
  { question: "Pendant la fusion d’un glaçon, l’énergie thermique :", options: ["est libérée par le glaçon", "est absorbée par le glaçon", "ne change pas"], answer: 1 }
];

let current = 0;
let score = 0;
let userAnswers = [];
let timer;
let timeLeft = 30;

const quizContainer = document.getElementById("quiz");
const summaryContainer = document.getElementById("summary");
const resultsContainer = document.getElementById("results");
const timerDisplay = document.getElementById("timer");
const toSummaryBtn = document.getElementById("to-summary");

function showQuestion(index) {
  clearInterval(timer);
  timeLeft = 30;
  updateTimer();
  timer = setInterval(() => {
    timeLeft--;
    updateTimer();
    if (timeLeft <= 0) {
      clearInterval(timer);
      checkAnswer(null);
    }
  }, 1000);

  const q = questions[index];
  quizContainer.innerHTML = `
    <div class="question">
      <h2>Question ${index + 1} : ${q.question}</h2>
      ${q.options.map((opt, i) => `<div class="option" onclick="checkAnswer(${i})">${opt}</div>`).join('')}
    </div>
  `;
}

function updateTimer() {
  timerDisplay.textContent = `${timeLeft}s`;
}

function checkAnswer(selected) {
  clearInterval(timer);
  const q = questions[current];
  const options = document.querySelectorAll(".option");

  options.forEach((opt, i) => {
    opt.onclick = null;
    if (i === q.answer) {
      opt.classList.add("correct");
    } else if (i === selected) {
      opt.classList.add("incorrect");
    }
  });

  userAnswers.push({ question: q.question, selected: selected, correct: q.answer });
  if (selected === q.answer) score++;

  setTimeout(() => {
    current++;
    if (current < questions.length) {
      showQuestion(current);
    } else {
      showResults();
    }
  }, 1000);
}

function showResults() {
  quizContainer.classList.add("hidden");
  timerDisplay.style.display = 'none';

  let html = `<h2>Votre score : ${score}/${questions.length}</h2>`;

  if (score >= 8) {
    html += `<h3>🎉 Excellent travail !</h3><img src="https://media.giphy.com/media/3o6ZtaO9BZHcOjmErm/giphy.gif">`;
  } else if (score >= 5) {
    html += `<h3>👍 Bon effort !</h3><img src="https://media.giphy.com/media/111ebonMs90YLu/giphy.gif">`;
  } else {
    html += `<h3>😟 Ne baisse pas les bras !</h3><img src="https://media.giphy.com/media/9Y5BbDSkSTiY8/giphy.gif">`;
  }

  html += `<br><button class="recap-button" onclick="showSummary()">Voir les réponses</button>`;
  resultsContainer.innerHTML = html;
  resultsContainer.classList.remove("hidden");
}

function showSummary() {
  resultsContainer.classList.add("hidden");
  summaryContainer.classList.remove("hidden");

  let html = `<h2>Récapitulatif des réponses</h2>`;
  userAnswers.forEach((entry, i) => {
    html += `<div class="summary">
      <h3>Question ${i + 1} : ${entry.question}</h3>`;
    if (entry.selected === entry.correct) {
      html += `<p>✅ Réponse juste : ${questions[i].options[entry.correct]}</p>`;
    } else {
      let wrong = entry.selected !== null ? questions[i].options[entry.selected] : "(aucune réponse)";
      html += `<p>❌ Votre réponse : <span class="incorrect">${wrong}</span><br>✅ Réponse correcte : ${questions[i].options[entry.correct]}</p>`;
    }
    html += `</div>`;
  });
  html += `<button class="recap-button" onclick="location.reload()">Recommencer le QCM</button>`;
  summaryContainer.innerHTML = html;
}

showQuestion(current);
</script>

</body>
</html>
