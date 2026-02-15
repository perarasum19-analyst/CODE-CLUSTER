# Design Document - Soil Sense AI

## System Architecture

### High-Level Architecture
```
┌─────────────────┐
│   Web Browser   │
│   (Frontend)    │
└────────┬────────┘
         │
         │ HTTP/HTTPS
         │
┌────────▼────────┐
│  Flask Server   │
│   (Backend)     │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
┌───▼──┐  ┌──▼────────┐
│Google│  │OpenWeather│
│OAuth │  │    API    │
└──────┘  └───────────┘
```

## Component Design

### 1. Authentication Module

#### Login System (app.py)
- **Routes**: `/` (GET, POST)
- **Methods**:
  - Manual authentication with hardcoded credentials
  - Session creation upon successful login
  - Error handling for invalid credentials

#### Google OAuth Integration
- **Route**: `/verify_token` (POST)
- **Flow**:
  1. Frontend receives Google credential token
  2. Backend verifies token using Google's public keys
  3. Extract user info (email, name, picture)
  4. Store in Flask session
  5. Return success/error response

#### Session Management
- **Route**: `/logout`
- Session-based user tracking
- Secure session key configuration

### 2. Image Analysis Module (summa.py & home.html)

#### Camera Interface
- **Components**:
  - Video stream capture using MediaDevices API
  - Front/back camera switching
  - Real-time preview display
  - Capture button for snapshot

#### Image Processing Pipeline
```
Image Input → RGB Analysis → Hyperspectral Simulation → Classification
     │              │                  │                      │
     │              │                  │                      │
     ▼              ▼                  ▼                      ▼
  Validation   Pixel-level      Color Mapping         Soil Type
  (COCO-SSD)   Calculations     (Spectrum View)      Identification
```

#### Analysis Algorithms
**Per-Pixel Calculations**:
- Nitrogen: `r*0.2 + g*0.6`
- Phosphorus: `r*0.6 + b*0.4 - g*0.2`
- Potassium: `(r+g)/2*0.9 - b*0.3`
- Iron: `r*0.9 - g*0.4`
- Moisture: `b*0.5 + g*0.3 - r*0.2`
- Stress Level: `1 - nutrients - (moisture*0.1)`

**Classification Logic**:
- Healthy Veg: nutrients > 0.65 AND potassium > 0.5
- Stressed Veg: nutrients > 0.4 (but not healthy)
- Iron-Rich Soil: iron > 0.55
- Clay Soil: moisture > 0.4
- Loam: default fallback

#### Visualization Layers
1. **RGB Canvas**: Original image display
2. **Spectrum Canvas**: Pseudo-hyperspectral visualization
3. **Ground Truth Canvas**: Color-coded soil classification

### 3. Soil Data Input Module (next.html)

#### Input Form
- pH Level: Number input (0-14, step 0.1)
- Temperature: Number input (-10 to 50°C, step 0.1)
- Drainage: Dropdown (Poor, Moderate, Good)

#### Analysis Engine
**Status Classification**:
- pH: Optimal (6-7.5), Acidic (<6), Alkaline (>7.5)
- Temperature: Ideal (18-32°C), Suboptimal (outside range)

**Suggestion Generator**:
- Acidic soil → Add lime
- Alkaline soil → Add sulfur/compost
- Cold soil → Use mulch
- Poor drainage → Add organic compost

### 4. Weather Integration Module

#### API Integration
- **Provider**: OpenWeatherMap
- **Endpoints**:
  - Geocoding: `/geo/1.0/direct`
  - Weather: `/data/2.5/weather`

#### Features
1. **City-based Search**:
   - Input: City name + Country code
   - Geocoding to lat/lon
   - Weather data retrieval

2. **Geolocation-based**:
   - Browser Geolocation API
   - Direct weather fetch by coordinates

3. **Auto-fill Integration**:
   - Temperature field auto-populated
   - City name updated

### 5. Crop Recommendation Engine

#### Crop Database Structure
```javascript
{
  name: String,
  icon: String (Font Awesome class),
  idealPh: [min, max],
  idealTemp: [min, max],
  idealDrainage: [Array of strings]
}
```

#### Matching Algorithm
```
FOR each crop IN database:
  IF (pH in range) AND 
     (temp in range) AND 
     (drainage matches):
    ADD to recommendations
```

#### Supported Crops
Rice, Wheat, Maize, Potato, Cotton, Sugarcane, Tomato, Millets, Carrot, Bell Pepper, Broccoli

### 6. Reporting & Visualization Module

#### Radar Chart (Chart.js)
- **Axes**: pH (normalized), Temperature (normalized), Drainage (scored)
- **Normalization**:
  - pH: `(value / 14) * 10`
  - Temp: `((value - (-10)) / 60) * 10`
  - Drainage: Poor=2, Moderate=5, Good=8

#### Report Sections
1. **Soil Parameters**: Grid display of pH, Temp, Drainage
2. **Health Dashboard**: Radar chart visualization
3. **Suggestions**: Bulleted list of improvements
4. **Recommended Crops**: Grid of crop cards
5. **Disease Prediction**: Warning-styled list
6. **Fertilizer Needs**: Recommendation-styled list
7. **Care Tips**: Info-styled list
8. **Economic Insights**: Insight-styled list

## UI/UX Design

### Design System

#### Color Palette
**Light Mode**:
- Primary: #3acb72 (Emerald Green)
- Primary Dark: #2a5c3f
- Background: #f8f9fa
- Card: #ffffff
- Text: #2c3e50

**Dark Mode**:
- Primary: #4CAF50
- Background: #1a1a2e
- Card: #16213e
- Text: #ecf0f1

#### Typography
- Primary: Poppins (sans-serif)
- Headings: Roboto Slab, Fredoka One
- Sizes: 16px base, responsive scaling

#### Visual Effects
- Glassmorphism: `backdrop-filter: blur(15px)`
- Transitions: 0.3-0.4s ease
- Hover effects: translateY, scale transforms
- Box shadows: Layered depth

### Page Layouts

#### 1. Login Page (index.html)
```
┌─────────────────────────┐
│       Header/Logo       │
├─────────────────────────┤
│                         │
│   ┌───────────────┐     │
│   │  Login Form   │     │
│   │  - Username   │     │
│   │  - Password   │     │
│   │  - Submit     │     │
│   └───────────────┘     │
│                         │
│   ┌───────────────┐     │
│   │ Google Sign-In│     │
│   └───────────────┘     │
│                         │
│   ┌───────────────┐     │
│   │ Mobile Login  │     │
│   └───────────────┘     │
└─────────────────────────┘
```

#### 2. Home Page (home.html)
```
┌─────────────────────────┐
│   Header + Theme Toggle │
├─────────────────────────┤
│      Hero Section       │
├─────────────────────────┤
│   Control Buttons       │
│ [Choose] [Camera] [...]│
├─────────────────────────┤
│                         │
│   ┌─────┬─────┬─────┐   │
│   │ RGB │Spec │Truth│   │
│   │     │     │     │   │
│   └─────┴─────┴─────┘   │
│                         │
├─────────────────────────┤
│  Parameter Cards Grid   │
│ [Iron][Nutrients][...]  │
├─────────────────────────┤
│    [Next Button] →      │
└─────────────────────────┘
```

#### 3. Analysis Page (next.html)
```
┌─────────────────────────┐
│   Header + Theme Toggle │
├──────────┬──────────────┤
│  Soil    │   Weather    │
│  Input   │   Widget     │
│  Form    │              │
├──────────┴──────────────┤
│   Analysis Results      │
│   - Parameters Grid     │
│   - Radar Chart         │
│   - Suggestions         │
│   - Crop Cards          │
│   - Disease List        │
│   - Fertilizer List     │
│   - Care Tips           │
│   - Economic Insights   │
├─────────────────────────┤
│       Footer            │
└─────────────────────────┘
```

## Data Flow

### Image Analysis Flow
```
1. User uploads/captures image
2. Load image to canvas (RGB display)
3. Enable "Process" button
4. User clicks "Process"
5. TensorFlow COCO-SSD validation
6. If valid → Pixel-by-pixel analysis
7. Calculate parameters (N, P, K, etc.)
8. Generate spectrum visualization
9. Classify soil type
10. Display results in parameter cards
11. Enable "Suggestions" button
```

### Weather Data Flow
```
1. User enters city OR clicks geolocation
2. If city: Geocoding API call
3. Get lat/lon coordinates
4. Weather API call with coordinates
5. Parse response data
6. Display weather info
7. Auto-fill temperature field
```

### Soil Analysis Flow
```
1. User fills form (pH, temp, drainage)
2. Submit form
3. Validate inputs
4. Calculate status classifications
5. Generate suggestions
6. Match crops from database
7. Predict diseases
8. Recommend fertilizers
9. Render radar chart
10. Display comprehensive report
```

## Security Considerations

### Authentication
- Session-based security with Flask secret key
- Google OAuth token verification server-side
- No client-side credential storage

### API Keys
- OpenWeatherMap API key (should be environment variable)
- Google Client ID (public, but restricted by domain)

### Input Validation
- Form field constraints (min, max, step)
- Type checking on all inputs
- Sanitization of user-provided data

## Performance Optimization

### Image Processing
- Canvas-based rendering (hardware accelerated)
- Efficient pixel iteration
- Clamping functions for value normalization

### API Calls
- Async/await for non-blocking requests
- Error handling with user feedback
- Timeout considerations

### UI Rendering
- CSS transitions over JavaScript animations
- Lazy loading of heavy libraries (TensorFlow.js, Chart.js)
- Responsive images with max-width constraints

## Extensibility

### Adding New Crops
```javascript
cropDatabase.push({
  name: 'New Crop',
  icon: 'fas fa-icon',
  idealPh: [min, max],
  idealTemp: [min, max],
  idealDrainage: ['type1', 'type2']
});
```

### Adding New Parameters
1. Update analysis algorithm
2. Add UI element in paramRow
3. Update visualization logic
4. Extend suggestion generator

### Theme Customization
- CSS custom properties (variables)
- Centralized color management
- Easy dark/light mode switching

## Testing Considerations

### Unit Testing
- Image processing algorithms
- Crop matching logic
- Status classification functions

### Integration Testing
- Google OAuth flow
- Weather API integration
- Session management

### UI Testing
- Responsive design breakpoints
- Theme toggle functionality
- Form validation

### Browser Compatibility
- Camera API support
- Canvas API rendering
- Modern JavaScript features (async/await, arrow functions)
