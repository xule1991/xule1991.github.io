---
layout: post
title: CleaningCode1
tags: [Reading]
---

>Make camp cleaner then it looks like when we came

Something about name

Code on Mine Clearance
sometimes we need to make code easier for reading, that will help a lot of maintainess.
```
Original code:

	public List<int[]> getThem() {
		List<int[]> list1 = new ArrayList<int[]>();
		for(int[] x : theList) 
			if(x[0] == 4)
				list1.add(x);
		return list1;
	}

change to:

	public List<int[]> getFlaggedCells() {
		for(int[] cell : gameBoard)
			if(cell[STATUS_VALUE] == FLAGGED)
				flaggedCells.add(cell);
		return flaggedCells;
	}
	
make it even better:

	public List<Cell> getFlaggedCells() {
		List<Cell> flaggedCells = new ArrayList<Cell>();
		for(Cell cell : gameBoard)
			if(cell.isFlagged()) 
				flaggedCells.add(cell);
		return flaggedCells;
	}
```

The meanings of some variables are not clear

```
Original code:

	private void printGuessStatistics(char candidate, int count) {
		String number;
		String verb;
		String pluralModifier;
		if (count == 0) {
			number = "no";
			verb = "are";
			pluralModifier = "s";
		} else if(count == 1){
			number = "1";
			verb = "is";
			pluralModifier = "";
		} else {
			number = Integer.toString(count);
			verb = "are";
			pluralModifier = "s";
		}
		String guessMessage = String.format(
			"There %s %s %s"%s", verb, number, candidate, pluralModifier
		);
		print(guessMessage);
	}
	
Change to:

	public class GuessStatisticsMessage {
		private String number;
		private String verb;
		private String pluralModifier;
		
		createPluralDependentMessageParts(count);
		return String.format(
			"There %s %s %s%s",
				verb, number, candidate, pluralModifier);
	}
	
	private void createPluralDependentMessageParts(int count) {
		if(count == 0) {
			thereAreNoLetters();
		} else if (count == 1) {
			thereIsOneLetter();
		} else {
			thereAreManyLetters(count);
		}
	}
	
	private void thereAreManyLetters(int count) {
		number = Integer.toString(count);
		verb = "are";
		pluralModifier = "s";
	}
	
	private void thereIsOneLetter() {
		number = "1";
		verb = "is";
		pluralModifier = "";
	}
	
	private void thereAreNoLetters() {
		number = "no";
		verb = "are";
		pluralModifier = "s";
	}
```


	乐此不疲～

2014-10-17 10:00:11









































































