<Talotekniiikka TTMI23KM>
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
      max-width: 900px;
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
    .direction {
      font-style: italic;
      margin-bottom: 10px;
      color: #555;
    }
    .options button, .word-button {
      display: inline-block;
      margin: 5px;
      padding: 8px 12px;
      font-size: 1em;
      border: 1px solid #ccc;
      border-radius: 5px;
      background: #fff;
      cursor: pointer;
      transition: background 0.3s;
    }
    .options button:hover:not(:disabled), .word-button:hover:not(:disabled) {
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
    #nextButton, #submitSentence, #clearSentence {
      padding: 10px 20px;
      font-size: 1em;
      cursor: pointer;
      margin-top: 10px;
    }
    #feedback {
      margin-top: 10px;
      font-weight: bold;
    }
    #sentenceArea {
      border: 1px solid #ccc;
      min-height: 40px;
      padding: 10px;
      margin-top: 10px;
      font-size: 1.1em;
      background: #f9f9f9;
    }
    #wordButtonsContainer {
      margin-top: 10px;
    }
  </style>
</head>
<body>
  <div class="container">
    <div id="quiz">
      <!-- Käännössuunta-info näkyy monivalintatehtävissä -->
      <div class="direction" id="directionInfo"></div>
      <div class="question" id="questionText">Kysymys tulee tähän...</div>
      <div id="taskArea">
        <!-- Tässä alueessa joko monivalintavaihtoehdot tai lauseen muodostus -elementit näytetään -->
      </div>
      <div id="feedback"></div>
      <!-- Painikkeet: monivalintatehtävissä käytetään vain Seuraava kysymys -->
      <button id="nextButton" style="display:none;">Seuraava kysymys</button>
      <!-- Lauseen muodostustehtävien painikkeet -->
      <button id="submitSentence" style="display:none;">Lähetä vastaus</button>
      <button id="clearSentence" style="display:none;">Tyhjennä</button>
    </div>
  </div>

  <script>
    // Apufunktio vastausvaihtoehtojen sekoittamiseen
    function shuffle(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    }

    // Quiz-datan rakenne sisältää kaksi tyyppiä:
    // - type: "mc" (monivalinta) ja "sentence" (lauseen muodostus)
    // - Kummassakin tapauksessa on kenttä question, direction (fi2sv tai sv2fi).
    // Monivalintatehtävissä:
    //   options: vaihtoehdot, correctAnswer: oikea vastaus (täsmälleen sama merkkijono).
    // Lauseen muodostustehtävissä:
    //   givenWords: taulukko annetuista sanoista (sekoitetaan), correctSentence: oikea lause.
    const quizData = [
      // Monivalintatehtäviä (type: "mc")
      {
        type: "mc",
        question: "Mikä on ruotsinkielinen vastine sanalle 'etäopetus'?",
        options: ["studera på distans", "en distansstuderande", "distansstudier", "distance"],
        correctAnswer: "distansstudier",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Vad betyder 'en distansstuderande' på finska?",
        options: ["etäopiskella", "etäopetus", "opiskella etänä", "etäopiskelija"],
        correctAnswer: "etäopiskelija",
        direction: "sv2fi"
      },
      // Lauseen muodostustehtävä (type: "sentence")
      {
        type: "sentence",
        question: "Muodosta lause ruotsiksi käyttämällä annettuja sanoja:",
        givenWords: ["studera", "Jag", "husteknik"],
        correctSentence: "Jag studera husteknik",  // Esimerkin vastaus, muokkaa tarvittaessa (voit halutessasi käyttää oikea muotoa, esim. "Jag studerar husteknik")
        direction: "fi2sv"
      },
      // Toinen lauseen muodostustehtävä
      {
        type: "sentence",
        question: "Formera en mening på finska med orden:",
        givenWords: ["opiskelen", "minä", "talotekniikkaa"],
        correctSentence: "minä opiskelen talotekniikkaa",
        direction: "sv2fi"
      },
      // Lisää muita monivalinta- tai lauseenmuodostustehtäviä tässä...
      {
        type: "mc",
        question: "Mikä on ruotsin sana 'examen' suomeksi?",
        options: ["tutkia", "ammattilainen", "koulutus", "tutkinto"],
        correctAnswer: "tutkinto",
        direction: "sv2fi"
      },
      {
        type: "mc",
        question: "Vad betyder 'Yrkeshögskoleexamen' på finska?",
        options: ["ammattiluottamus", "ammattikorkeakoulututkinto", "opintotodistus", "opintopiste"],
        correctAnswer: "ammattikorkeakoulututkinto",
        direction: "sv2fi"
      },
      // Voit lisätä vielä lisää tehtäviä, kunnes kokonaismäärä on haluttu (esim. 40 kysymystä)
    ];

    let currentQuestionIndex = 0;
    let currentShuffledOptions = [];
    let currentCorrectIndex = null;

    const questionText = document.getElementById("questionText");
    const taskArea = document.getElementById("taskArea");
    const feedback = document.getElementById("feedback");
    const nextButton = document.getElementById("nextButton");
    const submitSentence = document.getElementById("submitSentence");
    const clearSentence = document.getElementById("clearSentence");
    const directionInfo = document.getElementById("directionInfo");

    // Alustetaan lauseen muodostuksen muuttujat
    let currentFormedSentence = [];

    function loadQuestion() {
      feedback.textContent = "";
      nextButton.style.display = "none";
      submitSentence.style.display = "none";
      clearSentence.style.display = "none";
      taskArea.innerHTML = "";

      const currentQuestion = quizData[currentQuestionIndex];

      // Näytä käännössuunta-info
      if (currentQuestion.direction === "fi2sv") {
        directionInfo.textContent = "Käännä suomesta ruotsiksi:";
      } else {
        directionInfo.textContent = "Käännä ruotsista suomeksi:";
      }
      questionText.textContent = currentQuestion.question;

      if (currentQuestion.type === "mc") {
        // Monivalintatehtävä
        // Kopioidaan vaihtoehdot ja sekoitetaan
        currentShuffledOptions = shuffle([...currentQuestion.options]);
        currentCorrectIndex = currentShuffledOptions.findIndex(opt => opt === currentQuestion.correctAnswer);

        currentShuffledOptions.forEach((option, index) => {
          const button = document.createElement("button");
          button.textContent = option;
          button.addEventListener("click", () => selectMCOption(index, button));
          taskArea.appendChild(button);
        });
      } else if (currentQuestion.type === "sentence") {
        // Lauseen muodostustehtävä
        // Näytetään annetut sanat sekoitetussa järjestyksessä painikkeina
        currentFormedSentence = [];
        const shuffledWords = shuffle([...currentQuestion.givenWords]);

        const wordButtonsContainer = document.createElement("div");
        wordButtonsContainer.id = "wordButtonsContainer";
        shuffledWords.forEach((word) => {
          const wordBtn = document.createElement("button");
          wordBtn.textContent = word;
          wordBtn.classList.add("word-button");
          wordBtn.addEventListener("click", () => addWordToSentence(word, wordBtn));
          wordButtonsContainer.appendChild(wordBtn);
        });
        taskArea.appendChild(wordButtonsContainer);

        // Näytetään alue, johon muodostettu lause ilmestyy
        const sentenceArea = document.createElement("div");
        sentenceArea.id = "sentenceArea";
        sentenceArea.textContent = "";
        taskArea.appendChild(sentenceArea);

        // Näytetään lauseen lähetys- ja tyhjennysnapit
        submitSentence.style.display = "inline-block";
        clearSentence.style.display = "inline-block";
      }
    }

    // Monivalintatehtävän vastaus
    function selectMCOption(selectedIndex, buttonElement) {
      const buttons = taskArea.querySelectorAll("button");
      buttons.forEach(btn => btn.disabled = true);

      if (selectedIndex === currentCorrectIndex) {
        buttonElement.classList.add("correct");
        feedback.textContent = "Oikein!";
      } else {
        buttonElement.classList.add("incorrect");
        feedback.textContent = "Väärin!";
        if (buttons[currentCorrectIndex]) {
          buttons[currentCorrectIndex].classList.add("correct");
        }
      }
      nextButton.style.display = "block";
    }

    // Lauseen muodostustehtävä: sanan lisääminen
    function addWordToSentence(word, buttonElement) {
      // Poistetaan painike, jotta sanaa ei voi lisätä uudelleen
      buttonElement.disabled = true;
      currentFormedSentence.push(word);
      updateSentenceArea();
    }

    function updateSentenceArea() {
      const sentenceArea = document.getElementById("sentenceArea");
      sentenceArea.textContent = currentFormedSentence.join(" ");
    }

    // Lauseen muodostustehtävä: tarkista vastaus
    function submitSentenceAnswer() {
      const currentQuestion = quizData[currentQuestionIndex];
      const formed = currentFormedSentence.join(" ").trim();
      // Vertaillaan täsmälleen: voit lisätä pienoismuunnoksia (esim. pienet ja isot kirjaimet) tarpeen mukaan
      if (formed === currentQuestion.correctSentence) {
        feedback.textContent = "Oikein!";
      } else {
        feedback.textContent = "Väärin! Oikea vastaus: " + currentQuestion.correctSentence;
      }
      // Estetään lisämuokkaus
      disableWordButtons();
      nextButton.style.display = "block";
      submitSentence.style.display = "none";
      clearSentence.style.display = "none";
    }

    function disableWordButtons() {
      const wordButtons = document.querySelectorAll("#wordButtonsContainer button");
      wordButtons.forEach(btn => btn.disabled = true);
    }

    // Lauseen muodostustehtävä: tyhjennä muodostettu vastaus ja palauta painikkeet
    function clearSentenceAnswer() {
      currentFormedSentence = [];
      updateSentenceArea();
      // Palauta painikkeet aktivoiduiksi
      const wordButtons = document.querySelectorAll("#wordButtonsContainer button");
      wordButtons.forEach(btn => btn.disabled = false);
      feedback.textContent = "";
    }

    nextButton.addEventListener("click", () => {
      currentQuestionIndex++;
      if (currentQuestionIndex < quizData.length) {
        loadQuestion();
      } else {
        questionText.textContent = "Testi suoritettu!";
        taskArea.innerHTML = "";
        feedback.textContent = "";
        nextButton.style.display = "none";
        directionInfo.textContent = "";
      }
    });

    submitSentence.addEventListener("click", submitSentenceAnswer);
    clearSentence.addEventListener("click", clearSentenceAnswer);

    loadQuestion();
  </script>
</body>
</html>
