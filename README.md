# ✈️ Air-Line-Management-System
This Kivy-based Airline Passenger Management App lets users add passengers, view available seats, and check all registered passengers. It assigns random seats and flight times while fetching real-time weather for destinations via the Open-Meteo API. The app uses SQLite for data storage and Kivy's ScreenManager for smooth navigation.

from kivy.app import App
from kivy.uix.screenmanager import ScreenManager, Screen
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.label import Label
from kivy.uix.button import Button
from kivy.uix.textinput import TextInput
from kivy.uix.spinner import Spinner
from kivy.uix.scrollview import ScrollView
import sqlite3
import random
from datetime import datetime, timedelta
import requests


# 📃 Initialize SQLite Database
def initialize_database():
    conn = sqlite3.connect("airline_passenger_data.db")
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS passengers (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            destination TEXT NOT NULL,
            seat_number INTEGER NOT NULL,
            flight_time TEXT NOT NULL
        )
    ''')
    conn.commit()
    return conn


# ⚙️ Generate random flight time
def generate_random_flight_time():
    now = datetime.now()
    random_days = random.randint(0, 7)
    random_minutes = random.randint(0, 23 * 60)
    flight_time = now + timedelta(days=random_days, minutes=random_minutes)
    return flight_time.strftime("%Y-%m-%d %H:%M:%S")


# 🗺 Fetch weather data for a city
def get_weather_open_meteo(city):
    try:
        # Get latitude and longitude of the city
        geocode_url = f"https://geocoding-api.open-meteo.com/v1/search?name={city}"
        response = requests.get(geocode_url).json()
        if not response.get("results"):
            return {"error": "City not found."}

# 🌍 Extract latitude and longitude
location = response["results"][0]
latitude = location["latitude"]
longitude = location["longitude"]

# 🌐 Fetch weather data
  weather_url = f"https://api.open-meteo.com/v1/forecast?latitude={latitude}&longitude={longitude}&current_weather=true"
  weather_response = requests.get(weather_url).json()

   if "current_weather" in weather_response:
            weather = weather_response["current_weather"]
            return {
                "temperature": weather["temperature"],
                "windspeed": weather["windspeed"],
                "condition": "Clear" if weather["weathercode"] == 0 else "Cloudy or Rainy"
            }
    except Exception as e:
        return {"error": str(e)}

  return {"error": "Failed to fetch weather data."}


class HomeScreen(Screen):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        layout = BoxLayout(orientation="vertical", padding=20, spacing=10)

  layout.add_widget(Button(text="Add Passenger", size_hint=(1, 0.2), on_release=lambda x: setattr(self.manager, "current", "add")))
        layout.add_widget(Button(text="View Available Seats", size_hint=(1, 0.2), on_release=lambda x: setattr(self.manager, "current", "seats")))
        layout.add_widget(Button(text="View All Passengers", size_hint=(1, 0.2), on_release=lambda x: setattr(self.manager, "current", "view")))

  self.add_widget(layout)


class AddPassengerScreen(Screen):
    def __init__(self, conn, **kwargs):
        super().__init__(**kwargs)
        self.conn = conn

  layout = BoxLayout(orientation="vertical", padding=20, spacing=10)
      self.name_input = TextInput(hint_text="Enter Passenger Name", multiline=False)
        layout.add_widget(self.name_input)

  self.destination_spinner = Spinner(
            text="Select Destination",
            values=("Delhi", "Mumbai", "Bangalore", "Chennai", "Kolkata", "Hyderabad", "Jaipur")
        )
        layout.add_widget(self.destination_spinner)

  layout.add_widget(Button(text="Add Passenger", size_hint=(1, 0.2), on_release=self.add_passenger))
  layout.add_widget(Button(text="Back", size_hint=(1, 0.2), on_release=lambda x: setattr(self.manager, "current", "home")))

  self.message_label = Label()
  layout.add_widget(self.message_label)

  self.weather_label = Label()
  layout.add_widget(self.weather_label)

  self.add_widget(layout)

  def add_passenger(self, instance):
        name = self.name_input.text.strip()
        destination = self.destination_spinner.text

  if not name or destination == "Select Destination":
            self.message_label.text = "Please provide valid details."
            return

  cursor = self.conn.cursor()
        seat_number = random.randint(1, 1000)
        flight_time = generate_random_flight_time()

  cursor.execute(
            "INSERT INTO passengers (name, destination, seat_number, flight_time) VALUES (?, ?, ?, ?)",
            (name, destination, seat_number, flight_time)
        )
        self.conn.commit()

   self.message_label.text = f"Passenger {name} added with seat {seat_number}."
        self.name_input.text = ""
        self.destination_spinner.text = "Select Destination"

  # 💈Fetch and display weather information
   weather = get_weather_open_meteo(destination)
        if "error" not in weather:
            self.weather_label.text = f"Weather at {destination}:\nTemperature: {weather['temperature']}°C\n" \
                                       f"Wind Speed: {weather['windspeed']} km/h\nCondition: {weather['condition']}"
        else:
            self.weather_label.text = f"Weather data unavailable: {weather['error']}"


class ViewSeatsScreen(Screen):
    def __init__(self, conn, **kwargs):
        super().__init__(**kwargs)
        self.conn = conn

  layout = BoxLayout(orientation="vertical", padding=20, spacing=10)

   self.scroll_view = ScrollView(size_hint=(1, 0.8))
        self.seats_label = Label(size_hint_y=None, text_size=(self.width, None))
        self.scroll_view.add_widget(self.seats_label)
        layout.add_widget(self.scroll_view)

  layout.add_widget(Button(text="Back", size_hint=(1, 0.2), on_release=lambda x: setattr(self.manager, "current", "home")))

   self.add_widget(layout)

def on_enter(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT seat_number FROM passengers")
        assigned_seats = {row[0] for row in cursor.fetchall()}

  total_seats = set(range(1, 1001))
        available_seats = sorted(total_seats - assigned_seats)

   self.seats_label.text = f"Available Seats:\n{', '.join(map(str, available_seats))}"


class ViewPassengersScreen(Screen):
    def __init__(self, conn, **kwargs):
        super().__init__(**kwargs)
        self.conn = conn

  layout = BoxLayout(orientation="vertical", padding=20, spacing=10)

  self.passengers_label = Label(size_hint=(1, 0.8))
        scroll_view = ScrollView(size_hint=(1, 0.8))
        scroll_view.add_widget(self.passengers_label)
        layout.add_widget(scroll_view)

  layout.add_widget(Button(text="Back", size_hint=(1, 0.2), on_release=lambda x: setattr(self.manager, "current", "home")))

  self.add_widget(layout)

  def on_enter(self):
        cursor = self.conn.cursor()
        cursor.execute("SELECT * FROM passengers")
        passengers = cursor.fetchall()

  self.passengers_label.text = "\n".join(
            f"ID: {p[0]}, Name: {p[1]}, Destination: {p[2]}, Seat: {p[3]}, Time: {p[4]}" for p in passengers
        )


class AirlineApp(App):
    def build(self):
        conn = initialize_database()

  sm = ScreenManager()
        sm.add_widget(HomeScreen(name="home"))
        sm.add_widget(AddPassengerScreen(name="add", conn=conn))
        sm.add_widget(ViewSeatsScreen(name="seats", conn=conn))
        sm.add_widget(ViewPassengersScreen(name="view", conn=conn))

  return sm


if __name__ == "__main__":
    AirlineApp().run()
    
