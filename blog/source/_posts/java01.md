---
title: Java Software Design
date: 2018-01-22 13:34:38
tags: Java
categories: STUDY
---

This is the first part of Java Software Design notes.

<!--more-->

1. 
	- Primitive Type(stack): int, long, double, boolean, char, byte, short, float
	- Object Type(heap): classes, interfaces, arrays, enums, annotations ("=="(pointer) != ".equals()"(values))
	- Converts Primitive type to Object types: Integer, Long, Double, Boolean, Character, Byte, Short, Float
	- Generic Type contains Object type, eg. List<Integer>, HashMap<Byte, Double>

2. Method in all Objects/Classes because all class extends class "Object": 
	- .equals
	- .hashCode
	- .toString: every object can be printed
	Overwrite .equals must also overwrite .hashCode

3. Interface: a type without implementation
	- An interface can <u>extend</u> multiple interfaces
	- A class can <u>implement</u> multiple interfaces

4. Array  default method ".length()"
5. Enum

	```
	enum Planet {EARTH, ...;}
	Planet location = Planet.EARTH;
	location.equals(Planet.EARTH);
	```

6. Annotation: mark code without any immediate functional effect. eg. @Override (when code doesn;t overwrite a method will warning)
7. Primary collecton interfaces(java.util)

	```
		 Collection          Map
		 /   |   \
	  Set  List  Queue
	```
	Interface | Implementation
	:--:|:--:
	Set | HashSet(no order), LinkedHashSet(ordered by add), TreeSet
	List | ArrayList
	Queue | ArrayDeque
	Deque | ArrayDeque
	Map | HashMap
	
	```
	Set<String> s = new TreeSet<>()
	// s can only use methods in set(limit methods usage)
	```
	- LinkedHashSet can contain any Object Type.
	- TreeSet just contain those Object Type that can compared to each other.

8. arrays and Collection cannot mix. Priority: Collection > arrays
9. private: same class can access each others' private variable

	```
	class Point{
		private double x, y;
		public Point(double x, double y){
			this.x = s;
			this.y = y;
		}
		public coid translateBy(Point p){
			x += p.x; // in class Point, one can access another Point p private variable
			y += p.y;
		}
		public double getX(){return x;} // accessed by another class.
		public double getY(){return y;} 
	}
	```
10. Class and Interface

	```
	public interface Animal {
		void vocalize();
	}
	
	class Dog implements Animal{
		public void vocalize(){
			System.out.println("Woof!");
		}
	} 
	
	class Cow implements Animal{
		public void vocalize(){
			moo();
		}
		public void moo(){
			System.out.println("Moo");
		}
	} 
	
	//main
	
	// 1. Cannot define a use interface Animal
	// Animal a = new Animal(); 
	// vocalize();
	// 2. define Dog instance d, d can access Dog's every methods
	Dog d = new Dog();
	d.vocalize();
	// 3. define Animal b used implementation, b can only access interface Animal's methods
	Animal b = new Cow();
	b.vocalize();
	// moo() doesn't in interface Animal, b cannot access
	// b.moo();
	```
  