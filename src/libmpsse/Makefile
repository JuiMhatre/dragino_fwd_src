CC=gcc
SWIG=
PYDEV=
PYLIB=
SONAME=-soname
FRAMEWORK=
BUILD=
LDFLAGS=-lftdi1 
CFLAGS=-Wall -fPIC -fno-strict-aliasing -O2
LIBFTDI1=1
prefix=/usr/local
exec_prefix=${prefix}
LIBDIR=${exec_prefix}/lib
INCDIR=${prefix}/include
INC=-I$(PYDEV)
TARGET=mpsse

all: $(TARGET) py$(BUILD)-build

$(TARGET): mpsse.o fast.o
	$(CC) $(CFLAGS) -shared $(TARGET).o fast.o support.o \
		-o lib$(TARGET).so $(LDFLAGS)

example-code:
	make -C examples

mpsse.o: support.o
	$(CC) $(CFLAGS) $(LDFLAGS) -DLIBFTDI1=$(LIBFTDI1) -c mpsse.c

fast.o: support.o
	$(CC) $(CFLAGS) $(LDFLAGS) -c fast.c

support.o:
	$(CC) $(CFLAGS) $(LDFLAGS) -DLIBFTDI1=$(LIBFTDI1) -c support.c

pyswig-build:
	$(CC) $(CFLAGS) $(LDFLAGS) -DSWIGPYTHON -DLIBFTDI1=$(LIBFTDI1) -c support.c
	$(CC) $(CFLAGS) $(LDFLAGS) -DSWIGPYTHON -DLIBFTDI1=$(LIBFTDI1) -c mpsse.c
	$(SWIG) -python $(TARGET).i
	$(CC) $(CFLAGS) -c  -DLIBFTDI1=$(LIBFTDI1) $(TARGET)_wrap.c  $(INC)
	$(CC) $(CFLAGS) -shared $(FRAMEWORK) $(TARGET)_wrap.o mpsse.o support.o \
		-o _pylib$(TARGET).so $(LDFLAGS) $(INC)

pyswig-install:
	install -D -m644 pylib$(TARGET).py  $(DESTDIR)/$(PYLIB)/pylib$(TARGET).py
	install -D -m644 _pylib$(TARGET).so $(DESTDIR)/$(PYLIB)/_pylib$(TARGET).so
	install -D -m644 $(TARGET).py       $(DESTDIR)/$(PYLIB)/$(TARGET).py

pyswig-uninstall:
	rm -f $(DESTDIR)/$(PYLIB)/$(TARGET).* \
		$(DESTDIR)/$(PYLIB)/pylib$(TARGET).* \
		$(DESTDIR)/$(PYLIB)/_pylib$(TARGET).*

# Dummy rules for when $BUILD == "" (if --disable-python was passed to ./configure)
py-build:
py-install:
py-uninstall:

install: py$(BUILD)-install
	install -D -m644 lib$(TARGET).so $(DESTDIR)/$(LIBDIR)/lib$(TARGET).so
	install -D -m644 $(TARGET).h     $(DESTDIR)/$(INCDIR)/$(TARGET).h

uninstall: py$(BUILD)-uninstall
	rm -f $(DESTDIR)/$(LIBDIR)/lib$(TARGET).so \
		$(DESTDIR)/$(LIBDIR)/lib$(TARGET).a \
		$(DESTDIR)/$(INCDIR)/$(TARGET).h

clean:
	make -C examples clean
	rm -rf *.o *.so *.a _$(TARGET).py* pylib$(TARGET).py* $(TARGET)_wrap.c *.pyc

distclean: clean
	rm -rf *.cache config.* Makefile

