import matplotlib.pyplot as plt
import json
import os

data_file = "workout_data.json"

def load_data():
    if os.path.exists(data_file):
        with open(data_file, "r") as f:
            return json.load(f)
    return {}

def save_data(data):
    with open(data_file, "w") as f:
        json.dump(data, f, indent=4)

def log_workout():
    data = load_data()
    date = input("Enter workout date (YYYY-MM-DD): ")
    exercise = input("Enter exercise name: ")
    reps = int(input("Enter number of reps: "))
    weight = float(input("Enter weight used (kg): "))
    
    if date not in data:
        data[date] = []
    
    data[date].append({"exercise": exercise, "reps": reps, "weight": weight})
    save_data(data)
    print("Workout logged successfully!")

def view_progress():
    data = load_data()
    if not data:
        print("No workout data found.")
        return
    
    exercise = input("Enter exercise to view progress: ")
    dates, weights = [], []
    
    for date, workouts in data.items():
        for workout in workouts:
            if workout["exercise"].lower() == exercise.lower():
                dates.append(date)
                weights.append(workout["weight"])
    
    if not dates:
        print("No records found for this exercise.")
        return
    
    plt.plot(dates, weights, marker='o', linestyle='-')
    plt.xlabel("Date")
    plt.ylabel("Weight (kg)")
    plt.title(f"Progress for {exercise}")
    plt.xticks(rotation=45)
    plt.grid()
    plt.show()

def suggest_improvement():
    data = load_data()
    if not data:
        print("No workout data available.")
        return
    
    exercise = input("Enter exercise to get improvement tips: ")
    total_reps, total_weight, count = 0, 0, 0
    
    for workouts in data.values():
        for workout in workouts:
            if workout["exercise"].lower() == exercise.lower():
                total_reps += workout["reps"]
                total_weight += workout["weight"]
                count += 1
    
    if count == 0:
        print("No data available for this exercise.")
        return
    
    avg_weight = total_weight / count
    print(f"Suggestion: Increase weight slightly (try {avg_weight + 2.5:.1f} kg) while maintaining form.")

def main():
    while True:
        print("\nWorkout Tracker")
        print("1. Log a workout")
        print("2. View progress")
        print("3. Get improvement suggestions")
        print("4. Exit")
        choice = input("Enter choice: ")
        
        if choice == "1":
            log_workout()
        elif choice == "2":
            view_progress()
        elif choice == "3":
            suggest_improvement()
        elif choice == "4":
            break
        else:
            print("Invalid choice, try again.")

if __name__ == "__main__":
    main()
