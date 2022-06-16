# Sudoku

## Aim:
To develop a code to solve a sudoku puzzle using contraint propagation

## Theory:

Sudoku is one of the world's most popular puzzles. It consists of a 9x9 grid, and the objective is to fill the grid with digits in such a way that each row, each column, and each of the 9 principal 3x3 subsquares contains all of the digits from 1 to 9.

The rows will be labelled by the letters A, B, C, D, E, F, G, H, I.
The columns will be labelled by the numbers 1, 2, 3, 4, 5, 6, 7, 8, 9. In the below diagram we can see the unsolved and solved puzzles with the labels for the rows and columns.

The individual squares at the intersection of rows and columns will be called boxes. These boxes will have labels 'A1', 'A2', ..., 'I9'.

The complete rows, columns, and 3x3 squares, will be called units. Thus, each unit is a set of 9 boxes, and there are 27 units in total.

For a particular box (such as 'A1'), its peers will be all other boxes that belong to a common unit (namely, those that belong to the same row, column, or 3x3 square).

## Solving Puzzle:
![image](https://user-images.githubusercontent.com/75235293/173876556-8e285374-6d02-4d6d-a39d-b561102aa1d4.png)





## Design Steps:
### step 1: 
The starting values of the unsolved board will be represented by a string of digits with a dot(.) to represent if the box is empty.
### step 2:
The board itself will be represented in the form of a dictionary with the keys to be the labels of each the box and the values to be the digit in the box.
### step 3:
create a function that takes the unsolved sudoku board and returns the same board but after applying elimination ‘once’ to all of the boxes.
### Step 4:
Check whether any box which allows only a certain digit in the unit after elimination.
### Step 5:
Repeat 3 and 4 until we get the solved puzzle.
## Program:
```
%matplotlib inline
import matplotlib.pyplot as plt
import random
import math
import sys
rows = 'ABCDEFGHI'
cols = '123456789'

def cross(a,b):
    return [s+t for s in a for t in b]
boxes= cross(rows, cols)
row_units = [cross(r,cols) for r in rows]
column_units = [cross(rows,c) for c in cols]
square_units = [cross(rs,cs) for rs in ('ABC','DEF','GHI') for cs in ('123','456','789')]

unitlist = row_units + column_units + square_units
units = dict((s,[u for u in unitlist if s in u]) for s in boxes)
peers = dict((s,set(sum(units[s],[]))-set([s])) for s in boxes)
puzzle='4.129..752..3..8...7..8...6...1.3.621.5...4.373.6.8...6...2..3...7..1..489..651.7'
def display(values):
    width=1+max(len(values[s])for s in boxes)
    line = '+'.join(['-'*(width*3)]*3)
    for r in rows:
        print(''.join(values[r+c].center(width)+('|'if c in '36' else '')for c in cols))
        if r in 'CF':print(line)
    return
def elimination(values):
    solved_values = [box for box in values.keys() if len(values[box])==1]
    for box in solved_values:
        digit = values[box]
        for peer in peers[box]:
            values[peer] = values[peer].replace(digit,'')
    return values
def only_choice(values):
    for unit in unitlist:
        for digit in '123456789':
            dplaces = [box for box in unit if digit in values[box]]
            if len(dplaces) == 1:
                values[dplaces[0]] = digit
    return values
def grid_values(grid):
    assert len(grid) == 81, "Input grid must be a string length of 81 (9x9)"
    boxes = cross(rows,cols)
    return dict(zip(boxes,grid))

def grid_values_improved(grid):
    values = []
    all_digits = '123456789'
    for c in grid:
        if c == '.':
            values.append(all_digits)
        elif c in all_digits:
                values.append(c)
    assert len(values) == 81
    boxes = cross(rows,cols)
    return dict(zip(boxes,values))
def reduce_puzzle(values):
    stalled =False
    while not stalled:
        solved_values_before = len([box for box in values.keys() if len(values[box])==1])
        elimination(values)
        only_choice(values)
        solved_values_after = len([box for box in values.keys() if len(values[box])==1])
        stalled = solved_values_after == solved_values_before
        if len([box for box in values.keys() if len(values[box])==1])==0:
            return False
    return values
def search(values):
    values_reduced = reduce_puzzle(values)
    if not values_reduced:
        return False
    else:
        values=values_reduced
    if len([box for box in values.keys() if len(values[box])==1])==81:
        return values
    
    possibility_count_list = [(len(values[box]),box) for box in values.keys() if len(values[box])>1]
    
    possibility_count_list.sort()
    for(_,t_box_min) in possibility_count_list:
        for i_digit in values[t_box_min]:
            new_values = values.copy()
            new_values[t_box_min]=i_digit
            new_values = search(new_values)
            if new_values:
                return new_values
            
    return values
sudoku=grid_values_improved(puzzle)
result = search(sudoku)
display(result)
print(result)
```

## Output:
![image](https://user-images.githubusercontent.com/75235293/173875694-fea6f8d5-4e49-4aea-9fd8-a22811bc34d9.png)

![image](https://user-images.githubusercontent.com/75235293/173875770-082590a9-b718-4cea-94d9-9ff853598c65.png)




## Result:
Thus, a program to solve sudoku puzzle using constraint propagation is implemented successfully.
