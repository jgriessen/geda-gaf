
SUBDIRS = vams

EXTRA_DIST = README.bom README.pcb README.verilog README.vhdl README.switcap \
	     README.sysc README.eagle gnetlist.1 gnetlist.html

BUILT_SOURCES = gnetlist.html

man_MANS = gnetlist.1

docsreadmedir = @GEDADOCDIR@/readmes
docsreadme_DATA = README.bom README.pcb README.switcap README.verilog \
		  README.vhdl README.sysc README.eagle

docsmandir = @GEDADOCDIR@/man
docsman_DATA = gnetlist.html


%.html: %.1
	@echo "Creating html manpage (using groff)"
	@rm -rf $*.tmp
	@if test "x$(GROFF)" != xno; then                        \
		LC_NUMERIC=C $(GROFF) -man -T html  $*.1 >$*.tmp;\
		mv -f $*.tmp $*.html                            ;\
	else                                                     \
		echo "** Did not find groff **"                 ;\
		echo "Some Documentation might not be correct!" ;\
                touch $*.html                                   ;\
	fi

gnetlist.html: gnetlist.1

MOSTLYCLEANFILES = *.log *~
CLEANFILES = *.log *~ gnetlist.html
DISTCLEANFILES = *.log core FILE *~ prototype.bak 
MAINTAINERCLEANFILES = *.log *~ Makefile.in configure gnetlist.html
