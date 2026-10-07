# Quick Reference - LocalStorage Usage Examples

## 1. Login with Multiple Accounts

```javascript
import { useAuth } from '@/context/AuthContext'

function MyAuthComponent() {
  const { login, allSessions, switchSession, logout, user } = useAuth()

  // Login new user
  const handleLogin = async () => {
    await login('farmer@demo.com', 'demo123')
    // Session is automatically stored
  }

  // Quick login - switch to saved session
  const handleQuickSwitch = (email) => {
    switchSession(email)
  }

  // Logout current session
  const handleLogout = () => {
    logout()
  }

  return (
    <div>
      <h1>Current: {user?.email}</h1>
      
      <button onClick={handleLogin}>Login New Account</button>
      
      <h2>Saved Sessions ({allSessions.length})</h2>
      {allSessions.map(session => (
        <div key={session.id}>
          <span>{session.user.email} ({session.user.role})</span>
          <button onClick={() => handleQuickSwitch(session.user.email)}>
            Switch
          </button>
        </div>
      ))}
      
      <button onClick={handleLogout}>Logout</button>
    </div>
  )
}
```

---

## 2. Create & Manage Bookings

```javascript
import { useAuth } from '@/context/AuthContext'
import { bookingService } from '@/services/bookingService'

function BookingComponent() {
  const { user } = useAuth()

  // Create booking (auto-fallback to localStorage)
  const handleCreateBooking = async () => {
    try {
      const booking = await bookingService.create({
        equipmentId: 'eq-001',
        equipmentName: 'John Deere Tractor',
        startDate: '2026-04-10',
        endDate: '2026-04-12',
        deliveryAddress: 'Farm Road, Nashik',
        deliveryLat: 20.0059,
        deliveryLng: 73.7898,
        pricePerDay: 2500,
        totalAmount: 5000,
      })
      console.log('Booking created:', booking.id)
    } catch (err) {
      console.error('Booking failed:', err)
    }
  }

  // Get user's bookings
  const handleGetBookings = async () => {
    const bookings = await bookingService.getMyBookings()
    console.log('Your bookings:', bookings)
  }

  return (
    <div>
      <button onClick={handleCreateBooking}>Book Equipment</button>
      <button onClick={handleGetBookings}>View My Bookings</button>
    </div>
  )
}
```

---

## 3. Direct Storage Access

```javascript
// Session management
import {
  getCurrentUser,
  getCurrentToken,
  getAllSessions,
  switchSession,
  removeSession,
  clearAllSessions,
} from '@/utils/sessionStorage'

// Get current logged-in user
const user = getCurrentUser()
console.log(`Logged in as: ${user?.email}`)

// Get API token for requests
const token = getCurrentToken()
console.log(`Token: ${token?.substring(0, 20)}...`)

// List all available sessions
const sessions = getAllSessions()
console.log(`${sessions.length} sessions stored`)

// Switch account without logout
switchSession('owner@demo.com')

// Remove specific session
removeSession(sessionId)

// Logout from all devices
clearAllSessions()

---

// Booking management
import {
  getAllBookings,
  getUserBookings,
  getBooking,
  updateBookingStatus,
  cancelBooking,
  isEquipmentAvailable,
  getUserBookingStats,
} from '@/utils/bookingStorage'

// Get all bookings
const allBookings = getAllBookings()

// Get user's bookings
const myBookings = getUserBookings('farmer@demo.com')

// Get single booking
const booking = getBooking('AR-0000001-ABC123')

// Update status
updateBookingStatus('AR-0000001-ABC123', 'confirmed', 'paid')

// Cancel booking
cancelBooking('AR-0000001-ABC123', 'Customer requested')

// Check availability
const available = isEquipmentAvailable('eq-001', '2026-04-10', '2026-04-12')

// Get stats
const stats = getUserBookingStats('farmer@demo.com')
// { total: 5, pending: 1, confirmed: 2, completed: 1, totalSpent: 15000 }
```

---

## 4. Testing & Debugging

```javascript
// In browser console:

// Session tests
getCurrentUser()  // Should show current user
getAllSessions()  // Should show [{ id, user, token, ... }]
switchSession('test@demo.com')  // Switch account

// Booking tests
getAllBookings()  // Should show all bookings
getUserBookings('test@demo.com')  // Show user's bookings
getUserBookingStats('test@demo.com')  // Show stats

// Clear everything
clearAllSessions()  // Delete all sessions
clearAllBookings()  // Delete all bookings
localStorage.clear()  // Nuclear option
```

---

## 5. Error Handling

```javascript
import { bookingService } from '@/services/bookingService'

async function safeBooking(bookingData) {
  try {
    const booking = await bookingService.create(bookingData)
    console.log('✅ Booking successful:', booking.id)
    return booking
  } catch (err) {
    console.warn('⚠️ Backend failed, using localStorage:', err.message)
    // Automatic fallback already applied
    // Can retry or show user a message
  }
}

async function getBookingsWithFallback() {
  try {
    const bookings = await bookingService.getMyBookings()
    console.log('✅ Fetched from backend:', bookings.length)
    return bookings
  } catch (err) {
    console.warn('⚠️ Using cached bookings:', err.message)
    // Automatic fallback to localStorage
    return bookings  // Returns localStorage data
  }
}
```

---

## 6. Multi-Device Workflow

```javascript
// Device 1: Farmer logs in
await login('farmer@demo.com', 'demo123')
// Session created: AR-farm-device1

// Device 2: Same farmer logs in
await login('farmer@demo.com', 'demo123')
// New session created: AR-farm-device2
// Both sessions valid simultaneously

// Device 1: Switch to different account
switchSession('owner@demo.com')
// Now on device 1 with owner account

// Device 1: Logout
logout()
// Removes current session, other sessions remain active
```

---

## 7. Real-World Scenarios

### Scenario A: Offline Booking
```javascript
// User is offline (no backend)
// Booking service automatically uses localStorage
const booking = await bookingService.create({})
// Booking saved locally with ID "AR-0000001-XYZ123"

// User comes online
// On next activity, system can sync with backend
```

### Scenario B: Multi-Account Quick Switch
```javascript
// User managing multiple farms
const { allSessions, switchSession } = useAuth()

// Show dropdown with saved accounts
{allSessions.map(s => (
  <option onClick={() => switchSession(s.user.email)}>
    {s.user.name} - {s.user.role}
  </option>
))}

// One-click account switch without re-login
```

### Scenario C: Booking History
```javascript
// Get all historical bookings
const { user } = useAuth()
const bookings = await bookingService.getMyBookings()
const stats = getUserBookingStats(user.email)

// Show dashboard
// Cards showing: pending, confirmed, completed, cancelled
// Total spent, equipment used, ratings given
```

---

## 8. Storage Monitoring

```javascript
// Check localStorage size
function getStorageSize() {
  let total = 0
  for (let key in localStorage) {
    if (localStorage.hasOwnProperty(key)) {
      total += localStorage[key].length + key.length
    }
  }
  return (total / 1024).toFixed(2) + ' KB'
}

console.log('Storage used:', getStorageSize())

// Clean up old sessions
import { getAllSessions, removeSession } from '@/utils/sessionStorage'
const sessions = getAllSessions()
const thirtyDaysAgo = new Date(Date.now() - 30*24*60*60*1000)

sessions.forEach(s => {
  if (new Date(s.loginTime) < thirtyDaysAgo) {
    removeSession(s.id)
  }
})
```

---

## 9. Backup & Export

```javascript
import { exportBookingsAsJSON } from '@/utils/bookingStorage'

// Backup bookings
function downloadBackup() {
  const backup = exportBookingsAsJSON()
  const json = JSON.stringify(backup, null, 2)
  
  const blob = new Blob([json], { type: 'application/json' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = `agrirent-backup-${new Date().toISOString()}.json`
  a.click()
}

// Restore from backup
function restoreFromFile(file) {
  const reader = new FileReader()
  reader.onload = (e) => {
    const backup = JSON.parse(e.target.result)
    // Manually restore as needed
    console.log('Backup contains:', backup.count, 'bookings')
  }
  reader.readAsText(file)
}
```

---

## 10. API Integration Notes

- **Sessions**: Automatically attached to every API request via `Authorization` header
- **Tokens**: Fetched from current session's token
- **Fallback**: Triggered on network error or 500+ response codes
- **Sync**: Manual only - no auto-sync on reconnect yet
- **Conflict**: Backend data takes precedence if both exist

Visit `/storage-debug` to test and visualize all features!
