# jsonc-parser
Scanner and parser for JSON with comments.

[![npm Package](https://img.shields.io/npm/v/jsonfile.svg?style=flat-square)](https://www.npmjs.org/package/jsonfile)

Why?
----
JSONC is JSON with JavaScript style comments. This node module provides a scanner and fault tolerant parser that can process JSONC but is also useful for standard JSON.
 - the *scanner* tokenizes the input string into tokens and token offsets
 - the *parse* function evaluates the JavaScipt object represented by JSON string in a fault tolerant fashion.
 - the *visit* function implements a 'SAX' style parser with callbacks for the encountered properties and values

Installation
------------

    npm install --save jsonc-parser
    
    
API
---

### Scanner:
```typescript

/**
 * Creates a JSON scanner on the given text.
 * If ignoreTrivia is set, whitespaces or comments are ignored.
 */
export function createScanner(text:string, ignoreTrivia:boolean = false):JSONScanner;
    
/**
 * The scanner object, representing a JSON scanner at a position in the input string.
 */
export interface JSONScanner {
    /**
     * Sets the scan position to a new offset. A call to 'scan' is needed to get the first token.
     */
    setPosition(pos: number): any;
    /**
     * Read the next token. Returns the tolen code.
     */
    scan(): SyntaxKind;
    /**
     * Returns the current scan position, which is after the last read token.
     */
    getPosition(): number;
    /**
     * Returns the last read token.
     */
    getToken(): SyntaxKind;
    /**
     * Returns the last read token value. The value for strings is the decoded string content. For numbers its of type number, for boolean it's true or false.
     */
    getTokenValue(): string;
    /**
     * The start offset of the last read token.
     */
    getTokenOffset(): number;
    /**
     * The length of the last read token.
     */
    getTokenLength(): number;
    /**
     * An error code of the last scan.
     */
    getTokenError(): ScanError;
}
```

### Parser:
```typescript
/**
 * Parses the given text and returns the object the JSON content represents. On invalid input, the parser tries to be as fault tolerant as possible and still return a result.
 * Therefore always check the errors list to find out if the input was valid.
 */
export declare function parse(text: string, errors?: {
    error: ParseErrorCode;
}[]): any;

/**
 * Parses the given text and invokes the visitor functions for each object, array and literal reached.
 */
export declare function visit(text: string, visitor: JSONVisitor): any;
export interface JSONVisitor {
    /**
     * Invoked when an open brace is encountered and an object is started. The offset and length represent the location of the open brace.
     */
    onObjectBegin?: (offset: number, length: number) => void;
    /**
     * Invoked when a property is encountered. The offset and length represent the location of the property name.
     */
    onObjectProperty?: (property: string, offset: number, length: number) => void;
    /**
     * Invoked when a closing brace is encountered and an object is completed. The offset and length represent the location of the closing brace.
     */
    onObjectEnd?: (offset: number, length: number) => void;
    /**
     * Invoked when an open bracket is encountered. The offset and length represent the location of the open bracket.
     */
    onArrayBegin?: (offset: number, length: number) => void;
    /**
     * Invoked when a closing bracket is encountered. The offset and length represent the location of the closing bracket.
     */
    onArrayEnd?: (offset: number, length: number) => void;
    /**
     * Invoked when a literal value is encountered. The offset and length represent the location of the literal value.
     */
    onLiteralValue?: (value: any, offset: number, length: number) => void;
    /**
     * Invoked when a comma or colon separator is encountered. The offset and length represent the location of the separator.
     */
    onSeparator?: (charcter: string, offset: number, length: number) => void;
    /**
     * Invoked on an error.
     */
    onError?: (error: ParseErrorCode, offset: number, length: number) => void;
}


```

### Utilities:
```typescript
/**
 * Takes JSON with JavaScript-style comments and remove
 * them. Optionally replaces every none-newline character
 * of comments with a replaceCharacter
 */
export declare function stripComments(text: string, replaceCh?: string): string;

/**
 * For a given offset, evaluate the location in the JSON document. Each segment in a location is either a property names or an array accessors.
 */
export declare function getLocation(text: string, position: number): Location;
```


License
-------

(MIT License)

Copyright 2016, Microsoft