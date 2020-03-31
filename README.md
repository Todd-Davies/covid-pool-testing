```python
# Comptute the expected rate of positive tests based on South Korea, which
# does extensive testing and publishes results daily
# Source: https://www.cdc.go.kr/board/board.es?mid=a30402000000&bid=0030
```


```python
# Based on data from March 31st
total_tests = 395194
confirmed_tests = 9661
expected_rate = confirmed_tests / total_tests
print(expected_rate)
```

    0.024446221349514415



```python
import math

# Compute the optimum size of the pool to get a 50% positive rate
pool_size = math.log(0.5, 1 - expected_rate)
# Round the answer down
pool_size = math.floor(pool_size)
print(f'Ideal pool size: {pool_size}')
```

    Ideal pool size: 28



```python
import random

# Generates a random set of samples, each with a 'expected_rate' chance of being positive
def generate_samples(k):
    return random.choices([True, False], [expected_rate, 1 - expected_rate], k=k)

```


```python
# Find how many tests we'll need to do for a given pool of people
def count_tests(pool):
    if len(pool) == 3: # With only three people left, we can optimise
        first_two = [pool[0], pool[1]] # Test the first two
        if any(first_two): # If they're positive, we need to test them, and the third
            return count_tests(first_two) + 2
        else: # If it was negative, assume the third one to be positive
            return 1
    if len(pool) == 2: # With only two person in the pool...
        if pool[0]: # If the first one is positive, we also need to test the other
            return 2
        else:       # Otherwise, we can assume the second to be positive
            return 1;
    if any(pool):      # If any of the samples are true, we need to test further
        first_half = pool[:len(pool)//2]  # Get the first half
        second_half = pool[len(pool)//2:] # Get the second half
        # Return this test, plus any tests that were done by the other halves
        return 1 + count_tests(first_half) + count_tests(second_half)
    # The pool was negative, we just needed this one test.
    return 1
        
```


```python
# Lets find the expected number of tests empirically by running 1 million tests
cumulative_tests = 0
iterations = 1000000

for i in range(0, iterations):
    cumulative_tests += count_tests(generate_samples(pool_size))

expected_tests = cumulative_tests / iterations
print(f'Expected number of tests per pool: {expected_tests:.1f}')
```

    Expected number of tests per pool: 5.9



```python
# This means that for every 'pool_size' people, we need only 'expected_tests' tests
saved_tests = pool_size - expected_tests
reduction = 1 / pool_size * saved_tests
reduction_percent = reduction * 100
print(f'Reduction in the number of test kits required: {reduction_percent:.1f}%')
times_more_results = 1 / (1 - reduction)
print(f'{times_more_results:.2f}x more people can be tested')
```

    Reduction in the number of test kits required: 79.0%
    4.77x more people can be tested



```python

```
