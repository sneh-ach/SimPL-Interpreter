
# SimPL Interpreter: Implementation Report

**Author**: Sneh Acharya  
**Project URL**: [GitHub Repository](https://github.com/sneh-ach/SimPL-Interpreter/)

---

## Introduction

The SimPL interpreter is a Java-based implementation for a simplified, ML-inspired programming language. SimPL supports functional and imperative programming constructs, including type checking, expression evaluation, and environment management. The interpreter employs a robust type system with features like polymorphic typing, pairs, lists, and lambda calculus expressions.

---

## Project Structure and Execution

### Source Code Structure
The project is divided into three main packages:
- `src/simpl/parser/`: Handles syntax and parsing rules, transforming code into an Abstract Syntax Tree (AST).
- `src/simpl/interpreter/`: Manages runtime evaluation, including `Value` classes, the environment, and built-in functions.
- `src/simpl/typing/`: Implements SimPL’s type system for static type checking and inference.

### Running the Interpreter
To execute a SimPL program:
1. Compile the source code into a `.jar` file (e.g., `SimPL.jar`).
2. Run the `.jar` file with a program file:
   ```bash
   java -jar SimPL.jar ./factorial.spl
   ```
   **Expected output**:
   ```plaintext
   int
   24
   ```

---

## Lexical Definition, Syntax, and Typing Rules

### Lexical Definition and Syntax
SimPL uses the following lexical rules:
- **Atoms**:
  - Integer literals: Whole numbers in decimal form.
  - Identifiers: Alphanumeric names beginning with a letter.
- **Keywords**: `if`, `then`, `else`, `let`, `in`, `end`, `fn`, `rec`, `ref`, `true`, `false`, etc.
- **Operators**: `+`, `-`, `*`, `/`, `:=`, `::`, `andalso`, `orelse`.

SimPL’s parser processes these elements into an AST for evaluation and type safety.

### Typing Rules
The type checker enforces constraints to ensure expressions are well-typed:
- **Arithmetic Types**: Operands of arithmetic expressions must be integers.
- **Boolean Types**: Logical expressions require boolean operands.
- **Function Types**: Lambda expressions specify parameter and return types.
- **Polymorphic Types**: Enable generality in expressions like pairs.
- **Equality Types**: Allow comparisons only on types that support equality.

---

## Semantic Structure: Environment, Memory, and Pointer

### Environment, Memory, and Pointer
- **Environment (`Env`)**: Maps variable names to values, supporting scope-based resolution.
- **Memory (`Mem`)**: Emulates storage for imperative constructs.
- **Pointer (`p`)**: Tracks available memory locations.

### Semantic Rules
- **Assignments**: Evaluate the right-hand value and update memory.
- **Lambda Applications**: Bind arguments to function parameters in a new environment.
- **Conditionals**: Evaluate boolean conditions to select branches.

---

## Key Interpreter Components

### Library Function: `fst`
Returns the first element of a pair.

```java
package simpl.interpreter.lib;

import simpl.interpreter.*;
import simpl.parser.*;
import simpl.parser.ast.*;
import simpl.typing.*;

public class fst extends FunValue {
    public fst() {
        super(Env.empty, Symbol.symbol("fst"), getExpr());
    }

    private static Expr getExpr() {
        return new Expr() {
            @Override
            public TypeResult typecheck(TypeEnv E) {
                return TypeResult.of(new TypeVar(true));
            }

            @Override
            public Value eval(State s) {
                PairValue v = (PairValue) s.E.get(Symbol.symbol("fst"));
                return v.v1;
            }
        };
    }
}
```

---

### Primitive Computable Function: `iszero`
Checks if an integer is zero and returns a boolean.

```java
package simpl.interpreter.pcf;

import simpl.interpreter.*;
import simpl.parser.*;
import simpl.parser.ast.*;
import simpl.typing.*;

public class iszero extends FunValue {
    public iszero() {
        super(Env.empty, Symbol.symbol("iszero"), getExpr());
    }

    private static Expr getExpr() {
        return new Expr() {
            @Override
            public TypeResult typecheck(TypeEnv E) {
                return TypeResult.of(new IntType());
            }

            @Override
            public Value eval(State s) {
                IntValue v = (IntValue) s.E.get(Symbol.symbol("iszero"));
                return v.n == 0 ? new BoolValue(true) : new BoolValue(false);
            }
        };
    }
}
```

---

## Values

### `IntValue`
Represents integers in SimPL.
```java
public class IntValue extends Value {
    public final int n;
    public IntValue(int n) { this.n = n; }
}
```

### `PairValue`
Stores pairs of values.
```java
public class PairValue extends Value {
    public final Value v1, v2;
    public PairValue(Value v1, Value v2) { this.v1 = v1; this.v2 = v2; }
}
```

---

## Testing and Sample Programs

### Factorial Function
A recursive function demonstrating SimPL’s type safety and recursion handling:
```simpl
let fact = rec f => fn x => if x=1 then 1 else x * (f (x-1)) in fact 4 end
```

### Conditional Expressions
Tests conditional functionality:
```simpl
if iszero(0) then 1 else 2
```

---

## Conclusion

This report details the SimPL interpreter’s implementation, covering every major aspect, from lexical and syntax rules to type inference and evaluation. Each component demonstrates how Java’s object-oriented features can build a reliable, type-safe interpreter for a functional language.
