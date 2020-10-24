# cs61c-toolchain
Brian's custom compilation of tools for UC Berkeley's CS 61C: Great Ideas in Computer Architecture (Machine Structures)

## Using Venus Debugger
```
java -jar tools/venus.jar . -dm
```
or 
```
java -jar tools/venus.jar . -dm &
```

Then navigate to the online venus website to debug your RISC-V program [here](https://venus.cs61c.org/)!

https://venus.cs61c.org/

Make sure to mount on the online debugger as well.
```
mount local vmfs
```


## Using Logisim
```
java -jar tools/logisim-evolution.jar
```
or
```
java -jar tools/logisim-evolution.jar <path to .circ file>
```