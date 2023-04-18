# Midterm

- 강의자료에서 큰 그림에 대해서는 확실하게 이해할 것
- 문제가 아예 똑같이 나옴!

## Baisc Concept

- 1.1 Three dependencies in instruction level parallelism
- 1.2 Explain how to improve the performance of thread level parallelism (TLP) on GPU
- 1.3 Global and local branch history in branch prediction
- 1.4 Serial tag-data accesses in the cache
- 1.5 Miss state holding register (MSHR)
- 1.6 Non-blocking cache
- 1.7 Directory-based cache coherency protocol

## ILP problem 1 (2회)

- 2.1 Fill in the table of instruction status with clock cycle numbers from 2 to 22.
- 2.2 Explain why the last instruction “ADDD …” is issued a few cycles later than the previous instruction “DIVD …” (not immediately after the issue of DIVD).
- 2.3 Explain why the execution of the last instruction “ADDD …” finishes at cycle 22 (not earlier).

Scoreboard 달달 외울 것! ppt 나온대로!

## 3. ILP problem 2 (2회)

3.1 Fill in the table of instruction status with clock cycle numbers from 2 to 16.
3.2 Explain why the last instruction ADDD can finish write result earlier than some other instructions in terms of instruction issue and write result.
3.3

토마슐로 순서 / 토마슐로 + Branch Prediction 달달!

## 4. Basic cache architecture (2회)

4.1 Given an address bit configuration shown above, explain how the desired data block can be read from the cache in case of cache hit.
Use the given figure and draw missing parts to explain the cache hit operation.

4.2 Explain what cache conflict miss is and give a solution (among many) to reduce conflict misses.

기본적인 caching 방법에 대해 확실하게 이해할 것!

## 5. Cache prefetch

흠... 이거 봐야겠구먼

## 6. Cache coherency

- MESI protocol

## 4. Cache Problem 2

4.1 Explain the function of MSHR and its effect on memory access latency.

## 5. Memory Problem 1

- 4 bank DRAM

## 6. Memory Problem 2

- DRAM 칩...
