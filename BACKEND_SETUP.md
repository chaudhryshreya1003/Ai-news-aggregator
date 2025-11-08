# AI News Aggregator - Backend Setup Guide

This guide will help you set up the backend infrastructure for your AI News Aggregator using Supabase.

## 📋 Overview

The backend includes:
- **User Authentication** - Secure signup/login with Supabase Auth
- **PostgreSQL Database** - All data stored in Supabase
- **Bookmarks** - Save articles for later reading
- **User Preferences** - Dark mode, favorite categories, notifications
- **News Submissions** - User-submitted news and problem reports
- **Reading History** - Track what users have read

## 🚀 Quick Start

### Option 1: Using Supabase (Recommended for Production)

#### Step 1: Create a Supabase Project

1. Go to [https://supabase.com](https://supabase.com)
2. Click "Start your project" and sign up
3. Create a new project:
   - Choose a project name (e.g., "ai-news-aggregator")
   - Set a database password (save this!)
   - Select a region close to your users
   - Wait for the project to be created (~2 minutes)

#### Step 2: Set Up Database Tables

1. In your Supabase project dashboard, go to the **SQL Editor**
2. Click "New Query"
3. Copy the entire contents of `/database/schema.sql`
4. Paste into the SQL editor
5. Click "Run" to execute the SQL

This will create all necessary tables, indexes, and security policies.

#### Step 3: Get Your API Keys

1. In Supabase dashboard, go to **Settings** → **API**
2. Copy your:
   - **Project URL** (looks like: `https://xxxxx.supabase.co`)
   - **Anon/Public Key** (starts with `eyJ...`)

#### Step 4: Configure Your Application

Create a `.env` file in your project root:

```env
VITE_SUPABASE_URL=your_project_url_here
VITE_SUPABASE_ANON_KEY=your_anon_key_here
```

Replace `your_project_url_here` and `your_anon_key_here` with your actual values.

#### Step 5: Enable Email Authentication

1. In Supabase dashboard, go to **Authentication** → **Providers**
2. Make sure **Email** provider is enabled
3. Configure email templates if desired

### Option 2: Development Mode (No Setup Required)

The application includes **mock services** that work without any backend setup!

- All data is stored in `localStorage`
- Perfect for development and testing
- No Supabase account needed
- Just start coding!

When Supabase is not configured, the app automatically uses mock services.

## 📁 Project Structure

```
/lib
  /api
    auth.service.ts          # Authentication logic
    news.service.ts          # News fetching and management
    bookmark.service.ts      # Bookmark operations
    submission.service.ts    # News submissions
    preferences.service.ts   # User preferences
  database.types.ts          # TypeScript types for database
  supabase-client.ts         # Supabase client configuration

/hooks
  useAuth.ts                 # Authentication hook
  useBookmarks.ts            # Bookmarks management hook

/database
  schema.sql                 # Database schema and policies
```

## 🔐 Security Features

The backend includes comprehensive security:

- **Row Level Security (RLS)** - Users can only access their own data
- **Authentication** - Secure password hashing via Supabase Auth
- **Input Validation** - All inputs are validated before database insertion
- **SQL Injection Protection** - Parameterized queries via Supabase client

## 📊 Database Schema

### Tables

1. **users** - User profiles
   - id, email, username, avatar_url, created_at, updated_at

2. **news_articles** - All news articles
   - id, title, summary, source, category, image_url, published_at, sentiment, credibility_score, is_fake_news, is_trending

3. **bookmarks** - User bookmarks
   - id, user_id, article_id, created_at

4. **user_preferences** - User settings
   - id, user_id, dark_mode, favorite_categories, notification_enabled

5. **news_submissions** - User submissions
   - id, user_id, title, description, category, source, type, status

6. **reading_history** - Reading analytics
   - id, user_id, article_id, read_at, read_duration

## 🔌 API Services

### Authentication Service (`authService`)

```typescript
// Sign up new user
await authService.signup({ email, password, username });

// Login
await authService.login({ email, password });

// Logout
await authService.logout();

// Get current user
await authService.getCurrentUser();
```

### News Service (`newsService`)

```typescript
// Get all news
await newsService.getNews();

// Get news with filters
await newsService.getNews({ 
  category: 'Technology',
  search: 'AI',
  trendingOnly: true 
});

// Get single article
await newsService.getNewsById(articleId);
```

### Bookmark Service (`bookmarkService`)

```typescript
// Get user bookmarks
await bookmarkService.getUserBookmarks(userId);

// Toggle bookmark
await bookmarkService.toggleBookmark(userId, articleId);

// Check if bookmarked
await bookmarkService.isBookmarked(userId, articleId);
```

### Submission Service (`submissionService`)

```typescript
// Submit news or problem
await submissionService.submitNews({
  title,
  description,
  category,
  source,
  type: 'news', // or 'problem'
  userId
});

// Get user submissions
await submissionService.getUserSubmissions(userId);
```

### Preferences Service (`preferencesService`)

```typescript
// Get preferences
await preferencesService.getUserPreferences(userId);

// Update preferences
await preferencesService.updateUserPreferences(userId, {
  dark_mode: true,
  favorite_categories: ['Technology', 'Science']
});

// Toggle dark mode
await preferencesService.toggleDarkMode(userId);
```

## 🎣 React Hooks

### useAuth Hook

```typescript
const { user, isAuthenticated, login, signup, logout } = useAuth();

// Login
await login(email, password);

// Signup
await signup(email, password, username);

// Logout
await logout();
```

### useBookmarks Hook

```typescript
const { bookmarkedArticles, toggleBookmark, isBookmarked } = useBookmarks(userId);

// Toggle bookmark
await toggleBookmark(articleId);

// Check if article is bookmarked
const bookmarked = isBookmarked(articleId);
```

## 🔄 Migration from Mock to Supabase

Your app will automatically detect when Supabase is configured:

1. **Development**: Start with mock services (no setup needed)
2. **Add Supabase**: Add credentials to `.env`
3. **Automatic Switch**: App detects Supabase and uses real database
4. **No Code Changes**: All services work the same way

## 🐛 Troubleshooting

### Common Issues

**"Failed to connect to Supabase"**
- Check your `.env` file has correct values
- Ensure Supabase project is active
- Verify network connection

**"RLS Policy Error"**
- Make sure you ran the `schema.sql` file
- Check that RLS policies are enabled
- Verify user is authenticated

**"Email already exists"**
- This user already has an account
- Try logging in instead of signing up

## 📚 Additional Resources

- [Supabase Documentation](https://supabase.com/docs)
- [Supabase Auth Guide](https://supabase.com/docs/guides/auth)
- [Row Level Security](https://supabase.com/docs/guides/auth/row-level-security)

## 🎯 Next Steps

After setup, you can:

1. **Customize Database Schema** - Add more fields to tables
2. **Add Real News API** - Integrate NewsAPI, Google News, etc.
3. **Implement AI Features** - Add sentiment analysis, summarization
4. **Add Email Notifications** - Use Supabase Edge Functions
5. **Deploy to Production** - Host on Vercel, Netlify, etc.

## 💡 Pro Tips

- Use Supabase's real-time subscriptions for live updates
- Implement caching to reduce database queries
- Add database indexes for better performance
- Use Supabase Storage for user avatars
- Set up automated backups in Supabase dashboard

---

**Need Help?** Check the Supabase Discord or open an issue in the repository.
