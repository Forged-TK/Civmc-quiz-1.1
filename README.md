<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CivMC Quiz</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0f172a; /* Dark blue-gray background */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            color: #e2e8f0; /* Light gray text */
        }
        .quiz-container {
            background-color: #1e293b; /* Slightly lighter dark blue-gray */
            padding: 2.5rem;
            border-radius: 1rem; /* More rounded corners */
            box-shadow: 0 10px 15px rgba(0, 0, 0, 0.2);
            width: 100%;
            max-width: 500px;
            display: flex;
            flex-direction: column;
            gap: 1.5rem;
            text-align: center;
        }
        .question-text {
            font-size: 1.5rem;
            font-weight: 600;
            margin-bottom: 1.5rem;
            color: #f8fafc; /* White text for questions */
        }
        .options-container {
            display: flex;
            flex-direction: column;
            gap: 0.75rem;
            width: 100%;
        }
        .option-button {
            background-color: #334155; /* Medium dark blue-gray */
            color: #cbd5e1; /* Off-white text */
            padding: 0.75rem 1.25rem;
            border-radius: 0.625rem; /* Rounded button corners */
            border: 2px solid transparent;
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 1.1rem;
            width: 100%;
            text-align: left; /* Align text to the left */
        }
        .option-button:hover:not(.selected):not(.correct):not(.incorrect) {
            background-color: #475569; /* Darker hover */
            border-color: #64748b;
        }
        .option-button.selected {
            border-color: #60a5fa; /* Blue border for selected */
            background-color: #3b82f6; /* Blue background for selected */
            color: #ffffff;
        }
        .option-button.correct {
            background-color: #10b981; /* Green for correct */
            border-color: #059669;
            color: #ffffff;
        }
        .option-button.incorrect {
            background-color: #ef4444; /* Red for incorrect */
            border-color: #dc2626;
            color: #ffffff;
        }
        .action-button {
            background-color: #3b82f6; /* Blue button */
            color: white;
            padding: 0.875rem 2rem;
            border-radius: 0.75rem;
            border: none;
            cursor: pointer;
            font-size: 1.25rem;
            font-weight: 700;
            transition: background-color 0.3s ease, transform 0.1s ease;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            width: fit-content;
            align-self: center; /* Center the button */
        }
        .action-button:hover {
            background-color: #2563eb;
            transform: translateY(-2px);
        }
        .action-button:active {
            transform: translateY(0);
        }
        .score-container {
            font-size: 2rem;
            font-weight: 700;
            color: #a78bfa; /* Purple for score */
        }
        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div class="quiz-container">
        <h1 class="text-3xl font-bold text-center text-indigo-400 mb-4">CivMC Quiz</h1>

        <div id="quiz-screen">
            <div id="question-number" class="text-lg font-medium text-blue-300 mb-2">Question 1 of 5</div>
            <div id="question-text" class="question-text"></div>
            <div id="options-container" class="options-container">
                <!-- Options will be dynamically loaded here -->
            </div>
            <button id="next-button" class="action-button mt-6">Next Question</button>
        </div>

        <div id="result-screen" class="hidden">
            <h2 class="text-2xl font-bold mb-4">Quiz Completed!</h2>
            <div id="final-score" class="score-container"></div>
            <button id="restart-button" class="action-button mt-6">Restart Quiz</button>
        </div>
    </div>

    <script>
        // Array of quiz questions about CivMC
        const questions = [
            {
                question: "What is the primary focus of the CivMC Minecraft server?",
                options: ["PvP combat", "Building elaborate structures", "Civilization simulation", "Mini-games"],
                correctAnswer: 2 // Index of the correct answer (Civilization simulation)
            },
            {
                question: "Which unique CivMC mechanic allows players to protect their blocks and chests?",
                options: ["Enchanting", "Reinforcement", "Claiming", "Gating"],
                correctAnswer: 1 // Index of the correct answer (Reinforcement)
            },
            {
                question: "What is the main method for gaining XP in CivMC's economy, due to the Realistic Biomes plugin?",
                options: ["Mob grinding", "Mining", "Farming diverse crops", "Trading with NPCs"],
                correctAnswer: 2 // Index of the correct answer (Farming diverse crops)
            },
            {
                question: "Which significant CivMC event led to the dissolution of nations like Butternut and Gang-Shi?",
                options: ["The Great Build-off", "The Nether Rush", "The Grasslands Gambit", "The Enderman Invasion"],
                correctAnswer: 2 // Index of the correct answer (The Grasslands Gambit)
            },
            {
                question: "What is the name of the plugin that allows players to imprison others in CivMC?",
                options: ["PrisonBreak", "JailCraft", "ExilePearl", "Banishment"],
                correctAnswer: 2 // Index of the correct answer (ExilePearl)
            }
        ];

        // Global variables for quiz state
        let currentQuestionIndex = 0;
        let score = 0;
        let selectedOption = null; // To keep track of the currently selected option

        // DOM element references
        const quizScreen = document.getElementById('quiz-screen');
        const resultScreen = document.getElementById('result-screen');
        const questionNumberElement = document.getElementById('question-number');
        const questionTextElement = document.getElementById('question-text');
        const optionsContainer = document.getElementById('options-container');
        const nextButton = document.getElementById('next-button');
        const finalScoreElement = document.getElementById('final-score');
        const restartButton = document.getElementById('restart-button');

        /**
         * Displays the current question and its options.
         */
        function displayQuestion() {
            // Update question number display
            questionNumberElement.textContent = `Question ${currentQuestionIndex + 1} of ${questions.length}`;

            // Get the current question object
            const question = questions[currentQuestionIndex];
            questionTextElement.textContent = question.question;

            // Clear previous options
            optionsContainer.innerHTML = '';
            selectedOption = null; // Reset selected option for the new question

            // Create buttons for each option
            question.options.forEach((option, index) => {
                const button = document.createElement('button');
                button.textContent = option;
                button.classList.add('option-button');
                button.setAttribute('data-index', index); // Store the option's index

                // Add click listener to select an option
                button.addEventListener('click', () => selectOption(button, index));
                optionsContainer.appendChild(button);
            });

            // Re-enable the next button for the new question
            nextButton.textContent = "Next Question";
            nextButton.disabled = false;
        }

        /**
         * Handles the selection of an answer option.
         * Adds 'selected' class and stores the chosen option.
         * @param {HTMLButtonElement} button - The button element that was clicked.
         * @param {number} index - The index of the selected option.
         */
        function selectOption(button, index) {
            // Remove 'selected' class from previously selected option, if any
            if (selectedOption) {
                selectedOption.classList.remove('selected');
            }
            // Add 'selected' class to the newly selected option
            button.classList.add('selected');
            selectedOption = button; // Store the selected button element
        }

        /**
         * Checks the user's selected answer, updates the score, and highlights correct/incorrect options.
         * Disables further selection and prepares for the next question.
         */
        function checkAnswer() {
            // Ensure an option is selected before checking
            if (selectedOption === null) {
                // In a real app, you might show a message like "Please select an answer"
                console.log("Please select an answer before proceeding.");
                return;
            }

            const correctAnswerIndex = questions[currentQuestionIndex].correctAnswer;
            const selectedAnswerIndex = parseInt(selectedOption.getAttribute('data-index'));

            // Highlight the correct answer
            const optionButtons = optionsContainer.querySelectorAll('.option-button');
            optionButtons[correctAnswerIndex].classList.add('correct');

            // Check if the selected answer is correct
            if (selectedAnswerIndex === correctAnswerIndex) {
                score++;
                // The 'selected' class will already be on the button, which is now also 'correct'
            } else {
                // If incorrect, add 'incorrect' class to the selected option
                selectedOption.classList.add('incorrect');
            }

            // Disable all options after an answer is checked
            optionButtons.forEach(button => {
                button.disabled = true;
            });

            // Change button text to indicate progression or completion
            if (currentQuestionIndex === questions.length - 1) {
                nextButton.textContent = "Show Results";
            }
        }

        /**
         * Moves to the next question or finishes the quiz if all questions are answered.
         */
        function nextQuestion() {
            // Check the answer first if an option has been selected
            if (selectedOption !== null) {
                checkAnswer(); // Apply visual feedback for the current answer
            } else {
                 // If no option is selected, and next is pressed, log a message and do nothing further.
                 console.log("Please select an answer before moving to the next question.");
                 return;
            }

            // Disable the next button briefly to prevent double clicks or moving too fast
            nextButton.disabled = true;

            // Short delay to allow user to see correct/incorrect highlights
            setTimeout(() => {
                currentQuestionIndex++;
                if (currentQuestionIndex < questions.length) {
                    displayQuestion(); // Display the next question
                } else {
                    showResultScreen(); // Show results if all questions are answered
                }
            }, 700); // 700ms delay
        }


        /**
         * Shows the final score screen and hides the quiz screen.
         */
        function showResultScreen() {
            quizScreen.classList.add('hidden');
            resultScreen.classList.remove('hidden');
            finalScoreElement.textContent = `You scored ${score} out of ${questions.length}!`;
        }

        /**
         * Resets the quiz state and restarts the quiz from the beginning.
         */
        function restartQuiz() {
            currentQuestionIndex = 0;
            score = 0;
            selectedOption = null; // Reset selected option
            resultScreen.classList.add('hidden');
            quizScreen.classList.remove('hidden');
            displayQuestion(); // Start the quiz again
        }

        // Event Listeners
        nextButton.addEventListener('click', nextQuestion);
        restartButton.addEventListener('click', restartQuiz);

        // Initial call to start the quiz
        displayQuestion();
    </script>
</body>
</html>
