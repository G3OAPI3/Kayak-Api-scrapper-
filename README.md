# Kayak Flight & Car Search API

## 📡 API Endpoints

### 1. Flight Search

### 2. Car Rental Search

### 3. Location Search

````
---

## ✈️ Flight Search API

### Required Parameters

| Parameter        | Type   | Description                 | Example                   |
| ---------------- | ------ | --------------------------- | ------------------------- |
| `origin`         | string | Origin airport code         | `"MIA"`, `"LAX"`, `"LON"` |
| `destination`    | string | Destination airport code    | `"NYC"`, `"JFK"`, `"LHR"` |
| `departure_date` | string | Departure date (YYYY-MM-DD) | `"2025-10-25"`            |

### Optional Parameters

| Parameter          | Type   | Description                            |
| ------------------ | ------ | -------------------------------------- |
| `return_date`      | string | Return date for round-trip             |
| `filterParams`     | object | Filters (airlines, price, stops, etc.) |
| `searchMetaData`   | object | Pagination, pricing mode               |
| `userSearchParams` | object | Passengers, cabin class, sort mode     |

---

## 👥 Passenger Types & Cabin Classes

### Passenger Types

Configure passengers in `userSearchParams.passengers` as an array:

| Code  | Type                 | Age/Description |
| ----- | -------------------- | --------------- |
| `ADT` | Adults               | 18-64 years     |
| `SNR` | Seniors              | Over 65 years   |
| `STD` | Students             | Over 18 years   |
| `YTH` | Youths               | 12-17 years     |
| `CHD` | Children             | 2-11 years      |
| `INS` | Toddlers in own seat | Under 2 years   |
| `INL` | Infants on lap       | Under 2 years   |

**Example - 2 Adults + 1 Child:**

```json
{
  "userSearchParams": {
    "passengers": ["ADT", "ADT", "CHD"]
  }
}
````

**Example - Family Travel:**

```json
{
  "userSearchParams": {
    "passengers": ["ADT", "ADT", "YTH", "CHD", "INL"]
  }
}
```

### Cabin Classes

Specify cabin in `filterParams.fs` or in structured format:

| Cabin           | Code | Filter String |
| --------------- | ---- | ------------- |
| Economy         | `e`  | `cabin=e`     |
| Premium Economy | `p`  | `cabin=p`     |
| Business        | `b`  | `cabin=b`     |
| First           | `f`  | `cabin=f`     |

**Example - Business Class:**

```json
{
  "filterParams": {
    "fs": "cabin=b"
  }
}
```

**Note:** Cabin filtering via API is not 100% reliable. It's better to filter results client-side.

---

## 📋 Example Requests

### 1. One-Way Flight

```json
{
  "origin": "MIA",
  "destination": "NYC",
  "departure_date": "2025-10-25"
}
```

### 2. Round-Trip

```json
{
  "origin": "LAX",
  "destination": "JFK",
  "departure_date": "2025-11-01",
  "return_date": "2025-11-10"
}
```

### 3. With Filters (Nonstop, Under $500)

```json
{
  "origin": "LAX",
  "destination": "JFK",
  "departure_date": "2025-11-01",
  "filterParams": {
    "fs": "stops=0;price=-500"
  }
}
```

### 4. Advanced (Multiple Filters + Sort)

```json
{
  "origin": "LON",
  "destination": "NYC",
  "departure_date": "2025-10-20",
  "return_date": "2025-10-25",
  "filterParams": {
    "fs": "airlines=-OS;stops=-1;price=-2000;alliance=STAR_ALLIANCE"
  },
  "searchMetaData": {
    "pageNumber": 1
  },
  "userSearchParams": {
    "sortMode": "price_a"
  }
}
```

### 5. With Specific Passengers

```json
{
  "origin": "JFK",
  "destination": "CDG",
  "departure_date": "2025-12-20",
  "return_date": "2026-01-05",
  "userSearchParams": {
    "passengers": ["ADT", "ADT", "YTH", "CHD", "INL"]
  }
}
```

**2 Adults + 1 Youth (12-17) + 1 Child (2-11) + 1 Infant on lap**

### 6. Business Class with Multiple Passengers

```json
{
  "origin": "SFO",
  "destination": "LHR",
  "departure_date": "2025-11-15",
  "return_date": "2025-11-25",
  "filterParams": {
    "fs": "cabin=b;stops=0"
  },
  "userSearchParams": {
    "passengers": ["ADT", "ADT"]
  }
}
```

**Business class, nonstop, 2 adults**

---

## 🔍 Filter String (fs) Reference

All filters go in `filterParams.fs` as semicolon-separated values:

| Filter           | Syntax                      | Example                        |
| ---------------- | --------------------------- | ------------------------------ |
| **Airlines**     | `airlines=CODE` or `-CODE`  | `airlines=-OS,-BA` (exclude)   |
| **Airports**     | `airports=CODE` or `-CODE`  | `airports=-LGA,-LGW` (exclude) |
| **Stops**        | `stops=NUM` or `-NUM`       | `stops=0` (nonstop only)       |
| **Price**        | `price=-MAX` or `MIN-MAX`   | `price=-500` (under $500)      |
| **Duration**     | `legdur=-MAX` (minutes)     | `legdur=-600` (under 10h)      |
| **Layover**      | `layoverdur=MIN-` (minutes) | `layoverdur=120-` (min 2h)     |
| **Alliance**     | `alliance=NAME`             | `alliance=STAR_ALLIANCE`       |
| **Same Airline** | `sameair=sameair`           | `sameair=sameair`              |
| **Equipment**    | `equipment=TYPE` or `-TYPE` | `equipment=W` (wide-body)      |
| **WiFi**         | `wifi=wifi`                 | `wifi=wifi`                    |

**Combine filters:**

```
"fs": "airlines=-OS;stops=0;price=-500;alliance=STAR_ALLIANCE"
```

---

## 🎯 Sort Options

Set in `userSearchParams.sortMode`:

| Mode           | Description                         |
| -------------- | ----------------------------------- |
| `price_a`      | Price ascending (cheapest first)    |
| `duration_a`   | Duration ascending (shortest first) |
| `bestflight_a` | Best overall (default)              |
| `departure_a`  | Earliest departure                  |
| `arrival_a`    | Earliest arrival                    |

---

## 📊 Flight Search Response

```json
{
  "success": true,
  "error": null,
  "data": {
    "searchId": "...",
    "searchStatus": {
      "status": "complete",
      "resultsAvailable": true
    },
    "totalCount": 1768,
    "filteredCount": 831,
    "pageNumber": 1,
    "sortMode": "bestflight_a",
    "filterData": {
      "airlines": {...},
      "price": {...},
      "stops": {...}
    },
    "results": [...],
    "result_statistics": {
      "total_raw_results": 50,
      "filtered_ads": 5,
      "regular_flights": 45
    }
  }
}
```

---

## 🚗 Car Rental Search API

### Endpoint

```
POST /search-cars
```

### Required Parameters

| Parameter         | Type   | Description               | Example        |
| ----------------- | ------ | ------------------------- | -------------- |
| `pickup_location` | string | Location query ID         | `"16078"`      |
| `pickup_date`     | string | Pickup date (YYYY-MM-DD)  | `"2025-11-01"` |
| `dropoff_date`    | string | Dropoff date (YYYY-MM-DD) | `"2025-11-08"` |

**Note:** Use `/search-locations` endpoint to find location query IDs for cities.

### Optional Parameters

| Parameter         | Type    | Default | Description                                |
| ----------------- | ------- | ------- | ------------------------------------------ |
| `pickup_hour`     | integer | `12`    | Pickup hour (0-23)                         |
| `dropoff_hour`    | integer | `12`    | Dropoff hour (0-23)                        |
| `filterParams`    | object  | `{}`    | Filters (car class, transmission, etc.)    |
| `searchMetaData`  | object  | `{}`    | Pagination & config (pageNumber, pageSize) |
| `carSearchParams` | object  | `{}`    | Sort mode and other search parameters      |

### Filter String (fs) Parameters

The `filterParams.fs` string supports these filters (separated by semicolons):

| Filter              | Available Values                                                                               |
| ------------------- | ---------------------------------------------------------------------------------------------- |
| `carclass`          | `SMALL`, `MEDIUM`, `LARGE`, `SUV`, `VAN`, `PICKUPTRUCK`, `LUXURY`, `CONVERTIBLE`, `COMMERCIAL` |
| `transmission`      | `Automatic`, `Manual`                                                                          |
| `price`             | `305-` (min), `-1000` (max), `200-800` (range)                                                 |
| `bookingsites`      | Europcar, Hertz, Enterprise, Avis, Budget, Dollar, Thrifty, Sixt, Fox, etc.                    |
| `caragency`         | alamo, avis, budget, dollar, enterprise, europcar, fox, hertz, sixt, thrifty, etc.             |
| `carmaker`          | `3`=Audi, `5`=BMW, `8`=Chevrolet, `45`=Toyota, `1139`=Tesla, `30`=Mercedes, etc.               |
| `carpolicies`       | `cancel`, `driver`, `fuel`, `mileage`                                                          |
| `caroption`         | `AC`, `doors`, `awd`, `4wd`, `NavigationSystem`, `OpenAirAllTerrain`                           |
| `ecoclass`          | `Hybrid`, `Electric`, `Other`                                                                  |
| `pickupcity`        | `NON_AIRPORT`, airport codes                                                                   |
| `inairportterminal` | `LAX`, `BUR`, `LGB`, etc.                                                                      |
| `paymenttype`       | `PREPAY`, `DEPOSIT`, `POSTPAY`                                                                 |
| `timesaving`        | `earlyCheckin`, `skipCounter`                                                                  |
| `carsharing`        | `p2p`                                                                                          |
| `whiskyonly`        | `1`                                                                                            |

**Examples:**

```javascript
// Simple: Automatic SUV under $500
"fs": "carclass=SUV;transmission=Automatic;price=-500"

// Advanced: Luxury cars with all policies from specific agencies
"fs": "carclass=LUXURY;caragency=hertz,sixt;carpolicies=cancel,driver,fuel,mileage;caroption=AC,NavigationSystem"
```

### Sort Modes

| Mode      | Description                |
| --------- | -------------------------- |
| `rank_a`  | Best matches (recommended) |
| `price_a` | Price: Low to High         |
| `price_d` | Price: High to Low         |

### Structured Parameters (Consistent with Flights API)

**searchMetaData** - Pagination and display settings:

```json
{
  "searchMetaData": {
    "pageNumber": 0, // Page number (0-based)
    "pageSize": 15, // Results per page
    "priceMode": "total" // "total" or "per-day"
  }
}
```

**carSearchParams** - Search-specific parameters:

```json
{
  "carSearchParams": {
    "sortMode": "price_a" // Sort mode: rank_a, price_a, price_d
  }
}
```

&gt; **Note**: The API internally converts these structured parameters to Kayak's cars API format. You can use the same structure as the flights API for consistency.

---

## 📋 Car Search Examples

### 1. Simple Car Search

```json
{
  "pickup_location": "16078",
  "pickup_date": "2025-11-01",
  "dropoff_date": "2025-11-08"
}
```

### 2. With Filters

```json
{
  "pickup_location": "16078",
  "pickup_date": "2025-11-01",
  "dropoff_date": "2025-11-08",
  "filterParams": {
    "fs": "carclass=SUV;transmission=Automatic;carpolicies=cancel,mileage"
  }
}
```

### 3. Advanced with Pagination

```json
{
  "pickup_location": "16078",
  "pickup_date": "2025-11-01",
  "dropoff_date": "2025-11-08",
  "pickup_hour": 10,
  "dropoff_hour": 18,
  "filterParams": {
    "fs": "carclass=LUXURY;caragency=hertz,sixt;price=400-1000"
  },
  "searchMetaData": {
    "pageNumber": 0,
    "pageSize": 20,
    "priceMode": "total"
  },
  "carSearchParams": {
    "sortMode": "price_a"
  }
}
```

### Response Format

```json
{
  "success": true,
  "error": null,
  "data": {
    "searchId": "xyz123",
    "searchStatus": "complete",
    "filterData": {
      // All available filters with counts
    },
    "results": [
      {
        "resultId": "car-result-1"
        // Car rental details
      }
    ],
    "totalCount": 150,
    "filteredCount": 45,
    "result_statistics": {
      "total_raw_results": 50,
      "filtered_ads": 5,
      "regular_cars": 45
    }
  }
}
```

---

## 🌍 Location Search API

### Endpoint

```
GET /search-locations
```

### Parameters

| Parameter | Required | Description                         | Default       |
| --------- | -------- | ----------------------------------- | ------------- |
| `query`   | ✅       | City, airport name, or code         | -             |
| `type`    | ❌       | `airportonly`, `cityonly`, or `all` | `airportonly` |
| `locale`  | ❌       | Language code                       | `en`          |
| `country` | ❌       | Country code                        | `US`          |

### Examples

```bash
# Find airports in Miami
GET /search-locations?query=miami

# Find airports in New York
GET /search-locations?query=new+york

# Find specific airport
GET /search-locations?query=LAX&type=airportonly

# Find cities only
GET /search-locations?query=london&type=cityonly
```

### Response

```json
{
  "success": true,
  "error": null,
  "data": {
    "query": "miami",
    "count": 1,
    "results": [
      {
        "id": "MIA",
        "airportname": "Miami",
        "cityname": "Miami, Florida, United States",
        "displayname": "Miami (MIA - Miami Intl.)",
        "city": "Miami",
        "state": "Florida",
        "country": "United States",
        "countrycode": "US",
        "lat": 25.79325,
        "lng": -80.29056,
        "timezone": "America/New_York"
      }
    ]
  }
}
```

---

## 💡 Common Use Cases

### Use Case 1: Budget Travel

```json
POST /search-flights
{
  "origin": "LAX",
  "destination": "JFK",
  "departure_date": "2025-11-01",
  "filterParams": {
    "fs": "price=-400;stops=-0"
  },
  "userSearchParams": {
    "sortMode": "price_a"
  }
}
```

- Under $400
- At least 1 stop (cheaper)
- Sorted by price

### Use Case 2: Business Travel

```json
POST /search-flights
{
  "origin": "SFO",
  "destination": "LHR",
  "departure_date": "2025-11-15",
  "return_date": "2025-11-18",
  "filterParams": {
    "fs": "cabin=b;stops=0;alliance=STAR_ALLIANCE;equipment=W;wifi=wifi"
  },
  "userSearchParams": {
    "passengers": ["ADT"]
  }
}
```

- Business class cabin
- Nonstop only
- Star Alliance
- Wide-body aircraft
- WiFi required
- 1 adult passenger

### Use Case 3: Flexible Traveler

```json
POST /search-flights
{
  "origin": "MIA",
  "destination": "LAX",
  "departure_date": "2025-12-01",
  "filterParams": {
    "fs": "price=-600;stops=-2;legdur=-480"
  },
  "userSearchParams": {
    "passengers": ["ADT"]
  }
}
```

- Under $600
- Max 1 stop
- Under 8 hours
- 1 adult passenger

### Use Case 4: Family Trip

```json
POST /search-flights
{
  "origin": "LAX",
  "destination": "ORL",
  "departure_date": "2025-12-20",
  "return_date": "2026-01-05",
  "filterParams": {
    "fs": "cabin=e;price=-1500"
  },
  "userSearchParams": {
    "passengers": ["ADT", "ADT", "YTH", "CHD", "CHD"],
    "sortMode": "price_a"
  }
}
```

- Economy class
- Under $1500
- 2 Adults + 1 Youth (12-17) + 2 Children (2-11)
- Sorted by cheapest price

### Use Case 5: Student Travel

```json
POST /search-flights
{
  "origin": "NYC",
  "destination": "LON",
  "departure_date": "2025-06-01",
  "return_date": "2025-08-15",
  "filterParams": {
    "fs": "price=-800;stops=-0"
  },
  "userSearchParams": {
    "passengers": ["STD"],
    "sortMode": "price_a"
  }
}
```

- Under $800
- At least 1 stop (cheaper)
- 1 Student passenger
- Sorted by price

### Use Case 6: Senior Couple Trip

```json
POST /search-flights
{
  "origin": "MIA",
  "destination": "BCN",
  "departure_date": "2025-09-15",
  "return_date": "2025-09-30",
  "filterParams": {
    "fs": "cabin=p;stops=-2;equipment=W"
  },
  "userSearchParams": {
    "passengers": ["SNR", "SNR"],
    "sortMode": "duration_a"
  }
}
```

- Premium Economy
- Max 1 stop
- Wide-body aircraft (more comfort)
- 2 Senior passengers (over 65)
- Sorted by shortest duration

---

## 🔗 Complete Workflow

### Step 1: Find Airport Codes

```bash
GET /search-locations?query=miami
# Returns: MIA

GET /search-locations?query=new+york
# Returns: JFK, LGA, EWR
```

### Step 2: Search Flights

```bash
POST /search-flights
{
  "origin": "MIA",
  "destination": "JFK",
  "departure_date": "2025-11-01",
  "return_date": "2025-11-10"
}
```

### Step 3: Apply Filters

```bash
POST /search-flights
{
  "origin": "MIA",
  "destination": "JFK",
  "departure_date": "2025-11-01",
  "return_date": "2025-11-10",
  "filterParams": {
    "fs": "stops=0;price=-500"
  }
}
```

### Step 4: Paginate Results

```bash
POST /search-flights
{
  "origin": "MIA",
  "destination": "JFK",
  "departure_date": "2025-11-01",
  "return_date": "2025-11-10",
  "searchMetaData": {
    "pageNumber": 2
  }
}
```

---

## 🐛 Common Errors

### 400 - Missing Required Field

```json
{
  "success": false,
  "error": "origin is required in request body",
  "data": null
}
```

**Fix:** Add required fields (origin, destination, departure_date)

### 400 - Invalid Date Format

```json
{
  "success": false,
  "error": "departure_date must be in YYYY-MM-DD format",
  "data": null
}
```

**Fix:** Use YYYY-MM-DD format (e.g., "2025-10-25")

## 🧪 Testing with Postman

Import the Postman collection: `POSTMAN_EXAMPLES.json`

Or use these curl commands:

```bash
# Simple search
curl -X POST http://localhost:5000/search-flights \
  -H "Content-Type: application/json" \
  -d '{"origin":"MIA","destination":"NYC","departure_date":"2025-10-25"}'

# With filters
curl -X POST http://localhost:5000/search-flights \
  -H "Content-Type: application/json" \
  -d '{"origin":"LAX","destination":"JFK","departure_date":"2025-11-01","filterParams":{"fs":"stops=0;price=-500"}}'

# Location search
curl "http://localhost:5000/search-locations?query=miami"
```

---

---

## 🎓 Pro Tips

1. **Use Location Search First** - Get correct airport codes before searching flights
2. **Start Simple** - Begin with just origin, destination, and date
3. **Add Filters Incrementally** - Add one filter at a time
4. **Check Result Count** - Use `filteredCount` to see impact of filters
5. **Reuse Search ID** - For pagination, include `searchId` from first response
6. **Passenger Types** - Use correct passenger codes (ADT, SNR, STD, YTH, CHD, INS, INL)
7. **Cabin Filtering** - Cabin class filtering is not 100% reliable via API, better to filter client-side
8. **Family Travel** - Combine passenger types: `["ADT", "ADT", "CHD", "INL"]` for 2 adults + child + infant

---

## 📝 Complete Passenger & Cabin Reference

### All Passenger Codes

```json
{
  "userSearchParams": {
    "passengers": [
      "ADT", // Adult 18-64
      "SNR", // Senior over 65
      "STD", // Student over 18
      "YTH", // Youth 12-17
      "CHD", // Child 2-11
      "INS", // Toddler in own seat under 2
      "INL" // Infant on lap under 2
    ]
  }
}
```

### All Cabin Classes

```json
{
  "filterParams": {
    "fs": "cabin=e" // Economy (default)
    // "cabin=p"     // Premium Economy
    // "cabin=b"     // Business
    // "cabin=f"     // First Class
  }
}
```

**Real-world example combinations:**

```json
// Family vacation - 2 adults, 1 teen, 2 kids, 1 infant
{
  "userSearchParams": {
    "passengers": ["ADT", "ADT", "YTH", "CHD", "CHD", "INL"]
  }
}

// Business trip - 2 adults, business class
{
  "filterParams": { "fs": "cabin=b;stops=0" },
  "userSearchParams": {
    "passengers": ["ADT", "ADT"]
  }
}

// Student backpacking - 3 students
{
  "userSearchParams": {
    "passengers": ["STD", "STD", "STD"]
  }
}

// Senior couple - premium economy
{
  "filterParams": { "fs": "cabin=p" },
  "userSearchParams": {
    "passengers": ["SNR", "SNR"]
  }
}
```

---

## ✅ Features

- ✈️ Real-time flight data from Kayak
- 🚗 Car rental search with extensive filters
- 🔍 Extensive filters (airlines, price, stops, duration, car class, transmission, etc.)
- 🚫 Ad-free results (automatically filtered)
- 📄 Pagination support
- 🔄 Sort by price, duration, departure time, or rank
- 🌍 Airport/city location search
- 📊 Complete Kayak response (searchStatus, filterData, etc.)
- ⚡ Optimized for speed (~10-15s per request)

---

**Three powerful endpoints. Unlimited possibilities.** ✈️🚗🌍
