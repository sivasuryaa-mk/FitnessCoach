# 🏋️ AI Fitness Coach

An AI-powered personal trainer chatbot that provides personalized workout plans and nutrition guidance.

## Features

- **Personalized Workout Plans** - Tailored exercises based on your fitness level, goals, and equipment
- **Nutrition Calculations** - BMR/TDEE-based calorie and macro targets using Mifflin-St Jeor formula
- **Real-time Exercise Database** - Integration with Wger Workout Manager API
- **Safety-First Design** - Limitation-aware filtering and two-strike moderation system
- **Optional InBody Support** - Enhanced personalization with body composition data

## Architecture

The AI Fitness Coach follows a 5-layer architecture:

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/26b1e591-11f5-4927-8c9b-65c5d35e0d05" />



| Layer | Purpose |
|-------|---------|
| **Layer 1: Intent Clarity** | Welcomes user, asks profiling questions, uses chain-of-thought prompting |
| **Layer 2: Intent Confirmation** | Validates profile completeness, extracts dictionary, applies moderation |
| **Layer 3: Workout Mapping** | Fetches exercises dynamically from Wger API based on user profile, maps to muscle groups |
| **Layer 4: Fitness Matching** | Scores exercises against user profile, filters by equipment/limitations |
| **Layer 5: Recommendation** | Generates workout plans, provides nutrition guidance, handles Q&A |

## Quick Start

1. Create `api_key.txt` with your Gemini API key
2. Install dependencies: `pip install openai requests pandas`
3. Open `AIFitnessCoach.ipynb` and run all cells
4. Start chatting with your AI Fitness Coach!

## Tech Stack

- **LLM**: Google Gemini 2.5 Flash (via OpenAI-compatible API)
- **Exercise Data**: Wger Workout Manager API
- **Language**: Python 3.x / Jupyter Notebook
