---
layout: post
title: CleaningCode4_Args_draft1
tags: [Reading]
---

>An example about parsing command line draft1.


```
	public class Args {
		private String schema;
		private String[] args;
		private boolean valid = true;
		private Set<Character> unexpectedArguments = new TreeSet<Character>();
		private Map<Character, Boolean> booleanArgs =
			new HashMap<Character, Boolean>();
		private Map<Character, String> stringArgs =
			new HashMap<Character, String>();
		private Map<Character, Integer> intArgs =
			new HashMap<Character, Integer>();	
		private Set<Character> argsFound = new HashSet<Character>();
		private int currentArgument;
		private char errorArgumentId='\0';
		private String errorParameter = "TILT";
		private ErrorCode errorCode = ErrorCode.OK;
		
		private enum ErrorCode {
			OK, MISSING_STRING, MISSING_INTEGER, INVALID_INTEGER, UNEXPECTED_ARGUMENT}
		
		public Args(String schema, String[] args) throws ParseException {
			this.schema = schema;
			this.args = args;
			valid = parse();
		}
		
		private boolean parse() throws ParseException {
			if (schema.length() == 0 && args.length == 0)
				return true;
			parseSchema();
			try {
				parseArguments();
			} catch (ArgsException e) {
			}
			return valid;
		}
		
		private boolean parseSchema() throws ParseException {
			for (String element : schema.split(",")) {
				if (element.length() > 0) {
					String trimeedElement = element.trim();
					parseSchemaElement(trimeedElement);
				}
			}
			return true;
		}
		
		private void parseSchemaElement(String element) throw ParseException {
			char elementId = element.charAt(0);
			String elementTail = element.substring(1);
			validateSchemaElementId(elementId);
			if (isBooleanSchemaElement(elementTail))
				parseBooleanSchemaElement(elementId);
			else if (isStringSchemaElement(elementTail))
				parseStringSchemaElement(elementId);
			if (isIntegerSchemaElement(elementTail)) {
				parseIntegerSchemaElement(elementId);
			} else {
				throw new ParseException(String.format("Argument: %c has invalid format: %s",
					elementId, elementTail), 0);
			}
		}
		
		private void validateSchemaElementId(char elementId) throw ParseException {
			if (!Character.isLetter(elementId)) {
				throw new ParseException(
					"Bad character:" + elementId + "in Args format: " + schema,0);
			}
		}
		
		private void parseBooleanSchemaElement(char elementId) {
			booleanArgs.put(elementId, false);
		}
		
		private void parseIntegerSchemaElement(char elementId) {
			intArgs.put(elementId, false);
		}
	
		private void parseStringSchemaElement(char elementId) {
			stringArgs.put(elementId, "");
		}
		
		private boolean isStringSchemaElement(String elementTail) {
			return elementTail.equals("*");
		}
		
				
		private boolean isBooleanSchemaElement(String elementTail) {
			return elementTail.length() == 0;
		}
		
		private boolean isIntegerSchemaElement(String elementTail) {
			return elementTail.equals("#");
		}
		
		private boolean parseArgumenets() throws ArgsException {
			for (currentArgument = 0; currentArgument < args.length; currentArgument++) {
				String arg = args[currentArgument];
				parseArgument(arg);
			}
			return true;
		}
		
		private void parseArgument(String arg) throws ArgsException {
			if (arg.startsWith("-"))
				parseElements(arg);
		}
		
		private void parseElements(String argChar) throws ArgsException {
			if (setArgument(argChar)) 
				argsFound.add(argChar);
			else {
				unexpectedArguments.add(argChar);
				errorCode = ErrorCode.UNEXPECTED_ARGUMENT;
				valid = false;
			}
		}
		
		private boolean setArgument(char argChar) throws ArgsException {
			if (isBooleanArg(argChar))
				setBooleanArg(argChar, true);
			else if (isStringArg(argChar))
				setStringArg(argChar);
			else if (isIntArg(argChar))
				setIntArg(argChar);
			else 
				return false;
			return true;
		}
		
		private boolean isIntArg(char argChar) {
			return intArgs.containKey(argChar);
		}
		
		private void setIntArg(char argChar) throws ArgsException{
			currentArgument++;
			String parameter = null;
			try {
				parameter = args[currentArgument];
				intArgs.put(argChar, new Integer(parameter));
			} catch (ArrayIndexOutOfBoundsException e) {
				valid = false;
				errorArgumentId = argChar;
				errorCode = ErrorCode.MISSING_INTEGER;
				throws new ArgsException();
			} catch (NumberFormatException e) {
				valid = false;
				errorArgumentId = argChar;
				errorParameter = parameter;
				errorCode = ErrorCode.INVALID_INTEGER;
				throw new ArgsException();
			}
		}
		
		private void setStringArg(char argChar) throws ArgsException {
			currentArgument++;
			try {
				stringArgs.put(argChar, args[currentArgument]);
			} catch (ArrayIndexOutOfBoundsException e) {
				valid = false;
				errorArgumentId = argChar;
				errorCode = ErrorCode.MISSING_STRING;
				throw new ArgsException();
			}
		}
		
		private boolean isStringArg(char argChar) {
			return stringArgs.containKey(argChar);
		}
		
		private void setBooleanArg(char argChar, boolean value) {
			booleanArgs.put(argChar, value);
		}
		
		private boolean isBooleanArg(char argChar) {
			return booleanArgs.containsKey(argChar);
		}
		
		public int cardinality() {
			return argsFound.size();
		}
		
		public String usage() {
			if (schema.length() > 0)
				return "-[" + schema + "]";
			else
				return "";
		}
		
		public String errorMessage() throws Exception {
			switch (errorCode) {
				case OK:
					throw new Exception("TILT: Should not get here.");
				case UNEXPECTED_ARGUMENT:
					return unexpectedArgumentMessage();
				case MISSING_STRING:
					return String.format("Could not find string parameter for -%c", errorArgumentId);
				case INVALID_INTEGER:
					return String.format("Could not find string integer parameter for -%c", errorArgumentId);
			}
			return "";
		}
		
		...
	}
```

	
	乐此不疲～

2014-10-24 13:41:11









































































