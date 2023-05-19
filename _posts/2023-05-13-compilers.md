---
layout: post
title: "Interpreters"
date: 2023-05-13
published: false
---

## XXX
Translate expressions written in a high level language that's normally easier to use or closer to the user use case into a lower level language.
- Compilers normally go all the way and produce an binary executable that can be run directly by the computer. We have C in this case.
- On the other spectrum we have interpreters which just translate the expressions and statements into a data structure that can be interpreted "on the fly" by a compiler language. In this case we have ruby java.
- In reality this is an expectrum specially with new technologies like "just-in-time" compilation things have become a spectrum

## Getting started
One of the most common features for any programming language is be able to handle numeric expressions such as `2-3*3`. Without any context in the field my first instict would be to do something like this:

```ruby
def compile(source, idx, result)
  return result if idx >= source.size
  
  symbol = source[idx]
  case symbol
  when /[0-9]/
    result = compile(source, idx + 1, symbol.to_i)
  when '*'
    result *= compile(source, idx + 1, result)
  when '-'
    result -= compile(source, idx + 1, result)
  end

  result
end

puts compile("2-3*3", 0, 0)
```
(little blurb about how the algo works, maybe some graphics as well)

For this very specific numeric expression this naive algorithm returns the correct result `-7` but there are many glaring problems with the solutions. Let's list some of them:
- It does not respect order of precedence. The algorithm naively goes all the way to end and then starts recurring back to calculate the result. I intentionally placed the higher level precedence operations `*` at the end but a reverse expression e.g `2*3-3` results in `0` which is obviously wrong.
- It does not account for associativity. Because of the same reason mentioned above this algorightm does not handle associatiy correctly for the example the following expression `3-5-6` results in `4` because we're associating `5-6` => -1 and then applying the other operation `3-(-1) which results in 4. But negation is a left associative expression it should do the opposite for instance it should `3-5` => -2 and then apply (-2) - 6 => -8 which is the correct result
- Finally, well this solution is too simplistic it does not handle white spaces on our expression, multi-digit numbers and it's not very clear how we would go about extending it to accomodate other type of expressions and statements such as loops, conditionals, functions etc.

(Some link paragraph in here)

In general terms the field has been split into 3 main areas: lexing (scanning), parsing, evaluation.
- Lexing: has to do with identifying and extracting the "tokens" that make up the language. In our example above we were processing different types of characters we had numeric characters and math operation ("*", "-"). In a regular production grade langugage we would have many more types e.g If tokens, while tokens, strings tokens etc. Normally, the lexer is used by the parser to producer tokens on demand as they are needed. A lexer would also prune unnecessary characters that would not have any use during the interpresation of the language such as blank spaces, comments.

Parsing: Arranges the flat steam tokens produced by the lexer into a data structure that can better express the meaning of our expressions and statments. Let's take our previous example with the following `2*3-3` you can imagine that the lexer would produce a 

```ruby
# Token stream for 2*3-3
[Token::NUMBER(2), Token::STAR, Token::NUMBER(3), Token::MINUS, Token::NUMBER(3)]
```

but as we saw above just having a flat stream of tokens does not tell us much about how these should be evaluated, th calculation is ambigous and both results `0` and `3` are possible. A more useful representation would be a binary tree for instance:

[Insert tree]

The middle nodes corresponds to the operation to either leaf or other operation nodes. This reprensentation is not ambigious anymore because we can just evaluate the tree bottom up and it will give us the right result! In essense the parser job is to make sense of the stream of tokens and build a data structre that can be interpreted by the following steps. This expression tree is commonly known as Abstract Syntax Tree.

Interpreter: Finally, we have the interpreter which in this very simple example would just need to walk the resulting tree resulting from the parser to evaluate the corresponding expression. This can be accomplish with a stardard tree algorithm such as depths for search.

```ruby
def interpret(ast)
  case ast.type
  when Token::NUMBER
    return ast.value
  when Token::STAR
    return interpret(ast.left) *  interpret(ast.left)
  when Token::MINUS
    return interpret(ast.left) -  interpret(ast.left)
  end
end
```

It's worth noting that the parser does not necessarily have to always build AST, and in fact many modern interpreters / compilers doe not use this model of computation since it's not performance enough for production grade languages. Instead the could for instance "compile" the initial expression to a byte code that can be interpret (or processed) by a virtual machine, etc.

Uff with that out of the way let's 

## Everything is just code

It all comes down tranforming some human readable text into something that machines can understand. The process of tranlation is not standard meaning that you can go as 
- deep as you want going from a high level language all the way to the machine code
- and as wide as you want by coding a domain specific language all the way to  general purpose language

Also like in any other domain there are already tools (or stacks) that can help you to speed up the process and build production ready (e.g more error free) code.

For the purpose of this series I'll be doing targeting a limited subset of the lox language that way if you have a reference of the and while the content I'll is similar I'll be going around explaining in a different way.
- Arithmethic
- Control flow
- Scope

Let's start with the minimum amount of computation that I can: "8*4+3". Our goal can be broken down into these different steps:
- extract the numbers from the string
- identify the operations
- calculate the result





