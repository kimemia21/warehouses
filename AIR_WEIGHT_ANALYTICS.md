# Air Weight Analytics System

## Overview
This system tracks the weight of AIR shipments in the warehouse, providing real-time analytics on:
- Total warehouse weight (all AIR shipments)
- Weight dispatched today
- Total weight dispatched (all time)
- Remaining weight in warehouse

## How It Works

### 1. Weight Data Collection
When uploading AIR shipment Excel files:
- The system extracts the `actual_weight` from column F (A.Weight)
- This weight is stored in the `shipments` table under the `actual_weight` field
- Only AIR shipments (shipment_type = 'AIR') are tracked

### 2. Weight Calculation

#### Total Warehouse Weight
```sql
SUM(actual_weight) FROM shipments WHERE shipment_type = 'AIR'
```
Sum of all AIR shipment weights uploaded to the system.

#### Weight Dispatched Today
```sql
SUM(dr.weight_kg) 
FROM dispatch_records dr
INNER JOIN shipments s ON dr.shipment_id = s.id
WHERE s.shipment_type = 'AIR' 
AND DATE(dr.dispatch_date) = CURDATE()
```
Sum of weight from dispatch records created today for AIR shipments.

#### Total Weight Dispatched
```sql
SUM(dr.weight_kg)
FROM dispatch_records dr
INNER JOIN shipments s ON dr.shipment_id = s.id
WHERE s.shipment_type = 'AIR'
```
Sum of all weight ever dispatched for AIR shipments.

#### Remaining Weight
```
Total Warehouse Weight - Total Weight Dispatched
```
The weight still available in the warehouse (not yet dispatched).

## Backend API

### Endpoint
```
GET /api/analytics/air-weight
```

### Authentication
Requires:
- Valid JWT token
- `reports` permission with `view` access

### Response Format
```json
{
  "success": true,
  "data": {
    "total_warehouse_weight": 5065.00,
    "weight_dispatched_today": 0.00,
    "total_weight_dispatched": 603.00,
    "remaining_weight": 4462.00,
    "unit": "kg"
  }
}
```

### Example Usage
```bash
curl -X GET http://localhost:3000/api/analytics/air-weight \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

## Frontend (Flutter App)

### Service: `AnalyticsService`
Location: `app/lib/core/services/analytics_service.dart`

```dart
final analyticsService = AnalyticsService();
final data = await analyticsService.getAirWeightAnalytics();
```

### Screen: `AnalyticsScreen`
Location: `app/lib/screens/analytics_screen.dart`

Displays four weight cards:
1. **Total Warehouse Weight** (blue) - All AIR shipment weights
2. **Dispatched Today** (green) - Weight released today
3. **Total Dispatched** (orange) - All-time dispatched weight
4. **Remaining Weight** (highlighted) - Weight still in warehouse

Features:
- Pull-to-refresh
- Auto-refresh button
- Error handling with retry
- Formatted numbers (shows K for thousands)
- Color-coded cards for easy identification

## Database Schema

### Shipments Table
```sql
CREATE TABLE shipments (
  id INT PRIMARY KEY AUTO_INCREMENT,
  shipment_type ENUM('SEA', 'AIR') NOT NULL,
  actual_weight DECIMAL(10, 2),  -- AIR shipment weight
  ...
);
```

### Dispatch Records Table
```sql
CREATE TABLE dispatch_records (
  id INT PRIMARY KEY AUTO_INCREMENT,
  shipment_id INT NOT NULL,
  weight_kg DECIMAL(10,2) DEFAULT '0.00',  -- Weight dispatched
  dispatch_date TIMESTAMP,
  ...
);
```

## Excel Upload Format

### AIR Shipment Excel (Column F)
The actual weight should be in column F (6th column):
- Column A: House Number
- Column B: Customer Name
- Column C: Contact Number
- Column D: Destination
- Column E: Cartons (CTNs)
- **Column F: Actual Weight (A.Weight)** ← This is captured
- Column G: CBM
- Column H: Remarks

## Weight Deduction Process

When a package is dispatched:
1. Create a dispatch record via the dispatch form
2. Enter the `weight_kg` for the dispatched items
3. The system automatically:
   - Records the dispatch with the weight
   - The analytics endpoint recalculates remaining weight
   - Frontend shows updated values on refresh

## Future Enhancements (Not Implemented)

### For SEA Shipments
- SEA shipments don't have weight in the Excel format yet
- When SEA Excel format includes weight, similar tracking can be added

### Additional Features to Consider
- Daily/weekly/monthly weight trends
- Weight by destination
- Weight by customer
- Alert when remaining weight is low
- Export weight reports

## Testing

### Backend Test
```bash
cd backend
node -e "
const db = require('./config/database');
const analyticsController = require('./controllers/analyticsController');

const mockReq = { query: {} };
const mockRes = {
  json: (data) => console.log(JSON.stringify(data, null, 2))
};

analyticsController.getAirWeightAnalytics(mockReq, mockRes);
"
```

### Expected Output
```json
{
  "success": true,
  "data": {
    "total_warehouse_weight": <number>,
    "weight_dispatched_today": <number>,
    "total_weight_dispatched": <number>,
    "remaining_weight": <number>,
    "unit": "kg"
  }
}
```

## Troubleshooting

### Issue: Weight showing as 0
**Solution:** 
- Check if AIR shipments have been uploaded
- Verify Excel column F has weight data
- Check database: `SELECT SUM(actual_weight) FROM shipments WHERE shipment_type = 'AIR'`

### Issue: Dispatched weight not updating
**Solution:**
- Ensure dispatch records include `weight_kg` value
- Check: `SELECT * FROM dispatch_records ORDER BY id DESC LIMIT 5`
- Verify shipment_id links to AIR shipments

### Issue: Permission denied
**Solution:**
- User needs `reports.view` permission
- Check user permissions in user_types table
- Verify JWT token is valid

## Files Modified

### Backend
- `backend/controllers/analyticsController.js` - Simplified to only air weight analytics
- `backend/routes/analyticsRoutes.js` - Single endpoint `/api/analytics/air-weight`

### Frontend
- `app/lib/core/services/analytics_service.dart` - New service for air weight
- `app/lib/screens/analytics_screen.dart` - New UI showing weight cards

### Documentation
- `AIR_WEIGHT_ANALYTICS.md` - This file

## Summary

The system now provides a clean, focused analytics solution that:
✅ Tracks AIR shipment weights from Excel uploads
✅ Shows real-time warehouse weight
✅ Tracks daily and total dispatched weights
✅ Calculates remaining weight automatically
✅ Simple, easy-to-understand UI
✅ No complex analytics that don't make sense for the business

This is a practical solution that directly answers: "How much weight do we have in the warehouse right now?"
