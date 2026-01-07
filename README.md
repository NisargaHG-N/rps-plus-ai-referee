# rps-plus-ai-referee
AI Game Referee – Rock Paper Scissors Plus
from dataclasses import dataclass
import random

@dataclass
class GameState:
    round: int = 0
    user_score: int = 0
    bot_score: int = 0
    user_bomb_used: bool = False
    bot_bomb_used: bool = False

def validate_move(move, bomb_used):
    if move not in ["rock", "paper", "scissors", "bomb"]:
        return False

    if move == "bomb" and bomb_used:
        return False

    return True

def resolve_round(user_move, state):
    bot_choices = ["rock", "paper", "scissors"]
    if not state.bot_bomb_used:
        bot_choices.append("bomb")

    bot_move = random.choice(bot_choices)

    if user_move == "bomb":
        state.user_bomb_used = True
    if bot_move == "bomb":
        state.bot_bomb_used = True

    if user_move == bot_move:
        return bot_move, "draw"

    if user_move == "bomb":
        return bot_move, "user"

    if bot_move == "bomb":
        return bot_move, "bot"

    wins = {
        "rock": "scissors",
        "scissors": "paper",
        "paper": "rock"
    }

    if wins[user_move] == bot_move:
        return bot_move, "user"
    else:
        return bot_move, "bot"

def update_game_state(state, winner):
    state.round += 1
    if winner == "user":
        state.user_score += 1
    elif winner == "bot":
        state.bot_score += 1

def game_agent():
    state = GameState()

    print("Rock–Paper–Scissors–Plus")
    print("Best of 3 rounds")
    print("Moves: rock, paper, scissors, bomb (once)\n")

    while state.round < 3:
        move = input(f"Round {state.round + 1} - Your move: ").lower().strip()

        if not validate_move(move, state.user_bomb_used):
            print("Invalid move. Round wasted.\n")
            state.round += 1
            continue

        bot_move, winner = resolve_round(move, state)
        update_game_state(state, winner)

        print("Bot move:", bot_move)
        print("Round result:", winner.upper())
        print("Score -> You:", state.user_score, "Bot:", state.bot_score, "\n")

    print("Game Over")
    if state.user_score > state.bot_score:
        print("You win!")
    elif state.bot_score > state.user_score:
        print("Bot wins!")
    else:
        print("Draw!")


if __name__ == "__main__":
    game_agent()
