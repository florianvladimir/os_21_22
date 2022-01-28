# Memory 3

## Swap Space

Spcae welcher vom OS reserviert ist fürs Swapen


- Memory auf den Disk speicheren.
- Viel langsamer als RAM zugriff

## Then Present Bit

Zusätzliches Bit im Page Table Entry

### The Page Fault

Auf eine Page zugreiffen, wekche das Present Bit nicht hat. (Page ist auf der Disk)

- Context swaping, OS läuft, zugriff auf Disk

### Haldning the Page Fault

- Using the PTE, the disk address of the missing page can be determined
- The page-fault handler performs the necessary disk I/O to load the page from the swap space
- It then updates the PTE's PFN, this time using the memory address

Wenn er nicht genug platz hat im Memory, muss platz gemacht werden: Frame muss gespeichert werden

### Optimizations

It is not effcient to perform swapping on a page-per-page basis.
Instead, often there is some OS thread, which:

- Runs from time to time and checks the amount of free pages available
- If it is less than a specic minimum, it swaps out pages untilreaching the minimum amount again

## Replacement Polecies

Main memory can be thought of as a cache for pages (holding a subset of all pages). A good replacement policy thus tries to maximize cache hits (or minimize cache misses ).

TM and TD are the times required for memory and disk access, PMiss theprobability of a cache miss.

average memory access time: AMAT = TM + (PMiss · TD)

Assuming 100ns for TM and 10ms for TD:
- A hit rate of 90% (PMiss = 0.1) leads to 1.0001ms AMAT
- A hit rate of 99.9% (PMiss = 0.001) leads to 10.1µs AMAT

Clearly, already a tiny miss rate quickly becomes the dominant
factor for AMAT and must be avoided.

### Least-Recently Used (LRU) Policy

wird am meisten verwendet. Hier im Beispiel ist Hit Rate 6/7 optimal

### Clock

Ist eine gute approximierung des LRU welche nicht zu teuer ist.
Ist effizienter als Random und FIFO und quasi so gut wie LRU

# Excercise 

## 1.2.1

### FIFO

./paging-policy.py -s 0

8
8 7
8 7 4
2 7 4
2 5 4
2 5 4
2 5 7
3 5 7
3 4 7
3 4 5

./paging-policy.py -s 0 -c (Solution)

1Hit, 9Misses hitrate 10

### LRU

./paging-policy.py -s 0 --policy=LRU

8
8 7
8 7 4
2 7 4
2 5 4
7 5 4
7 3 4
7 3 4
5 3 4

./paging-policy.py -s 0 --policy=LRU -c

2 Hits, 8 Misses, hitrate 20

### OPT

./paging-policy.py -s 0 --policy=OPT

8
8 7
8 7 4
2 7 4
5 7 4
5 7 4
5 7 4
5 3 4
5 3 4

./paging-policy.py -s 0 --policy=OPT -c

4 Hits, 6 Misses hitrate 40

## 1.2.2

FIFO: 6 Pages die nacheinander immer rotieren, so dass es immer ein miss gibt: Immer wenn das 6te gefragt wird, ist es nicht im Cache, es gibt also immer ein miss
-> In diesem Fall wäre MRU die Beste strategie.

LRU: 6 Pages die nacheinander immer rotieren, so dass es immer ein miss gibt: Immer wenn das 6te gefragt wird, ist es nicht im Cache, es gibt also immer ein miss

MRU: 6 Pages wenn 1|2|3|4|5 im Cache sind und 6 kommt, dann 1|2|3|4|6: Im worstcase muss jetzt die 5 kommen: 1|2|3|4|5, dann wieder die 6, dann wieder die 5 usw. 
-> Ist schlecht, wenn immer die 2 selben Pages wieder und wieder angeschaut werden (immer code, stack abwechslungsweise)

_Lösung Stream ab 01:26_