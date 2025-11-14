# OpenWeather Current Weather

Gets current weather data for a specific location using coordinates (latitude and longitude) via the OpenWeather Current Weather API.

## Usage

The action expects a JSON object with weather configuration:

```json
{
  "weather_config": {
    "lat": 51.5073219,
    "lon": -0.1276474,
    "open_weather_api_key": "your-openweather-api-key"
  }
}
```

### Parameters

- `weather_config` (WeatherConfig, required): Configuration object containing:
  - `lat` (number, required): Latitude of the location (e.g., 51.5073219 for London)
  - `lon` (number, required): Longitude of the location (e.g., -0.1276474 for London)
  - `open_weather_api_key` (string, required): Your OpenWeather API key

### WeatherConfig Type

```typescript
{
  lat: number;
  lon: number;
  open_weather_api_key: string;
}
```

## Output

The action returns a `WeatherResponse` object with comprehensive current weather data:

```json
{
  "weather": {
    "coord": {
      "lon": -0.1276474,
      "lat": 51.5073219
    },
    "weather": [
      {
        "id": 800,
        "main": "Clear",
        "description": "clear sky",
        "icon": "01d"
      }
    ],
    "base": "stations",
    "main": {
      "temp": 285.15,
      "feels_like": 284.5,
      "temp_min": 283.15,
      "temp_max": 287.15,
      "pressure": 1013,
      "humidity": 65,
      "sea_level": 1013,
      "grnd_level": 1008
    },
    "visibility": 10000,
    "wind": {
      "speed": 3.5,
      "deg": 180
    },
    "clouds": {
      "all": 0
    },
    "dt": 1609459200,
    "sys": {
      "country": "GB",
      "sunrise": 1609416000,
      "sunset": 1609446000
    },
    "timezone": 0,
    "id": 2643743,
    "name": "London",
    "cod": 200
    }
}
```

### WeatherResponse Type

```typescript
{
  coord: {
    lon: number;
    lat: number;
  };
  weather: Array<{
    id: number;
    main: string;
    description: string;
    icon: string;
  }>;
  base: string;
  main: {
    temp: number;           // Temperature in Kelvin
    feels_like: number;     // Feels like temperature in Kelvin
    temp_min: number;       // Minimum temperature in Kelvin
    temp_max: number;       // Maximum temperature in Kelvin
    pressure: number;       // Atmospheric pressure in hPa
    humidity: number;       // Humidity percentage
    sea_level?: number;     // Sea level pressure in hPa
    grnd_level?: number;    // Ground level pressure in hPa
  };
  visibility: number;       // Visibility in meters
  wind: {
    speed: number;          // Wind speed in m/s
    deg: number;            // Wind direction in degrees
  };
  clouds: {
    all: number;            // Cloudiness percentage
  };
  dt: number;               // Time of data calculation (Unix timestamp)
  sys: {
    country: string;        // Country code
    sunrise: number;        // Sunrise time (Unix timestamp)
    sunset: number;         // Sunset time (Unix timestamp)
  };
  timezone: number;         // Timezone shift in seconds from UTC
  id: number;               // City ID
  name: string;             // City name
  cod: number;              // Internal parameter
}
```

## Features

- ✅ Uses OpenWeather Current Weather API
- ✅ Returns comprehensive weather data including temperature, humidity, pressure, wind, and more
- ✅ Includes weather conditions with descriptions and icons
- ✅ Returns sunrise/sunset times
- ✅ Provides visibility and cloud coverage data
- ✅ Composition action built on `starthubhq/http-get-wasm`

## Getting an OpenWeather API Key

1. Sign up at [OpenWeather](https://openweathermap.org/api)
2. Navigate to your API keys section
3. Generate a new API key
4. Use the API key in the `open_weather_api_key` field

## Temperature Units

**Note:** All temperatures are returned in **Kelvin** by default. To convert:
- **Celsius**: `temp - 273.15`
- **Fahrenheit**: `(temp - 273.15) * 9/5 + 32`

## Example

```json
{
  "weather_config": {
    "lat": 40.7128,
    "lon": -74.0060,
    "open_weather_api_key": "your-api-key-here"
  }
}
```

This will return current weather data for New York City (coordinates: 40.7128°N, 74.0060°W).

## Test

### Using the Starthub CLI

First, make sure the Starthub server is running:

```bash
starthub start
```

Then test the action using the CLI:

```bash
starthub run starthubhq/openweather-current-weather:0.0.1
```

The CLI will prompt you for the input values, or you can pipe JSON input:

```bash
echo '[{
  "lat": 51.5073219,
  "lon": -0.1276474,
  "open_weather_api_key": "your-api-key-here"
}]' | starthub run starthubhq/openweather-current-weather:0.0.1
```

### Using curl (API endpoint)

You can also test directly against the API endpoint:

```bash
curl -X POST http://localhost:3000/api/run \
  -H "Content-Type: application/json" \
  -d '{
    "action": "starthubhq/openweather-current-weather:0.0.1",
    "inputs": [{
      "lat": 35.6762,
      "lon": 139.6503,
      "open_weather_api_key": "your-api-key-here"
    }]
  }'
```

**Note:** The `inputs` parameter is an array where the first element is the `WeatherConfig` object (not wrapped in `weather_config`).

### Expected Output

```json
[{
  "coord": {
    "lon": 139.6503,
    "lat": 35.6762
  },
  "weather": [{
    "id": 800,
    "main": "Clear",
    "description": "clear sky",
    "icon": "01d"
  }],
  "main": {
    "temp": 288.15,
    "feels_like": 287.5,
    "temp_min": 286.15,
    "temp_max": 290.15,
    "pressure": 1015,
    "humidity": 70
  },
  "name": "Tokyo",
  ...
}]
```

## Combining with Coordinates Action

You can combine this action with `openweather-coordinates-by-location-name` to get weather by location name:

1. First, use `openweather-coordinates-by-location-name` to get coordinates from a location name
2. Then, use `openweather-current-weather` with those coordinates to get the weather data

