#*************************************************************#
#
#   Ensemble, 2_00
#   Copyright 2004 Cornell University, Hebrew University
#           IBM Israel Science and Technology
#   All rights reserved.
#
#   See ensemble/doc/license.txt for further information.
#
#*************************************************************#
#*************************************************************#
#
# MAKEFILE: root Ensemble Makefile
#
# Author: Ohad Rodeh  12/2001
# Based on code by Mark Hayden
#
#*************************************************************#

.PHONY: socket tar tools

ENSROOT = ..
include $(ENSROOT)/mk/config.mk
include $(ENSROOT)/mk/preamble.mk
include $(ENSROOT)/mk/files.mk

#*************************************************************#
# Choose whether to use the optimizing compiler or not.
#
ifdef OPT
include $(ENSROOT)/mk/ocamlopt.mk
else
include $(ENSROOT)/mk/ocaml.mk
endif

#*************************************************************#
INCLUDE = \
	-I socket -I util -I mm -I type -I infr -I route -I appl \
	-I trans -I groupd -I layers/trans -I layers/other -I crypto

include $(ENSROOT)/mk/rules.mk
#*************************************************************#
# cleanup

SUBDIRS = \
	appl		\
	crypto		\
	groupd		\
	infr		\
	layers		\
	layers/bypass	\
	layers/debug	\
	layers/flow	\
	layers/total	\
	layers/vsync	\
	layers/other	\
	layers/trans	\
	layers/gossip	\
	layers/scale	\
	layers/security	\
	mm		\
	prog		\
	route		\
	socket		\
	socket/u	\
	socket/s	\
	socket/s/unix	\
	socket/s/nt	\
	trans		\
	type		\
	util	

#*************************************************************#


LIBS = \
	$(ENSLIB)/libenscore$(CMA)\
	$(ENSLIB)/libensmin$(CMA) \
	$(ENSLIB)/libensrest$(CMA) \
	$(ENSLIB)/ensemble.mli \
	$(ENSLIB)/ensemble$(CMI)

#*************************************************************#
libs : $(LIBS)

all : 
	cd socket; $(MAKE) all
	cd crypto; $(MAKE) all
	$(MAKE) libs
	cd prog; $(MAKE) all

tests : all
	cd prog; $(MAKE) tests

# Call ourselves with the optimizing compiler switch
#
opt : 
	$(MAKE) OPT=yes all

#*************************************************************#
# This gets around a difference between NMAKE and MAKE. 
#
ifeq ("$(PLATFORM)" , "nt")
ENS_ABSROOT = $(MAKEDIR)
CLEANALL = for %%d in ($(SUBDIRS)) do (cd %%d & $(MAKE) clean & cd $(ENS_ABSROOT))
else
CLEANALL = for d in $(SUBDIRS); do (cd $$d; $(MAKE) -k clean); done
endif

clean :
	$(CLEANALL)
	$(CLEANDIR)
	$(RM) ensemble.ml ensemble.mli emrg* 
	$(RM) .unknown
#*************************************************************#


# Make libraries and global interface files.

$(ENSLIB)/libenscore$(CMA): $(ENSCOREOBJ)
	$(MLLIBR) -o $(ENSLIB)/libenscore$(CMA) -linkall $(ENSCOREOBJ)

$(ENSLIB)/libensmin$(CMA): $(ENSMINOBJ)
	$(MLLIBR) -o $(ENSLIB)/libensmin$(CMA) -linkall $(ENSMINOBJ)

$(ENSLIB)/libensrest$(CMA): $(ENSRESTOBJ)
	$(MLLIBR) -o $(ENSLIB)/libensrest$(CMA) -linkall $(ENSRESTOBJ)

#*************************************************************#
# The obj/ensemble.ml* files are generated from exported .mli
# files

$(ENSLIB)/ensemble.mli: $(ENSLIB)/socket$(CMI) $(ENSEMBLEMLI) $(ENSBIN)/$(EMRG)
	$(RM) ensemble.mli
	$(ENSBIN)/$(EMRG) -mli $(ENSEMBLEMLI) -o $(ENSLIB)/ensemble.mli

$(ENSLIB)/ensemble.ml: socket/socket$(CMI) $(ENSEMBLEMLI) $(ENSBIN)/$(EMRG)
	$(RM) ensemble.ml
	$(ENSBIN)/$(EMRG) -ml $(ENSEMBLEMLI) -o $(ENSLIB)/ensemble.ml

$(ENSLIB)/ensemble$(CMI): $(ENSLIB)/ensemble.mli 
	$(ENSCOMP) -c $(ENSLIB)/ensemble.mli

$(ENSLIB)/ensemble$(CMO): $(ENSLIB)/ensemble$(CMI) $(ENSLIB)/ensemble.ml $(ENSLIB)/ensemble.mli 
	$(ENSCOMP) -c $(INCLUDE) -I $(ENSLIB) $(ENSLIB)/ensemble.ml

#*************************************************************#

DEPENDS = \
	socket/socket.mli \
	appl/*.ml	appl/*.mli		\
	crypto/*/*.ml 	crypto/*/*.mli	\
	groupd/*.ml 	groupd/*.mli	\
	infr/*.ml 	infr/*.mli		\
	layers/*.ml 	layers/*.mli	\
	layers/bypass/*.ml	layers/bypass/*.mli	\
	layers/debug/*.ml	layers/debug/*.mli		\
	layers/flow/*.ml	layers/flow/*.mli		\
	layers/total/*.ml	layers/total/*.mli		\
	layers/vsync/*.ml	layers/vsync/*.mli		\
	layers/other/*.ml	layers/other/*.mli		\
	layers/trans/*.ml	layers/trans/*.mli		\
	layers/gossip/*.ml	layers/gossip/*.mli	\
	layers/scale/*.ml	layers/scale/*.mli		\
	layers/security/*.ml	layers/security/*.mli	\
	mm/*.ml	mm/*.mli			\
	route/*.ml	route/*.mli	\
	trans/*.ml	trans/*.mli		\
	type/*.ml	type/*.mli			\
	util/*.ml	util/*.mli 

depend: 
	ocamldep $(INCLUDE) $(DEPENDS) > $(DEPEND)

include $(DEPEND)
#*************************************************************#

