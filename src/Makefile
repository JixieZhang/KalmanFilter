########################################################################	
##
## -----------------------
##   Makefile for Kalman libs
## -----------------------
##
##
## (Targets)
##	all       	: creates libraries in lib.
##	clean     	: deletes *.o ...
##	distclean 	: deletes even lib/*.
## 
########################################################################	
MFLAGS  = 
CURRDIR = .

ifeq ("$(KALMANROOT)","")
KALMANROOT := .
endif

SUBDIRS	= geomlib  kallib  kaltracklib  utils

all: install

install: dirs
	@case '${MFLAGS}' in *[ik]*) set +e;; esac; \
	for i in $(SUBDIRS); do \
	(cd $$i; echo ``making'' install ``in $(CURRDIR)/$$i...''; \
	$(MAKE) $(MFLAGS) install); \
	done

lib: dirs
	@case '${MFLAGS}' in *[ik]*) set +e;; esac; \
	for i in $(SUBDIRS); do \
	(cd $$i; echo ``making'' lib ``in $(CURRDIR)/$$i...''; \
	$(MAKE) $(MFLAGS)) lib; \
	done
dirs:
	mkdir -p $(KALMANROOT)/lib $(KALMANROOT)/include


clean:
	@case '${MFLAGS}' in *[ik]*) set +e;; esac; \
	for i in $(SUBDIRS); do \
	(cd $$i; echo ``making'' clean ``in $(CURRDIR)/$$i...''; \
	$(MAKE) $(MFLAGS) clean); \
	done

distclean:
	@case '${MFLAGS}' in *[ik]*) set +e;; esac; \
	for i in $(SUBDIRS); do \
	(cd $$i; echo ``making'' distclean ``in $(CURRDIR)/$$i...''; \
	$(MAKE) $(MFLAGS) distclean); \
	done
	rm -rf lib include


