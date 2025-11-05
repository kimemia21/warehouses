# Backend API Integration Analysis

## Current State Analysis

### Backend Endpoints Available (from routes and controllers)

#### Auth Routes (`/api/auth`)
- POST `/login` ✓ (IMPLEMENTED in auth_service.dart)
- POST `/refresh-token` ❌ (NOT IMPLEMENTED)
- GET `/profile` ❌ (NOT IMPLEMENTED)
- PUT `/profile` ❌ (NOT IMPLEMENTED)
- POST `/change-password` ❌ (NOT IMPLEMENTED)
- POST `/register` ❌ (NOT IMPLEMENTED)

#### User Routes (`/api/users`)
- GET `/` - Get all users ❌ (NOT IMPLEMENTED - using mock data)
- GET `/:id` - Get user by ID ❌ (NOT IMPLEMENTED)
- PUT `/:id` - Update user ❌ (NOT IMPLEMENTED)
- DELETE `/:id` - Delete user ❌ (NOT IMPLEMENTED)
- POST `/:id/reset-password` - Reset password ❌ (NOT IMPLEMENTED)
- GET `/types/all` - Get all user types ❌ (NOT IMPLEMENTED)
- GET `/types/:id` - Get user type by ID ❌ (NOT IMPLEMENTED)
- POST `/types` - Create user type ❌ (NOT IMPLEMENTED)
- PUT `/types/:id` - Update user type ❌ (NOT IMPLEMENTED)
- DELETE `/types/:id` - Delete user type ❌ (NOT IMPLEMENTED)

#### Shipment Routes (`/api/shipments`)
- GET `/` ✓ (IMPLEMENTED in shipment_service.dart)
- GET `/:id` ✓ (IMPLEMENTED)
- POST `/` ✓ (IMPLEMENTED)
- PUT `/:id` ✓ (IMPLEMENTED)
- DELETE `/:id` ✓ (IMPLEMENTED)
- POST `/import` ✓ (IMPLEMENTED via uploadExcelFile)
- GET `/sea/summary/:container_number` ✓ (IMPLEMENTED)
- GET `/air/summary` ✓ (IMPLEMENTED)

#### Dispatch Routes (`/api/dispatch`)
- GET `/` ✓ (IMPLEMENTED in dispatch_service.dart)
- GET `/:id` ✓ (IMPLEMENTED)
- POST `/` ✓ (IMPLEMENTED)
- PUT `/:id` ✓ (IMPLEMENTED)
- DELETE `/:id` ✓ (IMPLEMENTED)
- GET `/shipment/:shipment_id/available` ✓ (IMPLEMENTED)
- GET `/shipment/:shipment_id/records` ✓ (IMPLEMENTED)
- GET `/shipment/:shipment_id/summary` ✓ (IMPLEMENTED)

#### Uploaded File Routes (`/api/files`)
- GET `/` ❌ (NOT IMPLEMENTED)
- GET `/:id` ❌ (NOT IMPLEMENTED)
- POST `/check` ❌ (NOT IMPLEMENTED)
- POST `/` ❌ (NOT IMPLEMENTED)
- PUT `/:id` ❌ (NOT IMPLEMENTED)
- DELETE `/:id` ❌ (NOT IMPLEMENTED)

### Current Implementation Patterns

1. **Communication Flow**: 
   - All API calls go through `Comms` singleton instance
   - Global `comms` instance in `constants.dart`
   - Token management via `setAuthToken()` and `clearAuthToken()`

2. **Error Handling**:
   - Responses follow: `{success: bool, rsp/data: any, error: {message: string}, statusCode: int}`
   - Backend returns structured errors: `{success: false, error: {message: "..."}}`

3. **Toast Notifications**:
   - `ToastService.showSuccess(context, message)`
   - `ToastService.showError(context, message)`
   - `ToastService.showInfo(context, message)`
   - `ToastService.showWarning(context, message)`

4. **Authentication**:
   - Login returns: `{success: true, data: {user: {...}, token: "...", refreshToken: "..."}}`
   - Token stored in secure storage and set in Comms headers
   - AuthService extends ChangeNotifier for reactive state

## Implementation Plan

### Phase 1: Auth Service Enhancement
- Add `refreshToken()` method
- Add `getProfile()` method
- Add `updateProfile()` method
- Add `changePassword()` method (replace mock)
- Add `registerUser()` method (replace mock in createUser)

### Phase 2: User Management Service
- Replace all mock data with real API calls
- Implement `fetchUsers()` with backend API
- Implement `createUser()` with backend API
- Implement `updateUser()` with backend API
- Implement `deleteUser()` with backend API
- Add `resetUserPassword()` method
- Add user type management methods

### Phase 3: Uploaded File Service
- Create new `UploadedFileService` class
- Implement all CRUD operations
- Implement file check/create functionality

### Phase 4: Screen Updates
- Update all screens using print() to use ToastService
- Update all screens using ScaffoldMessenger to use ToastService
- Ensure all error handling uses ToastService

## Endpoint Mapping

### Auth Endpoints
```dart
POST /api/auth/login -> auth_service.login() ✓
POST /api/auth/refresh-token -> auth_service.refreshToken() (NEW)
GET /api/auth/profile -> auth_service.getProfile() (NEW)
PUT /api/auth/profile -> auth_service.updateProfile() (ENHANCE)
POST /api/auth/change-password -> auth_service.changePassword() (ENHANCE)
POST /api/auth/register -> auth_service.registerUser() (NEW)
```

### User Management Endpoints
```dart
GET /api/users -> user_management_service.fetchUsers() (ENHANCE)
GET /api/users/:id -> user_management_service.getUserById() (ENHANCE)
PUT /api/users/:id -> user_management_service.updateUser() (ENHANCE)
DELETE /api/users/:id -> user_management_service.deleteUser() (ENHANCE)
POST /api/users/:id/reset-password -> user_management_service.resetUserPassword() (NEW)
GET /api/users/types/all -> user_management_service.fetchUserTypes() (NEW)
POST /api/users/types -> user_management_service.createUserType() (NEW)
PUT /api/users/types/:id -> user_management_service.updateUserType() (NEW)
DELETE /api/users/types/:id -> user_management_service.deleteUserType() (NEW)
```

### File Management Endpoints
```dart
GET /api/files -> uploaded_file_service.getAllFiles() (NEW)
GET /api/files/:id -> uploaded_file_service.getFileById() (NEW)
POST /api/files/check -> uploaded_file_service.checkOrCreateFile() (NEW)
POST /api/files -> uploaded_file_service.createFile() (NEW)
PUT /api/files/:id -> uploaded_file_service.updateFile() (NEW)
DELETE /api/files/:id -> uploaded_file_service.deleteFile() (NEW)
```
