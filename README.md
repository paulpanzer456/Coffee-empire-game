# Coffee-empire-game
<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8">
  <title>Coffee Empire - Python Game</title>
  <!-- Brython laden -->
  <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/brython@3.9.5/brython.min.js"></script>
  <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/brython@3.9.5/brython_stdlib.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f8e8d4;
      padding: 20px;
      text-align: center;
    }
    #output {
      width: 80%;
      height: 300px;
      margin: 20px auto;
      display: block;
      font-family: monospace;
    }
    button {
      padding: 10px 15px;
      margin: 10px;
      background: #8b4513;
      color: white;
      border: none;
      cursor: pointer;
      border-radius: 5px;
    }
    button:hover {
      background: #a0522d;
    }
  </style>
</head>
<body onload="brython()">
  <h1>Coffee Empire - Python Spiel</h1>
  <!-- Ausgabe-Textarea -->
  <textarea id="output" readonly>Drücke "Spiel starten", um zu beginnen.</textarea><br>
  <button id="startBtn">Spiel starten</button>

  <!-- Brython Python Code -->
  <script type="text/python">
import random
from browser import document, window

# Überschreibe input() mit window.prompt()
def input(prompt_text):
    return window.prompt(prompt_text)

# Überschreibe print(), sodass in das Textarea geschrieben wird
def print_to_output(*args, **kwargs):
    out = document["output"]
    text = " ".join(str(arg) for arg in args) + "\n"
    out.value += text

# Ersetze den eingebauten print durch unsere Funktion
print = print_to_output

class Cafe:
    def __init__(self, name, city):
        self.name = name
        self.city = city
        self.price = 5.0  # Standardpreis pro Kaffee
        self.employees = 1
        self.customers = random.randint(20, 50)
        self.revenue = 0
        self.expenses = 0

    def calculate_profit(self):
        self.revenue = self.customers * self.price
        self.expenses = self.employees * 50  # Gehalt pro Mitarbeiter
        return self.revenue - self.expenses

    def hire_employee(self):
        self.employees += 1
        print(f"{self.name} hat jetzt {self.employees} Mitarbeiter.")

    def set_price(self, new_price):
        self.price = new_price
        print(f"Neuer Preis für Kaffee in {self.name}: ${self.price:.2f}")

    def simulate_day(self):
        self.customers = max(10, random.randint(20, 50) - int(self.price * 2))
        profit = self.calculate_profit()
        print(f"\n{self.name} in {self.city} hatte {self.customers} Kunden heute.")
        print(f"Einnahmen: ${self.revenue:.2f} | Ausgaben: ${self.expenses:.2f} | Gewinn: ${profit:.2f}")
        return profit

class CoffeeEmpire:
    def __init__(self):
        self.cafes = []
        self.balance = 5000  # Startkapital

    def open_cafe(self, name, city):
        if self.balance >= 1000:
            new_cafe = Cafe(name, city)
            self.cafes.append(new_cafe)
            self.balance -= 1000
            print(f"Neues Café '{name}' in {city} eröffnet!")
        else:
            print("Nicht genug Geld für eine neue Filiale!")

    def play_turn(self):
        print("\n---- Neuer Tag beginnt ----")
        daily_profit = sum(cafe.simulate_day() for cafe in self.cafes)
        self.balance += daily_profit
        print(f"Gesamtkontostand: ${self.balance:.2f}")

    def manage_game(self):
        while True:
            print("\n1. Neues Café eröffnen\n2. Preis ändern\n3. Mitarbeiter einstellen\n4. Weiter zum nächsten Tag\n5. Spiel beenden")
            choice = input("Wähle eine Aktion: ")
            if choice is None:
                break  # Abbruch, falls dialog abgebrochen wird

            if choice == "1":
                name = input("Name des Cafés: ")
                city = input("Stadt: ")
                if name and city:
                    self.open_cafe(name, city)
                else:
                    print("Ungültige Eingabe.")
            elif choice == "2":
                if not self.cafes:
                    print("Keine Cafés vorhanden!")
                    continue
                for i, cafe in enumerate(self.cafes):
                    print(f"{i+1}. {cafe.name} ({cafe.city}) - Preis: ${cafe.price:.2f}")
                idx = input("Welches Café? (Nummer eingeben) ")
                if idx is None or not idx.isdigit():
                    print("Ungültige Eingabe.")
                    continue
                idx = int(idx) - 1
                new_price = input("Neuer Preis: ")
                try:
                    new_price = float(new_price)
                    self.cafes[idx].set_price(new_price)
                except:
                    print("Ungültiger Preis.")
            elif choice == "3":
                if not self.cafes:
                    print("Keine Cafés vorhanden!")
                    continue
                for i, cafe in enumerate(self.cafes):
                    print(f"{i+1}. {cafe.name} ({cafe.city}) - Mitarbeiter: {cafe.employees}")
                idx = input("Welches Café? (Nummer eingeben) ")
                if idx is None or not idx.isdigit():
                    print("Ungültige Eingabe.")
                    continue
                idx = int(idx) - 1
                self.cafes[idx].hire_employee()
            elif choice == "4":
                self.play_turn()
            elif choice == "5":
                print("Spiel beendet!")
                break
            else:
                print("Ungültige Eingabe, bitte erneut versuchen.")

def start_game(ev):
    # Textarea leeren
    document["output"].value = ""
    game = CoffeeEmpire()
    game.manage_game()

# Button-Event binden
document["startBtn"].bind("click", start_game)
  </script>
</body>
</html>
