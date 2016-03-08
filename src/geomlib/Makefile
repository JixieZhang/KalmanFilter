########################################################################
# This Makefile is used to create root class using cint
# It will compile all source code in  $(SRCDIR)
# User should provide $LIBNAME, otherwise current dir name will be use 
########################################################################

########################################################################
MYOS        := $(shell uname)
ARCH        := $(shell uname -m)
USER        := $(shell whoami)
MYHOST      := $(shell hostname -s)

########################################################################
LIBNAME     := S4Goem
VERSION     := 1.0.0

ifeq  ("$(LIBNAME)","")
LIBNAME     := $(shell basename $(shell pwd))
endif

LIBFILE     := lib$(LIBNAME).so
USERDICT    := $(LIBNAME)Dict
########################################################################
SRCDIR      := .
INCDIR      := . 
OBJDIR      := obj.$(ARCH)

########################################################################
MODELDIR    := 
INCDIR      := $(INCDIR):$(MODELDIR)
#OTHERINC just define the header file, will not be used for cint
OTHERINC    := -I../geomlib -I../kallib -I../kaltracklib -I../utils
########################################################################
# Compiler
AR          := ar
CC          := gcc
CXX         := g++
FF          := gfortran
LD          := g++

########################################################################
# Flags
ifeq ($(ARCH),i686)
    MODE    := -m32
else
    MODE    := -m64
endif
INCDIRS     := $(patsubst %,-I%,$(subst :, ,$(INCDIR)))
INCDIRS     += $(OTHERINC)
CFLAGS      := -Wall -fPIC -O3 -g $(MODE) $(INCDIRS)
CXXFLAGS    := -Wall -fPIC -O3 -g $(MODE) $(INCDIRS)
FFLAGS      := -Wall -fPIC -O3 -g $(MODE) $(INCDIRS)
ifeq ($(MYOS),Darwin)
#in Darwin, do not use -fno-leading-underscore
    FFLAGS  += -fno-second-underscore -fno-automatic -fbounds-check \
               -funroll-all-loops -fdollar-ok -ffixed-line-length-none \
               -fno-range-check
else
    FFLAGS  += -fno-leading-underscore -fno-second-underscore \
               -fno-automatic -fbounds-check -funroll-all-loops \
               -fdollar-ok -ffixed-line-length-none -fno-range-check
endif
GPPFLAGS    := -MM
LDFLAGS     := -O3 -g $(MODE)

########################################################################
# Generate obj file list
FSOURCES    := $(wildcard $(SRCDIR)/*.[Ff])
CSOURCES    := $(wildcard $(SRCDIR)/*.[Cc])
CSOURCES    += $(wildcard $(SRCDIR)/*.[Cc][Cc])
CSOURCES    += $(wildcard $(SRCDIR)/*.[Cc][XxPp][XxPp])
SOURCES     := $(FSOURCES) $(CSOURCES)
# header files
HEADERS     := $(foreach n,$(subst :, ,$(INCDIR)),$(wildcard $(n)/*.hh))
HEADERS     += $(foreach n,$(subst :, ,$(INCDIR)),$(wildcard $(n)/*.h))
LINKDEF     := $(foreach n,$(subst :, ,$(INCDIR)),$(wildcard $(n)/*LinkDef.h*))
#remove LinkDef.h from header because cint requires its position be at the end
#and only cint use it. No other source will use it
HEADERS     := $(filter-out $(LINKDEF), $(HEADERS))
# add .o to all the source files
OBJS        := $(addsuffix .o, $(basename $(SOURCES)))
OBJS        := $(patsubst  $(SRCDIR)/%.o,$(OBJDIR)/%.o,$(OBJS))
DEPS        := $(subst .o,.d,$(OBJS))

########################################################################
# Libs
SYSLIBS     := -lstdc++ -lgfortran
MODELLIST   := $(subst :, ,$(MODELDIR))
OTHEROBJS   := $(foreach n,$(MODELLIST),$(patsubst $(n)%,$(n)/$(OBJDIR)%,\
               $(wildcard $(n)/*.[CcFf])))
OTHEROBJS   += $(foreach n,$(MODELLIST),$(patsubst $(n)%,$(n)/$(OBJDIR)%,\
               $(wildcard $(n)/*.[Cc][Cc])))
OTHEROBJS   += $(foreach n,$(MODELLIST),$(patsubst $(n)%,$(n)/$(OBJDIR)%,\
               $(wildcard $(n)/*.[Cc][XxPp][XxPp])))
OTHEROBJS   := $(addsuffix .o, $(basename $(OTHEROBJS)))
OTHERLIBS   := $(addprefix ../../, $(OTHERLIBS))

########################################################################
# ROOT configure
ROOTCFLAGS  := $(shell root-config --cflags)
ROOTLIBS    := $(shell root-config --libs)
ROOTGLIBS   := $(shell root-config --glibs) -lMinuit

CXXFLAGS    += $(ROOTCFLAGS)
LIBS        := $(SYSLIBS) $(ROOTLIBS)
GLIBS       := $(SYSLIBS) $(ROOTGLIBS)

########################################################################
# You can specify the .SUFFIXES
.SUFFIXES: .c .C .cc .CC .cpp .cxx .f .F
.PHONY: all clean test
VPATH       := $(SRCDIR)

########################################################################
all: lib 

ifeq ("$(KALMANROOT)","")
KALMANROOT := ..
endif
install: lib
	@cp -f $(HEADERS) $(KALMANROOT)/include/.
	@cp -fl $(LIBFILE) $(KALMANROOT)/lib/.
	@cp -f $(LIBFILE).$(VERSION) $(KALMANROOT)/lib/.
########################################################################
# Make the $(TARGET).d file and include it.
$(OBJDIR)/%.d: %.c
	@echo Making dependency for file $< ......
	@set -e; \
	$(CC) $(GPPFLAGS) $(CFLAGS) $< | \
	sed 's!$*\.o!$(OBJDIR)/& $@!' > $@; \
	[ -s $@ ] || rm -f $@

$(OBJDIR)/%.d: %.C
	@echo Making dependency for file $< ......
	@set -e; \
	$(CXX) $(GPPFLAGS) $(CXXFLAGS) $< | \
	sed 's!$*\.o!$(OBJDIR)/& $@!' > $@; \
	[ -s $@ ] || rm -f $@

$(OBJDIR)/%.d: %.cc
	@echo Making dependency for file $< ......
	@set -e; \
	$(CXX) $(GPPFLAGS) $(CXXFLAGS) $< | \
	sed 's!$*\.o!$(OBJDIR)/& $@!' > $@; \
	[ -s $@ ] || rm -f $@

$(OBJDIR)/%.d: %.CC
	@echo Making dependency for file $< ......
	@set -e; \
	$(CXX) $(GPPFLAGS) $(CXXFLAGS) $< | \
	sed 's!$*\.o!$(OBJDIR)/& $@!' > $@; \
	[ -s $@ ] || rm -f $@

$(OBJDIR)/%.d: %.cpp
	@echo Making dependency for file $< ......
	@set -e; \
	$(CXX) $(GPPFLAGS) $(CXXFLAGS) $< | \
	sed 's!$*\.o!$(OBJDIR)/& $@!' > $@; \
	[ -s $@ ] || rm -f $@

$(OBJDIR)/%.d: %.cxx
	@echo Making dependency for file $< ......
	@set -e; \
	$(CXX) $(GPPFLAGS) $(CXXFLAGS) $< | \
	sed 's!$*\.o!$(OBJDIR)/& $@!' > $@; \
	[ -s $@ ] || rm -f $@

#$(OBJDIR)/%.d: %.f
#	@echo Making dependency for file $< ......
#	@set -e; \
#	$(FF) -cpp $(GPPFLAGS) $(FFLAGS) $< | \
#	sed 's!$*\.o!$(OBJDIR)/& $@!' > $@; \
#	[ -s $@ ] || rm -f $@

#$(OBJDIR)/%.d: %.F
#	@echo Making dependency for file $< ......
#	@set -e; \
#	$(FF) -cpp $(GPPFLAGS) $(FFLAGS) $< | \
#	sed 's!$*\.o!$(OBJDIR)/& $@!' > $@; \
#	[ -s $@ ] || rm -f $@

ifneq ($(DEPS),)
-include $(DEPS)
endif

########################################################################
lib: dir $(OBJS) $(OBJDIR)/$(USERDICT).o
	@for model in $(MODELLIST); do \
		if [ -d $$model ]; then \
			make -s -C $$model; \
		fi; \
	done;
	@$(AR) -r $(LIBFILE).$(VERSION) $(OBJS) $(OTHEROBJS) &>/dev/null
	@ln -sf $(LIBFILE).$(VERSION) $(LIBFILE)
	@echo "Linking $(LIBFILE) ...... done!"

$(USERDICT).cxx: $(HEADERS) LinkDef.h
		@echo "Generating dictionary file $(USERDICT).cxx ......"
		@$(ROOTSYS)/bin/rootcint -f $@ -c $(CXXFLAGS) $^

$(OBJDIR)/$(USERDICT).o: $(USERDICT).cxx
	@echo Compiling $< ......
	@$(CXX) -c $< -o $@  $(CXXFLAGS)

########################################################################
$(OBJDIR)/%.o: %.c
	@echo Compiling $< ......
	@$(CC) -c $< -o $@  $(CFLAGS)

$(OBJDIR)/%.o: %.C
	@echo Compiling $< ......
	@$(CXX) -c $< -o $@  $(CXXFLAGS)

$(OBJDIR)/%.o: %.cc
	@echo Compiling $< ......
	@$(CXX) -c $< -o $@  $(CXXFLAGS)

$(OBJDIR)/%.o: %.CC
	@echo Compiling $< ......
	@$(CXX) -c $< -o $@  $(CXXFLAGS)

$(OBJDIR)/%.o: %.cpp
	@echo Compiling $< ......
	@$(CXX) -c $< -o $@  $(CXXFLAGS)

$(OBJDIR)/%.o: %.cxx
	@echo Compiling $< ......
	@$(CXX) -c $< -o $@  $(CXXFLAGS)

$(OBJDIR)/%.o: %.f
	@echo Compiling $< ......
	@$(FF) -c $< -o $@  $(FFLAGS)

$(OBJDIR)/%.o: %.F
	@echo Compiling $< ......
	@$(FF) -c $< -o $@  $(FFLAGS)

dir:
	@if [ ! -d $(OBJDIR) ] ; then mkdir -p $(OBJDIR) ;fi

########################################################################
clean: dir
	@rm -f $(OBJDIR)/*
	@rm -f $(LIBFILE) $(LIBFILE).$(VERSION)
	@rm -f $(USERDICT).cxx $(USERDICT).h
	@rm -f *~ *# */*~ */*#

distclean: clean
	@for model in $(MODELLIST); do \
		if [[ -d $$model ]]; then \
			make clean -s -C $$model; \
		fi; \
	done;

test:
	@echo ==================================
	@echo \\MYOS\:$(MYOS) \\ARCH\:$(ARCH)
	@echo ==================================
	@echo \\LIBNAME\:$(LIBNAME) \\VERSION\:$(VERSION)
	@echo ==================================
	@echo \\USERDICT\:$(USERDICT) \\LINKDEF\:$(LINKDEF)
	@echo ==================================
	@echo \\CFLAGS\:$(CFLAGS)
	@echo ==================================	
	@echo \\CXXFLAGS\:$(CXXFLAGS)   
	@echo ==================================     
	@echo \\FFLAGS\:$(FFLAGS)
	@echo ==================================
	@echo \\LDFLAGS\:$(LDFLAGS)
	@echo ==================================
	@echo \\SYSLIBS\:$(SYSLIBS)
	@echo ==================================
	@echo \\fsources\: $(FSOURCES)	
	@echo ==================================
	@echo \\sources\: $(SOURCES)
	@echo ==================================
	@echo \\headers\: $(HEADERS)
	@echo ==================================
	@echo \\objs\: $(OBJS)	
	@echo ==================================
	@echo \\dependencies: \$(DEPS)
	@echo ==================================

help: test

env: test
	@echo \\current dir = $(shell basename `pwd`)