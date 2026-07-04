# Results
## Program 1
### Comparing results on using multiple threads for program 1
* I compiled and ran the program with the **spatial decomposition** approach and then assembled the following table.

| Thread no   | 2     | 3     | 4     | 5     | 6     | 7     | 8     |
|-------------|-------|-------|-------|-------|-------|-------|-------|
| Performance | 1.99x | 1.64x | 2.44x | 2.50x | 3.05x | 3.43x | 4.08x |

* Speedup is not linear.


```
 |
5|
 |
4|													.
 |											.
3|									.
 |					.		.
2|  .
 |			.
1|__________________________________________________________
  	2		3		4		5		6		7		8
```

* It starts getting linear at 5 threads.
* So the problem with using three threads exactly is that the middle thread takes most of the work. This is because the image has more computation to do in the middle. In case of using two threads, the work was almost equally divided. That's why using 3 threads actually worsen performance. This is an example of **load imbalance**.
* I think the very same reason is what prevents n threads from getting roughly nx speedup.
* I updated the code to calculate the time taken by each thread and you can clearly see that in the case of three threads. The following was part of the output.
```
heTime in thread 0 is [0.064284]
Time in thread 2 is [0.067499]
Time in thread 1 is [0.195483]
```
* Thread 1 does about 2.9x more work than thread 2.
* Boths threads 0 and 2 can take more work in parallel and thus result in more speedup. It's like you have more resources but you can't just get things balanced correctly.

### Distributing the workload
* So instead of just giving each thread a huge chuck, I am going to try and be more fair and just round-robin each row to the respective threads.
* I seem to be stuck at 5.40x-5.75x speedup. I am getting the right number of iterations per thread, but still I can't break the 7.0x ratio.

## Program 2
### CS149's fictitious SIMD intrinsics
* After running `./myexp -s 10000` for vector lengths 2, 4, 8, and 16, the following is the vector utilization of each.
2 -> 72.6%
4 -> 69%
8 -> 65.5%
16 -> 63.8%

* The main reason for the vector utilization decreasing as we increase the vector width is __masking__. Becase we have a while loop and an if statement, some lanes might be processing while others are idle waiting. The wider the vector width is, potentially the more lanes are sitting idle and thus the lower the overall utilization.
* To be specific. Here is the code:

```C
void clampedExpSerial(float* values, int* exponents, float* output, int N) {
  for (int i=0; i<N; i++) {
    float x = values[i];
    int y = exponents[i];
    if (y == 0) {
      output[i] = 1.f;
    } else {
      float result = x;
      int count = y - 1;
      while (count > 0) {
        result *= x;
        count--;
      }
      if (result > 9.999999f) {
        result = 9.999999f;
      }
      output[i] = result;
    }
  }
}
```
* Now you see `count` is directly proportional with the exponent value. The higher the exponent value, the more `while` iterations it takes. Now if we have a large vector width, the probability of the exponents being differnt increases which means more masking which means less utilization. This is also true for the if condition, but it is probably less noticable.
