# Home Fitness Coach AI
## Project Report

---

**Project Title:** Home Fitness Coach - AI-Powered Personal Trainer Chatbot

**Course:** UpGrad AI/ML Program - Build Your Own Project (BYOP)

**Date:** November 2025

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Project Objectives](#2-project-objectives)
3. [System Design](#3-system-design)
4. [Technical Implementation](#4-technical-implementation)
5. [User Experience Design](#5-user-experience-design)
6. [Challenges Faced](#6-challenges-faced)
7. [Evaluation & Results](#7-evaluation--results)
8. [Lessons Learned](#8-lessons-learned)
9. [Future Scope](#9-future-scope)
10. [Conclusion](#10-conclusion)
11. [References](#11-references)

---

## 1. Executive Summary

**Home Fitness Coach** is an AI-powered conversational chatbot designed to provide personalized workout recommendations, exercise guidance, and nutrition advice for home fitness enthusiasts. The system leverages Google's Gemini 2.5 Flash model through the OpenAI-compatible API, combined with the Wger Workout Manager API for real-time exercise data.

The chatbot employs a multi-layer architecture that:
- Gathers comprehensive user fitness profiles through natural conversation
- Matches users with appropriate exercises based on their goals, equipment, and limitations
- Generates personalized workout plans with nutrition guidance
- Handles follow-up questions about exercises and form

This project demonstrates the practical application of large language models (LLMs) in the health and fitness domain, combining conversational AI with rule-based systems for reliable, safe recommendations.

---

## 2. Project Objectives

### Primary Objectives

1. **Build a Conversational Fitness Assistant**: Create a chatbot that naturally gathers user information and provides personalized fitness guidance through dialogue.

2. **Implement Multi-Layer Architecture**: Design a modular system with distinct layers for intent clarity, confirmation, product mapping, and recommendations.

3. **Integrate External APIs**: Connect with real exercise databases to provide accurate, up-to-date workout information.

4. **Ensure User Safety**: Implement moderation checks and limitation-aware filtering to protect users from harmful content or inappropriate exercises.

### Secondary Objectives

1. Provide nutrition guidance aligned with fitness goals
2. Create a fallback system for offline functionality
3. Develop an evaluation framework for measuring system accuracy
4. Document design decisions for future reference and improvement

### Success Criteria

| Metric | Target | Achieved |
|--------|--------|----------|
| Profile extraction accuracy | >80% | Evaluated |
| Exercise relevance scoring | Functional | ✓ |
| Conversation naturalness | Subjective | ✓ |
| Safety moderation | 100% coverage | ✓ |
| API integration | Working | ✓ |

---

## 3. System Design

### 3.1 Architecture Overview

The Home Fitness Coach follows a **5-layer architecture** adapted from the ShopAssistAI reference project:

```
┌─────────────────────────────────────────────────────────────┐
│                    USER INTERFACE                            │
│              (Text-based conversation)                       │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│              LAYER 1: INTENT CLARITY                         │
│  • Welcomes user                                             │
│  • Asks profiling questions                                  │
│  • Uses chain-of-thought prompting                           │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│            LAYER 2: INTENT CONFIRMATION                      │
│  • Validates profile completeness                            │
│  • Extracts dictionary from response                         │
│  • Applies moderation checks                                 │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│            LAYER 3: WORKOUT MAPPING                          │
│  • Fetches exercises from Wger API                           │
│  • Caches data locally                                       │
│  • Maps exercises to muscle groups                           │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│            LAYER 4: FITNESS MATCHING                         │
│  • Scores exercises against user profile                     │
│  • Filters by equipment, limitations                         │
│  • Ranks by relevance                                        │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│            LAYER 5: RECOMMENDATION                           │
│  • Generates workout plans                                   │
│  • Provides nutrition guidance                               │
│  • Handles follow-up Q&A                                     │
└─────────────────────────────────────────────────────────────┘
```

### 3.2 User Profile Schema

The system captures 8 key attributes to create a comprehensive fitness profile:

```python
{
    'fitness_level': 'beginner | intermediate | advanced',
    'primary_goal': 'weight_loss | muscle_building | flexibility | endurance | general_fitness',
    'available_equipment': 'none | basic | full_gym',
    'time_per_session': '15 | 30 | 45 | 60',  # minutes
    'workout_frequency': 'low | medium | high',  # days per week
    'age_group': 'under_30 | 30_50 | over_50',
    'limitations': 'none | back | knee | shoulder | other',
    'preferred_style': 'cardio | strength | hiit | yoga | mixed'
}
```

### 3.3 Data Flow

1. **User Input** → Moderation Check → Intent Clarity Layer
2. **Profile Questions** → User Responses → Intent Confirmation
3. **Complete Profile** → Wger API Query → Exercise Database
4. **Exercise List** → Scoring Algorithm → Top Matches
5. **Matched Exercises** → LLM Generation → Workout Plan
6. **Follow-up Questions** → Recommendation Layer → Responses

### 3.4 Technology Stack

| Component | Technology | Rationale |
|-----------|------------|-----------|
| LLM | Gemini 2.5 Flash | Latest Google model with strong reasoning |
| API Interface | OpenAI SDK | Familiar patterns, easy migration |
| Exercise Data | Wger API | Free, comprehensive, no auth required |
| Language | Python 3.x | Industry standard for AI/ML |
| Environment | Jupyter Notebook | Interactive development and documentation |

---

## 4. Technical Implementation

### 4.1 Core Functions

#### 4.1.1 Conversation Initialization

```python
def initialize_conversation():
    """
    Creates system prompt with:
    - Role definition (fitness coach)
    - Profile schema and valid values
    - Chain-of-thought reasoning steps
    - Few-shot conversation examples
    """
```

The system prompt uses several prompt engineering techniques:
- **Role Assignment**: "You are an expert personal fitness coach"
- **Structured Output**: Defines exact dictionary format expected
- **Chain-of-Thought**: Four explicit reasoning steps
- **Few-Shot Learning**: Complete sample conversation included

#### 4.1.2 Chat Completion

```python
def get_chat_completions(messages, temperature=0.3):
    """
    Calls Gemini via OpenAI-compatible endpoint.
    Low temperature ensures consistent, focused responses.
    """
    response = client.chat.completions.create(
        model="gemini-2.5-flash",
        messages=messages,
        temperature=temperature
    )
    return response.choices[0].message.content
```

#### 4.1.3 Moderation Check

```python
def moderation_check(user_input):
    """
    Uses LLM to evaluate content for:
    - Hate speech, violence, explicit content
    - Self-harm promotion
    - Dangerous medical advice requests
    Returns: 'Flagged' or 'Not Flagged'
    """
```

#### 4.1.4 Exercise Scoring Algorithm

The scoring system evaluates exercises on a 10-point scale:

| Criterion | Max Points | Logic |
|-----------|------------|-------|
| Equipment Match | 3 | Bodyweight = 3, matching equipment = 3, missing = 0 |
| Goal Alignment | 3 | 2+ muscle overlap = 3, 1 = 2, 0 = 1 |
| Difficulty Match | 2 | Same level = 2, ±1 level = 1, ±2 = 0 |
| Style Preference | 2 | Direct match = 2, versatile = 1 |
| **Limitation Penalty** | -10 | Exercises targeting injured areas |

```python
def score_exercise(exercise, user_profile):
    score = 0
    # Equipment scoring
    # Goal alignment scoring
    # Difficulty matching
    # Style preference
    # Limitation safety check (penalty)
    return max(0, score)
```

### 4.2 API Integration

#### Wger Workout Manager API

- **Base URL**: `https://wger.de/api/v2`
- **Endpoints Used**:
  - `/exerciseinfo/` - Exercise details with muscles and equipment
- **No Authentication Required** for basic endpoints
- **Rate Limiting**: Handled with timeout and fallback

```python
def fetch_exercises_from_api(language=2, limit=100):
    url = f"{WGER_BASE_URL}/exerciseinfo/?language={language}&limit={limit}"
    response = requests.get(url, timeout=10)
    # Parse and structure exercise data
```

#### Fallback Database

25 curated exercises covering:
- Bodyweight exercises (10)
- Basic equipment exercises (6)
- Yoga/flexibility exercises (4)
- Advanced/HIIT exercises (5)

### 4.3 Nutrition Integration

Goal-specific nutrition guidelines:

| Goal | Calories | Protein | Key Tips |
|------|----------|---------|----------|
| Weight Loss | Deficit 300-500 | 1.6-2.2g/kg | Water, vegetables, avoid sugar |
| Muscle Building | Surplus 200-400 | 1.8-2.4g/kg | Post-workout protein, sleep |
| Flexibility | Maintenance | 1.2-1.6g/kg | Anti-inflammatory foods |
| Endurance | Slightly higher | 1.4-1.8g/kg | Carb-loading, electrolytes |
| General Fitness | Balanced | 1.2-1.6g/kg | Variety, whole foods |

---

## 5. User Experience Design

### 5.1 Conversation Flow

The chatbot follows a structured yet natural conversation pattern:

**Phase 1: Profile Gathering**
1. Warm welcome and goal inquiry
2. Fitness level assessment
3. Equipment and time availability
4. Age, limitations, and preferences
5. Profile confirmation

**Phase 2: Recommendation Delivery**
1. Structured workout plan presentation
2. Sets, reps, and rest periods
3. Warm-up and cool-down suggestions
4. Nutrition tips

**Phase 3: Follow-up Support**
- Exercise explanations
- Form guidance
- Alternative suggestions
- Nutrition questions

### 5.2 Response Design Principles

1. **Encouraging Tone**: Positive language, supportive of all fitness levels
2. **Clarity**: Clear formatting with lists and structure
3. **Personalization**: References user's specific profile in responses
4. **Safety First**: Always considers limitations and modifications

### 5.3 Error Handling

| Scenario | Response |
|----------|----------|
| Flagged content | Redirect to fitness topics |
| API failure | Use fallback database |
| Incomplete profile | Continue asking questions |
| No matching exercises | Expand criteria or human handoff |

---

## 6. Challenges Faced

### 6.1 Profile Extraction Consistency

**Challenge**: LLMs output profiles in various formats (dictionary, bullet points, narrative text), making extraction unreliable.

**Solution**: Implemented dual extraction approach:
1. Regex-based extraction for standard dictionary format
2. LLM-assisted extraction as fallback for non-standard formats

```python
def extract_dictionary_from_string(string):
    # Regex pattern for dictionary structures
    regex_pattern = r"\{[^{}]+\}"
    # Normalize and parse
```

### 6.2 Exercise Relevance Balancing

**Challenge**: Multiple factors (equipment, goals, limitations, style) needed to be balanced without any single factor dominating.

**Solution**: Developed weighted scoring system with:
- Maximum 10 points distributed across 4 criteria
- Heavy penalty (-10) for limitation violations
- Minimum score threshold for recommendations

### 6.3 API Reliability

**Challenge**: External API dependency could cause failures.

**Solution**: 
- Implemented 10-second timeout
- Created comprehensive fallback database
- Added CSV caching for successful API calls

### 6.4 Conversation Naturalness vs. Data Collection

**Challenge**: Gathering 8 profile attributes while maintaining natural conversation flow.

**Solution**:
- Chain-of-thought prompting guides systematic data collection
- Few-shot examples demonstrate ideal conversation patterns
- 2-3 questions maximum per response

---

## 7. Evaluation & Results

### 7.1 Test Personas

Three distinct personas were created to test system versatility:

**Persona 1: Weight Loss Beginner (Sarah)**
- Goal: Weight loss
- Level: Beginner
- Equipment: Basic (bands)
- Limitations: None

**Persona 2: Muscle Building Intermediate (Alex)**
- Goal: Muscle building
- Level: Intermediate
- Equipment: Full gym
- Limitations: Shoulder (healed)

**Persona 3: Senior Flexibility Focus (Margaret)**
- Goal: Flexibility
- Level: Beginner
- Equipment: None (yoga mat)
- Limitations: Back

### 7.2 Evaluation Metrics

```python
def evaluate_profile_extraction(expected, extracted):
    """
    Compares extracted profile against expected values.
    Returns: score, accuracy percentage, matches, mismatches
    """
```

### 7.3 Results Summary

| Persona | Expected Keys | Matched | Accuracy |
|---------|---------------|---------|----------|
| Weight Loss Beginner | 8 | Variable* | Variable* |
| Muscle Building | 8 | Variable* | Variable* |
| Flexibility Focus | 8 | Variable* | Variable* |

*Results vary based on LLM response variability; evaluation code included in notebook for live testing.

### 7.4 Qualitative Observations

1. **Conversation Quality**: Natural, encouraging dialogue maintained
2. **Profile Accuracy**: Core attributes (goal, level, equipment) consistently captured
3. **Exercise Relevance**: Matched exercises aligned with user profiles
4. **Safety**: Limitation filtering worked correctly (back issues → no back exercises)

---

## 8. Lessons Learned

### 8.1 Technical Lessons

1. **Prompt Engineering Matters**: Well-structured prompts with examples significantly improve output consistency.

2. **Hybrid Systems Work**: Combining LLM reasoning with rule-based logic provides both flexibility and reliability.

3. **Fallbacks Are Essential**: External dependencies require robust fallback mechanisms.

4. **Modular Architecture Pays Off**: Separating concerns into layers made debugging and iteration easier.

### 8.2 Domain Lessons

1. **Safety is Paramount**: In health/fitness applications, safety considerations must be built into every layer.

2. **Personalization Requires Detail**: More profile attributes enable better recommendations but increase conversation length.

3. **User Experience Over Technical Elegance**: Natural conversation flow matters more than optimal data collection.

### 8.3 Process Lessons

1. **Start with Reference Implementation**: Using ShopAssistAI as a template accelerated development.

2. **Test with Diverse Personas**: Different user types reveal edge cases and improve robustness.

3. **Document As You Build**: Inline documentation and markdown cells made the project self-explanatory.

---

## 9. Future Scope

### 9.1 Short-term Improvements

1. **Progress Tracking**: Add workout logging and visualization
2. **Exercise Images/Videos**: Integrate visual form guidance
3. **More Granular Limitations**: Support multiple simultaneous limitations

### 9.2 Medium-term Enhancements

1. **Adaptive Difficulty**: Adjust intensity based on user feedback
2. **Voice Interface**: Speech-to-text for hands-free interaction
3. **Detailed Meal Planning**: Expand nutrition to full meal plans

### 9.3 Long-term Vision

1. **Wearable Integration**: Connect with fitness trackers
2. **Fine-tuned Model**: Train specialized fitness LLM
3. **Community Features**: Share workouts, compare progress
4. **Professional Review**: Partnership with certified trainers

---

## 10. Conclusion

The **Home Fitness Coach** project successfully demonstrates the application of conversational AI in the health and fitness domain. By combining Gemini 2.5 Flash's natural language capabilities with rule-based exercise matching and external API integration, the system provides personalized, safe, and actionable fitness guidance.

Key achievements:
- ✅ Multi-layer conversational architecture
- ✅ Real-time API integration with fallback
- ✅ Comprehensive user profiling (8 attributes)
- ✅ Safety-aware exercise recommendations
- ✅ Integrated nutrition guidance
- ✅ Evaluation framework with test personas

The project serves as a template for building domain-specific AI assistants that balance conversational flexibility with structured, reliable outputs. The modular design allows for easy extension and improvement as LLM capabilities evolve.

---

## 11. References

1. **OpenAI API Documentation**: https://platform.openai.com/docs
2. **Google Gemini API**: https://ai.google.dev/docs
3. **Wger Workout Manager API**: https://wger.de/api/v2/
4. **ShopAssistAI Reference Project**: UpGrad Course Materials
5. **Prompt Engineering Guide**: OpenAI Best Practices
6. **American College of Sports Medicine**: Exercise Guidelines

---

## Appendix A: File Structure

```
homeFitnessCoach/
├── HomeFitnessCoach.ipynb    # Main implementation notebook
├── Project_Report.pdf         # This report (converted)
├── api_key.txt               # Gemini API key (user-provided)
└── exercise_cache.csv        # Cached exercise data
```

## Appendix B: How to Run

1. **Setup API Key**: Create `api_key.txt` with your Gemini API key
2. **Install Dependencies**: Run `!pip install openai requests pandas`
3. **Execute Cells**: Run all cells in order
4. **Start Chatbot**: Uncomment `dialogue_mgmt_system()` in Part 7

## Appendix C: Sample Conversation

```
🏋️ FITNESS COACH: Welcome! I'm your personal fitness coach...

You: Hi, I want to lose weight

🏋️ FITNESS COACH: Great goal! Let me understand your situation better.
What's your current fitness level?

You: I'm a beginner, haven't exercised in years

🏋️ FITNESS COACH: No problem! What equipment do you have access to?

[... conversation continues until profile complete ...]

🏋️ FITNESS COACH: Here's your personalized workout plan:

**30-Minute Weight Loss Workout**
1. Jumping Jacks - 3 sets x 30 seconds
2. Squats - 3 sets x 12 reps
3. Mountain Climbers - 3 sets x 20 seconds
...
```

---

*End of Report*

