#
# Makefile for ALAN-2019
#

# XXX Note: This Makefile is a bit more verbose, with more explicit rules, than
# I might normally write. But this is because I want to you try and understand
# properly how Makefiles work.

# compiler flags
DEBUG    = -ggdb
OPTIMISE = -O0
WARNINGS = -Wall -Wextra -Wno-variadic-macros -Wno-overlength-strings -pedantic
CFLAGS   = $(DEBUG) $(OPTIMISE) $(WARNINGS)
DFLAGS   = # -DDEBUG_PARSER -DDEBUG_SYMBOL_TABLE -DDEBUG_HASH_TABLE

# commands
# XXX Note: The clang executable is an LLVM front end. It is the default C
# compiler on macOS, and it is installed in the NARGA Ubuntu setup. In my
# opinion, it provides more informative error messages than GCC, which is the
# default C compiler on Linux. If you work on a computer without clang, then if
# you root, consider installing clang, or if you do not, set the CC variable
# below to "gcc". Linux installations typically provide functionality to manage
# "alternatives", for example, where you have two different C compilers
# available; consult your system documentation. In this case, and also by
# default, the "cc" executable is a link to the default C compiler, and
# therefore, can be used below.
CC       = clang
RM       = rm -f
COMPILE  = $(CC) $(CFLAGS) $(DFLAGS)
INSTALL  = install

# files
EXES     = primes

# directories
BINDIR   = ../bin
LOCALBIN = ~/.local/bin

# XXX Note: Setting LOCALBIN to ~/bin used to be accepted practice.  Nowadays,
# many systems (and people) prefer placing user-specific executables in
# ~/.local/bin instead.  (In the olden days it was de rigueur to show off of
# arcane knowledge of Unices by having one's user binaries in plain sight,
# whereas nowadays everything must appear to work automagically.) Also refer to
# the note below on setting up your PATH variable.

### RULES ######################################################################

# executables

primes: primes.c | $(BINDIR)
	$(COMPILE) -o $(BINDIR)/$@ $^
# BINDIR

$(BINDIR):
	mkdir $(BINDIR)

### PHONY TARGETS ##############################################################

.PHONY: all clean install uninstall types

all: primes 

clean:
	$(RM) $(foreach EXEFILE, $(EXES), $(BINDIR)/$(EXEFILE))
	$(RM) *.o
	$(RM) -rf $(BINDIR)/*.dSYM

# XXX Note: For your program to be in your PATH, ensure that the following is
# somewhere near the end of your ~/.profile (for macOS, this might actually be
# ~/.bash_profile) file:
#
# if [ -d "$HOME/bin" ] ; then
#     PATH="$HOME/bin:$PATH"
# fi
#
# If this is not set, the install rule will copy the executable to ~/bin, but
# it will not be resolved automatically by the shell.  If you update the
# profile, either close and reopen the terminal, or use the "source" command to
# reload the profile.
#
# Also, to enable bash completion (of filenames with .alan extensions), add
# the following at the end of ~/.bashrc:
#
# # bash completion for ALAN
# complete -f -X '!*.alan' alanc

# Install all compiler-related binaries in the local bin.
install:
	mkdir -p $(LOCALBIN)
	$(INSTALL) $(foreach EXEFILE, $(EXES), $(wildcard $(BINDIR)/$(EXEFILE))) \
		$(LOCALBIN)

# Remove all compiler-related binaries from the local bin.
uninstall:
	$(RM) $(foreach EXEFILE, $(EXES), $(LOCALBIN)/$(EXEFILE))

# XXX Note: Make a highlight file for user-defined types.  This requires
# Exuberant ctags and AWK.  To use this in Vim, add the following four lines to
# your .vimrc:
# au BufRead,BufNewFile *.[ch] let fname = expand('<afile>:p:h') . '/types.vim'
# au BufRead,BufNewFile *.[ch] if filereadable(fname)
# au BufRead,BufNewFile *.[ch]   exe 'so ' . fname
# au BufRead,BufNewFile *.[ch] endif
types: types.vim
types.vim: *.[ch]
	ctags --c-kinds=t -o- *.[ch] |\
		awk 'BEGIN{printf("syntax keyword cType\t")}\
		{printf("%s ", $$1)}\
		END{if(!NR){print "XXX_placeholder"}else{print ""}}' > $@
	ctags --c-kinds=gsu -o- *.[ch] |\
		awk 'BEGIN{printf("syntax keyword cStorageClass\t")}\
		{printf("%s ", $$1)}\
		END{if(!NR){print "XXX_placeholder"}else{print ""}}' >> $@
	ctags --c-kinds=e -o- *.[ch] |\
		awk 'BEGIN{printf("syntax keyword cConstant\t")}\
		{printf("%s ", $$1)}\
		END{if(!NR){print "XXX_placeholder"}else{print ""}}' >> $@
	ctags --c-kinds=d -o- *.[ch] |\
		awk 'BEGIN{printf("syntax keyword cDefine\t")}\
		{printf("%s ", $$1)}\
		END{if(!NR){print "XXX_placeholder"}else{print ""}}' >> $@