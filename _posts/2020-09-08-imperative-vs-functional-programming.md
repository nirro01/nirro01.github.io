---
layout: post
title: Imperative vs functional programming in java
subtitle: 10 examples of imperative and functional code 
tags: [java 8, lambda, functional programming]
#comments: true
---
It has been 6 years since java 8 was released,  
However, some developers still don't feel comfortable enough to use Lambda expressions and the stream API.  

I assume that they know the theoretical differences between declarative and imperative programming,
but they prefer the imperative paradigm because they are used to it.     

There are many tutorials which explains the stream API,
like this [java-8-streams](https://www.baeldung.com/java-8-streams)
so there is no point on repeating it.  
TL;DR - some methods are filter, map, collect, reduce, skip, sort, findAny, findFirst, limit  

I think that the best way to prove that stream API is more readable than traditional imperative code is 
by giving 10 examples of methods and two implementation for each example:
1.  by using loops and if statements - imperative style 
2.  by using the stream API - functional style  

The examples are showing common operations on lists 
and how to prevent loops and if statements using the Stream API-

All the examples are using the Person object-
```java
public record Person (String name, Integer age) {}
```  
> Records are immutable data classes that require only the type and name of fields - [https://www.baeldung.com/java-record-keyword](https://www.baeldung.com/java-record-keyword)

## Example 1  
```java
/**
Example 1
*
* @param  persons  List of person
* @return the first person with a name that starts with "n"
*/

public Optional<Person> example1A(List<Person> persons) {
    for(Person person : persons) {
        if(person.name().startsWith("n")) {
            return Optional.of(person);
        }
    }
    return Optional.empty();
}

public Optional<Person> example1B(List<Person> persons) {
    return persons
        .stream()
        .filter(person -> person.name().startsWith("a"))
        .findFirst();
}
```
## Example 2  
```java
/**
Example 2
*
* @param  persons  List of person
* @return returns a new list with all persons that their name 
*         starts with "n"
*/

public List<Person> example2A(List<Person> persons) {
    List<Person> result = new ArrayList<>();
    for(Person person : persons) {
        if(person.name().startsWith("n")) {
            result.add(person);
        }
    }
    return result;
}

public List<Person> example2B(List<Person> persons) {
    return persons
            .stream()
            .filter(person -> person.name().startsWith("a"))
            .collect(Collectors.toList());
}
```
## Example 3  
```java
/**
Example 3
*
* @param  persons  List of person
* @return returns a list of all persons that their name starts with "n",
          and multiply their age by 2
*/
public List<Person> example3A(List<Person> persons) {
    List<Person> result = new ArrayList<>();
    for(Person person : persons) {
        if(person.name().startsWith("n")) {
            result.add(new Person(person.name(), person.age() * 2));
        }
    }
    return result;
}

public List<Person> example3B(List<Person> persons) {
    return persons
        .stream()
        .filter(person -> person.name().startsWith("a"))
        .map(person -> new Person(person.name(), person.age() * 2))
        .collect(Collectors.toList());
}
```
## Example 4  
```java
/**
Example 4
*
* @param  persons  List of person
* @return returns a Map<Integer, List<Person>> that 
          group all persons by age
          key is age,
          value is a list of all persons with that age
*/
public Map<Integer, List<Person>> example4A(List<Person> persons) {
    Map<Integer, List<Person>> result = new HashMap<>();
    for(Person person : persons) {
        if (!result.containsKey(person.age())) {
            List<Person> list = new ArrayList<>();
            list.add(person);
            result.put(person.age(), list);
        } else {
            result.get(person.age()).add(person);
        }
    }
    return result;
}

public Map<Integer, List<Person>> example4B(List<Person> persons) {
    return persons
        .stream()
        .collect(Collectors.groupingBy(Person::age));
}
```
## Example 5  
```java
/**
Example 5
*
* @param  persons  List of person
* @return returns a Map<Integer, List<Person>> that 
          group all adults (18+) by age
          key is age,
          value is a list of all persons with that age
*/
public Map<Integer, List<Person>> example5A(List<Person> persons) {
    Map<Integer, List<Person>> result = new HashMap<>();
    for(Person person : persons) {
        if(person.age() >= 18) {
            if (!result.containsKey(person.age())) {
                List<Person> list = new ArrayList<>();
                list.add(person);
                result.put(person.age(), list);
            } else {
                result.get(person.age()).add(person);
            }
        }
    }
    return result;
}

public Map<Integer, List<Person>> example5B(List<Person> persons) {
    return persons
        .stream()
        .filter(person -> person.age() >= 18)
        .collect(Collectors.groupingBy(Person::age));
}
```
## Example 6  
```java
/**
Example 6
*
* @param  persons  List of person
* @return returns a Map<Integer, List<Person>> that 
          group all adults (18+) by age and convert
          their names into uppercase
          key is age,
          value is a list of all persons with that age
*/
public Map<Integer, List<Person>> example6A(List<Person> persons) {
    Map<Integer, List<Person>> result = new HashMap<>();
    for(Person person : persons) {
        if(person.age() >= 18) {
            if (!result.containsKey(person.age())) {
                List<Person> list = new ArrayList<>();
                list.add(new Person(person.name().toUpperCase() ,person.age()));
                result.put(person.age(), list);
            } else {
                result.get(person.age()).add(new Person(person.name().toUpperCase() ,person.age()));
            }
        }
    }
    return result;
}

public Map<Integer, List<Person>> example6B(List<Person> persons) {
    return persons
        .stream()
        .filter(person -> person.age() >= 18)
        .map(person -> new Person(person.name().toUpperCase(), person.age()))
        .collect(Collectors.groupingBy(Person::age));
}
```
## Example 7  
```java
/**
Example 7
*
* @param  persons  List of person
* @return true if all persons are minors (under 18), false otherwise
*/
public boolean example7A(List<Person> persons) {
    for(Person person : persons) {
        if(person.age() >= 18) {
            return false;
        }
    }
    return true;
}

public boolean example7B(List<Person> persons) {
    return persons
        .stream()
        .allMatch(person -> person.age() < 18);
}
```
## Example 8  
```java
/**
Example 8
*
* @param  persons  List of person
* @return true if all persons are minors (under 18), 
          and at least one of them is 17 years old.
          false otherwise
*/
public boolean example8A(List<Person> persons) {
    boolean atLeastOne17 = false;
    for(Person person : persons) {
        if(person.age() >= 18) {
            return false;
        }
        if(person.age() == 17) {
            atLeastOne17 = true;
        }
    }
    return atLeastOne17;
}

public boolean example8B(List<Person> persons) {
    boolean allMinors = persons
        .stream()
        .allMatch(person -> person.age() < 18);
    boolean atLeastOne17 = persons
        .stream()
        .anyMatch(person -> person.age() == 17);
    return allMinors && atLeastOne17;
}
```
## Example 9  
```java
/**
Example 9
*
* @param  persons  List of person
* @return sum the age of all minors (under 18)
*/
public int example9A(List<Person> persons) {
    int sum = 0;
    for(Person person : persons) {
        if(person.age() < 18) {
            sum += person.age();
        }
    }
    return sum;
}

public int example9B(List<Person> persons) {
    return persons
        .stream()
        .filter(person -> person.age() < 18)
        .mapToInt(Person::age)
        .sum();
}
```
## Example 10  
```java
/**
Example 10
*
* @param  persons  Varargs of List of person
* @return true if every person in every list is a minor (under 18)
*/
public boolean example10A(List<Person>... personLists) {
    for (List<Person> personList : personLists) {
        for (Person person : personList) {
            if (person.age() >= 18) {
                return false;
            }
        }
    }
    return true;
}

public boolean example10B(List<Person>... personLists) {
    return Arrays.stream(personLists)
        .flatMap(Collection::stream)
        .allMatch(person -> person.age() < 18);
}
```