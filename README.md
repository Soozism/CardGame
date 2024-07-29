# Card Game in Go

This project provides a simple card game implementation in Go, designed to demonstrate various Go features such as structs, methods, file operations, and unit testing. The project consists of three main files:

- `main.go`: The entry point of the application.
- `deck.go`: Contains the logic for deck manipulation.
- `deck_test.go`: Contains tests for deck functionality.

## Overview

The project creates a standard deck of cards, allows for shuffling and printing the deck, and includes functionality to save and load the deck from a file. It also features unit tests to verify the correctness of deck operations.

## Files and Functions

### `main.go`

The `main.go` file is the entry point for the application. It demonstrates how to create a deck, shuffle it, and print it.

**Code:**

```go
package main

func main() {
	cards := newDeck()
	cards.shuffle()
	cards.print()
}
```

**Function Explanation:**

- **`main`**:
  - **`newDeck()`**: Calls the `newDeck` function from `deck.go` to create a new deck of cards.
  - **`cards.shuffle()`**: Shuffles the deck using the `shuffle` method defined in `deck.go`.
  - **`cards.print()`**: Prints the shuffled deck using the `print` method.

### `deck.go`

The `deck.go` file contains the implementation for deck operations. It defines the `deck` type and associated methods for manipulating decks of cards.

**Code:**

```go
package main

import (
	"fmt"
	"io/ioutil"
	"math/rand"
	"os"
	"strings"
	"time"
)

type deck []string

func newDeck() deck {
	cards := deck{}
	cardSuits := []string{"Spades", "Diamonds", "Hearts", "Clubs"}
	cardValues := []string{"Ace", "Two", "Three", "Four"}

	for _, suit := range cardSuits {
		for _, value := range cardValues {
			cards = append(cards, value+" of "+suit)
		}
	}

	return cards
}

func (d deck) print() {
	for i, card := range d {
		fmt.Println(i, card)
	}
}

func deal(d deck, handSize int) (deck, deck) {
	return d[:handSize], d[handSize:]
}

func (d deck) toString() string {
	return strings.Join([]string(d), ",")
}

func (d deck) saveToFile(filename string) error {
	return ioutil.WriteFile(filename, []byte(d.toString()), 0666)
}

func newDeckFromFile(filename string) deck {
	bs, err := ioutil.ReadFile(filename)
	if err != nil {
		fmt.Println("Error: ", err)
		os.Exit(1)
	}
	return deck(strings.Split(string(bs), ","))
}

func (d deck) shuffle() {
	source := rand.NewSource(time.Now().UnixNano())
	r := rand.New(source)

	for i := range d {
		np := r.Intn(len(d) - 1)
		d[i], d[np] = d[np], d[i]
	}
}
```

**Function and Method Explanations:**

- **`newDeck`**:
  - **Purpose**: Creates and returns a new deck of 16 cards.
  - **Details**: Constructs a deck with four suits ("Spades", "Diamonds", "Hearts", "Clubs") and four values ("Ace", "Two", "Three", "Four").

- **`(d deck) print`**:
  - **Purpose**: Prints each card in the deck along with its index.
  - **Details**: Iterates over the deck and prints each card's index and name.

- **`deal(d deck, handSize int) (deck, deck)`**:
  - **Purpose**: Deals a hand of a specified size from the deck.
  - **Details**: Returns two decks: the hand of cards and the remaining cards.

- **`(d deck) toString`**:
  - **Purpose**: Converts the deck into a comma-separated string representation.
  - **Details**: Joins all card strings with commas.

- **`(d deck) saveToFile(filename string) error`**:
  - **Purpose**: Saves the deck to a file.
  - **Details**: Writes the comma-separated string representation of the deck to the specified file.

- **`newDeckFromFile(filename string) deck`**:
  - **Purpose**: Loads a deck from a file.
  - **Details**: Reads the file, splits the comma-separated string, and returns a deck.

- **`(d deck) shuffle`**:
  - **Purpose**: Randomly shuffles the cards in the deck.
  - **Details**: Uses the Fisher-Yates shuffle algorithm with a random source based on the current time.

### `deck_test.go`

The `deck_test.go` file contains unit tests to ensure the correctness of the deck operations.

**Code:**

```go
package main

import (
	"os"
	"testing"
)

func TestNewDeck(t *testing.T) {
	d := newDeck()

	if len(d) != 16 {
		t.Errorf("Expected deck length of 16, but got %v", len(d))
	}

	if d[0] != "Ace of Spades" {
		t.Errorf("Expected first card of Ace of Spades, but got %v", d[0])
	}

	if d[len(d)-1] != "Four of Clubs" {
		t.Errorf("Expected last card of Four of Clubs, but got %v", d[len(d)-1])
	}
}

func TestSaveToDeckAndNewDeckFromFile(t *testing.T) {
	os.Remove("_decktesting")

	deck := newDeck()
	deck.saveToFile("_decktesting")

	loadedDeck := newDeckFromFile("_decktesting")

	if len(loadedDeck) != 16 {
		t.Errorf("Expected 16 cards in deck, got %v", len(loadedDeck))
	}

	os.Remove("_decktesting")
}
```

**Function Explanations:**

- **`TestNewDeck(t *testing.T)`**:
  - **Purpose**: Tests the `newDeck` function to verify that a new deck has the correct number of cards and the correct first and last cards.
  - **Details**: Asserts that the deck length is 16, and checks that the first card is "Ace of Spades" and the last card is "Four of Clubs".

- **`TestSaveToDeckAndNewDeckFromFile(t *testing.T)`**:
  - **Purpose**: Tests the functionality of saving a deck to a file and then loading it back.
  - **Details**: Saves a new deck to a temporary file and then loads it from the file. Verifies that the loaded deck has the expected number of cards. Cleans up the temporary file afterward.

## Running the Program

1. Save the provided code files (`main.go`, `deck.go`, `deck_test.go`) in the same directory.
2. Open a terminal and navigate to the directory containing these files.
3. Run the following command to execute the program:

   ```bash
   go run main.go
   ```

## Running Tests

To ensure the correctness of the deck functions, run the tests using:

```bash
go test
```

## Notes

- The deck is initialized with 16 cards, consisting of 4 suits and 4 values.
- The `shuffle` method randomizes the order of cards using the Fisher-Yates algorithm.
- The deck can be saved to and loaded from a file to demonstrate data persistence.

## Contributing

Contributions are welcome! Please submit a pull request or open an issue if you have any suggestions or improvements.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
