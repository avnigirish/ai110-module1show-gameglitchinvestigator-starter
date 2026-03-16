# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
  - The game was not organized well. The instructions were inaccurate (i.e. the number of attempts the user had to guess the game). And the instruction was incorrect for when a user entered a number to guess the secret. And sometimes, when the secret is guessed, it would say "Game Over" and "You won."
- List at least two concrete bugs you noticed at the start  
  (for example: "the secret number kept changing" or "the hints were backwards").
  1. The hints were backwards from the range of 1-100. However, if entered a number outside the range, it simply said to go lower even if the number is bellow the range
  2. I noticed that the `New Game` button doesn't actually work and refresh the game. When clicked on the button, it said "ypu already won. Start a new game to play again." And there were score mismatches from the debugger output and the website screen

---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
  - Used **Claude Code** (Claude AI) as the primary assistant for debugging, identifying root causes, and implementing fixes.

- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
  - **Correct Suggestion**: Claude identified that the "Hard" difficulty range bug was caused by returning (1, 50) instead of a wider range like (1, 500). The AI correctly explained that Hard (1-50) was actually narrower than Normal (1-100), making Hard easier to guess rather than harder. I verified this by: (1) Reading the code and confirming 50 < 100, (2) Running the game at different difficulty levels and seeing that Hard was easier to guess, and (3) Testing with pytest after the fix to ensure the range was correct.

- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).
  - **Misleading Suggestion**: Claude initially suggested that the game's random number generation needed to be refactored using a custom random seed class. However, after deeper investigation, I realized the core issue was simpler: Streamlit was rerunning the entire page on each interaction, causing `random.randint()` to be called multiple times. The actual fix was to add Streamlit session state checks (`if "secret" not in st.session_state`) to preserve the secret number across reruns. I verified the fix worked by clicking the "New Game" button and confirming the secret persisted until explicitly reset, rather than changing on every submit.

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
  - I used a combination of manual testing and pytest. For each fix, I would run the game in Streamlit and test the specific behavior: (1) For the submit button bug, I entered invalid inputs (letters, decimals) and confirmed the attempt counter didn't increment. (2) For the difficulty range bug, I checked that Hard actually produced larger secret numbers than Normal. (3) I also ran `pytest` to verify all game logic functions returned correct outputs.

- Describe at least one test you ran (manual or using pytest) and what it showed you about your code.
  - **Manual Test**: Clicked "Submit Guess" with empty input → confirmed it showed error message and attempts stayed at same count, proving the attempt wasn't wasted. Clicked with valid number → confirmed attempts incremented. Also tested the difficulty range by selecting Hard, playing multiple games, and observing secrets were in 1-500 range (not 1-50).
  - **Pytest Test**: Ran `pytest test_logic_utils.py -v` which tested the `check_guess()` function with multiple cases (correct guess, too high, too low) and the `update_score()` function with various outcomes and attempt numbers. All 8 tests passed, confirming the refactored logic worked correctly.

- Did AI help you design or understand any tests? How?
  - Yes. Claude helped explain why certain tests were important (e.g., testing edge cases like high/low boundary guesses) and reviewed the test structure in conftest.py to understand how Streamlit session state fixtures were set up. This helped me understand that testing pure game logic separately from the UI was the right approach.

---

## 4. What did you learn about Streamlit and state?

- In your own words, explain why the secret number kept changing in the original app.
- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?
- What change did you make that finally gave the game a stable secret number?

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.
