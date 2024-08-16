# Java_Collection

## List vs Set

- A list with only unique elements does not behave like a SortedSet(TreeSet) because:
 * A list maintains insertion order, whereas a TreeSet maintains elements in a sorted order
 * The internal implementation of a TreeSet(typically a Red-Black tree) ensures that operations like add, remove, and contains are performed in 0 (log n) time. A List, on the other hand does not have this performance guarantees

 ```java
	//Using List to maintain insertion order
	StudentDoubtAnalyser analyser = new ListStudentDoubtAnalyserImpl();
	
	analyser.savenames("Kat5");
	analyser.savenames("Kat2");
	analyser.savenames("Kat1");
	analyser.savenames("Kat4");
	analyser.savenames("Kat3");
	
	System.out.println(analyser.returnStudents().toString()); //[Kat5, Kat2, Kat1, Kat4, Kat3]
	
	
	//Using TreeSet to maintain sorted order and uniqueness
	StudentDoubtAnalyser analyserSet = new TreeSetStudentDoubtAnalyserImpl();
	analyserSet.savenames("Kat5");
	analyserSet.savenames("Kat2");
	analyserSet.savenames("Kat1");
	analyserSet.savenames("Kat4");
	analyserSet.savenames("Kat3");
	
	System.out.println(analyserSet.returnStudents().toString()); //[Kat1, Kat2, Kat3, Kat4, Kat5]
	
	
	//Turn result of list into a sorted result 
	Collections.sort(analyser.returnStudents()); // Collections.sort() has void return type. If we want to maintain the original, we need to create a copy then sort on that copy
	System.out.println(analyser.returnStudents().toString()); //[Kat1, Kat2, Kat3, Kat4, Kat5]
	
}
```
- A TreeSet sorts its elements based on their "natural ordering" - defined by the compareTo() method of the Comparable interface
- Elements of a TreeSet **must** either implement Comparable interface or there should be a Comparator when creating the TreeSet. If the elements do not implement Comparable and no Comparator is provided, adding elements to the TreeSet will result in a ClassCastException.
- In Java, the String class and the wrapper classes for primitive data type ( Integer, Double, Character..) implement the Comparable interface and have a natural ordering

**the String class implements Comparable**. Its natural ordering is lexicographical( dictionary order) which is based on the Unicode value of each character. The compareTo() method in String  returns 0 if the strings are equal, return a negative num if str1 is less than str2, returns a positive number if str1 is greater than str2.

**Primitive Wrapper classes implement Comparable**
--> Boolean: False is less than True

---> These natural ordering are used when we sort any of those types using Collections.sort() or arrays using Arrays.sort();

- Apart from .compareTo() method, primitive wrapper classes (Integer, Double, Float, Character,Byte,Long,Short) provide a static **compare()** method that takes 2 objects as argument. The String class does not have a static compare() method because it's actually comparing 2 characters up to the length of the shortest string. 
```java
public class StudentDoubt implements Comparable<StudentDoubt> {
	String name;
	int noOfDoubtAsked;
	@Override
	public int compareTo(StudentDoubt o) { // comparing current object with another object
		// TODO Auto-generated method stub
		return Integer.compare(o.noOfDoubtAsked, this.noOfDoubtAsked); //this has nothing to do to Comparator interface
		//this is .compare() static method of Integer wrapper class.
	}
}
```

- Likewise, for other custom objects, we need to either implements Comparable interface or Comparator interface on them. Comparator provides flexibility of .compare() method to compare 2 objects instead of .compareTo() which comparing current object with the other. However, in order for it to be implementable by all custom objects, the .compare() method of Comparator is an instance method. Each class that implements Comparator interface must provide its own implementation of the .compare() method. 
- Collections.sort() can also be applied to custom objects but apart from the object itself, we need to pass the comparator instance as an argument as well IF the object class does not implement Comparable interface.
- There are 2 ways that Comparator can be implements on a class: 

OPTION 1:like Comparable, implemented directly by the class whose instances are being compared. The natural ordering defined by Comparator now is an intrinsic property of the class itself.

OPTION 2: this is an advantage of Comparator - create a separate Comparator class that implements Comparator interface. We can call instance of this custom Comparator class in any class and reuse the comparing merchanism in any class. 

---> Collection.sort(object, comparator_instance);
---> Set< Custom_Object > T = new TreeSet<>(comparator_instance);

```java
Set<StudentDoubt> studentDoubts  = new TreeSet<>();
	studentDoubts.add(student5);
	studentDoubts.add(student4);
	studentDoubts.add(student3);
	studentDoubts.add(student2);
	studentDoubts.add(student1);
	
	System.out.println(new ArrayList<>(studentDoubts).toString()); //comparable merchanism comparing on number of doubt ask
	//print: [StudentDoubt [name=kat5, noOfDoubtAsked=1], StudentDoubt [name=kat4, noOfDoubtAsked=2], StudentDoubt [name=kat3, noOfDoubtAsked=3], StudentDoubt [name=kat2, noOfDoubtAsked=4], StudentDoubt [name=kat1, noOfDoubtAsked=5]]

	Comparator<StudentDoubt> comparatorNameAndNoOfDoubt = new StudentComparatorOnNameAndDoubt();
	
	System.out.println("using comparator to compare on name and number of doubt");
	
	studentDoubts = new TreeSet<>(comparatorNameAndNoOfDoubt );
	studentDoubts.add(student5);
	studentDoubts.add(student4);
	studentDoubts.add(student3);
	studentDoubts.add(student2);
	studentDoubts.add(student1);
	
	System.out.println(new ArrayList<>(studentDoubts).toString());
    //print: [StudentDoubt [name=kat1, noOfDoubtAsked=5], StudentDoubt [name=kat2, noOfDoubtAsked=4], StudentDoubt [name=kat3, noOfDoubtAsked=3], StudentDoubt [name=kat4, noOfDoubtAsked=2], StudentDoubt [name=kat5, noOfDoubtAsked=1]]

```

```java
public class StudentComparatorOnNameAndDoubt implements Comparator<StudentDoubt> {

	@Override
	public int compare(StudentDoubt o1, StudentDoubt o2) {
		//sort first on the basis of name, then on no of doubt asked
		if(o1.getName().equals(o2.getName())) {
			return Integer.compare(o1.noOfDoubtAsked, o2.noOfDoubtAsked);
		}
		return o1.getName().compareTo(o2.getName());
	}

}
```

NOTE: if we place 2 objects in argument of Compartor in reverse order, we get  reverse order comparison. 

OPTION 3: we want to compare just once and dont want to create a separate class/method --> we create an anonymous Comparator class: 

```java
Comparator<StudentDoubt> anonymousComparator  = new Comparator<StudentDoubt>(){
    @Override
    public int compare(...)
}
```


