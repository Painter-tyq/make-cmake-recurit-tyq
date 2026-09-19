CC = gcc
CFLAGS = -Wall -Wextra -Iinclude

calculator: main.o calculator.o logger.o
	$(CC) main.o calculator.o logger.o -o calculator

main.o: src/main.c include/calculator.h include/logger.h
	$(CC) $(CFLAGS) -c src/main.c -o main.o

calculator.o: src/calculator.c include/calculator.h
	$(CC) $(CFLAGS) -c src/calculator.c -o calculator.o

logger.o: src/logger.c include/logger.h
	$(CC) $(CFLAGS) -c src/logger.c -o logger.o

.PHONY: clean
clean:
	rm -f calculator main.o calculator.o logger.o

