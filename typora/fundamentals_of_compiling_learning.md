### Introduction

##### language processor

- compiler: translate the code from source language to target language.
- interpreter: Perform operations in the source language directly from the input
- **example**: java processor:
  source language -> translator -> middle program -> virtual machine
                                                                                  input /                              \ output
- a language process system:
  source language -> pre-processor -> pre-processed program  -> compiler -> target assembler -> assembler -> relocatable machine code -> linker(resolves external memory addresses)&loader(execute all executable target files in memory) <- library files & relocatable files,     and finally generate target machine code from linker and loader.