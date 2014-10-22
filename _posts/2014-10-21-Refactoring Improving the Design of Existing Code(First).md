---
layout: post
title: Refactoring:ImprovingTheDesignOfExistingCode1
tags: [Reading]
---

>Refactor method used:
>Extract Method, Move Method, Replace Conditional with Polymorphism, 
>Self Encapsulate Field, Replace Type Code with State/Strategy,etc.
>All this refacting methods make assigning responsibilities more reasonaly,
>maintaining more easily.
>local variables will make extracting method difficult, and there are several ways to make this process works.

	1.Refactor means change the code structure without changing the function, and it will
not change the visible behavior of the existing software.

	2. There are two things you need to do when coding, add new features and refactor.when 
you add new features, you should not change the existing code,just add new features and make
sure that the features you added can pass all the tests.when you refactor the code, you should 
not add new features, just refine the structure,at this moment, you should not add any new test.
	
	3. Programs have two values, one is what it can do today, the other is what it can do tomorrow
	
	4. The values of indirect layers:
(1). Sharing the logic: For example, if a function is called in two places, or the function in a super
class is shared by two child class;


	5. Deprecated: When you want to change an interface, you need to retain the old interface,and let 
the new interface to implement the old one, and maintain both.

			The codes' bad smell
			
	1. Duplicated Code:
		Extract Method;
		Pull Up Method;
		Form Template Method to get a Template Method pattern;
		
	2. Long Method:
		Extract Method;
		Replace Temp with Query;
		Introduce Parameter Object;
		Preserve Whole Object;
		Decompose Conditional;
	3. Large Class:
		Extract Class;
		Extract Subclass;
	4. Long Parameter List:
		Replace Parameter with Method;
		Preserve Whole Object;
		Introduce Parameter Object;
	5. Switch statement:
		Use polimorphism instead of switch statement;
		
		
		
		
		
		
	Extract Method:
Change this code
 
```	
	void printOwing(double amount) {
		printBanner();
		
		//print details
		System.out.println("name: " + _name);
		System.out.println("amount: " + amount);
	}
```
to this

```	
	void printOwing(double amount) {
		printBanner();
		printDetails(amount);
	}
	
	void printDetails (double amount) {
		System.out.println("name: " + _name);
		System.out.println("amount: " + amount);
	}
```
Motivation:
	If there is a long method or a method which needs a comment to let people get across,it is better to extract
it to a method,there are several benefits like follows:
	1. The method will be in small granularity,and has more possibility to be reused;
	2. Make higher level method just like several lines of comments, make code easier to read;

Another thing need to be considered is whether the method has local variable:

1. method without local variable:

```	
	void printOwing() {
		Enumeration e = _orders.elements();
		double outstanding = 0.0;
		
		//print banner
		System.out.println("***********************");
		System.out.println("**** Customer Owes ****");
		System.out.println("***********************");
		
		//calculate outstanding
		while (e.hasMoreElements()) {
			Order each = (Order) e.nextElement();
			outstanding += each.getAmount();
		}
		
		// print details
		System.out.println("names: " + _name);
		System.out.println("amount" + outstanding);
	}
```

change to 

```	
	void printOwing() {
		Enumeration e = _orders.elements();
		double outstanding = 0.0;
		
		printBanner();
		
		//calculate outstanding
		while (e.hasMoreElements()) {
			Order each = (Order) e.nextElement();
			outstanding += each.getAmount();
		}
		
		// print details
		System.out.println("names: " + _name);
		System.out.println("amount" + outstanding);
	}
	
	void printBanner() {
		//print banner
		System.out.println("***********************");
		System.out.println("**** Customer Owes ****");
		System.out.println("***********************");
	}
```

With local variable

```	
	void printOwing() {
		Enumeration e = _orders.elements();
		double outstanding = 0.0;
		
		printBanner();
		
		//calculate outstanding
		while (e.hasMoreElements()) {
			Order each = (Order) e.nextElement();
			outstanding += each.getAmount();
		}
		
		printDetails(outstanding);
	}
	
	void printDetails(double outstanding) {
		// print details
		System.out.println("names: " + _name);
		System.out.println("amount" + outstanding);
	}
```

Extract calculation

```	
	void printOwing() {
		printBanner();
		double outstanding = getOutstanding();
		printDetails(outstanding);
	}
	
	double getOutstanding() {
		Enumeration e = _orders.elements();
		double result = 0.0;
		//calculate outstanding
		while (e.hasMoreElements()) {
			Order each = (Order) e.nextElement();
			result += each.getAmount();
		}
		return result;
	}
```

The original code is like this:
```	
void printOwing() {
		Enumeration e = _orders.elements();
		double outstanding = 0.0;
		
		//print banner
		System.out.println("***********************");
		System.out.println("**** Customer Owes ****");
		System.out.println("***********************");
		
		//calculate outstanding
		while (e.hasMoreElements()) {
			Order each = (Order) e.nextElement();
			outstanding += each.getAmount();
		}
		
		// print details
		System.out.println("names: " + _name);
		System.out.println("amount" + outstanding);
	}
```


After Extraction:


```	
	void printOwing() {
		printBanner();
		double outstanding = getOutstanding();
		printDetails(outstanding);
	}
	
	void printBanner() {
		//print banner
		System.out.println("***********************");
		System.out.println("**** Customer Owes ****");
		System.out.println("***********************");
	}
	
	double getOutstanding() {
		Enumeration e = _orders.elements();
		double result = 0.0;
		//calculate outstanding
		while (e.hasMoreElements()) {
			Order each = (Order) e.nextElement();
			result += each.getAmount();
		}
		return result;
	}
	
	void printDetails(double outstanding) {
		// print details
		System.out.println("names: " + _name);
		System.out.println("amount" + outstanding);
	}

```

Inline Method:

change this kind of code:

```	
	int getRating() {
		return (moreThanFileLateDeliveries()) ? 2 : 1;
	}
	
	boolean moreThanFileLateDeliveries() {
		return _numberOfLateDeliveries > 5;
	}
```

to:

```	
int getRating() {
		return (_numberOfLateDeliveries > 5) ? 2 : 1;
	}
```

Inline Temp:

if you have a temp just assigned once,like below:
```	
	double basePrice = anOrder.basePrice();
	return (basePrice > 1000);
```

to:

```
	return (anOrder.basePrice() > 1000);
```

Replace Temp with Query

```	
double basePrice = _quantity * _itemPrice;
	if (basePrice > 1000) 
		return basePrice * 0.95;
	else 
		return basePrice * 0.98;
```

to

```	
	if (basePrice() > 1000) 
		return basePrice * 0.95;
	else 
		return basePrice * 0.98;
		
	...
	double basePrice() { // The query
		return _quantity * _itemPrice;
	}
```

Local usually make it hard to extract method, so it is a common usage to make local variables to queries.

Example:

```	
	double getPrice() {
		int basePrice = _quantity * _itemPrice;
		double discountFactor;
		if (basePrice > 1000) 
			discountFactor = 0.95;
		else
			discountFactor = 0.98;
		return basePrice * discountFactor;
	}
```

We want to change all these two local variable;
first, we try to make them final, to find whether they just be assigned once.
```	
	double getPrice() {
		final int basePrice = _quantity * _itemPrice;
		final double discountFactor;
		if (basePrice > 1000) 
			discountFactor = 0.95;
		else
			discountFactor = 0.98;
		return basePrice * discountFactor;
	}
```

then extract local variable to query:
 
```	
	double getPrice() {
		return basePrice() * discountFactor();
	}
	
	private double discountFactor() {
		if (basePrice() > 1000) 
			return 0.95;
		else
			return 0.98;
	}
	
	private int basePrice() {
		return _quantity * _itemPrice;
	}
```

Introduce Explaining Variable

change 

```	
	if ((platform.toUpperCase().indexOf("MAC") > -1) &&
	(brower.toUpperCase().indexOf("IE") > -1) &&
	wasInitialized() && resize > 0) {
		
		//do something
	}
```

to 

```	
	final boolean isMacOs = platform.toUpperCase().indexOf("MAC") > -1;
	final boolean isIEBrowser = browser.toUpperCase().indexOf("IE") > -1;
	final boolean wasResized = resize > 0;
	
	if (isMacOs && isIEBrowser && wasResized) {
		//do something
	}
	
```

Motivation:
	expression sometimes will be very complicated and hard to read,in this case, local variable will help you
to convert expression to the format which is easier to maintain.

Split Temporary Variable

```	
	double temp = 2 * (_height * _width);
	System.out.println(temp);
	temp = _height * _width;
	System.out.println(temp);
```

to

```
	final double perimeter = 2 * (_height * _width);
	System.out.println(perimeter);
	final double area = _height * _width;
	System.out.println(area);	
```

Motivation:
	
Remove Assignments to Parameters

change 

```	
int discount (int inputVal, int quantity, int yearToDate) {
	if (inputVal > 50) inputVal -= 2;
}
```

to

```	
int discount (int inputVal, int quantity, int yearToDate) {
	int result = inputVal;
	if (inputVal > 50) result -= 2;
}
```

Motivation


```	
	void aMethod(Object foo) {
		foo.modifyInSomeWay();		// that's OK
		foo = anotherObject;		// trouble and despair will follow you
	}
```

Replace Method with Method Object
scenario: If you have a very long method, and the local variables annoys you, you can create An object and 
let the fields in that object to represent those local variables.

```	
class Order ...
	double price() {
		double primaryBasePrice;
		double secondaryBasePrice;
		double tertiaryBasePrice;
		//long computation;
		...
	}
```

you can create a new object to make this kind of method to 

```	
	Class PriceCalculator {}
	double primaryBasePrice;
	double secondaryBasePrice;
	double tertiaryBasePrice;
	double compute();
	...
```

Substitute Algorithm

```	
	String foundPerson(String[] people) {
		for(int i = 0; i < people.length; i++) {
			if (people[i].equals("Don")) {
				return "Don";
			}
			if (people[i].equals("John")) {
				return "John";
			}
			if (people[i].equals("Kent")) {
				return "Kent";
			}
		}
		return null;
	}
```

change to

```	
	String foundPerson(String[] people) {
		List candidates = Arrays.asList(new String[] {"Don", "John", "Kent"});
		for (int i = 0; i < people.length; i++)
			if (candidates.contains(people[i]))
				return people[i];
		return "";
	}
```

Extract Class is a way to impove concurrency,because you can add locks to both classes but not the whole class.


Change Value to Reference

Replace Constructor with Factory Method,
Example:

```	
	class Customer {
		public Customer(String name) {
			_name = name;
		}
		
		public String getname() {
			return _name;
		}
		
		private final String _name;
	}
	
	class Order...
		public Order(String customerName) {
			_customer = new Customer(customerName);
		}
		public void setCustomer(String customerName) {
			_customer = new Customer(customerName);
		}
		pbulic String getCustomerName() {
			return _customer.getName();
		}
		private Customer _customer;
		
		there are somewhere else which will also use this method:
		
	private static int numberOfOrdersFor(Collection orders, String customer) {
		int result = 0;
		Iterator iter = orders.iterator();
		while (iter.hasNext()) {
			Order each = (Order) iter.next();
			if(each.getCustomerName.equals(customer))
				result++;
		}
		return result;
	}
```

Currently, Customer is Value Object, even if several orders have a same customer,they have their own copies.
So, we change the code like below(factory method):

```	
	class Customer {
		private static Dictionary _instance = new Hashtable();
		static void loadCustomers() {
			new Customer("Lemon Car Hire").store();
			new Customer("Associated Coffee Machines").store();
			new Customer("Bilston Gasworks").store();
		}
		private void store() {
			_instances.put(this.getName(), this);
		}
		public static Customer create (String name) {
			return new Customer(name);	
		}
		private Customer (String name) {
			_name = name;
		}
		
		public static Customer getnamed(String name) {
			return (Customer) _instances.get(name);
		}
	}
	
	class Order {
		public Order (String customer) {
			_customer = Customer.create(customer);
		}
	}
```

Duplicate Observed Data

Initial code

```	
public class IntervalWindow extends Frame ... 
		java.awt.TextField _startField;
		java.awt.TextField _endField;
		java.awt.TextField _lengthField;
		
	class SymFocus extends java.awt.event.FocusAdapter {
		public void focusLost(java.awt.event.FocusEvent event) {
			Object object = event.getSource();
				if(object == _startField)
					StartField_FocusLost(event);
				else if(object == _endField)
					EndField_FocusLost(event);
				else if(object == _lengthField)
					LengthField_FocusLost(event);
		}
	}
	...
	
	void StartField_FocusLost(java.awt.event.FocusEvent event) {
		if (isNotInteger(_startField.getText()))
			_startField.setText("0");
		calculateLength();
	}
	
	void EndField_FocusLost(java.awt.event.FocusEvent event) {
		if (isNotInteger(_endField.getText()))
			_endField.setText("0");
		calculateLength();
	}
	
	void LengthField_FocusLost(java.awt.event.FocusEvent event) {
		if (isNotInteger(_lengthField.getText()))
			_lengthField.setText("0");
		calculateEnd();
	}
	...
	
	void calculateLength() {
		try{
			int start = Integer.parseInt(_startField.getText());
			int end = Integer.parseInt(_endField.getText());
			int length = end - start;
			_lengthField.setText(String.valueOf(length));
			} catch(NumberFormatException e) {
			throw new RuntimeException("Unexpected Number Format Error");
		}
	}
	
	void calculateEnd() {
		try{
			int start = Integer.parseInt(_startField.getText());
			int length = Integer.parseInt(_lengthField.getText());
			int end = start + length;
			_endField.setText(String.valueOf(end));
			} catch(NumberFormatException e) {
			throw new RuntimeException("Unexpected Number Format Error");
		}
	}
	
```

What we need to do is to extract the calculate logic from GUI,which means we need to move calculateLength() and 
calculateEnd() to an independent class,to achieve this,we need to get the value of Start, End and Length without
a reference of IntervalWindow.
The only way to do this is to copy these fields to field object and make the two value synchronized.
This what Duplicate Observed Data do.

Please refer to Observer/Observable pattern for resolution.

Change Unidirectional Association to Bidirectional and 
Change Bidirectional Association to Unidirectional	
							 
```	
	class Order ..
	Customer getCustomer() {
		return _customer;
	}
	void setCustomer(Customer arg) {
		_customer = arg;
	}
	void setCustomer(Customer arg) {
		if (_customer != null)
			_customer.friendOrders().remove(this);
			_customer = arg;
			if (_customer != null)
			_customer.friendOrders().add(this);
	}
	Customer _customer;
	
	class Customer {
		private Set _orders = new HashSet();
		Set friendOrders() {
			/** should only be used by Order when modifying the association*/
			return _orders;
		}
	}
```

Replace Type Code with Class

```	
	class Person {
		public static final int O = 0;
		public static final int A = 1;
		public static final int B = 2;
		public static final int AB = 3;
		
		private int _bloodGroup;
		
		public Person(int bloodGroup){
			_bloodGroup = bloodGroup;
		}
		public void setBloodGroup(int arg) {
			_bloodGroup = arg;
		}
		public int getBloodGroup() {
			return _bloodGroup;
		}
		
	}
```

change to

```	
class BloodGroup {
	public static final BloodGroup O = new BloodGroup(0);
	public static final BloodGroup A = new BloodGroup(1);
	public static final BloodGroup B = new BloodGroup(2);
	public static final BloodGroup AB = new BloodGroup(3);
	
	private final int _code;
	
	private BloodGroup(int code) {
		_code = code;
	}
	
	public int getCode() {
		return _code;
	}
	
	public static BloodGroup code(int arg) {
		return _values[arg];
	}

}

class Person {
		public static final int O = BloodGroup.O.getCode();
		public static final int A = BloodGroup.A.getCode();
		public static final int B = BloodGroup.B.getCode();
		public static final int AB = BloodGroup.AB.getCode();
		
		private int _bloodGroup;
		
		public Person(int bloodGroup){
			_bloodGroup = bloodGroup;
		}
		public void setBloodGroup(int arg) {
			_bloodGroup = arg;
		}
		public int getBloodGroup() {
			return _bloodGroup;
		}
		
	}
```

Replace Type Code with Subclassed

```	
	class Employee..
		private int _type;
		static final int ENGINEER = 0;
		static final int ENGINEER = 1;
		static final int ENGINEER = 2;
		
		static Employee create(int type) {
			return new Employee(type);
		}
		private Employee (int type) {
			_type = type;
		}
		
		int getType() {
			return _type;
		}
		
		
	...
	
	abstract int getType();
	
	static Employee create(int type) {
		switch(type) {
			case ENGINEER:
				return new Engineer();
			case SALESMAN:
				return new Salesman();
			case MANAGER:
				return new Manager();
			default:
				throw new IllegalArgumentException("Incorrect type code value");
		}
	}
```


Chapter 9: Decompose Conditional 

```	
change:

	if (date.before(SUMMER_START) || date.after(SUMMER_END))
		charge = quantity * _winterRate + _winterServiceCharge;
	else charge = quantity * _summerRate;

to:

	if(notSummer(date)) 
		charge = winterCharge(quantity);
	else charge = summerCharge(quantity);

	private boolean notSummer(Date date) {
		return date.before(SUMMER_START) || date.after(SUMMER_END);
	}
	
	private double summerCharge(int quantity) {
		return quantity * _summerRate;
	}
	
	private double winterCharge(int quantity) {
		return quantity * _winterRate + _winterServiceCharge;
	}
```

use &&

```	
change:

if(onVacation()) {
	if(lengthOfService() > 10)
		return 1;
return 0.5;
}
to:

if(onVacation() && lengthOfService() > 10) return 1;
else return 0.5;

to:

return (onVacation() && lengthOfService() > 10) ? 1 : 0.5;
```

same code in two branches:

```	
change:

if(isSpecialDeal()) {
	total = price * 0.95;
	send();
}else {
	total = price * 0.98;
	send();
}
to:

if(isSpecialDeal()) {
	total = price * 0.95;
}else {
	total = price * 0.98;
}
send();
```
It has the same idea as finally clause.


Use return to return tag

```	
change:

	void checkSecurity(String[] people) {
		String found = "";
		for(int i = 0; i < people.length; i++) {
			if(found.equals("")) {
				if (people[i].equals("Don")) {
					sendAlert();
					found = "Don";
				}
				if (people[i].equals("John")) {
					sendAlert();
					found = "Don";
				}				
			}
		}
	}
to:

void checkSecurity(String[] people) {
	String found = foundMiscreant(people);
	someLaterCode(found);
}
String foundMiscreant(String[] people) {
	String found = "";
	for(int i = 0; i < people.length; i++) {
		if(found.equals("")) {
			if (people[i].equals("Don")) {
				sendAlert();
				return "Don";
			}
			if (people[i].equals("John")) {
				sendAlert();
				return "Don";
			}				
		}
	}
	return "";
}
```

Replace Constructor with Factory Method

```	
	static final int ENGINEER = 0;
	static final int SALESMAN = 1;
	static final int MANAGER = 2;
	
	static Employee create(int type) {
		switch (type) {
			case ENGINEER:
				return new Engineer();
			case SALESMAN:
				return new Salesman();	
			case MANAGER:
				return new Manager();
		}
	}
```

```	
```

```	
```

```	
```

```	
```

```	
```

```	
```

```	
```

```	
```

```	
```

```	
```

```	
```

```	
```

```	
```

```	
```

v
```	
```

```	
```

```	
```




	乐此不疲～

2014-10-21 15:00:11









