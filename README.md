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
    .instruction {
      font-size: 0.9em;
      color: #555;
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
      <div class="instruction" id="instructionText"></div>
      <div class="options" id="optionsContainer">
        <!-- Vastausvaihtoehdot lisätään JavaScriptillä -->
      </div>
      <div id="feedback"></div>
      <button id="nextButton">Seuraava kysymys</button>
    </div>
  </div>

  <script>
    // Apufunktio vastausvaihtoehtojen sekoittamiseen (Fisher-Yates)
    function shuffleArray(array) {
      const arr = array.slice();
      for (let i = arr.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [arr[i], arr[j]] = [arr[j], arr[i]];
      }
      return arr;
    }

    /* 
      QuizData-esimerkit:
      - direction: "fiToSv" => Kysymys suomeksi, vastausvaihtoehdot ruotsiksi.
      - direction: "svToFi" => Kysymys ruotsiksi, vastausvaihtoehdot suomeksi.
      - questionFi ja questionSv sisältävät kysymyksen molemmilla kielillä.
      - options sisältää kaikki vastausvaihtoehdot (kieliversio riippuu kysymyksen suunnasta).
      - correctAnswer on oikea vastaus (kirjoitettu samanlaisena kuin options-arvossa).
    */
    const quizData = [
      // Opintoihin ja opiskeluun liittyvät kysymykset (1-20)
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsinkielinen vastine sanalle 'etäopetus'?",
        questionSv: "",
        options: ["studera på distans", "en distansstuderande", "distansstudier", "opiskella etänä"],
        correctAnswer: "distansstudier"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsinkielinen vastine sanalle 'etäopiskelija'?",
        questionSv: "",
        options: ["studera på distans", "en distansstuderande", "distansstudier", "opiskella etänä"],
        correctAnswer: "en distansstuderande"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsinkielinen termi 'opiskella etänä'?",
        questionSv: "",
        options: ["studera på distans", "en distansstuderande", "distansstudier", "et studera på nätet"],
        correctAnswer: "studera på distans"
      },
      {
        direction: "fiToSv",
        questionFi: "Onko väittämä totta? 'En distansstuderande opiskelee etänä.'",
        questionSv: "",
        options: ["Oikein", "Väärin"],
        correctAnswer: "Oikein"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'yrkeshögskola' suomeksi?",
        questionSv: "",
        options: ["ammattikorkeakoulu", "tutkinto", "opintopiste", "koulutus"],
        correctAnswer: "ammattikorkeakoulu"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin sana 'examen' suomeksi?",
        questionSv: "",
        options: ["tutkinto", "ammattilainen", "koulutus", "opiskelija"],
        correctAnswer: "tutkinto"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'Yrkeshögskoleexamen' suomeksi?",
        questionSv: "",
        options: ["ammattiluottamus", "ammattikorkeakoulututkinto", "opintotodistus", "opintopiste"],
        correctAnswer: "ammattikorkeakoulututkinto"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin ilmaisu 'ger dig färdigheter för' suomeksi?",
        questionSv: "",
        options: ["antaa sinulle taitoja", "kehittää taitoja", "suorittaa opinnot", "tutkinto"],
        correctAnswer: "antaa sinulle taitoja"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'Grundstudierna' suomeksi?",
        questionSv: "",
        options: ["perusopinnot", "ammattiopinnot", "valinnaiset opinnot", "vapaasti valittavat opinnot"],
        correctAnswer: "perusopinnot"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'Yrkesstudierna' suomeksi?",
        questionSv: "",
        options: ["ammattiopinnot", "perusopinnot", "valinnaiset opinnot", "vapaasti valittavat opinnot"],
        correctAnswer: "ammattiopinnot"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'valfria studier' suomeksi?",
        questionSv: "",
        options: ["valinnaiset opinnot", "ammattiopinnot", "perusopinnot", "opintopisteet"],
        correctAnswer: "valinnaiset opinnot"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'fritt valbara studier' suomeksi?",
        questionSv: "",
        options: ["vapaasti valittavat opinnot", "valinnaiset opinnot", "ammattiopinnot", "perusopinnot"],
        correctAnswer: "vapaasti valittavat opinnot"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'språk- och kommunikationsstudier' suomeksi?",
        questionSv: "",
        options: ["kieli- ja viestintäopinnot", "ammattiopinnot", "perusopinnot", "tutkinto"],
        correctAnswer: "kieli- ja viestintäopinnot"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'studiepoäng' suomeksi?",
        questionSv: "",
        options: ["opintopiste", "laajuus", "tutkinto", "opiskelija"],
        correctAnswer: "opintopiste"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'studerande' suomeksi?",
        questionSv: "",
        options: ["opiskelija", "tutkinto", "koulutus", "opiskelijavaihto"],
        correctAnswer: "opiskelija"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'utbildning' suomeksi?",
        questionSv: "",
        options: ["koulutus", "examens", "studier", "opintopiste"],
        correctAnswer: "koulutus"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'ackreditering' suomeksi?",
        questionSv: "",
        options: ["hyväksilukeminen", "avlägga en examen", "certifiering", "godkännande"],
        correctAnswer: "hyväksilukeminen"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'fortsatta studier' suomeksi?",
        questionSv: "",
        options: ["jatko-opinnot", "perusopinnot", "ammattiopinnot", "opintojen loppu"],
        correctAnswer: "jatko-opinnot"
      },
      {
        direction: "fiToSv",
        questionFi: "Mikä on ruotsin termi 'utbytesstudier' suomeksi?",
        questionSv: "",
        options: ["vaihto-opiskelu", "etäopinnot", "harjoittelu", "perusopinnot"],
        correctAnswer: "vaihto-opiskelu"
      },
      // Ammatti- ja sanastoteemoihin liittyvät kysymykset (21-40)
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'expert' på finska?",
        options: ["asiantuntija", "opiskelija", "tutkinto", "suunnittelu"],
        correctAnswer: "asiantuntija"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'expertuppdrag' på finska?",
        options: ["asiantuntijatehtävät", "tutkinnon suoritus", "opinnot", "koulutus"],
        correctAnswer: "asiantuntijatehtävät"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'planerings' på finska?",
        options: ["suunnittelu", "kehittäminen", "opintopiste", "koulutus"],
        correctAnswer: "suunnittelu"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'utvecklings' på finska?",
        options: ["kehittäminen", "suunnittelu", "harjoittelu", "koulutus"],
        correctAnswer: "kehittäminen"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'tekniska tjänster' på finska?",
        options: ["tekniset palvelut", "koulutus", "opiskelutavat", "suunnittelu"],
        correctAnswer: "tekniset palvelut"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'system' på finska?",
        options: ["järjestelmä", "opintopiste", "tutkinto", "asiantuntija"],
        correctAnswer: "järjestelmä"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'apparat' på finska?",
        options: ["laite", "järjestelmä", "koulutus", "opinnot"],
        correctAnswer: "laite"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'fastighet' på finska?",
        options: ["kiinteistö", "asunto", "tutkinto", "opiskelija"],
        correctAnswer: "kiinteistö"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'lokal' på finska?",
        options: ["tila", "järjestelmä", "koulutus", "opiskelija"],
        correctAnswer: "tila"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'VVS-planerare' på finska?",
        options: ["LVI-suunnittelija", "LVI-asentaja", "LVI-inspektööri", "LVI-tarkastusinsinööri"],
        correctAnswer: "LVI-suunnittelija"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'VVS-montör' på finska?",
        options: ["LVI-asentaja", "LVI-suunnittelija", "LVI-inspektööri", "LVI-tarkastusinsinööri"],
        correctAnswer: "LVI-asentaja"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'försäljningsingenjör' på finska?",
        options: ["myynti-insinööri", "opiskelija", "asiantuntija", "suunnittelija"],
        correctAnswer: "myynti-insinööri"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'konsult' på finska?",
        options: ["konsultti", "opiskelija", "tutkinto", "suunnittelu"],
        correctAnswer: "konsultti"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'underhållsingenjör' på finska?",
        options: ["ylläpitoinsinööri", "LVI-asentaja", "konsultti", "suunnittelija"],
        correctAnswer: "ylläpitoinsinööri"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'energianpassad' på finska?",
        options: ["energiatehokas", "ympäristöystävällinen", "kestävä", "suunniteltu"],
        correctAnswer: "energiatehokas"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'lösning' på finska?",
        options: ["ratkaisu", "järjestelmä", "opinnot", "koulutus"],
        correctAnswer: "ratkaisu"
      },
      {
        direction: "svToFi",
        questionFi: "",
        questionSv: "Vad betyder 'myndighet' på finska?",
       
