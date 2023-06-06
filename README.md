# Team-Selection
This is my code. I need to fix it such that i can print a dictionary with all player names and rating based on the user's inputted player choices. 
This is how my csv looks like: 
,Player Name,Country,Positions,Age,Attack,Defense,Goalkeeper
0,David de Gea,Spain  ,GK,31,0,0,85
1,Bruno Fernandes,Portugal  ,MF,27,88,35,0
2,Marcus Rashford,England  ,FW,24,87,21,0
3,Luke Shaw,England  ,DF,27,74,80,0
4,Christian Eriksen,Denmark  ,MF,30,84,35,0
and so on...

this is the format i would like my output dictionary to be:
'Ederson': [0, 0, 89],
       'Ruben Dias': [35, 88, 0],
       'John Stones': [43, 84, 0],
       'Kyle Walker': [59, 83, 0],
       'Manuel Akanji': [47, 84, 0],
       'Rodri': [61, 82, 0],
       'Gundogan': [84, 78, 0],
       'Kevin De Bruyne': [92, 65, 0],
       'Erling Haaland': [94, 30, 0],
       'Bernardo Silva': [86, 47, 0],
       'Jack Grealish': [83, 27, 0]
       
 im still a beginner so forgive me if these questions are stupid :)
 
 
 CODE:
 import csv
import pandas as pd
import random

away = {}
home = {}
stadium = ["home", "away"]
home_or_away = random.choice(stadium)
print("you are playing: " + home_or_away)

file_path = 'C:\\Users\\poonam\\PycharmProjects\\WebScrapingEPL\\manunited.csv'

goalkeeper_ratings = {}
defender_ratings = {}
midfielder_ratings = {}
forward_ratings = {}

with open(file_path, mode='r') as file:
    csv_reader = csv.DictReader(file)
    for row in csv_reader:
        if row['Positions'] == 'GK':
            player_name = row['Player Name']
            goalkeeper_rating = int(row['Goalkeeper'])
            goalkeeper_ratings[player_name.lower()] = goalkeeper_rating
        elif row['Positions'] == 'DF':
            player_name = row['Player Name']
            defender_rating = int(row['Defense'])
            defender_ratings[player_name.lower()] = defender_rating
        elif row['Positions'] == 'MF':
            player_name = row['Player Name']
            midfielder_rating = max(int(row['Defense']), int(row['Attack']))
            midfielder_ratings[player_name.lower()] = midfielder_rating
        elif row['Positions'] == 'FW':
            player_name = row['Player Name']
            forward_rating = int(row['Attack'])
            forward_ratings[player_name.lower()] = forward_rating


print("Choose your formation: \na. 4-3-3 \nb. 5-3-2 \nc. 5-2-3 \nd. 4-4-2 \ne. 3-4-3")
formation = input(" Enter a, b, c, d or e ").lower()

formation_inputs = {
    "a": {"defenders": 4, "midfielders": 3, "forwards": 3},
    "b": {"defenders": 5, "midfielders": 3, "forwards": 2},
    "c": {"defenders": 5, "midfielders": 2, "forwards": 3},
    "d": {"defenders": 4, "midfielders": 4, "forwards": 2},
    "e": {"defenders": 3, "midfielders": 4, "forwards": 3}
}

while True:
    if formation in formation_inputs:
        formation_name = formation_inputs[formation]
        print("You chose:", formation_name)
        break  # Exit the loop when a valid choice is made
    else:
        print("Invalid formation choice! Please try again.")
        formation = input("Enter a, b, c, d, or e: \n").lower()

num_defenders = formation_inputs[formation]["defenders"]
num_midfielders = formation_inputs[formation]["midfielders"]
num_forwards = formation_inputs[formation]["forwards"]

selected_defenders = []
selected_midfielders = []
selected_forwards = []

# Make the lineup

# Select Goalkeeper
for player, rating in goalkeeper_ratings.items():
    print(player.capitalize() + ":" + str(rating))
if home_or_away == "home":
    while True:
        home_gk = input("Enter the name of the goalkeeper: ").strip()
        if home_gk.lower() in goalkeeper_ratings:
            goalkeeper_rating = goalkeeper_ratings[home_gk.lower()]
            home[home_gk.capitalize()] = [0, 0, goalkeeper_rating]
            break
        else:
            print("Invalid goalkeeper selection! Please try again.")
elif home_or_away == 'away':
    while True:
        away_gk = input("Enter the name of the goalkeeper: ").strip()
        if away_gk.lower() in goalkeeper_ratings:
            goalkeeper_rating = goalkeeper_ratings[away_gk.lower()]
            away[away_gk.capitalize()] = [0, 0, goalkeeper_rating]
            break
        else:
            print("Invalid goalkeeper selection! Please try again.")


def team_selection(position_ratings, position_name, num_players):
    selected_players = []

    print(f"\n{position_name.capitalize()}:")
    for player, rating in position_ratings.items():
        print(player.capitalize())

    print(f"\nSelect {num_players} {position_name}:")
    while len(selected_players) < num_players:
        player = input(f"Enter the name of a {position_name}: ").lower()
        if player in position_ratings:
            player_rating = position_ratings[player]
            if player in [p[0] for p in selected_players]:
                print(f"{player.capitalize()} has already been selected!")
            else:
                selected_players.append((player, player_rating))
                print(f"You selected {player.capitalize()}: {player_rating}")
        else:
            print(f"Invalid {position_name} selection!")

    print(f"\nSelected {position_name}:")
    for player, rating in selected_players:
        print(f"{player.capitalize()} - Rating: {rating}")
    return selected_players


if home_or_away == 'home':
    # Select Defenders
    selected_defenders = team_selection(defender_ratings, "defenders", num_defenders)
    for player, rating in selected_defenders:
        home[player.capitalize()] = [rating, defender_ratings[player.lower()], 0]

    # Select Midfielders
    selected_midfielders = team_selection(midfielder_ratings, "midfielders", num_midfielders)
    for player, rating in selected_midfielders:
        home[player.capitalize()] = [rating, midfielder_ratings[player.lower()], 0]

    # Select Forwards
    selected_forwards = team_selection(forward_ratings, "forwards", num_forwards)
    for player, rating in selected_forwards:
        home[player.capitalize()] = [rating, 0, 0]
elif home_or_away == 'away':
    # Select Defenders
    selected_defenders = team_selection(defender_ratings, "defenders", num_defenders)
    for player, rating in selected_defenders:
        away[player.capitalize()] = [rating, defender_ratings[player.lower()], 0]

    # Select Midfielders
    selected_midfielders = team_selection(midfielder_ratings, "midfielders", num_midfielders)
    for player, rating in selected_midfielders:
        away[player.capitalize()] = [rating, midfielder_ratings[player.lower()], 0]

    # Select Forwards
    selected_forwards = team_selection(forward_ratings, "forwards", num_forwards)
    for player, rating in selected_forwards:
        away[player.capitalize()] = [rating, 0, 0]


if home_or_away == 'home':
    home = {}
    home.update()
    home.update(dict(selected_defenders))
    home.update(dict(selected_midfielders))
    home.update(dict(selected_forwards))
    print(home)

if home_or_away == 'away':
    away = {}
    away.update(dict(selected_defenders))
    away.update(dict(selected_midfielders))
    away.update(dict(selected_forwards))
    print(away)


       
       
