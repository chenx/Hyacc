hyacc
=====

What's New?
-----

Hyacc 0.97 is released on January 27, 2011.

What is Hyacc, and why it is special
-----

Many people have used Yacc. It is a LALR(1) parser generator, often used with the lexical analyzer Lex to create compilers. There are many variations of Yacc, most famous ones are like Bison and Byacc (Berkely yacc). The problem with LALR(1) parser generators is that they contain "mysterious" reduce/reduce conflicts even for deterministic and unambiguous grammars, and these require the language designer to tweak the grammar which is often a time-consuming and painful process, and the resulted grammar may not be the same as before modification.

Hyacc is similar to Yacc in that it is a parser generator. It is different from yacc in that it is a LR(1)/LALR(1)/LR(0) parser generator, which means it can accept all LR(1) grammars. This is more powerful than LALR(1) algorithm. A LR(1) parser generator also does not have the "mysterious reduce/reduce conflict" problem. Hyacc also supports LR(k) for limited cases.

Hyacc is pronounced as "HiYacc", means Hawaii Yacc.

Specifically, based on the original Knuth LR(1) algorithm, three optimizations are used:

    LR(1) parser generation by combining compatible states.
    Remove unit productions.
    Further remove repeated states after the previous step. 

Besides, Hyacc also implemented:

    LR(1) parser generation by lane-tracing.
    LALR(1) based on lane-tracing.
    LR(0).
    A LR(k) algorithm that works for limited cases. 

Hyacc is compatible with Yacc and Bison in the format of input file and command line switches. So anyone used Yacc or Bison before should be able to start using Hyacc immediately.

Hyacc is ANSI C compliant, and can be easily ported to other platforms.

Features
-----

Current features:

    Implements the original Knuth LR(1) algorithm.
    Combines compatible states using the concept of weak compatibility [1].
    Removes unit productions [2].
    Removes repeated states after removing unit productions.
    Implements the LR(1) lane-tracing algorithm.
    Supports LALR(1) based on the lane-tracing algorithm [3][4].
    Supports LR(0).
    Supports LR(k) for limited cases.
    Allows empty production.
    Allows mid-production actions.
    Allows these directives: %token, %left, %right, %expect, %start, %prec.
    In case of ambiguous grammars, uses precedence and associativity to resolve conflicts. When unavoidable conflicts happen, in case of shift/reduce conflicts the default action is to use shift, in case of reduce/reduce conflicts the default is to use the production that appears first in a grammar.
    Is backward compatible to yacc and bison in the ways of input file format, ambiguous grammar handling, error handling and output file format.
    If specified, can generate a graphviz input file for the parsing machine.
    If specified, the generated compiler can record the parsing steps in a file.
    Works together with Lex and Flex.
    Is ANSI C compliant.
    Rich information in debug output. 

What's not working and to be implemented:

    Hyacc requires that all the "%%" and "%..." directives start from the first column of the line. This restriction should be removed later.
    Hyacc is not reentrant.
    Hyacc does not support these Yacc directives: %nonassoc, %union, %type.
    Does not handle ' ' (space between apostrophes) terminal (Vladimir Lidovski, 10/10/2010)
    Does not handle grammars which begin with actions, for example, program: {ACTIONS} top_compstmt ... (see ruby.y) (Vladimir Lidovski, 10/10/2010) 


Download
-----

http://hyacc.sourceforge.net/

Hyacc can be compiled and used in Unix, Linux and Windows, or any system that supports ANSI C.

Documentations (included in the source packages): readme.pdf (http://hyacc.sourceforge.net/readme97.pdf),  
hyaccmanpage (http://hyacc.sourceforge.net/hyaccmanpage97.html)

Source package: Hyacc project page (http://sourceforge.net/projects/hyacc/). Or directly go to the download page (http://sourceforge.net/project/showfiles.php?group_id=215418).

License
-----

Hyacc source code is released under the GPL license.

The only exception is the parser engine file "hyaccpar", which is released under the BSD license. This guarantees that the generated parser can be used in both open source and commercial softwares. A similar situation was addressed by Richard Stallman in the release notes of Bison 1.23 and 1.24.

Author
-----
X. Chen
Copyright @ 2006 - 2014
