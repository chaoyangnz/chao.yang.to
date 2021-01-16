---
title: pseudo-random number & shuffle
toc: true
date: "2017-04-26T18:45:55+00:00"
---

# Pseudo random number

Given a seed, and you will always get the next number, which is predictable. So that is reason why it is called **pseudo** random.

A simple algorithm is [Linear congruential generator](https://en.wikipedia.org/wiki/Linear_congruential_generator).

```

next random = (a * random + c) mod 2^32
```


The initial random is `seed`.

## Java implementation

In `next(bits)` method:



```java
nextseed = (oldseed * multiplier + addend) & mask;
```


here, multiplier is 0x5DEECE66DL, addend is 0xBL, mask is (1L << 48) - 1.

# Shuffle

Shuffling is a procedure used to randomize a deck of playing cards to provide an element of chance in card games.

[Fisher–Yates shuffle](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle)


## implementation

![](/media/14931931313090.png)


For the unshuffled subarray, always select a random element swapping with the last one.



```java
public void shuffle(int[] arr) {
   for(int len = arr.length; len > 1; --len) {
       int index = new Random().nextInt(len);
       int temp = arr[index];
       arr[index] = arr[len-1];
       arr[len-1] = temp;
   }
}
```


Please refer to `Collections.shuffle(..)`. This algorithm has `O(N)` time complexity.

# Practice

## generate a 4-digit PIN code with no consecutive digits being the same

1232 is legal, but 1233 is illegal.



```java
public static void main(String[] args) {
	List<String> list = new ArrayList<>();
	// generate all PINs 
	for(int i=0; i<= 9999; ++i) {
	  String pin = padding(i);
	  if(!pin.matches("\\d*(\\d)\\1\\d*")) {
	      list.add(pin);
	  }
	}
	
  // shuffle
  Collections.shuffle(list);
  
  // each time, select the last PIN
  int last = list.size() - 1;
  String pin = list.get(last);
  list.remove(last);
  System.out.println(pin);
}
	
private static String padding(int i) {
	int remaining = i;
	int d4 = remaining / 1000;
	remaining %= 1000;
	int d3 = remaining / 100;
	remaining %= 100;
	int d2 = remaining / 10;
	remaining %= 10;
	int d1 = remaining;
	
	return "" + d4 + d3 + d2 + d1;
}
```


