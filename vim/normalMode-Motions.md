# IndexOf

Use these links to jump to specific topics

[Basic Navigation]()

[Horizontal Movement]()

[The Difference Between words and WORDS]()

[Move to Specific Characters in a Line]()

[More Basic Line Movements]()


[Vertical Movement]()

[Search Functionality]()

[Augementing Movement: Counts]()

[Bonus Tricks]()

# Normal Mode

the default mode that you enter when the program starts is Normal Mode. 
Normal Mode is the mode to go to when you want to move around text and edit text.

Inside of the editor some normal key combinations like Copy and Paste are different. In this program...


```
y is used for Copy (yank)

p is used for Paste
```

______

## Basic Navigation

Navigation in `vim` can be done by either utilizng the arrow keys, or alternatively you can use the following keys while in Normal Mode to move

```
h - left

j - down

k - up

l - right (yes that **IS** confusing)
```


## Horizontal Movement

```
w - move word by word  

b - move backwards word by word
```

both of these include digits numbers and letters, or a sequence of non-blank characters

This means that when entering the `w` keystroke when you come across
`America,`
You will move to the comma because it is grouped with "America" as a word.
Interestingly enough, this logic also applies to groups of special characters like...

,,,,
)))))
888

but while there are "words" there are even "WORDS"

when you encounter "WORDS" you will need to use the `SHIFT` key

`W` moves you forward a WORD

`B` moves you backwards a WORD
_________

### INTERLUDE: The Different Between "words" and "WORDS"

America,

is two "words" (America and the `,`)
however it is only one "WORD" (all the characters are grouped together)
let's use something that better exemplifies this

wORD-123_iAmword! would be a WORD, however all of that together is 4 words.

_________

### Back to Horizontal Movement

```
e - allows you to jump to the end of a word

ge - allows you to jump to the end of the previous word
- NOTE: you do not hit these keys that the same time. Instead you hit `g` then you can proceed to `e`
```

these also have WORD modes, but you do not need to hit the `SHIFT` key on `g`

______

### Move to Characters 

You can use to the next occurrence of a character in the current line by using the following

```
f <character to move to> - moves you to the next occurence of a character

F <character to move to> - moves you to the previous occurence of that character
```

Essentially this is a per-line character search, but there exists another line search command, which is called the "until" command

```
t <character to move just before> - moves to to the character BEFORE the next occurence of it

T <character to move just before> - does the same but backwards
```

To make these movements easier, you can use the `;` key after typing a character to essentially repeat that character like so. This works for both `f` and `t` as well as their WORD counterparts

```
type fdfdfd ==> v   v               v        v
                let damage = weapon.damage * d20();
                let damage = weapon.damage * d20();
type fd;;   ==> v   v               v        v
```

_____

### More Line Movements

```
0 - moves to the first character in a line

^ - moves to the first NON-BLANK character in a line

$ - moves to the end of a line

g_ - moves to the non-blank character at the end of a line
- remember with `g` you don't hit the characters at the same time, but rather sequentially
```
__________

## Vertical Movement

```
} - jump down a whole paragraph

{ - jump up a whole paragraph

CTRL + D - scroll down half a page

CTRL + U - scroll up half a page

NOTE: the occurrence of `+` means that you hit the keys at the same time, unlike with `g` as you read previously

```

## Search Functionality

While moving Horizontally and Vertically is cool and all, sometimes you need to find specific characters in a file. This is where the search functionality of vim comes in

```
/<pattern> - allows you to search forwards

?<pattern> - allows you to search backwards

```

If you are looking for multiple matches or the first result is not the one you wanted, you can use `n` to move to the next search

if you accidentally go over a result you wanted, or you need to go back, use `N`

The search functions are really great. You can even alter the direction of a search by using any either `?` or `/` and the search even supports regular expression searching

_______

## Counts

Counts are essentially augementations on the movement commands detailed above. Here is the syntax for counts:

```
<count><command>
```

so for example you could use `5l` to go 5 characters to the right, or you could use `8a` to jump 8 `a` characters on the same line

counts are augementations of anything you have learned prior, including the search function of the program. An example of this is `3/<variable>` will land you on the 3rd search result for `<variable>` 

## A Few More Helpful Motions

```
gg - go to top of gile

<line#>gg - go to the specified line number

G - go to end of file

```


