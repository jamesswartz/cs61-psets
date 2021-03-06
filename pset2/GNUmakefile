TESTS = cat61 blockcat61 randblockcat61 gather61 scatter61 reverse61 \
	reordercat61 stridecat61 ostridecat61 pipeexchange61
STDIOTESTS = $(patsubst %,stdio-%,$(TESTS))
SLOWTESTS = $(patsubst %,slow-%,$(TESTS))

# Default optimization level
O ?= 2

all: tests stdio
	@echo "*** Run 'make check' to check your work."

tests: $(TESTS)
stdio: $(STDIOTESTS)
slow: $(SLOWTESTS)

-include build/rules.mk

%.o: %.c io61.h $(BUILDSTAMP)
	$(call run,$(CC) $(CPPFLAGS) $(CFLAGS) -O$(O) $(DEPCFLAGS) -o $@ -c,COMPILE,$<)

$(TESTS): %: io61.o profile61.o %.o
	$(call run,$(CC) $(CFLAGS) -o $@ $^ $(LDFLAGS) $(LIBS),LINK $@)

$(SLOWTESTS): slow-%: slow-io61.o profile61.o %.o
	$(call run,$(CC) $(CFLAGS) -o $@ $^ $(LDFLAGS) $(LIBS),LINK $@)

$(STDIOTESTS): stdio-%: stdio-io61.o profile61.o %.o
	$(call run,$(CC) $(CFLAGS) -o $@ $^ $(LDFLAGS) $(LIBS),$(STDIO_LINK_LINE))
	@echo >$(DEPSDIR)/stdio.txt

text20meg.txt:
	echo > text20meg.txt
	while perl -e "exit((-s 'text20meg.txt') > 20000000)"; do cat /usr/share/dict/words >> text20meg.txt; done

clean: clean-main
clean-main:
	$(call run,rm -f $(TESTS) $(SLOWTESTS) $(STDIOTESTS) *.o core *.core,CLEAN)
	$(call run,rm -rf files $(DEPSDIR))
distclean: clean

check:
	MAKE=1 perl check.pl

check-%:
	MAKE=1 perl check.pl $(subst check-,,$@)

.PRECIOUS: %.o
.PHONY: all tests stdio slow \
	clean clean-main distclean check check-% prepare-check
