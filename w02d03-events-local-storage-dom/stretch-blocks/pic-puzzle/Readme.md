# Picture Puzzle

The goal of this exercise is to build a simple 3x3 [sliding picture puzzle](http://www.kirix.com/extensions/sliding-tile-picture-puzzle/)

Take any square picture you want. Cut it into 3x3 grid.

## Release 0
- Arrange 8 pieces of the image into a grid using css
- When start button is clicked, the pieces should rearrange randomly
- The pieces should slide to fill the gap when arrow keys are pressed

## Release 1
- When all the pieces are positioned correctly, alert `You have won!`
- Add a timer to the game
- Create a `Best Time` section and show the least time by any player by storing the value

## Release 2
- Change the `Best Time` section to display best 5 scores(times).
- Add a `reset` button which will erase all the best times.
- Make all the pieces of the puzzle clickable
  - move the piece when clicked
  - alert `invalid move` when no neighbour in the grid is empty
