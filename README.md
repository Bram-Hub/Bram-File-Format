
dotBram - Bram File Format
===


## What?

dotBram is an expressive, standardized, and human-readable XML standard for a wide-range of logical applications, originally developed for [Aris](https://github.com/Bram-Hub/Aris/) for use in all [Bram-Hub](https://github.com/Bram-Hub/) projects. 

## Why?

A standard file format is needed for Logic software to make the various projects more compatible with each other. Ideally, this file format would be somethign that is human-readable, in order to allow it to be easily parsed. Finally, this file format needs to be expressive in order to actually represent various logical proofs. There are currently no file formats that fulfill all of these qualities for logic software; The Fitch, coq and Slate formats all have issues that make them suboptimal as a standard file format. Thus the .bram file format has been made to fulfill the need for a standard for Logic softare.

## The Standard

### Specifications
`<Program>` : Name of the software that originally created the file.
`<Version>` : Version of software that created the file
`<author>` : File Author
`<created>` : Date file created as ISO 8601 in UTC
`<modified>` : Date file last modified as ISO 8601 in UTC
`<hash>` : (Optional) WARNING DEPRECATED. The hash value is recommended to be deprecated in the new version of dotBram due to the complexity of implemenation and the questionable use for an in-file integrity check. It is the sha256sum of the file with <hash>...</hash> removed + the authors_list. 
`<proof id="">` : This is the proof object given a unique positive integer id. Within in the proof object are the following:
* `<assumption linenum="">` : (Optional) Basic assumption for the proof. `linenum` is a unique global positive integer that serves as the universal identifier for assumptions, steps, goals, etc. in the file. Contains the following items:
    * `<raw>` : Raw user input to be parsed using the [libaris](https://github.com/Bram-Hub/Aris/) parser to produce an in-memory AST.
    * `<sen>` : (Optional) WARNING DEPRECATED. Polish notation of the abstract syntax tree. Recommended to be deprecated in favor of using `<raw>` to store information so there is no need to implement an additional parser for an in-memory AST.
* `<step linenum="">` : (Optional) This contains the following items that describe a step in the proof. Uses universal `linenum`. 
    *  `<raw>` : (Optional) Raw user input to be parsed using the [libaris](https://github.com/Bram-Hub/Aris/) parser to produce an in-memory AST.
    * `<sen>` : (Optional) WARNING DEPRECATED. Polish notation of the abstract syntax tree. Recommended to be deprecated in favor of using `<raw>` to store information so there is no need to implement an additional parser for an in-memory AST.
    * `<rule>` : Logical rationale for `<raw>`
    * `<premise>` : (Optional) `linenum` or proof `id` (in the case of a `SUBPROOF`) values for premises used to reach the conclusion in `<raw>` per the `<rule>`
* `<goal>` : (Optional) Goal of the proof, contains the following:
    *  `<raw>` : Raw user input to be parsed using the [libaris](https://github.com/Bram-Hub/Aris/) parser to produce an in-memory AST.
    * `<sen>` : (Optional) WARNING DEPRECATED. Polish notation of the abstract syntax tree. Recommended to be deprecated in favor of using `<raw>` to store information so there is no need to implement an additional parser for an in-memory AST.

`linenum`: The `linenum` is the most important part of the dotBram standard. It is the unique identifier for most items in a proof. It should be incremented in a way that makes visual sense to the user, rather than chronological in the dotBram file, i.e. lines should be incremented in the order that they will appear to the user. 

```xml=
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<bram> <!-- Document Root -->
    <Program>My Logic Program</Program>
    <Version>1.0</Version>
    <metadata>
        <author>Bram van Heuveln</author>
        <created>2019-04-25T08:00:00Z</created>
        <modified>2019-04-25T15:23:59Z</modified>
        <hash>qShJgPT/YKjyO0xVNrelUQrPt7Yofmx8wklDk88Y8Ms=</hash> 
        <!-- Note: hash is deprecated. See Specifications -->
    </metadata>
    
    <proof id="0">
        <assumption linenum="0">
            <sen>(P \/ Q)</sen>
            <!-- Note: sen is depcrated in favor of raw. See Specifications -->
            <raw>P v Q #This line means P or Q</raw>
        </assumption>
        <assumption linenum="1">
            <raw>R</raw>
        </assumption>
        <goal>
            <sen>R</sen>
            <!-- Note: sen is depcrated in favor of raw. See Specifications -->
            <raw>R #Here just for example</raw>
        </goal>
        <step linenum="2">
            <rule>SUBPROOF</rule>
            <premise>1</premise>
        </step>
        <step linenum="4">
            <rule>SUBPROOF</rule>
            <premise>2</premise>
        </step>
        <step linenum="6">
            <sen>R</sen>
            <raw>R</raw>
            <rule>DISJUNCTIVE_SYLLOGISM</rule>
            <premise>1</premise>
            <premise>2</premise>
            <premise>4</premise>
        </step>
    </proof>
    <proof id="1">
        <assumption linenum="2">
            <sen>P</sen>
            <raw>P</raw>
        </assumption>
        <step linenum="3">
            <sen>R</sen>
            <raw>R</raw>
            <rule/>
        </step>
    </proof>
    <proof id="2">
        <assumption linenum="4">
            <sen>Q</sen>
            <raw>Q</raw>
        </assumption>
        <step linenum="5">
            <sen>R</sen>
            <raw>R</raw>
            <rule/>
        </step>
    </proof>
</bram>
```



Examples
---

###	Example 1:
```xml=
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<bram>
  <program>My Logic Program</program>
  <version>0.5</version>
  <metadata>
    <author>Bram van Heuveln</author>
    <created>2019-04-23T09:28:13Z</created>
    <modified>2019-04-24T17:45:41Z</modified>
  </metadata>
  <proof id="0">
    <assumption linenum="0">
      <raw>¬A ∨ B</raw>
    </assumption>
    <assumption linenum="1">
      <raw>A ∨ C</raw>
    </assumption>
    <assumption linenum="2">
      <raw>¬D → ¬ C</raw>
    </assumption>
    <step linenum="3">
      <rule>SUBPROOF</rule>
      <premise>1</premise>
    </step>
    <step linenum="10">
      <rule>SUBPROOF</rule>
      <premise>4</premise>
    </step>
    <step linenum="17">
      <raw>B ∨ D</raw>
      <rule>DISJUNCTIVE_SYLLOGISM</rule>
      <premise>10</premise>
      <premise>1</premise>
      <premise>3</premise>
    </step>
    <goal>
      <raw>B∨D</raw>
    </goal>
  </proof>
  <proof id="1">
    <assumption linenum="3">
      <raw>A</raw>
    </assumption>
    <step linenum="4">
      <rule>SUBPROOF</rule>
      <premise>2</premise>
    </step>
    <step linenum="7">
      <rule>SUBPROOF</rule>
      <premise>3</premise>
    </step>
    <step linenum="9">
      <raw>B∨D</raw>
      <rule>DISJUNCTIVE_SYLLOGISM</rule>
      <premise>0</premise>
      <premise>4</premise>
      <premise>7</premise>
    </step>
  </proof>
  <proof id="2">
    <assumption linenum="4">
      <raw>¬A</raw>
    </assumption>
    <step linenum="5">
      <raw>⊥</raw>
      <rule>CONTRADICTION</rule>
      <premise>3</premise>
      <premise>4</premise>
    </step>
    <step linenum="6">
      <raw>B ∨D </raw>
      <rule>PRINCIPLE_OF_EXPLOSION</rule>
      <premise>5</premise>
    </step>
  </proof>
  <proof id="3">
    <assumption linenum="7">
      <raw>B</raw>
    </assumption>
    <step linenum="8">
      <raw>B∨D</raw>
      <rule>ADDITION</rule>
      <premise>7</premise>
    </step>
  </proof>
  <proof id="4">
    <assumption linenum="10">
      <raw>C</raw>
    </assumption>
    <step linenum="11">
      <rule>SUBPROOF</rule>
      <premise>5</premise>
    </step>
    <step linenum="14">
      <raw>¬¬D</raw>
      <rule>PROOF_BY_CONTRADICTION</rule>
      <premise>11</premise>
    </step>
    <step linenum="15">
      <raw>D</raw>
      <rule>DOUBLENEGATION</rule>
      <premise>14</premise>
    </step>
    <step linenum="16">
      <raw>B∨D</raw>
      <rule>ADDITION</rule>
      <premise>15</premise>
    </step>
  </proof>
  <proof id="5">
    <assumption linenum="11">
      <raw>¬D</raw>
    </assumption>
    <step linenum="12">
      <raw>¬C</raw>
      <rule>MODUS_PONENS</rule>
      <premise>2</premise>
      <premise>11</premise>
    </step>
    <step linenum="13">
      <raw>⊥</raw>
      <rule>CONTRADICTION</rule>
      <premise>10</premise>
      <premise>12</premise>
    </step>
  </proof>
</bram>

```

### Example 2 (Aris):

```xml=
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<bram>
  <program>Aris</program>
  <version>0.0.169</version>
  <metadata>
    <author>UNKNOWN</author>
    <hash>u1SzYwTt8LIb1DFbKsD/f0O24WJ9YIU3bVvc70iITYE=</hash>
  </metadata>
  <proof id="0">
    <assumption linenum="0">
      <sen>a</sen>
      <raw>a</raw>
    </assumption>
    <assumption linenum="1">
      <sen>b -&gt; c</sen>
      <raw>b -&gt; c</raw>
    </assumption>
    <step linenum="2">
      <sen>a</sen>
      <raw>a</raw>
      <rule>REITERATION</rule>
      <premise>0</premise>
    </step>
    <step linenum="3">
      <sen>(a \/ b)</sen>
      <raw>a ∨ b</raw>
      <rule>ADDITION</rule>
      <premise>2</premise>
    </step>
    <step linenum="4">
      <rule>SUBPROOF</rule>
      <premise>1</premise>
    </step>
    <step linenum="6">
      <rule>SUBPROOF</rule>
      <premise>2</premise>
    </step>
    <goal>
      <sen/>
      <raw/>
    </goal>
  </proof>
  <proof id="1">
    <assumption linenum="4">
      <sen>b</sen>
      <raw>b</raw>
    </assumption>
    <step linenum="5">
      <sen>c</sen>
      <raw>c</raw>
      <rule>MODUS_PONENS</rule>
      <premise>1</premise>
      <premise>4</premise>
    </step>
  </proof>
  <proof id="2">
    <assumption linenum="6">
      <sen>a</sen>
      <raw>a</raw>
    </assumption>
  </proof>
</bram> 
```


---

###### tags: `Logic` `Documentation` `Bram-Hub`

