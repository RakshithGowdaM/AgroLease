# LocalStorage Session & Booking System

## Overview

This document explains the comprehensive localStorage implementation for managing multiple login sessions and booking data in AgriRent frontend.

## Features

### 1. **Multiple Session Management**
- Support for multiple concurrent login sessions
- Store up to 5 recent sessions
- Auto-expiration after 30 days
- Quick login switching between accounts
- Device-specific session tracking

### 2. **Persistent Booking Storage**
- Store bookings locally for offline support
- Automatic fallback when backend is unavailable
- Booking status tracking (pending, confirmed, active, completed, cancelled)
- User-specific booking history
- Equipment availability date blocking

### 3. **Hybrid API Architecture**
- Primary: Backend API calls
- Fallback: localStorage when backend fails
- Seamless user experience with graceful degradation

---

## Session Management (`sessionStorage.js`)

### Core Functions

#### Adding a Session
```javascript
import { addSession } from '../utils/sessionStorage'

const newSession = addSession(user, token, 'farmer')
// Creates and stores a session
```

#### Getting Current User
```javascript
import { getCurrentUser, getCurrentToken } from '../utils/sessionStorage'

const user = getCurrentUser()  // null if no active session
const token = getCurrentToken() // null if no active session
```

#### Switching Between Sessions
```javascript
import { switchSession } from '../utils/sessionStorage'

switchSession('farmer@demo.com')  // Returns true if success
```

#### Listing Available Sessions (Quick Login)
```javascript
import { getQuickLoginsList } from '../utils/sessionStorage'

const sessions = getQuickLoginsList()
// Returns: [{ email, name, role, loginTime, avatar }, ...]
```

#### Managing Sessions
```javascript
// Get single session by email
import { getSessionByEmail } from '../utils/sessionStorage'
const session = getSessionByEmail('farmer@demo.com')

// Remove specific session
import { removeSession } from '../utils/sessionStorage'
removeSession(sessionId)

// Logout from all devices
import { clearAllSessions } from '../utils/sessionStorage'
clearAllSessions()

// Update activity timestamp
import { updateSessionActivity } from '../utils/sessionStorage'
updateSessionActivity()
```

### Session Object Structure
```javascript
{
  id: 'session_1712594400000_abc123def456',
  user: {
    id: '507f1f77bcf86cd799439011',
    email: 'farmer@demo.com',
    name: 'Ravi Kumar',
    phone: '+919876543210',
    role: 'farmer',
    avatar: 'https://example.com/avatar.jpg',
    location: { address: 'Pune, Maharashtra', ... }
  },
  token: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...',
  deviceId: 'device_1712594400000_abc123def456',
  loginTime: '2026-04-08T14:37:22.873Z',
  lastActiveTime: '2026-04-08T14:37:22.873Z',
  expiresAt: '2026-05-08T14:37:22.873Z'  // 30 days later
}
```

---

## Booking Storage (`bookingStorage.js`)

### Core Functions

#### Creating a Booking
```javascript
import { createLocalBooking } from '../utils/bookingStorage'

const booking = createLocalBooking({
  equipmentId: 'eq-001',
  equipmentName: 'John Deere Tractor',
  userId: 'user-123',
  userEmail: 'farmer@demo.com',
  userName: 'Ravi Kumar',
  userPhone: '+919876543210',
  startDate: '2026-04-10',
  endDate: '2026-04-12',
  duration: 2,
  deliveryAddress: 'Farm Road, Nashik',
  deliveryLat: 20.0059,
  deliveryLng: 73.7898,
  pricePerDay: 2500,
  pricePerHour: 350,
  totalAmount: 5000,
  status: 'pending',
  paymentStatus: 'pending'
})
// Returns booking object with auto-generated ID like "AR-0000001-ABC123"
```

#### Retrieving Bookings
```javascript
import { getUserBookings, getBooking, getEquipmentBookings } from '../utils/bookingStorage'

// Get all bookings for a user
const myBookings = getUserBookings('farmer@demo.com')

// Get specific booking
const booking = getBooking('AR-0000001-ABC123')

// Get all bookings for an equipment
const equipmentBookings = getEquipmentBookings('eq-001')
```

#### Updating Booking Status
```javascript
import { updateBookingStatus, cancelBooking } from '../utils/bookingStorage'

// Update status (pending → confirmed → active → completed)
updateBookingStatus('AR-0000001-ABC123', 'confirmed', 'paid')

// Cancel booking
cancelBooking('AR-0000001-ABC123', 'Customer request')
```

#### Availability Check
```javascript
import { isEquipmentAvailable, getBookedDatesForEquipment } from '../utils/bookingStorage'

// Check if equipment available for dates
const available = isEquipmentAvailable('eq-001', '2026-04-10', '2026-04-12')

// Get booked dates
const bookedDates = getBookedDatesForEquipment('eq-001')
// Returns: [{ start: '2026-04-10', end: '2026-04-12', bookingId: 'AR-...' }, ...]
```

#### Analytics
```javascript
import { getUserBookingStats, getTrendingEquipment } from '../utils/bookingStorage'

// Booking stats for user
const stats = getUserBookingStats('farmer@demo.com')
// Returns: {
//   total: 5,
//   pending: 1,
//   confirmed: 2,
//   active: 1,
//   completed: 1,
//   cancelled: 0,
//   totalSpent: 15000
// }

// Most booked equipment
const trending = getTrendingEquipment(5)
```

### Booking Object Structure
```javascript
{
  id: 'AR-0000001-ABC123',
  equipmentId: 'eq-001',
  equipmentName: 'John Deere Tractor',
  userId: 'user-123',
  userEmail: 'farmer@demo.com',
  userName: 'Ravi Kumar',
  userPhone: '+919876543210',
  startDate: '2026-04-10',
  endDate: '2026-04-12',
  duration: 2,
  deliveryAddress: 'Farm Road, Nashik',
  deliveryLat: 20.0059,
  deliveryLng: 73.7898,
  pickupAddress: 'Farm Road, Nashik',
  pricePerDay: 2500,
  pricePerHour: 350,
  securityDeposit: 5000,
  totalAmount: 5000,
  status: 'pending',  // pending, confirmed, active, completed, cancelled
  paymentStatus: 'pending',  // pending, paid, failed
  operatorRequired: false,
  fuelIncluded: true,
  notes: 'Need early morning delivery',
  createdAt: '2026-04-08T14:37:22.873Z',
  updatedAt: '2026-04-08T14:37:22.873Z',
  confirmedAt: null,
  completedAt: null
}
```

---

## AuthContext Usage

### Updated Context Values
```javascript
const {
  user,                      // Current logged-in user
  loading,                   // Loading state during session restore
  login,                     // Login function
  register,                  // Register function
  logout,                    // Logout function
  updateProfile,             // Update user profile
  allSessions,              // Array of all stored sessions
  switchSession,            // Switch to different logged-in session
  getCurrentToken           // Get API token for current session
} = useAuth()
```

### Example: Login with Session Storage
```javascript
import { useAuth } from '../context/AuthContext'

function LoginForm() {
  const { login, allSessions, switchSession } = useAuth()
  
  const handleLogin = async (email, password) => {
    try {
      await login(email, password)
      // Session automatically stored in localStorage
    } catch (err) {
      alert('Login failed: ' + err.message)
    }
  }
  
  const handleQuickLogin = (email) => {
    switchSession(email)
  }
  
  return (
    <div>
      <h2>Quick Login</h2>
      {allSessions.map(session => (
        <button key={session.user.email} onClick={() => handleQuickLogin(session.user.email)}>
          {session.user.name}
        </button>
      ))}
    </div>
  )
}
```

---

## Booking Service with Fallback

### Automatic Fallback to localStorage
```javascript
import { bookingService } from '../services/bookingService'

// When backend is unavailable, automatically uses localStorage
const booking = await bookingService.create({
  equipmentId: 'eq-001',
  startDate: '2026-04-10',
  endDate: '2026-04-12',
  // ... other fields
})
// Returns booking object (from backend or localStorage)

// Get user bookings with automatic fallback
const myBookings = await bookingService.getMyBookings()
// Tries backend first, falls back to localStorage if needed
```

---

## Testing with Storage Debug Page

### Access Debug Page
Add route to your router:
```javascript
import StorageDebugPage from '../pages/StorageDebugPage'

// In your AppRoutes
<Route path="/storage-debug" element={<StorageDebugPage />} />
```

Visit: `http://localhost:5175/storage-debug`

### Debug Features
- Create multiple test sessions
- Switch between sessions
- Create sample bookings
- View booking statistics
- Clear all data
- Monitor storage usage

---

## Data Persistence & Cleanup

### Storage Keys
```javascript
'agrirent_sessions'              // All stored sessions
'agrirent_current_session'       // Currently active session ID
'agrirent_device_id'             // Device identifier
'agrirent_bookings'              // All stored bookings
'agrirent_booking_counter'       // Booking ID counter
'agrirent_token'                 // Legacy token (deprecated)
'agrirent_user'                  // Legacy user (deprecated)
```

### Auto-Cleanup
- Sessions expire after 30 days
- Only 5 most recent sessions kept
- Expired sessions removed on next read

### Manual Cleanup
```javascript
import { clearAllSessions } from '../utils/sessionStorage'
import { clearAllBookings } from '../utils/bookingStorage'

// Clear specific data
clearAllSessions()      // Logout from all devices
clearAllBookings()      // Delete all bookings

// Export data for backup
import { exportBookingsAsJSON } from '../utils/bookingStorage'
const backup = exportBookingsAsJSON()
console.log(JSON.stringify(backup))
```

---

## Error Handling

### Try-Catch Pattern
```javascript
try {
  const bookings = await bookingService.getMyBookings()
} catch (err) {
  console.error('Failed to fetch bookings:', err.message)
  // Already fallen back to localStorage automatically
}
```

### Console Logging
```javascript
// Storage system logs actions
console.log('Booking created:', booking.id)
console.log(`Restored session for ${user.email}`)
console.log('Failed to fetch equipment:', err.message)
// Check browser console for debugging
```

---

## Best Practices

### ✅ DO
- Check `getCurrentUser()` before creating bookings
- Use `switchSession()` for multi-account support
- Leverage automatic API fallback
- Cache user sessions for quick access
- Monitor booking status regularly

### ❌ DON'T
- Manually edit localStorage keys
- Store sensitive data beyond tokens
- Assume backend is always available
- Clear sessions without user confirmation
- Store passwords or card details

---

## Migration from Old System

Old localStorage format is automatically detected and migrated:
```javascript
// Old format
localStorage.setItem('agrirent_token', 'token...')
localStorage.setItem('agrirent_user', JSON.stringify({...}))

// Automatically detected and migrated to new format on load
// New sessions structure used going forward
```

---

## Browser Support

- Chrome/Edge: ✅ Full support
- Firefox: ✅ Full support
- Safari: ✅ Full support
- Mobile browsers: ✅ Full support

**Storage Limit**: 5-10MB per domain (localStorage)

---

## Troubleshooting

### Session Lost After Refresh
- Check if session has expired (30-day limit)
- Verify device ID is consistent
- Check browser localStorage quota

### Bookings Not Showing
- Ensure user email matches
- Check booking status isn't deleted
- Verify localStorage hasn't been cleared

### API Not Fallback to localStorage
- Check console for specific error
- Verify getCurrentUser() returns user object
- Ensure booking data structure is correct

### Storage Quota Exceeded
- Clear old sessions: `clearAllSessions()`
- Clear completed bookings manually
- Export data before clearing

---

## Future Enhancements

- [ ] IndexedDB support for larger data
- [ ] Service Worker sync for offline bookings
- [ ] Encryption for sensitive data
- [ ] Automatic sync on reconnection
- [ ] Cross-tab session sync
- [ ] Firebase Realtime Sync
