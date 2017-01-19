# HYACC 

Hyacc 0.98 new features added by: Perry Smith  
On: January 1, 2017  
See: https://github.com/pedz/HYAC


HYACC is a full LR(1) parser generator that can also do LALR(1) and
LR(0) as well as LR(k) for limited cases.

Hyacc is pronounced as "HiYacc", means Hawaii Yacc.  The original web
page describing HYACC is here: http://hyacc.sourceforge.net/

## Origin

The initial commit in this GIT repository is the unaltered code from
the HYACC 0.97 source from
https://sourceforge.net/projects/hyacc/files/hyacc%200.97/hyacc_unix_src_01_27_2011.tar.gz/download

## New features added in Hyacc 0.98 by Perry Smith

As I mentioned, I started with your 0.97 tarball and put it into the git 
repository so the git history has all of my changes in it that you can review.  
I will try to remember the major points:

1) Got %union as well as %type, %left, and the others to work.  This includes several steps.

There are two parses of the input language — one in gen_compiler.c and 
the other in get_yacc_grammar.c.  This is sort of unfortunate because 
the knowledge in get_yacc_grammar.c is needed in gen_compiler.c to properly 
understand the types of the terminals and non-terminals.  
The major stumbling block is what you call “mid production rules”.

When the code of the mid production rule is found in gen_compiler, 
“rule_count” is the index for the mid production rule.  This does not 
have the correct LHS.  It is a made up name of (e.g.) $$123@4 with no type.  
The RHS is empty.  So understanding the type for $$ or $&lt;digit> is hard.

What I did was I wrote some helper routines.  find_full_rule starts at 
rule_count and looks at later rules (rule_count + 1, …) until it finds a 
rule where the LHS does not start with “$$”.  I call this the “full rule” — 
because it contains the LHS plus the complete list of RHS symbols.

The second helper is called find_mid_prod_index.  This is hard to explain.  
Lets say there is a rule like:

dog : apple banana { temp = $1; } dog { blah = $4; } ;

With the original code, when the { temp = $1 } code was interpreted, 
rule_count pointed to the mid production rule.  It has a RHS_count of 0.  
So the result was yypvt[1 - 0] or yypvt[1] where the 1 comes from the $1 
and the 0 comes from the RHS_count of the mid production rule.  
But this is incorrect.

The correct index in this case would be yypvt[-1] — (I believe).  It took me 
a lot of tries to figure it out.  The way I imagine it the code is in index 2 
(apple is at RHS index 0, banana is at index 1, { temp = $1; } is at index 2, 
and dog is at index 3, and the { blah = $4; } code after dog would be at index 4.  
Note ... usually, you use RHS_count when the { blah = $4; } code is interpreted w
hich would also have a value of 4 in this case.

find_mid_prod_index walks down the list of RHS symbols for the full rule until it 
finds the production whose name matches the name of the mid production rule that 
rule_count points to and returns the index.  In our example, for the 
{ temp = $1; } code, it would return 2.  Now we get the correct value with 
yypvt[1 - 2] yielding yypvt[-1] where the 1 is the same as before from the 
$1 and the 2 comes from the index from the result of find_mid_prod_index.

Based upon my experiments, I think this is correct.

I also got the odd $<val>1 syntax to work.

2) I came across a problem with YYERROR.  The problem is that YYERROR is 
defined as a goto yyerrlab.  I’ve used that in some of my rules.  The rules 
are put into a function called yyparse while the yyerrlab label is in a 
function called yyerror_handler.  So, the compiler barfs.

I think the place the label should be is here (in yyparse):

     ...
    } else if (yyaction == YYNOACTION) { /* no action found. error */
 yyerrlab: /* add label here */
      yyerr_hdl = yyerror_handler(yystate); 
      if (yyerr_hdl == -1) break; /* YYABORT; */
      else if (yyerr_hdl == 1) continue; /* eat a token. */

    } else if (yyaction < 0) { /* is reduction */
     ...

I had to go review what / when YYERROR is used.  Its used when the 
input stream matches the grammar but you don’t really want the grammar to 
match due to semantic reasons.  One place I use it is if the user tries 
to initialize a typedef (which is illegal).

As I mentioned, I wrote this grammar back in ’84 time frame.  Since then 
I’ve read various opinions on this topic and it may be better just to remove 
the YYERROR from my grammar but, I’m sure there will be other folks who want 
to use the construct.

I hope to play with the error handling a bit and make sure this is the proper 
place to put the label but I’ve not done that yet.

3) I’ve moved and tweaked various things in hyaccpar to solve some compiler 
complaints.  Right now, the code produced for my two grammars “works” — it 
compiles and appears to executes correctly.

I just noticed a list in your original page titled "What's not working and to be implemented:" 
— a couple by the same person: Vladimir Lidovski.  I noticed he is playing with Ruby’s grammar.  
You might contact him if you can and let him know that I’ve fixed a few of his issues.  
As far as a rule that starts with an action — I need to go try that sometime.  
The ‘ ‘ as a terminal I fixed.  So, as best I know, three of the five issues listed are fixed.

Please let me know your thoughts.  I plan to keep working on my program dex and may 
bump into more issues with HYACC but right now I think its working for me.
