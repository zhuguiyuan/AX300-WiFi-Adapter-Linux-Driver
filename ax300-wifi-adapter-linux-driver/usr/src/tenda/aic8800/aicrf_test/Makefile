LDFLAGS := -lpthread

default: all

all: wifi_test bt_test

wifi_test: wifi_test.o
	$(CROSS_COMPILE)gcc $+ -o $@

bt_test: bt_test.o
	$(CROSS_COMPILE)gcc $+ $(LDFLAGS) -o $@

%.o: %.c
	$(CROSS_COMPILE)gcc -c $+ -o $@

clean:
	rm *.o wifi_test bt_test

install:
	sudo cp wifi_test /sbin
	sudo cp bt_test /sbin

uninstall:
	sudo rm /sbin/wifi_test
	sudo rm /sbin/bt_test
