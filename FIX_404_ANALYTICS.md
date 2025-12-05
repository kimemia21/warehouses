# Fix: Analytics 404 Error

## Problem
Flutter app was getting a 404 error when accessing the analytics endpoint, even though Postman worked fine with:
```
https://warehous-production.up.railway.app/api/analytics/air-weight
```

## Root Cause
The `AppConfig.baseUrl` already includes `/api`:
```dart
static const String localUrl = "https://warehous-production.up.railway.app/api";
```

But the analytics service was adding `/api` again:
```dart
// ❌ WRONG - Creates double /api/api
Uri.parse('${AppConfig.baseUrl}/api/analytics/air-weight')
// Results in: https://warehous-production.up.railway.app/api/api/analytics/air-weight
```

## Solution
Remove the duplicate `/api` from the analytics service:
```dart
// ✅ CORRECT
Uri.parse('${AppConfig.baseUrl}/analytics/air-weight')
// Results in: https://warehous-production.up.railway.app/api/analytics/air-weight
```

## File Changed
- `app/lib/core/services/analytics_service.dart`

## How Other Services Work
Other services use the `comms` service which automatically constructs URLs:
```dart
// Other services pass endpoints without leading slash
comms.getRequests(endpoint: 'api/shipments')
// comms constructs: $baseUrl/$endpoint
```

But since we're using `http` package directly in analytics service, we need to be careful about the URL construction.

## Testing
```bash
# Expected URL
https://warehous-production.up.railway.app/api/analytics/air-weight

# Test with curl
curl -X GET https://warehous-production.up.railway.app/api/analytics/air-weight \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## Status
✅ **FIXED** - Analytics endpoint now works correctly in Flutter app.
