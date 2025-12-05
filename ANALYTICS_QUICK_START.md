# Air Weight Analytics - Quick Start Guide

## 🚀 Quick Start (5 Minutes)

### Backend Setup
```bash
cd backend
npm start
# Server runs on http://localhost:3000
```

### Test API
```bash
# 1. Login (get token)
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"name":"admin","password":"admin123"}'

# 2. Get Analytics (use token from step 1)
curl -X GET http://localhost:3000/api/analytics/air-weight \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

### Expected Response
```json
{
  "success": true,
  "data": {
    "total_warehouse_weight": 5065,
    "weight_dispatched_today": 0,
    "total_weight_dispatched": 603,
    "remaining_weight": 4462,
    "unit": "kg"
  }
}
```

---

## 📱 Flutter App

### Run App
```bash
cd app
flutter pub get
flutter run
```

### Access Analytics
1. Login to app
2. Open side menu
3. Tap "Analytics"
4. View weight cards
5. Pull down to refresh

---

## 📊 What You'll See

### 4 Weight Cards

1. **Total Warehouse Weight** 🏭
   - All AIR shipment weights
   - Navy blue card
   - Shows: 5,065 kg (example)

2. **Dispatched Today** 🚚
   - Weight released today
   - Green card
   - Shows: 0 kg (example)

3. **Total Dispatched** ✅
   - All-time dispatched weight
   - Orange card
   - Shows: 603 kg (example)

4. **Remaining Weight** 📦
   - Current inventory
   - Highlighted card
   - Shows: 4,462 kg (example)

---

## 🔧 Common Tasks

### Upload New AIR Shipment
1. Go to "Add Inventory"
2. Select "AIR"
3. Upload Excel file
4. Weight from Column F auto-captured
5. Analytics updates automatically

### Dispatch Package
1. Go to shipment details
2. Create dispatch
3. Enter weight_kg
4. Submit
5. Analytics recalculates remaining weight

### Check Weight
1. Open Analytics screen
2. View current weights
3. Pull to refresh for latest data

---

## 🎯 Key Features

✅ Real-time weight tracking
✅ Auto-calculation of remaining weight
✅ Today's dispatch tracking
✅ Beautiful color-coded UI
✅ Pull-to-refresh
✅ Error handling
✅ Permission-based access

---

## 📖 Full Documentation

- **Detailed Guide:** `AIR_WEIGHT_ANALYTICS.md`
- **Implementation:** `IMPLEMENTATION_SUMMARY.md`
- **API Docs:** See Analytics API section in docs

---

## ⚡ Quick Facts

- **Endpoint:** `GET /api/analytics/air-weight`
- **Permission:** `reports.view`
- **Data Source:** `shipments.actual_weight` (Column F in Excel)
- **Dispatch Source:** `dispatch_records.weight_kg`
- **Unit:** Kilograms (kg)
- **Update Frequency:** Real-time (on API call)

---

## 🐛 Quick Fixes

**Problem:** Weight shows 0
**Fix:** Upload AIR shipments with weight in Excel Column F

**Problem:** Can't access analytics
**Fix:** User needs `reports.view` permission

**Problem:** Dispatch not deducting
**Fix:** Enter weight_kg when creating dispatch

---

## 📞 Need Help?

1. Check error message on screen
2. Review `AIR_WEIGHT_ANALYTICS.md`
3. Check backend logs
4. Verify user permissions
5. Ensure data exists in database

---

**Status:** ✅ Production Ready

**Last Updated:** December 2024
