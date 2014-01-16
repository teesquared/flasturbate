flasturbate
===========

A SWF obfuscator.

Where's the source?
-------------------

[Flasturbate][] is part of a fork of RABCDAsm (A Robust ABC Assembler/Disassembler).

You can find the full source here,

[Teesquared's fork of RABCDAsm][]

Follow the "Compiling from source" instruction there and you will also build flasturbate.exe.

  [Flasturbate]: https://github.com/Teesquared/flasturbate
  [Teesquared's fork of RABCDAsm]: https://github.com/Teesquared/RABCDAsm

Usage
-----

[Flasturbate][] is a command line program. You pass in options and the SWF file or files your want to obfuscate.
By default it will produce an output file with all qualified symbols obfuscated.

The program currently only obfuscates symbol names. It does not alter bytecodes to thwart disassembly but it sure makes reading the code in a SWF really hard to understand.

Since it's built on a powerful assembler/disassembler, the future may see a version that does mangle bytecodes.

    D:\projects\github\Teesquared\flasturbate
    >flasturbate -h
    Usage: flasturbate [OPTION] FILE ...
    A tool that let's you play with your swf.
    
    Options:
          --allowDebug               allow enable debugger tags and debug opcodes
      -e, --excludes=FILE            exclude names that match any listed in FILE
          --fart                     don't use this option
      -f, --fixed=FILE               use a fixed renaming for names listed in FILE
          --funny                    this option is undocumented
      -g, --globalFile=FILE          the global file to use (multiple supported, default: "./playerglobal.swc")
      -h, --help                     display this help and exit
      -i, --includes=FILE            include names that match any listed in FILE
      -j, --json                     the symbol name of a json binary tag to process (multiple supported)
      -n, --namePrefix               prefix for each generated name (default: "")
      -o, --outputExt                the output file extension (default: "out")
      -q, --quiet                    do not print renames
      -t, --test                     load a swf, write it back out, and report any inconsistencies
      -v, --verbose                  enable verbose output
          --version                  output version information and exit
    
      "Live long and flasturbate."

Platforms
---------

[Flasturbate][] was developed for and runs under Windows 7 64 bit.
The base engine should work on Macs, but it is untested.
If you are building it on a different platform, use the "--test" option. This will read and write out the SWF.
It reports any inconsistency. So if it works, you're in good shape.

Example
-------

The best way to learn to flasturbate, is to watch someone do it. So here's an example.

Download and build Adam Atomic's [mode][] program. Make sure you build the release version.
It doesn't make sense to obfuscate a debug program so flasturbate will complain if you try to do it.

[mode]: https://github.com/AdamAtomic/Mode

First, make sure the program works by running mode.swf (e.g. double click on it and run it in Flash Player).

If that works, then great. Play the game a little and email Adam and tell him it's too hard (j/k tell him you think it's fun and a great test bed!).

Next, make sure you have a copy of playerglobal.swc. This file defines many global symbols that flasturbate cannot obfuscate.
If you don't use it then classes like "MovieClip" and "Sprite" will get renamed and your program won't work. If flasturbate cannot find a global file, it will fail. You can download the latest playerglobal.swc from Adobe [here](http://www.adobe.com/support/flashplayer/downloads.html).

You can tell flasturbate where your playerglobal.swc is by using the "--globalFile" option or just copy the file to the same directory you run flasturbate from and it will find it by default.

For this example, just copy playerglobal.swc and mode.swf to the same directory where flasturbate.exe is. Then run this,

    D:\projects\github\Teesquared\flasturbate
    >flasturbate mode.swf
    FRM: Preloader => 1t84s0
    EXP: org.flixel.FlxGame_SndBeep => 1t11s0.1t11s1.1t684s0
    ABC: org.flixel => 1t11s0.1t11s1
    ABC: FlxState => 1t12s0
    ABC: FlxSound => 1t16s0
    ABC: FlxCamera => 1t20s0
    ABC: FlxBasic => 1t21s0
    ABC: FlxGame => 1t22s0
    ABC: FlxObject => 1t35s0
    ABC: FlxPath => 1t36s0
    ABC: FlxPoint => 1t37s0
    ABC: FlxRect => 1t38s0
    ABC: FlxTimer => 1t39s0
    ABC: org.flixel.system => 1t11s0.1t11s1.1t41s0
    ABC: FlxQuadTree => 1t42s0
    ABC: org.flixel.system.replay => 1t11s0.1t11s1.1t41s0.1t44s0
    ...

Now you'll see a new file "mode.swf.out" in the same directory. Rename it to my-first-flasturbation.swf (or whatever) and run it...

Congratulations! You just flasturbated. I'm sure it's something you'll never forget :)

So technically it looks like it's working great but there is something wrong. The game saves its state in a shared object file (sol). In this case, it has only one variable it saves which is "plays". You need to tell flasturbate to always obfuscate the variable plays the same way. You can do this by putting it in a text file and using the "--fixed" option.

    D:\projects\github\Teesquared\flasturbate
    >flasturbate -f mode-fixed.txt mode.swf
    ...
    ABC: plays => 1f5
    ...

Now if you make changes and release a new version of the game, when you obfuscate it again, the variable "plays" will always be renamed to the wonderfully descriptive name "1f5."

When you flasturbate, the output looks like a total mess. It's actually very useful to see what it's doing.

So looking at a sample,

    ...
    ABC: org.flixel.system.replay => 1t11s0.1t11s1.1t41s0.1t44s0
    ...

This shows you the tag (a DoABC tag), the symbol name (org.flixel.system.replay), and the obfuscated new symbol name (1t11s0.1t11s1.1t41s0.1t44s0 ... don't you just love the sound of that?).

If you have a problem with a flasturbated SWF, the flash exception dialog will display an obfuscated name. You can refer back to the output of flasturbate to see which source file that comes from.

This is one of the complications of obfuscation in a language like ActionScript that supports reflection. There are situations in your code where the variable name must not change. The shared object variable name above is an example of when it must be a fixed value. There are cases where the variable must not be renamed at all (like MovieClip and Sprite). Flasturbate has an option called "--excludes" where you can list each name you want excluded from obfuscation. Plus you can provide multiple global files (playerglobal or airglobal, RSL SWC(s), and ANE(s)). All symbols in those globals will be excluded as well.

Another example of a problem with obfuscation is when you use json. In ActionScript, when you load the json file, it returns a dynamic object with properties named using the names defined in the json name/value pairs. For example, let's say I have a json file that defines a value named "funny." My ActionScript would try to access it using a property called "funny" on the object returned from the json parser (say "obj.funny"). After obfuscation, the code may turn into "obj.1t12s0" which is clearly not "funny." To solve this problem for json that is embedded as a binary tag in the SWF, you can use the "--json" option and flasturbate will also obfuscate your json data using the same symbol name mapping for the ActionScript code that uses it. Even though it's still not funny, no one thinks it's funny, and it works. Got it? If you load json data dynamiclly at runtime, then you will need to obfuscate it manually using the symbol map that flasturbate outputs or just add all the json names to a text file and use the "--excludes" option.

funny?
------

    D:\projects\github\Teesquared\flasturbate
    >flasturbate --funny
      "Once you flasturbate, you'll never have a fake obfuscation again."

