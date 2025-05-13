# computer_organization_114
##  Q2
./build/X86/gem5.opt configs/example/se.py -c tests/test-progs/hello/bin/x86/linux/hello --cpu-type=TimingSimpleCPU --caches --l2cache --mem-type=NVMainMemory --nvmain-config=../NVMain/Config/PCM_ISSCC_2012_4GB.config

## Q3
./build/X86/gem5.opt configs/example/se,py -c ./quicksort --options="input.txt" --cpu-type=TimingSimpleCPU --caches --l2cache --l3cache --l1i_size=32kB --l1d_size=32kB --l2_size=128kB --l3_size=128kB --l3_assoc=2 --mem-type=NVMainMemory --nvmain-config=../NVMain/Config/PCM_ISSSC_2012_4GB.config
