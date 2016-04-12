---
layout: post
title: Lisp lexer
summary: Writing a simple clojure lexer from scratch in Haskell
status: done
hn-discussion:
tags: haskell, parser, programing
---
# Introduction
By the end of this tutorial you will be able to build for yourself a faily basic lexer in haskell for clojure.

** This article assumes familiarity with basic haskell **

# Lexer

The job of the lexer is to take a stream of characters and convert them into "tokens".

The lexer doesnt ensure the gramatical correctness of the given input.
That is the job of our parser.

So first we define the list of tokens we want in the language:

{% highlight haskell %}
-- define tokens
data Tok = TokOB
            | TokCB
            | TokPlus
            | TokMinus
            | TokMult
            | TokDiv
            | TokNum Int
            | TokTrue
            | TokFalse
            | TokGT
            | TokLT
            | TokAnd
            | TokOr
            | TokEq
            | TokIf deriving (Show, Eq)
{% endhighlight %}

We state the obvious now, which is the type signature of of our lexer:

{% highlight haskell %}
-- lexer
lexer :: [String] -> [Tok]
{% endhighlight %}

The input to the lexer are words, i.e Strings based on space separated input form the user input.
for example: `(+ 2 2)` will become `["(", "2", "2", ")"]`.

Yes we will have to ensure the paranthesis and + are not treated as one word, we shall get to that later.
for now assume the input is space separated as we want it to be.

And now we define the lexer function.
The job of this function will be to read one part of the input list of strings and generate the relevant token for it.

{% highlight haskell %}
lexer [] = []
lexer (x:xs) | isNumber x   = (TokNum (read x)) : lexer xs
             | x == "("     = TokOB    : lexer xs
             | x == ")"     = TokCB    : lexer xs
             | x == "+"     = TokPlus  : lexer xs
             | x == "-"     = TokMinus : lexer xs
             | x == "*"     = TokMult  : lexer xs
             | x == "/"     = TokDiv   : lexer xs
             | x == "if"    = TokIf   : lexer xs
             | x == "True"  = TokTrue : lexer xs
             | x == "False" = TokFalse : lexer xs
             | x == ">" = TokGT : lexer xs
             | x == "<" = TokLT : lexer xs
             | x == "==" = TokEq : lexer xs
             | x == "and" = TokAnd : lexer xs
             | x == "or" = TokOr : lexer xs
             | otherwise = error $ "unrecognised token::" ++ x
             where isNumber = all isDigit

{% endhighlight %}

We can implement that easily using recursion.

After doing this we shall have a list of tokens which will be suitable as a input to a parser.
