NAME=template
include ../../Makefile_comp_tests.target

ROCP_SDK_INCL=-I$(PAPI_ROCP_SDK_ROOT)/include     \
              -I$(PAPI_ROCP_SDK_ROOT)/include/hsa \
              -I$(PAPI_ROCP_SDK_ROOT)/hsa/include

AMDCXX   ?= amdclang++
CFLAGS    = $(OPTFLAGS)
CPPFLAGS += $(INCLUDE) $(ROCP_SDK_INCL)
LDFLAGS  += $(PAPILIB) $(TESTLIB) $(UTILOBJS)
HSA_LINK = -L$(PAPI_ROCP_SDK_ROOT)/lib -lhsa-runtime64

GPUARCH = $(shell rocm_agent_enumerator 2>/dev/null | head -1)
ifneq ($(GPUARCH),)
    ARCHFLAG=--offload-arch=$(GPUARCH)
endif
GPUFLAGS=$(ARCHFLAG) --hip-link --rtlib=compiler-rt -unwindlib=libgcc

TESTS = simple simple_dyn simple_withHIP simple_withHIP_dyn simple_withLateHIP simple_withLateHIP_dyn
template_tests: $(TESTS)

%.o: %.c
	$(CC) $(CPPFLAGS) $(CFLAGS) $(OPTFLAGS) -D__HIP_PLATFORM_AMD__ -c -o $@ $<

kernel.o: kernel.cpp
	$(AMDCXX) -D__HIP_ROCclr__=1 -O2 -g -DNDEBUG $(ARCHFLAG) -W -Wall -Wextra -Wshadow -o kernel.o -x hip -c kernel.cpp

simple: simple.o kernel.o
	$(AMDCXX) -O2 -g -DNDEBUG $(GPUFLAGS) simple.o kernel.o -o simple $(LDFLAGS)

simple_dyn: simple.o kernel.o
	$(AMDCXX) -O2 -g -DNDEBUG $(GPUFLAGS) simple.o kernel.o -o simple_dyn -L../../../ -lpapi $(TESTLIB) -ldl

simple_withHIP: simple_withHIP.o kernel.o
	$(AMDCXX) -O2 -g -DNDEBUG $(GPUFLAGS) simple_withHIP.o kernel.o -o simple_withHIP $(LDFLAGS)

simple_withHIP_dyn: simple_withHIP.o kernel.o
	$(AMDCXX) -O2 -g -DNDEBUG $(GPUFLAGS) simple_withHIP.o kernel.o -o simple_withHIP_dyn -L../../../ -lpapi $(TESTLIB) -ldl

simple_withLateHIP: simple_withLateHIP.o kernel.o
	$(AMDCXX) -O2 -g -DNDEBUG $(GPUFLAGS) simple_withLateHIP.o kernel.o -o simple_withLateHIP $(LDFLAGS)

simple_withLateHIP_dyn: simple_withLateHIP.o kernel.o
	$(AMDCXX) -O2 -g -DNDEBUG $(GPUFLAGS) simple_withLateHIP.o kernel.o -o simple_withLateHIP_dyn -L../../../ -lpapi $(TESTLIB) -ldl

advanced: advanced.o kernel.o
	$(AMDCXX) -O2 -g -DNDEBUG $(GPUFLAGS) advanced.o kernel.o -o advanced $(LDFLAGS)

two_eventsets: two_eventsets.o kernel.o
	$(AMDCXX) -O2 -g -DNDEBUG $(GPUFLAGS) two_eventsets.o kernel.o -o two_eventsets $(LDFLAGS)

simple_sampling: simple_sampling.o kernel.o
	$(AMDCXX) -O2 -g -DNDEBUG $(GPUFLAGS) simple_sampling.o kernel.o -o simple_sampling $(LDFLAGS) -pthread

clean:
	rm -f $(TESTS) *.o
