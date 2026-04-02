# KisanDost: Hackathon Architecture & Jury Guide

This document is written in extremely simple, non-technical terms so you can confidently explain the "magic" behind the platform to any hackathon jury, even if you do not know Python or complex Machine Learning.

---

## 🌾 Part 1: How does the Yield Predictor Work?

The Yield Predictor does not just guess randomly. It relies on an **Environmental Multiplier Algorithm** that looks at three core factors which determine farm success. 

### The Three Core Factors
1. **NDVI (Vegetation Health):** Measures how green and leafy the plants are. 
2. **Soil Moisture:** Measures how wet the ground is (Optimal is usually around 45%).
3. **Rainfall:** Measures precipitation (Optimal is 250-350mm).

### The Math (How the calculation works)
Instead of relying strictly on complex Neural Networks, our system uses a weighted scoring system based on agricultural thresholds. 

1. **Base Yield:** Every crop has a standard "Base Yield". For example, Wheat generally yields 3.0 tons per acre under normal conditions.
2. **Impact Factors:** The system calculates a score/multiplier for each of the three conditions:
   - *If NDVI is very low (bare soil)*, it drastically cuts the potential yield (multiplier drops to 0.05x).
   - *If Moisture is perfect (~45%)*, it acts as a massive boost (multiplier jumps to 2.5x). If there is a massive drought or extreme flooding, it penalizes the score.
   - *If Rainfall is optimal (~300mm)*, it gives a 2.0x boost.
3. **The Final Equation:** 
   The system gives weight to all three factors to build one **Combined Environmental Factor**:
   - 45% importance is given to NDVI
   - 40% importance is given to Soil Moisture
   - 15% importance is given to Rainfall

   It then takes the `Base Yield` and multiplies it by this `Combined Environmental Factor` to get the final `Yield Per Acre`. Finally, it multiplies that by the farmer's land size to get the **Total Predicted Yield**.

*(Note for the Jury: The app is capable of routing these numbers to a Python FastAPI backend for deeper predictions, but it also has this mathematically sound ultra-sensitive fallback algorithm built directly into TypeScript)*

---

## 🤖 Part 2: How does the AI Crop Suggestion Map Work?

The AI Crop Suggestion tool turns a raw map into a dynamic, intelligent farming assistant through a 3-step pipeline.

### Step 1: Solving the Location (Reverse Geocoding)
When the user clicks anywhere on the map of Gujarat:
- The map captures the exact latitude (X) and longitude (Y).
- We send these coordinates into a **Weather API**. 
- The Weather API translates those GPS points into a human-readable city/district name (e.g., "Ahmedabad" or "Surat").

### Step 2: Grabbing the Local Context
We don't immediately ask the AI for generic advice. That would result in bad, generic answers. 
Instead, we take that District Name and look it up in our custom `districts-dataset.json` database. This database holds deep local knowledge, such as:
- What season is it currently?
- How much water is natively available in this region?
- What crops traditionally grow best here?

### Step 3: NVIDIA Generative AI (The Magic)
Now that we have all the puzzle pieces, we send a highly specific "Prompt" to **NVIDIA's powerful NIM AI Model (powered by Meta LLaMA 3 70B)**. 

The prompt is injected behind-the-scenes with the exact **Current Date** and commands the AI: 
> *"Act as an expert agricultural advisor for Gujarat. The current date is [Today]. The farmer is in [Ahmedabad district]. Water conditions here are [Medium]. Traditionally they grow [Cotton, Wheat]. Based on this exact time of year, what should they do right now, and what should they prepare for in the next 1-2 months?"*

Because the NVIDIA AI receives such a heavily structured and timely prompt, it guarantees a 3-4 sentence response that is hyper-personalized, fully actionable, and highly relevant to the actual current date and location!
