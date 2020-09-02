

# Requirements :

* The program should support the standard rules of chess.
* The program should support two human players. The program will not provide an AI.
* The program should provide a text-based interface :
	* The program should render the game board in plain text.
	* Players should express their moves by entering numbers representing locations on the chessboard.



# Subsystems :


Subsystem Name		Instances	Functionality		Interfaces		Interfaces 	
								 Exported		 Consumed	




GamePlay		    1		Starts game		Game Over		Take Turn (on Player)								Controls game flow				Draw (on ChessBoardView)
					Controls drawing
					Declares winner
					Ends game


ChessBoard		    1		Stores chess pieces.	Get Piece At 		Game Over (on GamePlay)
					Checks for ties and 	Set Piece At
					checkmates.


ChessBoardView		    1		Draw the associated	Draw			Draw (on ChessBoardView)
					ChessBoard


ChessPiece		   32		Moves itself		Move			Get Piece At (on ChessBoard)
					Checks for legal	Check Move		Set Piece At (on ChessBoard)
					moves


ChessPieceView		   32		Draws the associated	Draw			None
					ChessPiece


Player			    2		Interacts with the	Take Turn		Get Piece At (on ChessBoard)
					user by prompting				Move (on ChessBoard)
					the user for a move				Check Move (on ChessPiece)
					and obtaining the 
					user's move
					Moves pieces


ErrorLogger		    1		Writes errorr		Log Error		None				
					messages to a log file





# Decide threads :

* Multy-Threaded Program
* Single-Threaded Program




# Specify Class Hierarchies for each Subsystem :


1. ChessPiece :
	* Rook
	* Bishop
	* Knight
	* King
	* Pawn
	* Queen


2. ChessBoardView :
	* ChessBoardViewConsole
	* ChessBoardViewGUI2D
	* ChessBoardViewGUI3D

etc.




# Classes, Data Structures, Algorithms and Patterns for each Subsystem :



Subsystem		Classes			Data Structures		Algorithms		Patterns



GamePlay	     GamePlay class		GamePlay object		Gives each player	None
						includes one 		a turn to play
						ChessBoard object
						and two Player
						objects


ChessBoard	    ChessBoard class		ChessBoard objects	Checks for a win	None
						store a 2D rep. of	or tie after each
						32 ChessPieces		move


ChessBoardView	   ChessBoardView class		Stores information	Draws a chessboard	Observer
		   abstract base class		on how to draw a
		   Concrete derived classes	chessboard.
		   ChessBoardViewConsole
		   ChessBoardViewGUI2D and
		   so on. 


ChessPiece	   ChessPiece abstract base	Each piece stores its	Piece checks for a	None 
		   class, Rook, Bishop,		location on the 	legal move by querying
		   Knight, King, Pawn and 	chessboard.		the chessboard for
		   Queen derived classes.				pieces at various
									locations.



ChessPieceView    ChessPieceView abstract	Stores info on how	Draws a ches piece	Observer
		  base class, Derrived classes	to draw a chess piece
		  RookView, BishopView, and so
		  on, and concrete derived
		  classes RookViewConsole,
		  RookViewGUI2D, and so on.



Player 		   Player abstract base class,	None			Prompts the user for	Mediator
		   Concrete derived classes,				a move, checks if move
		   PlayerConcole, PlayerGUI2D,				is legal and moves
		   and so on.						piece



ErrorLogger	   One ErrorLogger class	A queue of messages	Buffers messages and	Dependency
						to the log		writes them to a log	injection
									file

* NOTE : This section of the design document would normally present the actual interfaces for each class.





# Specify Error Handeling for each Subsystem :


The error handeling should include both system errors, such as memory allocation failure, and user errors, such as invalid entries.




Subsystem		Handeling System Errors			Handeling User Errors




GamePlay		Logs an error with the			Not applicable (no direct user
			ErrorLogger, shows a			interface)
			message to the user,
			shuts down program if
			unable to allocate
			memory for ChessBoard or
			Players.


ChessBoard		Log an error with the			Not applicable (no direct user 
ChessPiece		ErrorLogger and throw			interface)
		 	exceptions if unable to
			allocate memory.


ChessBoardView		Logs an error with the			Not applicable (no direct user 
ChessPieceView		ErrorLogger and throws an		interface)
			exception if something goes
			wrong during rendering.


Player 			Logs an error with the			Sanity-checks an user entry to 
			ErrorLogger and throws an		ensure that it is not off the board;
			exception if unable to			it then prompts the user for another 
			allocate memory.			entry. This subsystem checks each move's
								legality before moving the piece; if illegal,
								it prompts the user for another move.



ErrorLogger		Attempts to log an error,		Not applicable (no direct user
			informs the user, shuts down		interface)
			the program if unable to allocate
			memory







.
