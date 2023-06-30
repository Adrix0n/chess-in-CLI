# General information

Simple chess game wrote in C++, which uses Ocject-Oriented Programming (OOP) paradigm. To play it, compile main.cpp and run.

# Gameplay:

At the beginning, the program ask the player, if he want to load last game. There is two correct answers: '**y**' - yes and '**n**' - no. After that the figures are placed on the board, which is then shown to the player. Under the board is information, which tells whose turn is it now. To make the move, write coordinates of the figure and then write the destination (for example '**A2A4**' or '**a2a4**'). If the move is incorrect, there would be a relevant information about it. The game also ends with a relevant information (win for black/white or the stalemate).

  

# Board markings:

       # - white field
         - black field
       P - Pawn
       R - Rook
       H - Knight
       B - Bishop
       Q - Queen
       K - King
       
       wP - white pawn
       bK - black king
       ...

  
  

# Code explanation:

There is a base class '*figure*' and 6 subclasses of it (P,R,H,B,Q,K). The '*figure*' object has protected fields like position, name, color. There is one virtual class called `AllMoves()`, which in general returns all "possible" moves. Some of returned moves can be incorrect, for example if there is other figure blocking the way, but these moves are characteristic for each chess figure (for example, `Bishop.AllMoves()` returns all diagonal fields that fits on the board)

The board (in source code named 'board' or 'b') is a two dimensional array of '*figure*' class pointers. It makes moving the figures easier.

There are two one dimensional arrays in the source code: `white[16]` and `black[16]`. Both of them contains '*figure*' pointers. Because figures are statically indexed to these arrays, it is easy to find the king and check some conditions (the king is always at index 12). To get rid of the repetitions, there is also an extra array named `bw` (`fbw` in functions) which contains arrays `white[16]` and `black[16]`.

  # Functions explanation:

- `void PlaceFigures(figure **white, figure **black, figure* b[][8],bool NewGame)`
  
	Place figures from `white[]` and `black[]` arrays on the board.

- `void SaveGame(figure **white,figure **black,bool m,string history)`
  
	Saves the game in save.txt file.

- `void LoadGame(figure **white, figure **black, bool *m,figure* b[][8], string* history)`
  
    Reads the saved game from save.txt file, but does not run PlaceFigure() for its own.

- `bool MakeMove(figure **white, figure **black, bool *who, figure* b[][8], char* pos1, char* pos2, figure** fbw[])`
  
  It is the most complex function. It does the following:

    - Testing, if the given coordinates are within boundaries

    - Testing, if the player is trying to move an empty field

    - Testing, if the player is trying to move an opponent's figure

    - Testing, if move is found in figure's AllMoves() result,

    - Testing, if the way is clear (exluding the destination field), the destination field is ignored,

    - Additional move condition for pawn. If the pawn didn't move yet, it can move 2 fields upward(downward), the pawn can move diagonally only if it takes an opponent's figure.

    - Testing, if the player is trying to take his own figure,

    - Testing, if castle is possible,

    - Testing, if the king is save after the move,

    - The move,

    - Pawn promotion check,

    - En passant check
	
- `void DrawBoard(figure **white, figure **black,figure* b[][8])`
  
	Draws the board :)

  

- `bool CanReach(figure* fig,char* K,figure* b[][8])`
  
	Function which in general is used in other functions. It checks, if there is any figure on the way to the destination.

- `bool IsKingSave(figure* fig,char* pos2,bool *who, figure* b[][8], figure** fbw[])`
  
	Checks, if king would be save after the 'fig' figure move to 'pos2'. To do this, it simulate the move and checks all opponent's figures, if any of them can attack the king.

- `int IsEnd(bool *who, figure* b[][8],figure** fbw[])`
  
	Checks, if the king is under attack and if it is so, it then checks all moves for all figures for move, which could save the king.

- `bool PawnMoveAdditionalCondition(figure* fig,char* pos2, figure *b[][8])`
  
	Verifies, if the pawn move is possible. AllMoves() for pawn returns 3 front fields and a second field in the front of the pawn. For example there is a need to check if diagonal move is avaible.


- `void PawnPromotion(figure* fig, bool *who, figure** white, figure** black, figure* b[][8])`
  
	Function is called, when a pawn goes to a last possible field on the other side.
It is responsible for promoting a pawn.

  
  
# For test purposes
Below are some input series, which allows to fast travel to described game state:

- the fool's mate: F2F3 E7E6 G2G4 D8H4

- white pawn 0 on promotion field: A2A4 B7B6 A4A5 A7A6 A5B6 A6A5 B6B7 A8A6 B7C8

- black pawn 1 on promotion field; A2A4 B7B5 D2D3 B5A4 C2C4 A4A3 C4C5 A3A2 C5C6 A2B1

- castle 1: B1C3 A7A6 B2B3 B7B6 C1A3 B7B6 C7B6 C7C6 D2D4 D7D5 D1D3 E7E6 E1B1

- castle 2: G2G4 G7G5 G1F3 G8F6 F1H3 F8H6 E1G1 E8G8

- ending 1: A2A4 A7A5 B2B4 B7B5 C2C4 C7C5 D2D4 D7D5 E2E4 E7E5 F2F4 F7F5 G2G4 G7G5 H2H4 H7H5 A4B5 A5B4 A1A8 H5G4 H4G5 H8H1 A8B8 H1G1 B8C8 G1F1 E1E2 F1D1 C8D8 E8E7 D8F8 D1C1 F8G8 C1B1 G8E8 E7E8 F4E5 B1E1 E2E1 C5D4 C4D5 F5F4 B5B6 B4B3 B6B7 B3B2 E1D1 E8D8 D1C2 D8D7 C2B2 D7C7 B2C2 C7B7

- pawns go forward: A2A4 A7A5 B2B4 B7B5 C2C4 C7C5 D2D4 D7D5 E2E4 E7E5 F2F4 F7F5 G2G4 G7G5 H2H4 H7H5

- 0 pawns: A2A4 A7A5 B2B4 B7B5 C2C4 C7C5 D2D4 D7D5 E2E4 E7E5 F2F4 F7F5 G2G4 G7G5 H2H4 H7H5 A4B5 A5B4 A1A8 H5G4 H4G5 H8H1 A8B8 H1G1 B8C8 G1F1 E1E2 F1D1 C8D8 E8E7 D8F8 D1C1 F8G8 C1B1 G8E8 E7E8 F4E5 B1E1 E2E1 C5D4 C4D5 F5F4 B5B6 B4B3 B6B7 B3B2 E1D1 E8D8 D1C2 D8D7 C2B2 D7C7 B2C2 C7B7 C2D3 B7C7 D3D4 C7D7 D5D6 D7E6 D4D3 E6E5 G5G6 E5D6 D3D4 G4G3 E4E5 D6E6 D4E4 F4F3 E4F3 E6E5 F3G3 E5F5 G3H3 F5G6 H3H4

- En passant: A2A4 A7A5 B2B4 B7B6 B4B5 C7C5 (B5C6)
