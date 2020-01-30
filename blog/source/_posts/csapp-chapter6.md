---
title: csapp-chapter6
date: 2017-04-08 12:08:14
tags: csapp
categories: STUDY
---

chapter 6 REVIEW

<!--more-->
### DRAM vs. SRAM
SRAM is faster and significantly more expensive than DRAM. SRAM is used for cache memories, both on and off the CPU chip. DRAM is used for the main memory plus the frame buffer of a graphics system.
### Memory Capacity & Access Time

{% math %}
Capacity = \frac{\#bytes}{sector} \times \frac{average\#sectors}{track} \times \frac{\#tracks}{surface} \times \frac{\#surface}{platter} \times \frac{\#platter}{disk}
{% endmath %}

{% math %}
access\ time=seek\ time+rotation\ latency+transfer\ time
{% endmath %}

{% math %}
T_{ave\ rotation}(ms)=\frac{1}{2}T_{max\ rotation}=\frac{1}{RPM}\times \frac{60000ms}{1min}
{% endmath %}
{% math %}T_{ave\ transfer}(ms)=\frac{1}{RPM}\times \frac{1}{average\#sectors/track}\times \frac{60000ms}{1min}
{% endmath %}

### Locality
Programs tend to use data and insructions with address near(spatial) or equal(temporal) to those they have used recently.
### Memory Hierarchy
![memory hierarchy](https://raw.githubusercontent.com/Annashuo/hello-world/master/memory_hierarchy.png)

- cache hit
- cahce miss(cold misses, comflict misses, capacity misses)

Each memory address has m bits that form $M=2^{m}$ unique addresses. A cache is organized as an array of $S=2^{s}$ cache sets. Each set consist of E cache lines. Each line consists of a data block of $B=2^{b}$ bytes, a valid bit that indicates whether or not the line contain meaningful information, and $t=m-(b+s)$ tag bits that uniquely identify the block stored in the cache line.

#### Type of Cache:

- Direct-Mapped Cache(E=1)
- Set Associative Cache(E!=1)
- Fully Associative Cache(E=C/B, S=1)

#### Issue with Writes:

- After the cache updates its copy of w, what does it do about updating the copy of w in the next lower level of the hierarchy?
	- write-through: immediately write w's cahce back to the next lower level.
	- write-back: writing the update block to the next lower level only when it is evicted from the cache by the replacement algrithm

- How to deal with write miss?
	- write-allocate: loads the corresponding block from the next lower level into the cache and then updates the cache block.
	- no-write-allocate: bypasses the cache and writes the word directly to the next lower level.

write-through + no-write-allocate  vs. write-back + write-allocate

#### Cache Blocking

```
	//non-blocking,total miss: (9n/8)*n^2=9/8*n^3
	for(i=0;i<n;i++){
		for(j=0;j<n;j++){
			for(k=0;k<n;k++){
				c[i*n+j]=a[i*n+k]*b[k*n+j];
			}
		}
	}
	
	//blocking, total misses: nB/4*(n/B)^2=n^3/(4B)
	for(i=0;i<n;i+=B){
		for(j=0;j<n;j+=B){
			for(k=0;k<n;k+=B){
				for(i1=i;i1<i+B;i1++){
					for(j1=j;j1<j+B;j1++){
						for(k1=k;k1<k+B;k1++){
							c[i1*n+j1]=a[i1*n+k1]*b[k1*n+j1];
						}
					}
				}
			}
		}
	}
```

