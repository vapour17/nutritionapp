# Fitness Tracker App

A modern, mobile-first fitness tracking application built with Flutter, following MVVM architecture and clean code principles. This app integrates with Supabase for backend services.

## 🎯 Features

- **Dashboard**: Track daily progress with visual rings for calories, steps, and active minutes
- **Workout Logging**: Create, track, and manage workouts with detailed exercise tracking
- **Nutrition Tracking**: Log meals, monitor calories and macros
- **Hydration Tracking**: Track daily water intake
- **Community**: Social features and challenges (placeholder)
- **Profile Management**: User settings and preferences (placeholder)
- **Onboarding**: Smooth onboarding experience for new users

## 🏗️ Architecture

This project follows **Clean Architecture** principles with **MVVM** pattern:

### Folder Structure

```
lib/
├── core/                          # Shared/Reusable code
│   ├── constants/                 # App-wide constants
│   │   └── app_constants.dart
│   ├── navigation/                # Routing configuration
│   │   ├── app_router.dart
│   │   └── app_shell.dart
│   ├── services/                  # Core services
│   │   └── supabase_service.dart
│   ├── theme/                     # Design tokens & themes
│   │   ├── app_colors.dart
│   │   ├── app_text_styles.dart
│   │   └── app_theme.dart
│   └── widgets/                   # Reusable UI components
│       ├── buttons.dart
│       ├── cards.dart
│       ├── common_widgets.dart
│       ├── input_fields.dart
│       └── progress_widgets.dart
│
├── features/                      # Feature modules
│   ├── dashboard/
│   │   ├── api/                  # Supabase API calls
│   │   │   └── dashboard_api.dart
│   │   ├── model/                # Data models
│   │   │   └── dashboard_models.dart
│   │   ├── usecase/              # Business logic
│   │   │   └── dashboard_usecase.dart
│   │   └── ui/                   # UI layer
│   │       ├── screens/
│   │       │   └── dashboard_screen.dart
│   │       └── widgets/
│   │           ├── challenge_card.dart
│   │           ├── hydration_widget.dart
│   │           ├── nutrition_card.dart
│   │           └── workout_summary_card.dart
│   │
│   ├── workout/                   # Similar structure
│   ├── nutrition/                 # Similar structure
│   ├── community/                 # Similar structure
│   ├── profile/                   # Similar structure
│   └── onboarding/               # Similar structure
│
└── main.dart                      # App entry point
```

### Architecture Layers

1. **UI Layer** (`ui/screens` & `ui/widgets`)
   - Screens and widgets for presentation
   - Consumes data from UseCases via Provider

2. **UseCase Layer** (`usecase/`)
   - Business logic and state management
   - Uses ChangeNotifier for state updates
   - Coordinates between API and UI

3. **API Layer** (`api/`)
   - Handles Supabase interactions
   - Data fetching and persistence

4. **Model Layer** (`model/`)
   - Data models with JSON serialization
   - Represents domain entities

## 🚀 Getting Started

### Prerequisites

- Flutter SDK (>=3.9.2)
- Dart SDK
- Supabase account (for backend services)

### Installation

1. **Clone the repository**
   ```bash
   git clone <your-repo-url>
   cd fitness_app
   ```

2. **Install dependencies**
   ```bash
   flutter pub get
   ```

3. **Configure Supabase**
   
   Update `lib/main.dart` with your Supabase credentials:
   ```dart
   await SupabaseService.instance.initialize(
     url: 'YOUR_SUPABASE_URL',
     anonKey: 'YOUR_SUPABASE_ANON_KEY',
   );
   ```

4. **Run the app**
   ```bash
   flutter run