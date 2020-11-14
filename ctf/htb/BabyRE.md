# BabyRE

## Keynotes

* file (command)
* strings
* objdump
* ltrace

## Tracking

1. Run `file` on the unarchived resource and obtained found out that its an *ELF* file

2. Tried to apply strings and obtained a fake base64 encoded flag

3. Tried some dissassembly, obtained something, but wasn't able to read it 😞

```
objdump -d -M intel baby program.asm
```

4. Decided to run it with some kind of debugger to see each step, and read about ltrace

```
ltrace ./baby
```

5. After inserting a bad string, the dissassembled code is written: `strcmp("asdsd\n", "abcde122313\n")`

6. Executed the program, inserted `abcde122313` and obtained the flag

7. Observed that the key was also outputed by strings in the initial step 😀