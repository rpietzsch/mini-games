# Firebase Security Rules for Multiplayer Pong

## Overview
This file contains comprehensive security rules for the multiplayer Pong game that prevent abuse while maintaining functionality.

## Security Features

### 1. **Rate Limiting**
- **Room Creation**: 1 room per user per minute (client-side) + database validation
- **Game Actions**: Max 60 actions per second (16ms intervals)
- **Game State Updates**: Max 60 updates per second from host

### 2. **Data Validation**
- **Player Names**: 1-20 characters, strings only
- **Room Codes**: 6 characters, auto-generated
- **Timestamps**: Must be within 1 second of server time
- **Score Values**: 0-99 range, numbers only
- **Game Actions**: Only `paddle` and `disconnect` allowed

### 3. **Access Control**
- **Room Reading**: Only if created within 24 hours
- **Room Writing**: Only host, or new players joining (max 2 players)
- **Game State**: Only host can update
- **Player Data**: Users can only modify their own data

### 4. **Size Limits**
- **Max 1000 rooms** globally
- **Max 2 players** per room
- **Max 1000 actions** per room
- **Max 10 data fields** per action
- **String lengths**: Names 20 chars, IDs 50 chars

### 5. **Auto-Cleanup**
- **Rooms expire** after 24 hours
- **Rate limit data** expires automatically
- **Actions** are limited to prevent spam

## Rule Structure

```json
{
  "rules": {
    "rooms": {
      "$roomId": {
        // Room-specific rules
      }
    },
    "rate_limit": {
      // Rate limiting storage
    },
    "global_limits": {
      // Global rate limiting
    },
    "cleanup": {
      // Cleanup configuration
    }
  }
}
```

## Key Security Measures

### **Prevents:**
- ✅ Room flooding/spam
- ✅ Excessive database writes
- ✅ Invalid data injection
- ✅ Unauthorized game state changes
- ✅ Player impersonation
- ✅ Bandwidth abuse
- ✅ Long-term data accumulation

### **Allows:**
- ✅ Normal gameplay
- ✅ Real-time synchronization
- ✅ Room creation and joining
- ✅ Paddle movement
- ✅ Score tracking

## Implementation

### 1. **Deploy Rules**
1. Copy `firebase-security-rules.json` content
2. Go to Firebase Console → Realtime Database → Rules
3. Paste the rules and click "Publish"

### 2. **Client-Side Rate Limiting**
The game client includes additional rate limiting:
- Room creation: 1 per minute
- Actions: 16ms intervals
- Rate limit tracking in database

### 3. **Error Handling**
The game handles rate limit errors gracefully:
- Shows user-friendly messages
- Queues actions when rate limited
- Reconnects on connection drops

## Monitoring

### **What to Monitor:**
- Room creation frequency
- Action frequency per user
- Database read/write operations
- Connection count
- Data size growth

### **Firebase Console Metrics:**
- Go to Firebase Console → Usage
- Monitor database operations
- Set up billing alerts
- Check for unusual spikes

## Cost Optimization

### **Free Tier Limits:**
- 1GB storage
- 10GB/month bandwidth
- 100 simultaneous connections

### **Optimization Features:**
- Auto-cleanup prevents data accumulation
- Rate limiting reduces operations
- Size limits prevent large payloads
- Timestamp validation prevents old data

## Troubleshooting

### **Common Issues:**
1. **"Permission denied"** → Check if user is authenticated
2. **"Rate limited"** → Wait 1 minute between room creations
3. **"Room not found"** → Room may have expired (24 hours)
4. **"Too many players"** → Room is full (2 players max)

### **Debug Tips:**
- Check browser console for errors
- Verify Firebase config is correct
- Test with Firebase Rules Playground
- Monitor database operations

This security setup provides robust protection while maintaining smooth gameplay experience.