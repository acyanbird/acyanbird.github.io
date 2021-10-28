---
title: week 4 问题
date: 2021-10-22 20:30:43
categories: 学校学习
tags: linux c
---

##### OS

- [x] linux  /swap  is treat as main memory? What place will the new stage process(job) in? main memory or disk? or in the /swap stage?

Not really understand, maybe in the swapping. When process in main memory idle or main memory is full, swap to the waiting.

- [x] meaning of addr space? is code, data, stack?

No, is the heap and stack in PCB, but it has it own pid. Virtual id, logical id, the pointer in c has this memory. Actual id is physical id.

- [x] Do all processes has parents? init is the ancestor of the all processes, create all processes?

Yes, that's how the process created. Parent process is responsible to collect all child's state. So orphan process will be assign to init, which responsible to collect its state.

- [ ] what is fd in lecture menas?
- [x] where will job be in the memory? main or disk?

disk. modern OS don't have long term scheduler, that means process don't have new stage. But for the embedded one will look after if this process can be put to main memory. 

- [ ] what is execlp() use for?
- [x] using for loop to frok(), is that means we have several processes doing the same fork() together cuz it's copy?

yes. it will exactly copy every thing of parent.



##### digital system

- [x] Is that means until sysnthesis give netlist, FPGA and ASIC is same, and implement netlist to broad in mapping stage is difference?
- [ ] scanning of 7 seg, auto doing by FPGA?

no, done by yours

- [x] underline separate num like 8'b1001_1111, can it be put in different place like  8'b100_11111

yes, it does not matter

- [ ] why in the top, output abcdefg, an which did not change in main program? init the var?