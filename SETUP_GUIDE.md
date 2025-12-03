# Fitness Tracker - Setup Guide

## 📋 Prerequisites

Before you begin, ensure you have the following installed:
- Flutter SDK (version 3.9.2 or higher)
- Dart SDK
- Android Studio / VS Code with Flutter extensions
- Xcode (for iOS development on Mac)
- A Supabase account

## 🔧 Step-by-Step Setup

### 1. Install Flutter

If you haven't installed Flutter yet:

**Windows:**
```bash
# Download Flutter SDK from flutter.dev
# Extract to C:\src\flutter
# Add to PATH: C:\src\flutter\bin

flutter doctor
```

**macOS/Linux:**
```bash
# Download Flutter SDK
cd ~/development
git clone https://github.com/flutter/flutter.git -b stable
export PATH="$PATH:`pwd`/flutter/bin"

flutter doctor
```

### 2. Clone the Project

```bash
git clone <your-repository-url>
cd fitness_app
```

### 3. Install Dependencies

```bash
flutter pub get
```

### 4. Setup Supabase

#### Create a Supabase Project

1. Go to [supabase.com](https://supabase.com)
2. Create a new account or sign in
3. Create a new project
4. Note your project URL and anon key

#### Create Database Tables

Run these SQL commands in the Supabase SQL Editor:

```sql
-- Profiles table
CREATE TABLE profiles (
  id UUID REFERENCES auth.users PRIMARY KEY,
  email TEXT NOT NULL,
  name TEXT,
  avatar_url TEXT,
  date_of_birth DATE,
  gender TEXT,
  height FLOAT,
  weight FLOAT,
  goals JSONB,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Daily progress table
CREATE TABLE daily_progress (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  date TIMESTAMP NOT NULL,
  calories FLOAT DEFAULT 0,
  calories_goal FLOAT DEFAULT 2000,
  steps INT DEFAULT 0,
  steps_goal INT DEFAULT 10000,
  active_minutes INT DEFAULT 0,
  active_minutes_goal INT DEFAULT 30,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Workouts table
CREATE TABLE workouts (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  type TEXT NOT NULL,
  duration INT NOT NULL,
  calories FLOAT DEFAULT 0,
  date TIMESTAMP NOT NULL,
  status TEXT DEFAULT 'completed',
  exercises JSONB,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Meals table
CREATE TABLE meals (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  meal_type TEXT NOT NULL,
  calories FLOAT DEFAULT 0,
  protein FLOAT DEFAULT 0,
  carbs FLOAT DEFAULT 0,
  fat FLOAT DEFAULT 0,
  date TIMESTAMP NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Daily nutrition table
CREATE TABLE daily_nutrition (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  date TIMESTAMP NOT NULL,
  total_calories FLOAT DEFAULT 0,
  calories_goal FLOAT DEFAULT 2000,
  protein FLOAT DEFAULT 0,
  carbs FLOAT DEFAULT 0,
  fat FLOAT DEFAULT 0,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Daily hydration table
CREATE TABLE daily_hydration (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  date TIMESTAMP NOT NULL,
  glasses INT DEFAULT 0,
  glasses_goal INT DEFAULT 8,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Challenges table
CREATE TABLE challenges (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  title TEXT NOT NULL,
  description TEXT,
  status TEXT DEFAULT 'active',
  total_participants INT DEFAULT 0,
  top_participants JSONB,
  start_date TIMESTAMP,
  end_date TIMESTAMP,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Community posts table
CREATE TABLE community_posts (
  id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
  user_name TEXT,
  user_avatar TEXT,
  title TEXT NOT NULL,
  content TEXT,
  image_url TEXT,
  likes INT DEFAULT 0,
  comments INT DEFAULT 0,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Create indexes for better performance
CREATE INDEX idx_daily_progress_user_date ON daily_progress(user_id, date);
CREATE INDEX idx_workouts_user_date ON workouts(user_id, date);
CREATE INDEX idx_meals_user_date ON meals(user_id, date);
CREATE INDEX idx_nutrition_user_date ON daily_nutrition(user_id, date);
CREATE INDEX idx_hydration_user_date ON daily_hydration(user_id, date);
CREATE INDEX idx_posts_created ON community_posts(created_at);
```

#### Enable Row Level Security (RLS)

```sql
-- Enable RLS on all tables
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE daily_progress ENABLE ROW LEVEL SECURITY;
ALTER TABLE workouts ENABLE ROW LEVEL SECURITY;
ALTER TABLE meals ENABLE ROW LEVEL SECURITY;
ALTER TABLE daily_nutrition ENABLE ROW LEVEL SECURITY;
ALTER TABLE daily_hydration ENABLE ROW LEVEL SECURITY;
ALTER TABLE challenges ENABLE ROW LEVEL SECURITY;
ALTER TABLE community_posts ENABLE ROW LEVEL SECURITY;

-- Create policies (users can only access their own data)
CREATE POLICY "Users can view own profile" ON profiles FOR SELECT USING (auth.uid() = id);
CREATE POLICY "Users can update own profile" ON profiles FOR UPDATE USING (auth.uid() = id);

CREATE POLICY "Users can view own progress" ON daily_progress FOR ALL USING (auth.uid() = user_id);
CREATE POLICY "Users can view own workouts" ON workouts FOR ALL USING (auth.uid() = user_id);
CREATE POLICY "Users can view own meals" ON meals FOR ALL USING (auth.uid() = user_id);
CREATE POLICY "Users can view own nutrition" ON daily_nutrition FOR ALL USING (auth.uid() = user_id);
CREATE POLICY "Users can view own hydration" ON daily_hydration FOR ALL USING (auth.uid() = user_id);

CREATE POLICY "Anyone can view challenges" ON challenges FOR SELECT USING (true);
CREATE POLICY "Anyone can view community posts" ON community_posts FOR SELECT USING (true);
CREATE POLICY "Users can create posts" ON community_posts FOR INSERT WITH CHECK (auth.uid() = user_id);
```

### 5. Configure App Credentials

Update `lib/main.dart` with your Supabase credentials:

```dart
await SupabaseService.instance.initialize(
  url: 'https://your-project.supabase.co',
  anonKey: 'your-anon-key-here',
);
```

### 6. Create Asset Folders

```bash
mkdir -p assets/images
mkdir -p assets/icons
mkdir -p assets/animations
```

### 7. Run the App

```bash
# Check for issues
flutter doctor

# Run on connected device
flutter run

# Run on specific device
flutter devices
flutter run -d <device-id>
```

## 🧪 Testing the Setup

1. The app should launch and show the Dashboard screen
2. Bottom navigation should work (switching between screens)
3. Check console for any Supabase connection errors

## 🐛 Troubleshooting

### Common Issues:

**"Package not found" errors:**
```bash
flutter pub get
flutter clean
flutter pub get
```

**Supabase connection errors:**
- Verify your URL and anon key are correct
- Check your internet connection
- Ensure Supabase project is active

**Build errors:**
```bash
flutter clean
flutter pub get
flutter run
```

**iOS-specific issues:**
```bash
cd ios
pod install
cd ..
flutter run
```

## 📱 Running on Physical Devices

### Android:
1. Enable Developer Mode on your Android device
2. Enable USB Debugging
3. Connect via USB
4. Run `flutter run`

### iOS:
1. Open project in Xcode
2. Sign with your Apple ID
3. Trust certificate on device
4. Run from Xcode or `flutter run`

## 🚀 Next Steps

After setup:
1. Review the code structure in `lib/`
2. Explore the Dashboard feature
3. Start implementing remaining features
4. Test with real Supabase data
5. Customize the design as needed

## 📚 Resources

- [Flutter Documentation](https://docs.flutter.dev)
- [Supabase Documentation](https://supabase.com/docs)
- [Provider Package](https://pub.dev/packages/provider)
- [GoRouter Package](https://pub.dev/packages/go_router)

## 💡 Tips

- Use hot reload (press `r` in terminal) for quick UI changes
- Use hot restart (press `R`) for logic changes
- Check `flutter doctor` regularly
- Keep dependencies updated with `flutter pub upgrade`

## 🆘 Getting Help

- Check Flutter logs in terminal
- Use `flutter doctor -v` for detailed diagnostics
- Check Supabase logs in dashboard
- Review error messages carefully

Good luck with your Fitness Tracker app! 🎉
