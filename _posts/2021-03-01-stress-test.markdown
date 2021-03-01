---
layout: post
title:  "Stress Test"
date:   2021-03-01 07:30:00 -0300
image:  stress.png
tags:   test algorithm python go
---
<a href="https://www.mentalhealth.org.uk/sites/default/files/stress-wording-tile.png">Image Source</a>

Algorithm is everywhere. From our fridge to rockets. That's why we have to make them as perfect as possible.

However, we are flawed creatures, how can we write (nearly) perfect algorithms? Well, we can't. But we should try either way.

What makes an algorithm perfect? 

An perfect algorithm is accurate and cheap (as in it takes little resources). Our resources are limited. We can't afford to pay gazillion dollars for memory and CPU just to iterate over a (really) long list.

To achieve that efficiency we have to test the hell out of our algorithms.

# Stress Test

Stress Test comes down to sending a lot of different inputs to the algorithm and comparing the output to what we expected.

We'll see how it works.

Let's say we want to find the biggest [product](https://en.wikipedia.org/wiki/Product_(mathematics)) in an array.

We just came up with this algorithm, but we are not sure if it will work correctly.

<table>
    <tr>
        <th>Python</th>
        <th>Go</th>
    </tr>
    <tr>
        <td><script src="https://gist.github.com/SilvaAriel/b5fa07421878d789bbd3583c4367491e.js"></script></td>
        <td><script src="https://gist.github.com/SilvaAriel/e94e639d01f2f25eb2ada5dcd3b37f7f.js"></script></td>
    </tr>
</table>


Explanation: Since the biggest product is basically the multiplication of the two biggest numbers, we traverse the array to find them. If the `number` is bigger than `biggest`, assign `number` to `biggest`, but if it's not and `number` is bigger than `second_biggest` assign it to `second_biggest` then.

Now it's time to make sure our algorithm works reliably, whatever the input size. How can we do this? <span style="color: gray">(We don't want to test it for String input, just integer size and correctness of the result)</span>

We create a simple brute-force algorithm that we are as sure as possible that it will work.

The simplest way to find what's the biggest product between two numbers is to traverse the whole array twice, comparing the product of each number with the others. That is, we will multiply `n` by `n+1`.

This algorithm will take `O(n²)`, which is really bad for large inputs, but we are not trying to achieve efficiency here. We want an reliable algorithm to compare with our optimized algorithm.

Let's write our brute-force algorithm.

<table>
    <tr>
        <th>Python</th>
        <th>Go</th>
    </tr>
    <tr>
        <td><script src="https://gist.github.com/SilvaAriel/d4ab5d18173168a4927248f27e6f6db9.js"></script></td>
        <td><script src="https://gist.github.com/SilvaAriel/672528c995797c860c9960bf125ebde5.js"></script></td>
    </tr>
</table>

For an simple algorithm like this one we can compare two things:

1. Execution time
2. Result

The result will show us if our algorithms are returning the expect results.

What expected results? Those returned by our simple algorithm.

Let's create our Stress Testing logic.
<table>
    <tr>
        <th>Python</th>
        <th>Go</th>
    </tr>
    <tr>
        <td><script src="https://gist.github.com/SilvaAriel/d41f1ddbbb5a25dd3dd0561bd11fde0a.js"></script></td>
        <td><script src="https://gist.github.com/SilvaAriel/083e9696b876ac0c15134712ab57f9d4.js"></script></td>
    </tr>
</table>

First, we compare large inputs (like `random.randint(1,1000)`) to increase our chance to find any bug.

After ~~if~~ we find it we can decrease our input's size (like `random.randint(1,10)`) to try to find the bug in a more constraint case. This will help us debugging it, because it's easier to determine the biggest product in an array with values `[3,5,9,9]` than in one array with values `[300,523,8760,5231]`.

Let's run the Stress Test. It will find a difference.

```bash
Max Common result: 43472
Max Faster result: 42845
Max Common took: 145979 ns
Max Faster took: 577 ns
Values differ
```

It looks like our Faster (measured in nanoseconds) algorithm is probably with a bug. But we don't know yet what it is.

Let's decrease the size of the input and print the array to check its values.

```bash
Values: [4 6 0 1 5 2 7 8 3]
Max Common result: 56
Max Faster result: 40
Max Common took: 740 ns
Max Faster took: 181 ns
Values differ
```

Now the array has elements from 0 to 10. This will make it easier to debug.

We know that we want the product of the biggest numbers in the array. They are 7 and 8. Their product is 56. Our brute force algorithm (called Max Common) is correct, so we can use him as a reference.

We have to understand why our faster algorithm (called Max Faster) is failing. Let's print the values of `biggest` and `second_biggest`.

```bash
Biggest: 8
Second Biggest: 5
```

The `biggest` value is correct. But the `second_biggest` somehow is 5 instead of 7. What happened?

Let's check our conditionals, since they are the ones responsible for making these assignments. We'll add an additional print to check the values each variable is getting.

<table>
    <tr>
        <th>Python</th>
        <th>Go</th>
    </tr>
    <tr>
        <td><script src="https://gist.github.com/SilvaAriel/e265127692fd23e56a3804e5decd7ccc.js"></script></td>
        <td><script src="https://gist.github.com/SilvaAriel/bd28dada267c5cef28ad84ab3b7663f6.js"></script></td>
    </tr>
</table>

<b>The output was:</b>

```bash
Values: [4 6 0 1 5 2 7 8 3]
Biggest value: 4
Biggest value: 6
Second Biggest value: 1
Second Biggest value: 5
Biggest value: 7
Biggest value: 8
```
<br/>
It seems that the `biggest` variable got 4 and then 6. But the `second_biggest` didn't get the actual second biggest value (at first, it was 4), but 1 (it didn't get 0 because its initial value is 0). The first number bigger than `second_biggest`'s initial value was the number 1.

The problem seems to be that the `biggest` variable is being assigned bigger values and discarding the preceding ones, even if they were bigger than `second_biggest`.

To solve that we just have to add a condition for when `biggest > second_biggest`. Like this:

<table>
    <tr>
        <th>Python</th>
        <th>Go</th>
    </tr>
    <tr>
        <td><script src="https://gist.github.com/SilvaAriel/3dd66213e3b17ac3fb5b7bd70982c871.js"></script></td>
        <td><script src="https://gist.github.com/SilvaAriel/2fb510bf46acfaf158b6d7505e8f9a2e.js"></script></td>
    </tr>
</table>

Now let's increase the number of random integers and test our algorithm again.

```python
# Python
n = random.randint(1,10000)
```

```go
// Go
n := rand.Intn(10000)
```

<br/>
I ran the code for 30 seconds (and pressed CTRL+C to stop it) and no difference was found. It means Max Faster algorithm is working just fine.

Just out of curiosity, let's see how faster our algorithm is. Add these lines:

```python
# Python
print("Max Faster is %.0f times faster than Max Common" % (elapsed_common/elapsed_fast))
```

```go
// Go
fmt.Printf("Max Faster is %.0d times faster than Max Common", int64(elapsed_max/elapsed_faster))
```
<br />
<b>Python outputted:</b>

```bash
Max Common took: 148081865 ns
Max Faster took: 128261 ns
Max Faster is 1155 times faster than Max Common
------------------
Max Common took: 93722602 ns
Max Faster took: 120747 ns
Max Faster is 776 times faster than Max Common
------------------
Max Common took: 138593956 ns
Max Faster took: 156146 ns
Max Faster is 888 times faster than Max Common
------------------
Max Common took: 57775530 ns
Max Faster took: 107723 ns
Max Faster is 536 times faster than Max Common
```
<br />
<b>Go outputted:</b>

```bash
Max Common took: 210722 ns
Max Faster took: 1090 ns
Max Faster is 193 times faster than Max Common
--------------
Max Common took: 828717 ns
Max Faster took: 1235 ns
Max Faster is 671 times faster than Max Common
--------------
Max Common took: 3168791 ns
Max Faster took: 4066 ns
Max Faster is 779 times faster than Max Common
--------------
Max Common took: 48031 ns
Max Faster took: 735 ns
Max Faster is 65 times faster than Max Common
```
<br/>
# Conclusion

Never trust entirely on you algorithm. It was made by a flawed creator: you.

In this post's example we tested an simple algorithm, but an complex one can have a lot of corner cases. Whenever you write an algorithm try to think about all the corner cases and test your code against them. 

Stress Test will be just one of the tools you will use to test your algorithm. Instead of you creating the data to test it, outsource it to the computer and let it do its magic.

Also, remember that the test can also be flawed, so try to keep it as simple as possible.