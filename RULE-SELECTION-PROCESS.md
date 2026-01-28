# Rule Selection from *100 Go Mistakes and How to Avoid Them*

# This document explain why was each rule selected or eliminated

------

## #1 Unintended variable shadowing **(SELECTED)**

Can be a cause of runtime errors, the compiler does not prevent it, resulting in a possible nil reference. Also, can be hard to find in a large codebase.

## #2 Unnecessary nested code **(ELIMINATED)**

Every developer knows that overly nested code is bad, also its much easier to detect since its visual. Sometimes overly nested code is necessary.

## #3 Misusing init functions **(ELIMINATED)**

Even though init functions can be sources of bugs, there would only be 2 possible implementations in my use case. 1: Tell the user to not use init function 2: Transform init function into a normal function. Because of this, there wouldn't be very logical to include it.

## #4 Overusing getters and setters **(ELIMINATED)**

It is only mentioned to not overuse it. Only linting I could provide for it is to respect the naming convention of not having "Get" in the getter.

## #5 Interface polution **(ELIMINATED)**

Its about having too much interface/abstraction. However, this just can't be measured via the source code, without domain knowledge. Only the number of interfaces and its method declarations could be checked.

## #6 Interface on the producer side **(QUESTIONABLE)**

Since interfaces should live in the client side, not restricting the clients, it could be a good choice to include this into the linter. It is also detectable via source code.

## #7 Returning an interface **(QUESTIONABLE)**

Depends if the previous rule is implemented. Checkable via source code, check whether the return is an interface or an implementation.

## #8 any says nothing **(SELECTED)**

any similarly to TypeScript allows to skip static type checking, which is for a statically typed language really bad. Functions that accept/return any data should be refactored into concrete type implementation.

## #9 Being confused about when to use generics **(SELECTED PARTIALLY)**

Provides a general guideline when to use generics and when not. Implementation will focus on one use case, that is when calling a method of the type argument, meaning the linter will warn to swap out generic to a concrete type argument.

## #10 Not being aware of the possible problems with type embedding **(SELECTED PARTIALLY)**

Almost everything could be solved without type embedding, I will add a rule to warn about a specific case - when a mutex is embedded, which could lead to undesired behaviour.

## #11 Not using the functional options pattern **(ELIMINATED)**

Would be extremely hard and complex to implement, also it would cover very minimal amount of use cases.

## #12 Project misorganization **(ELIMINATED)**

Project organization is highly dependant, even though there are some unofficial standards that gained traction over the years, its not worth providing deterministic rules for a highly subjective topic.

## #13 Creating utility packages **(SELECTED)**

Removing packages names like utils, common, shared, etc. could improve clarity and expressiveness.

## #14 Ignoring package name collisions **(SELECTED)**

If package name collides with a variable name, the package in the scope of the variable cannot be accessed, leading to runtime bugs.

## #15 Missing code documentation **(QUESTIONABLE)**

Even though code documentation is really important and can make a whole lot of difference, enforcing it might be annoying for small-medium sized project. It can be added but with default option being toggled off.

## #16 Not using linters **(ELIMINATED)**

## #17 Creating confusion with octal literals **(ELIMINATED)**

Most programmers do know that if an integer starts with a 0 its represents an octal number. This can be added as a warning, but not planned.

## #18 Neglecting integer overflows **(SELECTED)**

This is one of the most common mistakes during arithmetic. However, I will need to research (or develop) a solution to detect possible overflows during static analysis.

## #19 Not understanding floating points **(ELIMINATED)**

Error during very accurate floating point calculations are rare, and people who do them are aware of them.

## #20 Not understanding slice length and capacity **(QUESTIONABLE)**

Maybe some kind of analysis could/should be made which tries to tracks the length/capacity.

## #21 Inefficient slice initialization **(SELECTED)**

Providing length and capacity 99% is a good idea and can also help the Go Garbage Collector to minimize reallocations and copying if the backed array needs to be adjusted.

## #22 Being confused about nil vs. empty slices **(SELECTED PARTIALLY)**

The linter should check if a slice is declared as []string{}, and recommned to change it to []string, since the []string{} will result in a non nil slice.

## #23 Not properly checking if a slice is empty **(SELECTED)**

Instead of checking whether a slice is nil, it should check the length, checkable via source code.

## #24 Not making slice copies corretly **(SELECTED)**

Using the built in copy function can produce runtime bugs, if the destination slice has less length than the source slice. Therefore, the destination slice must have the length >= of the length of the source slice. This rule is similar to rule #21

## #25 Unexpected side effect using slice append **(SELECTED)**

Since slices can share a backed array, changing one slice can result in unexpected change in another slice, which can also be hard to detect. This is what rust solved by not allowing shared mutable ownership. 

