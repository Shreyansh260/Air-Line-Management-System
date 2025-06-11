# ✈️ Air-Line-Management-System

> A sleek Kivy-powered GUI app to manage airline passengers, track seat availability, and check live weather conditions for destinations.

---

## 🚀 Overview

The **Air-Line-Management-System** is a Python-based application built using the **Kivy framework**. It provides an intuitive user interface for airline staff or management to:

- Add and register new passengers  
- Check available seats dynamically  
- View all registered passengers and their flight info  
- Get **real-time weather updates** for destination cities via the **Open-Meteo API**

Data is stored securely using **SQLite**, and the app ensures smooth screen transitions using Kivy’s `ScreenManager`.

---

## 🛠️ Features

### ✅ Passenger Management
- Add passenger name and choose a destination
- Random seat assignment (1–1000)
- Auto-generated random flight time within 7 days

### 🪑 Seat Tracking
- View all available seats in real-time
- Ensures no duplicate seat assignments

### 🌦️ Weather Forecasting
- Uses [Open-Meteo Geocoding API](https://open-meteo.com/) to fetch latitude and longitude
- Displays current weather, temperature, and wind speed for destination cities

### 💾 Local Database
- Uses **SQLite** for offline persistent storage of passenger data

### 🎨 UI/UX
- Simple, responsive layout built with Kivy’s `BoxLayout`
- Clean and scrollable views using `ScrollView`, `Spinner`, and `TextInput`

---

## 📸 Screenshots


---

## 🧱 Tech Stack

| Component     | Technology        |
|---------------|-------------------|
| GUI           | Kivy              |
| Database      | SQLite3           |
| API           | Open-Meteo API    |
| Language      | Python 3.8+       |
| UI Management | Kivy ScreenManager|

---

## 🔌 Installation

1. **Clone the Repository**
   ```bash
   git clone https://github.com/your-username/air-line-management-system.git
   cd air-line-management-system
2. **Install Required Libraries**
   ```bash
   pip install kivy requests
3. **Run the App**
   ```bash
   python mainapp.py
   ```
---
## 📦 File Structure
```
air-line-management-system/
├── airline_passenger_data.db  # SQLite database (auto-created)
├── main.py                    # Main Kivy app
├── README.md                  # Project README
```
---

## 💡 Future Enhancements
- ✈️ Live flight tracking integration

- 📱 Mobile-friendly APK build using KivyMD or Buildozer

- 🔒 Authentication system for admin use

- 📊 Data analytics dashboard for seat booking stats

---

## 📬 Contact

Made By **Shriyansh Singh Rathore**

- 📧 Email: shreyanshsinghrathore7@gmail.com
- 📱 Phone: +91-8619277114
- 📍 Poornima University, AI & Data Science
---

## 📃 License
This project is licensed under the MIT License.
