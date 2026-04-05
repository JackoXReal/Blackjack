import random

# Card values
RANKS = ['2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K', 'A']
SUITS = ['♠', '♥', '♦', '♣']

def create_deck():
    deck = []
    for r in RANKS:
        for s in SUITS:
            deck.append((r, s))
    random.shuffle(deck)
    return deck

def card_value(card):
    rank, _ = card
    if rank in ['J', 'Q', 'K']:
        return 10
    if rank == 'A':
        return 11
    return int(rank)

def hand_value(hand):
    total = sum(card_value(c) for c in hand)
    aces = sum(1 for c in hand if c[0] == 'A')
    while total > 21 and aces > 0:
        total -= 10
        aces -= 1
    return total

def show_hand(hand, hidden=False):
    if hidden:
        return f"[{hand[0][0]}{hand[0][1]}] [??]"
    return " ".join(f"[{r}{s}]" for r, s in hand)

def play_round():
    deck = create_deck()

    player_hand = [deck.pop(), deck.pop()]
    dealer_hand = [deck.pop(), deck.pop()]

    print("=== Blackjack ===")
    print(f"Dealer: {show_hand(dealer_hand, hidden=True)}")
    print(f"You:    {show_hand(player_hand)} (total: {hand_value(player_hand)})")

    # Player turn
    while True:
        if hand_value(player_hand) == 21:
            print("Blackjack! You stand.")
            break
        choice = input("Hit or stand? (h/s): ").strip().lower()
        if choice == 'h':
            player_hand.append(deck.pop())
            print(f"You:    {show_hand(player_hand)} (total: {hand_value(player_hand)})")
            if hand_value(player_hand) > 21:
                print("You bust! Dealer wins.")
                return
        elif choice == 's':
            break
        else:
            print("Please type 'h' or 's'.")

    # Dealer turn
    print(f"\nDealer reveals: {show_hand(dealer_hand)} (total: {hand_value(dealer_hand)})")
    while hand_value(dealer_hand) < 17:
        input("Dealer hits. Press Enter...")
        dealer_hand.append(deck.pop())
        print(f"Dealer: {show_hand(dealer_hand)} (total: {hand_value(dealer_hand)})")

    player_total = hand_value(player_hand)
    dealer_total = hand_value(dealer_hand)

    if dealer_total > 21:
        print("Dealer busts! You win.")
    elif dealer_total > player_total:
        print("Dealer wins.")
    elif dealer_total < player_total:
        print("You win!")
    else:
        print("Push (tie).")

def main():
    while True:
        play_round()
        again = input("\nPlay again? (y/n): ").strip().lower()
        if again != 'y':
            print("Thanks for playing!")
            break
        print()

if __name__ == "__main__":
    
