# CSE 15L: Servers and Bugs

## Intro:

Hello, my name is Brian Ponce. In this second lab report, I will be going over the following:

* The web-server: StringServer
* One instance of a bug in ArrayExamplesfrom

## Part 1: String Server

Code for StringServer:

![code](https://github.com/bponce04/lab2/blob/main/StringServer.png?raw=true)

Screenshot #1: 

![screenshotOne](https://github.com/bponce04/lab2/blob/main/StringServer%20example%201.png?raw=true)

Screenshot #2:

![screenshotTwo](https://github.com/bponce04/lab2/blob/main/StringServer%20example%202.png?raw=true)

Methods used:

* `.getPath()`: used to get the URL Path.
* `.getQuery()`: used to get the query component of the URI (returns null if none found).
*  The most important arguments in each of the two screenshots are checking whether the query equals `null` and if it starts with `s=`, if so then it can concatenate the old and new Strings together using `\n`.
*  The relevant field value that changes the most when given the specific request (`/add-message?s=<string>`) is `message`. It's field value gets updated to concatenate the new String while still retaining the old one (new String below the old String).

## Part 2: Bugs

ArrayExamples.java (input that prompts **no** bug to be triggered):

```
@Test
public void testReversedOne() {

  int[] input1 = {};
  
  ArrayExamples.reversed(input1);
  
  AssertArrayEquals(new int[] {}, input1} 
  
  // The following test passes in JUnit because no elements are in the array

```

ArrayExamples.java (input that prompts the bug to be triggered):

```
@Test
public void testReversedTwo() {

  int[] input2 = {3,2,1};
  
  ArrayExamples.reversed(input2);
  
  AssertArrayEquals(new int[] {1,2,3}, input2);
  
}
```
Symptoms:

Test case #1:

![imageOne](https://github.com/bponce04/cse15l-lab-reports/blob/main/testfailedOne.png?raw=true)

Test case #2:

![imageTwo](https://github.com/bponce04/cse15l-lab-reports/blob/main/testfailedTwo.png?raw=true)

When `testReversedTwo` and `testReversedThree` are used for testing JUnit points out that both tests failed, explaining that each test was expecting for the first element to be 1 (testReversedTwo) and 2 (testReversedThree), the actual output was 0. This is because none of the elements from the old array `arr` are being copied over to the new array `newArray` The approach that I took to fix the issue was:

1) Copy all of the elements from `arr` to `newArray` 
2) Change the range of the for loop in `reversed`. Instead of i iterating until it is strictly less than the length of `arr`, I rewrote it so that it would iterature until it was strictly less half of `arr`'s length --> `arr.length/2`
3) When swapping the variables inside the body of the for loop, instead of using `arr` I replaced it with `newArray`. The use of temp was also implemented to ensure that the elements were actually being swapped.

```
// Before:

static int[] reversed(int[] arr) {
    int[] newArray = new int[arr.length];
    for(int i = 0; i < arr.length; i += 1) {
      arr[i] = newArray[arr.length - i - 1];
    }
    return arr;
  }

// After:

static int[] reversed(int[] arr) {
    int[] newArray = new int[arr.length];
    for (int i = 0; i < newArray.length; i ++) {
      newArray[i] = arr[i];
    }
    for(int i = 0; i < newArray.length/2; i += 1) {
      int temp = newArray[i];
      newArray[i] = newArray[newArray.length - i - 1];
      newArray[newArray.length - i - 1] = temp;
    }
    return newArray;
  }
 ```
The code in `//After` addresses all of the issues described above because prior to my implementation of `reversed`, the method would not actually reverse the elements. With my new code, this issue is addressed by ensuring that the `arr` elements are copied to `newArray`. Another issue that was addressed in my implementation was that `arr` was still being used to swap the variables when it should have been `newArray`. Without these fixes, the code would have not been able to successfully create a new array with all of the elements reversed.

## Part 3: Reflection

Through out this lab, I learned the fundamentals of running and creating a web-server from scratch. This largely entailed having to learn how to use the URLHandler interface and its public methods (`handleRequested(URI uri)`). It was interesting being responsible of creating a small remote web-server when implemeing `StringServer`. One other important lesson I learned from the lab was figuring how to identify and fix bugs in a body of code. This involved having to understand what exactly the code was supposed to do (writing out tests on paper and seeing what worked and what did not). Those were the two most important aspects of the lab that I learned.
