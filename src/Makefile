# set directories
BUILD        := build
SRC          := src
INC          := include
LIB          := $(CURDIR)/lib
OUTPUT       := bin

MAKE         := make
CMDPARSER    := cmd-parser

# set search path
vpath %.o    $(BUILD)
vpath %.cc   $(SRC) 
vpath %.h    $(INC) 
vpath %.hh   $(INC) 

# set compiler and flags
CXX          ?= g++
CXXFLAGS     := -fPIC -I$(INC) -I$(CMDPARSER)/include -g -std=c++11

# get the stdlib in there
ifeq ($(CXX),clang++)
CXXFLAGS += -stdlib=libc++
endif

# HEP flags
FASTJETFLAGS   := $(shell fastjet-config --cxxflags --plugins)
FASTJETLDFLAGS  =
FASTJETLIBS     = $(shell fastjet-config --libs --plugins)

ROOTFLAGS      := $(shell root-config --cflags)
ROOTLDFLAGS    := $(shell root-config --ldflags)
ROOTLIBS       := $(shell root-config --glibs)

PYTHIAFLAGS    := $(shell pythia8-config --cxxflags)
PYTHIALDFLAGS  := $(shell pythia8-config --ldflags)
PYTHIALIBS      = $(shell pythia8-config --libs)


HEPLIBS += $(FASTJETLIBS)
HEPLIBS += $(ROOTLIBS)
HEPLIBS += $(PYTHIALIBS)

# Add HEP flags to common stuff
CXXFLAGS += $(ROOTFLAGS)
CXXFLAGS += $(FASTJETFLAGS)
CXXFLAGS += $(PYTHIAFLAGS)

LIBS     += $(HEPLIBS)

LDFLAGS   = $(ROOTLDFLAGS) $(PYTHIALDFLAGS) $(FASTJETLDFLAGS)

GEN_OBJ_SRC   := $(wildcard $(SRC)/*.cc)
GEN_OBJ       := $(notdir $(GEN_OBJ_SRC:%.cc=%.o))
GEN_OBJ_PATHS := $(GEN_OBJ:%=$(BUILD)/%)

EXE_PREFIX   := jet-
ALL_EXE_SRC   := $(wildcard $(SRC)/$(EXE_PREFIX)*.cc)
ALL_EXE       := $(notdir $(ALL_EXE_SRC:%.cc=%))
ALL_EXE_PATHS := $(ALL_EXE:%=$(OUTPUT)/%)

all: $(ALL_EXE_PATHS)
	@echo "jet-generator build sucessful."


$(OUTPUT)/$(EXE_PREFIX)%: $(GEN_OBJ_PATHS) #$(BUILD)/$(EXE_PREFIX)%.o
	@mkdir -p $(OUTPUT)
	@echo "linking $^ --> $@"
	@$(CXX) -o $@ $^ $(LDFLAGS) $(LIBS)

.SECONDARY: $(GEN_OBJ_PATHS)
# auto dependency generation for build

# compile rule
$(BUILD)/%.o: %.cc
	@echo compiling $<
	@mkdir -p $(BUILD)
	@$(CXX) -c $(CXXFLAGS) $< -o $@

# use auto dependency generation
DEP = $(BUILD)

DEPTARGSTR = -MT $(BUILD)/$*.o -MT $(DEP)/$*.d
$(DEP)/%.d: %.cc
	@echo making dependencies for $<
	@mkdir -p $(DEP)
	@$(CXX) -MM -MP $(DEPTARGSTR) $(CXXFLAGS) $< -o $@ 

# clean
.PHONY : clean rmdep
CLEANLIST     = *~ *.o *.o~ *.d core 

clean:
	@echo "cleaning up..."
	@rm -fr $(CLEANLIST) $(CLEANLIST:%=$(BUILD)/%) $(CLEANLIST:%=$(DEP)/%) $(BUILD) $(OUTPUT)

purge: clean rmdep
	@echo "removing all created files and folders"
	@rm -fr $(OUTPUT) 


rmdep: 
	@echo "removing dependencies"
	@rm -f $(DEP)/*.d