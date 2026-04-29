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
Time in thread 0 is [0.064284]
Time in thread 2 is [0.067499]
Time in thread 1 is [0.195483]
```
* Thread 1 does about 2.9x more work than thread 2. 
* Boths threads 0 and 2 can take more work in parallel and thus result in more speedup. It's like you have more resources but you can't just get things balanced correctly.
