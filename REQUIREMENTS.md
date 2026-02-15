# Requirements Document - Soil Sense AI

## Project Overview
Soil Sense AI is a smart agriculture platform that leverages hyperspectral imaging analysis and real-time weather data to provide comprehensive soil health assessments and crop recommendations for farmers.

## Functional Requirements

### 1. User Authentication
- Manual login with username/password credentials
- Google OAuth 2.0 integration for social sign-in
- Session management for authenticated users
- Secure logout functionality

### 2. Soil Analysis via Image Processing
- Image upload from local device
- Real-time camera capture with front/back camera switching
- Hyperspectral visualization of soil samples
- RGB image processing and analysis
- Detection and rejection of images containing humans or irrelevant objects (using TensorFlow COCO-SSD)

### 3. Soil Parameter Measurement
The system must analyze and display:
- Iron content (%)
- Nutrient levels (%)
- Phosphorus content (%)
- Potassium content (%)
- Moisture levels (%)
- Nitrogen content (%)
- Stress level assessment (%)
- Soil type classification (Healthy Veg, Stressed Veg, Iron-Rich Soil, Clay Soil, Loam)

### 4. Manual Soil Data Input
- pH level input (0-14 scale)
- Temperature input (°C)
- Drainage type selection (Poor, Moderate, Good)

### 5. Weather Integration
- Live weather data retrieval by city/country
- Geolocation-based weather detection
- Auto-fill temperature field from weather data
- Display: temperature, humidity, wind speed, weather conditions

### 6. Crop Recommendation System
- Database of 11+ crops with ideal growing conditions
- Intelligent crop matching based on:
  - pH range compatibility
  - Temperature range compatibility
  - Drainage requirements
- Visual crop cards with icons

### 7. Comprehensive Analysis Reports
- Soil health dashboard with radar chart visualization
- pH status classification (Optimal, Acidic, Alkaline)
- Temperature status assessment
- Improvement suggestions based on soil conditions
- Disease prediction based on environmental factors
- Fertilizer recommendations
- Crop care tips
- Economic insights for farmers

### 8. User Interface Features
- Dark/Light theme toggle
- Responsive design for mobile and desktop
- Smooth animations and transitions
- Real-time processing indicators
- Glassmorphism design elements

## Non-Functional Requirements

### 1. Performance
- Image processing should complete within 2-3 seconds
- Weather API calls should respond within 3 seconds
- Smooth UI transitions (0.3-0.4s)

### 2. Security
- Secure session management with secret keys
- Google OAuth token verification
- Input validation for all form fields

### 3. Usability
- Intuitive navigation flow
- Clear visual feedback for all actions
- Error messages for invalid inputs
- Accessibility-compliant design

### 4. Compatibility
- Modern web browsers (Chrome, Firefox, Safari, Edge)
- Mobile responsive (tablets and smartphones)
- Camera API support for image capture

### 5. Scalability
- Modular architecture for easy feature additions
- Extensible crop database
- API-based weather integration

## Technical Requirements

### Backend
- Python 3.x
- Flask web framework
- Google OAuth 2.0 libraries
- OpenCV for image processing
- NumPy for numerical computations

### Frontend
- HTML5, CSS3, JavaScript (ES6+)
- TailwindCSS for styling
- Font Awesome icons
- Google Fonts (Poppins, Fredoka One, Roboto Slab)
- TensorFlow.js for ML model inference
- Chart.js for data visualization

### APIs
- Google Sign-In API
- OpenWeatherMap API (weather data)

### Browser APIs
- MediaDevices API (camera access)
- Geolocation API (location detection)
- Canvas API (image manipulation)

## User Roles
- Farmer/End User: Primary user who performs soil analysis and receives recommendations
- System Administrator: Manages authentication credentials and system configuration

## Data Requirements
- User session data (temporary)
- Soil analysis results (session-based)
- Weather data (real-time from API)
- Crop database (static, expandable)

## Constraints
- Requires internet connection for weather data and Google authentication
- Camera access required for real-time image capture
- Browser must support modern JavaScript features
- OpenWeatherMap API key required for weather functionality
