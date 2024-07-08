### ***Regex***

[**学习、受启发于deerchao的教程**](https://deerchao.cn/tutorials/regex/regex.htm)

> what is regex? something similer to wildcard, but more complex, more flexible and strong.

**matecharacter**: special character that has special rule to match characters.

> `.`: match anycharacters except `\n`
>
> `\w`: match letters, numbers, _ (and chinese), [a-z0-9A-Z_]
>
> `\s`: match blank characters include `\n`
>
> `\d`: match numbers
>
> `\b`: match the beginning or ending of characters, autually, it matches a place that one or both of former and latter character match `\w`
>
> `^`: match the beginning of characters
>
> `$`: match the ending of characters

examples:

`\bhi\b`: precise match "hi"

`\bhi\b.*\bLucy\b`: match "hi [...] lucy"

`\ba.*\b`: match words start with letter a

`\b\w{6}\b`: match words with 6 characters

`\b\d{5, 12}\b`: match numbers that no less than 5 digits and no more than 12 digits.

`\b\d{5, 12}@qq.com\b`: match QQ mail box

`^\w*@\w{?, ?}.\w{2, ?}$`: match any mail box(i have forgot how many characters a mail boox have).

**meaning-transform**: `\`, transform matecharacter into normal character if you want the matecharacter itself, including `\\`

example: `\($*\).o`: ?

**repetition**: \

> `*`: repeat zero or more times
>
> `+`: repeat one or more times
>
> `?`: repeat 0 or 1 times
>
> `{n}`: repeat n times
>
> `{n,}`: repeat n or more times
>
> `{n, m}`: repeat no less then n times but no more than m times

example: `^\w+`: match the first words in a sentense.

**character class**: to match none-matecharacters

`[aeiou]`, match a, e, i, o, u

`[.?!]`: match punctuation like ., ?, !

`[0-9]`: equal to `\d`

[a-z0-9A-Z_]: equal to `\w`

`\(?0\d{2}[)-]?\d{8}`: match phonre numbers start with 0

