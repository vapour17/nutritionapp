# Fitness App - Simple User Stats Tracker

A simple Flutter app that allows users to enter their name and steps count, storing the data in a Supabase database.

## Setup Instructions

### 1. Supabase Setup

1. Go to [Supabase](https://supabase.com) and create a new project
2. Once your project is created, go to the SQL Editor
3. Run the following SQL to create the required table:

```sql
CREATE TABLE user_stats_eg (
  id BIGSERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  steps INTEGER NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT TIMEZONE('utc'::text, NOW()) NOT NULL
);
```

4. Go to Project Settings > API to get your:
   - Project URL
   - Anon public key

### 2. Flutter App Setup

1. Open `lib/main.dart`
2. Replace the placeholder values with your actual Supabase credentials:
   ```dart
   await Supabase.initialize(
     url: 'YOUR_ACTUAL_SUPABASE_URL', // Replace this
     anonKey: 'YOUR_ACTUAL_SUPABASE_ANON_KEY', // Replace this
   );
   ```

### 3. Run the App

1. Make sure you have Flutter installed
2. Run `flutter pub get` to install dependencies
3. Run `flutter run` to start the app

## Features

- Simple form with two fields: Name and Steps
- Input validation
- Data storage to Supabase
- Success/error messages
- Loading states

## Database Schema

The app uses a table called `user_stats_eg` with the following columns:
- `id` (BIGSERIAL, Primary Key) - Auto-generated unique identifier
- `name` (TEXT) - User's name
- `steps` (INTEGER) - Number of steps
- `created_at` (TIMESTAMP) - Auto-generated timestamp

## Usage

1. Enter your name in the first field
2. Enter the number of steps in the second field
3. Click "Submit" to save the data to Supabase
4. You'll see a success message when the data is saved
5. The form will be cleared for the next entry