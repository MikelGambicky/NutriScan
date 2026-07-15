# NutriScan — AI Food Recognition & Nutrition Tracking

An Android app that identifies food from a photo and returns calorie and macronutrient estimates. Built in Kotlin as my final-year BSc Computer Science project at the University of West London (2026).

Point the camera at a meal → NutriScan identifies the food, filters out non-food noise (plates, cutlery, hands), and returns portion-scaled calories, protein, carbs, and fat. Scans are saved to a local history with daily summaries.

## How it works

1. **Image classification** — the photo is classified using the Clarifai image recognition API.
2. **Three-layer validation pipeline** (custom) — raw predictions are filtered through:
   - an edible-label whitelist (supported food vocabulary)
   - a non-edible ignore list (plates, cutlery, packaging, hands)
   - a confidence threshold — low-confidence results return the top 3 candidates for user correction instead of silently guessing
3. **Nutrition mapping** — the confirmed label is matched against a USDA-derived nutrition dataset and scaled by portion size (per-100g baseline).
4. **History & summaries** — scans persist in a local Room database, with daily calorie/macro summaries.

## Research background

The project's research phase evaluated a fine-tuned MobileNetV2 model for on-device classification, achieving **73.3% accuracy** on the test set (model files are included in `app/src/main/assets` for reference). The production build uses a cloud vision API for broader food coverage, with the custom validation pipeline handling precision. Usability testing with real users scored **76.9 on the System Usability Scale**, above the industry benchmark of 68.

## Tech stack

- Kotlin, Android SDK (min 26, target 34)
- OkHttp for API integration
- Room for local persistence
- Navigation component + ViewBinding
- Clarifai image recognition API

## Running the project

1. Clone the repo and open in Android Studio.
2. Get a free API key from [clarifai.com](https://www.clarifai.com).
3. Add this line to your `local.properties` file (created automatically by Android Studio, never committed to Git):
   ```
   CLARIFAI_API_KEY=your_key_here
   ```
4. Build and run on a device or emulator (API 26+).

## Project structure

```
app/src/main/java/com/nutriscan/
├── MainActivity.kt
├── ml/
│   ├── FoodClassifier.kt      # API classification
│   └── ValidationModule.kt    # three-layer prediction filtering
├── util/
│   └── NutritionMapper.kt     # portion-scaled nutrition lookup
├── data/
│   ├── model/                 # data classes
│   └── local/                 # Room database
└── ui/                        # scan, results, history, summary screens
```
