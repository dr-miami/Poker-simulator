# 5-Card Draw Poker Engine (Windows)

A terminal-based 5-Card Draw Poker game written in C++ for Windows. This version is intentionally Windows-only and targets the native console APIs used for keyboard input and screen clearing.

---

## Features

- Full 5-Card Draw flow — opening deal → hold/discard phase → CPU draw → showdown
- Interactive hold system — toggle cards to keep by number, with live HOLD indicators in the terminal
- CPU AI opponent — holds pairs or better, flush draws, and falls back to highest card
- Economy system — persistent balance across rounds, charity mode when broke
- Multiplied payouts — winnings scale with hand strength (1x for a pair up to 10x for a Royal Flush)
- Audio hooks — background music, jackpot sound on win, fart sound on loss (via miniaudio)
- ANSI color UI — colored cards, bold headers, arrow-key menu navigation

---

## Project Structure

```
.
├── main5card.cpp          # Game loop, draw phase, showdown, audio
├── functions5card.hpp     # Card/Deck types, hand evaluator, CPU AI, helpers
├── miniaudio.h            # Single-header audio library (miniaudio.io)
├── sound.wav              # Background music
├── jackpot.wav            # Win sound effect
├── fart.wav               # Loss sound effect
└── .files/
    ├── rules.txt          # Shown in "Rules" menu option
    └── points.txt         # Shown in "Hand Rankings" menu option
```

---

## Requirements

| Requirement | Notes |
|---|---|
| Windows 10/11 | Uses Win32 console APIs and `_getch()` |
| C++17 or later | Uses std::set, structured bindings, inline globals |
| A Windows C++ compiler | MSVC or MinGW-w64 |
| miniaudio.h | Single-header, no install needed — just drop it in |
| Optional: sound files | WAV, MP3, or FLAC supported via miniaudio |

---

## Building

With Microsoft Visual Studio or MinGW:

```bat
cl /std:c++17 /EHsc src\main.cpp /Fe:poker.exe
```

or:

```bash
g++ -std=c++17 src\main.cpp -o poker.exe
```

This project intentionally fails to build on non-Windows platforms.

---

## How to Play

1. Launch `./poker5card`
2. Navigate the menu with up/down arrow keys, confirm with ENTER
3. Enter your bet when prompted
4. You are dealt 5 cards
5. Type the numbers of cards you want to HOLD (e.g. `1 3 5`), then press ENTER
   - You can toggle cards on/off multiple times before confirming
   - Press ENTER on an empty line to confirm your selection and draw
6. The CPU draws its replacement cards
7. Hands are revealed and compared — best hand wins

---

## Hand Rankings & Payouts

| Hand | Multiplier |
|---|---|
| High Card | 1x |
| One Pair | 2x |
| Two Pair | 3x |
| Three of a Kind | 4x |
| Straight | 5x |
| Flush | 6x |
| Full House | 7x |
| Four of a Kind | 8x |
| Straight Flush | 9x |
| Royal Flush | 10x |

Winnings are calculated as `bet × hand multiplier`. On a loss, you forfeit the bet. On a draw, the bet is returned.

---

## CPU AI Behaviour

The CPU uses a simple rule-based strategy:

1. Holds any cards that are part of a pair or better (pairs, trips, quads, full houses)
2. Holds the highest card if no pairs are found
3. Prefers flush draws (3+ cards of the same suit) when nothing else qualifies
4. Discards everything else and draws replacements

This is intentionally beatable — it doesn't bluff, and it doesn't evaluate straight draws.

---

## Differences from the Texas Hold'em Version

| | Texas Hold'em | 5-Card Draw |
|---|---|---|
| Community cards | 3–5 shared cards (flop/turn/river) | None |
| Hand size | 2 hole + 5 table = best 5 of 7 | Exactly 5 cards |
| Draw phase | No | Yes — player and CPU both draw |
| Evaluator input | 7 cards → best 5 | 5 cards exactly |
| Ace-low straight | Handled implicitly | Explicit A-2-3-4-5 check |
| CPU behaviour | Hidden hand reveal only | Active draw decision |

---

## Customising

- **Starting balance** — edit `inline int playerBalance = 1000;` in `functions5card.hpp`.
- **Charity amount** — when broke, the player receives $200 by default. Change the value in `placeBet()`.
- **CPU difficulty** — the `cpuDecide()` function in `functions5card.hpp` returns a `vector<bool>` of which cards to keep. You can extend it with straight-draw detection or probabilistic bluffing.
- **Sound files** — replace `sound.wav`, `jackpot.wav`, and `fart.wav` with any WAV/MP3/FLAC files. miniaudio supports them with no extra dependencies.
- **Rules / rankings text** — edit `.files/rules.txt` and `.files/points.txt` to change what's shown in the menu.

---

## Porting Notes

This project is now intentionally Windows-only and uses the original Windows console approach:

- `<conio.h>` and `_getch()` for raw keyboard input
- `system("cls")` for screen clearing
- Win32 console APIs when needed

Non-Windows builds are explicitly rejected at compile time.

---

## License

Do whatever you want with it.