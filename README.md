# TalotekniikkaPeli
<!DOCTYPE html>
<html>
<head>
    <title>Talotekniikka-sanapeli</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #f5f5f5;
        }
        .game-container {
            text-align: center;
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }
        button {
            padding: 10px 20px;
            margin: 5px;
            font-size: 16px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1>Talotekniikka-sanapeli</h1>
        <p id="question">Kysymys tulee tähän</p>
        <button onclick="checkAnswer(true)">Oikein</button>
        <button onclick="checkAnswer(false)">Väärin</button>
        <p id="score">Pisteet: 0</p>
    </div>

    <script>
        const questions = [
            { question: "Kunna, kan, kunde, kunnat", correct: true },
            { question: "Avlägga, avlägger, avlade, avlagt en examen", correct: true },
            { question: "Husteknik, hustekniken", correct: true },
            { question: "En benämning, benämningen", correct: false },
            { question: "En helhet, helheten", correct: true },
            { question: "Bestå, består, bestod, bestått av ngt", correct: false },
            { question: "Tekniska tjänster, de tekniska tjänsterna", correct: true },
            { question: "Ett system, systemet, system, systemen", correct: true },
            { question: "En apparat, apparaten, apparater, apparaterna", correct: false },
            { question: "En fastighet, fastigheten, fastigheter, fastigheterna", correct: true },
            { question: "En lokal, lokalen, lokaler, lokalerna", correct: true },
            { question: "Höra, hör, hörde, hört till ngt", correct: false },
            { question: "Delas in i ngt", correct: true },
            { question: "VVS-husteknik", correct: true },
            { question: "En elektrisk husteknik", correct: false },
            { question: "Utöver ngt", correct: true },
            { question: "Naturvetenskapliga ämnen", correct: true },
            { question: "En studerande, studeranden", correct: true },
            { question: "Kompetenser, kompetenserna i ngt", correct: false },
            { question: "En planering, planeringen", correct: true },
            { question: "En planerare, planeraren, planerare, planerarna", correct: true },
            { question: "En produktion, produktionen", correct: true },
            { question: "En hantering, hanteringen", correct: false },
            { question: "En utveckling, utvecklingen", correct: true },
            { question: "Ett värmesystem, -systemet, -system, -systemen", correct: true },
            { question: "Ett vattenförsörjningssystem", correct: false },
            { question: "Ett ventilationssystem", correct: true },
            { question: "Den utexaminerade", correct: true },
            { question: "Behärska, behärskar, behärskade, behärskat", correct: false },
            { question: "Tillämpa, tillämpar, tillämpade, tillämpat", correct: true },
            { question: "Miljövänlig", correct: true },
            { question: "Energieffektiv", correct: true },
            { question: "Ett bruk, bruket", correct: false },
            { question: "Hälsosam", correct: true },
            { question: "Trivsam", correct: true },
            { question: "En bo- och arbetsmiljö, -miljön", correct: false },
            { question: "Förverkliga, förverkligar, förverkligade, förverkligat", correct: true },
            { question: "Fungerande", correct: true },
            { question: "Säker, säkert, säkra", correct: true },
            { question: "En lösning, lösningen, lösningar, lösningarna", correct: false }
        ];

        let score = 0;
        let currentQuestionIndex = 0;

        function showQuestion() {
            document.getElementById("question").textContent = questions[currentQuestionIndex].question;
        }

        function checkAnswer(isCorrect) {
            if (isCorrect === questions[currentQuestionIndex].correct) {
                score++;
            }
            currentQuestionIndex++;
            if (currentQuestionIndex < questions.length) {
                showQuestion();
            } else {
                alert("Peli ohi! Lopulliset pisteet: " + score);
                currentQuestionIndex = 0;
                score = 0;
                showQuestion();
            }
            document.getElementById("score").textContent = "Pisteet: " + score;
        }

        showQuestion();
    </script>
</body>
</html>
