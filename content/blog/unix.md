---
title: "Unix"
date: 2020-04-27T13:32:24+03:00
---

Notes from [edX: Unix Tools: Data, Software and Production Engineering](https://courses.edx.org/courses/course-v1:DelftX+UnixTx+1T2020/course/)

### grep 
##### Number of repetitions

```
 egrep 's{3}' words # Words with three s characters
 egrep '[^aeiouy]{7}' words # Words with seven consonants
 egrep '^.{,15}$' words | wc -l # Words with a length up to 15
 egrep '^.{15,}$' words | wc -l # Words with a length of at least 15
 egrep '^.{14}.+$' words | wc -l # Same using + (one or more)
 egrep '^.{15,16}$' words | wc -l # Words with a length between 15 and 16
 egrep '^.{15}.?$' words | wc -l # Same using ? (one or zero)
```

##### Back-references

```
 egrep '^(.).*\1$' words | head # Words beginning and ending with same letter
 egrep '^(.)(.)((.)\4)?\2\1$' words | head # Find 4-6 letter palindromes
```

##### Alternative matches

```
 egrep '^(aba|ono).*(ly|ne)$' words # Words with alternate start/end parts
```

##### Path 

```
 echo $PATH |
 > egrep '(^\.:)|(:\.:)|(:\.$)' >/dev/null && # Does the path contain .?
 > echo Current directory in PATH 
```


##### Complement matches
```
 egrep '^[     ]*(/\*|\*)' *.c | head -5 # List comment lines
 egrep -v '^[  ]*(/\*|\*)' *.c | head -5 # List non-comment lines
```

##### Search for fixed strings
```
 cd /usr/src/linux/fs # Linux filesystem source code directory
 fgrep ... *.c | head -5

 grep -o 'st_[a-z]*' /usr/include/sys/stat.h  | # Obtain status fields
 > sort -u >/tmp/statfields

 head /tmp/statfields # List status fields

 fgrep -f /tmp/statfields *.c | head -5 # List status field matches
```


### cut awk sed
```
 head -5 /etc/passwd
 cut -d: -f 1 /etc/passwd | head -5 # Output field 1
 cut -d: -f 3-4 /etc/passwd | head -5 # Output fields 3-4
  

 awk '/bash/' /etc/passwd # Output lines containing "bash"
 awk -F: '$3 > 1000' /etc/passwd # Lines where field 3 > 1000
 awk -F: '{print $1}' /etc/passwd | head -5 # Output field 1
 awk '!/^#/ {print $1}' /etc/services | head # Combine predicate and action

 cd /usr/src/linux/kernel # Linux kernel source code directory
 sed -n 's/#include *["<]\([^">]*\).*/\1/p' *.c | # Output included file names
 > head

 cd /usr/share/dict # Output lines from lines 1000 to 1005
 sed -n 1000,1005p words

 cd /usr/src/linux/kernel/printk
 sed -n '/^enum log_flags/,/^};$/p' printk.c # Output log_flags definition

 curl -q 'http://api.geonames.org/citiesJSON?north=37&south=38&east=24&west=23&lang=en&username=demo&maxRows=1'
 >result.json
 jq -r '.geonames[0].name,.geonames[0].countrycode' result.json

 curl -q 'http://api.geonames.org/cities?north=51&south=52&east=0&west=1&lang=en&username=demo&maxRows=1'
 >result.xml
 xmlstarlet sel -t -c /geonames/geoname/name result.xml
```


### sort
```
 sort -k 2 dates | head -5 # Sort by second and subsequent fields
 sort -k 2,2 -k 1,1 dates | head -5 # Sort by second, then first field
 sort -k 5.5,5.6 dates | head -5 # Sort by time minutes
 sort -k 4r dates | head -5 # Reverse sort
 sort -t : -k 4n /etc/passwd | head -8 # Sort by numeric group-id
```

### logs wrangling
```
 logresolve /var/log/access.log >resolved
 head resolved
 cut -d ' ' -f 1 resolved | # Obtain domain name
 awk -F. '{print $NF}' | # Obtain top-level domain
 > head

  cut -d ' ' -f 1 resolved | # Obtain domain name
  awk -F. '{print $NF}' | # Obtain top-level domain
  grep -v '[0-9]' | # Remove numeric IP addresses
  sort | # Order by TLD
  uniq -c | # Count duplicates
  sort -rn | # Order by number, descending
  > head
```

### compare
```
 ls /bin >linux.bin
 ssh freefall.freebsd.org ls /bin >freebsd.bin
 comm linux.bin freebsd.bin | head -20
```

### relational
```
  find . -type f -print0 | # Output all files
> xargs -0 md5sum >md5-sum.out # Run md5-sum on each of them

  cut -d ' ' -f 1 md5-sum.out | # Obtain first field
> sort | # Sort
> uniq -d >duplicates
 ```

### tower of hanoi with sed
``` 
$ cat hanoi.sed


# Towers of Hanoi in sed.
#
#       @(#)hanoi.sed   5.1 (Berkeley) 10/10/90
#
#
# Ex:
# Run "sed -f hanoi.sed", and enter:
#
#       :abcd: : :<CR><CR>
#
# note -- TWO carriage returns, a peculiarity of sed), this will output the
# sequence of states involved in moving 4 rings, the largest called "a" and
# the smallest called "d", from the first to the second of three towers, so
# that the rings on any tower at any time are in descending order of size.
# You can start with a different arrangement and a different number of rings,
# say :ce:b:ax: and it will give the shortest procedure for moving them all
# to the middle tower.  The rules are: the names of the rings must all be
# lower-case letters, they must be input within 3 fields (representing the
# towers) and delimited by 4 colons, such that the letters within each field
# are in alphabetical order (i.e. rings are in descending order of size).
#
# For the benefit of anyone who wants to figure out the script, an "internal"
# line of the form
#               b:0abx:1a2b3 :2   :3x2
# has the following meaning: the material after the three markers :1, :2,
# and :3 represents the three towers; in this case the current set-up is
# ":ab :   :x  :".  The numbers after a, b and x in these fields indicate
# that the next time it gets a chance, it will move a to tower 2, move b
# to tower 3, and move x to tower 2.  The string after :0 just keeps track
# of the alphabetical order of the names of the rings.  The b at the
# beginning means that it is now dealing with ring b (either about to move
# it, or re-evaluating where it should next be moved to).
#
# Although this version is "limited" to 26 rings because of the size of the
# alphabet, one could write a script using the same idea in which the rings
# were represented by arbitrary [strings][within][brackets], and in place of
# the built-in line of the script giving the order of the letters of the
# alphabet, it would accept from the user a line giving the ordering to be
# assumed, e.g. [ucbvax][decvax][hplabs][foo][bar].
#
#                       George Bergman
#                       Math, UC Berkeley 94720 USA

# cleaning, diagnostics
s/  *//g
/^$/d
/[^a-z:]/{a\
Illegal characters: use only a-z and ":".  Try again.
d
}
/^:[a-z]*:[a-z]*:[a-z]*:$/!{a\
Incorrect format: use\
\       : string1 : string2 : string3 :<CR><CR>\
Try again.
d
}
/\([a-z]\).*\1/{a\
Repeated letters not allowed.  Try again.
d
}
# initial formatting
h
s/[a-z]/ /g
G
s/^:\( *\):\( *\):\( *\):\n:\([a-z]*\):\([a-z]*\):\([a-z]*\):$/:1\4\2\3:2\5\1\3:3\6\1\2:0/
s/[a-z]/&2/g
s/^/abcdefghijklmnopqrstuvwxyz/
:a
s/^\(.\).*\1.*/&\1/
s/.//
/^[^:]/ba
s/\([^0]*\)\(:0.*\)/\2\1:/
s/^[^0]*0\(.\)/\1&/
:b
# outputting current state without markers
h
s/.*:1/:/
s/[123]//gp
g
:c
# establishing destinations
/^\(.\).*\1:1/td
/^\(.\).*:1[^:]*\11/s/^\(.\)\(.*\1\([a-z]\).*\)\3./\3\2\31/
/^\(.\).*:1[^:]*\12/s/^\(.\)\(.*\1\([a-z]\).*\)\3./\3\2\33/
/^\(.\).*:1[^:]*\13/s/^\(.\)\(.*\1\([a-z]\).*\)\3./\3\2\32/
/^\(.\).*:2[^:]*\11/s/^\(.\)\(.*\1\([a-z]\).*\)\3./\3\2\33/
/^\(.\).*:2[^:]*\12/s/^\(.\)\(.*\1\([a-z]\).*\)\3./\3\2\32/
/^\(.\).*:2[^:]*\13/s/^\(.\)\(.*\1\([a-z]\).*\)\3./\3\2\31/
/^\(.\).*:3[^:]*\11/s/^\(.\)\(.*\1\([a-z]\).*\)\3./\3\2\32/
/^\(.\).*:3[^:]*\12/s/^\(.\)\(.*\1\([a-z]\).*\)\3./\3\2\31/
/^\(.\).*:3[^:]*\13/s/^\(.\)\(.*\1\([a-z]\).*\)\3./\3\2\33/
bc
# iterate back to find smallest out-of-place ring
:d
s/^\(.\)\(:0[^:]*\([^:]\)\1.*:\([123]\)[^:]*\1\)\4/\3\2\4/
td
# move said ring (right, resp. left)
s/^\(.\)\(.*\)\1\([23]\)\(.*:\3[^ ]*\) /\1\2 \4\1\3/
s/^\(.\)\(.*:\([12]\)[^ ]*\) \(.*\)\1\3/\1\2\1\3\4 /
tb
s/.*/Done!  Try another, or end with ^D./p
d

$ sed -f hanoi.sed
```


### diff
```
 diff -c file1 file2 # List file differences in context
 diff -u file1 file2 # List file differences in unified context
 diff -W 40 -y file1 file2 # List differences in two 40 character columns

 diff -u mary.c mary2.c >mary.patch # Generate patch as a context diff
 patch john.c <mary.patch # Patch John's copy with Mary's patch

 diff -b john.c mary.c # Ignore changes in number of blanks
 diff -w john.c mary.c # Ignore all whitespace changes
 diff -r a b # Recursive diff

 diff -q file1 file3 >/dev/null && echo Same
 diff -q file1 file2 >/dev/null || echo Different
```

### test and eval
```
 test -d / && echo Directory # Test if directory
 test -f / && echo File # Test if file
 test hi = there && echo Same # Test if strings equal
 test -z "" && echo Empty # Test if string empty
 test . -nt / && echo . is newer than  / # Test if file newer than other
 test -w / && echo Writable # Test if writable
 
 if [ -d /etc/bash_completion.d ] ; then # Script use
  echo $(ls /etc/bash_completion.d | wc -l) completion scripts installed
 fi

 expr 1 + 2 # Add
 expr 12 \% 5 # Remainder
 expr John \> Mary # Compare strings
 expr length 'To be or not to be' # String length
```

### tr
```
 curl -s --compressed https://www.gutenberg.org/cache/epub/1342/pg1342.txt >pride-and-prejudice.txt
 tr a-z l-za-k <pride-and-prejudice.txt >secret
 openssl enc -e -aes-256-cbc -pbkdf2 <pride-and-prejudice.txt >real-secret
 openssl enc -d -aes-256-cbc -pbkdf2 <real-secret | head
```

### find and paste
```
 find . | # List current directory entries
> paste - /usr/share/dict/words | # Pair entries with words
> awk 'NF == 2 && $1 != "."' | # List pairs apart from the current directory
> tac |
> sed 's/^/mv /' | # Convert pairs to rename commands 
> sh # Have the shell execute the commands
 ```

### sound
```
  sox sox-orig.wav sox-orig.mp3 # Convert between file formats
  sox sox-orig.wav sox-low.wav pitch -600 # Lower pitch by 600 cents
  play -q sox-low.wav
  sox sox-orig.wav sox-fast.wav tempo 1.5 # Increase tempo by 50%
  
  sox sox-orig.wav sox-chorus.wav chorus 0.5 0.9 50 0.4 0.25 2 -t \
> 60 0.32 0.4 2.3 -t 40 0.3 0.3 1.3 -s # Apply chorus effect
```


### format and email
```
 openssl ciphers |
> sed 's/:/ /g' | # Separate words with space
> fmt | # Format words in lines
> head
 
 sendmail john.smith@example.com <<\EOF
> From: Alice Jones <alice.jones@example.com>
> To: John Smith <john.smith@example.com>
> Subject: Hi there
>
> I'm learning how to send email from the command line.
> EOF


$ cat send-connections.sh
#!/bin/sh
sendmail john.smith@example.com <<EOF
From: Diomidis Spinellis <dds@aueb.gr>
To: John Smith <john.smith@example.com>
Date: $(date -R)
Subject: Current network connections

These are the currently active network connections.
$(netstat)
EOF
 sh send-connections.sh

##### convert attachements int text
  dd if=/dev/random of=data count=32 bs=1
  more data
  base64 data >data.base64
  base64 -d <data.base64 >data.decoded
  cmp data data.decoded && echo Files are the same
```


##### Prefer redirection to pipes
```
 cat file | command # Wasteful execution of cat
 command <file # A redirection is all that's needed
```

##### Test command, not its exit code
```
 command
 if [ $? -ne 0 ] ; then # Verbose exit variable test
> echo Error >&2
> fi
 if ! command ; then # A simple negation will do
> echo Error >&2
> fi
```

##### Use the sed and awk predicates
```
 grep pattern | awk '{ ... }' # Unneeded use of grep
 awk '/pattern/ { ... }' # Simply prepend pattern 

 grep pattern | sed '...' # Unneeded use of grep
 sed '/pattern/ { ... }' # Simply prepend pattern
```

##### Grep can recurse directories
```
 grep pattern afile | wc -l # Count matches
 grep -c pattern afile # Modern count matches
 
 find . -type f | xargs grep pattern # Recursive search
 grep -r pattern . # Modern recursive search
```

##### Prefer wildcards to ls
```
 echo $(ls) # This is the same
 ls # As a simple invocation
 for i in $(ls) ; do # The ls here
> ...
> done

 for i in * ; do # can be replaced by a wildcard
> ...
> done
```

##### Replace awk with cut
```
 head -2 /etc/passwd | 
> awk -F: '{print $1, $7}' # Print fields 1 and 7

 head -2 /etc/passwd | 
> cut -d : -f 1,7 # More efficient way to print fields 1 and 7
```


##### Replace sed with expr
```
 echo $LANG
 echo $LANG |
> sed 's/.*\.\(.*\)/\1/' # Isolate encoding

 expr "$LANG" : '.*\.\(.*\)' # More efficient way to isolate encoding
```

##### Process find's output

```
 ls -ld **/core # Find files named core; might not fit
 find . -name core | # Find files named core
> while read filename ; do
>   ls -ld "$filename"
> done

 find . -name core \
> -exec ls -ld '{}' \; # Execute ls for each found file

 find . -name core -print0 |
> xargs -0 ls -ld # Execute ls in batches
```

### Pipe through ssh
```
 tar -czf - work-directory | # Pack directory to standard output 
> ssh backup-server dd of=/dev/st0 bs=1M # Send data to a remote tape 

ssh backup-server dd if=/dev/st0 bs=1M | # Obtain data from a remote tape
> tar -xzf - # Unpack files from standard input
 
 tar -czf - work-directory | # Pack directory to standard output
> ssh otherhost tar -xzf - # Unpack files from standard input
```

### bypass firewall
```
 ssh -f -L 8389:ldap.example.com:389 shell.example.com sleep 9999
```
local port 8389 goes to shell.example.com, which goes to ldap.example.com:839
