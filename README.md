# computer_organization_114
##  Q2
./build/X86/gem5.opt configs/example/se.py -c tests/test-progs/hello/bin/x86/linux/hello --cpu-type=TimingSimpleCPU --caches --l2cache --mem-type=NVMainMemory --nvmain-config=../NVMain/Config/PCM_ISSCC_2012_4GB.config

## Q3

> ./build/X86/gem5.opt configs/example/se.py -c ./quicksort --cpu-type=TimingSimpleCPU --caches --l2cache --l3cache --l3_assoc=? --l1i_size=32kB --l1d_size=32kB --l2_size=128kB --l3_size=256kB --mem-type=NVMainMemory --nvmain-config=../NVMain/Config/PCM_ISSCC_2012_4GB.config
>
> 2way: 2 \
> fullway: 4096

### 2way
system.l3.overall_miss_rate::total           0.682357                       # miss rate for overall accesses

```python=
assoc = 2
tag_latency = 20
data_latency = 20
response_latency = 20
mshrs = 20
tgts_per_mshr = 12
write_buffers = 8
replacement_policy = \
Param.BaseReplacementPolicy(LFURP(),"Replacement policy")
```

### fullway
system.l3.overall_miss_rate::total           0.726195                       # miss rate for overall accesses 

```python=
assoc = 2048
tag_latency = 20
data_latency = 20
response_latency = 20
mshrs = 20
tgts_per_mshr = 12
write_buffers = 8
replacement_policy = \
Param.BaseReplacementPolicy(LFURP(),"Replacement policy")
```

### discuss
the total miss rate of 2-way is lower than the total miss rate of fullway

## Q4
### Caches.py

```python=
class L3Cache(Cache):
    assoc = 8
    tag_latency = 20
    data_latency = 20
    response_latency = 20
    mshrs = 20
    tgts_per_mshr = 12
    write_buffers = 8
    replacement_policy = \
    Param.BaseReplacementPolicy(LFURP(),"Replacement policy")
```


### previos (LRU)
system.l3.replacements                          19364 

### modified (LFU)
system.l3.replacements                          25313  
