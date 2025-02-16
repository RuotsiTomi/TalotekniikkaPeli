<Talotekniikka TTMI23KM>
<html lang="fi">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Kaksikielinen Quiz</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f0f0f0;
      margin: 0;
      padding: 20px;
    }
    .container {
      max-width: 600px;
      margin: auto;
      background: #fff;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    .question {
      font-size: 1.2em;
      margin-bottom: 10px;
    }
    .options button {
      display: block;
      width: 100%;
      padding: 10px;
      margin: 8px 0;
      font-size: 1em;
      border: 1px solid #ccc;
      border-radius: 5px;
      background: #fff;
      cursor: pointer;
      transition: background 0.3s;
    }
    .options button:hover:not(:disabled) {
      background: #e0e0e0;
    }
    .options button.correct {
      background: #c8e6c9;
      border-color: #388e3c;
      color: #388e3c;
    }
    .options button.incorrect {
      background: #ffcdd2;
      border-color: #d32f2f;
      color: #d32f2f;
    }
    #nextButton {
      padding: 10px 20px;
      font-size: 1em;
      cursor: pointer;
      margin-top: 10px;
      display: none;
    }
    #feedback {
      margin-top: 10px;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <div class="container">
    <div id="quiz">
      <div class="question" id="questionText">Kysymys tulee tähän...</div>
      <div class="options" id="optionsContainer">
        <!-- Vastausvaihtoehdot lisätään tähän JavaScriptillä -->
      </div>
      <div id="feedback"></div>
      <button id="nextButton">Seuraava kysymys</button>
    </div>
  </div>

  <script>
    // Kysymysten ja vastausten data
    const quizData = [
      {
        question: "Mikä on ruotsinkielinen vastine sanalle 'etäopetus'?",
        options: [
          "studera på distans",
          "en distansstuderande",
          "distansstudier",
          "opiskella etänä"
        ],
        correctIndex: 2
      },
      {
        question: "Mikä on oikea käännös lauseelle: 'LVI-suunnittelija suunnittelee lämmitys-, vesi- ja ilmastointijärjestelmiä rakennuksiin.'?",
        options: [
          "En VVS-planerare planerar värme-, vatten- och ventilationssystem för byggnader.",
          "En VVS-montör installerar och underhåller VVS-system.",
          "En konsult erbjuder expertistjänster inom VVS.",
          "En underhållsingenjör ansvarar för VVS-systemens funktion."
        ],
        correctIndex: 0
      },
      // Lisää kysymyksiä halutessasi
    ];

    let currentQuestionIndex = 0;
    const questionText = document.getElementById("questionText");
    const optionsContainer = document.getElementById("optionsContainer");
    const feedback = document.getElementById("feedback");
    const nextButton = document.getElementById("nextButton");

    // Lataa kysymys
    function loadQuestion() {
      // Nollataan palaute ja piilotetaan seuraava-painike
      feedback.textContent = "";
      nextButton.style.display = "none";
      // Tyhjennetään aiemmat vastausnapit
      optionsContainer.innerHTML = "";

      const currentQuestion = quizData[currentQuestionIndex];
      questionText.textContent = currentQuestion.question;

      currentQuestion.options.forEach((option, index) => {
        const button = document.createElement("button");
        button.textContent = option;
        button.addEventListener("click", () => selectOption(index, button));
        optionsContainer.appendChild(button);
      });
    }

    // Käsittele vastausnapin klikkaus
    function selectOption(selectedIndex, buttonElement) {
      // Estetään lisäklikkaukset
      const buttons = optionsContainer.querySelectorAll("button");
      buttons.forEach(btn => btn.disabled = true);

      const currentQuestion = quizData[currentQuestionIndex];
      if(selectedIndex === currentQuestion.correctIndex) {
        buttonElement.classList.add("correct");
        feedback.textContent = "Oikein!";
      } else {
        buttonElement.classList.add("incorrect");
        feedback.textContent = "Väärin!";
        // Korostetaan oikeaa vastausta
        buttons[currentQuestion.correctIndex].classList.add("correct");
      }
      nextButton.style.display = "block";
    }

    // Siirry seuraavaan kysymykseen
    nextButton.addEventListener("click", () => {
      currentQuestionIndex++;
      if(currentQuestionIndex < quizData.length) {
        loadQuestion();
      } else {
        questionText.textContent = "Testi suoritettu!";
        optionsContainer.innerHTML = "";
        feedback.textContent = "";
        nextButton.style.display = "none";
      }
    });

    // Aloitetaan ensimmäisellä kysymyksellä
    loadQuestion();
  </script>
</body>
</html>
