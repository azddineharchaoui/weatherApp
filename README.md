# Weather Application Documentation

## Overview

This weather application provides real-time weather information using the WeatherAPI.com service. The application offers a clean, responsive user interface that displays current weather conditions and hourly forecasts for a specified location. Users can search for weather by city name or use their current geolocation.

## Technical Stack

- **Frontend Framework:** Vue.js 3 with Composition API and `<script setup>` syntax
- **Build Tool:** Vite
- **HTTP Client:** Axios
- **Styling:** Scoped CSS with transitions and responsive design
- **Font:** Quicksand from Google Fonts
- **API:** WeatherAPI.com

## Features

- Current weather conditions display
- Hourly weather forecast
- Geolocation support
- Multi-language support (English and French)
- Responsive design with scrollable hourly forecasts
- Clean, user-friendly interface with visual feedback

## API Integration

The application uses the [WeatherAPI.com](https://www.weatherapi.com/) service to retrieve weather data.

### API Key

The application uses an API key stored in the main component:

```javascript
const API_KEY = '1d493fb85b524203af5111937251704'
```

**Note:** In a production environment, API keys should be stored in environment variables.

### API Endpoints Used

1. **Current Weather & Forecast:**
   - URL: `https://api.weatherapi.com/v1/forecast.json`
   - Parameters:
     - `key`: API key
     - `q`: Location (city name or coordinates)
     - `lang`: Language code (en/fr)
     - `hours`: Number of forecast hours

### API Response Structure

Key portions of the API response used in the application:

```javascript
{
  location: {
    name: "City Name",
    country: "Country Name"
  },
  current: {
    temp_c: 23.0,
    condition: {
      text: "Partly cloudy",
      icon: "//cdn.weatherapi.com/weather/64x64/day/116.png"
    },
    humidity: 65,
    wind_kph: 5.4
  },
  forecast: {
    forecastday: [{
      hour: [
        {
          time: "2023-07-15 00:00",
          temp_c: 19.6,
          condition: {
            text: "Clear",
            icon: "//cdn.weatherapi.com/weather/64x64/night/113.png"
          }
        },
        // More hourly forecasts...
      ]
    }]
  }
}
```

## Component Structure

The application consists primarily of a single Vue component (`App.vue`) with the following structure:

### Template Sections

1. **Header Section:**
   - Title and language selector

2. **Search Section:**
   - City input field
   - Search button
   - Geolocation button

3. **Current Weather Section:**
   - City and country name
   - Weather condition icon
   - Description text
   - Temperature
   - Humidity
   - Wind speed

4. **Forecast Section:**
   - Scrollable container for hourly forecasts
   - Individual forecast cards with time, icon, and temperature

### Script Functions

1. **Data Management:**
   - Reactive state for city, weather, forecast, and language
   - Translation object for multi-language support

2. **API Interaction:**
   - `fetchWeather()`: Gets weather data based on city input
   - `useGeolocation()`: Gets weather data based on user's current location

3. **Utility Functions:**
   - `t()`: Translation function
   - `getIconUrl()`: Format icon URLs from API data
   - `formatHour()`: Format datetime strings to readable time
   - `switchLanguage()`: Handle language changes

## Styling

The application uses modern CSS techniques:

- Flexbox for layout
- CSS variables for consistent styling
- Transitions and transforms for interactive elements
- Responsive design principles
- Backdrop filters for glass-like effects
- Custom styling for form elements
- Horizontally scrollable containers with hidden scrollbars

## Installation and Setup

1. **Clone the repository:**
   ```bash
   git clone <repository-url>
   cd meteo
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Create the assets folder for background image:**
   ```bash
   mkdir -p public/assets
   ```

4. **Add a clouds.jpg file to the assets folder**

5. **Run the development server:**
   ```bash
   npm run dev
   ```

## Usage Guide

1. **Search for a location:**
   - Type a city name in the search box
   - Press Enter or click the Search button

2. **Use your current location:**
   - Click the "Use My Location" button
   - Allow location access when prompted

3. **View weather details:**
   - Current temperature, conditions, humidity, and wind speed are displayed
   - Scroll horizontally to view hourly forecasts

4. **Change language:**
   - Select your preferred language from the dropdown in the header

## Customization

To customize the application:

1. **Modify the UI colors:**
   - Edit the gradient and color values in the scoped CSS section of App.vue

2. **Add additional languages:**
   - Extend the translations object with new language codes and translation strings

3. **Change the background image:**
   - Replace the clouds.jpg file in the assets folder

## Limitations and Known Issues

- Limited to current weather and short-term forecasts
- No persistence of user preferences
- API key is hardcoded rather than using environment variables
- Limited error handling for API failures

## Future Enhancements

- Extended forecast (5-day weather)
- Weather alerts and notifications
- Temperature unit conversion (Celsius/Fahrenheit)
- Additional languages
- User preferences storage
- More detailed weather data (UV index, pressure, etc.)
- Weather maps integration
- Dark/light theme toggle
