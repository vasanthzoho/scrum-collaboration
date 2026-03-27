# Scrum Collaboration Board

A real-time collaborative discussion board where team members can drag-and-drop answer cards onto questions to vote. Changes sync instantly across all connected users.

## Features

- **Real-time Collaboration**: Multiple users can participate simultaneously
- **Drag & Drop Voting**: Intuitive answer card dragging
- **Live Vote Counting**: See votes update in real-time
- **Dynamic Questions**: Add/remove questions on the fly
- **Custom Answers**: Add your own answer options

## Setup Instructions

### 1. Firebase Configuration

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Create a new project (or use existing)
3. Enable **Realtime Database**:
   - Go to "Database" in the left sidebar
   - Click "Create database"
   - Choose "Start in test mode" (you can change security rules later)
4. Get your Firebase config:
   - Go to Project Settings (gear icon)
   - Scroll to "Your apps" section
   - Click "Add app" → Web app (</>)
   - Copy the config object

### 2. Configure the App

1. Open `index.html`
2. Replace the `firebaseConfig` object with your Firebase config:

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
    databaseURL: "https://YOUR_PROJECT_ID-default-rtdb.firebaseio.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT_ID.appspot.com",
    messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

> Important: do not commit real Firebase config values in a public repo. Use environment variables or secret management for deployment and rotate keys immediately when they are exposed.


### 3. Deploy to GitHub Pages

1. Create a new GitHub repository
2. Push your code to the repository
3. Go to repository Settings → Pages
4. Set source to "Deploy from a branch"
5. Select "main" branch and "/ (root)" folder
6. Save and wait for deployment

### 4. Share with Team

Share the GitHub Pages URL with your team. Everyone can now collaborate in real-time!

## Security Configuration

### Firebase Security Rules

**CRITICAL**: After setting up Firebase, you MUST configure security rules to prevent unauthorized access.

1. Go to Firebase Console → Realtime Database → Rules
2. Replace the default rules with the contents of `firebase-rules.json`:

```json
{
  "rules": {
    ".read": "auth != null",
    ".write": "auth != null",
    "rooms": {
      "$roomId": {
        ".read": true,
        ".write": "auth != null",
        "answers": {
          ".read": true,
          ".write": "auth != null"
        },
        "questions": {
          ".read": true,
          ".write": "auth != null"
        },
        "votes": {
          ".read": true,
          ".write": "auth != null",
          "$questionId": {
            ".read": true,
            ".write": "auth != null",
            "$answer": {
              ".read": true,
              ".write": "auth != null && newData.val() == (data.val() || 0) + 1",
              ".validate": "newData.isNumber() && newData.val() >= 0 && newData.val() <= 1000"
            }
          }
        }
      }
    }
  }
}
```

### Security Features

- **Anonymous Authentication**: Users are authenticated anonymously to prevent anonymous writes
- **Vote Validation**: Votes can only be incremented by 1, preventing vote manipulation
- **Data Validation**: Vote counts are limited to reasonable ranges (0-1000)
- **Read Access**: Anyone can read data (necessary for collaboration)
- **Write Protection**: Only authenticated users can modify data

### Additional Security Measures

1. **Domain Restriction**: Consider adding domain validation in security rules
2. **Rate Limiting**: Implement rate limiting for vote operations
3. **Data Sanitization**: All user inputs are trimmed and validated
4. **HTTPS Only**: Deploy only on HTTPS (GitHub Pages provides this)
5. **Regular Audits**: Monitor Firebase usage and security events

### Environment Variables

Never commit your Firebase config to version control. Consider using environment variables for production deployments.

### Security Features Implemented

- **Anonymous Authentication**: All users are authenticated before any write operations
- **Input Sanitization**: All user inputs are sanitized to prevent XSS attacks
- **Content Security Policy**: Strict CSP headers prevent unauthorized script execution
- **Vote Validation**: Firebase rules ensure votes can only be incremented by 1
- **Data Validation**: Input length limits and type validation
- **Error Monitoring**: Client-side error logging for security monitoring
- **HTTPS Enforcement**: Deployed on HTTPS-only platform (GitHub Pages)
- **No Search Indexing**: robots.txt prevents search engine crawling

### Security Checklist

- [ ] Firebase project created with proper configuration
- [ ] Security rules deployed (see firebase-rules.json)
- [ ] Anonymous authentication enabled in Firebase Console
- [ ] Domain restrictions configured if needed
- [ ] Regular security audits scheduled
- [ ] Monitor Firebase usage for suspicious activity

### Data Protection

- No personal information is collected or stored
- Anonymous authentication provides session-based access
- All data is encrypted in transit (HTTPS)
- Firebase handles data encryption at rest
- Data retention can be configured in Firebase Console

## Local Development

Open `index.html` directly in your browser for local testing (Firebase config required).

## Browser Support

Works in all modern browsers that support:
- ES6 Modules
- Drag & Drop API
- Firebase SDK
