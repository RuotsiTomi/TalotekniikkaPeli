<TTMI23KM html>
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
      <!-- Näytetään käännössuunta esim. "Käännä suomesta ruotsiksi:" -->
      <div class="direction" id="directionInfo"></div>
      <div class="question" id="questionText">Kysymys tulee tähän...</div>
      <div id="taskArea">
        <!-- Tähän lisätään joko monivalintavaihtoehdot tai lauseen muodostus -elementit -->
      </div>
      <div id="feedback"></div>
      <button id="nextButton" style="display:none;">Seuraava kysymys</button>
      <button id="submitSentence" style="display:none;">Lähetä vastaus</button>
      <button id="clearSentence" style="display:none;">Tyhjennä</button>
    </div>
  </div>

  <script>
    // Apufunktio sekoittamaan taulukon alkiot
    function shuffle(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    }

    /* Quiz-data:
       Ensimmäiset 20 kysymystä (1-10: monivalinta, 11-20: lauseen muodostus) liittyvät opintoihin.
       Seuraavat 20 kysymystä (21-30: monivalinta, 31-40: lauseen muodostus) työ- ja sanastoteemoihin.
       Kaikissa lauseen muodostustehtävissä käännetään suomesta ruotsiksi (direction: "fi2sv").
    */
    const quizData = [
      // OPINTOAIHEISET MONIVALINTAKYSYMYKSET (1-10)
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'ammattikorkeakoulu'?",
        options: ["yrkeshögskola", "universitet", "högskola", "lärarutbildning"],
        correctAnswer: "yrkeshögskola",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'opintopiste'?",
        options: ["studiepoäng", "kursenhet", "kredit", "utbildningspoäng"],
        correctAnswer: "studiepoäng",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'perusopinnot'?",
        options: ["grundstudier", "basstudier", "grundläggande studier", "förberedande studier"],
        correctAnswer: "grundstudier",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'ammattiopinnot'?",
        options: ["yrkesstudier", "teknikstudier", "yrkesutbildning", "yrkeskurser"],
        correctAnswer: "yrkesstudier",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'valinnaiset opinnot'?",
        options: ["valfria studier", "fria studier", "frivilliga studier", "alternativa studier"],
        correctAnswer: "valfria studier",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'kieli- ja viestintäopinnot'?",
        options: ["språk- och kommunikationsstudier", "språkundervisning", "kommunikationskurser", "språkstudier"],
        correctAnswer: "språk- och kommunikationsstudier",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'hyväksilukeminen'?",
        options: ["ackreditering", "godkännande", "certifiering", "avlägga en examen"],
        correctAnswer: "ackreditering",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'jatko-opinnot'?",
        options: ["fortsatta studier", "vidareutbildning", "avancerade studier", "efterstudier"],
        correctAnswer: "fortsatta studier",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'vaihto-opiskelu'?",
        options: ["utbytesstudier", "studieresa", "utbyte", "studentutbyte"],
        correctAnswer: "utbytesstudier",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'opiskelijavaihto'?",
        options: ["studerandeutbyte", "studentutbyte", "utbytesstudier", "utbytestudent"],
        correctAnswer: "studerandeutbyte",
        direction: "fi2sv"
      },

      // OPINTOAIHEISET LAUSEENMUODOSTUSTEHTÄVÄT (11-20)
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Opiskelen talotekniikkaa Kaakkois-Suomen ammattikorkeakoulussa Mikkelin kampuksella.'",
        givenWords: ["Jag", "studerar", "husteknik", "vid", "Sydöstra", "Finlands", "yrkeshögskola", "på", "Mikkelin", "campus"],
        correctSentence: "Jag studerar husteknik vid Sydöstra Finlands yrkeshögskola på Mikkelin campus",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Aloitin opintoni tammikuussa 2023 ja olen suorittanut 60 opintopistettä.'",
        givenWords: ["Jag", "började", "mina", "studier", "i", "januari", "2023", "och", "har", "avlagt", "60", "studiepoäng"],
        correctSentence: "Jag började mina studier i januari 2023 och har avlagt 60 studiepoäng",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Valitsin insinööriopinnot, koska olen kiinnostunut teknologiasta ja kehityksestä.'",
        givenWords: ["Jag", "valde", "ingenjörsstudier", "eftersom", "jag", "är", "intresserad", "av", "teknik", "och", "utveckling"],
        correctSentence: "Jag valde ingenjörsstudier eftersom jag är intresserad av teknik och utveckling",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Olen opiskellut kolme vuotta ja suorittanut 90 opintopistettä.'",
        givenWords: ["Jag", "har", "studerat", "i", "tre", "år", "och", "har", "avlagt", "90", "studiepoäng"],
        correctSentence: "Jag har studerat i tre år och har avlagt 90 studiepoäng",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Perusopinnot muodostavat koulutukseni perustan.'",
        givenWords: ["Grundstudier", "utgör", "grunden", "för", "min", "utbildning"],
        correctSentence: "Grundstudier utgör grunden för min utbildning",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Kieli- ja viestintäopinnot ovat tärkeitä tulevaisuuden työelämässä.'",
        givenWords: ["Språk- och", "kommunikationsstudier", "är", "viktiga", "för", "framtidens", "arbetsliv"],
        correctSentence: "Språk- och kommunikationsstudier är viktiga för framtidens arbetsliv",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Hyväksilukeminen mahdollistaa opintojen nopeamman etenemisen.'",
        givenWords: ["Ackreditering", "möjliggör", "snabbare", "studieframsteg"],
        correctSentence: "Ackreditering möjliggör snabbare studieframsteg",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Jatko-opinnot tarjoavat syvempää osaamista alalla.'",
        givenWords: ["Fortsatta", "studier", "erbjuder", "djupare", "kompetens", "inom", "området"],
        correctSentence: "Fortsatta studier erbjuder djupare kompetens inom området",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Opiskelijavaihto rikastuttaa kulttuurista osaamista.'",
        givenWords: ["Studerandeutbyte", "berikar", "den", "kulturella", "kompetensen"],
        correctSentence: "Studerandeutbyte berikar den kulturella kompetensen",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Valinnaiset opinnot antavat mahdollisuuden erikoistua omaan kiinnostukseen.'",
        givenWords: ["Valfria", "studier", "ger", "möjligheten", "att", "specialisera", "sig", "inom", "eget", "intresse"],
        correctSentence: "Valfria studier ger möjligheten att specialisera sig inom eget intresse",
        direction: "fi2sv"
      },

      // TYÖ- JA SANASTOTEEMAISET MONIVALINTAKYSYMYKSET (21-30)
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'asiantuntija'?",
        options: ["expert", "konsult", "ingenjör", "planerare"],
        correctAnswer: "expert",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'asiantuntijatehtävät'?",
        options: ["expertuppdrag", "projektuppgifter", "arbetsuppgifter", "utvecklingsuppdrag"],
        correctAnswer: "expertuppdrag",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'suunnittelu'?",
        options: ["planering", "utveckling", "produktion", "design"],
        correctAnswer: "planering",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'kehittäminen'?",
        options: ["utveckling", "planering", "förbättring", "innovation"],
        correctAnswer: "utveckling",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'tekniset palvelut'?",
        options: ["tekniska tjänster", "industriella tjänster", "service", "konsulttjänster"],
        correctAnswer: "tekniska tjänster",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'järjestelmä'?",
        options: ["system", "struktur", "ramverk", "modell"],
        correctAnswer: "system",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'laite'?",
        options: ["apparat", "maskin", "utrustning", "verktyg"],
        correctAnswer: "apparat",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'kiinteistö'?",
        options: ["fastighet", "byggnad", "lokal", "anläggning"],
        correctAnswer: "fastighet",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'tila'?",
        options: ["lokal", "rum", "område", "yta"],
        correctAnswer: "lokal",
        direction: "fi2sv"
      },
      {
        type: "mc",
        question: "Mikä on ruotsin termi 'LVI-suunnittelija'?",
        options: ["VVS-planerare", "VVS-montör", "VVS-tekniker", "VVS-inspektör"],
        correctAnswer: "VVS-planerare",
        direction: "fi2sv"
      },

      // TYÖ- JA SANASTOTEEMAISET LAUSEENMUODOSTUSTEHTÄVÄT (31-40)
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Työnjohtoharjoittelijana opin työelämän käytännöt ja johtamistaitoja.'",
        givenWords: ["Som", "arbetsledningspraktikant", "lär", "jag", "arbetslivets", "praxis", "och", "ledarskapsförmåga"],
        correctSentence: "Som arbetsledningspraktikant lär jag mig arbetslivets praxis och ledarskapsförmåga",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Husteknikbranschen tarjoaa monipuolisia työtehtäviä suunnittelussa ja tuotannossa.'",
        givenWords: ["Husteknikbranschen", "erbjuder", "mångsidiga", "arbetsuppgifter", "inom", "planering", "och", "produktion"],
        correctSentence: "Husteknikbranschen erbjuder mångsidiga arbetsuppgifter inom planering och produktion",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'LVI-insinöörit vastaavat järjestelmien ylläpidosta ja toimivuudesta.'",
        givenWords: ["VVS-ingenjörer", "ansvarar", "för", "systemens", "underhåll", "och", "funktion"],
        correctSentence: "VVS-ingenjörer ansvarar för systemens underhåll och funktion",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Projektipäällikkö johtaa LVI-projekteja aikataulujen ja budjettien mukaisesti.'",
        givenWords: ["Projektledaren", "leder", "VVS-projekt", "enligt", "tidsplaner", "och", "budgetar"],
        correctSentence: "Projektledaren leder VVS-projekt enligt tidsplaner och budgetar",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Myynti-insinööri tarjoaa teknistä tukea ja ratkaisuja asiakkaille.'",
        givenWords: ["Försäljningsingenjören", "erbjuder", "teknisk", "support", "och", "lösningar", "till", "kunder"],
        correctSentence: "Försäljningsingenjören erbjuder teknisk support och lösningar till kunder",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Konsultti neuvoo asiakkaita LVI-järjestelmien suunnittelussa ja toteutuksessa.'",
        givenWords: ["Konsulten", "ger", "råd", "till", "kunder", "vid", "planering", "och", "genomförande", "av", "VVS-system"],
        correctSentence: "Konsulten ger råd till kunder vid planering och genomförande av VVS-system",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Ingenjörerna työskentelevät sekä yksityisellä että julkisella sektorilla.'",
        givenWords: ["Ingenjörerna", "arbetar", "både", "i", "den", "privata", "och", "den", "offentliga", "sektorn"],
        correctSentence: "Ingenjörerna arbetar både i den privata och den offentliga sektorn",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Tuoteteollisuudessa valmistetaan moderneja ratkaisuja erilaisiin tarpeisiin.'",
        givenWords: ["I", "produktindustrin", "tillverkas", "moderna", "lösningar", "för", "olika", "behov"],
        correctSentence: "I produktindustrin tillverkas moderna lösningar för olika behov",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Julkinen sektori tarjoaa vakaita työllistymismahdollisuuksia.'",
        givenWords: ["Den", "offentliga", "sektorn", "erbjuder", "stabila", "anställningsmöjligheter"],
        correctSentence: "Den offentliga sektorn erbjuder stabila anställningsmöjligheter",
        direction: "fi2sv"
      },
      {
        type: "sentence",
        question: "Muodosta ruotsiksi: 'Fastighets- ja fastighetsservicebolag ovat merkittäviä työnantajia.'",
        givenWords: ["Fastighets-", "och", "fastighetsservicebolag", "är", "viktiga", "arbetsgivare"],
        correctSentence: "Fastighets- och fastighetsservicebolag är viktiga arbetsgivare",
        direction: "fi2sv"
      }
    ];

    let currentQuestionIndex = 0;
    let currentShuffledOptions = [];
    let currentCorrectIndex = null;
    let currentFormedSentence = [];

    const questionText = document.getElementById("questionText");
    const taskArea = document.getElementById("taskArea");
    const feedback = document.getElementById("feedback");
    const nextButton = document.getElementById("nextButton");
    const submitSentence = document.getElementById("submitSentence");
    const clearSentence = document.getElementById("clearSentence");
    const directionInfo = document.getElementById("directionInfo");

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
        // Monivalintatehtävä: sekoitetaan vaihtoehdot
        currentShuffledOptions = shuffle([...currentQuestion.options]);
        currentCorrectIndex = currentShuffledOptions.findIndex(opt => opt === currentQuestion.correctAnswer);
        currentShuffledOptions.forEach((option, index) => {
          const button = document.createElement("button");
          button.textContent = option;
          button.addEventListener("click", () => selectMCOption(index, button));
          taskArea.appendChild(button);
        });
      } else if (currentQuestion.type === "sentence") {
        // Lauseen muodostustehtävä: näytetään annetut sanat sekoitetussa järjestyksessä
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

        const sentenceArea = document.createElement("div");
        sentenceArea.id = "sentenceArea";
        sentenceArea.textContent = "";
        taskArea.appendChild(sentenceArea);

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

    // Lauseen muodostustehtävä: lisätään sana
    function addWordToSentence(word, buttonElement) {
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
      if (formed === currentQuestion.correctSentence) {
        feedback.textContent = "Oikein!";
      } else {
        feedback.textContent = "Väärin! Oikea vastaus: " + currentQuestion.correctSentence;
      }
      disableWordButtons();
      nextButton.style.display = "block";
      submitSentence.style.display = "none";
      clearSentence.style.display = "none";
    }

    function disableWordButtons() {
      const wordButtons = document.querySelectorAll("#wordButtonsContainer button");
      wordButtons.forEach(btn => btn.disabled = true);
    }

    // Lauseen muodostustehtävä: tyhjennä vastaus ja aktivoi painikkeet uudelleen
    function clearSentenceAnswer() {
      currentFormedSentence = [];
      updateSentenceArea();
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
