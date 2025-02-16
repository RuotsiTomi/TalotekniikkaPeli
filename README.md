<Talotekniiikka TTMI23KM>
<!DOCTYPE html>
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
      max-width: 800px;
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
        <!-- Vastausvaihtoehdot lisätään JavaScriptillä -->
      </div>
      <div id="feedback"></div>
      <button id="nextButton">Seuraava kysymys</button>
    </div>
  </div>

  <script>
    // Kysymysten data – 40 kysymystä, joissa 20 liittyy opintoihin ja 20 ammatti- ja sanastoteemoihin.
    const quizData = [
      // Opintoihin ja opiskeluun liittyvät kysymykset (1-20)
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
        question: "Mikä on ruotsinkielinen vastine sanalle 'etäopiskelija'?",
        options: [
          "studera på distans",
          "en distansstuderande",
          "distansstudier",
          "opiskella etänä"
        ],
        correctIndex: 1
      },
      {
        question: "Mikä on ruotsinkielinen termi 'opiskella etänä'?",
        options: [
          "studera på distans",
          "en distansstuderande",
          "distansstudier",
          "et studera på nätet"
        ],
        correctIndex: 0
      },
      {
        question: "Onko väittämä totta? 'En distansstuderande opiskelee etänä.'",
        options: [
          "Oikein",
          "Väärin"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'yrkeshögskola' suomeksi?",
        options: [
          "ammattikorkeakoulu",
          "tutkinto",
          "opintopiste",
          "koulutus"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin sana 'examen' suomeksi?",
        options: [
          "tutkinto",
          "ammattilainen",
          "koulutus",
          "opiskelija"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'Yrkeshögskoleexamen' suomeksi?",
        options: [
          "ammattiluottamus",
          "ammattikorkeakoulututkinto",
          "opintotodistus",
          "opintopiste"
        ],
        correctIndex: 1
      },
      {
        question: "Mikä on ruotsin ilmaisu 'ger dig färdigheter för' suomeksi?",
        options: [
          "antaa sinulle taitoja",
          "kehittää taitoja",
          "suorittaa opinnot",
          "tutkinto"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'Grundstudierna' suomeksi?",
        options: [
          "perusopinnot",
          "ammattiopinnot",
          "valinnaiset opinnot",
          "vapaasti valittavat opinnot"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'Yrkesstudierna' suomeksi?",
        options: [
          "ammattiopinnot",
          "perusopinnot",
          "valinnaiset opinnot",
          "vapaasti valittavat opinnot"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'valfria studier' suomeksi?",
        options: [
          "valinnaiset opinnot",
          "ammattiopinnot",
          "perusopinnot",
          "opintopisteet"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'fritt valbara studier' suomeksi?",
        options: [
          "vapaasti valittavat opinnot",
          "valinnaiset opinnot",
          "ammattiopinnot",
          "perusopinnot"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'språk- och kommunikationsstudier' suomeksi?",
        options: [
          "kieli- ja viestintäopinnot",
          "ammattiopinnot",
          "perusopinnot",
          "tutkinto"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'studiepoäng' suomeksi?",
        options: [
          "opintopiste",
          "laajuus",
          "tutkinto",
          "opiskelija"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'studerande' suomeksi?",
        options: [
          "opiskelija",
          "tutkinto",
          "koulutus",
          "opiskelijavaihto"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'utbildning' suomeksi?",
        options: [
          "koulutus",
          "examens",
          "studier",
          "opintopiste"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'ackreditering' suomeksi?",
        options: [
          "hyväksilukeminen",
          "avlägga en examen",
          "certifiering",
          "godkännande"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'fortsatta studier' suomeksi?",
        options: [
          "jatko-opinnot",
          "perusopinnot",
          "ammattiopinnot",
          "opintojen loppu"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'utbytesstudier' suomeksi?",
        options: [
          "vaihto-opiskelu",
          "etäopinnot",
          "harjoittelu",
          "perusopinnot"
        ],
        correctIndex: 0
      },
      // Ammatti- ja sanastoteemoihin liittyvät kysymykset (21-40)
      {
        question: "Mikä on ruotsin sana 'expert' suomeksi?",
        options: [
          "asiantuntija",
          "opiskelija",
          "tutkinto",
          "suunnittelu"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin sana 'expertuppdrag' suomeksi?",
        options: [
          "asiantuntijatehtävät",
          "tutkinnon suoritus",
          "opinnot",
          "koulutus"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin sana 'planerings' suomeksi?",
        options: [
          "suunnittelu",
          "kehittäminen",
          "opintopiste",
          "koulutus"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin sana 'utvecklings' suomeksi?",
        options: [
          "kehittäminen",
          "suunnittelu",
          "harjoittelu",
          "koulutus"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'tekniska tjänster' suomeksi?",
        options: [
          "tekniset palvelut",
          "koulutus",
          "opiskelutavat",
          "suunnittelu"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin sana 'system' suomeksi?",
        options: [
          "järjestelmä",
          "opintopiste",
          "tutkinto",
          "asiantuntija"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin sana 'apparat' suomeksi?",
        options: [
          "laite",
          "järjestelmä",
          "koulutus",
          "opinnot"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'fastighet' suomeksi?",
        options: [
          "kiinteistö",
          "asunto",
          "tutkinto",
          "opiskelija"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'lokal' suomeksi?",
        options: [
          "tila",
          "järjestelmä",
          "koulutus",
          "opiskelija"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'VVS-planerare' suomeksi?",
        options: [
          "LVI-suunnittelija",
          "LVI-asentaja",
          "LVI-inspektööri",
          "LVI-tarkastusinsinööri"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'VVS-montör' suomeksi?",
        options: [
          "LVI-asentaja",
          "LVI-suunnittelija",
          "LVI-inspektööri",
          "LVI-tarkastusinsinööri"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin sana 'försäljningsingenjör' suomeksi?",
        options: [
          "myynti-insinööri",
          "opiskelija",
          "asiantuntija",
          "suunnittelija"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin sana 'konsult' suomeksi?",
        options: [
          "konsultti",
          "opiskelija",
          "tutkinto",
          "suunnittelu"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'underhållsingenjör' suomeksi?",
        options: [
          "ylläpitoinsinööri",
          "LVI-asentaja",
          "konsultti",
          "suunnittelija"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'energianpassad' suomeksi?",
        options: [
          "energiatehokas",
          "ympäristöystävällinen",
          "kestävä",
          "suunniteltu"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'lösning' suomeksi?",
        options: [
          "ratkaisu",
          "järjestelmä",
          "opinnot",
          "koulutus"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'myndighet' suomeksi?",
        options: [
          "viranomainen",
          "opiskelija",
          "työnantaja",
          "koulutus"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'placera' suomeksi?",
        options: [
          "sijoittua",
          "opiskella",
          "suorittaa",
          "kehittää"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'ingenjörs- och planeringsbyrå' suomeksi?",
        options: [
          "insinööri- ja suunnittelutoimisto",
          "koulutusinstituutio",
          "työpaikka",
          "opintokeskus"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'produktindustri' suomeksi?",
        options: [
          "tuoteteollisuus",
          "koulutus",
          "opiskelija",
          "tutkinto"
        ],
        correctIndex: 0
      },
      {
        question: "Mikä on ruotsin termi 'offentliga sektorn' suomeksi?",
        options: [
          "julkinen sektori",
          "yksityinen sektori",
          "opetus",
          "koulutus"
        ],
        correctIndex: 0
      }
    ];

    let currentQuestionIndex = 0;
    const questionText = document.getElementById("questionText");
    const optionsContainer = document.getElementById("optionsContainer");
    const feedback = document.getElementById("feedback");
    const nextButton = document.getElementById("nextButton");

    // Lataa kysymys
    function loadQuestion() {
      feedback.textContent = "";
      nextButton.style.display = "none";
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
      const buttons = optionsContainer.querySelectorAll("button");
      buttons.forEach(btn => btn.disabled = true);

      const currentQuestion = quizData[currentQuestionIndex];
      if(selectedIndex === currentQuestion.correctIndex) {
        buttonElement.classList.add("correct");
        feedback.textContent = "Oikein!";
      } else {
        buttonElement.classList.add("incorrect");
        feedback.textContent = "Väärin!";
        if(buttons[currentQuestion.correctIndex]) {
          buttons[currentQuestion.correctIndex].classList.add("correct");
        }
      }
      nextButton.style.display = "block";
    }

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

    loadQuestion();
  </script>
</body>
</html>
