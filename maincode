<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Align-dle: The Marketing Strategy Game</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f7f9fc;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        /* Custom colors for the Dle logic */
        .color-green { background-color: #10b981; color: white; } /* Emerald 500 */
        .color-yellow { background-color: #f59e0b; color: white; } /* Amber 500 */
        .color-red { background-color: #ef4444; color: white; } /* Red 500 */
        .color-default { background-color: #e5e7eb; color: #1f2937; } /* Gray 200 */
        .shadow-marketing { box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05); }
    </style>
</head>
<body>

<div id="app" class="w-full max-w-xl mx-4 my-8 p-6 bg-white rounded-xl shadow-marketing border-t-8 border-indigo-600">
    <h1 class="text-3xl font-extrabold text-gray-900 text-center mb-1">🎯 Align-dle</h1>
    <p class="text-center text-gray-500 mb-6">Guess the Marketing Objective & Platform in 6 tries.</p>

    <!-- Scenario Display -->
    <div class="bg-indigo-50 p-4 rounded-lg mb-6 border-l-4 border-indigo-400">
        <p class="text-sm font-semibold text-indigo-600 mb-1">Today's Campaign Scenario:</p>
        <p id="scenario-text" class="text-lg text-gray-800 font-medium italic">Loading...</p>
    </div>

    <!-- Input Controls -->
    <div id="input-area" class="space-y-4 mb-6">
        <select id="objective-select" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-indigo-500 focus:border-indigo-500 text-gray-700">
            <option value="">Select Objective...</option>
        </select>
        <select id="platform-select" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-indigo-500 focus:border-indigo-500 text-gray-700">
            <option value="">Select Platform...</option>
        </select>
        <button id="submit-guess-btn" onclick="checkGuess()" class="w-full bg-indigo-600 text-white font-bold py-3 rounded-lg hover:bg-indigo-700 transition duration-150 disabled:opacity-50" disabled>
            Submit Guess (6 remaining)
        </button>
    </div>

    <!-- Message Box -->
    <div id="message-box" class="text-center text-sm font-semibold mb-4 hidden"></div>

    <!-- History Grid -->
    <div id="history-grid" class="space-y-2">
        <!-- Guesses will be inserted here -->
    </div>
</div>

<script>
    // --- Configuration ---
    const MAX_GUESSES = 6;
    let guessesRemaining = MAX_GUESSES;
    let targetScenario = {};
    let isGameOver = false;

    // The universe of possible objectives and platforms
    const OBJECTIVES = [
        "Awareness", "Consideration", "Lead Generation", "Conversion", "Retention",
        "Loyalty/Advocacy", "Brand Equity", "Market Research"
    ];

    const PLATFORMS = [
        "TikTok", "Instagram", "YouTube", "Email Marketing", "Google Ads (Search)",
        "Facebook (Meta)", "LinkedIn", "TV/Streaming Ads"
    ];

    // Strategic Mapping for the "Yellow" Proximity Logic
    const OBJECTIVE_STAGES = {
        "Awareness": "Top", "Brand Equity": "Top",
        "Consideration": "Mid", "Market Research": "Mid",
        "Lead Generation": "Mid",
        "Conversion": "Bottom",
        "Retention": "Post", "Loyalty/Advocacy": "Post"
    };

    const PLATFORM_MEDIA_TYPES = {
        "TikTok": "ShortVideo", "Instagram": "ShortVideo",
        "YouTube": "LongVideo", "TV/Streaming Ads": "LongVideo",
        "Email Marketing": "Direct",
        "Google Ads (Search)": "Search",
        "Facebook (Meta)": "PaidSocial", "LinkedIn": "PaidSocial"
    };

    // Array of Scenarios (The Daily Puzzle)
    const SCENARIOS = [
        {
            scenario: "A B2C sneaker brand created a 15-second, high-energy video showcasing a new shoe model being used by diverse athletes performing extreme stunts.",
            objective: "Awareness",
            platform: "TikTok"
        },
        {
            scenario: "A B2B SaaS company launched a series of gated whitepapers and email drip campaigns to nurture MQLs.",
            objective: "Lead Generation",
            platform: "Email Marketing"
        },
        {
            scenario: "An e-commerce brand targeted customers who abandoned their carts in the last 24 hours with a limited-time discount code.",
            objective: "Conversion",
            platform: "Facebook (Meta)"
        },
        {
            scenario: "A tech startup ran a campaign asking users to fill out a 10-minute survey about market needs and pain points for a new product category.",
            objective: "Market Research",
            platform: "LinkedIn"
        },
        {
            scenario: "A premium travel company created an exclusive online community forum and sent personalized anniversary deals to past clients.",
            objective: "Loyalty/Advocacy",
            platform: "Email Marketing"
        },
        {
            scenario: "A local service business bid on high-intent keywords like 'plumber near me' and 'emergency electrician'.",
            objective: "Conversion",
            platform: "Google Ads (Search)"
        },
        {
            scenario: "A financial institution released a long-form 30-minute documentary on responsible investing and promoted it with a pre-roll ad.",
            objective: "Brand Equity",
            platform: "YouTube"
        }
    ];

    // --- DOM Elements ---
    const scenarioText = document.getElementById('scenario-text');
    const objectiveSelect = document.getElementById('objective-select');
    const platformSelect = document.getElementById('platform-select');
    const submitButton = document.getElementById('submit-guess-btn');
    const historyGrid = document.getElementById('history-grid');
    const messageBox = document.getElementById('message-box');

    // --- Utility Functions for Logic ---

    /** Gets the strategic funnel stage for the objective. */
    function getObjectiveStage(objective) {
        return OBJECTIVE_STAGES[objective] || null;
    }

    /** Gets the strategic media type for the platform. */
    function getPlatformType(platform) {
        return PLATFORM_MEDIA_TYPES[platform] || null;
    }

    /** Determines the color based on the guess and target */
    function determineColor(guess, target, mappingFn) {
        if (guess === target) {
            return 'green'; // Perfect match
        }
        if (mappingFn(guess) && mappingFn(guess) === mappingFn(target)) {
            return 'yellow'; // Strategic proximity match
        }
        return 'red'; // No match
    }

    // --- Game Functions ---

    function initializeDropdowns() {
        // Populate Objective dropdown
        OBJECTIVES.forEach(obj => {
            const option = document.createElement('option');
            option.value = obj;
            option.textContent = obj;
            objectiveSelect.appendChild(option);
        });

        // Populate Platform dropdown
        PLATFORMS.forEach(plat => {
            const option = document.createElement('option');
            option.value = plat;
            option.textContent = plat;
            platformSelect.appendChild(option);
        });
    }

    function startGame() {
        try {
            // Select a random scenario for the daily puzzle
            const todayIndex = Math.floor(Math.random() * SCENARIOS.length);
            targetScenario = SCENARIOS[todayIndex];

            // Set up the UI
            scenarioText.textContent = targetScenario.scenario;
            historyGrid.innerHTML = '';
            messageBox.style.display = 'none';
            messageBox.className = 'text-center text-sm font-semibold mb-4';
            guessesRemaining = MAX_GUESSES;
            isGameOver = false;

            updateButtonState();

            // Clear selections
            objectiveSelect.value = '';
            platformSelect.value = '';
        } catch (error) {
            console.error("Failed to start game:", error);
            scenarioText.textContent = "Error loading scenario. Please refresh.";
        }
    }

    function updateButtonState() {
        submitButton.textContent = `Submit Guess (${guessesRemaining} remaining)`;
        const objectiveSelected = objectiveSelect.value !== '';
        const platformSelected = platformSelect.value !== '';
        submitButton.disabled = isGameOver || !(objectiveSelected && platformSelected);
    }

    function displayMessage(text, colorClass) {
        messageBox.textContent = text;
        messageBox.className = `text-center text-sm font-semibold mb-4 p-2 rounded ${colorClass}`;
        messageBox.style.display = 'block';
    }

    function checkGuess() {
        if (isGameOver || guessesRemaining <= 0) return;

        const guessObjective = objectiveSelect.value;
        const guessPlatform = platformSelect.value;

        if (!guessObjective || !guessPlatform) {
            displayMessage("Please select both an Objective and a Platform.", 'bg-yellow-100 text-yellow-800');
            return;
        }

        const objectiveColor = determineColor(guessObjective, targetScenario.objective, getObjectiveStage);
        const platformColor = determineColor(guessPlatform, targetScenario.platform, getPlatformType);

        // 1. Create Guess Row
        const guessRow = document.createElement('div');
        guessRow.className = 'flex space-x-2';
        guessRow.innerHTML = `
            <div class="flex-1 p-3 rounded-lg text-center font-bold text-sm color-${objectiveColor} transition duration-300">
                ${guessObjective}
            </div>
            <div class="flex-1 p-3 rounded-lg text-center font-bold text-sm color-${platformColor} transition duration-300">
                ${guessPlatform}
            </div>
        `;
        historyGrid.prepend(guessRow);

        // 2. Check Win Condition
        if (objectiveColor === 'green' && platformColor === 'green') {
            isGameOver = true;
            displayMessage("🏆 SUCCESS! You nailed the strategy!", 'bg-green-100 text-green-800');
            submitButton.textContent = `SOLVED!`;
            submitButton.disabled = true;
            return;
        }

        // 3. Update Game State
        guessesRemaining--;
        updateButtonState();

        // 4. Check Lose Condition
        if (guessesRemaining === 0) {
            isGameOver = true;
            displayMessage(
                `❌ Game Over! The correct strategy was: ${targetScenario.objective} on ${targetScenario.platform}.`,
                'bg-red-100 text-red-800'
            );
            submitButton.textContent = `Try Again Tomorrow!`;
            submitButton.disabled = true;
        } else {
            // Optional: Clear selections for next guess
            objectiveSelect.value = '';
            platformSelect.value = '';
            displayMessage(`Guess #${MAX_GUESSES - guessesRemaining} submitted. Keep going!`, 'bg-indigo-100 text-indigo-800');
        }
    }

    // --- Initialize on load ---
    window.onload = () => {
        initializeDropdowns();
        startGame();

        // Add event listener to enable button when selections are made
        const handleChange = () => {
            updateButtonState();
            messageBox.style.display = 'none'; // Clear minor messages on interaction
        };
        objectiveSelect.addEventListener('change', handleChange);
        platformSelect.addEventListener('change', handleChange);
    };

</script>

</body>
</html>
